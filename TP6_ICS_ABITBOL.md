#### Jérémy ABITBOL

# TP 6 - Gestion des disques / Tâches d’administration

## Exercice 1. Disques et partitions

### *Vérifiez que ce nouveau disque dur est bien détecté par le système*

Avec les lignes de commandes suivantes on peut voir les disques durs présent sur la machine :
>ll /dev/sd*

>sudo fdisk -l

>lsblk

<pre>
Disk /dev/sda: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: AFE3D5C5-A080-4ACE-9B30-ABA03F59FC89

Device     Start      End  Sectors Size Type
/dev/sda1   2048     4095     2048   1M BIOS boot
/dev/sda2   4096 20969471 20965376  10G Linux filesystem


Disk /dev/sdb: 5 GiB, 5368709120 bytes, 10485760 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
</pre>

### *Partitionnez ce disque en utilisant fdisk : créez une première partition de 2 Go de type Linux (n°83), et une seconde partition de 3 Go en NTFS (n°7)*

>fdisk /dev/sdb

Faire m pour avoir l'ensemble des commandes :

<pre>
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-10485759, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-10485759, default 10485759): +2G

Created a new partition 1 of type 'Linux' and of size 2 GiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2):
First sector (4196352-10485759, default 4196352):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-10485759, default 10485759):

Created a new partition 2 of type 'Linux' and of size 3 GiB.

Command (m for help): tt
Partition number (1,2, default 2): 1
Hex code (type L to list all codes): 883^H
Hex code (type L to list all codes): 83

Changed type of partition 'Linux' to 'Linux'.

Command (m for help): t
Partition number (1,2, default 2): 2
Hex code (type L to list all codes): 7

Changed type of partition 'Linux' to 'HPFS/NTFS/exFAT'.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
</pre>

### *A ce stade, les partitions ont été créées, mais elles n’ont pas été formatées avec leur système de fichiers. A l’aide de la commande mkfs, formatez vos deux partitions (pensez à consulter le manuel !)*

>mkfs.ext4 /dev/sdb1

>mkfs.ntfs /dev/sdb2

### *Pourquoi la commande df -T, qui affiche le type de système de fichier des partitions, ne fonctionne-t-elle pas sur notre disque ?*

On ne peut pas les afficher car on n'a pas monté le système de fichier.

### *Faites en sorte que les deux partitions créées soient montées automatiquement au démarrage de la machine, respectivement dans les points de montage /data et /win (vous pourrez vous passer des UUID en raison de l’impossibilité d’effectuer des copier-coller)*

Il faut créer deux dossier :
>sudo mkdir /data

>sudo mkdir /win

Il faut ensuite aller dans le fichier /etc/fstab et écrire les 2 dernières lignes :
<pre>
UUID=861369eb-f58d-4f63-aa0d-34ca26734603 / ext4 defaults 0 0
/swap.img       none    swap    sw      0       0
/dev/sdb1 /data ext3 defaults 0 0
/dev/sdb2 /win ntfs defaults 0 0
</pre>

Ensuite il faut faire la ligne de commande :
>sudo mount -a

Pour que les changements soient pris en compte.

## Exercice 2. Partitionnement LVM

### *1. On va réutiliser le disque de 5 Gio de l’exercice précédent. Commencez par démonter les systèmes de fichiers montés dans /data et /win s’ils sont encore montés, et supprimez les lignes correspondantes du fichier /etc/fstab*

>sudo umount /data

>sudo umount /win

On a supprimé les lignes correspodnants du fichier /etc/fstab et il ne reste plus que ça qui était initialement présent.
<pre>
UUID=861369eb-f58d-4f63-aa0d-34ca26734603 / ext4 defaults 0 0
/swap.img       none    swap    sw      0       0
</pre>

