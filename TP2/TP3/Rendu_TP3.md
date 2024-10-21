Compte Rendu Exercices Shell

# Exercice 1 :

J'ai créé un fichier `analyse.sh`, ouvert, puis ajouté mon script, et j'ai rendu le fichier exécutable.

root@serveur1:~# nano analyse.sh
root@serveur1:~# chmod +x analyse.sh
root@serveur1:~#
    

## Script :

#!/bin/bash

\# Compter et afficher le nombre de paramètres
echo "Bonjour, vous avez rentré $# paramètres."

\# Afficher le nom du script
echo "Le nom du script est $0."

\# Afficher le troisième paramètre s'il existe
if \[ $# -ge 3 \]; then
    echo "Le 3ème paramètre est $3."
else
    echo "Le 3ème paramètre n'existe pas."
fi

\# Afficher la liste de tous les paramètres
echo "Voici la liste des paramètres : $@"
    

**Explication du script :**

-   `$#` représente le nombre de paramètres passés au script.
-   `$0` affiche le nom du script.
-   `$3` affiche le 3ème paramètre s'il est disponible, sinon, un message informe qu'il n'existe pas.
-   `$@` liste tous les paramètres passés au script.

## Résultat :

root@serveur1:~# ./analyse.sh un deux trois quatre
Bonjour, vous avez rentré 4 paramètres.
Le nom du script est ./analyse.sh.
Le 3ème paramètre est trois.
Voici la liste des paramètres : un deux trois quatre
root@serveur1:~#
    

# Exercice 2 : concat.sh

J'ai créé le fichier `concat.sh` comme dans l'exercice 1. Voici mon script :

#!/bin/bash

\# Vérifier le nombre de paramètres
if \[ $# -ne 2 \]; then
    echo "Erreur : Vous devez entrer exactement 2 paramètres."
    exit 1
fi

\# Concaténer les deux mots
CONCAT="$1$2"

\# Afficher le résultat
echo "La concaténation des mots est : $CONCAT"
    

**Explication du script :**

-   Vérifie que 2 paramètres sont passés, sinon il affiche une erreur et quitte avec `exit 1`.
-   Concatène les deux mots passés en paramètres `$1` et `$2` dans la variable `CONCAT`.
-   Affiche la concaténation.

## Résultat :

root@serveur1:~# ./concat.sh Bonjour Louis
La concaténation des mots est : BonjourLouis
root@serveur1:~#
    

root@serveur1:~# ./concat.sh Bonjour
Erreur : Vous devez entrer exactement 2 paramètres.
root@serveur1:~#
    

# Exercice 3 :

Voici le script `test-fichier.sh` :

#!/bin/bash

\# Vérifier le nombre de paramètres
if \[ $# -ne 1 \]; then
    echo "Erreur : Vous devez entrer exactement 1 paramètre."
    exit 1
fi

\# Définir le fichier passé en paramètre
FICHIER="$1"

\# Vérifier si le fichier existe
if \[ -e "$FICHIER" \]; then
    # Afficher le type du fichier
    if \[ -d "$FICHIER" \]; then
        echo "$FICHIER est un répertoire."
    elif \[ -f "$FICHIER" \]; then
        echo "$FICHIER est un fichier ordinaire."
    elif \[ -L "$FICHIER" \]; then
        echo "$FICHIER est un lien symbolique."
    else
        echo "$FICHIER est un type de fichier non reconnu."
    fi

    # Afficher les permissions
    PERMISSIONS=$(ls -l "$FICHIER" | awk '{print $1}')
    echo "$FICHIER a les permissions : $PERMISSIONS"
else
    echo "Erreur : Le fichier $FICHIER n'existe pas."
    exit 1
fi
    

**Explication du script :**

-   Vérifie que le script reçoit 1 paramètre.
-   Affiche des informations sur le type du fichier : répertoire, fichier ordinaire, lien symbolique, ou type inconnu.
-   Affiche les permissions du fichier en utilisant la commande `ls -l` et `awk` pour extraire les permissions.

## Résultat :

root@serveur1:~# ./test-fichier.sh /etc
/etc est un répertoire.
/etc a les permissions : drwxr-xr-x
root@serveur1:~#
    

root@serveur1:~# ./test-fichier.sh
Erreur : Vous devez entrer exactement 1 paramètre.
root@serveur1:~#
    

root@serveur1:~# ./test-fichier.sh /dms
Erreur : Le fichier /dms n'existe pas.
root@serveur1:~#
    

# Exercice 4 :

Voici le script du fichier `listedir.sh` :

#!/bin/bash

\# Vérifier le nombre de paramètres
if \[ $# -ne 1 \]; then
    echo "Erreur : Vous devez entrer exactement 1 paramètre (le chemin du répertoire)."
    exit 1
fi

\# Définir le répertoire passé en paramètre
REPERTOIRE="$1"

\# Vérifier si le répertoire existe
if \[ -d "$REPERTOIRE" \]; then
    echo "####### fichiers dans $REPERTOIRE/"
    # Lister les fichiers
    for fichier in "$REPERTOIRE"/*; do
        if \[ -f "$fichier" \]; then
            echo "$fichier"
        fi
    done

    echo "####### répertoires dans $REPERTOIRE/"
    # Lister les sous-répertoires
    for dossier in "$REPERTOIRE"/*; do
        if \[ -d "$dossier" \]; then
            echo "$dossier"
        fi
    done
else
    echo "Erreur : Le répertoire $REPERTOIRE n'existe pas."
    exit 1
fi
    

## Résultat :

root@serveur1:~# ./listedir.sh /boot
####### fichiers dans /boot/
/boot/config-6.1.0-25-amd64
/boot/initrd.img-6.1.0-25-amd64
/boot/System.map-6.1.0-25-amd64
/boot/vmlinuz-6.1.0-25-amd64
####### répertoires dans /boot/
/boot/grub
root@serveur1:~#
    

# Exercice 5 :

Voici le script de mon fichier `users_List` :

#!/bin/bash

\# Lister les utilisateurs avec UID supérieur à 100
echo "Utilisateurs avec UID supérieur à 100 :"
awk -F: '$3 > 100 { print $1 }' /etc/passwd
    

## Résultat :

Utilisateurs avec UID supérieur à 100 :
nobody
systemd-network
avahi-autoipd
sshd
polkitd
root@serveur1:~#
    

## Exercice 6 : Mon utilisateur existe t-il ?

Voici le script du fichier `check_User.sh` :

```

#!/bin/bash

# Vérification du nombre de paramètres
# Le script nécessite exactement 1 paramètre : un nom d'utilisateur ou un UID.
if [ $# -ne 1 ]; then
    echo "Usage: $0 <nom_utilisateur|UID>"
    exit 1
fi

# Vérification si c'est un nombre (UID)
# Si le paramètre est un nombre, c'est un UID, donc on cherche l'utilisateur correspondant.
if [[ $1 =~ ^[0-9]+$ ]]; then
    uid=$1
    user=$(getent passwd "$uid" | cut -d: -f1)
    if [ -n "$user" ]; then
        echo "L'utilisateur avec UID $uid est : $user"
    else
        echo "Aucun utilisateur trouvé avec l'UID $uid."
    fi
else
    # Si ce n'est pas un nombre, on suppose que c'est un nom d'utilisateur.
    user=$1
    uid=$(id -u "$user" 2>/dev/null)
    if [ $? -eq 0 ]; then
        echo "L'utilisateur $user existe avec UID : $uid"
    else
        echo "L'utilisateur $user n'existe pas."
    fi
fi

```

Voici la réponse donnée par ce script :

```

root@serveur1:~# ./check_User.sh root
L'utilisateur root existe avec UID : 0

root@serveur1:~# ./check_User.sh dd
L'utilisateur dd n'existe pas.

root@serveur1:~# ./check_User.sh
Usage: ./check_User.sh <nom_utilisateur|UID>

```

## Exercice 7 : Création d'utilisateur

Voici le script du fichier `create_user.sh` :

```

#!/bin/bash

# Vérification si l'utilisateur courant est root
# Le script doit être exécuté en tant que root pour pouvoir créer des utilisateurs.
if [ "$EUID" -ne 0 ]; then
    echo "Ce script doit être exécuté en tant que root." >&2
    exit 1
fi

# Demander les informations nécessaires pour créer un nouvel utilisateur.
read -p "Entrez le login de l'utilisateur : " login
read -p "Entrez le nom complet de l'utilisateur : " fullname
read -p "Entrez l'UID de l'utilisateur : " uid
read -p "Entrez le GID de l'utilisateur : " gid
read -p "Entrez des commentaires : " comments

# Vérifier si l'utilisateur existe déjà
if id "$login" &>/dev/null; then
    echo "L'utilisateur '$login' existe déjà." >&2
    exit 1
fi

# Création de l'utilisateur avec les informations fournies
useradd -m -u "$uid" -g "$gid" -c "$comments" "$login"

# Vérification de la création de l'utilisateur
if [ $? -eq 0 ]; then
    echo "L'utilisateur '$login' a été créé avec succès."
else
    echo "Échec de la création de l'utilisateur '$login'." >&2
    exit 1
fi

```

Voici la réponse obtenue lors de l'exécution :

```

root@serveur1:~# ./create_user.sh
Entrez le login de l'utilisateur : dms
Entrez le nom complet de l'utilisateur : tr dams
Entrez l'UID de l'utilisateur : 202
Entrez le GID de l'utilisateur : 5
Entrez des commentaires : user secondaire
useradd warning: dms's uid 202 outside of the UID_MIN 1000 and UID_MAX 60000 range.
L'utilisateur 'dms' a été créé avec succès.

```

## Exercice 8 : Lecture au clavier

Voici le script permettant de lire et afficher une chaîne entrée au clavier :

```

#!/bin/bash

# Demander à l'utilisateur d'entrer son nom.
echo -n "Entrer votre nom: "
read nom

# Afficher le nom saisi par l'utilisateur.
echo "Votre nom est $nom"

```

Voici la réponse donnée :

```

root@serveur1:~# ./lecture.sh
Entrer votre nom: dms
Votre nom est dms

```

## Exercice 9 : Appréciation des notes

Voici le script du fichier `appreciation.sh` :

```

#!/bin/bash

# Boucle infinie pour demander des notes à l'utilisateur.
while true; do
    # Demander une note à l'utilisateur, avec la possibilité de quitter.
    read -p "Veuillez saisir une note (ou 'q' pour quitter) : " note

    # Si l'utilisateur entre 'q', le script se termine.
    if [ "$note" == "q" ]; then
        echo "Au revoir!"
        exit 0
    fi

    # Vérifier que la note est bien un nombre.
    if ! [[ "$note" =~ ^[0-9]+(\.[0-9]+)?$ ]]; then
        echo "Veuillez entrer une note valide."
        continue
    fi

    # Convertir la note pour les comparaisons.
    note_float=$(echo "$note" | bc)

    # Afficher une appréciation selon la note.
    if (( $(echo "$note_float >= 16" | bc -l) )); then
        echo "Très bien"
    elif (( $(echo "$note_float >= 14" | bc -l) )); then
        echo "Bien"
    elif (( $(echo "$note_float >= 12" | bc -l) )); then
        echo "Assez bien"
    elif (( $(echo "$note_float >= 10" | bc -l) )); then
        echo "Moyen"
    else
        echo "Insuffisant"
    fi
done

```

Voici la réponse donnée par ce script :

```

root@serveur1:~# ./appreciation.sh
Veuillez saisir une note (ou 'q' pour quitter) : 10
Moyen
Veuillez saisir une note (ou 'q' pour quitter) : 17
Très bien
Veuillez saisir une note (ou 'q' pour quitter) : 14
Bien
root@serveur1:~#

```
