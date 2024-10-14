## TP 02 : Services, processus signaux




`## 1 Secure Shell : SSH

##1.1 Connection ssh root (reprise fin tp-01)

J'avais déjà configuré SSH pendant le TP 1. Pour permettre la connexion à root à distance, j'ai décommenté la ligne `PermitRootLogin` et je l'ai mise à `yes`. J'ai accédé au fichier de configuration avec la commande suivante :

```
nano /etc/ssh/sshd_config`` 

Ensuite, je me suis connecté sur mon PC avec cette commande :

`ssh -p 2222 root@127.0.0.1` 

## 1.2 Génération de clés

J'ai généré la clé avec cette commande :

`ssh-keygen -t rsa -b 4096` 

Voici le résultat de la commande :

`Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa
Your public key has been saved in /root/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:616N18shfuASVFWEcCz354LSVdqTtFqDER8j0t4pROk root@serveur1
The key's randomart image is:
+---[RSA 4096]----+
|           .+B=*o|
|            +**.+|
|           .+o=*+|
|          .  EoO+|
|        S. . o+.+|
|         .oo+o. .|
|        . o++.o. |
|       . ..o.o.o |
|       .o  ...o  |
+----[SHA256]-----+` 

### a. Copie de la clé vers mon serveur distant

La commande pour accéder à ma clé publique est :
`cat ~/.ssh/id_rsa.pub` 

Voici le contenu de ma clé publique :
`ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDA3xwzEBrsPYqYDcsnjI2ueDIXRhySCTujTgZX57yl9c7Gd+AHBtnH4EJ/Sc4Xa9dISdMbQ8ciN29/YGipXQlOfOG0aFnKYpszg9PIKXHwTNx/uEa0zaTMxc3Uz0qMlyHwbnrE4o6syD9oIe60OCkpXPO/ki6VshDOi5JRPxa4NCxuki9TFRGyEnXwsYoti5WKyDIUWu9Z5p9cHCmXkGC0/aawEAD3x3k5doGOYrKzzsydt++bgNysR8YpvfDO4IToxSgzl6qP2vG0E/xBE1vzG/n3HoZSWa9kRr6rGCWCGyalaZwm0waMEcQDd65Ib1SKbqNFytUsjXYA2NZyt5f6hBCcuWGvzFyqZ+X4QDz9ytYFThBK1Disy9qVpeySNF6kIbTHemAOizVLEDZz3HkSI0hg85mg4sNQPYQSx+GOz2iv+d27RIrSc7Ei/zFkwCw5nYDs+sApRIQYf+gfJN8w8tdMYHHkUMgU3vYAUvXTDeJK4SylFQ3UO4e0rUmZlFaVSWBC8S/abITUJMj0QT/fUO9PAANVEnx6TWf5BU51TvV2Zq8bvOUSclzsc0XC2dh232qZ3HjMumzGPP3fh1FExaZ6VUz7RjCnjdtYaROoNB02wZkut9J9VHEhuZY9YAZ91XPFhKtW9aTJjHOdTuPztq4SsdEhlyVngQ1UtVrw5w== malle@LAPTOP-2MNUDURO` 

D'abord, pour la connexion avec ma clé, l'accès avait été refusé. Je n'arrivais pas à me connecter parce que je n'utilisais pas la bonne méthode. Je devais générer la clé sur ma machine hôte, mais je faisais le contraire.

### Connexion refusée

`ssh -i ~/.ssh/id_rsa root@127.0.0.1
# ssh: connect to host 127.0.0.1 port 22: Connection refused` 

Pour le TP, j'ai utilisé ma machine personnelle et j'ai utilisé Windows avec le terminal PowerShell.

Ne pas utiliser de passphrase pour une clé privée SSH est dangereux en environnement réel, car si la clé privée est volée ou compromise, elle peut être utilisée sans aucune protection. Une passphrase ajoute une couche de sécurité en chiffrant la clé, ce qui empêche une utilisation immédiate sans autorisation supplémentaire.

Enfin, j'avais déjà un dossier SSH créé et le dossier `authorized_keys`. J'ai juste copié ma clé publique pour que ma connexion soit une réussite.

## 1.4 : Authentification par clef : depuis la machine hote


`ssh -i ~/.ssh/id_rsa -p 2222 root@127.0.0.1` 

Voici le résultat :

`Linux serveur1 6.1.0-25-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.106-3 (2024-08-26) x86_64

The programs included with the Debian GNU/Linux system are free software; 
the exact distribution terms for each program are described in the 
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent 
permitted by applicable law.
Last login: Wed Oct  9 16:10:31 2024 from 10.0.2.2
root@serveur1:~#

J'ai réussi à me connecter.

`## 
## 1.5 Exercice : S´ecurisez

