
# Structures de contrôle

## IF / ELSE / ELSEIF

```powershell
if ($Texte -eq "Condition")
{
    "Condition remplie"
}
elseif ($Texte -is [string])
{
    "Pas la bonne valeur mais c'est bien une string"
}
else
{
    "Condition non remplie"
}
```

## FOREACH

```powershell
$Names = @('Mathieu','Julien','Sylvain')

foreach ($Name in $Names)
{
    "Hello $Name!"
}
```

Très utile pour générer une collection d'objets :

```powershell
$Table = foreach ($Number in 1..10)
{
    [PSCustomObject]@{
        Calcul   = "$Number x 10"
        Resultat = $Number * 10
    }
}
$Table
```

## WHILE / DO-WHILE / DO-UNTIL

```powershell
# Vérifie AVANT — peut ne jamais s'exécuter
while ($i -ge 0) { $i ; $i-- }

# Vérifie APRÈS — s'exécute au moins une fois
do { $i ; $i-- } while ($i -ge 0)

# Comme DO-WHILE mais inverse la logique
do { $i ; $i-- } until ($i -lt 0)
```

## SWITCH

```powershell
$Value = "Condition"

switch ($Value)
{
    'Condition' { "Cas 1" }
    'Autre'     { "Cas 2" }
    default     { "Aucun cas" }
}
```

Avec conditions complexes (scriptblock) :

```powershell
$Value = 15

switch ($Value)
{
    { $_ -gt 0 }  { "Supérieur à 0" }
    { $_ -lt 20 } { "Inférieur à 20" }
}
```

Avec wildcard / regex :

```powershell
switch -Wildcard ("Condition")
{
    'Condition' { "Cas exact" }
    'Condi*'    { "Cas wildcard" }
}
```

## break / continue

- `break` : sort immédiatement de la boucle.
- `continue` : saute l'itération en cours, passe à la suivante.

```powershell
foreach ($i in 1..10) {
    if ($i -eq 5) { continue }   # saute le 5
    if ($i -eq 8) { break    }   # s'arrête avant 8
    $i
}
# → 1 2 3 4 6 7
```
