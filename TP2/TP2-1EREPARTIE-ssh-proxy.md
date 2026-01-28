---

````markdown
# TP 02 – Partie 1 : Secure Shell (SSH)

## 1. Secure Shell : SSH

Tout d’abord, j’ai réalisé une connexion SSH en tant que root.  
Pour cela, j’ai installé le service SSH avec la commande suivante :

```bash
apt install ssh
````

Puis j’ai vérifié que le service fonctionnait correctement avec :

```bash
systemctl status ssh
```

(J’ai trouvé cette commande sur internet afin de vérifier que le service SSH était bien actif.)

---

### Configuration de SSH

À l’aide de la commande :

```bash
man sshd_config
```

j’ai consulté le manuel du fichier de configuration SSH.
Le fichier de configuration se trouve dans :

```bash
/etc/ssh/sshd_config
```

Dans ce fichier, j’ai modifié l’option **PermitRootLogin** afin d’autoriser la connexion distante en root.

Par défaut, laisser la configuration SSH sans restriction peut exposer le serveur à des vulnérabilités, comme les attaques par force brute ou les tentatives d’accès non autorisées.

Pour autoriser la connexion root avec un mot de passe, il suffit de définir :

```text
PermitRootLogin yes
```

---

### Différentes options de PermitRootLogin

Voici une description des différentes options possibles, avec leurs avantages et inconvénients.

#### **PermitRootLogin no**

* **Description :** Désactive complètement la connexion SSH en root.
* **Avantage :** Très bonne sécurité.
* **Inconvénient :** Il faut se connecter avec un autre utilisateur et utiliser `sudo`.

#### **PermitRootLogin yes**

* **Description :** Autorise la connexion SSH en root avec un mot de passe.
* **Avantage :** Simple à utiliser et pratique pour l’administration.
* **Inconvénient :** Peu sécurisé, vulnérable aux attaques.
* **Quand l’utiliser :** Pour des TP ou des machines personnelles non exposées.

#### **PermitRootLogin prohibit-password**

* **Description :** Autorise la connexion root uniquement avec une clé SSH.
* **Avantage :** Beaucoup plus sécurisé.
* **Inconvénient :** Nécessite la gestion de clés SSH.
* **Quand l’utiliser :** Pour un serveur destiné à être exposé ou utilisé en production.

Dans un premier temps, j’ai utilisé l’option :

```text
PermitRootLogin yes
```

afin de faciliter les tests et la compréhension du fonctionnement de SSH.

---

## 1.2 Authentification par clé – Génération des clés

Dans cette partie, j’ai appris à utiliser l’authentification par clé SSH.

Pour générer un couple de clés (publique et privée), j’ai utilisé la commande :

```bash
ssh-keygen
```

Cette commande crée deux fichiers :

* une **clé privée**
* une **clé publique**, reconnaissable par l’extension `.pub`

---

### Dépôt de la clé publique sur le serveur

J’ai ensuite copié ma clé publique dans le fichier `authorized_keys`, qui contient les clés autorisées à se connecter au serveur :

```bash
cat /root/.ssh/ma_cle_publique.pub >> /root/.ssh/authorized_keys
```

---

### Droits sur les fichiers SSH

Pour respecter les règles de sécurité d’OpenSSH, j’ai modifié les permissions du dossier `.ssh` :

```bash
chmod 700 .ssh
```

Cela donne tous les droits uniquement au propriétaire du dossier.

Après cela, j’ai testé la connexion depuis ma machine hôte, et la connexion a fonctionné.

---

## 1.5 Sécurisation de l’accès SSH

Afin de sécuriser l’accès SSH au compte root, j’ai modifié la configuration du serveur SSH.

Dans le fichier `/etc/ssh/sshd_config`, j’ai appliqué les paramètres suivants :

```text
PermitRootLogin prohibit-password
```

Cela permet uniquement l’authentification par clé SSH pour le compte root.

```text
PasswordAuthentication no
```

Cette option désactive complètement l’authentification par mot de passe.

```text
PubkeyAuthentication yes
```

Cette option active l’authentification par clé publique.

Après ces modifications, j’ai testé la connexion depuis la machine hôte et celle-ci a bien fonctionné.

---

## Attaques par brute force SSH

Une attaque par **brute force SSH** consiste à tester automatiquement un grand nombre de combinaisons de mots de passe jusqu’à en trouver un valide.

### Moyens de protection :

* Utiliser des mots de passe solides
* Limiter les comptes accessibles en SSH
* Désactiver l’authentification par mot de passe
* Utiliser l’authentification par clé SSH
* Détecter et bloquer les tentatives répétées de connexion
* Limiter les dégâts en cas de compromission

```
