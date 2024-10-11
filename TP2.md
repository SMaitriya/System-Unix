1.1 Exercice : Connection ssh root (reprise fin tp-01)


a) Creation d'une clef publique et privé dans notre local

Résultat :
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

genere un cople de clé publique / privée id_rsa : fichier qui contient la clé privée id_rsa.pub : fichier qui contient la cle publique

b) renommé les dossiers en maclef et maclet.pub
- PS C:\Users\maitr> mv C:\Users\maitr\.ssh\id_rsa C:\Users\maitr\.ssh\maclef
- PS C:\Users\maitr> mv C:\Users\maitr\.ssh\id_rsa.pub C:\Users\maitr\.ssh\maclef.pub
- PS C:\Users\maitr> ls C:\Users\maitr\.ssh


- Répertoire : C:\Users\maitr\.ssh


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        06/12/2023     15:20            411 id_ed25519
-a----        06/12/2023     15:20            101 id_ed25519.pub
-a----        08/10/2024     20:00           1779 known_hosts
-a----        02/10/2024     14:15            927 known_hosts.old
-a----        09/10/2024     19:32           2602 maclef
-a----        09/10/2024     19:32            569 maclef.pub

c) Copier sa clef publique avec la commande cat ~/.ssh/id_rsa.pub
PS C:\Users\maitr> cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC6VVJcvVEWVHEpkwmq1jptiAFDwWdjlc9Y4l85MQYy1jkLxNdRsislWEbBUCL1/6L09FaKDQGqzqpRsv4qfKMDHSHzowxcSZOGqieJq3nhye2gc3kQZV/Xm0xkgyCUmjzDjpgkQpTbePK6JO1xoC1SASN9RJHcSWc8sg+uP4jaLswWBhgyyYJcoMatnDVjaijhHwkLnQehTnOvsP54Lmev0bDFI9/UkAiTAZTUbokiiNmTmYnmSEoDiypq5e5YBynpFrVfkMzZ76saqn5P1jPaa4EOu97Yc1Hk39WV4DwZgXkzjrXluk5yAAxrFkC9BL3y+Q4DyTacakKQ79hNvyLTMKn7BM/Hk8pma+KloRqXMwxPFvEDOL6gA7xXjF2TPRahM29p9HFVs1dymv6m4l+JsCPca2rdjO0EGsfp6tvI8azt77WaNc8HxbE2PwmklufSf1rXmtMN57NK/ZD7MpLEYihfJVmEB8OzMJf3vVWfmWICZxtYD/m3fQZI+1L1T4M= maitr@Maitriya

Dans le cas reel ne pas mettre de passphrase pour proteger sa clef privé est une mauvaise pratique car  si quelqu'un parvient à obtenir votre clé privée, il pourra acceder à la machine sans difficulté

1.3 Exercice : Authentification par clef / Connection serveur

a) Vérifier que le ficher authorized_keys existe :
root@serveur1:~# cd /root/.ssh && ls -l
total 20
-rw-r--r-- 1 root root  568  9 oct.  16:25 authorized_keys #le voici
-rw------- 1 root root 2602  9 oct.  15:13 id_rsa
-rw-r--r-- 1 root root  567  9 oct.  15:13 id_rsa.pub
-rw------- 1 root root  978  2 oct.  13:57 known_hosts
-rw-r--r-- 1 root root  142  2 oct.  13:57 known_hosts.old

b) Accéder au dossier authorized_keys:
nano authorized_keys et coller la clef publique

1.4 Exercice : Authentification par clef : depuis la machine hote

Connection a la machine : ssh -i C:\Users\maitr\.ssh\maclef -p 2222 root@127.0.0.1
PS C:\Users\maitr> ssh -i C:\Users\maitr\.ssh\maclef -p 2222 root@127.0.0.1
Linux serveur1 6.1.0-25-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.106-3 (2024-08-26) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Oct  9 20:59:27 2024 from 10.0.2.2

1.5 Exercice : Securisez

- aller dans la config : root@serveur1:~/.ssh# nano /etc/ssh/sshd_config
- Editer :
PasswordAuthentication no
PermitRootLogin without-password
- sauvegarder
- Restart : root@serveur1:~/.ssh# systemctl restart sshd
- Resultat :
PS C:\Users\maitr> ssh -p 2222 root@127.0.0.1
root@127.0.0.1: Permission denied (publickey).
- Il faut utiliser sa clef publique pour pouvoir se connecter avec : ssh -i .ssh\maclef -p 2222 root@127.0.0.1

Attaques type brute-force SSH : cela consistent à tester de nombreux mots de passe sur un compte utilisateur pour trouver le bon (via des scripts notamment)

