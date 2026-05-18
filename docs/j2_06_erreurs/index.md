---
title: Gestion d'erreurs
---

# Gestion d'erreurs — Try / Catch / Finally

## Types d'erreurs

PowerShell distingue deux types :

**Non-Terminating**  
Une erreur est émise mais le code continue.

**Terminating**  
L'erreur arrête immédiatement l'exécution.

## ErrorAction & ErrorActionPreference

| Valeur | Comportement |
| --- | --- |
| `Continue` | Émet l'erreur, continue. *(défaut)* |
| `SilentlyContinue` | Masque l'erreur, continue. **Impossible à catcher.** |
| `Ignore` | Supprime l'erreur, n'alimente pas `$Error`. **Impossible à catcher.** |
| `Inquire` | Affiche l'erreur, demande confirmation. |
| `Stop` | Transforme en **Terminating** → **catchable** dans Try/Catch. |

!!! danger "Erreur fréquente"
    Mettre `$ErrorActionPreference = "SilentlyContinue"` en tête de script pour "éviter les erreurs" est une **anti-pattern**. On masque les vrais problèmes au lieu de les gérer.

## Try / Catch — principe

Pour gérer les erreurs **Terminating**, on les encadre dans un bloc `Try / Catch`.

```powershell
$ComputerList = "OfflineComputer","WKS02"

foreach ($Computer in $ComputerList) {
    try {
        $OS = Get-CimInstance -ComputerName $Computer -ClassName Win32_OperatingSystem -ErrorAction Stop
        Write-Host "✅ $Computer : $($OS.Caption)"
    }
    catch {
        Write-Warning "❌ Échec pour $Computer : $($_.Exception.Message)"
    }
}
```

Sans le `Try / Catch`, la première erreur arrêterait la boucle entière. Avec, chaque échec est géré et la boucle continue.

## `$_` dans un Catch

Dans un bloc `catch`, `$_` (ou `$PSItem`) est un objet **`ErrorRecord`** complet — pas juste un message.

Propriétés utiles :

```powershell
catch {
    $_.Exception.Message              # Le message lisible
    $_.Exception.GetType().FullName   # Le type de l'exception
    $_.ScriptStackTrace               # La pile d'appels
    $_.InvocationInfo.PositionMessage # L'emplacement exact dans le code
}
```

## Catch typé — gérer par type d'exception

On peut avoir plusieurs blocs `catch` ciblant des exceptions précises.

```powershell
try {
    Remove-Item "C:\fichier.txt" -ErrorAction Stop
}
catch [System.Management.Automation.ItemNotFoundException] {
    Write-Warning "Le fichier est introuvable"
}
catch [System.IO.IOException] {
    Write-Warning "Vous n'avez pas les droits"
}
catch {
    Write-Warning "Erreur inconnue : $($_.Exception.Message)"
}
```

### Identifier le type d'une exception

Juste après avoir rencontré l'erreur :

```powershell
$Error[0].Exception.GetType().FullName
```

## Finally — nettoyage garanti

Le bloc `Finally` s'exécute **toujours**, qu'il y ait eu une erreur ou non. Utile pour libérer une ressource.

```powershell
$Session = New-PSSession -ComputerName SRV01
try {
    Invoke-Command -Session $Session -ScriptBlock { ... } -ErrorAction Stop
}
catch {
    Write-Error "Échec : $($_.Exception.Message)"
}
finally {
    if ($Session) { Remove-PSSession $Session }
}
```

!!! tip "Piège classique"
    On oublie souvent le `Finally` alors qu'il est crucial pour éviter les fuites de ressources (sessions, connexions, fichiers ouverts, etc.).

## Gérer les programmes externes — `$LASTEXITCODE`

PowerShell gère ses propres erreurs avec `Try/Catch`, mais quand vous appelez un **programme externe** (`.exe`, `.bat`, utilitaires système), il faut vérifier manuellement son **code de retour**.

### Le problème

```powershell
robocopy C:\Source D:\Destination /MIR
```

Même si `robocopy` échoue, PowerShell ne déclenche **aucune exception**. Le script continue comme si de rien n'était.

### La solution — `$LASTEXITCODE`

Après chaque appel à un programme externe, PowerShell stocke son code de retour dans `$LASTEXITCODE`.

```powershell
robocopy C:\Source D:\Destination /MIR

if ($LASTEXITCODE -ge 8) {
    throw "Robocopy a échoué avec le code $LASTEXITCODE"
}
```

**Codes de retour Robocopy** :
- `0-7` : Succès (avec ou sans fichiers copiés)
- `8+` : Échec

### Exemple complet

```powershell
function Invoke-Robocopy {
    [CmdletBinding()]
    param (
        [string]$Source,
        [string]$Destination
    )

    Write-Verbose "Copie de $Source vers $Destination"
    
    robocopy $Source $Destination /MIR /R:3 /W:5 /NP
    
    if ($LASTEXITCODE -ge 8) {
        throw "Robocopy a échoué (code $LASTEXITCODE)"
    }
    
    Write-Verbose "Copie terminée (code $LASTEXITCODE)"
}
```

### Différence avec `$?`

| Variable | Signification |
| --- | --- |
| `$?` | `$true` si la **dernière commande PowerShell** a réussi, `$false` sinon. |
| `$LASTEXITCODE` | Code de retour numérique du **dernier programme externe** exécuté. |

`$?` ne fonctionne **pas** pour les programmes externes — utilisez toujours `$LASTEXITCODE`.

!!! warning "Cas courants nécessitant $LASTEXITCODE"
    - `robocopy`, `xcopy`
    - `git`, `svn`
    - `netsh`, `wmic`
    - `msiexec`, `setup.exe`
    - Scripts `.bat` / `.cmd`
