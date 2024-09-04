# Snort 3 - Initial configuration

## I - CONFIGURATION DE LA CARTE RESEAU (A CHAUD)

ip link set dev ens18 promisc on


En mode normal, une interface réseau ne traite que les paquets qui sont adressés spécifiquement à elle, plus les paquets de diffusion (broadcast) et de multidiffusion (multicast) auxquels elle est abonnée
En mode promiscuous, l'interface réseau traite tous les paquets qu'elle voit passer sur le réseau, indépendamment du fait qu'ils lui soient destinés ou non



Language...
1
ip ink show ens18
apt install ethtool ethtool -k ens18 |grep receive-offload

ethtool -K ens18 gro off lro off

ethtool -k ens18 | grep receive-offload



La commande ethtool -k ens18 | grep receive-offload permet de vérifier les paramètres d'optimisation de la réception des paquets sur l'interface ens18
GRO et LRO combinent plusieurs paquets TCP reçus en un seul paquet plus grand pour réduire le nombre de paquets à traiter


La commande ethtool -K ens18 gro off lro off est utilisée pour désactiver deux types d'optimisations matérielles de réception sur l'interface réseau ens18
Désactiver ces optimisations est souvent nécessaire pour des outils d'analyse de paquets comme Snort ou Wireshark, afin de garantir que tous les paquets sont capturés et analysés individuellement


## II - CONFIGURATION DU PARAMETRAGE AUTO DE LA NIC EN TANT QUE SERVICE

Création du fichier de service




Language...
1
nano /etc/systemd/system/snort3-nic.service


Contenu du fichier




Language...
1
[Unit] Description=Set Snort 3 NIC in promiscuous mode and Disable GRO, LRO on boot
After=network.target

[Service] Type=oneshot ExecStart=/usr/sbin/ip link set dev ens18 promisc on ExecStart=/usr/sbin/ethtool -K ens18 gro off lro off TimeoutStartSec=0 RemainAfterExit=yes

[Install] WantedBy=default.target



Activation et redémarrage du service


Language...
1
systemctl daemon-reload
systemctl start snort3-nic.service systemctl enable --now snort3-nic.service





III - CREATION D'UN FICHIER DE REGLES PERSONNALISEES

Création du fichier de règles


Language...
1
nano /etc/snort/rules/local.rules


Contenu du fichier




Language...
1
alert icmp any any -> any any (msg:"!!! ICMP Alert !!!";sid:1000001;rev:1;classtype:icmpevent;)




III - CONFIGURATION DES LOGS

Création du répertoire


Language...
1
mkdir /var/log/snort
chmod 777 /var/log/snort



Modification du fichier de configuration de Snort


Language...
1
nano /etc/snort/snort.lua


Modifier le fichier


Language...
1
---------------------------------------------------------------------------
-- 7. configure outputs

alert_full = { file = true, limit = 50 } alert_full= { file = true, limit = 50 }



## IV - TEST



Lancer un ping à destination de votre machine




Language...
1
snort -c /etc/snort/snort.lua -R /etc/snort/rules/local.rules -i ens18 -A alert_fast -l /var/log/snort


Autres options d'alerte :

alert_fast
alert_full
