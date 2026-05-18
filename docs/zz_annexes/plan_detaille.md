
# Plan détaillé — heure par heure

Document à usage du **formateur**. Le timing est indicatif et doit être adapté au public.

## Jour 1

### Matin (09:00 → 12:30 — pause 15 min vers 10:45)

| Plage | Durée | Bloc | Pages support |
| --- | --- | --- | --- |
| 09:00 → 09:15 | 15 min | Tour de table + présentation de la formation | `index` |
| 09:15 → 09:30 | 15 min | Intro : qu'est-ce que PowerShell, pourquoi se former | `jour1/fondations` |
| 09:30 → 10:15 | 45 min | Syntaxe `Verb-Noun` + Triforce `Get-Command` / `Get-Help` / `Get-Member` | `jour1/triforce` |
| 10:15 → 10:45 | 30 min | Pipeline & langage objet (ByValue / ByPropertyName) | `jour1/pipeline_objets` |
| 10:45 → 11:00 | 15 min | **Pause** | |
| 11:00 → 11:30 | 30 min | Variables, typage, propriétés/méthodes | `jour1/variables` |
| 11:30 → 12:00 | 30 min | Array, Hashtable, PSCustomObject, variables auto | `jour1/collections` |
| 12:00 → 12:15 | 15 min | **💪 Exercice 1 — Variables & Collections** | `jour1/exercice_variables` |
| 12:15 → 12:30 | 15 min | Correction exercice 1 | |

### Après-midi (13:30 → 17:00 — pause 15 min vers 15:15)

| Plage | Durée | Bloc | Pages support |
| --- | --- | --- | --- |
| 13:30 → 14:00 | 30 min | Opérateurs (comparaison, logique, type, arithmétique, `-f`) | `jour1/operateurs` |
| 14:00 → 14:45 | 45 min | Manipulation d'objets : Where / Select / Sort / Group / ForEach | `jour1/manipulation_objets` |
| 14:45 → 15:05 | 20 min | **💪 Exercice 2 — Opérateurs & Manipulation objets** | `jour1/exercice_objets` |
| 15:05 → 15:15 | 10 min | Correction exercice 2 | |
| 15:15 → 15:30 | 15 min | **Pause** | |
| 15:30 → 16:00 | 30 min | Import / Export (CSV, JSON, CliXml) | `jour1/import_export` |
| 16:00 → 16:15 | 15 min | **💪 Exercice 3 — Import/Export** | `jour1/exercice_import_export` |
| 16:15 → 16:30 | 15 min | Correction exercice 3 | |
| 16:30 → 17:00 | 30 min | Synthèse J1, questions, preview J2 | |

## Jour 2

### Matin (09:00 → 12:30 — pause 15 min vers 10:45)

| Plage | Durée | Bloc | Pages support |
| --- | --- | --- | --- |
| 09:00 → 09:15 | 15 min | Récap J1, questions | |
| 09:15 → 09:35 | 20 min | Scripts vs Fonctions, Execution Policy | `jour2/scripts_fonctions` |
| 09:35 → 10:15 | 40 min | Structures de contrôle (if, foreach, while, switch, break/continue) | `jour2/structures` |
| 10:15 → 10:30 | 15 min | Construire son code : squelette Inputs / Outputs / Contrôles | `jour2/construire_code` |
| 10:30 → 10:45 | 15 min | **Pause** | |
| 10:45 → 12:10 | 85 min | Paramètres avancés (Mandatory, Position, Validate*, ParameterSet, ValueFromPipeline) | `jour2/parametres` |
| 12:10 → 12:30 | 20 min | **💪 Exercice 4 — Structures & Paramètres** | `jour2/exercice_structures` |

### Après-midi (13:30 → 17:00 — pause 15 min vers 15:15)

