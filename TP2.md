# 1.1 Exercice : Connection ssh root (reprise fin tp-01)


### a) Creation d'une clef publique et privé dans notre local


- Générez une paire de clés publique/privée : id_rsa pour le fichier contenant la clé privée et id_rsa.pub pour le fichier contenant la clé publique.

Résultat :
```
PS C:\Users\maitr> ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\maitr/.ssh/id_rsa):#vide
Enter passphrase (empty for no passphrase):#vide
Enter same passphrase again:#vide
Your identification has been saved in C:\Users\maitr/.ssh/id_rsa
Your public key has been saved in C:\Users\maitr/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:G0qd7XxvNnPF0wyQ77G9X908PntUghfy9yivnNdpNk4 maitr@Maitriya
The key's randomart image is:
+---[RSA 3072]----+
|             .   |
|            + .  |
|             * . |
|       . o  . B o|
|      . S .  o @+|
|     . . =  . +o@|
|      . . o .o EX|
|           o oXB*|
|            +==BB|
+----[SHA256]-----+

```


***


### b) Renommé les dossiers en maclef et maclet.pub



- PS C:\Users\maitr> mv C:\Users\maitr\.ssh\id_rsa C:\Users\maitr\.ssh\maclef
- PS C:\Users\maitr> mv C:\Users\maitr\.ssh\id_rsa.pub C:\Users\maitr\.ssh\maclef.pub
- PS C:\Users\maitr> ls C:\Users\maitr\.ssh
- Répertoire : C:\Users\maitr\.ssh

Résultat :
```
Mode                 LastWriteTime         Length Name

-a----        06/12/2023     15:20            411 id_ed25519
-a----        06/12/2023     15:20            101 id_ed25519.pub
-a----        08/10/2024     20:00           1779 known_hosts
-a----        02/10/2024     14:15            927 known_hosts.old
-a----        09/10/2024     19:32           2602 maclef
-a----        09/10/2024     19:32            569 maclef.pub
```

***


 ### c) Copier sa clef publique avec la commande cat ~/.ssh/id_rsa.pub



Résultat :
```
PS C:\Users\maitr> cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC6VVJcvVEWVHEpkwmq1jptiAFDwWdjlc9Y4l85MQYy1jkLxNdRsislWEbBUCL1/6L09FaKDQGqzqpRsv4qfKMDHSHzowxcSZOGqieJq3nhye2gc3kQZV/Xm0xkgyCUmjzDjpgkQpTbePK6JO1xoC1SASN9RJHcSWc8sg+uP4jaLswWBhgyyYJcoMatnDVjaijhHwkLnQehTnOvsP54Lmev0bDFI9/UkAiTAZTUbokiiNmTmYnmSEoDiypq5e5YBynpFrVfkMzZ76saqn5P1jPaa4EOu97Yc1Hk39WV4DwZgXkzjrXluk5yAAxrFkC9BL3y+Q4DyTacakKQ79hNvyLTMKn7BM/Hk8pma+KloRqXMwxPFvEDOL6gA7xXjF2TPRahM29p9HFVs1dymv6m4l+JsCPca2rdjO0EGsfp6tvI8azt77WaNc8HxbE2PwmklufSf1rXmtMN57NK/ZD7MpLEYihfJVmEB8OzMJf3vVWfmWICZxtYD/m3fQZI+1L1T4M= maitr@Maitriya
```

 - Dans le cas reel ne pas mettre de passphrase pour proteger sa clef privé est une mauvaise pratique car si quelqu'un parvient à obtenir votre clé privée, il pourra acceder à la machine sans difficulté



***


## 1.3 Exercice : Authentification par clef / Connection serveur

### a) Vérifier que le ficher authorized_keys existe :

