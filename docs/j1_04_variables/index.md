# Variables

Une variable est un emplacement mémoire identifié par un nom, un type et une valeur. En PowerShell, elle est précédée du signe `$`.

## Assignation

```powershell
$Variable = "ma variable"
$Process  = Get-Process -Name Notepad
```

PowerShell détermine le type automatiquement :

```powershell
$Variable = 1   ; $Variable | Get-Member   # → System.Int32
$Variable = "1" ; $Variable | Get-Member   # → System.String
```

## Forcer le typage

```powershell
[string]$Variable   = 1                       # int  → string
[int]   $Variable   = "1"                     # string → int
[char]  $Char       = 0201                    # ASCII → caractère
[datetime] $Variable = "10/12/1984"           # string → DateTime
```

## Propriétés et méthodes

Les propriétés et méthodes de l'objet stocké sont appelées avec un `.` :

```powershell
$Texte = "Ceci est une phrase"
$Texte.Length        # → 19
$Texte.ToUpper()     # → "CECI EST UNE PHRASE"
```

```powershell
$Process = Get-Process notepad
$Process.Id
$Process.Kill()
```

## Utilisation dans une chaîne

```powershell
$Service = Get-Service Spooler
"Le service Spooler est $($Service.Status)"
```

La syntaxe `$(...)` empêche que l'expression `$Service.Status` ne soit interprétée littéralement.

## Variables automatiques utiles

| Variable | Description |
| --- | --- |
| `$?` | `$true` si la dernière commande a réussi, `$false` sinon. |
| `$null` | Valeur nulle / indéfinie. |
| `$error` | Liste des erreurs de la session ; `$error[0]` = la plus récente. |
| `$_` / `$PSItem` | Objet courant dans un pipeline (`Where-Object`, `ForEach-Object`). |
| `$args` | Arguments non nommés passés à un script ou une fonction. |
| `$PSScriptRoot` | Dossier du script en cours — **indispensable** pour les chemins relatifs. |
| `$PSCommandPath` | Chemin complet du script en cours. |
| `$PROFILE` | Chemin vers le profil utilisateur (chargé à chaque ouverture de console). |
| `$HOME` | Dossier personnel de l'utilisateur. |

## `$null` — pièges de comparaison

`$null` est une valeur spéciale qui représente l'absence de valeur. Elle se comporte de manière surprenante dans certains cas.

**Piège 1 — ordre des opérandes**

Quand le membre gauche d'une comparaison est une collection, PowerShell filtre la collection plutôt que de renvoyer un booléen :

```powershell
$Resultats = Get-ADUser -Filter { Department -eq "IT" }   # peut renvoyer $null si aucun résultat

# ❌ À éviter : si $Resultats est une collection, PowerShell filtre et renvoie [] au lieu de $false
if ($Resultats -eq $null) { "Aucun résultat" }

# ✅ À privilégier : $null à gauche force une vraie comparaison booléenne
if ($null -eq $Resultats) { "Aucun résultat" }
```

La règle est simple : **`$null` toujours à gauche** dans une comparaison d'égalité.

**Piège 2 — variable non initialisée**

Une variable non déclarée vaut `$null` — pas d'erreur, juste un comportement silencieux :

```powershell
if ($null -eq $MaVariable) {
    "Variable non initialisée ou explicitement nulle"
}
```

**Piège 3 — `$null` dans une collection**

`$null` peut se retrouver dans un tableau sans que ce soit évident :

```powershell
$Liste = "A", $null, "B"
$Liste.Count        # → 3
$Liste -ne $null    # → @("A", "B")  (filtre les nulls)
```

!!! tip "Bonne pratique"
    Toujours mettre `$null` à gauche de l'opérateur de comparaison.
    PSScriptAnalyzer signale automatiquement les violations de cette règle (`PSPossibleIncorrectComparisonWithNull`).

## Variables d'environnement

Le drive virtuel `Env:` expose les variables d'environnement Windows.

```powershell
$env:USERNAME       # Utilisateur courant
$env:COMPUTERNAME   # Nom de la machine
$env:USERDOMAIN     # Domaine
$env:TEMP           # Dossier temporaire
$env:PATH           # Chemins exécutables (séparés par ; sous Windows)
```

Pour tout lister : `Get-ChildItem Env:`.