Protection :
- clés SSH comme on vient de faire : connexion par clé publique en déasctivant les connexions par mot de passe
- Changer le port SSH par défaut (22) : modifier le port pour le SSH . Les bots cherchent généralement sur ce port
- Limiter les utilisateurs autorisés : sshd_config pour n'autoriser que certains utilisateurs ou groupes à se connecter
- Configurer un pare-feu (firewall) : Bloquer les connexions sur les ports non utilisés et limiter l'accès par adresse IP.

2) Processus
2.1 Exercice : Etude des processus UNIX

a) Chercher sur le man la commande pour lister tous les processus
1- man ps
2- "/"
3- chercher "procesus"
4- Resultat :
Pour voir tous les processus du système en utilisant la syntaxe BSD :
   ps ax
   ps axu

- Resultat:
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

b) TIME

- L'information TIME donne le temps total utilisé par le processus.

c) Processus + utilisé le processeur sur votre machine
- utilisez la commande "top"
- Resultat :
  PID UTIL.     PR  NI    VIRT    RES    SHR S  %CPU  %MEM    TEMPS+ COM.
    563 root      20   0   17996  10920   9108 S   0,3   0,5   0:00.74 sshd
      1 root      20   0  102116  12140   9172 S   0,0   0,6   0:00.67 systemd
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd

    PID UTIL.     PR  NI    VIRT    RES    SHR S  %CPU  %MEM    TEMPS+ COM.
    688 root      20   0       0      0      0 I   0,3   0,0   0:00.96 kworker/0:0-events

C'est sshd et kworker qui ont le plus utilisé le processeur sur ma machine

d) Premier processus lancé après le démarrage du système : avec -p 1

Résultat :
root@serveur1:~# ps -p 1
    PID TTY          TIME CMD
      1 ?        00:00:00 systemd
      
Le premier processus lancé après le demarrage est celui avec le PID 1(Process ID 1).

e) A quelle heure votre machine a-t-elle démarrée ? 

e1>
Résultat : 
root@serveur1:~# who -b
démarrage système 2024-10-10 16:03

e2> 
root@serveur1:~# uptime
 16:59:27 up 55 min,  2 users,  load average: 0,00, 0,00, 0,0

 Elle a démarré à 16H03

f) Nombre de processus crées : ps -aux | wc -l  

root@serveur1:~# ps -aux | wc -l
77

Il y'a 77 processus crées.

2.2 -Sous UNIX, chaque processus (except´e le premier) est cr´e´e par un autre processus, son
processus p`ere. Le processus p`ere d’un processus est identifi´e par son PPID (Parent PID)

=>  Trouver une option de la commande ps permettant d’afficher le PPID d’un processus.

root@serveur1:~#  ps -o pid,ppid,comm
    PID    PPID COMMAND
    566     550 bash
   1672     566 ps

550 et 566 sont les PPID du processus.

– Donner la liste ordonnée de tous les processus ancˆetres de la commande ps en cours d’exécution.
root@serveur1:~# ps -o ppid
   PPID
    550
    566
    
3 - Reprendre la question pr´ec´edente avec la commande pstree.
Vous devrez sans doute installer ce package : voir apt update ; apt search ; apt install.

- Lorsque j'ai fait apt search : 
root@serveur1:~# apt search pstree
En train de trier... Fait
Recherche en texte intégral... Fait
psmisc/stable 23.6-1 amd64
utilitaires qui utilisent le système de fichiers proc

- Il faut donc installer psmisc :
root@serveur1:~# apt install psmisc

- faire la rechercher en utilisant le PID du processus:
root@serveur1:~# pstree -ps 566
systemd(1)───sshd(508)───sshd(550)───bash(566)───pstree(1730)

4 - Essayez la commande top, qui affiche les mˆemes informations que ps mais en raffraichissant
p´eriodiquement l’affichage.

Afficher dans top la liste de processus
tri´ee par occupation m´emoire (“resident memory”) d´ecroissant

- cliquez sur "?" pour plus d'information
- pendant que "top" est lancé j'ai appuyé sur shift + "m"
- Resultat :
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
   1748 root      20   0   11728   5228   3304 R   0,0   0,3   0:00.09 top                                                  448 message+  20   0    9120   4912   4340 S   0,0   0,2   0:00.09 dbus-daemon
    566 root      20   0    8184   4796   3472 S   0,0   0,2   0:00.10 bash
    451 root      20   0    5868   3600   2768 S   0,0   0,2   0:00.02 dhclient
    556 root      20   0  168696   3076      0 S   0,0   0,2   0:00.00 (sd-pam)
    446 root      20   0    6612   2692   2444 S   0,0   0,1   0:00.03 cron
    497 root      20   0    5876   1008    924 S   0,0   0,1   0:00.01 agetty          
















