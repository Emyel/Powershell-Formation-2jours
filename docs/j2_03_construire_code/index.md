
# Construire son code

## Tout commence par une commande

On identifie d'abord la ou les **commandes-cœur** : celles qui récupèrent les données ou qui effectuent l'action finale du code.

On apprend précisément leur fonctionnement, les propriétés et méthodes des objets qu'elles renvoient.

Le code consistera ensuite à **entourer ces commandes** de :

- validations d'entrée,
- logique métier,
- contrôle d'erreur,
- formatage des données renvoyées.

## Le squelette en 3 piliers

**Inputs** — paramètres validés, typés, nommés.
**Outputs** — données structurées en objets, retour cohérent.
**Contrôles** — gestion d'erreurs, verbosité, debug.

```powershell
function Verb-Noun
{
    [CmdletBinding()]
    param (
        # === INPUTS : paramètres validés ===
        [Parameter(Mandatory)]
        [string]$ComputerName
    )

    begin {
        # === CONTRÔLES : initialisation, logs ===
        Write-Verbose "Start $($MyInvocation.MyCommand)"
    }

    process {
        try {
            # === CŒUR DU CODE ===
            $Data = Get-CimInstance -ComputerName $ComputerName -ClassName ... -ErrorAction Stop

            # === OUTPUTS : un objet structuré ===
            [PSCustomObject]@{
                ComputerName = $ComputerName
                Property1    = $Data.Property1
            }
        }
        catch {
            # === CONTRÔLES : gestion d'erreur ===
            Write-Warning "Échec pour $ComputerName : $($_.Exception.Message)"
        }
    }

    end {
        Write-Verbose "End $($MyInvocation.MyCommand)"
    }
}
```

C'est **ce squelette** qu'on va remplir progressivement dans les chapitres suivants : paramètres validés, outputs en `PSCustomObject`, canaux Verbose/Warning, Try/Catch, etc.

## Fonction ou script ?

**Fonction**
Mono-tâche, spécialisée, réutilisable dans n'importe quel contexte.

**Script**
Orchestration d'un processus. Si un bloc de code est répété → il devient une fonction (dans le même fichier ou dans une bibliothèque chargée par dot-sourcing).
