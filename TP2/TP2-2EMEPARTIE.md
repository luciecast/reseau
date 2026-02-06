
````md
# Étude des processus

## 1.1 Afficher les processus
Tout d'abord, j'ai commencé par regarder ce que proposait la commande `ps`, donc j'ai effectué la commande :

```bash
man ps
````

Par la suite, j'ai pu trouver la commande pour afficher tous les processus avec les informations demandées :

```bash
ps -eo user,pid,pcpu,pmem,stat,start,time,command
```

Cette commande montre tous les processus avec les colonnes souhaitées.

---

## À quoi sert TIME ?

La colonne **TIME** indique le temps total que le processus a passé sur le processeur depuis qu’il existe.
C'est le temps CPU.

---

## Processus qui a utilisé le plus de CPU

En regardant la colonne **TIME**, le processus qui a le plus consommé était :

```bash
/sbin/init
```

---

## Premier processus lancé au démarrage

Le tout premier processus lancé par Linux est :

```bash
systemd
```

On peut le voir avec :

```bash
ps -e | head
```

---

## Heure de démarrage de la machine

Grâce à la commande suivante, nous pouvons voir à quelle heure notre machine a été allumée :

```bash
w
```

J’ai vu que ma machine était allumée depuis **34 minutes**, et qu’il était **10h04**, donc démarrage vers **9h30**.

Autre commande possible :

```bash
who -b
```

---

## Nombre de processus créés

Pour compter les processus actuels :

```bash
ps -e | wc -l
```

J’en avais environ **90**.

---

# Processus père (PPID)

## Afficher le PPID

On peut afficher le PPID avec :

```bash
ps -o ppid
```

---

## Trouver les ancêtres du processus ps

En lançant `ps` et en affichant son PPID, on peut remonter la chaîne des parents.

---

# pstree

Après installation :

```bash
apt install pstree
```

On peut afficher l’arbre des processus :

```bash
pstree -p
```

C’est plus clair que `ps` pour voir qui a créé quoi.

## Trier par mémoire résidente
Avec la commande man top, j'ai pu voir que avec le préfix -o, on pouvait chercher la
tache que nous voulons, ici nous voulons la mémoire résidente donc "RES" :

```bash
top -o RES
```

---

## Processus le plus gourmand

Le processus qui consomme le plus dépend de la machine.
Souvent, c’est un navigateur ou un service système.

---

## Commandes utiles dans top

Pour changer les couleurs, on appuie sur “z” et ensuite on choisi pour les donner avec S, M, H ou T et ma couleur qu’on veut : noir, rouge, vert, jaune, bleu, magenta, cyan, blanc
Pour mettre en avant la colonne triée il suffit de cliquer sur la touche `x` et pour changer la colonne de tri, la touche `o`

---

## htop

### Avantages

Les avantages sont que c'est plus lisible, les couleurs y sont déjà pas besoin de les configurer comme top et c'est plus facile à utiliser

### Inconvénients

Les inconvénients sont que nous devons installer htop

---

# Arrêt d’un processus

## Scripts

Par la suite j'ai créé les 2 fichiers : 
### date.sh

```bash
while true; do sleep 1; echo -n 'date '; date +%T; done
```

Affiche l’heure chaque seconde.

---

### date-toto.sh

```bash
while true; do sleep 1; echo -n 'toto '; date --date '5 hour ago' +%T; done
```

Affiche l’heure d’il y a 5 heures.

# Journaux système – rsyslog

## Vérifier si rsyslog tourne

Pour vérifier si rsyslog tourne, on utilise cette commande

```bash
ps -e | grep rsyslog
```

Et on voit son PID si le service est actif.

---

La différence entre 'tee' et 'cat' est que cat permet d'afficher le contenue d'un fichier
alors que al commande tee permet d'afficher mais aussi d'enregistrer.

```bash
ls | cat
```
Permet d'afficher les fichiers du dossier dans lequel nous somme

```bash
ls -l | cat > liste
```
Permet de mettre de mettre le resultat de la commande ls -s dans le fichier liste

```bash
ls -l | tee liste
```
Permet de faire la meme chose qu'avant, sauf que ça affiche dans le terminal

```bash
ls -l | tee liste | wc -l
```
Permet de voir le nombre de ligne dans le fichier liste qui contient le resultat de la commande ls -l

Le service rsyslog est lancé dans `/etc/rsyslog.conf`
Le PID est 897

```bash
tail -f /var/log/syslog
```

Le fichier `/etc/logrotate.conf` sert à gere les règles globales, comme la fréquence de rotation ou le nombre de fichiers archivés à conserver

La commande :
```bash
dmesg
```
Le modele de processeur est pour ma part : 12th gen intel(r) cpre (tm) i5-1235U

````
