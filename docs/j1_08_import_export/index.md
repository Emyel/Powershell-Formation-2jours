# Exportation / Importation

## Fichier brut

```powershell
Get-WinEvent -LogName Application -Newest 10 | Out-File Log.txt -Encoding utf8NoBOM
Get-Content C:\Windows\System32\drivers\etc\hosts
```

## CSV

```powershell
Get-Process | Export-Csv -Path .\Processes.csv -NoTypeInformation
Import-Csv -Path .\Processes.csv
```

- `-Delimiter` : caractère de séparation (`,` par défaut, souvent `;` en France).
- `-NoTypeInformation` : retire l'en-tête `#TYPE` qui n'est plus utile depuis PS6.

!!! warning "Encodage et accents"
    Par défaut, `Export-Csv` produit de l'UTF-8 sans BOM en PS 6+, mais de l'Unicode (UTF-16) en PS 5.1.
    Résultat : les accents s'affichent en caractères parasites à l'ouverture dans Excel.

    ```powershell
    # PS 5.1 — forcer UTF-8 avec BOM (lisible par Excel sans configuration)
    Get-Process | Export-Csv -Path .\Processes.csv -NoTypeInformation -Encoding UTF8

    # PS 7 — si Excel râle sur les accents
    Get-Process | Export-Csv -Path .\Processes.csv -NoTypeInformation -Encoding utf8BOM

    # En France, combiner délimiteur et encodage
    Get-Process | Export-Csv -Path .\Processes.csv -NoTypeInformation -Delimiter ";" -Encoding utf8BOM
    ```

## JSON

```powershell
Get-Service | ConvertTo-Json | Out-File services.json -Encoding utf8NoBOM
Get-Content services.json -Raw | ConvertFrom-Json
```

`ConvertFrom-Json` ne prend pas de fichier en entrée mais une **string**. Le `-Raw` de `Get-Content` lit tout le fichier d'un coup au lieu de le splitter ligne par ligne.

Cas d'usage très fréquent : appel à une REST API.

```powershell
$Response = Invoke-RestMethod -Uri "https://api.exemple.com/users/42"
$Response.email
```

`Invoke-RestMethod` fait le `ConvertFrom-Json` pour nous.

## XML (CliXml)

`Export-CliXml` est spécial : il **préserve** le type, les propriétés et les méthodes des objets PowerShell. C'est le format idéal pour sérialiser/désérialiser des objets PowerShell complexes.

```powershell
Get-Process | Export-Clixml .\Processes.xml
Import-Clixml .\Processes.xml
```

Cas d'usage typique : **stocker un credential** réutilisable de manière chiffrée.

```powershell
$Creds = Get-Credential
$Creds | Export-Clixml "$HOME\creds.xml"   # Chiffré DPAPI (utilisateur + machine)

# Plus tard, dans un autre script :
$Creds = Import-Clixml "$HOME\creds.xml"
```

!!! warning "Le fichier ne sera **déchiffrable que par le même utilisateur sur la même machine**"
    C'est très bien pour un compte de service sur une machine, c'est inutilisable pour partager des creds entre deux postes.

    Pour des cas plus avancés (équipe, multi-machines), regarder les modules `Microsoft.PowerShell.SecretManagement` et `Microsoft.PowerShell.SecretStore`.

---

!!! note "Récapitulatif encodages courants"
    | Valeur | PS | Quand l'utiliser |
    |---|---|---|
    | `UTF8` | 5.1 | UTF-8 avec BOM — compatible Excel |
    | `utf8BOM` | 7 | UTF-8 avec BOM — compatible Excel |
    | `utf8NoBOM` | 7 | UTF-8 sans BOM — scripts, API, échanges Linux |
    | `Default` | 5.1/7 | Encodage ANSI système — à éviter en production |