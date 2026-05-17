
# Array, Hashtable, PSCustomObject

## Array — liste de valeurs

```powershell
$Ints    = 1, 2, 3, 4
$Strings = "Un", "Deux", "Trois"
```

### Indexation

```powershell
$Ints[0]      # Premier élément
$Ints[-1]     # Dernier élément
$Ints[0..2]   # Plage (premiers 3 éléments)
```

>[!IMPORTANT]
> Piège du `+=` `$Array += "valeur"` paraît anodin mais **recrée intégralement le tableau à chaque ajout** (les arrays .NET sont immutables). 
> À éviter dès qu'on traite plus de quelques dizaines d'éléments.

Alternatives :
- Affecter directement une boucle : `$Result = foreach (...) { ... }`.
- Utiliser une `[System.Collections.Generic.List[object]]::new()` puis `.Add(...)`.

(On y reviendra en J2 sur les outputs.)

## Hashtable — clé/valeur

```powershell
$Env = @{
    PROD = "SERVER-PROD"
    DEV  = "SERVER-DEV"
    HOM  = "SERVER-HOM"
}

$Env["PROD"]   # → "SERVER-PROD"
$Env.PROD      # → "SERVER-PROD" (syntaxe propriété)
```

## PSCustomObject — objet personnalisé

Le `PSCustomObject` est l'**outil de sortie standard** d'un script PowerShell. Il est ordonné, sérialisable, et s'affiche proprement.

```powershell
$Computer = [PSCustomObject]@{
    Name  = "HAL9000"
    OS    = "Windows 3.11"
    Disks = "256Mo"
}

$Computer.Name   # → "HAL9000"
```

Ajouter une propriété après coup :

```powershell
$Computer | Add-Member -MemberType NoteProperty -Name RAM -Value "512Mo"
```

!!! tip
> Pourquoi PSCustomObject plutôt que Hashtable ?
> - **Ordre garanti** des propriétés (contrairement à une hashtable classique).
> - S'affiche en colonnes automatiquement dans la console.
> - Compatible nativement avec `Export-Csv`, `ConvertTo-Json`, `Sort-Object`, etc.
>
> C'est ce qu'on retournera systématiquement depuis nos fonctions en J2.
!!!