### *2. Supprimez les deux partitions du disque, et créez une patition unique de type LVM*
*La création d’une partition LVM n’est pas indispensable, mais vivement recommandée quand
on utilise LVM sur un disque entier. En effet, elle permet d’indiquer à d’autres OS ou logiciels de
gestion de disques (qui ne reconnaissent pas forcément le format LVM) qu’il y a des données sur ce disque.*

**Attention à ne pas supprimer la partition système !**

Il faut exécuter la commande suivante :
>sudo fdisk /dev/sdb

puis taper **d** et sélectionner la partition 1. Il faut exactement faire pareil pour la partition 2 qui sera elle sélectionner par défaut. Enfin il faut enregistrer avec **w**.

Voici ce qu'il s'est affiché sur mon écran :

<pre>

Welcome to fdisk (util-linux 2.33.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): d
Partition number (1,2, default 2): 1

Partition 1 has been deleted.

Command (m for help): d
Selected partition 2
Partition 2 has been deleted.
</pre>

J'ai ensuite créer un nouvel partition de type LVM en faisant :
>sudo fdisk /dev/sdb

J'ai appuyé sur **n** pour créer une nouvel partition puis sur entrée pour valider les paramètres par défaut. Enfin j'ai appuyé sur **t** pour séléctionner le type de la partition. En appuyant sur **l** j'ai pu voir tous les types de partitions et j'ai donc rentré le code **8e** pour créer une partition de type Linux LVM.

<pre>
Selected partition 1
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
</pre>

### *3. A l’aide de la commande pvcreate, créez un volume physique LVM. Validez qu’il est bien créé, en utilisant la commande pvdisplay.*

*Toutes les commandes concernant les volumes physiques commencent par pv. Celles concernant
les groupes de volumes commencent par vg, et celles concernant les volumes logiques par lv.*

Création d'un nouvel partition avec la commande sous root :
>pvcreate /dev/sdb1

<pre>
root@serveur:/home/abitbol# pvcreate /dev/sdb1
WARNING: ext3 signature detected on /dev/sdb1 at offset 1080. Wipe it? [y/n]: y
  Wiping ext3 signature on /dev/sdb1.
  Physical volume "/dev/sdb1" successfully created.
</pre>

Le volume physique a bien été validé on peut le vérifier avec la commande suivante en étant root :
>pvdisplay

<pre>
root@serveur:/home/abitbol# pvdisplay
  "/dev/sdb1" is a new physical volume of "<5,00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name
  PV Size               <5,00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               CxUpOA-dHm5-OYiG-uq1F-B0LS-TdyK-kQwWNB
</pre>

### *4. A l’aide de la commande vgcreate, créez un groupe de volumes, qui pour l’instant ne contiendra que le volume physique créé à l’étape précédente. Vérifiez à l’aide de la commande vgdisplay.*

*Par convention, on nomme généralement les groupes de volumes vgxx (où xx représente l’indice du groupe de volume, en commençant par 00, puis 01...)*

Création du groupe de volume portant le nom *VolumeGroupExo2Qu4* avec la commande suivante:
>vgcreate VolumeGroupExo2Qu4 /dev/sdb1

Voici le résultat :
<pre>
root@serveur:/home/abitbol# vgcreate VolumeGroupExo2Qu4 /dev/sdb1
  Volume group "VolumeGroupExo2Qu4" successfully created
</pre>

Vérification avec la commande :
>vgdisplay

<pre>
root@serveur:/home/abitbol# vgdisplay
  --- Volume group ---
  VG Name               VolumeGroupExo2Qu4
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <5,00 GiB
  PE Size               4,00 MiB
  Total PE              1279
  Alloc PE / Size       0 / 0
  Free  PE / Size       1279 / <5,00 GiB
  VG UUID               F73PzT-RYR0-VoGA-sPTN-Gz9F-R0X9-d0boYz
</pre>

### *5. Créez un volume logique appelé lvData occupant l’intégralité de l’espace disque disponible.*