```
root@serveur1:~# cd /root/.ssh && ls -l
total 20
-rw-r--r-- 1 root root  568  9 oct.  16:25 authorized_keys 
-rw------- 1 root root 2602  9 oct.  15:13 id_rsa
-rw-r--r-- 1 root root  567  9 oct.  15:13 id_rsa.pub
-rw------- 1 root root  978  2 oct.  13:57 known_hosts
-rw-r--r-- 1 root root  142  2 oct.  13:57 known_hosts.old
```

***


### b) Accéder au dossier authorized_keys:

- nano authorized_keys et coller la clef publique


***


## 1.4 Exercice : Authentification par clef : depuis la machine hote

Connection à la machine : ssh -i C:\Users\maitr\.ssh\maclef -p 2222 root@127.0.0.1

Résultat : 

```
PS C:\Users\maitr> ssh -i C:\Users\maitr\.ssh\maclef -p 2222 root@127.0.0.1
Linux serveur1 6.1.0-25-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.106-3 (2024-08-26) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Oct  9 20:59:27 2024 from 10.0.2.2
```

***


## 1.5 Exercice : Securisez

- Aller dans la config : root@serveur1:~/.ssh# nano /etc/ssh/sshd_config
- Editer :

```
PasswordAuthentication no
PermitRootLogin without-password
```

- sauvegarder
- Restart : root@serveur1:~/.ssh# systemctl restart sshd
- Resultat :

```
PS C:\Users\maitr> ssh -p 2222 root@127.0.0.1
root@127.0.0.1: Permission denied (publickey).
```

- Il faut utiliser sa clef publique pour pouvoir se connecter avec : ssh -i .ssh\maclef -p 2222 root@127.0.0.1

- Attaques type brute-force SSH : cela consistent à tester de nombreux mots de passe sur un compte utilisateur pour trouver le bon (via des scripts notamment)

Protections :
- Clés SSH comme on vient de faire : connexion par clé publique en déasctivant les connexions par mot de passe
- Changer le port SSH par défaut (22) : modifier le port pour le SSH . Les bots cherchent généralement sur ce port
- Limiter les utilisateurs autorisés : sshd_config pour n'autoriser que certains utilisateurs ou groupes à se connecter
- Configurer un pare-feu (firewall) : Bloquer les connexions sur les ports non utilisés et limiter l'accès par adresse IP.


***


# 2) Processus

## 2.1 Exercice : Etude des processus UNIX

### a) Chercher sur le man la commande pour lister tous les processus

1- man ps

2- "/"

3- chercher "procesus"

4- Resultat :

"Pour voir tous les processus du système en utilisant la syntaxe BSD :
   ps ax
   ps axu"

- Resultat:

