# Exercice 5 — Outputs, Erreurs, Splatting

**Durée : 20 min**
**Couverture : PSCustomObject, Try/Catch, Verbose, Splatting**

---

## 5.1 — Fonction avec outputs structurés

Créez une fonction `Get-DiskReport` qui :

1. Récupère le disque `C:`
2. Retourne un `PSCustomObject` avec :
   - `DriveLetter`
   - `SizeGB` (arrondi à 2 décimales)
   - `FreeGB` (arrondi à 2 décimales)

**Indice** :
```powershell
Get-Volume -DriveLetter C
```


## 5.2 — Gestion d'erreur

Créez une fonction `Get-SafeService` qui :

1. Accepte un paramètre `ServiceName`
2. Tente de récupérer le service avec `Get-Service -ErrorAction Stop`
3. En cas d'erreur, affiche un **Warning** "Service introuvable : X"
4. Retourne `$null` si le service n'existe pas


## 5.3 — Splatting

Recréez l'exercice 5.1 en utilisant le **splatting** pour les paramètres de `Get-Volume`.


<details>
<summary><strong>Solutions</strong></summary>

### Solution 5.1

```powershell
function Get-DiskReport {
    [CmdletBinding()]
    param ()

    $vol = Get-Volume -DriveLetter C

    [PSCustomObject]@{
        DriveLetter = $vol.DriveLetter
        SizeGB      = [math]::Round($vol.Size / 1GB, 2)
        FreeGB      = [math]::Round($vol.SizeRemaining / 1GB, 2)
    }
}

Get-DiskReport
```

### Solution 5.2

```powershell
function Get-SafeService {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [string]$ServiceName
    )

    try {
        Get-Service -Name $ServiceName -ErrorAction Stop
    }
    catch {
        Write-Warning "Service introuvable : $ServiceName"
        return $null
    }
}

Get-SafeService -ServiceName "Spooler"
Get-SafeService -ServiceName "ServiceInexistant"
```

### Solution 5.3

```powershell
function Get-DiskReport {
    [CmdletBinding()]
    param ()

    $GetVolumeParams = @{
        DriveLetter = 'C'
    }

    $vol = Get-Volume @GetVolumeParams

    [PSCustomObject]@{
        DriveLetter = $vol.DriveLetter
        SizeGB      = [math]::Round($vol.Size / 1GB, 2)
        FreeGB      = [math]::Round($vol.SizeRemaining / 1GB, 2)
    }
}
```

</details>
