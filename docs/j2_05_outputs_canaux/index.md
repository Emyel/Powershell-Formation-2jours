---
title: Outputs & canaux
---

# Outputs structurés & canaux de sortie

## Renvoyer un PSCustomObject

Le retour standard d'une fonction PowerShell est un **PSCustomObject**. C'est ordonné, sérialisable, affichable en tableau.

```powershell
function Get-ComputerInfo {
    [CmdletBinding()]
    param ([string]$ComputerName)

    $OS = Get-CimInstance -ComputerName $ComputerName -ClassName Win32_OperatingSystem

    [PSCustomObject]@{
        ComputerName = $OS.CSName
        OSVersion    = $OS.Caption
        FreeRAM      = [math]::Round($OS.FreePhysicalMemory / 1GB, 2)
    }
}
```

## Construire une collection : bonne et mauvaise pratique

### ❌ À proscrire : `$Result = @()` + `+=`

```powershell
$Result = @()
foreach ($Computer in $ComputerList) {
    $Result += Get-ComputerInfo -ComputerName $Computer
}
```

**Pourquoi c'est mauvais ?** Les arrays .NET sont **immutables**. Chaque `+=` recrée un nouveau tableau et recopie tous les éléments précédents → **O(n²)**. Sur 10 000 éléments, c'est catastrophique.

### Mesurer la performance — `Measure-Command`

`Measure-Command` permet de chronométrer l'exécution d'une commande ou d'un bloc de code.

```powershell
# Méthode inefficace
Measure-Command {
    $Result = @()
    foreach ($i in 1..1000) {
        $Result += $i
    }
}
```

```
TotalMilliseconds : 287.4532
```

```powershell
# Méthode efficace
Measure-Command {
    $Result = foreach ($i in 1..1000) {
        $i
    }
}
```

```
TotalMilliseconds : 4.2156
```

**Résultat** : l'affectation directe est **70× plus rapide** sur 1000 éléments. L'écart empire avec la taille.

### ✅ Recommandé : affecter la boucle directement

```powershell
$Result = foreach ($Computer in $ComputerList) {
    Get-ComputerInfo -ComputerName $Computer
}
```

PowerShell construit la collection efficacement.

### Alternative : `[System.Collections.Generic.List[object]]`

Si vous devez vraiment ajouter conditionnellement au fil de l'eau :

```powershell
$Result = [System.Collections.Generic.List[object]]::new()
foreach ($Computer in $ComputerList) {
    if ($Computer -like "SRV*") {
        $Result.Add((Get-ComputerInfo -ComputerName $Computer))
    }
}
```

## Les 6 canaux de sortie

PowerShell dispose de 6 canaux distincts :

| # | Canal | Commande | Par défaut | Activation |
| --- | --- | --- | --- | --- |
| 1 | Success | (pipeline) | Visible | Toujours |
| 2 | Error | `Write-Error` | Visible | Toujours |
| 3 | Warning | `Write-Warning` | Visible | Toujours |
| 4 | Verbose | `Write-Verbose` | Masqué | `-Verbose` ou `$VerbosePreference` |
| 5 | Debug | `Write-Debug` | Masqué | `-Debug` ou `$DebugPreference` |
| 6 | Information | `Write-Information` | Masqué | `-InformationAction` ou `$InformationPreference` |

```powershell
function Get-Example {
    [CmdletBinding()]
    param ()

    Write-Verbose   "Démarrage de la fonction"
    Write-Debug     "Variable X = 42"
    Write-Information "Message informatif" -Tags status
    Write-Warning   "Ceci est un avertissement"

    [PSCustomObject]@{ Result = "OK" }   # Canal Success (1)
}
```

```powershell
Get-Example -Verbose -InformationAction Continue
```

## Verbose vs Debug vs Information

- **Verbose** : progression, étapes du traitement. Pour l'utilisateur qui veut comprendre ce qui se passe.
- **Debug** : valeurs de variables, état interne. Pour le développeur qui cherche un bug.
- **Information** : messages informatifs filtrables (remplaçant moderne de `Write-Host`).

## Préférences par canal

On peut contrôler le comportement via les variables automatiques :

```powershell
$VerbosePreference      = 'Continue'     # Active Verbose globalement
$DebugPreference        = 'Continue'     # Active Debug globalement
$WarningPreference      = 'SilentlyContinue'   # Masque les warnings
$ErrorActionPreference  = 'Stop'         # Transforme les erreurs en exceptions
$InformationPreference  = 'Continue'     # Active Information
```

Ou via les paramètres des commandes :

```powershell
Get-Example -Verbose -WarningAction SilentlyContinue -InformationAction Continue
```

## Enregistrer une session complète — `Start-Transcript`

`Start-Transcript` enregistre **tout** ce qui se passe dans la console dans un fichier texte : les commandes, les sorties, les erreurs.

```powershell
Start-Transcript -Path "C:\Logs\Session_$(Get-Date -Format 'yyyyMMdd_HHmmss').txt"

# Vos commandes ici
Get-Service
Get-Process

Stop-Transcript
```

**Cas d'usage** :
- **Audit** : garder une trace complète d'une intervention manuelle.
- **Debugging** : capturer un problème intermittent.
- **Formation** : enregistrer une démo complète.

**Bonnes pratiques** :
- Toujours encadrer dans un `try/finally` pour garantir l'appel à `Stop-Transcript` même en cas d'erreur.
- Nommer le fichier avec un timestamp pour éviter les écrasements.

```powershell
try {
    Start-Transcript -Path "$HOME\Logs\$(Get-Date -Format 'yyyyMMdd_HHmmss').txt"
    
    # Votre code ici
    
} finally {
    Stop-Transcript
}
```

!!! tip "Pourquoi c'est important"
    Ces canaux structurent la sortie de votre code. Un script de production émet du **Verbose** pour le suivi, des **Warning** pour signaler des anomalies non bloquantes, des **Error** pour les échecs, et retourne des **objets** dans le canal Success.
    
    Jamais de `Write-Host` en prod.
