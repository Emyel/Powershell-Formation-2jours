
# CMD vs PowerShell — équivalences

Même si l'habitude rend l'utilisation des commandes DOS natives plus rapide, leurs équivalents PowerShell renvoient des **objets** permettant une utilisation plus fine.

| Commande DOS | Équivalent PowerShell | Remarque |
| --- | --- | --- |
| `ipconfig` | `Get-NetIPConfiguration` | Renvoie des objets `NetIPConfiguration` |
| `ping` | `Test-Connection` | Renvoie des objets avec RTT, statut, etc. |
| `telnet <host> <port>` | `Test-NetConnection <host> -Port <port>` | Test de connexion TCP |
| `tracert` | `Test-NetConnection <host> -TraceRoute` | Trace route réseau |
| `nslookup` | `Resolve-DnsName` | Résolution DNS complète |
| `netstat` | `Get-NetTCPConnection` | Liste des connexions TCP actives |
| `net share` | `Get-SmbShare` | Partages SMB locaux |
| `net user` | `Get-LocalUser` | Utilisateurs locaux |
| `tasklist` | `Get-Process` | Processus en cours |
| `taskkill` | `Stop-Process` | Arrêter un processus |
| `dir` | `Get-ChildItem` | Lister fichiers/dossiers |
| `copy` | `Copy-Item` | Copier fichiers |
| `del` | `Remove-Item` | Supprimer fichiers |

## Exemples pratiques

### Tester la connectivité réseau

```powershell
# DOS
ping google.fr

# PowerShell
Test-Connection google.fr -Count 4
```

L'avantage PowerShell : on peut filtrer les résultats, les passer dans le pipeline, les exporter.

```powershell
Test-Connection google.fr -Count 4 | Where-Object { $_.ResponseTime -lt 50 }
```

### Lister les connexions TCP établies

```powershell
# DOS
netstat -ano | findstr ESTABLISHED

# PowerShell
Get-NetTCPConnection -State Established | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, OwningProcess
```

### Résoudre un nom DNS

```powershell
# DOS
nslookup nyxen.com

# PowerShell
Resolve-DnsName nyxen.com
```

Renvoie un objet avec `Type`, `TTL`, `IPAddress`, etc. → exploitable dans un script.

## En résumé

Les commandes DOS restent pratiques pour un usage terminal rapide. Dès qu'on **scripte**, les cmdlets PowerShell offrent une manipulation objet incomparablement plus puissante.
