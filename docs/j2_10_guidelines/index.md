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

### `Write-Host` — quand l'utiliser, quand l'éviter

`Write-Host` écrit directement dans la console et **casse le pipeline** : sa sortie ne peut pas être capturée, redirigée ou traitée par la commande suivante.

Dans une **fonction réutilisable**, préférez toujours :

- `Write-Output` (ou laisser l'objet tomber dans le pipeline) pour les données.
- `Write-Verbose` pour les messages de progression.
- `Write-Warning` pour les anomalies non bloquantes.
- `Write-Information` pour les messages informatifs filtrables.

```powershell
# ❌ Casse le pipeline — $result sera vide
$Result = Get-ComputerInfo -ComputerName SRV01 | Where-Object { $_.OSVersion -like "*2022*" }
# (si Get-ComputerInfo utilise Write-Host au lieu de retourner un objet)

# ✅ L'objet circule dans le pipeline
function Get-ComputerInfo {
    [CmdletBinding()]
    param ([string]$ComputerName)

    Write-Verbose "Interrogation de $ComputerName"   # progression
    [PSCustomObject]@{ ComputerName = $ComputerName; OSVersion = "..." }   # donnée
}
```

**Les cas légitimes de `Write-Host`** existent néanmoins :

- **Scripts interactifs** destinés à être lus par un humain en direct (menus, assistants pas-à-pas), où le pipeline n'est pas utilisé.
- **Affichage coloré** dans un script de démo ou d'onboarding (`-ForegroundColor Green`).
- **Environnements contraints** comme Azure Automation Runbooks ou certains outils de CI/CD qui n'exposent que la sortie console — dans ces cas, `Write-Host` est parfois le seul moyen d'émettre un message visible.

!!! tip "La règle simple"
    Bibliothèque de fonctions, module, code réutilisable → **jamais de `Write-Host`**.
    Script one-shot interactif, démo, outil de diagnostic → `Write-Host` est acceptable avec modération.

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
- `Write-Host` → interdit dans les fonctions réutilisables, acceptable dans les scripts interactifs.
- **PSScriptAnalyzer** : activé dès le début, corrigé avant commit.