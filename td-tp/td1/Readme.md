# TP1 

Ce TP servira d'un rappel des commandes de bases d'un noyau Unix et l'évaluation de l'impact de certaines politiques de sécurité.

## 1. Commandes de bases 

1. Taper la commande vous permettant d'afficher le système installé
2. Taper la commande vous permettant d'afficher la distribution installée
3. Taper la commande vous permettant de connaitre les utilisateurs en cours du système 
4. Taper la commande vous permettant d'ouvrir un nouveau terminal en mode utilisateur
5. Taper la commande permettant de fermer le terminal 
6. Rediriger les 5 dernières commandes que vous venez de taper dans le fichier partie1.txt (`history 5 > partie1.txt`)

## 2. Commande utilisateurs 

1. Afficher à l'écran à quoi sert la commande `man`.
2. Taper la commande qui permet de lister le contenu du repertoire /etc
3. Taper la commande vous permettant de lister de manière détaillée le contenu du répertoire /etc
4. Taper la commande vous permettant d'afficher le contenu du répertoire /dev
5. Taper la commande vous permettant d'afficher le contenu du fichier /etc/passwd
6. Taper la commande vous permettant d'afficher le contenu du fichier /etc/shadow (Affiche à l'écran du terminal le résultat )
7. Taper la commande vous permettant d'afficher par ordre alphabétique les utilisateurs définis dans le fichier /etc/passwd
8. Taper la commande vous permettant de rechercher tous les fichiers du répertoire /etc contenant la chaine de caractères **root**
9. Taper la commande vous permettant de rechercher la localisation du fichier **stdio.h** dans le système
10. Taper la commande vous permettant d'afficher le nombre de lignes, mots et de caractères que comportent le fichier /etc/passwd
11. Rediriger les 10 dernières commandes que vous venez de taper dans le fichier partie2.txt (`history 10 > partie2.txt`)

## 3. Gestions des répertoires 

1. Créer les deux dossiers **HACKER** et **CRACKER** en une seule commande dans le répertoire où tu te situes 
2. Renommer le dossier **CRACKER** avec le nom **BLACKHAT**
3. Créer (en une seule commande) le fichier toto.txt et y ajouter le message suivant (**I ........ because  ...... iiiiiiiii.**)
4. Copier le fichier toto.txt dans les répertoires **ANDIN** et **BLACKHAT** (en une seule commande)
5. Crée un lien symbolique(raccourci) pour chacun  dossiers précédents que vous nommerez à votre guise. 
6. Taper la commande pour supprimer les dossiers **HACKER** et **BLACKHAT** 
7. Taper une commande qui pourra vous permettre de vérifier si les liens symboliques existe toujours après suppression des deux dossiers
8. Rediriger les 7 dernières commandes que vous viens de taper dans le fichier partie3.txt (`history 8 > partie3.txt`)

## 4. Gestion et tri 

1. Taper la commande permettant d'afficher la liste de **tous** les fichiers d'un repertoire.
2. Taper la commande permettant d'extraire la ligne 14 d'un fichier texte nommé **fichier.txt**.
3. Taper la commande permettant d'effacer un fichier nommé **fichier.txt**
4. Taper la commande permettant d'afficher le nombre de lignes d'un fichier nommé **exemple.txt**.
5. Taper la commande permettant d'afficher les fichiers d'un repertoire dont le nom commence par une lettre entre **a** et **e**.
6. Taper la commande permettant de trier un fichier nommé **data.txt** par ordre décroissant alphabétique de ce fichier.
7. Taper la commande permettant de trier un fichier nommé **data.txt** par ordre croissant numérique.
8. Taper la commande permettant de chercher dans toute l'arborescence  (**répertoire/**) les fichiers dont les noms se termine par **".c"**.
9. Taper la commande permettant de chercher dans toute l'arborescence les fichiers dont les noms commencent par "**X ou x**".
10. Taper la commande permettant de chercher dans toute l'arborescence les fichiers dont les noms ne contiennent pas de chiffres.
11. Taper la commande permettant d'afficher à l'écran le contenu du fichier "**affiche.txt**" de telle sorte que tout les "**:**"  sont remplacé par "**";"**. C.-à-d. si le contenu du fichier affiche.txt est **toto:billy:soso**, votre commande devra afficher à l'écran **toto;billy;soso**.
12. Taper la commande permettant d'afficher à l'écran les repertoires dont les noms se terminent par**se ou Se**.
13. Taper la commande permettant de chercher dans **/usr**les fichiers dont la taille dépasse 1Mo (2000 blocs de 500Ko) et dont les droits sont fixés à 755 (**-rwxr-xr-x**).
14. Taper la commande permettant de savoir combien de fichiers sont dans toute l'arborescence (**répertoire /**) vous appartenant et ayant les droits fixés à 466 (**-rw-rw-rw-**).
15. Rediriger les 14 dernières commandes que vous venez de taper dans le fichier partie4.txt (`history 14 > partie4.txt`).

