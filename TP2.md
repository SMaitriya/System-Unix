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
PS C:\Users\maitr> mv C:\Users\maitr\.ssh\id_rsa C:\Users\maitr\.ssh\maclef
PS C:\Users\maitr> mv C:\Users\maitr\.ssh\id_rsa.pub C:\Users\maitr\.ssh\maclef.pub
PS C:\Users\maitr> ls C:\Users\maitr\.ssh


    Répertoire : C:\Users\maitr\.ssh


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
nano authorized_keys






