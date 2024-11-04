# Snort 3 - Initial configuration


## I - CONFIGURATION DE LA CARTE RESEAU (A CHAUD)
Activation du mode promiscuous
```
ip link set dev ens18 promisc on
```
>En mode normal, une interface réseau ne traite que les paquets qui sont adressés spécifiquement à elle, plus les paquets de diffusion (broadcast) et de multidiffusion (multicast) auxquels elle est abonnée.

>En mode promiscuous, l'interface réseau traite tous les paquets qu'elle voit passer sur le réseau, indépendamment du fait qu'ils lui soient destinés ou non.

Configuration de l'offload (exemple pour ens18)
```
ip link show ens18
apt install ethtool
ethtool -k ens18 | grep receive-offload
ethtool -K ens18 gro off lro off
ethtool -K ens18 gro off gro off
ethtool -k ens18 | grep receive-offload
```
>La commande ethtool -k ens18 | grep receive-offload permet de vérifier les paramètres d'optimisation de la réception des paquets sur l'interface ens18 GRO et LRO combinent plusieurs paquets TCP reçus en un seul paquet plus grand pour réduire le nombre de paquets à traiter.

>La commande ethtool -K ens18 gro off lro off est utilisée pour désactiver deux types d'optimisations matérielles de réception sur l'interface réseau ens18.
Désactiver ces optimisations est souvent nécessaire pour des outils d'analyse de paquets comme Snort ou Wireshark, afin de garantir que tous les paquets sont capturés et analysés individuellement.


## II - CONFIGURATION DU PARAMETRAGE AUTO DE LA NIC EN TANT QUE SERVICE
Création du fichier de service
```
nano /etc/systemd/system/snort3-nic.service
```
Contenu du fichier (exemple pour ens18)
```
[Unit]
Description=Set Snort 3 NIC in promiscuous mode and Disable GRO, LRO on boot
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ip link set dev ens18 promisc on
ExecStart=/usr/sbin/ethtool -K ens18 gro off lro off
ExecStart=/usr/sbin/ethtool -K ens18 gro off gro off
TimeoutStartSec=0
RemainAfterExit=yes

[Install]
WantedBy=default.target
```
Activation et redémarrage du service
```
systemctl daemon-reload
systemctl start snort3-nic.service
systemctl enable --now snort3-nic.service
```


## III - CONFIGURATION DES REGLES
Création du répertoire de règles
```
mkdir /etc/snort/rules
```
Création du fichier de règles
```
nano /etc/snort/rules/local.rules
```
Contenu du fichier
```
# ----------------
# LOCAL RULES
# ----------------
# This file intentionally does nots come with signatures. Put your local additions here.

alert icmp any any -> any any (msg:"!!! ICMP Alert !!!";sid:1000001;rev:1;classtype:icmpevent;)
```
Configuration des règles dans le fichier de conf snort.lua
```
nano /etc/snort/snort.lua
```
Dans la section -- 5. configure detection
* Décommenter la ligne "enable_builtin_rules = true,"
* Ajouter la ligne "include = "/etc/snort/rules/local.rules","

```
---------------------------------------------------------------------------
-- 5. configure detection
---------------------------------------------------------------------------

references = default_references
classifications = default_classifications

ips =
{
    -- use this to enable decoder and inspector alerts
    enable_builtin_rules = true,

    -- use include for rules files; be sure to set your path
    -- note that rules files can include other rules files
    -- (see also related path vars at the top of snort_defaults.lua)

    include = "/etc/snort/rules/local.rules",

    variables = default_variables
}

-- use these to configure additional rule actions
-- react = { }
-- reject = { }

-- use this to enable payload injection utility
-- payload_injector = { }
```


## III - CONFIGURATION DES LOGS
Création du répertoire
```
mkdir /var/log/snort
chmod 660 /var/log/snort
```
Modification du fichier de configuration de Snort
```
nano /etc/snort/snort.lua
```
Modifier le fichier

>Se rendre tout en bas du fichier, dans la catégorie "7. configure outputs"
```
---------------------------------------------------------------------------
-- 7. configure outputs
---------------------------------------------------------------------------

alert_fast =
{
    file = true,
    limit = 100000
}

alert_full =
{
    file = true,
    limit = 100000
}
```


## IV - TEST
Lancer un ping à destination de votre machine

Lancer la capture Snort
```
snort -c /etc/snort/snort.lua -R /etc/snort/rules/local.rules -i ens18 -A alert_fast -l /var/log/snort
```

> Autres options d'alerte :
>- alert_fast
>- alert_full