```
root@serveur1:~# ps ax
    PID TTY      STAT   TIME COMMAND
      1 ?        Ss     0:00 /sbin/init
      2 ?        S      0:00 [kthreadd]
      3 ?        I<     0:00 [rcu_gp]
      4 ?        I<     0:00 [rcu_par_gp]
      5 ?        I<     0:00 [slub_flushwq]
      6 ?        I<     0:00 [netns]
      8 ?        I<     0:00 [kworker/0:0H-kblockd]
     10 ?        I<     0:00 [mm_percpu_wq]
     11 ?        I      0:00 [rcu_tasks_kthread]
     12 ?        I      0:00 [rcu_tasks_rude_kthread]
     13 ?        I      0:00 [rcu_tasks_trace_kthread]
     14 ?        S      0:00 [ksoftirqd/0]
     15 ?        I      0:00 [rcu_preempt]
     16 ?        S      0:00 [migration/0]
     18 ?        S      0:00 [cpuhp/0]
     20 ?        S      0:00 [kdevtmpfs]
     21 ?        I<     0:00 [inet_frag_wq]
     22 ?        S      0:00 [kauditd]
     23 ?        S      0:00 [khungtaskd]
     25 ?        S      0:00 [oom_reaper]
     26 ?        I<     0:00 [writeback]
     28 ?        S      0:00 [kcompactd0]
     29 ?        SN     0:00 [ksmd]
     30 ?        SN     0:00 [khugepaged]
     31 ?        I<     0:00 [kintegrityd]
     32 ?        I<     0:00 [kblockd]
     33 ?        I<     0:00 [blkcg_punt_bio]
     34 ?        I<     0:00 [tpm_dev_wq]
     35 ?        I<     0:00 [edac-poller]
     36 ?        I<     0:00 [devfreq_wq]
     38 ?        S      0:00 [kswapd0]
     44 ?        I<     0:00 [kthrotld]
     46 ?        I<     0:00 [acpi_thermal_pm]
     48 ?        I<     0:00 [mld]
     49 ?        I<     0:00 [ipv6_addrconf]
     54 ?        I<     0:00 [kstrp]
     59 ?        I<     0:00 [zswap-shrink]
     60 ?        I<     0:00 [kworker/u3:0]
    125 ?        I<     0:00 [ata_sff]
    126 ?        S      0:00 [scsi_eh_0]
    127 ?        I<     0:00 [scsi_tmf_0]
    128 ?        S      0:00 [scsi_eh_1]
    129 ?        S      0:00 [scsi_eh_2]
    130 ?        I<     0:00 [scsi_tmf_2]
    131 ?        I<     0:00 [scsi_tmf_1]
    138 ?        I<     0:00 [kworker/0:2H-kblockd]
    170 ?        S      0:00 [jbd2/sda1-8]
    171 ?        I<     0:00 [ext4-rsv-conver]
    212 ?        Ss     0:00 /lib/systemd/systemd-journald
    233 ?        Ss     0:00 /lib/systemd/systemd-udevd
    263 ?        I<     0:00 [cryptd]
    299 ?        S      0:00 [irq/18-vmwgfx]
    368 ?        S      0:00 [jbd2/sda2-8]
    373 ?        I<     0:00 [ext4-rsv-conver]
    400 ?        S      0:00 [jbd2/sda3-8]
    401 ?        I<     0:00 [ext4-rsv-conver]
    421 ?        Ssl    0:00 /lib/systemd/systemd-timesyncd
    437 ?        Ss     0:00 /usr/sbin/cron -f
    438 ?        Ss     0:00 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation
    441 ?        Ss     0:00 /lib/systemd/systemd-logind
    455 ?        Ss     0:00 dhclient -4 -v -i -pf /run/dhclient.enp0s3.pid -lf /var/lib/dhcp/dhclient.enp0s3.leases -I
    458 ?        Ss     0:00 /sbin/wpa_supplicant -u -s -O DIR=/run/wpa_supplicant GROUP=netdev
    502 tty1     Ss     0:00 /bin/login -p --
    511 ?        Ss     0:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
    551 ?        Ss     0:00 /lib/systemd/systemd --user
    552 ?        S      0:00 (sd-pam)
    558 tty1     S+     0:00 -bash
    563 ?        Ss     0:00 sshd: root@pts/0
    569 pts/0    Ss     0:00 -bash
    591 ?        I      0:00 [kworker/u2:0-flush-8:0]
    592 ?        I      0:00 [kworker/0:0-ata_sff]
    657 ?        I      0:00 [kworker/0:1-cgroup_destroy]
    681 ?        I      0:00 [kworker/u2:2-events_unbound]
    682 ?        I      0:00 [kworker/0:2-ata_sff]
    684 ?        I      0:00 [kworker/0:3-events]
    686 pts/0    R+     0:00 ps ax
```

  ***


### b) TIME

- L'information TIME donne le temps total utilisé par le processus.

  ***


### c) Processus + utilisé le processeur sur votre machine

