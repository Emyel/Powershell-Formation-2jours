# Exercice 2 — Opérateurs et Manipulation d'objets

**Durée : 20 min**
**Couverture : Where-Object, Select-Object, Sort-Object, Group-Object**

---

## 2.1 — Top 5 des processus

1. Récupérez les **5 processus les plus gourmands en CPU**
2. Affichez uniquement `ProcessName`, `Id` et `CPU`
3. Triez par CPU décroissant


## 2.2 — Services automatiques

À partir de `Get-Service` :

1. Filtrez uniquement les services dont le `StartType` est `Automatic`
2. Conservez uniquement `Name`, `Status`, `StartType`
3. Regroupez par `Status` pour compter combien sont démarrés vs arrêtés


## 2.3 — Fichiers récents

À partir d'un dossier de votre choix (ex: `C:\Windows\Temp`) :

1. Listez les fichiers modifiés dans les **7 derniers jours**
2. Affichez leur `Name` et `LastWriteTime`
3. Triez du plus récent au plus ancien


<details>
<summary><strong>Solutions</strong></summary>

### Solution 2.1

```powershell
Get-Process |
    Sort-Object CPU -Descending |
    Select-Object ProcessName, Id, CPU -First 5
```

### Solution 2.2

```powershell
Get-Service |
    Where-Object { $_.StartType -eq "Automatic" } |
    Select-Object Name, Status, StartType |
    Group-Object Status
```

### Solution 2.3

```powershell
$DateLimite = (Get-Date).AddDays(-7)

Get-ChildItem -Path C:\Windows\Temp -File |
    Where-Object { $_.LastWriteTime -gt $DateLimite } |
    Select-Object Name, LastWriteTime |
    Sort-Object LastWriteTime -Descending
```

</details>
