

````markdown
# TP 01 – Installation de serveurs

Licence professionnelle Développement Web et Mobile  
Lucie Castelli

---

## Installation d’une machine virtuelle

Tout d’abord, de ce que j’ai compris, l’objectif de ce TP était de procéder à l’installation d’un serveur Linux en utilisant **VirtualBox** avec la distribution **Debian stable**.  
La machine virtuelle créée utilise un processeur de type **AMD64**.

Nous sommes donc allés sur un site miroir de Debian pour récupérer l’image d’installation.  
J’ai utilisé **Debian stable (Trixie – version 13)**, conformément aux consignes.

L’installation s’est faite via l’image **mini.iso**, ce qui permet de télécharger les paquets directement en ligne.

J’ai récupéré l’image sur un miroir Debian à l’adresse suivante :  
http://ftp.fr.debian.org/debian/dists/trixie/main/installer-amd64/current/images/netboot/

La version stable courante de Debian était **Trixie (13)**.  
Cette version étant la nouvelle branche stable au moment de l’installation, je l’ai donc utilisée à la place de **Bookworm**.

Au début, j’ai eu du mal à trouver l’image car je n’avais pas compris dans la consigne qu’il fallait remplacer certains mots dans l’URL.  
Après réflexion, j’ai compris comment accéder au bon lien.

---

## Création et installation de la machine virtuelle

Par la suite, j’ai ouvert **VirtualBox** et créé une nouvelle machine virtuelle dans laquelle j’ai sélectionné l’image **mini.iso**, puis j’ai démarré la machine.

J’ai ensuite suivi les différentes étapes d’installation (langue, configuration du clavier, etc.).  
Je n’ai rencontré aucun problème particulier lors de cette étape.

Après l’installation, j’ai retiré le CD virtuel de la machine, puis je l’ai redémarrée.

J’ai ensuite exécuté la commande suivante afin de vérifier le nombre de paquets installés :

