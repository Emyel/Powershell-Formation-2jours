
# Modules PowerShell

Un **module** est une bibliothèque de commandes (fonctions, cmdlets) dédiée à la gestion d'un élément précis : application, API, rôle Windows Server, service cloud, etc.

## Lister les modules installés

```powershell
Get-Module -ListAvailable
```

## Charger un module

Depuis PowerShell v3, le chargement est **automatique** quand on appelle une commande du module.

Pour forcer le chargement :

```powershell
Import-Module ActiveDirectory
Import-Module Microsoft.Graph
```

## Lister les commandes d'un module

```powershell
Get-Command -Module ActiveDirectory
Get-Command -Module Microsoft.Graph.Users
```

## Installer un module depuis PowerShell Gallery

```powershell
Install-Module -Name PowerShell-Yaml -Scope CurrentUser
Install-Module -Name Pester -Scope CurrentUser -Force -SkipPublisherCheck
```

`-Scope CurrentUser` installe dans le profil utilisateur (pas besoin d'élévation admin).

## PowerShell Gallery

[https://www.powershellgallery.com/](https://www.powershellgallery.com/)

Repository public officiel Microsoft. Attention : certains modules sont communautaires (pas validés par Microsoft). Vérifier les stats, le nombre de téléchargements, les dernières mises à jour.

## Modules indispensables

| Module | Usage |
| --- | --- |
| `ActiveDirectory` | Gestion AD on-prem (RSAT). |
| `Microsoft.Graph` | Gestion Entra ID, Microsoft 365 (remplaçant officiel d'AzureAD et MSOnline). |
| `ExchangeOnlineManagement` | Gestion Exchange Online. |
| `Az` | Gestion Azure (compute, storage, networking, etc.). |
| `Pester` | Tests unitaires. |
| `PSScriptAnalyzer` | Linter PowerShell. |
| `ImportExcel` | Manipulation Excel sans avoir Excel installé. |
| `PnP.PowerShell` | Administration SharePoint Online. |

### Microsoft.Graph — le nouveau standard Entra ID

Depuis 2023, Microsoft a déprécié les modules **AzureAD** et **MSOnline** au profit de **Microsoft.Graph**.

```powershell
Install-Module Microsoft.Graph -Scope CurrentUser

# Se connecter
Connect-MgGraph -Scopes "User.Read.All","Group.ReadWrite.All"

# Lister les utilisateurs
Get-MgUser -All

# Créer un groupe
New-MgGroup -DisplayName "IT-Team" -MailEnabled:$false -SecurityEnabled:$true -MailNickname "it-team"
```

Graph est **modulaire** : `Microsoft.Graph.Users`, `Microsoft.Graph.Groups`, etc. Installer le sous-module qui vous intéresse ou installer `Microsoft.Graph` (tout).

## Créer son propre module

Un module = un fichier `.psm1` (+ optionnellement un `.psd1` manifeste).

### Module simple

```powershell
# MesFonctions.psm1
function Get-Hello {
    [CmdletBinding()]
    param ([string]$Name)
    "Hello, $Name!"
}

function Get-Goodbye {
    [CmdletBinding()]
    param ([string]$Name)
    "Goodbye, $Name!"
}

Export-ModuleMember -Function Get-Hello, Get-Goodbye
```

```powershell
Import-Module .\MesFonctions.psm1
Get-Hello -Name "Mathieu"
```

### Module avec manifeste

```powershell
New-ModuleManifest -Path .\MesFonctions.psd1 -RootModule .\MesFonctions.psm1 -Author "Mathieu" -Description "Mes fonctions utiles" -ModuleVersion "1.0.0"
```

Le `.psd1` contient les métadonnées : auteur, version, dépendances, fonctions exportées, etc.

## En résumé

- Les modules structurent le code en bibliothèques réutilisables.
- PowerShell Gallery est le dépôt central officiel.
- `Microsoft.Graph` est le standard actuel pour Entra ID / M365.
- Créer son propre module = transformer une collection de fonctions en package installable.

## Pour aller plus loin

- [about_Modules](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_modules)
- [PowerShell Gallery](https://www.powershellgallery.com/)
- [Microsoft Graph PowerShell SDK](https://learn.microsoft.com/en-us/powershell/microsoftgraph)
