
# Scripts vs Fonctions

## Différence

- Un **script** (`.ps1`) est un enchaînement de commandes, structures et fonctions qui automatise un processus complexe.
- Une **fonction** est un morceau de code nommé, mono-tâche, réutilisable.

!!! tip "Règle d'or"
    Si vous répétez **au moins deux fois** le même bloc de code pour une même opération, ce bloc doit devenir une fonction.

## Paramètres d'un script

```powershell
[CmdletBinding()]
param (
    [string]$ComputerName,
    [string]$FilePath
)
```

## Exécution d'un script

```powershell
# Direct
.\monscript.ps1

# Depuis un autre script
& "C:\chemin\vers\monscript.ps1"

# Dot Sourcing — charge les variables/fonctions dans le contexte appelant
. "C:\chemin\vers\monscript.ps1"
```

Le **dot sourcing** est notamment utile pour charger une bibliothèque de fonctions :

```powershell
. "$PSScriptRoot\Functions.ps1"   # Toutes les fonctions deviennent disponibles
```

∗`$PSScriptRoot` contient le dossier du script en cours d'exécution — indispensable pour les chemins relatifs fiables.*
