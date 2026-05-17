
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
| `-contains` | Présence d'une valeur dans une liste |

Préfixer par `c` rend sensible à la casse : `-ceq`, `-clike`, ...
Préfixer par `not` inverse : `-notlike`, `-notmatch`, ...

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

"nom.prenom@societe.com" -split "@"
# → @("nom.prenom","societe.com")

"nom.prenom","societe.com" -join "@"
# → "nom.prenom@societe.com"
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
