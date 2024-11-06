# 1 - Snort 3 - Installation

> Cette procédure a été testée et validée sur Debian 12.2.0-14

## INSTALLATION DES DÉPENDANCES
```
apt update && apt upgrade
apt install build-essential libpcap-dev libpcre3-dev libdumbnet-dev bison flex git cmake libdaq2 libdaq-dev  libhwloc-dev luajit libluajit-5.1-dev libssl-dev zlib1g-dev liblzma-dev gawk libcmocka-dev libsqlite3-dev uuid-dev libnetfilter-queue-dev libmnl-dev autotools-dev libunwind-dev libfl-dev && apt install hwloc
```


## INSTALLATION DE LIBDAQ
> libdaq (Data Acquisition Library) est une bibliothèque essentielle pour Snort, elle gère l'acquisition des paquets réseau et agit comme interface entre Snort et les méthodes utilisées pour capturer le trafic réseau.
```
cd
git clone https://github.com/snort3/libdaq.git
cd libdaq
./bootstrap
./configure
make install
ldconfig
```

## INSTALLATION DE SNORT 3
```
cd
git clone https://github.com/snort3/snort3.git
cd snort3
export my_path=/
./configure_cmake.sh --prefix=$my_path
cd build
make -j $(nproc)
make install
```


## TEST DE FONCTIONNEMENT
Afficher la version de Snort (confirme le fonctionnement)
```
snort -V
```
Tester le fichier de configuration
```
snort -c /etc/snort/snort.lua -T
```