- utilisez la commande "top"
- Resultat :
```
  
  PID UTIL.     PR  NI    VIRT    RES    SHR S  %CPU  %MEM    TEMPS+ COM.
    563 root      20   0   17996  10920   9108 S   0,3   0,5   0:00.74 sshd
      1 root      20   0  102116  12140   9172 S   0,0   0,6   0:00.67 systemd
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd

    PID UTIL.     PR  NI    VIRT    RES    SHR S  %CPU  %MEM    TEMPS+ COM.
    688 root      20   0       0      0      0 I   0,3   0,0   0:00.96 kworker/0:0-events
  ```

- C'est sshd et kworker qui ont le plus utilisé le processeur sur ma machine

***


### d) Premier processus lancé après le démarrage du système : avec -p 1

Résultat :

```
root@serveur1:~# ps -p 1
    PID TTY          TIME CMD
      1 ?        00:00:00 systemd
      
 ```
     
- Le premier processus lancé après le demarrage est celui avec le PID 1, systemd.

***


### e) A quelle heure votre machine a-t-elle démarrée ? 

#### e1>
Résultat : 

```
root@serveur1:~# who -b
démarrage système 2024-10-10 16:03
```

#### e2> 

```
root@serveur1:~# uptime
 16:59:27 up 55 min,  2 users,  load average: 0,00, 0,00, 0,0
```

 - Elle a démarré à 16H03

 ***


### f) Nombre de processus crées : ps -aux | wc -l  

Résultat:

```
root@serveur1:~# ps -aux | wc -l
77
```

- Il y'a 77 processus crées.


***


## 2.2 -Sous UNIX, chaque processus (except´e le premier) est cr´e´e par un autre processus, son processus père. Le processus père d’un processus est identifi´e par son PPID (Parent PID)

###  Trouver une option de la commande ps permettant d’afficher le PPID d’un processus.

```
root@serveur1:~#  ps -o pid,ppid,comm
    PID    PPID COMMAND
    566     550 bash
   1672     566 ps
```

- 550 et 566 sont les PPID du processus.

###  Donner la liste ordonnée de tous les processus ancetres de la commande ps en cours d’exécution.

```
root@serveur1:~# ps -o ppid
   PPID
    550
    566
```

  

***
    
# 3 - Reprendre la question précédente avec la commande pstree.
Vous devrez sans doute installer ce package : voir apt update ; apt search ; apt install.

- Lorsque j'ai fait apt search :

```
root@serveur1:~# apt search pstree
En train de trier... Fait
Recherche en texte intégral... Fait
psmisc/stable 23.6-1 amd64
utilitaires qui utilisent le système de fichiers proc
```

- Il faut donc installer psmisc :

```
root@serveur1:~# apt install psmisc
```

- faire la rechercher en utilisant le PID du processus:

```
root@serveur1:~# pstree -ps 566
systemd(1)───sshd(508)───sshd(550)───bash(566)───pstree(1730)
```


***


# 4 - Essayez la commande top, qui affiche les mêmes informations que ps mais en raffraichissant périodiquement l’affichage.

### a) Afficher dans top la liste de processus triee par occupation mémoire (“resident memory”) décroissant