Pour ce faire, j'ai accédé à la configuration SSH avec la commande :` 

nano /etc/ssh/sshd_config


 `### Modifications réalisées :

- Désactiver l'authentification par mot de passe :` 

PasswordAuthentication no

 `- Désactiver l'accès SSH pour l'utilisateur root :` 

PermitRootLogin no

 `- Modifier la ligne pour interdire l'accès root par mot de passe :` 

PermitRootLogin prohibit-password

 `- Activer l'authentification par clé publique :` 

PubkeyAuthentication yes

 `J'ai ensuite enregistré et redémarré le service SSH pour appliquer les modifications :` 

systemctl restart ssh

 `--- 
### Attaques par brute force SSH

Une **attaque par brute force** consiste à essayer un grand nombre de combinaisons de mots de passe pour accéder à un compte. Dans le contexte de SSH, un attaquant tente de deviner le mot de passe de l'utilisateur pour se connecter au serveur. Ces attaques sont souvent automatisées avec des scripts, ce qui rend l'attaque plus rapide et plus efficace. Elles peuvent non seulement compromettre la sécurité du serveur, mais aussi augmenter considérablement le trafic, ralentissant les performances.

(Source : Mes cours de cybersécurité - BTS)

--- 
##  Autres techniques de protection contre les attaques

### 1. Utiliser un pare-feu (Firewall)

- **Description** : Configurer un pare-feu pour n'autoriser que certaines adresses IP à accéder au port SSH (par défaut le port 22).
- **Avantages** : Limite les tentatives d'accès à celles provenant d'adresses IP de confiance.
- **Inconvénients** : Difficile à gérer si l'on doit accéder au serveur depuis plusieurs adresses IP.
- **Exemple** :` 

ufw allow from <190.0.10.1> to 22


 ``--- 
### Changer le port SSH par défaut

- **Description** : Modifier le port par défaut (22) pour éviter que les scanners de ports détectent facilement le service SSH.
- **Avantages** : Réduit les attaques automatisées ciblant le port standard.
- **Inconvénients** : Peut provoquer des confusions si on oublie le nouveau port.
- **Exemple** :
Modifier la ligne suivante dans le fichier `sshd_config` :`` 

Port 22

``Par un autre port, puis redémarrer SSH.

--- 
###  Limiter le nombre de tentatives de connexion

- **Description** : Utiliser **fail2ban** pour bloquer une adresse IP après un certain nombre de tentatives échouées.
- **Avantages** : Protège efficacement contre les attaques par brute force.
- **Inconvénients** : Configuration initiale nécessaire et risque de bloquer des utilisateurs légitimes.
  
--- 
### Activer l’authentification à deux facteurs (2FA)

- **Description** : Ajouter une couche de sécurité en demandant un code généré par une application (ex. Google Authenticator) en plus du mot de passe.
- **Avantages** : Renforce considérablement la sécurité des connexions.
- **Inconvénients** : Plus complexe à mettre en place et ajoute des étapes à la connexion.

--- 
## 2. Processus 

### 2.1 Etude des processus UNIX

J'ai commencé par installer `ps` avec :`` 

apt install ps


`Puis, j'ai exécuté la commande suivante pour afficher les processus, triés par utilisation CPU :` 

ps aux --sort=-%cpu

 `#### réponse :` 

USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND root 714 66.6 0.2 11168 4296 pts/0 R+ 17:22 0:00 ps aux --sort=-%cpu root 593 0.6 0.0 0 0 ? I 16:46 0:12 [kworker/0:2-cgroup_destroy] root 666 0.2 0.0 0 0 ? I 17:07 0:01 [kworker/0:4-ata_sff] root 690 0.1 0.0 0 0 ? I 17:12 0:00 [kworker/0:0-events]


TIME === représente le temps total CPU utilisé par le processus, affiché en minutes et secondes. Cela indique combien de temps le CPU a été utilisé par ce processus depuis son lancement.

 ``- **Le processus ayant utilisé le plus de CPU est** :  
 - **PID** : 714  
 - **USER** : root  
 - **%CPU** : 66.6  
 - **COMMAND** : `ps aux --sort=-%cpu`

Le premier processus lancé après le démarrage du système est identifié par le **PID 1**, qui correspond au **/sbin/init**.

###  Commande `uptime`

Pour afficher le temps de démarrage du serveur :`` 

uptime

17:30:47 up 58 min, 3 users, load average: 0.00, 0.00, 0.00

 ``Le serveur a démarré il y a 58 minutes, soit à **16:32:47**.

