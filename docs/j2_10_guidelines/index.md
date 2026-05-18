
# Les Guidelines PowerShell

## Règles de base

### Commenter **pourquoi**, pas **quoi**

Inutile de commenter `# Boucle sur les utilisateurs`. C'est évident. Commentez **la raison** : `# On filtre les comptes de service car ils n'ont pas d'email`.

### Jamais d'alias ni de paramètres positionnels dans le code

Dans votre terminal, tapez ce que vous voulez. **Dans un script** :

❌ À éviter :

```powershell
gci c:\temp
```

✅ À privilégier :

```powershell
Get-ChildItem -Path "C:\temp"
```

Nommer explicitement les paramètres rend le code lisible 6 mois plus tard.

### Respecter la convention `Verb-Noun`

Verbes approuvés (`Get-Verb`), au singulier, + nom au singulier.

❌ `Create-Password` (verbe non approuvé)
✅ `New-Password`

### Éviter `Write-Host`

Préférez :

- `Write-Output` (ou laisser tomber l'objet dans le pipeline) pour les données.
- `Write-Verbose` pour les messages de progression / debug.
- `Write-Information` pour les messages informatifs filtrables.

`Write-Host` écrit **directement dans la console** et casse le pipeline. Historiquement, c'était la seule façon d'afficher du texte coloré, mais `Write-Information` + `Write-Host` coloré ne doivent être utilisés que pour des scripts interactifs, jamais dans des fonctions réutilisables.

### Variables en PascalCase

```powershell
$MaVariable = "valeur"
$ListeDeServeurs = @()
```

Pas de `camelCase`, pas de `snake_case` (sauf pour des variables d'environnement type `$env:PATH`).

### Indentation cohérente

Avant :

```powershell
function Get-ComputerStatus
{
[CmdletBinding()]
param (
[string[]]$ComputerName)
Write-Verbose "Start"
foreach ($Computer in $ComputerName)
{
Write-Verbose "Querying $Computer"
<# Votre code #>
}
}
```

Après :

```powershell
function Get-ComputerStatus {
    [CmdletBinding()]
    param (
        [string[]]$ComputerName
    )

    Write-Verbose "Start"

    foreach ($Computer in $ComputerName) {
        Write-Verbose "Querying $Computer"
        <# Votre code #>
    }
}
```

Dans **VSCode** : `Shift+Alt+F` ou clic droit > "Mettre le document en forme".

## PSScriptAnalyzer — le linter officiel

[PSScriptAnalyzer](https://github.com/PowerShell/PSScriptAnalyzer) est un **linter** : il analyse votre code et détecte les violations de bonnes pratiques.

### Installation

```powershell
Install-Module PSScriptAnalyzer -Scope CurrentUser
```

### Utilisation

```powershell
Invoke-ScriptAnalyzer -Path .\MonScript.ps1
Invoke-ScriptAnalyzer -Path .\MonDossier -Recurse
```

Exemple de résultat :

```text
RuleName                            Severity     ScriptName Line  Message
--------                            --------     ---------- ----  -------
PSAvoidUsingWriteHost               Warning      script.ps1   42  Avoid using Write-Host because it might not work in all hosts...
PSUseSingularNouns                  Warning      script.ps1   15  The cmdlet 'Get-Servers' uses a plural noun. A singular noun should be used instead.
PSAvoidUsingPositionalParameters    Warning      script.ps1   23  Avoid using positional parameters when calling a command.
```

### Intégration VSCode

L'extension **PowerShell** pour VSCode inclut PSScriptAnalyzer. Les warnings apparaissent en direct dans l'éditeur (soulignés en vert/jaune).

### Corriger automatiquement

Certaines règles peuvent être auto-corrigées :

```powershell
Invoke-ScriptAnalyzer -Path .\MonScript.ps1 -Fix
```

!!! tip "Workflow recommandé"
    1. Écrire le code.
    2. Lancer `Invoke-ScriptAnalyzer`.
    3. Corriger les warnings **avant** de committer.
    4. Intégrer PSScriptAnalyzer en CI/CD pour bloquer les PRs avec des violations.

### Personnaliser les règles

Créer un fichier `PSScriptAnalyzerSettings.psd1` :

```powershell
@{
    ExcludeRules = @('PSAvoidUsingWriteHost')   # Désactiver une règle
    Severity     = @('Error','Warning')         # Ignorer les Info
}
```

```powershell
Invoke-ScriptAnalyzer -Path . -Settings .\PSScriptAnalyzerSettings.psd1
```

## En résumé

- Alias / param positionnels → **jamais dans un script**.
- Nommage : `Verb-Noun`, PascalCase, singulier.
- Indentation : VSCode le fait pour vous.
- **PSScriptAnalyzer** : activé dès le début, corrigé avant commit.
