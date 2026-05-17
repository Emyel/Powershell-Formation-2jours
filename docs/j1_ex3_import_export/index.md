# Exercice 3 — Import / Export

**Durée : 15 min**
**Couverture : CSV, JSON, Export/Import**

---

## 3.1 — Exporter des processus

1. Récupérez les 10 processus les plus gourmands en mémoire
2. Sélectionnez `ProcessName`, `Id`, `WorkingSet`
3. Exportez le résultat en **CSV** dans `C:\Temp\top_processes.csv`


## 3.2 — Créer et exporter un inventaire serveur

Créez 3 objets serveurs avec les propriétés `Nom`, `IP`, `Statut` :

```text
SRV01, 192.168.1.10, Online
SRV02, 192.168.1.11, Offline
SRV03, 192.168.1.12, Online
```

Exportez-les en **JSON** dans `C:\Temp\inventaire.json`.


## 3.3 — Réimporter et filtrer

1. Réimportez le fichier JSON créé précédemment
2. Filtrez uniquement les serveurs `Online`
3. Affichez leur `Nom` et `IP`


<details>
<summary><strong>Solutions</strong></summary>

### Solution 3.1

```powershell
Get-Process |
    Sort-Object WorkingSet -Descending |
    Select-Object ProcessName, Id, WorkingSet -First 10 |
    Export-Csv -Path C:\Temp\top_processes.csv -NoTypeInformation
```

### Solution 3.2

```powershell
$serveurs = @(
    [PSCustomObject]@{ Nom = "SRV01"; IP = "192.168.1.10"; Statut = "Online" }
    [PSCustomObject]@{ Nom = "SRV02"; IP = "192.168.1.11"; Statut = "Offline" }
    [PSCustomObject]@{ Nom = "SRV03"; IP = "192.168.1.12"; Statut = "Online" }
)

$serveurs | ConvertTo-Json | Out-File C:\Temp\inventaire.json
```

### Solution 3.3

```powershell
$data = Get-Content C:\Temp\inventaire.json | ConvertFrom-Json

$data | Where-Object { $_.Statut -eq "Online" } | Select-Object Nom, IP
```

</details>
