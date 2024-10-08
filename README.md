# System-Unix

1 Installation Machine virtuelle

Des difficultés de compréhension, mais j'ai fini par réussir . 

2 Post-Installation

2.1 configuration ssh et connection

Pour pouvoir faire une connections ssh à la machien virtuel il faut :
- executer Apt install ssh
-	Commande => nano /etc/ssh/sshd_config
-	Changez deux lignes de codes :  PermitRootLogin yes et PasswordAuthentication yes
-	enregistrer
-	restart ssh avec commande => systemctl restart ssh
-	vérifier la ssh => systemctl status ssh
-	faire une redirection de ports (port hôte 2222 et port hote 22)
-	Se connecter sur la machine hôte avec la commande ssh root@localhost
-	Connecté !
-	(Chez moi cela n'a pas fonctionné, j'ai dû utilisé la commande ip a sur la machine virtuel , récupéré l'ip et faire la commande ssh -p 2222 root@127.0.0.1 sur mon terminal)


2.3 Nombre de paquets

Commande => dpkg -l | wc -l

- dpkg -l  => ceci liste tous les paquets installés.
wc -l  => compte le nombre de lignes dans cette liste
donc dpkg -l | wc -l => correspond au nombre total de paquets installé

Resultat : 
root@serveur1:~# dpkg -l | wc -l
353

2.4 Space Usage

COmmande => df -h

Resultat : 
root@serveur1:~# df -h
Sys. de fichiers Taille Utilisé Dispo Uti% Monté sur
udev               965M       0  965M   0% /dev
tmpfs              197M    572K  197M   1% /run
/dev/sda1          9,1G   1007M  7,7G  12% /
tmpfs              984M       0  984M   0% /dev/shm
tmpfs              5,0M       0  5,0M   0% /run/lock
/dev/sda2          3,6G     40K  3,4G   1% /tmp
/dev/sda3          921M    233M  625M  28% /var
tmpfs              197M       0  197M   0% /run/user/0

Explication:
- df : "disk free", cette commande affiche des informations sur la taille, l'utilisation et la disponibilité des systèmes de fichiers.
- -h : signifie "human-readable", ce qui convertit les informations en un format facile à lire ( des unités comme Mo, Go, To au lieu de blocs).


2.5 a indiquer dans le rendu et expliquer les commandes et le resultat obtenu

a)echo $LANG

Resultat : 
root@serveur1:~# echo $LANG
fr_FR.UTF-8

Explication : 
Cette commande affiche la locale du système, c'est-à-dire la langue et les paramètres régionaux en cours d'utilisation. Ici, cela signifie que le système utilise le français avec encodage UTF-8.

b) env

Resultat : 
root@serveur1:~# env
SHELL=/bin/bash
PWD=/root
LOGNAME=root
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/root
LANG=fr_FR.UTF-8
SSH_CONNECTION=10.0.2.2 62594 10.0.2.15 22
XDG_SESSION_CLASS=user
TERM=xterm-256color
USER=root
SHLVL=1
XDG_SESSION_ID=3
XDG_RUNTIME_DIR=/run/user/0
SSH_CLIENT=10.0.2.2 62594 22
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/0/bus
SSH_TTY=/dev/pts/0
_=/usr/bin/env

Explication : Elle retourne toutes les variarables d'environnement définies pour la session. Ici on voit la langue utilisé, la connexion SSH , l'ID utilisateur, le shell utilisé (bash) ect

c) nom machine : hostname

Résultat : 
root@serveur1:~# hostname
serveur1
Commande : hostname

Explication : c'est le nom d'hôte utilisé par le système pour identifier l'ordinateur sur un réseau. Ici c'est serveur1

d) hostname -d

Resultat:
root@serveur1:~# hostname -d
ufr-info-p6.jussieu.fr

Explication : 
Cette commande retourne le nom de domaine de la machine (il y'a aussi -f pour hostname+domain)

f) verification emplacement depots : cat /etc/apt/sources.list | grep -v -E ’^#|^$’

Résultat : 
root@serveur1:~# cat /etc/apt/sources.list | grep -v -E '^#|^$'
deb http://deb.debian.org/debian/ bookworm main
deb http://security.debian.org/debian-security bookworm-security main
deb http://deb.debian.org/debian/ bookworm-updates main

Explication :
- cat /etc/apt/sources.list : Affiche le contenu du fichier sources.list.
- grep -v -E '^#|^$' :
- grep => rechercher du texte dans des fichiers
- -v : affiche les lignes qui ne correspondent pas au motif
- -E : pouvoir utiliser des expressions régulières
- '^#|^$' : Exclut les lignes commençant par # (commentaires) 
- cat /etc/apt/sources.list | grep -v -E '^#|^$' :  permet d'afficher uniquement les lignes non commentées du fichier /etc/apt/sources.list, qui contient la liste des dépôts de paquets.

g) passwd/shadow : cat /etc/shadow | grep -vE ’:\*:|:!\*:’

