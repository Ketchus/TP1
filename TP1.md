Compte rendu TP1

1.2 Téléchargement de l'installeur (mini.iso) : https://ftp.lip6.fr/pub/linux/distributions/debian/dists/Debian12.2/main/installer-amd64/current/images/netboot/

2.1 configuration du SSH
- installation du ssh, commande utilisée : apt install openssh-server
Remarque: j'ai pris un peu de temps à trouver la bonne commande car je mettais "sudo" devant le reste de la commande ce qui ne marchait évidemment pas.

-configuration du ssh, commande utilisée : /etc/ssh/sshd_config
Remarque: j'ai eu du mal à faire l'installation du ssh car je rentrais la commande dans le mauvais serveur donc je n'avait pas l'autorisation de configuerer le ssh 
- pour permettre la connexion en tant que root modifier ou ajouter en dessous de la ligne : "#PermitRootLogin prohibit-password" en "PermitRootLogin yes"
- se deconnecter et relancer la machine 

2.2 connection
- lancer la machine virtuelle
- rentrer dans les lignes de commandes: Vbox LicencePro2023
- se connecter grace à son mot de passe Virtualbox

2.3 Nombre de paquets
J'ai 329 paquets.
Pour v´erifier j'ai utiliser la commande : dpkg -l | wc -l

2.4 Space Usage
La racine / représente moins de 1 Go d’espace utilisé.
j'ai vérifié avec la comande : df -h

2.5 a indiquer dans le rendu et expliquer les commandes et le resultat obtenu
- la commande : echo $LANG permet de regarder dans quel langue on est et ça renvoie en_FR.UTF-8
- quand on rentre hostname affiche le nom du système hôte en cours
- pour trouver le nom de domaine de la machine en cours on utiliser la commande: hostnamectl ça affiches les caractéristiques de l'hote entre autre: nom de l'hote, icon name, machine ID, BOOT ID et c'est dans le Kernel qu'on retrouve le nom de domaine)
- verification emplacement depots : cat /etc/apt/sources.list | grep -v -E ’^#|^$’ la bonne commande est cat /etc/apt/sources.list | grep -v -E '^#|^$' et cette commande permet d'afficher les lignes actives du fichier /etc/apt/sources.list, qui est un fichier de configuration utilisé par le gestionnaire de paquets APT pour déterminer d'où télécharger les paquets logiciels.
- passwd/shadow : cat /etc/shadow | grep -vE ’:\*:|:!\*:’ la bonne commande est cat /etc/shadow | grep -vE ':\*:|:!\*:' elle permet d'afficher les lignes du fichier /etc/shadow en excluant les lignes correspondant à des comptes d'utilisateurs ayant un mot de passe désactivé.
- compte utilisateurs : cat /etc/passwd | grep -vE ’nologin|sync’ la bonne commande est cat /etc/passwd | grep -vE 'nologin|sync'permet d'afficher les lignes du fichier /etc/passwd en excluant les lignes correspondant aux comptes utilisateurs "nologin" et "sync" et ça affiche : root:x:0:0:root:/root:/bon:bash
- expliquer le retour des commandes :
_ fdisk -l /dev/sda: elle liste les partitions de disque sur le système ça affiche les périphériques, Début, Fin , secteur, taille, type
_ fdisk -x /dev/sda: elle liste les partitions de disque sur le système avec la différence que ça affiche les périphériques, Début, Nom attribut, Fin , secteur, type-UUID ET UUID en gros c'est un affichage plus détaillé
_ df -h :  est utilisée pour afficher la manière dont l'espace disque est utilisé dans les différentes parties du système de fichiers, y compris les partitions de disque 

3.1 installation automatique, preseed :est un fichier texte qui s'installe en meme temps que le mini.iso et contient toutes les réponses aux questions posées lors de l'instalation de le Virtualbox, il permet de définir les paramètres d'installation automatiquement.

3.2 rescue mode
Pour changer de mot de passe en cas d'oublie:
méthode1: (source: https://community.ovh.com/t/resolu-persister-le-password-root-et-debian-en-mode-rescue/29109/3)
- enter la commande : lsblk
- puis la commande :  mount /dev/sdb1 /mnt
- ensuite la commande : chroot /mnt
- après la commande :  passwd
- on demande de rentrer le nouveau mot de passe le rentrer
- réécrire le nouveau mot de passe
- enfin rentrer: exit

méthode1: (source: https://forum-debian.fr/wiki/Modifier,_r%C3%A9cup%C3%A9rer,_son_mot_de_passe_root,_en_cas_de_perte)
- Démarrage du PC via l’Interrupteur électrique O/I
- Dans le Menu GRUB avant la fin du compte à rebours taper "e"
- Ouvre l’éditeur d’option de démarrage
- Se placer sur la ligne qui débute par "linux" et se rendre à la fin de cette ligne
- Changer le  ro = lecture seule (read only) en rw (read and write) 
- Se placer à la fin de la ligne après “quiet” faire un espace suivi de init=/bin/sh ( ou init=/bin/bash ) on obtiens une ligne se terminant comme cela :  linux ... rw quiet init=/bin/sh(bash)
- puis faire [Ctrl+x] ( les 2 touches en même temps ) ça ouvre une console en root (sans demande de MdP)
- Taper directement la commande: passwd puis rentrer le nouveau mdp 2 fois
- Pour fermer la console et basculer vers le mode graphiqe faire :exec /sbin/init

3.3 redimentionnement partition (source: https://debian-facile.org/atelier:chantier:redimensionner-la-partition-racine-a-chaud-sans-la-demonter)
Pour redimmentionner ma partition racine sans reinstaller:
- faire: fdisk -l /dev/sda 
- faire la commande p pour lister les partitions et vérifier le numéro de la partition racine.
- puis la commande d pour supprimer la partition racine
- ensuite recréez la partition racine en utilisant la commande n avec le même numéro de partition, puis suivez les instructions pour spécifier la nouvelle taille.
- enfin enregistrez les modifications en utilisant la commande w.
- forcer un fsck sur la prochaine réinitialisation pour s'assurer que la partition est saine avant son montage: touch /forcefsck puis rentrer init 6
- après le redémarrage on informe le système de fichier de ce changement de taille: resize2fs /dev/sda1
- Puis un dernier redémarrage et on teste pour voir si ça a marché avec : df -h

Remarque finale : au debut c'etait un peu complique de faire les paramétratges d'installation après cet étape passé, c'est devenu relativement simple. Le seul problème était sue je n'étais pas connecté en tant que root et je n'utilisais pas le bon serveur et des fois y avait des petites erreur de syntaxe sinon tout fonctionnait bien.
