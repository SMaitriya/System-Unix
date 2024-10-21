# Exercice : paramètres


- Faire nano analyse.sh
- Faire le script :

```
#!/bin/sh
echo "Bonjour, vous avez rentre $# paramètres."
echo "Le nom du script est $0"
echo "Le 3`eme paramètre est $3"
echo "Voici la liste des paramètres : $@"
```
- enregistrer

Tester :

```
root@serveur1:~# ./analyse.sh un deux trois
-bash: ./analyse.sh: Permission non accordée
root@serveur1:~# chmod +x analyse.sh
root@serveur1:~# ./analyse.sh un deux trois
Bonjour, vous avez rentre 3 paramètres.
Le nom du script est ./analyse.sh
Le 3eme paramètre est trois
Voici la liste des paramètres : un deux trois
```

# Exercice : vérification du nombre de paramètres

- Script : 
```
#!/bin/sh
if [ "$#" -ne 2 ]; then
  echo "Il faut deux paramètres!"
  exit 1
else
CONCAT="$1$2"
  echo "Résultat de la concaténation : $CONCAT"
fi
```
- Résultat :

```
root@serveur1:~# ./concat.sh Salut toi
Résultat de la concaténation : Saluttoi
```

# Exercice : argument type et droits

- Script : 
```
#!/bin/sh
if [ $# -eq 0 ]; then
    echo "Il faut un fichier"
    exit 1
fi
fichier="$1"
type=$(file -b "$fichier")
droits=$(stat -c "%A" "$fichier")
echo "Le fichier $fichier est un $type, accessible par $USER en $droits"
```

- Résultat : 
```
root@serveur1:~# bash ./test-fichier.sh testpourscript.txt
Le fichier testpourscript.txt est un directory, accessible par root en drwxr-xr-x
```


# Exercice : Afficher le contenu d’un répertoire
(commencement)
-Script:
```

#!/bin/sh
if [ $# -ne 1 ]; then        # Vérifie si un argument est fourni
  echo "Il faut un argument"
  exit 1
fi
directory="$1"
if [ ! -d "$directory" ]; then # vérifie si c'est un répertoire
  echo "Le répertoire spécifié n'existe pas."
  exit 1
fi
echo "####### Fichiers dans $directory/"

 # Affiche les fichiers et vérifie s'il y en a
find "$directory" -maxdepth 1 -type f 

if [ $(find "$directory" -maxdepth 1 -type f | wc -l) -eq 0 ]; then
  echo "Aucun fichier trouvé dans $directory."
fi
```



Résultat : 

- On va créer un répertoire test avec des fichiers à l'intérieur pour tester. On va aussi tester sur un répertoire inexistant
```

root@serveur1:~# ./listedir.sh sshd
Le répertoire spécifié n'existe pas.
root@serveur1:~# mkdir repertoiretest
root@serveur1:~# touch repertoiretest/fichier1.txt
root@serveur1:~# touch repertoiretest/fichier2.txt
root@serveur1:~# ./listedir.sh repertoiretest
####### Fichiers dans repertoiretest/
repertoiretest/fichier2.txt
repertoiretest/fichier1.txt

```

# Exercice : Lister les utilisateurs


Script :

```
#!/bin/bash

echo "Liste des utilisateurs avec un UID supérieur à 100 :"
# Utilisation de cut pour extraire le nom d'utilisateur (champ 1) et l'UID (champ 3)
for user in $(cut -d: -f1,3 /etc/passwd); do
    # Utilisation de awk pour séparer le nom d'utilisateur et l'UID
    login=$(echo "$user" | awk -F: '{print $1}')
    uid=$(echo "$user" | awk -F: '{print $2}')
    # Vérification si l'UID est supérieur à 100
    if [ "$uid" -gt 100 ]; then
        echo "$login : $uid"
    fi
done
```

Résultat : 

```
root@serveur1:~# ./liste_utilisateurs.sh
Liste des utilisateurs avec un UID supérieur à 100 :
nobody : 65534
systemd-network : 998
systemd-timesync : 997
avahi-autoipd : 101
sshd : 102
```

# Exercice : Mon utilisateur existe t’il

Script:
```

# Récupération des paramètres
login_utilisateur="$1"
uid_utilisateur="$2"

# Vérification par login
uid_login=$(getent passwd "$login_utilisateur" | cut -d: -f3)
if [ -n "$uid_login" ]; then
    echo "L'utilisateur '$login_utilisateur' existe avec UID : $uid_login"
fi

# Vérification par UID
login_uid=$(getent passwd | awk -F: -v uid="$uid_utilisateur" '$3 == uid {print $1}')
if [ -n "$login_uid" ]; then
    echo "L'utilisateur avec UID '$uid_utilisateur' est : $login_uid"
fi
 3)
for user in $(cut -d: -f1,3 /etc/passwd); do
    # Utilisation de awk pour séparer le nom d'utilisateur et l'UID
    login=$(echo "$user" | awk -F: '{print $1}')
    uid=$(echo "$user" | awk -F: '{print $2}')
    # Vérification si l'UID est supérieur à 100
    if [ "$uid" -gt 100 ]; then
        echo "$login : $uid"
    fi
done
```


Résultat : 
```

root@serveur1:~# ./verifier_utilisateur.sh login nobody
L'utilisateur existe avec UID : 65534
root@serveur1:~# ./verifier_utilisateur.sh UID 65534
L'utilisateur existe avec UID : 65534
```


# Exercice : Creation utilisateur












