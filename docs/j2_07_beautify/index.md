
# Splatting, Format Operator, Type Accelerators

## Splatting — lisibilité des commandes complexes

Le **splatting** consiste à définir les paramètres d'une commande sous forme de hashtable.

Avant :

```powershell
New-ADUser -DisplayName "Luke Skywalker" -Name "SKYWALKER" -SamAccountName "lskywalker" -UserPrincipalName "lskywalker@starwars.com" -Surname "Skywalker" -GivenName "Luke" -Path "OU=Users,DC=StarWars,DC=com" -AccountPassword $("Azerty123" | ConvertTo-SecureString -AsPlainText -Force) -Enabled $true
```

Après :

```powershell
$NewUserParams = @{
    DisplayName       = "Luke Skywalker"
    Name              = "SKYWALKER"
    SamAccountName    = "lskywalker"
    UserPrincipalName = "lskywalker@starwars.com"
    Surname           = "Skywalker"
    GivenName         = "Luke"
    Path              = "OU=Users,DC=StarWars,DC=com"
    AccountPassword   = "Azerty123" | ConvertTo-SecureString -AsPlainText -Force
    Enabled           = $true
}

New-ADUser @NewUserParams
```

Note : `@NewUserParams` (arobase), pas `$NewUserParams`.

### Avantage 2 : adapter les paramètres conditionnellement

```powershell
if ($Credentials) {
    $NewUserParams["Credential"] = $Credentials
}
if ($SamAccountName -like "ADM-*") {
    $NewUserParams["Path"] = "OU=Admins,DC=StarWars,DC=com"
}

New-ADUser @NewUserParams
```

On ne répète jamais la commande, on adapte juste la hashtable.

### Combiner plusieurs splats

```powershell
$Common = @{
    SubnetMask    = '255.255.255.0'
    LeaseDuration = (New-TimeSpan -Days 8)
}
$DHCPScope = @{
    Name       = 'TestNetwork'
    StartRange = '10.0.0.2'
    EndRange   = '10.0.0.254'
}

Add-DhcpServerv4Scope @DHCPScope @Common
```

## Format Operator `-f`

L'opérateur `-f` permet de formater des chaînes avec des placeholders `{0}`, `{1}`, etc.

```powershell
"Bonjour {0}, tu as {1} ans" -f "Mathieu", 38
# → "Bonjour Mathieu, tu as 38 ans"
```

Formatage de nombres :

```powershell
"{0:N2}" -f 123.456789   # → "123,46"     (2 décimales)
"{0:N3}" -f 123.456789   # → "123,457"    (3 décimales)
"{0:P0}" -f 0.85         # → "85 %"       (pourcentage sans décimale)
"{0:D5}" -f 42           # → "00042"      (padding sur 5 chiffres)
"{0:0# ## ## ## ##}" -f 0611223344  # → "06 11 22 33 44"  (template)
```

Créer une liste incrémentielle :

```powershell
1..10 | ForEach-Object { 'File{0:D3}.txt' -f $_ }
# → File001.txt, File002.txt, ..., File010.txt
```

## Type Accelerators — raccourcis .NET

Les **Type Accelerators** sont des alias vers des classes .NET fréquentes.

### Vérifier une IP

```powershell
"192.168.1.255" -as [ipaddress]   # → objet IPAddress si valide, $null sinon
```

### Créer un objet Version

```powershell
[version]"1.0.2" -gt [version]"1.0.1"   # → $true
```

### Regex nommées pour extraction

```powershell
$Pattern = "^(?<titre>.*?)\.S(?<saison>\d+)E(?<episode>\d+)\.(?<reste>.*)$"
"Westworld.S03E01.VOSTFR.1080p.AMZN.WEB-DL" -match $Pattern

[PSCustomObject]@{
    Titre   = $Matches.titre
    Saison  = $Matches.saison
    Episode = $Matches.episode
}
```

```text
Titre     Saison Episode
-----     ------ -------
Westworld 03     01
```

!!! tip 
> Quand utiliser quoi ?
> - **Splatting** : dès qu'une commande a plus de 3-4 paramètres ou que les paramètres sont conditionnels.
> - **Format Operator** : dès qu'on construit des chaînes avec des valeurs dynamiques — bien plus lisible que la concaténation.
> - **Type Accelerators** : validation de format (IP, DateTime, Version), casting, regex nommées.
