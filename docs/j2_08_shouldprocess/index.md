
# SupportsShouldProcess — `-WhatIf` / `-Confirm`

## Le problème

On crée une fonction qui supprime des fichiers, modifie Active Directory, redémarre des serveurs... bref, une **action destructive**.

L'utilisateur aimerait pouvoir :

1. **Simuler** l'exécution sans rien toucher (`-WhatIf`).
2. **Demander confirmation** avant chaque action (`-Confirm`).

C'est ce que font les commandes natives :

```powershell
Remove-Item C:\temp\*.log -WhatIf   # Simule, n'exécute rien
Stop-Service Spooler -Confirm        # Demande confirmation
```

## La solution : `SupportsShouldProcess`

On ajoute cet attribut à `[CmdletBinding()]` et on encadre les actions destructives dans un `if ($PSCmdlet.ShouldProcess(...))`.

```powershell
function Remove-OldFiles {
    [CmdletBinding(SupportsShouldProcess)]
    param (
        [Parameter(Mandatory)]
        [string]$Path,

        [int]$DaysOld = 30
    )

    $Files = Get-ChildItem -Path $Path -File | Where-Object {
        $_.LastWriteTime -lt (Get-Date).AddDays(-$DaysOld)
    }

    foreach ($File in $Files) {
        if ($PSCmdlet.ShouldProcess($File.FullName, "Supprimer")) {
            Remove-Item $File.FullName -Force
        }
    }
}
```

### Utilisation

```powershell
# Simuler
Remove-OldFiles -Path C:\temp -WhatIf
```

```text
Opération « Supprimer » en cours sur la cible « C:\temp\fichier1.log ».
Opération « Supprimer » en cours sur la cible « C:\temp\fichier2.log ».
```

Rien n'est supprimé.

```powershell
# Demander confirmation
Remove-OldFiles -Path C:\temp -Confirm
```

```text
Confirmer
Voulez-vous vraiment effectuer cette action ?
Opération « Supprimer » en cours sur la cible « C:\temp\fichier1.log ».
[O] Oui  [T] Oui pour tout  [N] Non  [A] Non pour tout  [S] Suspendre  [?] Aide (« Oui » par défaut) :
```

## Syntaxe `ShouldProcess`

```powershell
$PSCmdlet.ShouldProcess($Target, $Action)
```

- `$Target` : l'objet / chemin sur lequel on agit (affiché à l'utilisateur).
- `$Action` : le verbe d'action (affiché à l'utilisateur).

Si `-WhatIf` est présent, `ShouldProcess` renvoie **toujours `$false`** → le bloc `if` n'est jamais exécuté.

Si `-Confirm` est présent, `ShouldProcess` affiche un prompt et renvoie `$true` ou `$false` selon la réponse.

## Quand l'utiliser ?

**Toujours** sur les fonctions qui :

- Suppriment / modifient des données.
- Redémarrent / arrêtent des services / machines.
- Modifient Active Directory, Exchange, Entra ID.
- Envoient des emails.
- Font des changements irréversibles.

!!! danger Mauvaise pratique
Coder un `if ($WhatIf)` manuel dans les paramètres. PowerShell gère ça nativement avec `SupportsShouldProcess` — c'est standardisé, attendu par les utilisateurs, et intégré aux pipelines.
!!!

## Impact = High

Par défaut, `-Confirm` ne se déclenche que si l'impact de la commande est considéré "élevé". On peut forcer cet impact :

```powershell
[CmdletBinding(SupportsShouldProcess, ConfirmImpact = 'High')]
```

Valeurs possibles : `None`, `Low`, `Medium`, `High`.

La variable `$ConfirmPreference` de l'utilisateur détermine le seuil à partir duquel le prompt de confirmation apparaît. Par défaut, c'est `High`.
