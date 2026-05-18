
# Exportation / Importation

## Fichier brut

```powershell
Get-EventLog -LogName Application -Newest 10 | Out-File Log.txt
Get-Content C:\Windows\System32\drivers\etc\hosts
```

## CSV

```powershell
Get-Process | Export-Csv -Path .\Processes.csv -NoTypeInformation
Import-Csv -Path .\Processes.csv
```

- `-Delimiter` : caractère de séparation (`,` par défaut, souvent `;` en France).
- `-NoTypeInformation` : retire l'en-tête `#TYPE` qui n'est plus utile depuis PS6.

## JSON

```powershell
Get-Service | ConvertTo-Json | Out-File services.json
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
