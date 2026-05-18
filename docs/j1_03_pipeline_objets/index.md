# Le pipeline PowerShell

Le pipeline est l'un des concepts les plus importants de PowerShell.

Il permet de transmettre le résultat d'une commande vers une autre commande grâce au caractère :

```powershell
|
```

Exemple :

```powershell
Get-Process | Sort-Object CPU
```

Ici :
- `Get-Process` récupère les processus,
- `Sort-Object` trie les résultats.

---

## PowerShell ne manipule pas du texte

C'est une erreur fréquente chez les débutants.

Quand vous exécutez :

```powershell
Get-Service
```

PowerShell ne retourne PAS du texte.

Il retourne des objets .NET.

Le texte affiché à l'écran n'est qu'une représentation visuelle simplifiée.

---

## Voir le vrai contenu des objets

Utilisez `Get-Member` :

```powershell
Get-Service | Get-Member
```

Exemple :

```text
TypeName: System.ServiceProcess.ServiceController
```

Cela signifie que chaque élément retourné est un objet possédant :
- des propriétés,
- des méthodes,
- un type réel.

---

## Propriétés vs méthodes

### Les propriétés

Les propriétés contiennent des informations.

Exemple :

```powershell
Get-Process | Select-Object Name, Id
```

Résultat :

```text
Name        Id
----        --
chrome      8420
explorer    5200
```

---

### Les méthodes

Les méthodes permettent d'effectuer des actions.

Exemple :

```powershell
(Get-Process notepad).Kill()
```

Cette méthode ferme le processus Notepad.

---

## Le pipeline transporte des objets

Exemple :

```powershell
Get-Process | Stop-Process
```

`Stop-Process` reçoit directement les objets processus.

Il n'a pas besoin de parser du texte.

C'est ce qui rend PowerShell :
- robuste,
- cohérent,
- extrêmement automatisable.

---

## Sélectionner des propriétés

PowerShell n'affiche souvent qu'une partie des informations disponibles.

Afficher certaines propriétés :

```powershell
Get-Service | Select-Object Name, Status
```

Afficher toutes les propriétés :

```powershell
Get-Service | Select-Object *
```

---

## Filtrer les objets

Exemple :

```powershell
Get-Service | Where-Object Status -eq Running
```

Ici :
- PowerShell récupère les services,
- puis conserve uniquement ceux dont le statut est `Running`.

---

## Résumé

PowerShell est puissant parce qu'il manipule des objets et non du texte.

Le pipeline permet :
- de transmettre ces objets,
- de les filtrer,
- de les transformer,
- de les exploiter automatiquement.

!!! tip "Réflexe essentiel"
    Quand une commande retourne un résultat inattendu :

    ```powershell
    Ma-Commande | Get-Member
    ```

    Comprendre l'objet permet presque toujours de comprendre la commande.