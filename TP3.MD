- Faire nano analyse.sh
- Faire le script :

#!/bin/sh

echo "Bonjour, vous avez rentre $# paramètres."
echo "Le nom du script est $0"
echo "Le 3`eme paramètre est $3"
echo "Voici la liste des paramètres : $@"

- enregistrer

Tester :
root@serveur1:~# ./analyse.sh un deux trois
-bash: ./analyse.sh: Permission non accordée
root@serveur1:~# chmod +x analyse.sh
