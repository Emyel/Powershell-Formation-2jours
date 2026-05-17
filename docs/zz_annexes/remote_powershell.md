
# Remote PowerShell

À l'instar de SSH sur Unix, PowerShell permet l'ouverture de sessions distantes et l'exécution de commandes sur des machines distantes.

## Activer WinRM sur la machine cible

```powershell
Enable-PSRemoting -Force
```

Côté firewall, le port **5985** (HTTP) ou **5986** (HTTPS) doit être ouvert.

## Session interactive

```powershell
Enter-PSSession -ComputerName "Server01"
```

On obtient un prompt `[Server01]:` — toutes les commandes tapées s'exécutent sur la machine distante.

Sortir : `Exit-PSSession`.

## Session persistante

```powershell
$Session = New-PSSession -ComputerName "Server01"

# Exécuter une commande
Invoke-Command -Session $Session -ScriptBlock { Get-Process }

# Copier un fichier vers la machine distante
Copy-Item -ToSession $Session -Path "C:\local\script.ps1" -Destination "C:\remote\script.ps1"

# Exécuter le script distant
Invoke-Command -Session $Session -ScriptBlock { . "C:\remote\script.ps1" }

# Fermer la session
Remove-PSSession $Session
```

## Exécuter sur plusieurs machines

```powershell
$Servers = "SRV01","SRV02","SRV03"

Invoke-Command -ComputerName $Servers -ScriptBlock {
    Get-Service | Where-Object { $_.Status -eq "Running" }
}
```

Chaque serveur exécute le scriptblock en parallèle. Le résultat est regroupé avec une colonne `PSComputerName`.

## Credentials

Si la machine distante nécessite des credentials différents :

```powershell
$Creds = Get-Credential
New-PSSession -ComputerName "Server01" -Credential $Creds
```

## Cas d'usage typiques

- **Déployer un script** sur plusieurs serveurs.
- **Collecter des logs** depuis plusieurs machines.
- **Redémarrer un service** sur un parc de serveurs.
- **Installer un module** sur un cluster.

## Limitations & sécurité

- **Double-hop** : si vous êtes sur A, connecté à B, et que B doit se connecter à C → ça échoue par défaut (délégation de credentials). Solution : CredSSP (désactivé par défaut pour raisons de sécurité).
- **Kerberos / NTLM** : selon votre domaine, la configuration d'authentification peut nécessiter des ajustements (TrustedHosts pour workgroup, etc.).
- **HTTPS** : pour chiffrer la session, configurer WinRM avec un certificat.

## Pour aller plus loin

- [about_Remote](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_remote)
- [Invoke-Command](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/invoke-command)
- SSH remoting : PowerShell 7+ supporte SSH comme transport alternatif à WinRM.
