**Durée: 2h30, Note/20**
Pour les exercices de programmation, utiliser le langage de votre choix. 
______


## Analyse de code (3pts)

Analyser les codes suivants et corrigez leurs problèmes de sécurité et erreurs de programmation. 

*Code 1*

```C
char* normalize_color(uint32_t* img_pixels, uint32_t curr)
{
tab_colors = ["#FF5733", "#452A24"];
index = (img_pixels[curr]+1)+(img_pixels[curr]>>8))>>4);
return tab_colors[index];
}
```

*Code 2*

```C
void report()
{
char** days=["monday","tuesday","wednesday","thursday","friday","saturday","friday"];
//Global variable can be changed
//by another process
extern char maintenance;
char buff[8];
int i = 0;
for(i=0; i<7; i++)
{
    strcpy(buff, days[i]);
    //Should we enter in monitoring mode
    if(!strcmp(monitoring,'y'))
    {
        printf("Can't do reporting");
        maintenanceMode();
        exit()
    }
    performReporting(buff);
}

printf("Reporting correctly done");

}

```

## Gestion mémoire (6pts)

Considerez le tas d'un processus P ci-dessous:

```C
address perms offset dev inode pathname
00400000-00452001 r-xp 00000000 08:02 173521 /usr/bin/dbus-daemon
00651000-00652000 r--p 00051000 08:02 173521 /usr/bin/dbus-daemon
00652000-00655000 rw-p 00052000 08:02 173521 /usr/bin/dbus-daemon
00e03000-00e24000 rw-p 00000000 00:00 0 [heap]
00e24000-011f7080 rw-p 00000000 00:00 0 [heap]
...
35b1800000-35b1820000 r-xp 00000000 08:02 135522 /usr/lib64/ld-2.15.so
35b1a1f000-35b1a20000 r--p 0001f000 08:02 135522 /usr/lib64/ld-2.15.so
35b1a20000-35b1a21000 rw-p 00020000 08:02 135522 /usr/lib64/ld-2.15.so
35b1a21000-35b1a22000 rw-p 00000000 00:00 0
35b1c00000-35b1dac000 r-xp 00000000 08:02 135870 /usr/lib64/libc2.15.so
35b1dac000-35b1fac000 ---p 001ac000 08:02 135870 /usr/lib64/libc2.15.so
35b1fac000-35b1fb9000 r--p 001ac000 08:02 135870 /usr/lib64/libc2.15.so
35b1fb0000-35b1fb2000 rw-p 001b0000 08:02 135870 /usr/lib64/libc2.15.so
...
f2c6ff8c000-7f2c7078c000 rw-p 00000000 00:00 0 [stack:986]
...
7fffb2c0d000-7fffb2c2e000 rw-p 00000000 00:00 0 [stack]
7fffb2d48000-7fffb2d49000 r-xp 00000000 00:00 0 [vdso]
```

1. Ecrire un script qui analyse le tas suivant et fournit des statistiques sur les tailles de chaque élement. Concrètement, votre script doit retourner la moyenne, la médiane, la plus petite, et la plus grande taille. Pour la plus petite et la plus grande taille, associez les libraries qui correspondent à ces tailles. 

2. Ecrire un script qui détecte les zones susceptible d'être invulnérable au buffer overflow mais vulnérable au processus fils.

3. Ecrire un pseudo code en C qui utilisera mmap et mprotect pour changer la
protection de la librairie /usr/lib64/ld-2.15.so pour qu'elle soit protégée
entièrement en exécution. 

## LLP (6pts)

Considerez le tableau des droits des utilisateurs sur des fichiers sensible d'un serveur.

|     Dossier     	| Utilisateur 	| Droits 	|
|:---------------:	|:-----------:	|:------:	|
|   chiffrement   	|    Keen   	|   rw   	|
| codesmonitoring 	|    Keen    	|    x   	|
| codebackeend    	| Cooper      	| rx     	|
| codebackeend    	| Red         	| rwx    	|
| chiffrement     	| Cooper      	| x      	|
| codesmonitoring 	| Red         	| rw     	|

1. Trouvez un format simple pour vous permettre ces informations simplement et les manipuler. Utiliser votre format pour représenter le tableau précedent.

2. Ecrivez un script qui prends en entrée votre fichier précedent et fournit pour chaque dossier les utilisateurs avec le plus de droits sur ces dossiers. Pour cela, considerez la valeur numérique de chaque droit. 

3. Écrire un script qui permet de s'assurer que le principe LLP (Least Level Privilege) est appliqué. Concrètement, servez-vous des informations ci-dessous pour vérifier le rôle de chaque utilisateur et s'assurer que ses droits correspondent. Si l'utilisateur possède plus ou moins de droits que prévu, alors, le notifier avec un message sur la console (en précisant si il/elle a moins ou plus de droit).

```
Keen est un administrateur système. 
Elle définit la longueur des clés de chiffrement et a besoin de tester les 
protocoles qui utilisent les clés. 

Red est un testeur backend. Il a juste pour rôle de vérifier si les 
codes du backend fonctionnent et génère un rapport. 
Par contre, il aide à maintenir le code du monitoring.

Cooper est le guru du code du monitoring. Il a plein pouvoir sur ce code.
Pour les autres activités (chiffrement et backend), il observe juste et 
donne un avis purement esthétique sur le code.
``` 

 ## Dictionnaire et chiffrement Cesar (5pts)

Vous avez réussi à obtenir les informations concernant 
la date d'anniversaire, la date d'anniversaire des enfants, 
le jour d'embauche, et la date de naissance des parents pour 
1000 employés sensible. 
Ces données sont compilés ici: [stolen_data.csv](./td-tp/srioeye_stolen_data.csv)

1. Vous devez communiquer ce dictionnaire à vos collègues 
de façon sécurisée. Pour cela, écrire un script pour que le script soit chiffré avec le chiffrement de César à 4/3 caractères (4 pour les consonnes 
et 3 pour les voyelles). La clé de votre chiffrement de César ne sera pas l'alphabet mais la chaine de caractère suivante `eaoiocmsjtfodzwaxctjbkpgcy` (considerez la première occurence d'un élement dans la clé). 
Les élements qui ne sont pas dans la clé ne sont pas modifié.

2. Quelle est la nouvelle taille de votre fichier sensible ? 
