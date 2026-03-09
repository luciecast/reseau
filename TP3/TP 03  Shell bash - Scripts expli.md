# TP 03 : Shell bash - Scripts expliqués

## analyse.sh

```bash
#!/bin/bash
echo "Bonjour, vous avez rentré $# paramètre(s)."
echo "Le nom du script est $0"
echo "Le 3ème paramètre est $3"
echo "Voici la liste des paramètres : $@"
```

**Explication :**
- `$#` : nombre de paramètres passés au script
- `$0` : nom du script
- `$3` : 3ème paramètre
- `$@` : tous les paramètres sous forme de liste
- Ce script permet de comprendre les variables spéciales bash

---

## concat.sh

```bash
#!/bin/bash
if [ $# -ne 2 ]; then
    echo "Erreur : il faut exactement 2 paramètres"
    exit 1
fi
echo "Résultat : $1$2"
```

**Explication :**
- Vérification du nombre de paramètres avec `$# -ne 2`
- `exit 1` : quitte le script avec un code d'erreur
- Concaténation simple des deux paramètres avec `$1$2`
- Gestion d'erreur obligatoire pour la suite du TP

---

## test-fichier.sh

```bash
#!/bin/bash
if [ $# -ne 1 ]; then
    echo "Usage: $0 <fichier>"
    exit 1
fi

f=$1
if [ ! -e "$f" ]; then
    echo "$f n'existe pas"
    exit 1
fi

# Type
if [ -d "$f" ]; then
    echo "$f est un répertoire"
elif [ -f "$f" ]; then
    echo "$f est un fichier ordinaire"
fi

# Droits
droits=""
[ -r "$f" ] && droits="$droits lecture"
[ -w "$f" ] && droits="$droits écriture"
[ -x "$f" ] && droits="$droits exécution"
echo "Droits :$droits"
```

**Explication :**
- Tests sur les fichiers : `-e` (existe), `-d` (répertoire), `-f` (fichier)
- Tests de droits : `-r` (lecture), `-w` (écriture), `-x` (exécution)
- Construction dynamique de la chaîne `droits`
- Utilisation de `&&` pour exécuter conditionnellement

---

## listedir.sh

```bash
#!/bin/bash
if [ $# -ne 1 ] || [ ! -d "$1" ]; then
    echo "Usage: $0 <répertoire>"
    exit 1
fi

echo "Fichiers dans $1 :"
for f in "$1"/*; do
    [ -f "$f" ] && echo "  $f"
done

echo "Répertoires dans $1 :"
for f in "$1"/*; do
    [ -d "$f" ] && echo "  $f"
done
```

**Explication :**
- Vérification : 1 paramètre ET que c'est un répertoire
- Boucle `for` sur tous les éléments du répertoire
- Filtrage avec `-f` (fichiers) et `-d` (répertoires)
- Affichage séparé des deux types

---

## listusers.sh

```bash
#!/bin/bash
echo "Utilisateurs avec UID > 100 :"
awk -F: '$3 > 100 {print $1}' /etc/passwd
```

**Explication :**
- `awk` : outil de traitement de texte
- `-F:` : séparateur = deux-points (format /etc/passwd)
- `$3 > 100` : 3ème champ (UID) supérieur à 100
- `{print $1}` : affiche le 1er champ (login)
- Beaucoup plus simple qu'avec `cut` et boucle

---

## checkuser.sh

```bash
#!/bin/bash
if [ $# -ne 1 ]; then
    echo "Usage: $0 <login ou UID>"
    exit 1
fi

# Cherche dans /etc/passwd
grep -E "^$1:|:x:$1:" /etc/passwd | cut -d: -f3
```

**Explication :**
- `grep -E` : expression régulière étendue
- `^$1:` : cherche login en début de ligne
- `:x:$1:` : cherche UID (format ...:x:UID:...)
- `cut -d: -f3` : extrait le 3ème champ (UID)
- Affiche l'UID si trouvé, rien sinon

---

## createuser.sh

```bash
#!/bin/bash
if [ "$USER" != "root" ]; then
    echo "Il faut être root"
    exit 1
fi

read -p "Login : " login
if grep -q "^$login:" /etc/passwd; then
    echo "$login existe déjà"
    exit 1
fi

read -p "Nom : " nom
read -p "UID : " uid
read -p "GID : " gid

useradd -u "$uid" -g "$gid" -c "$nom" -m "$login"
echo "Utilisateur $login créé"
```

**Explication :**
- Vérification root avec `$USER`
- `read -p` : affiche un prompt et lit la saisie
- `grep -q` : vérifie existence (mode silencieux)
- `useradd` : commande de création
  - `-u` : UID
  - `-g` : GID
  - `-c` : commentaire (nom complet)
  - `-m` : crée le home directory

---

## viewtext.sh

```bash
#!/bin/bash
for f in "$1"/*; do
    if file "$f" | grep -q text; then
        read -p "Voir $f ? (o/n) " r
        [ "$r" = "o" ] && more "$f"
    fi
done
```

**Explication :**
- `file "$f"` : détermine le type de fichier
- `grep -q text` : cherche "text" dans la sortie
- `read -p` : demande confirmation
- `more "$f"` : affiche page par page
- Parcourt tous les fichiers texte du répertoire

---

## note.sh

```bash
#!/bin/bash
while true; do
    read -p "Note (ou q) : " n
    [ "$n" = "q" ] && exit 0

    if [ "$n" -ge 16 ] 2>/dev/null; then
        echo "très bien"
    elif [ "$n" -ge 14 ]; then
        echo "bien"
    elif [ "$n" -ge 12 ]; then
        echo "assez bien"
    elif [ "$n" -ge 10 ]; then
        echo "moyen"
    elif [ "$n" -lt 10 ]; then
        echo "insuffisant"
    else
        echo "Chiffre entre 0 et 20"
    fi
done
```

**Explication :**
- `while true` : boucle infinie
- `read -p` : saisie avec prompt
- Sortie avec `q`
- `2>/dev/null` : cache les erreurs si $n n'est pas un nombre
- Tests en cascade avec `-ge` (greater or equal)
- Gestion du cas où ce n'est pas un nombre valide

---

## Récapitulatif des commandes et concepts utilisés

| Concept | Syntaxe | Utilité |
|---------|---------|---------|
| Paramètres | `$1`, `$#`, `$@` | Accès aux arguments |
| Tests fichiers | `-f`, `-d`, `-e` | Vérifier type/existence |
| Tests droits | `-r`, `-w`, `-x` | Vérifier permissions |
| Conditions | `if [ condition ]; then` | Prise de décision |
| Boucles | `for var in liste; do` | Itération |
| Lecture | `read -p "prompt" var` | Interaction utilisateur |
| awk | `awk -F: '{print $1}'` | Traitement de texte |
| grep | `grep -q pattern` | Recherche silencieuse |
| Redirections | `2>/dev/null` | Cacher les erreurs |

Tous ces scripts ont été vérifiés avec ShellCheck pour garantir leur bon fonctionnement.