J'ai utiliser uptime, je pouvais aussi utiliser : who –b => @serveur1:~# who -b
démarrage système 2024-10-10 16:32

**--le nombre approximatif de processus créés** depuis le démarrage (“boot”) de ma machine = vioci la comande : ps -e | wc –l

@serveur1:~# ps -e | wc -l

82
root@serveur1:~# donc le nombre est 82.


**2- Sous UNIX, chaque processus (except´e le premier) est créé par un autre processus, son processus père.
une option de la commande ps permettant d’afficher le PPID d’un processus === @serveur1:~# ps -eo  pid,ppid,cmd**

Voici une partie de la reponse donnée par la comande

PID PPID CMD

1 0 /sbin/init

2 0 [kthreadd]

3 2 [rcu_gp]

4 2 [rcu_par_gp]

5 2 [slub_flushwq]

6 2 [netns]

8 2 [kworker/0:0H-events_highpri]

10 2 [mm_percpu_wq]

11 2 [rcu_tasks_kthread]

12 2 [rcu_tasks_rude_kthread]

13 2 [rcu_tasks_trace_kthread]--- 

**---- la liste ordonnee de tous les processus ancetres de la commande ps en cours d’execution sont disponible avec cette comande et =>: @serveur1:~# ps –forest**

Voici la reponsse de la comande

PID TTY TIME CMD

710 pts/0 00:00:00 bash

777 pts/0 00:00:00 \_ ps

root@serveur1:~#
## 3--Arbre des processus avec `pstree`

Après avoir installé `pstree` avec la commande :`` 

apt install pstree

`J'ai exécuté :` 

pstree

reponse ====

systemd─┬─agetty ├─cron ├─dbus-daemon ├─dhclient ├─login───bash───pstree ├─systemd───(sd-pam) ├─systemd-journal ├─systemd-logind ├─systemd-udevd └─wpa_supplicant


 ``--- 
## 4. Utilisation de `top`

J'ai installé `top` avec :`` 

apt install top

``En exécutant la commande `top`, l'écran se rafraîchit constamment avec de nouvelles données. Voici un exemple :`` 

top - 17:45:16 up 1:12, 3 users, load average: 0,03, 0,05, 0,01 Tâches: 81 total, 1 en cours, 80 en veille, 0 arrêté, 0 zombie %Cpu(s): 0,0 ut, 0,7 sy, 0,0 ni, 99,3 id, 0,0 wa, 0,0 hi, 0,0 si, 0,0 st

TOP affiche des informations sur les processus en  cours  d'exécution sur le système, en les mettant à jour en temps réel. Cela inclut  l'utilisation du CPU, de la mémoire, et d'autres  statistiques.

 ``### Trier par utilisation de la mémoire :
Appuyer sur `Shift + M` pour trier les processus selon leur utilisation de la mémoire.

Le processus le plus gourmand en CPU est **systemd** (PID 1).

Pour afficher le detail j’ai  utiliser man systemd pour avoir plus d’infos sur ce  processueur la reponse suit =>

Il est le gestionnaire de services et d'initialisation sous Linux. Il démarre le système  lors du boot, gère les services (démarrage, arrêt, redémarrage) et leurs  dépendances, et assure la journalisation des événements. En somme, c'est  essentiel pour le bon fonctionnement des systèmes Linux modernes.

Pour changer de couleur j’ai  appuyez sur maj Z

Pour mettre  en  avant les colones de trie : shift +>

Et enfin pour changer la colonne de trie == Shift + F

---

## 5. Comparaison entre `top` et `htop`

### Avantages de `htop` :
- Interface colorée et attrayante.
- Facilité de navigation avec les flèches.
- Affichage graphique de l'utilisation du CPU et de la mémoire.
- Actions rapides (tuer ou renicer un processus).
- Filtres et recherche avancés.

### Inconvénients de `htop` :
- Nécessite une installation manuelle.
- Légère consommation de ressources supplémentaires par rapport à `top`.

En résumé, `htop` est plus convivial et visuellement informatif, tandis que `top` est simple et toujours disponible.`` 
(source google)

**``# Exercice 2 :**

## Création des scripts

J'ai ouvert un éditeur de texte pour créer deux scripts.

1. **Script `date.sh` :**
   ``
   nano date.sh`` 

Contenu du script `date.sh` :


`while true; do
    sleep 1
    echo -n 'date '
    date +%T
done` 

2.  **Script `date-toto.sh` :**
    
    `nano date-toto.sh` 
    
    Contenu du script `date-toto.sh` :
  
    
    `#!/bin/sh
    
    while true; do
        sleep 1
        echo -n 'toto '
        date --date '5 hours ago' +%T
    done` 
    

