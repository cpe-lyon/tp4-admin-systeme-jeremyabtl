##### Jérémy ABITBOL - ICS
# TP 4 - Utilisateurs, groupes et permissions

## Exercice 1. Gestion des utilisateurs et des groupes 

### *Commencez par créer deux groupes groupe1 et groupe2*

Pour créer les groupes : groupe1 et groupe2 il faut faire les commandes suivantes :
> sudo addgroup groupe1

>sudo addgroup groupe2

### *Créez ensuite 4 utilisateurs u1, u2, u3, u4 avec la commande useradd, en demandant la création de leur dossier personnel et avec bash pour shell*

>sudo useradd -m u1

>sudo useradd -m u2

>sudo useradd -m u3

>sudo useradd -m u4

### *Placez les utilisateurs dans les groupes :*
- u1, u2, u4 dans groupe1 
- u2, u3, u4 dans groupe2 

Pour placer u1, u2, u4 dans groupe1 il faut faire les instructions suivantes :

> sudo usermod -a -G groupe1 u1

> sudo usermod -a -G groupe1 u2

> sudo usermod -a -G groupe1 u4

Pour placer u2, u3, u4 dans groupe2 il faut faire les instructions suivantes :

> sudo usermod -a -G groupe2 u2

> sudo usermod -a -G groupe2 u3

> sudo usermod -a -G groupe2 u4

### *Donnez deux moyens d’aﬀicher les membres de groupe2*

>cat /etc/group | grep "groupe2"

Donne le résultat suivant : **groupe2:x:1002:u2,u3,u4**

>sudo apt install members

>members groupe2

Donne le résultat suivant : **u2 u3 u4**

### *Faites de groupe1 le groupe propriétaire de /home/u1 et/home/u2 :* 
>sudo chgrp groupe1 /home/u1

>sudo chgrp groupe1 /home/u2
### *Faites de groupe2 le groupe propriétaire de /home/u3 et /home/u4*
>sudo chgrp groupe2 /home/u3

>sudo chgrp groupe2 /home/u4

### *Remplacez le groupe primaire des utilisateurs :* 
- groupe1 pour u1 et u2 

>sudo usermod -g groupe1 u1

>sudo usermod -g groupe1 u2

- groupe2 pour u3 et u4 

>sudo usermod -g groupe2 u3

>sudo usermod -g groupe2 u4

### *Créez deux répertoires /home/groupe1 et /home/groupe2 pour le contenu commun aux groupes :* 

On change de répertoire courant avec la commande :
>cd /home

et on créer les 2 fichiers groupe1 et groupe2 :
>sudo mkdir groupe1

>sudo mkdir groupe2

### *Mettre en place les permissions permettant aux membres de chaque groupe d’écrire dans le dossier associé*

>sudo chmod 770 groupe1

>sudo chmod 770 groupe2

### *Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer ou supprimer ce fichier?*

Il faut mettre un sticky bit avec l'instruction suivante :

>sudo chmod 1770 groupe1

Il faut faire la commande suivante pour connaitre les permissions sur le dossier :

>stat -c %a groupe1

On obtient le résultat suivant : **1770**

>sudo chmod 1770 groupe2

### *Pouvez-vous vous connecter en tant que u1? Pourquoi?*

La commande :

>su u1

permet de prendre l'identité de u1. Mais vu que u1 ne possède pas de mot de passe il est impossible de se connecter en tant que u1.

### *Activez le compte de l’utilisateur u1 et vérifiez que vous pouvez désormais vous connecter avec son compte.*

>sudo passwd u1

on demande a saisir un nouveau mot de passe

Je saisi u1 comme mot de passe pour l'utilisateur u1.

>su u1

Puis saisir le mot de passe. Le compte est activité et on peut donc se connetcer.

### *Quels sont l’uid et le gid de u1 ?*

>id u1

### *Quel utilisateur a pour uid 1003?*

>getent group groupe1 1003

On obtient le résultat suivant : **groupe1:x:1001:u1,u2,u4
u1:x:1003:**
Donc u1 correspond à l'UID.

### *Quel est l’id du groupe groupe1?*

>getent group groupe1

On obtient le résultat suivant : **groupe1:x:1001:u1,u2,u4**

### *Quel groupe a pour guid 1002? (Rien n’empêche d’avoir un groupe dont le nom serait 1002...)*

>getent group 1002

On obtient le résultat suivant : **groupe2:x:1002:u2,u3,u4**

### *Retirez l’utilisateur u3 du groupe groupe2. Que se passe-t-il? Expliquez.*

Pour retirer l'utilisateur il faut faire la commande :

>sudo gpasswd -d u3 groupe2

Cela ne marche pas car lorsque l'on fait **id u3** on peut voir que l'utilisateur u3 est toujours présent dans le groupe 2 car c'est son unique groupe et un utilisateur doit au moins appartenir à un groupe.

### *Modifiez le compte de u4 de sorte que :* 
- il expire au 1er juin 2020 
>sudo chage --expiredate 2020-06-01 u4
- il faut changer de mot de passe avant 90 jours 
>sudo chage -M 90 u4
- il faut attendre 5 jours pour modifier un mot de passe 
- l’utilisateur est averti 14 jours avant l’expiration de son mot de passe 
- le compte sera bloqué 30 jours après expiration du mot de passe


