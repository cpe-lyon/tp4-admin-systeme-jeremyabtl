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
>sudo chage -m 5 u4
- l’utilisateur est averti 14 jours avant l’expiration de son mot de passe 
>sudo chage -W 14 u4
- le compte sera bloqué 30 jours après expiration du mot de passe
>sudo chage -I 30 u4

Voici le résultat :
<pre>
20:37-abitbol@serveur:~$ sudo chage -l u4

Last password change                                    : sept. 30, 2019
Password expires                                        : déc. 29, 2019
Password inactive                                       : janv. 28, 2020
Account expires                                         : juin 01, 2020
Minimum number of days between password change          : 5
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 14
</pre>

### *Quel est l’interpréteur de commandes (Shell) de l’utilisateur root?*

Il faut exécuter la commande suivante :
>getent passwd root

On obtient ce résultat : **root:x:0:0:root:/root:/bin/bash**

### *A quoi correspond l’utilisateur nobody ?*

L'utilisateur nobody est un "non-privileged user". Comme le non l'indique il n'a aucun droit ( si on regardes dans /etc/shadow il n'y a pas de mot de passe), mais néanmoins il joue un grand rôle pour le lancement de certains service.

### *Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire?*

Après avoir exécuté la commande sudo la première fois, le mot de passe durera 15 minutes par défaut, vous n’avez donc pas besoin de saisir un mot de passe pour chaque commande sudo.

### *Quelle commande permet de forcer sudo à oublier votre mot de passe?*

En faisant la commande : **man sudo** on a accès au manuel concernant sudo et on peut voir ceci :
<pre>
 -K, --remove-timestamp
                 Similar to the -k option, except that it removes the user's cached credentials entirely and may not
                 be used in conjunction with a command or other option.  This option does not require a password.
                 Not all security policies support credential caching.
        
</pre>

Il faut donc exécuter la commande suivante pour forcer sudo à oublier notre mot de passe :
>sudo -K

## Exercice 2. Gestion des permissions 

### *Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques lignes de texte. Quels sont les droits sur test et fichier?*


Pour connaître les droits sur test il faut faire :
>stat -c %a test

On obtient le résultat suivant : 775

Pour connaître les droits sur fichier il faut faire :
>stat -c %a fichier

On obtient le résultat suivant : 664

### *Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’aﬀicher en tant que root. Conclusion?*

Pour enlever tous les droits il faut saisir l'instruction suivante :
>sudo chmod a-rwx fichier

Lorsque l'on fait :
<pre>
10:39-abitbol@serveur:~/test$ cat fichier
</pre>

On obtient le résultat suivant : **cat: fichier: Permission denied**

Par contre lorsque l'on fait :
<pre>
10:40-abitbol@serveur:~/test$ sudo su
root@serveur:/home/abitbol/test# cat fichier
hello world !
</pre>

On peut donc voir que l'on n'a plus accès au fichier en tant qu'utilisateur abitbol par exemple mais lorsque l'on fait **sudo su** et que l'on se retrouve en mode root il est aller possible de lire le fichier.

### *Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un fichier s’il existe déjà. Que peut-on dire au sujet des droits?*

Pour me redonner les droits en écriture et exécution j'utilise la commande suivante :
>chmod 300 fichier

Lorsque l'on fait la commande :
> cat fichier 

on obtient le résultat suivant : **cat: fichier: Permission denied** indiquant que nous ne possédons pas les droit pour lire le fichier ce qui est logique car nous ne possédons que les droits d'écriture et d'exécution.

### *Essayez d’exécuter le fichier. Est-ce que cela fonctionne? Et avec sudo? Expliquez.*

Avec l'instruction suivante :
<pre>
10:50-abitbol@serveur:~/test$ ./fichier
bash: ./fichier: Permission denied
</pre>

On peut voir que il est impossible d'exécuter le fichier puisque je n'ai pas les droits de lecture. Même si j'ai les droits d'exécution, exécuter un fichier que je ne peux pas lire est stupide et donc impossible.

Avec sudo cela fonctionne car l'admin à toujours les droits.

<pre>
10:54-abitbol@serveur:~/test$ sudo ./fichier
Hello
</pre>

### *Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le contenu du répertoire, puis exécutez ou aﬀichez le contenu du fichier fichier. Qu’en déduisez-vous? Rétablissez le droit en lecture sur test*

