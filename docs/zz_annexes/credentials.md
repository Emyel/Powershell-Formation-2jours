
# Gestion des credentials

## Get-Credential — saisie interactive

```powershell
$Creds = Get-Credential
```

Affiche une fenêtre de dialogue (GUI) ou un prompt (CLI) pour saisir utilisateur + mot de passe.

```powershell
Set-ADUser "jdupont" -Enabled $false -Credential $Creds
```

La commande s'exécute avec les droits de l'utilisateur contenu dans `$Creds`.

## Construction programmatique (sans prompt)

Pour scripter sans interaction :

```powershell
$User      = "DOMAIN\admin"
$PwdSecure = ConvertTo-SecureString "MotDePasse" -AsPlainText -Force
$Creds     = New-Object System.Management.Automation.PSCredential($User, $PwdSecure)
```

!!! danger 
> Attention
> Mettre un mot de passe en clair dans un script est une **très mauvaise pratique**. À utiliser uniquement pour des tests locaux temporaires.

## Stockage chiffré sur disque (DPAPI)

```powershell
# Sauvegarder
$Creds = Get-Credential
$Creds | Export-Clixml "$HOME\creds.xml"

# Recharger plus tard
$Creds = Import-Clixml "$HOME\creds.xml"
```

Le fichier est **chiffré par DPAPI** (Data Protection API) : lié à l'utilisateur Windows + la machine. Impossible à déchiffrer depuis un autre compte ou une autre machine.

**Cas d'usage** : compte de service sur une machine dédiée, scripts planifiés.

**Limite** : ne fonctionne pas pour partager un credential entre plusieurs utilisateurs ou plusieurs serveurs.

## SecretManagement & SecretStore (PowerShell 7+)

Pour des cas plus avancés (équipe, multi-machines, rotation de secrets) :

```powershell
Install-Module Microsoft.PowerShell.SecretManagement -Scope CurrentUser
Install-Module Microsoft.PowerShell.SecretStore -Scope CurrentUser

# Enregistrer un vault
Register-SecretVault -Name MyVault -ModuleName Microsoft.PowerShell.SecretStore

# Stocker un secret
Set-Secret -Name "AdminCreds" -Secret (Get-Credential)

# Récupérer
$Creds = Get-Secret -Name "AdminCreds" -AsPlainText
```

SecretStore chiffre localement. Pour un stockage partagé centralisé, intégration possible avec Azure Key Vault, HashiCorp Vault, etc. (via des extensions SecretManagement tierces).

## En résumé

| Besoin | Solution |
| --- | --- |
| Script interactif | `Get-Credential` |
| Test local ponctuel | Construction manuelle (`ConvertTo-SecureString`) |
| Compte de service sur une machine | `Export-Clixml` / `Import-Clixml` (DPAPI) |
| Équipe / multi-serveurs | `SecretManagement` + vault centralisé |

## Pour aller plus loin

- [about_Credential](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_credential)
- [SecretManagement](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.secretmanagement)
