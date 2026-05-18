# Qu'est-ce que PowerShell ?

**PowerShell** est un shell et un langage d'automatisation conçu par Microsoft.

Comme Bash ou CMD, il permet d'exécuter des commandes et d'automatiser des tâches système.

Mais contrairement aux shells traditionnels qui manipulent principalement du **texte**, PowerShell manipule des **objets**.

C'est la différence fondamentale à comprendre.

| Shell texte | Shell objet |
|---|---|
| Manipule des chaînes de caractères | Manipule des objets .NET |
| Nécessite souvent du parsing texte | Accès direct aux propriétés |
| Fragile aux changements d'affichage | Structuré et prédictible |

Exemple :

CMD :

```cmd
tasklist
```

PowerShell :

```powershell
Get-Process
```

Dans PowerShell, les résultats retournés possèdent :
- des propriétés,
- des méthodes,
- un type.

Cette approche rend le pipeline PowerShell extrêmement puissant.

---

## Pourquoi apprendre PowerShell ?

- [x] Automatiser des tâches répétitives
- [x] Administrer Windows efficacement
- [x] Interagir avec Microsoft 365, Azure et Active Directory
- [x] Manipuler des APIs REST et des services cloud
- [x] Gérer des centaines de machines comme une seule

!!! info "PowerShell est partout"
    Aujourd'hui, PowerShell est utilisé aussi bien :
    
    - en administration système,
    - en cybersécurité,
    - en DevOps,
    - en cloud,
    - en scripting d'entreprise.

---

## PowerShell 5.1 vs PowerShell 7

| | PowerShell 5.1 | PowerShell 7 |
|---|---|---|
| Inclus dans Windows | Oui | Non |
| Cross-platform | Non | Oui |
| Basé sur | .NET Framework | .NET |
| Performance | Correcte | Meilleure |
| Nouvelles fonctionnalités | Limitées | Oui |

### Recommandation

- Utilisez **PowerShell 7** pour les nouveaux projets.
- Gardez **PowerShell 5.1** pour certains anciens modules Microsoft.

Exemples :
- ancien module ActiveDirectory,
- certains modules Exchange On-Prem,
- outils historiques Microsoft.

---

## Windows PowerShell vs PowerShell 7

| Windows PowerShell | PowerShell 7 |
|---|---|
| `powershell.exe` | `pwsh.exe` |
| Windows uniquement | Windows / Linux / macOS |
| Technologie historique | Version moderne |

Afficher votre version :

```powershell
$PSVersionTable
```

---

## Execution Policy

PowerShell protège l'exécution des scripts via une **Execution Policy**.

| Policy | Description |
|---|---|
| `Restricted` | Aucun script autorisé |
| `AllSigned` | Tous les scripts doivent être signés |
| `RemoteSigned` | Les scripts téléchargés doivent être signés |
| `Bypass` | Désactive les restrictions |

### Configuration recommandée

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Afficher les policies actives :

```powershell
Get-ExecutionPolicy -List
```

!!! warning "Bonne pratique"
    Évitez `Bypass` à l'échelle machine.

    Préférez :
    - `RemoteSigned`
    - le scope `CurrentUser`
    - ou un bypass temporaire en CI/CD.

---

## Environnement recommandé

Aujourd'hui, l'environnement recommandé pour travailler avec PowerShell est :

- VS Code
- Windows Terminal
- Extension PowerShell Microsoft

Avantages :
- autocomplétion,
- coloration syntaxique,
- terminal intégré,
- debugging,
- formatage automatique,
- analyse de bonnes pratiques.

!!! tip "Conseil"
    Évitez l'ancien PowerShell ISE.
    
    Il n'est plus activement développé.