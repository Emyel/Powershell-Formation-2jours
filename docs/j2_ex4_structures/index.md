# Exercice 4 — Structures et Paramètres

**Durée : 20 min**
**Couverture : If, Foreach, Fonctions, Paramètres obligatoires et validation**

---

## 4.1 — Vérifier un service

Créez une fonction `Test-ServiceStatus` qui :

1. Accepte un paramètre `ServiceName` (obligatoire)
2. Vérifie si le service existe
3. Si oui, affiche "Le service X est Y" (Y = Running ou Stopped)
4. Si non, affiche "Service introuvable"

**Indices** :
```powershell
Get-Service -Name "Spooler" -ErrorAction SilentlyContinue
```


## 4.2 — Boucle sur des services

Créez une fonction `Get-ServiceReport` qui :

1. Accepte un tableau de noms de services en paramètre
2. Pour chaque service, retourne un objet avec `Name` et `Status`
3. Testez avec : `@("Spooler", "WinRM", "W32Time")`


## 4.3 — Validation de plage

Créez une fonction `Set-Volume` qui :

1. Accepte un paramètre `Level` (obligatoire)
2. Valide que `Level` est entre **0 et 100**
3. Affiche "Volume réglé à X%"

**Indice** : `[ValidateRange(0,100)]`


<details>
<summary><strong>Solutions</strong></summary>

### Solution 4.1

```powershell
function Test-ServiceStatus {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [string]$ServiceName
    )

    $svc = Get-Service -Name $ServiceName -ErrorAction SilentlyContinue

    if ($svc) {
        Write-Output "Le service $ServiceName est $($svc.Status)"
    } else {
        Write-Output "Service introuvable"
    }
}

Test-ServiceStatus -ServiceName "Spooler"
```

### Solution 4.2

```powershell
function Get-ServiceReport {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [string[]]$ServiceName
    )

    foreach ($svc in $ServiceName) {
        $service = Get-Service -Name $svc -ErrorAction SilentlyContinue

        if ($service) {
            [PSCustomObject]@{
                Name   = $service.Name
                Status = $service.Status
            }
        }
    }
}

Get-ServiceReport -ServiceName @("Spooler", "WinRM", "W32Time")
```

### Solution 4.3

```powershell
function Set-Volume {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [ValidateRange(0,100)]
        [int]$Level
    )

    Write-Output "Volume réglé à $Level%"
}

Set-Volume -Level 75
Set-Volume -Level 150  # → Erreur de validation
```

</details>