| Plage | Durée | Bloc | Pages support |
| --- | --- | --- | --- |
| 13:30 → 13:40 | 10 min | Correction exercice 4 | |
| 13:40 → 14:10 | 30 min | Outputs structurés : PSCustomObject + 6 canaux (Verbose / Warning / Information / Debug) | `jour2/outputs_canaux` |
| 14:10 → 14:40 | 30 min | Gestion d'erreurs : Try / Catch / Finally + types d'exception | `jour2/erreurs` |
| 14:40 → 15:00 | 20 min | Splatting + Format operator + Type Accelerators | `jour2/beautify` |
| 15:00 → 15:20 | 20 min | **💪 Exercice 5 — Outputs, Erreurs, Splatting** | `jour2/exercice_production` |
| 15:20 → 15:30 | 10 min | **Pause** | |
| 15:30 → 15:50 | 20 min | SupportsShouldProcess : WhatIf / Confirm | `jour2/shouldprocess` |
| 15:50 → 16:05 | 15 min | Comment-Based Help | `jour2/help` |
| 16:05 → 16:20 | 15 min | Guidelines & PSScriptAnalyzer | `jour2/guidelines` |
| 16:20 → 16:50 | 30 min | **🎯 TP Final — Get-ComputerInfo** | `jour2/tp_final` |
| 16:50 → 17:00 | 10 min | Correction TP final, conclusion, questions | |

---

## Notes formateur

### ⚠️ Blocs compressibles si retard

- Opérateur `-f` (10 min) → donner cheat-sheet
- Compare-Object (5 min) → survol rapide ou skip
- Type Accelerators (10 min) → donner exemples sans détailler

### ✅ Blocs NON négociables

- Try / Catch (essentiel production)
- PSCustomObject (retour de fonctions)
- Paramètres Mandatory / Validate* (qualité du code)
- Pipeline ValueFromPipeline (réutilisabilité)

### 🎯 Timing libéré suite suppression Pester

**25 min récupérées** → utilisées pour :
- Renforcer ShouldProcess (+5 min, passé de 15 à 20 min)
- Renforcer Guidelines/PSScriptAnalyzer (+5 min, passé de 10 à 15 min)
- Marge de sécurité (+15 min répartis sur J2)

### 📊 Répartition pédagogique

| Type | Durée totale | % |
| --- | --- | --- |
| Cours magistral | ~9h00 | 65% |
| Exercices pratiques | ~2h30 | 18% |
| Pauses | ~1h15 | 9% |
| Questions / Ajustements | ~1h15 | 9% |

### 💡 Conseils animation

- **Exercices** : ne PAS donner la solution avant le temps écoulé (même si tout le monde a fini)
- **TP Final** : valoriser les approches partielles, ne pas exiger la perfection
- **Questions** : noter les questions "avancées" pour y répondre en fin de session
- **Rythme** : mieux vaut finir 15 min en avance que bâcler la fin


## Variantes possibles

### Formation 1,5 jour (réduction)

Supprimer :
- Opérateur `-f` (donner cheat-sheet)
- Compare-Object
- Type Accelerators
- SupportsShouldProcess (mentionner juste l'existence)

### Formation 2,5 jours (extension)

Ajouter :
- Pester (45 min — tests unitaires avec démo)
- Modules (30 min — créer son module)
- REST APIs (45 min — Invoke-RestMethod)
- Workshops optionnels (JSON consolidation, AD Users)

| 15:00 → 15:15 | 15 min | **Pause** | |
| 15:15 → 16:00 | 45 min | Splatting + Format Operator + Type Accelerators | `jour2/beautify` |
| 16:00 → 16:20 | 20 min | `SupportsShouldProcess` (`-WhatIf` / `-Confirm`) | `jour2/shouldprocess` |
| 16:20 → 16:40 | 20 min | Comment-Based Help | `jour2/help` |
| 16:40 → 17:00 | 20 min | Pester en 5 minutes + Guidelines & PSScriptAnalyzer | `jour2/pester` + `jour2/guidelines` |
| (optionnel) | | **TP final** : workshop Computer Info | `jour2/exercices` |

!!! tip "Si on a du temps en plus"
    - Pousser sur le **TP final** (workshop Computer Info) en J2 après-midi.
    - Sinon, donner le TP en exercice "à la maison" avec correction en début de session suivante (si formation Niv.3 enchaînée).

!!! warning "Si on a moins de temps"
    Les blocs à compresser en priorité :
    - Opérateurs (donner en cheat-sheet).
    - Compare-Object (rare en pratique).
    - Foreach-Object **vs** mot-clé foreach (à traiter dans `structures` uniquement).

Le bloc à **ne jamais sacrifier** : Try / Catch et PSCustomObject.
