# Formation Fusion 2 jours — Notes de conception

Cette formation condense **Niv.1 + Niv.2** en un parcours **2 jours** allant de la première commande à la fonction de production complète (paramètres validés, gestion d'erreurs, aide intégrée, tests).

## Sources

- **Niv.1** : Introduction, Triforce, pipeline, variables, collections, opérateurs, manipulation d'objets, import/export.
- **Niv.2** : Paramètres avancés, outputs structurés, canaux de sortie, gestion d'erreurs, splatting, Comment-Based Help, guidelines.
- **Ajouts spécifiques** : `SupportsShouldProcess` (WhatIf/Confirm), Pester (tests unitaires), intégration PSScriptAnalyzer.

## Organisation pédagogique

### Jour 1 — Fondations & manipulation d'objets

**Matin (3h30)** : Shell, syntaxe, Triforce (Get-Command / Get-Help / Get-Member), pipeline & objets (ByValue / ByPropertyName), variables, collections (Array / Hashtable / PSCustomObject), variables automatiques.

**Après-midi (3h30)** : Opérateurs (comparaison, logique, type, arithmétique, `-f`), manipulation d'objets (Where / Select / Sort / Group / ForEach / Compare), import/export (CSV, JSON, CliXml), **TP**.

### Jour 2 — Scripting & qualité de code

**Matin (3h30)** : Scripts vs fonctions, Execution Policy, structures de contrôle (if / foreach / while / switch / break / continue), squelette de code (Inputs / Outputs / Contrôles), paramètres avancés (Mandatory, Position, Validate*, ParameterSet, ValueFromPipeline).

**Après-midi (3h30)** : Outputs structurés (PSCustomObject) + 6 canaux de sortie (Success / Error / Warning / Verbose / Debug / Information), gestion d'erreurs (Try / Catch / Finally + types d'exception), splatting + format operator + type accelerators, `SupportsShouldProcess` (WhatIf / Confirm), Comment-Based Help, Pester (introduction aux tests), guidelines & PSScriptAnalyzer, **TP final**.

## Ajouts par rapport à Niv.1+Niv.2

- **SupportsShouldProcess** : absent des deux formations d'origine, ajouté car indispensable pour les fonctions destructives (20 min, J2 après-midi).
- **PSScriptAnalyzer** : mentionné dans Niv.2 (guidelines) mais peu développé. Ici, installation + usage + intégration VSCode détaillés (15 min, J2 après-midi).
- **Opérateur `-f`** : présent dans Niv.2 mais étendu avec plus d'exemples (padding, pourcentage, template).
- **Variables automatiques enrichies** : `$PSScriptRoot`, `$_`, `$args`, `$PROFILE`, `$env:*` absents ou survolés dans Niv.1, centralisés ici.
- **Syntaxe simplifiée Where-Object** : `Where-Object Status -eq Running` absente de Niv.1, ajoutée.
- **Pipeline ByValue / ByPropertyName** : survolé dans Niv.1, détaillé ici avec exemples.
- **Exercices intercalés** : 5 exercices courts + 1 TP final au lieu de 2 blocs en fin de journée, pour renforcement immédiat après chaque bloc thématique.

## Coupes par rapport au cumul Niv.1+Niv.2

Blocs retirés ou réduits pour tenir dans 2 jours :

- **Pester** : supprimé. Trop avancé pour une formation débutant/intermédiaire 2 jours. Mériterait une formation dédiée ou un Niv.3. Les 25 min libérées ont été redistribuées : +5 min ShouldProcess, +5 min PSScriptAnalyzer, +15 min de marge de sécurité.
- **Regex IP complexe** (Niv.1) : remplacée par un renvoi vers `-as [ipaddress]`.
- **Compare-Object** : conservé mais en survol rapide (rare en pratique).
- **CMD vs PowerShell** (Niv.1) : déplacé en annexe (handout).
- **Remote PowerShell** (Niv.1) : déplacé en annexe (handout).
- **Credentials avancés** (Niv.1) : déplacé en annexe, seul `Export-Clixml` pour stocker un cred est conservé dans Import/Export.
- **Modules** (Niv.1) : déplacé en annexe (handout).
- **Workshops JSON consolidation et Create AD Users** (Niv.2) : retirés du parcours principal, donnés en exercices optionnels post-formation. Seul le workshop **Computer Info** est conservé comme TP final J2.
- **ParameterSets avancés** (Niv.2) : un seul exemple (Convert-IPMask), pas de cas complexes multi-sets.
- **Type Accelerators avancés** (Niv.2) : conservé uniquement `[ipaddress]`, `[version]`, regex nommée (exemple TV show).

## Timing détaillé

Voir `annexes/plan_detaille.md` pour le séquencement heure par heure.

## Formats livrables

- **Support stagiaire** : tous les chapitres au format Retype (navigable, indexé, cherchable).
- **Handout annexes** : CMD vs PS, Remote PS, Credentials, Modules, workshops optionnels.
- **TP avec solutions** : intégrés dans le support.
- **Plan détaillé formateur** : séquencement heure par heure.

## Prérequis environnement

- PowerShell 5.1 minimum, **7.x recommandé**.
- VSCode + extension PowerShell.
- Modules à installer dès J1 : `PSScriptAnalyzer`, `Pester` (v5+).
- Accès Internet pour `Update-Help` et installation de modules.

## Adaptation au public

Le plan détaillé marque les **blocs compressibles** si le public prend du retard, et les **blocs non négociables** (Try/Catch, PSCustomObject, paramètres Mandatory/Validate*).

La formation peut être allégée en 1,5 jour en retirant Pester et en compressant les opérateurs (cheat-sheet), ou étendue à 2,5 jours en ajoutant les workshops optionnels et en détaillant Pester + REST APIs.
