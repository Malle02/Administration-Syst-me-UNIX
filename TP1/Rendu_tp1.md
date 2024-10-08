
`# Rendu TP 01 : Installation Serveurs

## 1. Installation de la machine

L'installation a été un succès, même si j'ai a eu quelques problèmes liés à la configuration, du à un manque de puissance de mon PC et quelques mauvaises manipulations de ma part. Cependant, cela a été un franc succès après plusieurs tentatives. Le TP est bien expliqué et très bien guidé.

## 2. Post-Installation

Pour la connexion à ma machine, j’ai rencontré quelques difficultés liées au réseau sur lequel était ma machine virtuelle. J’ai dû effectuer quelques manipulations dans le réseau NAT de la machine virtuelle. À la fin, j’ai exécuté la commande suivante pour finaliser la connexion :

PS C:\Users\malle> ssh -p 2222 root@127.0.0.1` 


### 2.3 Nombre de packages :

`root@serveur1:~# dpkg -l | wc -l` 

**Résultat :** 287

### 2.4 Espace de stockage :

`root@serveur1:~# df -h` 

**Résultat :**

`Sys. de fichiers Taille Utilisé Dispo Uti% Monté sur
udev            962M     0  962M   0% /dev
tmpfs           197M  552K  197M   1% /run
/dev/sda1      9,1G  1,2G  7,5G  14% /
tmpfs           984M     0  984M   0% /dev/shm
tmpfs            5,0M     0  5,0M   0% /run/lock
/dev/sda4      5,9G   24K  5,6G   1% /swap
/dev/sda2      3,6G   32K  3,4G   1% /tmp
/dev/sda3      921M   88M  770M  11% /var/log
tmpfs           197M     0  197M   0% /run/user/0` 

## 2.5 Détails des commandes

1.  **Locales :** `echo $LANG`
    
    -   **Explication :** Cette commande affiche la locale actuellement utilisée par le système. Les locales définissent la langue, les paramètres régionaux et le jeu de caractères. Mon résultat est :
        
    `root@serveur1:~# echo $LANG
    fr_FR.UTF-8` 
    
    Cela signifie que mon système est configuré en français avec l'encodage UTF-8. La commande `env` affiche toutes les variables d’environnement.
    
2.  **Nom de la machine :** `hostname`
    
    -   **Explication :** Cette commande affiche le nom de l'hôte, qui est le nom de la machine sur le réseau. Mon résultat est :
   
    `root@serveur1:~# hostname
    serveur1` 
    
3.  **Domaine :** `hostname --domain`
    
    -   **Explication :** Cette commande permet d'afficher le domaine de l'hôte si celui-ci est configuré. Le domaine est utile pour les réseaux utilisant un système de noms de domaine. Mon résultat est :
    
    `root@serveur1:~# hostname --domain
    ufr-info-p6.jussieu.fr` 
    
4.  **Vérification des emplacements des dépôts :** `cat /etc/apt/sources.list | grep -v -E '^#|^$'`
    
    -   **Explication :** Le fichier `/etc/apt/sources.list` contient les sources de paquets que le gestionnaire APT utilise pour télécharger et installer des logiciels. Cette commande affiche les lignes non commentées (sans `#`) et non vides.
      
    `root@serveur1:~# cat /etc/apt/sources.list | grep -v -E '^#|^$'
    deb http://ftp.fr.debian.org/debian/ bookworm main non-free-firmware
    deb-src http://ftp.fr.debian.org/debian/ bookworm main` 
    
` 
    
5.  **Fichier shadow :** `cat /etc/shadow | grep -vE ':\*:|:!\*:'`
    
    -   **Explication :** Le fichier `/etc/shadow` contient les mots de passe chiffrés des utilisateurs. Cette commande filtre et affiche uniquement les utilisateurs avec un mot de passe défini, en excluant les comptes sans mot de passe ou verrouillés.
        
    `root@serveur1:~# cat /etc/shadow | grep -vE ':\*:|:!\*:'` 
    
6.  **Comptes utilisateurs :** `cat /etc/passwd | grep -vE 'nologin|sync'`
    
    -   **Explication :** Le fichier `/etc/passwd` contient des informations sur les utilisateurs du système. Cette commande filtre et affiche les utilisateurs qui ont des comptes "connectables", en excluant ceux dont l'accès est bloqué (nologin ou sync).
    
    `root@serveur1:~# cat /etc/passwd | grep -vE 'nologin|sync'` 
    
