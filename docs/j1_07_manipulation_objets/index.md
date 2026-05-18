
# Manipulation d'objets dans le pipeline

## Where-Object — **filtrer**

Filtre une collection pour ne garder que les objets répondant à une condition.

```powershell
$names = @("Aaron","Albert","Alphonse","Julien","Mathieu","Lucile","Cédric","Sébastien")
$names | Where-Object { $_ -like "A*" }
```

```text
Aaron
Albert
Alphonse
```

```powershell
Get-Service | Where-Object { $_.Status -eq "Running" }
Get-ChildItem | Where-Object { $_.LastWriteTime -gt (Get-Date "01/09/2023") }
```

**Syntaxe simplifiée (PS3+)** pour les filtres simples :

```powershell
Get-Service | Where-Object Status -eq Running
Get-Process | Where-Object CPU -gt 10
```

## Select-Object — **projeter**

Choisit les propriétés à conserver, en crée de nouvelles, limite le nombre d'objets.

```powershell
Get-Service | Select-Object Name, Status, StartType
Get-Service | Select-Object -First 10
Get-Service | Select-Object -Last 5

# Voir TOUTES les propriétés (même celles masquées par défaut)
Get-Disk -Number 0 | Select-Object *

# Créer une propriété calculée
Get-ChildItem | Select-Object Name, @{
    Name       = "Taille en MB"
    Expression = { ($_.Length / 1MB) }
}
```

## Sort-Object — **trier**

```powershell
Get-Process | Sort-Object CPU -Descending
Get-Service | Sort-Object Status
$names | Sort-Object -Descending
```

## Group-Object — **regrouper**

```powershell
Get-Service | Group-Object Status
```

```text
Count Name
----- ----
  173 Stopped
  122 Running
```

## ForEach-Object — **transformer**

Exécute un bloc de code pour chaque objet du pipeline.

```powershell
"google.com","1.1.1.1" | ForEach-Object { Test-Connection $_ -Count 1 }

Get-ChildItem E:\ -Directory | ForEach-Object {
    New-Item -Path $_.FullName -Name "Readme.txt"
}
```

!!! NOTE
> `ForEach-Object` vs mot-clé `foreach`
> - `ForEach-Object` (commande) : **streame** les objets du pipeline au fur et à mesure.
> - `foreach (...)` (structure, vue en J2) : charge tout en mémoire avant de boucler.
> PS7+ : `ForEach-Object -Parallel { ... }` permet le multi-threading.

## Compare-Object — **comparer**

Compare deux collections et identifie les différences.

```powershell
$before = Get-Process
Start-Process Notepad
$after = Get-Process

Compare-Object -ReferenceObject $before -DifferenceObject $after
```

Le `SideIndicator` indique de quel côté est la différence : `<=` (uniquement dans Reference), `=>` (uniquement dans Difference).

L'option `-PassThru` renvoie les objets eux-mêmes au lieu d'objets de comparaison.