*On peut renseigner la taille d’un volume logique soit de manière absolue avec l’option -L (par exemple -L 10G pour créer un volume de 10 Gio), soit de manière relative avec l’option -l : -l 60%VG pour utiliser 60% de l’espace total du groupe de volumes, ou encore -l 100%FREE pour utiliser la totalité de l’espace libre.*

Création d'un volume logique occupant l'intégralité de l'espace disque disponible avec la commande suivante suivante :
>lvcreate -n lvData -l  100%FREE VolumeGroupExo2Qu4

Voici le résultat :
<pre>
root@serveur:/home/abitbol# lvcreate -n lvData -l  100%FREE VolumeGroupExo2Qu4
  Logical volume "lvData" created.
</pre>

On vérifie la création avec la commande :
>vldisplay

<pre>
root@serveur:/home/abitbol# lvdisplay
  --- Logical volume ---
  LV Path                /dev/VolumeGroupExo2Qu4/lvData
  LV Name                lvData
  VG Name                VolumeGroupExo2Qu4
  LV UUID                nwyxKF-m7oy-mh7n-Mz9Q-iuph-wTPq-NmwRUv
  LV Write Access        read/write
  LV Creation host, time serveur, 2019-10-20 12:37:24 +0000
  LV Status              available
  # open                 0
  LV Size                <5,00 GiB
  Current LE             1279
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
</pre>

### *6. Dans ce volume logique, créez une partition que vous formaterez en ext4, puis procédez comme dans l’exercice 1 pour qu’elle soit montée automatiquement, au démarrage de la machine, dans /data.*

*A ce stade, l’utilité de LVM peut paraître limitée. Il trouve tout son intérêt quand on veut par exemple agrandir une partition à l’aide d’un nouveau disque.*

Création d'une partition ext4 :

>mkfs.ext4 /dev/VolumeGroupExo2Qu4/lvData

<pre>
root@serveur:/home/abitbol# mkfs.ext4 /dev/VolumeGroupExo2Qu4/lvData
mke2fs 1.44.6 (5-Mar-2019)
Creating filesystem with 1309696 4k blocks and 327680 inodes
Filesystem UUID: 5edc87e8-cef1-429a-bf3d-093491297ebc
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
</pre>

### Modification du ficher /etc/fstab et ajout d ela ligne suivante :
>/dev/mapper/VolumeGroupExo2Qu4-lvData /data ext4 defaults 0 0

ou

>UUID: 5edc87e8-cef1-429a-bf3d-093491297ebc /ext4 defaults 0 0

### On monte ensuite le volume :
>mount /dev/VolumeGroupExo2Qu4/lvData /data


### *7. Eteignez la VM pour ajouter un second disque (peu importe la taille pour cet exercice). Redémarrez la VM, vérifiez que le disque est bien présent. Puis, répétez les questions 2 et 3 sur ce nouveau disque.*

Mon ssh ne fonctionne plus du coup il n'y aura plus de copier coller. 

J'ai ajouté un nouveau disque dur sur virtual box. Le nouveau disque ce nomme **sdc**.

### *8. Utilisez la commande vgextend <nom_vg> <nom_pv> pour ajouter le nouveau disque au groupe de volumes*

La commande suivante :
>vgextend VolumeGroupExo2Qu4 /dev/sdc

permet d'étendre le contenu au nouveau disque.

### *Utilisez la commande lvresize (ou lvextend) pour agrandir le volume logique. Enfin, il ne faut pas oublier de redimensionner le système de fichiers à l’aide de la commande resize2fs.*

*Il est possible d’aller beaucoup plus loin avec LVM, par exemple en créant des volumes par bandes (l’équivalent du RAID 0) ou du mirroring (RAID 1). Le but de cet exercice n’était que de présenter les fonctionnalités de base.*

>lvresize -L +5G /dev/VolumeGroupTest/lvData

Dans ce cas j'ai agrandi le volume logique de 5Gb.

