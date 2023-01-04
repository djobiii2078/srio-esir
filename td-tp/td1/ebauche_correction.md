# Ebauche de correction TD1 

## Question 1-4

Ce sont des commandes de bases que vous pouvez retrouvez très facilement en ligne.
Donc, je ne donnerais pas de corrigé sachant qu'il peut y avoir plusieurs facon d'effectuer chaque opération demandé.

## Question 5

1. Il y'a plusieurs facon de représenter ces informations mais ce qu'il faut garder en tête c'est comment on va les exploiter.
Concrètement, en fonction de votre algorithme de parcours des informations, il faut avoir une structure simple qui vous facilite la vie. 

Ici je choisis le format `json` pour la représentation simple où les utilisateurs sont repertoriés par niveau. 
De cette facon, pour parcourir les utilisateurs, je vais parcourir par niveau sachant que les dossiers de chaque niveau sont connus d'avance. Exemple de représentation:

```
{
	"users":
  {
    "level1": [
      {
        "name" : "joseph",
        "rights": "x"
      },
      {
        "name": "abena",
        "rights": "rw"
      },
      {
        "name": "kinfack",
        "rights": "r"
      }
    ],

    "level2": [
      {
        "name": "bosco",
        "rights": "rx"
      },
      {
        "name": "branktor",
        "rights": "r"
      }
    ],
    "level3": [
      {
      "name" : "Moyo",
      "rights": "r"
    },
    {
      "name": "Magally",
      "rights": "x"
    },
    {
      "name" : "Jeanette",
      "rights" : "rwx"
    }]
}
}
```

2. D'un point de vue algorithmique, l'idée c'est a) parcourir les utilisateurs à partir du fichier, b) pour chaque utilisateur
connaitre les dossiers concernés et les droits, c) créer l'utilisateur, et d) appliquer les droits nécessaire. 

Pour être modulaire, je vais avoir une fonction pour chaque sous étape et les appeler dans une boucle qui parcours chaque utilisateur. 
Il y'a plusieurs facons de gérer les droits des dossiers mais une facon modulaire serait de créer des groupes "level1", "level2", et "level3". 
Par défaut, un utilisateur de chaque groupe a tout les droits sur les dossiers concernés par le groupe.
Puis, à sa création, les droits sont retirés.
```
#!/bin/bash

level1_folders=( "graphiques" "finances" "hr" )
level2_folders=( "developpement" "maintenance" "acquisition" )
level3_folders=( "recherche" "securite" "armement" )

#
# Cette fonction crée les dossiers s'ils n'existent pas, les groupes associés et 
# empèche ce qui ne sont pas dans le bon groupe de voir les dossiers concernés
#

initFolders(){
	echo -e "\nInitializing level 1 folders"
	for folder in ${level1_folders[@]};do
		rm -rf folder
		mkdir folder 
	done
	echo -e "\nInitialization ok... going to level 2 folders"
	for folder in ${level2_folders[@]};do
		rm -rf folder
		mkdir folder 
	done
	echo -e "\nInitialization ok... going to level 3 folders"
	for folder in ${level3_folders[@]};do
		rm -rf folder
		mkdir folder 
	done
	echo -e "\nInitialization ok... Creating groups level1, level2, level3"
	
	groupadd level1
	groupadd level2
	groupadd level3

	echo -e "\nGroups created (you can check /etc/group), setting restrictions on folders"
	echo -e "\nSetting permissions for each group"
	for folder in ${level1_folders[@]};do
		chgrp folder level1
	done
	for folder in ${level2_folders[@]};do
		chgrp folder level2
	done
	for folder in ${level3_folders[@]};do
		chgrp folder level3
	done
	
}

#
# Cette fonction créer un utilisateur.
# On lui passe un nom en paramètre et il s'occupe de le créer
#

createUser(){
useradd -m -s /usr/bin/bash $1 
#les options -m et -s c'est pour créer un dossier home et permettre que l'utilisateur puisse lancer des scripts
}

#
# cette fonction rajoute un utilisateur à un groupe
# Cela a pour effet de lui donner un accès aux dossiers de ce groupe 
#
addUserToGroup(){
usermod -G $1 $2
}


#
# Cette fonction mets à jour les permissions d'un utilisateurs $1, de niveau $2, avec les droits $3
#
updatePermissions(){
if [ "$2" -eq "1"]; then
	for folder in ${level1_folders[@]};do
			setfacl -m u:$1:$3 folder  
	done
fi 

if [ "$2" -eq "2"]; then 
	for folder in ${level1_folders[@]};do
			setfacl -m u:$1:$3 folder  
	done
	for folder in ${level2_folders[@]};do
			setfacl -m u:$1:$3 folder  
	done
	
if [ "$2" -eq "3"]; then 
	for folder in ${level1_folders[@]};do
			setfacl -m u:$1:$3 folder  
	done
	for folder in ${level2_folders[@]};do
			setfacl -m u:$1:$3 folder  
	done
	for folder in ${level3_folders[@]};do
			setfacl -m u:$1:$3 folder  
	done
fi 
}
```

Le reste consiste à parcourir votre fichier en entrée et exploiter les fonctions à votre disposition.
Par contre, si votre structure de représentation changes, vous n'aurez pas forcément les même fonctions.

3. Lorsque vous faîtes un "ls -la", vous obtenez la liste des dossiers, fichiers avec les droits qui y sont associés.
De plus, avec sudo -u <user> <commande>, vous pouvez executer une commande en étant un autre utilisateur.
Par exemple: `sudo -u jimmy ls -la`, exécuterez la commande `ls -la` comme si c'était l'utilisateur `jimmy` (si jimmy existe).
Grâce à cela, vous obtenez les droits sur les dossiers concernés et pouvez comparer avec ceux de votre fichier en entrée pour déterminer si tout est conforme ou non.  

## Question 6

Le but des deux questions est de surveiller les opérations sur les fichiers de notre choix.
La commande inotifywait (du package `inotify-tools`) permet exactement cela.
Ci-dessous je mets la commande qui permet de faire cela, et en sortie de cette commande vous avez les paramètres 
qui donne l'heure et le type d'opération sur le fichier/dossier qu'on surveille. 

Je vous laisse completer la mini-fonction.

```
#!/bin/bash 

# surveille le fichier et envoie les informations dans un fichier

monitorFile()
{
	inotifywait -q -m -e access,modify,open --format %e $1 |
		#à vous de remplir le contenu --- globalement c'est juste recupérer l'argument $action et faire un echo dans un fichier
		echo .... > $2 
}
 ```
