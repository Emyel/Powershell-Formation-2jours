# Opérateurs

Vue d'ensemble compacte des opérateurs PowerShell. Une cheat-sheet à conserver.

## Comparaison de nombres

| Opérateur | Description |
| --- | --- |
| `-eq` / `-ne` | Égal / Différent |
| `-gt` / `-ge` | Plus grand / Plus grand ou égal |
| `-lt` / `-le` | Plus petit / Plus petit ou égal |

## Comparaison de chaînes

| Opérateur | Description |
| --- | --- |
| `-eq` | Égalité (insensible à la casse par défaut) |
| `-like` | Wildcard (`*` et `?`) — `"Texte" -like "Text*"` |
| `-match` | Regex — `$_ -match "^A"` |
| `-in` | Présence d'une valeur dans une liste — `"A" -in @("A","B","C")` |
| `-notin` | Inverse de `-in` |

Préfixer par `c` rend sensible à la casse : `-ceq`, `-clike`, ...
Préfixer par `not` inverse : `-notlike`, `-notmatch`, ...

`$Admins = @("Prue","Piper","Phoebe")`
`"Prue" -in $Admins      # → $true`
`"Paige"  -notin $Admins   # → $true`

## Comparaison de collections

| Opérateur | Description |
| --- | --- |
| `-contains` | La collection **contient-elle** cette valeur ? (membre à droite) |
| `-notcontains` | Inverse de `-contains` |
| `-in` | La valeur est-elle **dans** cette collection ? (membre à gauche) |
| `-notin` | Inverse de `-in` |

```powershell
$Serveurs = "SRV01", "SRV02", "SRV03"

$Serveurs -contains "SRV02"   # → $true
"SRV02" -in $Serveurs         # → $true  (syntaxe alternative, plus lisible)

$Serveurs -contains "SRV99"   # → $false
```

!!! tip "`-contains` vs `-match`"
    `-contains` teste la **présence d'un élément dans une collection** — ce n'est pas un opérateur de chaîne.
    Pour chercher une sous-chaîne, utilisez `-like` ou `-match` :

    ```powershell
    # ❌ Ne fait pas ce qu'on croit — teste si le tableau contient exactement "SRV"
    "SRV01", "SRV02" -contains "SRV"   # → $false

    # ✅ Chercher une sous-chaîne dans une chaîne
    "SRV01" -like "*SRV*"   # → $true
    "SRV01" -match "SRV"    # → $true
    ```

## Logique

| Opérateur | Description |
| --- | --- |
| `-and` | ET |
| `-or`  | OU |
| `-not` (ou `!`) | NON |
| `-xor` | OU exclusif |

## Manipulation de chaînes

```powershell
"Le héros s'appelle Zelda !" -replace "Zelda","Link"
# → "Le héros s'appelle Link !"

"charles.dieu@nyxen.com" -split "@"
# → @("charles.dieu","nyxen.com")

"charles.dieu","nyxen.com" -join "@"
# → "charles.dieu@nyxen.com"
```

### Opérateur de formatage `-f`

Très utile, souvent oublié.

```powershell
"Bonjour {0}, tu as {1} ans" -f "Mathieu", 38
# → "Bonjour Mathieu, tu as 38 ans"

"{0:N2}" -f 123.456789       # → "123,46"     (2 décimales)
"{0:P0}" -f 0.85             # → "85 %"       (pourcentage)
"{0:D5}" -f 42               # → "00042"      (padding zéros)
```

## Arithmétique

| Opérateur | Description |
| --- | --- |
| `+`, `-`, `*`, `/` | Opérations classiques |
| `%` | Modulo |
| `++`, `--` | Incrémentation / Décrémentation |
| `+=`, `-=`, `*=`, `/=` | Opérations avec affectation |

## Opérateurs de type

| Opérateur | Exemple | Description |
| --- | --- | --- |
| `-is` | `99 -is [int]` | Vérifie le type, renvoie `$true`/`$false` |
| `-isnot` | `99 -isnot [string]` | Inverse de `-is` |
| `-as` | `"01/09/23" -as [datetime]` | Tente une conversion ; `$null` si impossible |

```powershell
"192.168.1.10"  -as [ipaddress]   # → objet IPAddress
"192.168.1.300" -as [ipaddress]   # → $null  (donc IP invalide)
```