Ensuite, j'ai rendu les deux scripts exécutables avec la commande :

`chmod +x date.sh date-toto.sh` 

## Exécution des scripts

1.  **Lancement du script `date.sh` :**
      
    `./date.sh &` 
    
    **Résultat :**
    
    `[1] 640
    root@serveur1:~# date
    date 14:24:57
    date 14:24:59
    date 14:25:00
    ...` 
    
    Le `&` permet de mettre le script en arrière-plan (source : Baptiste). La boucle tourne à l'infini, et j'ai dû utiliser `CTRL+Z` puis `CTRL+C` pour arrêter temporairement le script avant de me déconnecter.
    
2.  **Lancement du script `date-toto.sh` :**
       
    `./date-toto.sh &` 
    
    Résultat :
    
     
    `[1] 1242
    root@serveur1:~# toto 09:29:57
    toto 09:29:58
    ...` 
    
    J'ai rencontré le même problème que précédemment et l'ai résolu de la même manière (source : Baptiste).
    

## Gestion des processus

La commande `jobs` permet de voir les processus en arrière-plan :


`jobs` 

Résultat :


`[1] En cours d'exécution ./date-toto.sh &
[2] En cours d'exécution ./date-toto.sh &
[4] En cours d'exécution ./date.sh &` 

Execute les scripts en arriere plan

La commande `fg %1` ramène le premier script au premier plan et `fg %2` ramène le second script au premier plan (source : Baptiste).

### Utilisation de `ps` pour visualiser les processus

La commande `ps aux | grep date` permet d'afficher les processus liés aux scripts `date.sh` et `date-toto.sh` :


`ps aux | grep date` 

Résultat :


`root   640  0.0  0.0   2576   932 ? S  14:24  0:00 /bin/sh ./date.sh
root  1242  0.0  0.0   2576   868 ? S  14:29  0:00 /bin/sh ./date-toto.sh` 

### Arrêt des processus

J'ai utilisé la commande `kill` pour arrêter les processus en arrière plan :

`kill 640  # Pour arrêter le premier date.sh
kill 4731 # Pour arrêter le deuxième date.sh
kill 1242 # Pour arrêter le premier date-toto.sh
kill 2379 # Pour arrêter le deuxième date-toto.sh` 

## Utilisation de `man`

La commande `man` permet d'afficher la page de manuel d'une commande. Par exemple :

Pour date.sh

Le script date.sh exécute une boucle infinie qui affiche l'heure actuelle chaque seconde.



date - Afficher ou configurer la date et l'heure du système

SYNOPSIS

date [OPTION]... [+FORMAT]

date [-u|--utc|--universal] [MMJJhhmm[[CC]AA][.ss]]

`.
-   `man sleep` explique la fonction de `sleep`, qui suspend le script pendant 1 seconde.
-   `man echo` décrit la commande `echo`, utilisée pour afficher du texte.

# Exercice 3 : Les tubes

-   cat affiche le contenu des fichiers ou de l’entrée standard.
    

-   tee Lit l'entrée standard et envoie la sortie à la fois vers un ou plusieurs fichiers et vers la sortie standard (comme un "T", qui divise la sortie).
    

ls | cat  

Cela liste les fichiers dans le répertoire courant.

= root@serveur1:~# ls | cat

date.sh

date-toto.sh

leep

root@serveur1:~#

-   ls -l | cat > liste  
    

-   Cette commande liste les fichiers en détail (ls -l) puis redirige la sortie vers cat. cat ne fait qu'afficher la sortie, mais ici, la sortie de cat est redirigée vers un fichier nommé liste. Résultat : la sortie de ls -l sera écrite dans le fichier liste, remplaçant son contenu.
    

root@serveur1:~# ls -l | tee liste

total 100

-rwxr-xr-x 1 root root 66 11 oct. 14:20 date.sh

-rwxr-xr-x 1 root root 87 11 oct. 14:22 date-toto.sh

-rw-r--r-- 1 root root 82503 11 oct. 15:01 leep

-rw-r--r-- 1 root root 268 11 oct. 15:15 liste

-rw-r--r-- 1 root root 218 11 oct. 15:14 liste~

root@serveur1:~#

=> $ ls -l | tee liste

-   Cette commande liste les fichiers en détail (ls -l), et tee enregistre cette sortie dans le fichier liste tout en affichant la sortie à l'écran. Le contenu du fichier liste est remplacé.
    

# 5 Journal système `rsyslog`

``## Gestion des journaux avec `rsyslog`

### 1. Installation et Vérification de l'État de `rsyslog`

Pour vérifier le statut du service `rsyslog`, j'ai d'abord exécuté la commande suivante :

```bash
systemctl status rsyslog`` 