Pour retirer le droit de lecture à l'utilisateur :

>chmod u-r ../test

<pre>
11:06-abitbol@serveur:~/test$ ls
ls: cannot open directory '.': Permission denied
11:07-abitbol@serveur:~/test$ cat fichier
cat: fichier: Permission denied
</pre>

Sans droit de lecture il est impossible d'effectuer de commande **ls** pour voir le contenu du répertoire et il est impossible de faire un **cat fichier** pour lire le contenu du fichier fichier.

On rétabli le droit de lecture en faisant :
>chmod u+r ../test

ce qui permet l'exécution de la commande **ls** et donc de voir le contenu du répertoire.

### *Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit en écriture au répertoire test.Tentez de modifier le fichier nouveau,puis de le supprimer.Que pouvez vous déduire de toutes ces manipulations?*

<pre>
11:10-abitbol@serveur:~/test$ touch nouveau
11:17-abitbol@serveur:~/test$ ls
fichier  nouveau
11:18-abitbol@serveur:~/test$ mkdir sstest
11:18-abitbol@serveur:~/test$ ls
fichier  nouveau  sstest
11:18-abitbol@serveur:~/test$ chmod u-w nouveau
11:19-abitbol@serveur:~/test$ chmod u-w ../test
</pre>

Il est impossible de modifier le **fichier nouveau** lorsque l'on se trouve dans nano il y a le message suivant :
*  *[ File 'nouveau' is unwritable ]*

Après avoir rétabli le droit en écriture du répertoire test il est toujours impossible de modifier le fichier **nouveau**.

Lorsque l'on souhaite supprimer le fichier **nouveau**  le message suivant apparait :
* *rm: remove write-protected regular empty file 'nouveau'?*

Il est donc impossible de supprimer le fichier.

Pour conclure :
Par exemple lorsque dossier n'a pas de droit de lecture il sera impossbile de lire le contenu du dossier ni le contenu des fichiers présent dans le dossier. Par contre lorsqu'un fichier n'a pas le droit de lecture mais est situé dans un dossier ou l'on possède le droit de lecture il est possible de lire le contenu du dossier mais il est impossible de lire le contenu du fichier pour lequel nousn ne possèdons pas le droit de lecture.

### *Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test. Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en lister le contenu, etc…Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires?*

Pour retirer le droit en exécution du répertoire test il faut faire la commande suivante :
>chmod u-x test

Comme le montre le résultat suivant :
<pre>
11:30-abitbol@serveur:~$ cd test
-bash: cd: test: Permission denied
</pre>

Plus aucune commande de manipulation ne fonctionne. Retirer le droit d'exécution n'autorise plus l'utilisateur a créer, supprimer, modifier, se déplacer dans le répertoire.

### *Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire test, de vous déplacer dans ssrep, de lister son contenu. Qu’en concluez-vous quant à l’influence des droits que l’on possède sur le répertoire courant? Peut-on retourner dans le répertoire parent avec ”cd ..”? Pouvez-vous donner une explication?*

Pour rétablir le droit en exécution du répertoire test il faut faire :
>chmod u+x test

Pour retirez à nouveau le droit en exécution il faut faire :
>chmod u+x ../test

<pre>
11:35-abitbol@serveur:~$ cd test
11:36-abitbol@serveur:~/test$ ls
fichier  nouveau  sstest
11:36-abitbol@serveur:~/test$ cd sstest
11:36-abitbol@serveur:~/test/sstest$ cd ..
11:36-abitbol@serveur:~/test$ cat nouveau
11:36-abitbol@serveur:~/test$ cat fichier
cat: fichier: Permission denied
11:36-abitbol@serveur:~/test$ touch bonjour
11:37-abitbol@serveur:~/test$ mkdir bonjour
mkdir: cannot create directory ‘bonjour’: File exists
11:37-abitbol@serveur:~/test$ mkdir yoyototo
11:37-abitbol@serveur:~/test$ ls
bonjour  fichier  nouveau  sstest  yoyototo
</pre>

Tous les fils d'un répertoire courant hérite de ses droits. Il est possible de retourner dans le répertoire parent avec la commande **cd ..** car si l'on peut se déplacer dans les fichiers parents il est aussi possible de revenir dans ceux-ci.

