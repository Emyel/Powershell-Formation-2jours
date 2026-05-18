# TP Final — Get-ComputerInfo

**Durée : 30 min**
**Objectif : Synthèse de tous les concepts J1 + J2**

---

## Cahier des charges

Créer une fonction **Get-ComputerInfo** qui interroge des machines distantes et retourne un objet structuré avec leurs informations système.

### Spécifications fonctionnelles

La fonction doit :

1. Accepter un ou plusieurs noms de machines via le paramètre `ComputerName` (obligatoire)
2. Pouvoir recevoir les machines **depuis le pipeline** (par valeur ET par nom de propriété)
3. Utiliser un **alias** `Name` pour le paramètre (compatible avec `Get-ADComputer`)
4. Retourner un `PSCustomObject` avec :
   - Nom de la machine
   - Version de l'OS (Caption + Version)
   - Nombre de cœurs CPU
   - RAM totale en GB
   - Espace libre sur C: en GB
5. Encadrer les appels CIM avec **Try / Catch** (`-ErrorAction Stop`)
6. Émettre un `Write-Verbose` à chaque machine traitée
7. En cas d'erreur sur une machine, émettre un `Write-Warning` et continuer
8. Avoir une **Comment-Based Help** avec :
   - `.SYNOPSIS`
   - `.DESCRIPTION`
   - `.PARAMETER ComputerName`
   - `.EXAMPLE` (au moins un)


## Indices techniques

### Récupérer l'OS et la RAM

```powershell
Get-CimInstance -ComputerName <name> -ClassName Win32_OperatingSystem
```

### Récupérer le CPU

```powershell
Get-CimInstance -ComputerName <name> -ClassName Win32_Processor
```

### Récupérer l'espace disque C:

```powershell
Get-CimInstance -ComputerName <name> -ClassName Win32_LogicalDisk -Filter "DeviceID='C:'"
```

Les propriétés utiles sont `FreeSpace` (octets) et `Size` (octets).


## Bonus 1 (optionnel)

- Ajouter un paramètre `[switch]$IncludeDisk` qui inclut ou non les infos disque
- Utiliser le **splatting** pour les paramètres de `Get-CimInstance`
- Ajouter `SupportsShouldProcess` (même si la fonction est en lecture seule, pour la pratique)


## Bonus 2 — Créer un utilisateur cloud avec splatting (15-20 min)

**Contexte** : Votre entreprise migre vers le cloud. Vous devez créer un utilisateur dans **Entra ID** (anciennement Azure AD).

### Mission

1. **Trouvez le module PowerShell** pour gérer Entra ID / Microsoft 365
   - Indice : ce n'est **pas** le module `AzureAD` (déprécié depuis 2023)
   - Cherchez dans PowerShell Gallery ou la documentation Microsoft

2. **Installez le module** (si pas déjà fait)

3. **Connectez-vous** à votre tenant Entra ID

4. **Trouvez la cmdlet** pour créer un utilisateur
   - Indice : elle suit la convention `Verb-Noun`
   - Le verbe est `New`, le nom fait référence à "User"

5. **Créez un utilisateur** avec le **splatting** :

**Données utilisateur** :
```
Prénom         : Leia
Nom            : Organa
Email (UPN)    : leia.organa@votredomaine.onmicrosoft.com
Mot de passe   : ChangeMe2024!
```

**Contraintes** :
- Utiliser le **splatting** (hashtable de paramètres)
- Le mot de passe doit forcer un changement à la première connexion
- Le compte doit être **activé** (`AccountEnabled`)
- Ajouter une **Comment-Based Help** basique

### Indices

Pour créer un utilisateur cloud, vous aurez besoin de :
- `DisplayName` (nom complet)
- `UserPrincipalName` (email de connexion)
- `MailNickname` (alias email, souvent prénom.nom)
- `GivenName` (prénom)
- `Surname` (nom de famille)
- `PasswordProfile` (hashtable avec `Password` et `ForceChangePasswordNextSignIn`)
- `AccountEnabled` (booléen)

### Squelette de départ

```powershell
function New-CloudUser {
    [CmdletBinding()]
    param (
        # Vos paramètres ici
    )

    # Se connecter si nécessaire
    # Connect-...

    # Construire la hashtable de splatting
    $NewUserParams = @{
        # Vos paramètres ici
    }

    # Appeler la cmdlet
    # New-...User @NewUserParams
}
```


