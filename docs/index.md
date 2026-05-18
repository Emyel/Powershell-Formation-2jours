
# Formation Powershell — 2 jours

Cette formation condense en 2 jours les fondamentaux PowerShell **et** les bonnes pratiques de scripting pour passer d'un code "vite-fait" à un code fiable, maintenable et réutilisable.

## Public visé

IT, sysadmins, administrateurs identité / poste de travail, débutants ou auto-didactes en PowerShell.
Pas de prérequis fort : il faut savoir ouvrir une console et avoir une idée de ce qu'est une commande shell.

## Objectifs pédagogiques

À l'issue de la formation, le stagiaire doit être capable de :

- Comprendre la philosophie **objet** de PowerShell.
- Construire des **pipelines** propres pour filtrer, trier, regrouper, transformer des données.
- Écrire une **fonction avancée** avec paramètres validés, gestion d'erreurs, sorties contrôlées et aide intégrée.
- Appliquer les **bonnes pratiques** (nommage, structure, idempotence, `-WhatIf` / `-Confirm`).
- Savoir où chercher pour aller plus loin (modules, PSScriptAnalyzer).

## Déroulé

| Jour | Bloc | Contenu |
| --- | --- | --- |
| **J1 matin** | Fondations | Shell, syntaxe, triforce, pipeline & objets, variables, collections |
| **J1 après-midi** | Pipeline & objets | Opérateurs, manipulation d'objets, import/export, **TP** |
| **J2 matin** | Du one-liner à la fonction | Scripts/fonctions, structures, squelette, paramètres avancés |
| **J2 après-midi** | Robustesse & qualité | Outputs structurés, canaux, erreurs, splatting, help, **ShouldProcess**, guidelines, **TP final** |

## Format

- Théorie + démos en live.
- **TP courts** à la fin de chaque demi-journée.
- **TP final** en fin de J2 mobilisant tout ce qui a été vu.
- Ratio cible : ~50% théorie, 50% pratique.

!!! tip "Environnement"
    - PowerShell 5.1 minimum, **7.x recommandé**
    - Éditeur : **VSCode** + extension PowerShell
    - Modules à installer dès l'ouverture : `PSScriptAnalyzer`
    
    