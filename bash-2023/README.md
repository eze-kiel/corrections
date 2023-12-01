# Corrections des exercices de Bash 2023

## Fonctionnalités de base du shell

1. Utiliser la commande `cat` pour écrire quelques caractères dans le fichier `/tmp/foobar`.

```
$ cat > /tmp/foobar
```

2. Utiliser la commande `cat` pour afficher le contenu du fichier `/etc/hosts` de deux manières : en
passant le nom du fichier en argument, puis en utilisant une redirection.

```
$ cat /etc/hosts
$ cat < /etc/hosts
```

3. Combien de dossiers y-a-t-il dans `/etc` ?

```
$ ls -l | grep -c '^d'
```

4. Créer un fichier appartenant à l’utilisateur et au groupe `root`. Grâce aux mécanismes vus précédemment, écrire le contenu de `/etc/passwd` dans ce fichier. Quel est le problème ? Comment le contourner ?

```
$ touch file
$ sudo chown root:root file
$ cat /etc/passwd > file
bash: file: Permission denied
cat /etc/passwd | sudo tee file
```

## Bases du scripting shell

1. Écrire une boucle `for` qui affiche 30 nombres aléatoires.

```bash
for i in {1..30}; do
    echo "$RANDOM"
done
```

2. Écrire un script `rename.sh` qui change l’extension des fichiers finissants par `.jpeg` en `.jpg`.

```bash
#!/bin/bash

set -eu

folder="$1"

if [ -z "$1" ]; then
    echo "first arg cannot be empty" 1>&2
    exit 1
fi

for f in $(find "$folder" -name "*.jpeg"); do
    newname=$(echo "$f" | sed 's/jpeg/jpg/')
    mv "$f" "$newname"
done
```

```
$ mkdir plop/
$ touch plop/{1..20}.jpeg                                                                    
$ ls plop
10.jpeg  12.jpeg  14.jpeg  16.jpeg  18.jpeg  1.jpeg   2.jpeg  4.jpeg  6.jpeg  8.jpeg
11.jpeg  13.jpeg  15.jpeg  17.jpeg  19.jpeg  20.jpeg  3.jpeg  5.jpeg  7.jpeg  9.jpeg
$ ./rename.sh plop
$ ls plop
10.jpg  12.jpg  14.jpg  16.jpg  18.jpg  1.jpg   2.jpg  4.jpg  6.jpg  8.jpg
11.jpg  13.jpg  15.jpg  17.jpg  19.jpg  20.jpg  3.jpg  5.jpg  7.jpg  9.jpg
```


3. Écrire un script `ex.sh` qui extrait automatiquement une archive avec le bon outil en fonction de
l’extension du fichier donné en paramètre. Les extensions supportées devront être: `.zip`, `.tar`,
`.gz`, `.bz2`, `.tar.gz`, `.tar.bz2`, `.7z`.

*TBD*

4. Écrire un script `analyze.sh` qui affiche le nombre de paramètres, le nom du script, le troisième
paramètre, et la liste de tous les paramètres.

```bash
#!/bin/bash

set -eu

echo "$#"
echo "$0"
echo "$4"
echo "$@"
```

```
$ ./all.sh foo bar baz plop
4
all.sh
plop
foo bar baz plop
```

## Concepts avancés

### Manipulation de strings

1. Ré-écrire le script `rename.sh` du chapitre précédent avec les nouvelles notions vues.

```bash
#!/bin/bash

set -eu

folder="$1"

if [ -z "$1" ]; then
    echo "first arg cannot be empty" 1>&2
    exit 1
fi

for f in $(find "$folder" -name "*.jpeg"); do
    newname=${f//jpeg/jpg}
    mv "$f" "$newname"
done
```

## Exercices

### Statistiques de fichier

*TBD*

### Dicepass

```bash
#!/bin/bash

set -eu
IFS="$(printf '\n\t')"

# https://www.eff.org/files/2016/07/18/eff_large_wordlist.txt
dict_path="/usr/share/dict/eff_large_wordlist.txt"

[[ -f "$dict_path" ]] || (echo "error: wordlist '$dict_path' not found" >&2; exit 1)

password=""
for i in {1..6}; do
   sequence="$(< /dev/urandom tr -dc 1-6 | head -c${1:-5};echo;)"
   password="${password}-$(grep "$sequence" "$dict_path" | awk '{print $2}')"
done

echo "${password:1}"
```

### Récurence

*TBD*