<details>
<summary><strong>Solution Bonus 2</strong></summary>

### Étape 1 : Installer et se connecter

```powershell
# Le module est Microsoft.Graph
Install-Module Microsoft.Graph -Scope CurrentUser

# Se connecter
Connect-MgGraph -Scopes "User.ReadWrite.All"
```

### Étape 2 : Fonction avec splatting

```powershell
function New-CloudUser {
<#
.SYNOPSIS
    Crée un utilisateur dans Entra ID avec splatting.

.DESCRIPTION
    Fonction de démonstration pour créer un utilisateur cloud en utilisant
    le module Microsoft Graph et le pattern splatting.

.EXAMPLE
    New-CloudUser
#>
    [CmdletBinding()]
    param ()

    # Construire le profil de mot de passe
    $PasswordProfile = @{
        Password                      = "ChangeMe2024!"
        ForceChangePasswordNextSignIn = $true
    }

    # Splatting des paramètres
    $NewUserParams = @{
        DisplayName       = "Leia Organa"
        UserPrincipalName = "leia.organa@votredomaine.onmicrosoft.com"
        MailNickname      = "leia.organa"
        GivenName         = "Leia"
        Surname           = "Organa"
        PasswordProfile   = $PasswordProfile
        AccountEnabled    = $true
    }

    # Création de l'utilisateur
    try {
        New-MgUser @NewUserParams -ErrorAction Stop
        Write-Output "✅ Utilisateur créé : $($NewUserParams.UserPrincipalName)"
    }
    catch {
        Write-Warning "❌ Échec : $($_.Exception.Message)"
    }
}

New-CloudUser
```

### Avantages du splatting ici

- **Lisibilité** : impossible de lire la version "ligne unique" avec 7+ paramètres
- **Maintenabilité** : facile d'ajouter/retirer un paramètre
- **Réutilisabilité** : la hashtable peut être construite dynamiquement (boucle sur un CSV, par exemple)

</details>


## Solution TP principal

<details>
<summary><strong>Solution Get-ComputerInfo (squelette)</strong></summary>

```powershell
function Get-ComputerInfo {
<#
.SYNOPSIS
    Récupère les informations système d'un ou plusieurs ordinateurs.

.DESCRIPTION
    Interroge des machines distantes via CIM pour obtenir leur OS, CPU, RAM et espace disque.

.PARAMETER ComputerName
    Le ou les noms des machines à interroger. Accepte le pipeline.

.EXAMPLE
    Get-ComputerInfo -ComputerName SRV01, SRV02

.EXAMPLE
    Get-ADComputer -Filter * | Get-ComputerInfo -Verbose
#>
    [CmdletBinding()]
    param (
        [Parameter(Mandatory, ValueFromPipeline, ValueFromPipelineByPropertyName)]
        [Alias('Name')]
        [string[]]$ComputerName
    )

    begin {
        Write-Verbose "Démarrage de Get-ComputerInfo"
    }

    process {
        foreach ($Computer in $ComputerName) {
            Write-Verbose "Interrogation de $Computer"

            try {
                $OS   = Get-CimInstance -ComputerName $Computer -ClassName Win32_OperatingSystem -ErrorAction Stop
                $CPU  = Get-CimInstance -ComputerName $Computer -ClassName Win32_Processor -ErrorAction Stop
                $Disk = Get-CimInstance -ComputerName $Computer -ClassName Win32_LogicalDisk -Filter "DeviceID='C:'" -ErrorAction Stop

                [PSCustomObject]@{
                    ComputerName = $OS.CSName
                    OSVersion    = "$($OS.Caption) $($OS.Version)"
                    CPUCores     = $CPU.NumberOfCores
                    RAMTotalGB   = [math]::Round($OS.TotalVisibleMemorySize / 1MB, 2)
                    DiskFreeGB   = [math]::Round($Disk.FreeSpace / 1GB, 2)
                }
            }
            catch {
                Write-Warning "Échec pour $Computer : $($_.Exception.Message)"
            }
        }
    }

    end {
        Write-Verbose "Fin de Get-ComputerInfo"
    }
}
```

</details>