- pendant que "top" est lancé j'ai appuyé sur shift + "m"
- (on peut aussi utiliser htop en commande après l'avoir installé)
  
- Resultat :

```
 PID UTIL.     PR  NI    VIRT    RES    SHR S  %CPU  %MEM    TEMPS+ COM.
      1 root      20   0  167636  12052   9108 S   0,0   0,6   0:01.35 systemd
    550 root      20   0   17996  11064   9248 S   0,0   0,5   0:03.58 sshd
    211 root      20   0   32968  11052   7940 S   0,0   0,5   0:00.28 systemd-journal
    553 root      20   0   18976  10632   8900 S   0,0   0,5   0:00.13 systemd
    508 root      20   0   15432   8904   7664 S   0,0   0,4   0:00.01 sshd
    453 root      20   0   25200   7852   6820 S   0,0   0,4   0:00.11 systemd-logind
    422 systemd+  20   0   90080   6624   5748 S   0,0   0,3   0:00.30 systemd-timesyn
    234 root      20   0   26184   5880   4584 S   0,0   0,3   0:00.18 systemd-udevd
    468 root      20   0   16532   5800   4932 S   0,0   0,3   0:00.20 wpa_supplicant
   1748 root      20   0   11728   5228   3304 R   0,0   0,3   0:00.09 top                                                
    566 root      20   0    8184   4796   3472 S   0,0   0,2   0:00.10 bash
    451 root      20   0    5868   3600   2768 S   0,0   0,2   0:00.02 dhclient
    556 root      20   0  168696   3076      0 S   0,0   0,2   0:00.00 (sd-pam)
    446 root      20   0    6612   2692   2444 S   0,0   0,1   0:00.03 cron
    497 root      20   0    5876   1008    924 S   0,0   0,1   0:00.01 agetty
```

***


### b)Quel est le processus le plus gourmand sur votre machine ? A quoi correspond-il ? (rappel : vous pouvez utiliser man truc pour d´ecouvrir ce que fait truc...).

- C'est  le systemd . C'est un système d'initialisation et un gestionnaire de services pour Linux, chargé de démarrer les processus et de gérer les dépendances des services. Il assure également la journalisation des événements système.


***

### c) Trouvez les commandes interactives permettant de : passer l’affichage en couleur, mettre en avant le colonne de trie, changer la colonne de trie.


D'après h dans top:

- z : Ca permet d'activé ou désactivé l'affichage en couleur
- les flèches <> : Déplace la colonne de tri vers la gauche/droite
- f : Changer la colonne de tri

***
 
 ### d) Essayez la commande htop. Expliquez les avantages et/ou inconvénients par rapport a top

 
-  Avantages de htop : interface conviviale et facile à utiliser.
-  Inconvénients de htop : il n'est pas installé par défaut , il faut l'installer avec apt install htop (donc prend de la place aussi). Aussi on ne peut pas faire de copier/coller

***

# 3 Exercice 2 : Arrêt d’un processus

#### - Créer deux fichiers : 

```
root@serveur1:# nano date-toto.sh  
root@serveur1:~# nano date.sh
```

#### - Coller le script demandé et enregistré

#### - Lancer le 1er scripts,  le mettre en arriere plan (CTRL-Z).

```
root@serveur1:~# ./date.sh

  -bash: ./date.sh: Permission non accordée
root@serveur1:~# chmod +x date.sh date-toto.sh (ca permet de rendre les deux scripts exécutable)
```

Résultat : 

```
root@serveur1:~# ./date.sh
date 21:43:40
date 21:43:41
date 21:43:42
date 21:43:43
date 21:43:44
date 21:43:45
[1]+  Stoppé                 ./date.sh

```

#### - Lancer le 2eme scripts. Le mettre en arrière plan (CTRL-Z).

Résultat :

```
root@serveur1:~# ./date-toto.sh
toto 16:45:37
toto 16:45:38
toto 16:45:39
toto 16:45:40
[2]+  Stoppé                 ./date-toto.sh

```

#### - Utilisation de la commande "job" permet de voir l'état des scripts

Résultat :

```
root@serveur1:~# jobs
[1]-  Stoppé                 ./date.sh
[2]+  Stoppé                 ./date-toto.sh
```

#### - La commande fg permet de relancer les scripts et CTRL+C pour les arreter

Résultat : 

```

root@serveur1:~# fg
./date-toto.sh
toto 16:51:00
toto 16:51:01
toto 16:51:02
```


#### - Même question en utilisant les commandes ps et kill (avec un PID)

On lance les scripts. Ensuite on fait une commande pour trouver leur PID : ps aux | grep date puis on fait un kill en utilisant leur PID

Résultat :

```
root@serveur1:# ps aux | grep date

root         988  0.0  0.0   2576   896 pts/0    T    21:55   0:00 /bin/sh ./date-toto.sh
root         998  0.0  0.0   2576   896 pts/0    T    21:59   0:00 /bin/sh ./date.sh

toto 17:33:37
date 22:33:37
toto 17:33:38
date 22:33:38


root@serveur1:~# kill 988 998
```
  
### Expliquer les scripts à l’aide du man.


- while true; do ... done : C'est une boucle infinie qui exécute les commandes à l'intérieur de do et done indéfiniment

- sleep 1 : Cette commande suspend l'exécution du script pendant 1 seconde

- echo -n 'date ' : Cette commande affiche "date " 

- date +%T : Cette commande affiche l'heure actuelle

- echo -n 'toto ' : Affiche "toto"

- date --date '5 hour ago' +%T : Cette commande affiche l'heure actuelle moins 5 heures


- En bref , le premier script affiche l'heure actuelle, tandis que le deuxième affiche l'heure qu'il était 5 heures auparavant


***

# 4 Exercice 3 : les tubes

## Quelle est la diff´erence entre tee et cat ?

- cat : Affiche le contenu d'un fichier ou combine plusieurs fichiers.

- tee : Affiche du texte et l'enregistre en même temps dans un fichier.

## Que font les commandes suivantes :


### $ ls | cat


- La commande $ ls | cat affiche la liste des fichiers et répertoires du répertoire courant, comme si on avait juste tapé ls.

Résultat:

```
root@serveur1:~# ls | cat
date.sh
date-toto.sh
sssef
```

### ls -l | cat > liste

- La commande ls -l | cat > liste enregistre la liste détaillée des fichiers et répertoires dans un fichier nommé "liste" sans l'afficher à l'écran


### ls -l | tee liste

- La commande ls -l | tee liste affiche la liste détaillée des fichiers et répertoires tout en l'enregistrant dans un fichier nommé "liste"

Résultat : 

```
root@serveur1:~#  ls -l | tee liste
total 28
-rwxr-xr-x 1 root root    80 14 oct.  21:19 date.sh
-rwxr-xr-x 1 root root    87 14 oct.  21:16 date-toto.sh
-rw-r--r-- 1 root root   218 15 oct.  20:04 liste
-rw-r--r-- 1 root root 13067  9 oct.  14:45 sssef
```

### ls -l | tee liste | wc -l

- La commande ls -l | tee liste | wc -l affiche la liste détaillée des fichiers, l'enregistre dans le fichier "liste" et compte le nombre de lignes affichées.

Résultat :

```
root@serveur1:~# ls -l | tee liste | wc -l
5
```

***

# 5 Journal système rsyslog

##  Le service rsyslog est-il lancé sur votre système ? Quel est le PID du démon ?

- Vu que je ne l'avais pas, je l'ai installé avec la commande apt-get install rsyslog, puis j'ai utilisé la commande systemctl status rsyslog pour voir si elle était bien opérationnelle.
- On peut voir sur "Main PID" que PID du démon rsyslogd est 728 .

Résultat :

```
root@serveur1:~# systemctl status rsyslog
● rsyslog.service - System Logging Service
     Loaded: loaded (/lib/systemd/system/rsyslog.service; enabled; preset: enabled)
     Active: active (running) since Tue 2024-10-15 20:22:10 CEST; 45s ago
TriggeredBy: ● syslog.socket
       Docs: man:rsyslogd(8)
             man:rsyslog.conf(5)
             https://www.rsyslog.com/doc/
   Main PID: 728 (rsyslogd)    
      Tasks: 4 (limit: 2315)
     Memory: 1.3M
        CPU: 15ms
     CGroup: /system.slice/rsyslog.service
             └─728 /usr/sbin/rsyslogd -n -iNONE

oct. 15 20:22:10 serveur1 systemd[1]: Starting rsyslog.service - System Logging Service...
oct. 15 20:22:10 serveur1 systemd[1]: Started rsyslog.service - System Logging Service.
oct. 15 20:22:10 serveur1 rsyslogd[728]: imuxsock: Acquired UNIX socket '/run/systemd/journal/syslog' (fd 3) from syste>
oct. 15 20:22:10 serveur1 rsyslogd[728]: [origin software="rsyslogd" swVersion="8.2302.0" x-pid="728" x-info="https://w>

```

## Le principal fichier de configuration de rsyslog est /etc/rsyslog.conf. Dans quel fichier rsyslog ´ecrit-il les messages issus des services standards ? Et la plupart des autres messages ? Vérifier le contenu de ces fichiers

- rsyslog écrit les messages issus des services standards principalement dans le fichier /var/log/syslog
- les messages  peuvent être dirigés vers d'autres fichiers  tels que /var/log/messages


##  A quoi sert le service cron ?

- Le service cron sert à exécuter automatiquement des tâches à des horaires réguliers. Il permet d'automatiser des commandes et des scripts.

## Que fait la commande tail -f ? 

- La commande tail -f affiche en temps réel les nouvelles lignes ajoutées à un fichier

## A l’aide de cette commande, placer en bas de votre ´ecran un fenˆetre qui permette de visualiser en “temps r´eel” le contenu du fichier /var/log/messages.

- Cela n'a pas fonctionné, j'ai donc plutôt utilisé la commande tail -f /var/log/syslog.
  
 Résultat :

 ```
 root@serveur1:~# tail -f /var/log/syslog
2024-10-15T20:22:10.717566+02:00 serveur1 kernel: [   10.280610] e1000: enp0s3 NIC Link is Up 1000 Mbps Full Duplex, Flow Control: RX
2024-10-15T20:22:10.717575+02:00 serveur1 kernel: [   10.323868] IPv6: ADDRCONF(NETDEV_CHANGE): enp0s3: link becomes ready
2024-10-15T20:22:10.716162+02:00 serveur1 systemd[1]: Listening on syslog.socket - Syslog Socket.
2024-10-15T20:22:10.718724+02:00 serveur1 systemd[1]: Starting rsyslog.service - System Logging Service...
2024-10-15T20:22:10.714096+02:00 serveur1 systemd[1]: Started rsyslog.service - System Logging Service.
2024-10-15T20:22:10.715486+02:00 serveur1 rsyslogd: imuxsock: Acquired UNIX socket '/run/systemd/journal/syslog' (fd 3) from systemd.  [v8.2302.0]
2024-10-15T20:22:10.715687+02:00 serveur1 rsyslogd: [origin software="rsyslogd" swVersion="8.2302.0" x-pid="728" x-info="https://www.rsyslog.com"] start
2024-10-15T20:30:01.195847+02:00 serveur1 CRON[745]: (root) CMD ([ -x /etc/init.d/anacron ] && if [ ! -d /run/systemd/system ]; then /usr/sbin/invoke-rc.d anacron start >/dev/null; fi)
2024-10-15T20:34:59.619392+02:00 serveur1 systemd[1]: anacron.service - Run anacron jobs was skipped because of an unmet condition check (ConditionACPower=true).
2024-10-15T20:41:39.619741+02:00 serveur1 systemd[1]: apt-daily-upgrade.service - Daily apt upgrade and clean activities was skipped because of an unmet condition check (ConditionACPower=true).
```

## Que voyez-vous si vous redémarrez le service cron depuis un autre shell ?

- utilisation de la commande systemctl restart cron. On peut voir les messages qui indiquent que le service cron a été arrêté puis redémarré

Résultat : 

```
root@serveur1:~# tail -f /var/log/syslog
2024-10-15T22:17:50.994240+02:00 serveur1 CRON[827]: (root) CMD (cd / && run-parts --report /etc/cron.hourly)
2024-10-15T22:29:03.021138+02:00 serveur1 systemd[1]: Started session-9.scope - Session 9 of User root.
2024-10-15T22:29:25.759831+02:00 serveur1 systemd[1]: Stopping cron.service - Regular background program processing daemon...
2024-10-15T22:29:25.761365+02:00 serveur1 systemd[1]: cron.service: Deactivated successfully.
2024-10-15T22:29:25.763155+02:00 serveur1 systemd[1]: Stopped cron.service - Regular background program processing daemon.
2024-10-15T22:29:25.789094+02:00 serveur1 systemd[1]: Started cron.service - Regular background program processing daemon.
2024-10-15T22:29:25.805915+02:00 serveur1 cron[846]: (CRON) INFO (pidfile fd = 3)
2024-10-15T22:29:25.807086+02:00 serveur1 cron[846]: (CRON) INFO (Skipping @reboot jobs -- not system startup)

```

##  Expliquer à quoi sert le fichier /etc/logrotate.conf.

- Le fichier /etc/logrotate.conf sert à gérer la rotation des fichiers de log sur le système. Il indique quand et comment ces fichiers doivent être renommés, archivés ou supprimés pour ne pas prendre trop d’espace disque.



## Examiner la sortie de la commande dmesg. Quel modele de processeur linux détecte-il sur votre machine ? Quels modéles de cartes réseaux détecte-il


- On utilise la commande dmesg | grep -i 'cpu\|network\|eth\|wlan' et on peut voir que le processeur détecter est AMD Ryzen 7 5825U et la carte réseau est Intel PRO/1000 Network Connection

  
Résultat : 

```
root@serveur1:~# dmesg | grep -i 'cpu\|network\|eth\|wlan'
[    0.003504] CPU MTRRs all blank - virtualized system.
[    0.010977] ACPI: SSDT 0x000000007FFF02A0 00036C (v01 VBOX   VBOXCPUT 00000002 INTL 20100528)
[    0.018510] smpboot: Allowing 1 CPUs, 0 hotplug CPUs
[    0.024885] setup_percpu: NR_CPUS:8192 nr_cpumask_bits:1 nr_cpu_ids:1 nr_node_ids:1
[    0.037329] percpu: Embedded 61 pages/cpu s212992 r8192 d28672 u2097152
[    0.037338] pcpu-alloc: s212992 r8192 d28672 u2097152 alloc=1*2097152
[    0.037342] pcpu-alloc: [0] 0
[    0.037523] kvm-guest: PV spinlocks disabled, single CPU
[    0.053530] SLUB: HWalign=64, Order=0-3, MinObjects=0, CPUs=1, Nodes=1
[    0.066648] rcu:     RCU restricting CPUs from NR_CPUS=8192 to nr_cpu_ids=1.
[    0.066654] rcu: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=1
[    0.169262] CPU0: Hyper-Threading is disabled
[    0.300769] smpboot: CPU0: AMD Ryzen 7 5825U with Radeon Graphics (family: 0x19, model: 0x50, stepping: 0x0)
[    0.302111] smp: Bringing up secondary CPUs ...
[    0.302114] smp: Brought up 1 node, 1 CPU
[    0.308518] cpuidle: using governor ladder
[    0.308528] cpuidle: using governor menu
[    1.670070] ledtrig-cpu: registered to indicate activity on CPUs
[    2.423323] e1000: Intel(R) PRO/1000 Network Driver
[    3.754408] e1000 0000:00:03.0 eth0: (PCI:33MHz:32-bit) 08:00:27:c4:be:36
[    3.754424] e1000 0000:00:03.0 eth0: Intel(R) PRO/1000 Network Connection
[    3.767236] e1000 0000:00:03.0 enp0s3: renamed from eth0
[    7.175804] cryptd: max_cpu_qlen set to 1000
[    9.821599] audit: type=1400 audit(1729015022.640:5): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-client.action" pid=419 comm="apparmor_parser"
[    9.821609] audit: type=1400 audit(1729015022.640:6): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-helper" pid=419 comm="apparmor_parser"
```












