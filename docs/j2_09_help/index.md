
# Comment-Based Help — documenter sa fonction

Une fonction bien faite a une **aide intégrée** accessible via `Get-Help`.

```powershell
function Get-ComputerStatus {
<#
.SYNOPSIS
    Récupère le statut d'une ou plusieurs machines.

.DESCRIPTION
    Cette commande interroge des ordinateurs distants pour récupérer leur version d'OS,
    leur consommation CPU, leur RAM disponible et l'espace disque libre.

.PARAMETER ComputerName
    Le ou les noms des machines à interroger. Accepte le pipeline.

.EXAMPLE
    Get-ComputerStatus -ComputerName Computer01

    ComputerName : Computer01
    OSVersion    : Microsoft Windows 11 Professionnel 10.0.22621
    CPUName      : Intel(R) Core(TM) i7-8700K CPU @ 3.70GHz
    FreeRAM      : 2,84 GB

.EXAMPLE
    Get-ADComputer -Filter * | Get-ComputerStatus -Verbose

    Interroge toutes les machines du domaine et affiche les messages de progression.

.NOTES
    Version    : 1.0.0
    Auteur     : Mathieu
    Dernière MàJ : 05/10/2023

.LINK
    https://docs.contoso.com/powershell/Get-ComputerStatus
#>
    [CmdletBinding()]
    param (
        [Parameter(Mandatory, ValueFromPipeline, ValueFromPipelineByPropertyName)]
        [Alias('Name','Hostname')]
        [string[]]$ComputerName
    )

    process {
        foreach ($Computer in $ComputerName) {
            # ...
        }
    }
}
```

## Afficher l'aide

```powershell
Get-Help Get-ComputerStatus
Get-Help Get-ComputerStatus -Examples
Get-Help Get-ComputerStatus -Parameter ComputerName
Get-Help Get-ComputerStatus -Full
```

## Mots-clés disponibles

| Mot-clé | Usage |
| --- | --- |
| `.SYNOPSIS` | Description courte (une ligne). |
| `.DESCRIPTION` | Description détaillée (plusieurs paragraphes possibles). |
| `.PARAMETER <Nom>` | Documentation d'un paramètre. À répéter pour chaque paramètre. |
| `.EXAMPLE` | Exemple d'utilisation. À répéter autant que nécessaire. |
| `.INPUTS` | Type(s) d'objet acceptés via le pipeline. |
| `.OUTPUTS` | Type(s) d'objet retournés. |
| `.NOTES` | Informations complémentaires (version, auteur, prérequis, changelog). |
| `.LINK` | URL vers la documentation complète. |

## Emplacement

Le bloc de commentaire peut être placé :

- **Juste avant la fonction** (recommandé).
- **Juste après `function Nom {`** (début du corps).
- **À la fin du corps de la fonction** (avant la dernière accolade).

La première position est la plus lisible.

## Pourquoi c'est important ?

Une fonction sans aide est une fonction qu'on ne réutilise pas 6 mois plus tard, parce qu'on a oublié comment elle marche.

Le `.SYNOPSIS` + `.DESCRIPTION` + `.EXAMPLE` transforment une fonction en cmdlet professionnelle.

Coût : 2 minutes. Bénéfice : le prochain utilisateur (souvent vous-même dans 3 mois) gagne 30 minutes.
