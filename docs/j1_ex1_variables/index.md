# Exercice 1 — Variables et Collections

**Durée : 15 min**
**Couverture : Variables, Array, Hashtable, PSCustomObject**

---

## 1.1 — Les services

Stockez la liste de tous les services Windows dans une variable. **Sans relancer la commande**, répondez :

1. Combien y a-t-il de services au total ?
2. Quel est le nom du premier service ?
3. Quel est le statut du dernier service ?


## 1.2 — Un serveur en objet

Créez un `PSCustomObject` représentant un serveur avec :

```text
Nom    : SRV-WEB01
IP     : 192.168.1.10
OS     : Windows Server 2022
Statut : Online
```

Puis :
1. Affichez une chaîne `"Serveur : <Nom> - <IP>"`
2. Modifiez le statut en `Offline`


## 1.3 — Une hashtable d'environnements

Créez une hashtable avec 3 environnements :

```powershell
PROD = "SRV-PROD-01"
DEV  = "SRV-DEV-01"
TEST = "SRV-TEST-01"
```

Affichez le serveur de l'environnement `DEV`.


<details>
<summary><strong>Solutions</strong></summary>

### Solution 1.1

```powershell
$services = Get-Service
$services.Count
$services[0].Name
$services[-1].Status
```

### Solution 1.2

```powershell
$srv = [PSCustomObject]@{
    Nom    = "SRV-WEB01"
    IP     = "192.168.1.10"
    OS     = "Windows Server 2022"
    Statut = "Online"
}

"Serveur : $($srv.Nom) - $($srv.IP)"
$srv.Statut = "Offline"
```

### Solution 1.3

```powershell
$env = @{
    PROD = "SRV-PROD-01"
    DEV  = "SRV-DEV-01"
    TEST = "SRV-TEST-01"
}

$env.DEV
# ou
$env["DEV"]
```

</details>
