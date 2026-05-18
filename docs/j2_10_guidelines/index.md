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

!!! tip "Le linter intégré à VSCode"
    L'extension **PowerShell** pour VSCode inclut un linter qui analyse votre code en temps réel et souligne les violations de bonnes pratiques directement dans l'éditeur (alias, paramètres positionnels, nommage, etc.).
    Pas besoin d'action supplémentaire : dès que vous installez l'extension, il est actif.

## En résumé

- Alias / param positionnels → **jamais dans un script**.
- Nommage : `Verb-Noun`, PascalCase, singulier.
- Indentation : VSCode le fait pour vous (`Shift+Alt+F`).
- `Write-Host` → interdit dans les fonctions réutilisables, acceptable dans les scripts interactifs.
- VSCode souligne les violations en temps réel — corrigez avant de committer.