```bash
dpkg -l | wc -l
````

Le nombre de paquets installés était de **263**.

---

## Installation et configuration de SSH

Vient ensuite l’installation de SSH.
Il était demandé d’utiliser les commandes `apt search` et `apt install`.

J’ai donc utilisé la commande suivante pour rechercher SSH :

```bash
apt search ssh
```

Puis je l’ai installé.

---

### Configuration du serveur SSH

Pour la configuration de SSH, je me suis aidée d’internet car je ne savais pas comment procéder.
J’ai vu qu’il fallait modifier le fichier suivant :

```bash
/etc/ssh/sshd_config
```

J’ai donc utilisé la commande :

```bash
nano /etc/ssh/sshd_config
```

(`nano` permet d’ouvrir un éditeur de texte.)

Dans ce fichier, j’ai vu beaucoup de paramètres et je ne savais pas exactement quoi modifier.
Pour configurer le serveur SSH, j’ai regardé une vidéo qui expliquait qu’il fallait modifier l’option **PermitRootLogin**, qui est par défaut en `prohibit-password`, et la passer à `yes`.

Cette option permet de contrôler l’accès au compte **root** à distance.

---

## Connexion SSH et configuration réseau

Ensuite, j’ai ouvert l’invite de commande sur mon PC afin de me connecter à la machine virtuelle en SSH.
Je ne savais pas quelle commande utiliser, donc je suis allée chercher sur internet.

On m’a indiqué qu’il fallait utiliser l’adresse IP de la machine.
J’ai donc utilisé la commande suivante sur mon PC :

```bash
ip a
```

Une adresse IP commençant par **10.** est apparue.
Au début, cela m’a paru étrange, mais j’ai essayé de me connecter quand même.
La connexion a échoué avec un **timeout**.

Au début, je ne comprenais pas pourquoi.
Je pensais m’être trompée dans la configuration de SSH, puis je me suis rappelée qu’il fallait aussi configurer le réseau de la machine virtuelle.

Dans les paramètres VirtualBox, le **mode d’accès réseau** était réglé sur **NAT**.
Après recherche, j’ai compris que le mode NAT permet l’accès à internet via la machine hôte, mais empêche l’accès direct aux services de la machine virtuelle depuis une autre machine du réseau.

Comme je devais accéder à SSH depuis mon PC, j’ai donc changé le mode réseau en **accès par pont (bridge)**.

Même si j’avais entendu que cela pouvait parfois ne pas fonctionner, dans mon cas cela a bien marché.

---

## Commandes système utilisées

Pour vérifier la langue de l’environnement, j’ai utilisé :

```bash
echo $LANG
```

Résultat : `fr_FR.UTF-8`
Cette commande permet d’afficher la langue de l’environnement.

---

Pour connaître le nom de la machine, j’ai utilisé :

```bash
hostname
```

Résultat : `debian`
Cette commande permet de connaître le nom de l’hôte.

---

### Utilisation de la commande `man`

Pour en savoir plus sur la commande `hostname`, je devais utiliser :

```bash
man hostname
```

Cependant, la commande `man` n’était pas installée.
Après recherche sur un forum Debian, j’ai utilisé la commande suivante :

```bash
sudo apt-get install man-db
```

Après installation, j’ai pu consulter le manuel et utiliser l’option `-d` pour afficher le nom de domaine :

```bash
hostname -d
```

Résultat : `ufr-info-p6.jussieu.fr`

---

## Vérification des sources APT

J’ai utilisé la commande suivante pour afficher les sources APT actives :

```bash
cat /etc/apt/sources.list | grep -v -E '^#|^$'
```

Résultat :

```text
deb http://deb.debian.org/debian/ trixie main
```

---

## Comptes et utilisateurs

Pour afficher les mots de passe hachés, j’ai utilisé :

```bash
cat /etc/shadow
```

Exemple de résultat :

```text
root:$y$j9T$REu32pAkkXiDNoToDg8V71$P88.MvbNuT1ZnQJCUBiCUu7jN36oKTVjx5/AW5BaPs2:20458:0:99999:7:::
dhcpcd:!:20458::::::
```

Pour afficher les utilisateurs sans les comptes système inutiles, j’ai utilisé :

```bash
cat /etc/passwd | grep -vE 'nologin|sync'
```

Résultat :

```text
root:x:0:0:root:/root:/bin/bash
dhcpcd:x:100:65534:DHCP Client Daemon:/usr/lib/dhcpcd:/bin/false
```

---

## Disques et espace disque

Pour obtenir des informations sur les partitions, je me suis aidée du wiki Ubuntu concernant la commande `fdisk`.

```bash
fdisk -l
```

Cette commande permet d’afficher les informations sur les disques et partitions.

La commande suivante permet d’avoir plus de détails :

```bash
fdisk -x
```

---

Pour connaître l’espace disque utilisé, j’ai utilisé :

```bash
df -h
```

* `df` signifie *disk free*
* `-h` permet d’afficher les tailles en Mo ou Go

## 3. Aller plus loin

> **Note** : Les réponses ci-dessous sont théoriques et reposent sur la documentation Debian et des ressources officielles Linux. Aucune mise en œuvre pratique n’a été réalisée sur la machine virtuelle.

---

### 3.1 Installation automatique – *Preseed*

L'idée derrière le fichier preseed est de regrouper les réponses qui seraient normalement fournies par l'installateur Debian. Normalement, celui-ci déclenche une série de fenêtres de dialogue selon un scénario spécifique. Le but du fichier preseed est de fournir des réponses à ces questions en avance.

Ce mécanisme est très utilisé dans les environnements professionnels et pour le déploiement de serveurs à grande échelle.

---

### 3.2 Rescue mode – Perte du mot de passe root

Le **mode Rescue** permet de démarrer un serveur dans un environnement Linux de secours afin de réparer le système, modifier la configuration ou sauvegarder des données lorsque le serveur ne démarre plus. Il offre un accès root via SSH sans altérer les données existantes et permet de revenir ensuite au fonctionnement normal.

Quand on a perdu le mot de passe root, la procédure est la suivante :

1. Démarrer le serveur en Rescue mode
(via l’interface d’administration ou option “Reboot to rescue”).
2. Se connecter en SSH avec les identifiants temporaires fournis.
3. Monter la partition du système (exemple : /dev/sda1) : 

```bash
mount /dev/sda1 /mnt
```
4. Entrer dans le système installé :

```bash
chroot /mnt
```
5. Changer le mot de passe root :

```bash
passwd
```
6. Quitter et redémarrer normalement :

```bash
exit
reboot
```

Le serveur redémarre ensuite normalement avec un nouveau mot de passe root fonctionnel.

---

### 3.3 Redimensionnement de partition

Il est possible de redimensionner une partition Linux sans réinstaller le système, sous certaines conditions.

Cela peut prendre un certain temps en fonction de la vitesse et de la capacité de votre disque. Un disque plus grand = plus de temps. Conduite plus rapide = moins de temps.

* Démarrez à partir d'un système externe (disque live comme Ubuntu ou Fedora).
* Réduire la partition « /home » à ce que vous souhaitez.
* Déplacez la partition /home. C’est ce qui prend du temps, car il faut littéralement déplacer toutes les données.
* Redimensionnez la partition / en occupant le nouvel espace vide. Ce sera essentiellement instantané.

---

### 3.4 Proxy

Un **proxy** est un système ou un routeur qui fournit une passerelle entre les utilisateurs et Internet.
Dans ce TP, le proxy est utilisé :

* **Nom** : proxy.ufr-info-p6.jussieu.fr

Pour configurer le proxy on utilise des variables d’environnement, définies par exemple dans /etc/profile :

```bash
export http_proxy="http://proxy.ufr-info-p6.jussieu.fr:3128"
export https_proxy="http://proxy.ufr-info-p6.jussieu.fr:3128"
```

Pour le gestionnaire de paquets APT, la configuration se fait dans le fichier `/etc/apt/apt.conf` :

```bash
Acquire::http::Proxy "http://proxy.ufr-info-p6.jussieu.fr:3128/";
```

Cette configuration permet aux outils réseau de fonctionner correctement dans un environnement utilisant un proxy.

---


