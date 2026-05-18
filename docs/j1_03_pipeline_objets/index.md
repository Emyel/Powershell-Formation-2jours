
# Pipeline & objets

## Langage objet

Sous PowerShell, chaque commande renvoie un **objet** d'un type précis possédant ses propriétés et ses méthodes. C'est ce qui change tout par rapport à Bash où tout est texte.

```powershell
Get-Service Spooler | Get-Member
```

L'objet `ServiceController` retourné a des propriétés (`Name`, `Status`, `StartType`...) et des méthodes (`Stop()`, `Start()`, `Refresh()`...). On peut les manipuler directement.

## Le pipeline `|`

Le pipeline (`AltGr + 6` sur clavier français) **chaîne** les commandes : la **sortie** de la gauche devient l'**entrée** de la droite.

```powershell
# Sans pipeline
Get-Process -Name notepad
Stop-Process -Name notepad

# Avec pipeline
Get-Process -Name notepad | Stop-Process
```

## ByValue vs ByPropertyName

Une commande de droite peut recevoir un objet du pipeline de deux façons :

**ByValue** — la commande accepte l'objet entier comme valeur d'un paramètre, si le type correspond.

```powershell
Get-Process notepad | Stop-Process
# Stop-Process accepte directement des objets Process en pipeline
```

**ByPropertyName** — la commande cherche une **propriété du même nom** (ou alias) que ses paramètres.

```powershell
Get-ADUser jdupont | Set-Mailbox -DisplayName "Jean Dupont"
# Set-Mailbox prend la propriété "Identity" de Get-ADUser comme paramètre -Identity
```

Pour savoir comment un paramètre accepte le pipeline :

```powershell
Get-Help Stop-Process -Parameter Name
```

```text
-Name <String[]>
    ...
    Accept pipeline input?       True (ByPropertyName, ByValue)
```

!!! NOTE
> Pourquoi c'est important ?
> C'est en comprenant **ByValue / ByPropertyName** qu'on passe du copier-coller au scripting autonome. La plupart des "magies" du pipeline reposent là-dessus.
