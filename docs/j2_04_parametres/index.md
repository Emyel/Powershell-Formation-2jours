
# Paramètres avancés

Un paramètre PowerShell ne se limite pas à un nom et un type. On peut **valider**, **regrouper**, **alimenter depuis le pipeline**.

## Vue d'ensemble

```powershell
param (
    [Parameter(
        Mandatory                       = $true,
        Position                        = 0,
        ParameterSetName                = "ByName",
        ValueFromPipeline               = $true,
        ValueFromPipelineByPropertyName = $true,
        HelpMessage                     = "Le nom de l'ordinateur"
    )]
    [Alias('Hostname','Name')]
    [ValidateNotNullOrEmpty()]
    [ValidatePattern('^\w+$')]
    [string[]]$ComputerName
)
```

## Options principales

### Mandatory

Force la fourniture du paramètre. Un prompt apparaît s'il est omis.

```powershell
[Parameter(Mandatory)]
[string]$Path
```

### Position

Permet d'appeler sans nommer.

```powershell
param (
    [Parameter(Position = 0)] [string]$Source,
    [Parameter(Position = 1)] [string]$Destination
)
# Appel : Copy-Truc "src" "dst"  (équivaut à -Source "src" -Destination "dst")
```

### ParameterSetName

Plusieurs **jeux** de paramètres mutuellement exclusifs. Détectable via `$PSCmdlet.ParameterSetName`.

```powershell
function Convert-IPMask {
    [CmdletBinding()]
    param (
        [Parameter(ParameterSetName = 'CIDR', Mandatory)]
        [int]$CIDR,

        [Parameter(ParameterSetName = 'IP', Mandatory)]
        [ipaddress]$Mask
    )

    switch ($PSCmdlet.ParameterSetName) {
        'CIDR' { ... }
        'IP'   { ... }
    }
}
```

### ValueFromPipeline / ValueFromPipelineByPropertyName

- **ByValue** : le paramètre prend la valeur de l'objet entier (si le type colle).
- **ByPropertyName** : le paramètre cherche une propriété du même nom (ou alias) dans l'objet du pipeline.

```powershell
function Set-Machine {
    [CmdletBinding()]
    param (
        [Parameter(ValueFromPipeline, ValueFromPipelineByPropertyName)]
        [Alias('Name')]                # → matchera la propriété "Name" de Get-ADComputer
        [string[]]$ComputerName
    )

    process {
        foreach ($Name in $ComputerName) {
            Write-Verbose "Processing $Name"
        }
    }
}

Get-ADComputer -Filter * | Set-Machine -Verbose
```

## Validation des entrées

| Attribut | Effet |
| --- | --- |
| `[ValidateNotNull()]` | La valeur ne peut pas être `$null`. |
| `[ValidateNotNullOrEmpty()]` | Pas `$null`, pas `""`, pas `@()`. |
| `[ValidateSet('A','B','C')]` | Seules les valeurs listées sont acceptées (+ auto-complétion). |
| `[ValidateRange(0,100)]` | Plage numérique. |
| `[ValidateLength(3,20)]` | Longueur de string. |
| `[ValidateCount(1,5)]` | Nombre d'éléments dans une collection. |
| `[ValidatePattern('^\d+$')]` | Doit matcher la regex. |
| `[ValidateScript({ Test-Path $_ })]` | Bloc de code retournant `$true`/`$false`. |
| `[AllowNull()]` / `[AllowEmptyString()]` / `[AllowEmptyCollection()]` | Autorise ces valeurs même si `Mandatory`. |

```powershell
param (
    [Parameter(Mandatory)]
    [ValidateSet('Prod','Dev','Hom')]
    [string]$Environnement,

    [Parameter(Mandatory)]
    [ValidateScript({ Test-Path $_ })]
    [string]$LogPath,

    [ValidateRange(1, 24)]
    [int]$RetentionHours = 12
)
```

L'avantage majeur : la validation est faite par PowerShell **avant** que le corps de la fonction ne s'exécute. Pas besoin de coder le `if ($Path -ne $null -and (Test-Path $Path))`.

## Le bloc CmdletBinding

`[CmdletBinding()]` transforme une fonction simple en **fonction avancée** :

- Active les **common parameters** (`-Verbose`, `-Debug`, `-ErrorAction`, `-WarningAction`, `-InformationAction`, `-OutVariable`, ...).
- Permet l'usage de `$PSCmdlet` (`.ParameterSetName`, `.ShouldProcess()`, ...).

C'est **toujours** la première ligne d'une fonction de production.