J'ai constaté que `rsyslog` n'était pas encore installé. Pour l'installer, j'ai utilisé la commande :


`apt install rsyslog` 

Après l'installation, j'ai relancé la commande pour vérifier à nouveau l'état de `rsyslog` :

`systemctl status rsyslog` 

La sortie indiquait que le service était actif, avec le statut suivant :


`Active: active (running)` 

De plus, le PID du démon était affiché comme suit :

`Main PID: 11253 (rsyslogd)` 

### 2. Consultation du Fichier de Configuration

Ensuite, j'ai ouvert le fichier de configuration de `rsyslog` pour examiner les réglages de journalisation :


`cat /etc/rsyslog.conf` 

Voici un extrait du contenu du fichier :


`# Log anything besides private authentication messages to a single log file
*.*;auth,authpriv.none          -/var/log/syslog

#Log commonly used facilities to their own log file
auth,authpriv.*                 /var/log/auth.log
cron.*                          -/var/log/cron.log
kern.*                          -/var/log/kern.log
mail.*                          -/var/log/mail.log
user.*                          -/var/log/user.log` 

#### Explication des Paramètres

Les messages standards, à l'exception de ceux liés à l'authentification, sont envoyés vers le fichier `/var/log/syslog`. Les messages d'authentification sont enregistrés dans le fichier `/var/log/auth.log`.

### 3. Contenu du Fichier de Log

Pour avoir un aperçu du contenu des journaux standards, j'ai exécuté la commande suivante :


`cat /var/log/syslog` 

Voici un extrait des logs :


`2024-10-11T15:24:25.571264+02:00 serveur1 kernel: [    0.000000] Linux version 6.1.0-25-amd64 (debian-kernel@lists.debian.org) (gcc-12 (Debian 12.2.0-14) 12.2.0, GNU ld (GNU Binutils for Debian) 2.40) #1 SMP PREEMPT_DYNAMIC Debian 6.1.106-3 (2024-08-26)
2024-10-11T15:24:25.571369+02:00 serveur1 kernel: [    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.1.0-25-amd64 root=UUID=ccb9bdc7-eef9-4b0f-ae7c-5c8b5384ee80 ro quiet` 

### 4. Gestion des Tâches Planifiées avec `cron`

Le service `cron` est un programme permettant l'exécution automatique de scripts, de commandes ou de logiciels à des moments précis ou selon un cycle défini à l'avance. Chaque utilisateur peut configurer ses propres tâches via un fichier `crontab`.


La commande `tail -f` affiche les nouvelles lignes ajoutées à un fichier en temps réel, ce qui permet de suivre les événements au fur et à mesure qu'ils se produisent.

### 6. Configuration de la Rotation des Journaux

J'ai ensuite examiné le fichier de configuration pour la rotation des journaux :


`cat /etc/logrotate.conf` 

Voici le contenu  :


`# see "man logrotate" for details

 global options do not affect preceding include directives
weekly
rotate 4
create
#dateext
#compress
include /etc/logrotate.d` 

Le fichier `/etc/logrotate.conf` gère la rotation des fichiers journaux en définissant la fréquence de rotation (hebdomadaire), le nombre de copies à conserver (4) et en permettant la création de nouveaux fichiers journaux après rotation. Cela aide à gérer l'espace disque et à organiser les journaux efficacement.

### 7. Détails sur le Processeur et le Réseau

La sortie de la commande `dmesg` a révélé des informations sur le processeur, indiquant qu'il s'agissait d'un processeur virtualisé, probablement sous VirtualBox :


`[    0.000008] tsc: Detected 1190.401 MHz processor` 

Pour obtenir des informations sur la configuration réseau, j'ai filtré les logs avec la commande suivante :


`dmesg | grep -i "network"` 

Voici la sortie de cette commande :


`[    1.528754] e1000: Intel(R) PRO/1000 Network Driver
[    2.592185] e1000 0000:00:03.0 eth0: Intel(R) PRO/1000 Network Connection` 

-   **Pilote de réseau :** Intel(R) PRO/1000 Network Driver
-   **Carte réseau :** Intel(R) PRO/1000 Network Connection associée à l'interface `eth0`.

# Conclusion

Le TP était intéressant mais un peu long et complexe. J'ai dû effectuer de nombreuses recherches et j'ai perdu beaucoup de temps sur la première étape à cause d'un problème de connexion lié à une mauvaise manipulation de ma clé publique.