## 5. Least Level Privilege

Vous êtes à la charge d'établir les droits d'accès sur les documents d'une entreprise $\lambda$. Pour vous aider dans cette tâche, on vous fournit le schéma ci-dessous qui explique les différents dossiers du réseau de l'entreprise ainsi que les niveaux de sécurités appliqués.



![llp.png](images/llp.png)






Les utilisateurs de niveau **n**, peuvent avoir accès aux dossiers de niveau $\leq n$. Par contre, leur type d'accès est défini par le code couleur de chaque utilisateur comme sur le schéma. 

1. A l'aide d'un format de données simple (`json`, `xml`, etc.), exprimer l'ensemble des accès possible pour chaque utilisateur dans un fichier.
2. A l'aide de votre fichier créee précedement, écrivez un script avec le language de votre choix (je vous conseille `bash`) qui permet de créer les utilisateurs et dossiers de la figure ci-dessous avec les droits associés. 
3. Ecrivez un script qui prend en entrée un nom d'utilisateur et un fichier de décrivant l'ensemble des droits de chaque utilisateur (comme celui de la question 1), et qui vérifie s'il ne possède pas plus de droits que prévu. 

![carbon (6).png](images/carbon (6).png)


## 6. Surveillance

A la suite de vos résultats à l'exercice précedent, on vous demande de mettre en place une surveillance supplémentaire pour savoir qui et quand quelqu'un.e accède un fichier des fichiers sensible niveau $\geq 2$. 

1. Ecrire un script qui prend en entrée un fichier de description (comme 5.1) et surveille tout les fichiers supérieure à un niveau de sécurité $\leq n$ (n étant un paramètre d'entrée du script), et une durée en secondes, $m$. Concrètement, le script vous permet d'enregistrer dans un fichier log, l'heure et l'utilisateur ayant accéder à un fichier dans un répertoire surveillé. Votre script ne doit agir que $m$ secondes après son lancement. (Vous pouvez utiliser `auditd` , `inotifywait`, etc.)
2. Ecrire un script qui prend en entrée le fichier de log generé par $6.1$, et affiche une courbe sur le terminal qui présente pour chaque dossier qui apparaît dans le log, le nombre d'accès enregistré (comme sur le figure ci-dessous). Pour vous aider avec la courbe, voici un exemple de script (vous êtes libre de l'utiliser ou non).



![Capture du 2022-09-26 16-18-28.png](images/Capture du 2022-09-26 16-18-28.png)



```
#!/bin/bash
 labels=( "hr" "finance" "recherche" "defense")
 values=( 20 40 60 30)
 
 # Show a title
 tput clear
 printf " %10s " ""
 tput setaf 7; tput smul;
 printf "%s\n\n" "Analyse des accès du fichier ($1)"
 tput rmul;
 
 # cycle thru data and show a label,
 for index in "${!labels[@]}"
    do
          tput setaf $(expr $index + 1); # don't use 0 (black)
          printf " %10s " "${labels[index]}"
          eval "printf '█%.0s' {1..${values[index]%.*}}"
          printf " %s %s\n\n" ${values[index]} ${units[index]}
    done
    sleep 10
done
```





