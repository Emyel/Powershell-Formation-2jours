
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

## Fonction ou script ?

**Fonction**
Mono-tâche, spécialisée, réutilisable dans n'importe quel contexte.

**Script**
Orchestration d'un processus. Si un bloc de code est répété → il devient une fonction (dans le même fichier ou dans une bibliothèque chargée par dot-sourcing).