### *Rétablissez le droit en exécution du répertoire test. Attribuez au fichier fichier les droits suﬀisants pour qu’une autre personne de votre groupe puisse y accéder en lecture, mais pas en écriture.*

Pour attribuez au fichier fichier les droits suﬀisants pour qu’une autre personne de votre groupe puisse y accéder en lecture, mais pas en écriture il faut faire :
>chmod 750 fichier

### *Définissez un umask très restrictif qui interdit à quiconque à part vous l’accès en lecture ou en écriture, ainsi que la traversée de vos répertoires. Testez sur un nouveau fichier et un nouveau répertoire.*

>umask 077

<pre>
11:59-abitbol@serveur:~/testumask$ ll
total 12
drwxrwxr-x  3 abitbol abitbol 4096 oct.   2 11:59 ./
drwxr-xr-x 11 abitbol abitbol 4096 oct.   2 11:52 ../
-rw-------  1 abitbol abitbol    0 oct.   2 11:59 test
drwx------  2 abitbol abitbol 4096 oct.   2 11:59 test2/
-rw-rw-r--  1 abitbol abitbol    0 oct.   2 11:52 umask
</pre>

On peut bien voir que l'user à tout les droits mais que les groupes et les autres ont aucuns droits.

<pre>
12:02-abitbol@serveur:~/testumask$ umask -S
u=rwx,g=,o=
</pre>

### *Définissez un umask très permissif qui autorise tout le monde à lire vos fichiers et traverser vos répertoires, mais n’autorise que vous à écrire. Testez sur un nouveau fichier et un nouveau répertoire.*


<pre>
12:02-abitbol@serveur:~/testumask$ umask 022
12:05-abitbol@serveur:~/testumask$ umask -S
u=rwx,g=rx,o=rx
</pre>

<pre>
12:05-abitbol@serveur:~/testumask$ mkdir testpermisif
12:06-abitbol@serveur:~/testumask$ touch testbis
12:06-abitbol@serveur:~/testumask$ stat -c %a testpermisif
755
12:07-abitbol@serveur:~/testumask$ stat -c %a testbis
644
</pre>

### *Définissez un umask équilibré qui vous autorise un accès complet et autorise un accès en lecture aux membres de votre groupe. Testez sur un nouveau fichier et un nouveau répertoire.*

<pre>
12:07-abitbol@serveur:~/testumask$ umask 037
12:10-abitbol@serveur:~/testumask$ umask -S
u=rwx,g=r,o=
12:10-abitbol@serveur:~/testumask$ mkdir testequilibre
12:10-abitbol@serveur:~/testumask$ touch testequi
12:12-abitbol@serveur:~/testumask$ stat -c %a testequi
640
12:13-abitbol@serveur:~/testumask$ stat -c %a testequilibre
740
</pre>

### *Transcrivez les commandes suivantes de la notation classique à la notation octale ou vice-versa (vous pourrez vous aider de la commande stat pour valider vos réponses) :*
 - chmod u=rx,g=wx,o=r fic 

 **chmod 534 fic**
 - chmod uo+w,g-rx fic en sachant que les droits initiaux de fic sont r--r-x--
 **chmod 602 fic**
- chmod 653 fic en sachant que les droits initiaux de fic sont 711
**chmod u-x,g+r,o+w fic**
- chmod u+x,g=w,o-r fic en sachant que les droits initiaux de fic sont r--r-x---
**chmod 520 fic**

### *Aﬀichez les droits sur le programme passwd. Que remarquez-vous? En aﬀichant les droits du fichier /etc/passwd, pouvez-vous justifier les permissions sur le programme passwd?*

<pre>
12:13-abitbol@serveur:~/testumask$ ls -l /usr/bin | grep "passwd"
-rwsr-xr-x 1 root   root       84016 mars  22  2019 gpasswd
-rwxr-xr-x 1 root   root      249904 mars  11  2019 grub-mkpasswd-pbkdf2
-rwsr-xr-x 1 root   root       63736 mars  22  2019 passwd
</pre>

Cela signifie que tout le monde peut écrire et exécuter avec l'uid de root mais seul root peut lire le fichier.

<pre>
12:29-abitbol@serveur:~$ stat -c %a /etc/passwd
644
</pre>

Notre réponse précédente est confirmer car cette commande porouve que seul le root peut lire le fichier mais tous les utilisateurs peuvent écrire leurs mots de passes dans le fichier passwd.