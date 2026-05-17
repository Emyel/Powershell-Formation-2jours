
# Syntaxe & les 3 commandes indispensables

## Syntaxe `Verb-Noun`

Toutes les commandes PowerShell (appelées **cmdlets**) se composent d'un verbe et d'un nom séparés par un tiret. Le résultat de la commande peut être influencé par l'ajout de **paramètres**.

```powershell
Get-Service                  # → tous les services
Get-Service -Name "*Net*"    # → uniquement ceux dont le nom contient "Net"
```

Chaque verbe correspond à un type d'action précis :

| Verbe | Action |
| --- | --- |
| `Get` | Requêter |
| `Set` | Configurer / Définir |
| `New` | Créer |
| `Remove` | Supprimer |
| `Start` / `Stop` | Démarrer / Arrêter |
| `Test` | Vérifier |

`Get-Verb` donne la liste complète des verbes approuvés par les bonnes pratiques PowerShell.

## La Triforce

Trois commandes à connaitre par cœur. Avec elles, on peut **découvrir tout le reste** de PowerShell.

### `Get-Command` — chercher une commande

```powershell
Get-Command -Name "*process*"
Get-Command -Verb Get -Noun *service*
Get-Command -Module ActiveDirectory
```

### `Get-Help` — obtenir l'aide d'une commande

```powershell
Get-Help Get-Service
Get-Help Get-Service -Examples
Get-Help Get-Service -Full
Get-Help Get-Service -Online   # ouvre la page Microsoft Docs
```

!!! tip 
> Mettre à jour l'aide locale : `Update-Help` (en admin) télécharge la dernière version de l'aide pour toutes les commandes installées.
!!!

### `Get-Member` — connaître un objet

`Get-Member` donne la **carte d'identité** d'un objet : son type, ses propriétés, ses méthodes.

```powershell
Get-Process | Get-Member
```

```text
   TypeName: System.Diagnostics.Process

Name      MemberType   Definition
----      ----------   ----------
Kill      Method       void Kill()
Refresh   Method       void Refresh()
Id        Property     int Id {get;}
Name      Property     string Name {get;}
```

C'est avec `Get-Member` qu'on découvre qu'une variable contient bien plus qu'elle n'affiche par défaut.
