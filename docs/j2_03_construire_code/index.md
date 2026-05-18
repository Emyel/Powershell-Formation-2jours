# Construire son code

## Tout commence par une commande

On identifie d'abord la ou les **commandes-cœur** : celles qui récupèrent les données ou qui effectuent l'action finale du code.

On apprend précisément leur fonctionnement, les propriétés et méthodes des objets qu'elles renvoient.

Le code consistera ensuite à **entourer ces commandes** de :

- validations d'entrée,
- logique métier,
- contrôle d'erreur,
- formatage des données renvoyées.

**Exemple** : un script qui désactive des comptes AD va *orchestrer* plusieurs opérations (lire un CSV, appeler une fonction de désactivation, journaliser).
La désactivation elle-même est une fonction réutilisable.

## Le squelette en 3 piliers

**Inputs** — paramètres validés, typés, nommés.
**Outputs** — données structurées en objets, retour cohérent.
**Contrôles** — gestion d'erreurs, verbosité, debug.

```powershell
function Verb-Noun {
    [CmdletBinding()]
    param (
        # === INPUTS : paramètres validés ===
        [Parameter(Mandatory)]
        [string]$ComputerName
    )

    # === CŒUR DU CODE ===
    $Data = Get-CimInstance -ComputerName $ComputerName -ClassName Win32_OperatingSystem

    # === OUTPUTS : un objet structuré ===
    [PSCustomObject]@{
        ComputerName = $ComputerName
        OSVersion    = $Data.Caption
    }
}
```

!!! note "Et la gestion d'erreurs ? Et le pipeline ?"
    Ce squelette est volontairement simplifié. Dans les pages suivantes,
    on va l'enrichir avec les blocs `begin`/`process`/`end`,
    `ValueFromPipeline`, `Try/Catch` et les canaux `Verbose`/`Warning`.

## Les blocs `begin` / `process` / `end`

Ces trois blocs structurent l'exécution d'une fonction qui reçoit des objets **depuis le pipeline**.

| Bloc | Quand s'exécute-t-il ? | Usage typique |
|---|---|---|
| `begin` | Une seule fois, avant le premier objet | Initialisation, connexion, log de démarrage |
| `process` | Une fois **par objet** reçu du pipeline | Le cœur du traitement — là où la logique vit |
| `end` | Une seule fois, après le dernier objet | Nettoyage, déconnexion, log de fin |

Sans ces blocs, le corps de la fonction s'exécute une seule fois avec **tous** les objets d'un coup — ce qui casse le streaming pipeline.

```powershell
function Get-OSVersion {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory, ValueFromPipelineByPropertyName)]
        [Alias('Name')]
        [string]$ComputerName
    )

    begin {
        Write-Verbose "Démarrage de Get-OSVersion"
    }

    process {
        # Ce bloc s'exécute une fois par machine reçue du pipeline
        try {
            $OS = Get-CimInstance -ComputerName $ComputerName -ClassName Win32_OperatingSystem -ErrorAction Stop

            [PSCustomObject]@{
                ComputerName = $ComputerName
                OSVersion    = $OS.Caption
            }
        }
        catch {
            Write-Warning "Échec pour $ComputerName : $($_.Exception.Message)"
        }
    }

    end {
        Write-Verbose "Fin de Get-OSVersion"
    }
}

# Appel direct
Get-OSVersion -ComputerName SRV01

# Appel depuis le pipeline — process s'exécute une fois par machine
"SRV01", "SRV02", "SRV03" | Get-OSVersion -Verbose

# Compatible avec Get-ADComputer grâce à l'alias 'Name'
Get-ADComputer -Filter * | Get-OSVersion
```

!!! tip "Règle pratique"
    Dès qu'une fonction a `ValueFromPipeline` ou `ValueFromPipelineByPropertyName` sur un paramètre, elle doit avoir un bloc `process`. Sinon, seul le **dernier** objet du pipeline sera traité.

## Fonction ou script ?

**Fonction**
Mono-tâche, spécialisée, réutilisable dans n'importe quel contexte.

**Script**
Orchestration d'un processus. Si un bloc de code est répété → il devient une fonction (dans le même fichier ou dans une bibliothèque chargée par dot-sourcing).