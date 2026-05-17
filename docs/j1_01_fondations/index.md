
# Jour 1 — Matin

## Bloc 1 — Fondations

### Qu'est-ce que PowerShell ?

**PowerShell** est avant tout un shell : une interface utilisateur permettant de traduire les commandes utilisateur en exécution au niveau du système.

Contrairement à Bash ou CMD qui sont des shells **textes** (ils manipulent des chaînes), PowerShell est un shell **objet** : chaque commande renvoie des objets typés avec leurs propriétés et leurs méthodes. C'est la différence fondamentale dont découle tout le reste.

### Pourquoi se former à PowerShell ?

- [x] **Interopérable** avec tout l'écosystème Microsoft et avec les APIs tierces (REST, .NET, COM).
- [x] **Productivité** : une action manuelle sur 1 élément vs une action scriptée sur 1 000 éléments — le ROI est immédiat.
- [x] **Standardisation** : une fois le code écrit, l'action est rejouable à l'identique, sans oubli ni mauvaise manipulation.
- [x] **Universel** : depuis PowerShell 7, le moteur est cross-platform (Windows, Linux, macOS).

### PowerShell 5.1 vs 7.x — lequel utiliser ?

| | PowerShell 5.1 | PowerShell 7.x |
| --- | --- | --- |
| Installation | Inclus dans Windows 10/11 et Server 2016+ | À installer (winget, MSI) |
| Cross-platform | Non | Oui |
| Performance | Correcte | Notablement meilleure |
| Nouveautés (`-Parallel`, opérateurs `??`/`?.`, etc.) | Non | Oui |

**Recommandation** : 7.x si possible. 5.1 reste indispensable pour certains modules (ActiveDirectory historique, certains modules Exchange on-prem).