Résultat : 
root@serveur1:~# cat /etc/shadow | grep -vE ':\*:|:!\*:'
root:$y$j9T$VbIjgVwGod661jObZXyTs.$lR4TUkEhnXzxOpddANzafGxOKlpmkLuEw4Vko4oZNL6:19998:0:99999:7:::
messagebus:!:19998::::::
avahi-autoipd:!:19998::::::
sshd:!:19998::::::

Explication :
- cat /etc/shadow : contient les mots de passes
- grep -vE ':\*:|:!\*:' : exclu des lignes comme :*: ou :!*:, ce qui signifie que les utilisateurs avec des comptes verrouillés ou désactivés ne seront pas affichés.
- cat /etc/shadow | grep -vE ':\*:|:!\*:' : Cette commande affiche les comptes d'utilisateurs avec des mots de passe actifs stocké sous forme cryptée et excluent ceux qui sont désactivés ou verrouillés


h)  compte utilisateurs : cat /etc/passwd | grep -vE ’nologin|sync’

Résultat :
root@serveur1:~# cat /etc/passwd | grep -vE 'nologin|sync'
root:x:0:0:root:/root:/bin/bash

Explication :
- root:x:0:0:root:/root:/bin/bash : x=>mot de passe stocké dans shadow,0 => IUD
- cat /etc/passwd | grep -vE 'nologin|sync' : affiche les informations du compte "root", en omettant les comptes d'utilisateurs qui ne peuvent pas se connecter, comme ceux ayant nologin ou sync
  
i) fdisk -l 

Résultat:
root@serveur1:~# fdisk -l
Disque /dev/sda : 20 GiB, 21474836480 octets, 41943040 secteurs
Modèle de disque : VBOX HARDDISK
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d'E/S (minimale / optimale) : 512 octets / 512 octets
Type d'étiquette de disque : gpt
Identifiant de disque : F2A5E24C-47D2-4E44-A10C-1F33AEA555D3

Périphérique    Début      Fin Secteurs Taille Type
/dev/sda1        2048 19531775 19529728   9,3G Système de fichiers Linux
/dev/sda2    19531776 27344895  7813120   3,7G Système de fichiers Linux
/dev/sda3    27344896 29298687  1953792   954M Système de fichiers Linux
/dev/sda4    29298688 41940991 12642304     6G Partition d'échange Linux

Explication:
- -l : liste les informations
- fdisk -l  : Cette commande retourne les partitions du disque dur du système.

j) fdisk -x

Résultat:
root@serveur1:~# fdisk -x
Disque /dev/sda : 20 GiB, 21474836480 octets, 41943040 secteurs
Modèle de disque : VBOX HARDDISK
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d'E/S (minimale / optimale) : 512 octets / 512 octets
Type d'étiquette de disque : gpt
Identifiant de disque: F2A5E24C-47D2-4E44-A10C-1F33AEA555D3
Premier LBA utilisable: 34
Dernier LBA utilisable: 41943006
LBA alternatif: 41943039
LBA de départ des entrées de partition: 2
Entrées de partitions allouées: 128
LBA de fin des entrées de partition: 33

Périphérique    Début      Fin Secteurs Type-UUID                            UUID                                 Nom Attr.
/dev/sda1        2048 19531775 19529728 0FC63DAF-8483-4772-8E79-3D69D8477DE4 CBB3EB5D-9F1B-45B4-BEBF-DAD3A2061C3F la racine

/dev/sda2    19531776 27344895  7813120 0FC63DAF-8483-4772-8E79-3D69D8477DE4 0302E099-615E-448E-ABCD-9F271C339302 espace tempo

/dev/sda3    27344896 29298687  1953792 0FC63DAF-8483-4772-8E79-3D69D8477DE4 6D9CC73D-BFA2-4007-A108-358C76C799A4 les logs

/dev/sda4    29298688 41940991 12642304 0657FD6D-A4AB-43C4-84E5-0933C84B4F4F 51BFF560-C268-45B9-A7C4-B2642DF999E7 ma swap

Explication:
- -x :  afficher des informations plus détaillées que -l pour les disque
- Cette commande retourne des informations détaillées sur les partitions du disque principal, y compris le type d'étiquette, ainsi qu'un tableau indiquant les périphériques, les secteurs, des identifiants uniques pour chaque partition et leur type

k) df -h

Résultat:
root@serveur1:~# df -h
Sys. de fichiers Taille Utilisé Dispo Uti% Monté sur
udev               965M       0  965M   0% /dev
tmpfs              197M    572K  197M   1% /run
/dev/sda1          9,1G   1007M  7,7G  12% /
tmpfs              984M       0  984M   0% /dev/shm
tmpfs              5,0M       0  5,0M   0% /run/lock
/dev/sda2          3,6G     40K  3,4G   1% /tmp
/dev/sda3          921M    233M  625M  28% /var
tmpfs              197M       0  197M   0% /run/user/0

Explication:






