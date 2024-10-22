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

Script :
```

#!/bin/bash

# Vérification de l'utilisateur actuel (doit être root)
if [ "$EUID" -ne 0 ]; then
    echo "Ce script doit être exécuté en tant qu'utilisateur root."
    exit 1
fi

# Fonction pour vérifier si un répertoire home existe déjà
check_home_directory() {
    home_dir="/home/$1"
    if [ -d "$home_dir" ]; then
        echo "Le répertoire home $home_dir existe déjà."
        return 1
    else
        return 0
    fi
}

# Demander des informations pour la création du nouvel utilisateur
read -p "Login : " login

# Vérifier si l'utilisateur existe déjà
if id "$login" &>/dev/null; then
    echo "L'utilisateur $login existe déjà. Veuillez choisir un autre login."
    exit 1
fi

read -p "Nom : " nom
read -p "Prénom : " prenom
read -p "UID : " uid

# Vérifier si l'UID existe déjà
if getent passwd "$uid" &>/dev/null; then
    echo "L'UID $uid existe déjà. Veuillez choisir un autre UID."
    exit 1
fi

read -p "GID : " gid
read -p "Commentaires : " commentaires

# Vérifier si le répertoire home existe déjà
if check_home_directory "$login"; then
    # Création du nouvel utilisateur
    useradd -m -c "$commentaires" -d "/home/$login" -u "$uid" -g "$gid" "$login"
    echo "L'utilisateur $login a été créé avec succès."
else
    echo "La création de l'utilisateur a été annulée en raison de la présence d'un répertoire home existant."
fi

```


Résultat:

```

root@serveur1:~# ./create_user.sh
Login : newuser
Nom : Dupond
Prénom : Jean
UID : 2301
GID : 100
Commentaires : Utilisateur test
L'utilisateur newuser a été créé avec succès.
root@serveur1:~# ./liste_utilisateurs.sh
Liste des utilisateurs avec un UID supérieur à 100 :
nobody : 65534
systemd-network : 998
systemd-timesync : 997
avahi-autoipd : 101
sshd : 102
newuser : 2301
```

Exercice : lecture au clavier

- Test 1
```
root@serveur1:~# echo -n "Entrer votre nom: "
Entrer votre nom: root@serveur1:~# Mat
-bash: Mat : commande introuvable
root@serveur1:~# read nom
mat
root@serveur1:~# echo "Votre nom est $nom"
Votre nom est mat
```

Script : 
```

#!/bin/bash

# Vérification du nombre de paramètres
if [ $# -ne 1 ]; then
    echo "Utilisation : $0 <répertoire>"
    exit 1
fi

directory="$1"

# Vérification de l'existence du répertoire
if [ ! -d "$directory" ]; then
    echo "Le répertoire spécifié n'existe pas."
    exit 1
fi

# Compteur pour les fichiers texte
count=0

# Parcours du répertoire
for file in "$directory"/*; do
    if [ -f "$file" ] && file -b "$file" | grep -q "text"; then
        count=$((count + 1))
        echo "Fichier trouvé : $file"  # Message de débogage
        read -p "Voulez-vous visualiser le fichier $file ? (O/n) " response
        if [[ "$response" =~ ^[Oo]$ ]]; then
            more "$file"
        fi
    fi
done

if [ $count -eq 0 ]; then
    echo "Aucun fichier texte trouvé dans le répertoire $directory."
fi
```



Résultat :
```
root@serveur1:~# ./visualiser.sh repertoiretest
Voulez-vous visualiser le fichier repertoiretest/fichier1.txt ? (O/n)
#affichage du texte
```

# Exercice : appréciation


Script : 

```
#!/bin/bash

while true; do
    read -p "Entrez une note (ou 'q' pour quitter) : " note

    if [ "$note" = "q" ]; then
        echo "Programme quitté."
        exit 0
    fi

    if [[ $note =~ ^-?[0-9]+$ ]]; then
        if ((note >= 16 && note <= 20)); then
            echo "Très bien"
        elif ((note >= 14 && note < 16)); then
            echo "Bien"
        elif ((note >= 12 && note < 14)); then
            echo "Assez bien"
        elif ((note >= 10 && note < 12)); then
            echo "Moyen"
        elif ((note < 10)); then
            echo "Insuffisant"
        else
            echo "Note invalide. La note doit être entre 0 et 20."
        fi
    else
        echo "Veuillez entrer un entier."
    fi
done
```





Résultat :

```
root@serveur1:~# ./evaluer.ssh
Entrez une note (ou 'q' pour quitter) : 17
Très bien
Entrez une note (ou 'q' pour quitter) : 10
Moyen
Entrez une note (ou 'q' pour quitter) : 5
Insuffisant
Entrez une note (ou 'q' pour quitter) : q
Programme quitté.
root@serveur1:~# ./evaluer.ssh
Entrez une note (ou 'q' pour quitter) : 17
Très bien
Entrez une note (ou 'q' pour quitter) : 15
Bien
Entrez une note (ou 'q' pour quitter) : 13
Assez bien
Entrez une note (ou 'q' pour quitter) : 10
Moyen
Entrez une note (ou 'q' pour quitter) : 5
Insuffisant
Entrez une note (ou 'q' pour quitter) : q
Programme quitté.
```