7.  **Commande fdisk -l et fdisk -x**
    
    -   **Explication :**
        -   `fdisk -l` liste toutes les partitions présentes sur les disques connectés à la machine. Elle affiche les informations telles que la taille des partitions et leur point de montage.
        -   `fdisk -x` permet d’afficher les partitions avec des détails supplémentaires, comme les UUID des partitions.
    -   **Résultat :**
       
    `root@serveur1:~# fdisk -l
    fdisk: impossible d'ouvrir : Aucun fichier ou dossier de ce type
    root@serveur1:~#` 
    
8.  **Commande df -h**
    
    -  
    -   **Explication :** La commande `df -h` affiche l'utilisation de l'espace disque sur le système de fichiers, avec des tailles lisibles par l'humain (en GiB, MiB, etc.). 
    -   **Résultat :**
      
    `root@serveur1:~# df -h
    Sys. de fichiers Taille Utilisé Dispo Uti% Monté sur
    udev            962M     0  962M   0% /dev
    tmpfs           197M  552K  197M   1% /run
    /dev/sda1      9,1G  1,2G  7,5G  14% /
    tmpfs           984M     0  984M   0% /dev/shm
    tmpfs            5,0M     0  5,0M   0% /run/lock
    /dev/sda4      5,9G   24K  5,6G   1% /swap
    /dev/sda2      3,6G   32K  3,4G   1% /tmp
    /dev/sda3      921M   88M  770M  11% /var/log
    tmpfs           197M     0  197M   0% /run/user/0` 
    

## 3. Aller plus loin

### 3.1. Preseed :

Preseed est une méthode permettant d'automatiser l'installation de Debian en fournissant à l'installeur un fichier contenant toutes les réponses aux questions posées pendant le processus (langue, partitionnement, utilisateurs, etc.). Cela permet de réaliser des installations sans intervention manuelle. Ce fichier peut être utilisé via une clé USB, un serveur réseau ou une image ISO personnalisée.

### 3.2 Réinitialiser le mot de passe root :

1.  **Accéder à GRUB :**
    
    -   je redémarre l'ordinateur.
    -   Lorsque l'écran de GRUB apparaît, sélectionner l'entrée Debian et appuyer sur `e` pour éditer les paramètres de démarrage.
2.  **je modifie  la ligne de commande :**
    
    -   La ligne commençant par `linux`.
    -   remplacer  `rw single` à la fin de cette ligne pour permettre l'accès en lecture-écriture et démarrer en mode single-user.
       
    `linux /boot/vmlinuz-... root=UUID=... rw single` 
    
3.  ** les modifications :**
    
    -    `Ctrl + X` pour démarrer le système avec les options modifiées.
4.  **Pour changer le mot de passe root :**
    
    
    `passwd root` 
    
    -   Puis je saisie le nouveau mot de passe .
5.  **Enfin je redémarre :**
    
    -    `reboot` pour redémarrer le système.

### 3.3 redimentionnement partition :

-
    
- On doit installer GParted : ```apt update apt install gparted
- Ouvrir GParted :

`gparted`
Sélectionner la partition à redimensionner :

Choisir **Resize/Move**.

Ajuster la taille 

Entre la nouvelle taille manuellement.

Puis Appliquer les changements.

Ouvrir Parted :

`parted /dev/sda`
On remplace `/dev/sda` par le disque que l’on veut.

Puis on affiche les partitions :

`print` 

On supprime la partition existante (sans perdre de données) :


`rm N  # Remplace N par le numéro de la partition à supprimer` 

Puis on recrée la partition avec la nouvelle taille :

`mkpart primary ext4 START END  # Remplace START et END par les valeurs désirées` 

Et on quitte Parted :

`quit`
## Conclusion

Moi, je n’ai pas réussi à  faire le 3.3 sur ma machine, mais j’ai trouvé la manip à faire sur internet. Je pense que j’ai un souci avec mon PC.

Enfin, le TP était très instructif. Cela m'a permis d’apprendre à connaître davantage de commandes Linux, et c’était la première fois qu’une machine virtuelle marchait sur mon PC. J’ai eu beaucoup de plaisir à pratiquer ce TP.