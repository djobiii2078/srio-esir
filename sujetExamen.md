
Une société SRIOEYE a réalisé 30 millions d'euros de bénéfices avec 120 employés avec pour fond de commerce l'installation et la maintenance des caméras de surveillance chez des particuliers, grandes surfaces, lieux de rassemblement et certains lieux gouvernementaux.

Sur chaque équipement (caméra), le firmware tourne sur Debian Jessy.
Dû à une faible capacité de stockage, les enregistrements journaliers sont sauvegardés sur des serveurs distants. Ces enregistrements sont disponibles grâce à une plateforme web sécurisée grâce à un mécanisme d'authentification. 

**Attaquants**

Une entreprise concurrente vous appelle afin de pouvoir faire tomber SRIOEYE. Pour cela, vous allez être amené à réaliser un ensemble d'opérations à travers des scripts pour collecter, pénétrer, et attaquer les systèmes de SRIOEYE.

**Défenseurs**

SRIOEYE vous contacte parce qu'elle a bien conscience que son récent succès va attirer un regard malveillant. Elle vous demande d'inspecter et améliorer la sécurité 
de leurs systèmes. 

1. D'après vous, Debian est-il mieux sécurisé qu'un système Windows ? 
_________________________________________________

2. D'après votre compréhension du business de SRIOEYE, quelle sont les deux principes de sécurité que vous jugez plus délicat (commentez votre réponse)?
____________________________________________________

3. Si vous devez résumer en un mot la raison pour laquelle les caméras de SRIOEYE sont vulnérables, quel mot utiliserez-vous ?
____________________________________________________

4. Pour un équipement IOT comme une caméra connectée à faible puissance, quelles sont les conséquences sur les protocoles de sécurité ?

____________________________________________________

5. Quelle peut être l'avantage d'un protocole comme LORA pour SRIOEYE ? 
____________________________________________________

## Obtention de secret d'architecture

Grâce à l'ex copine d'un responsable du SI de SRIOEYE, vous arrivez à ruser et à obtenir des informations précieuses concernant les dossiers sensibles et l'architecture de certaines solutions. 

1. Comment appelle-t-on cette stratégie façon d'extirper les informations sur une cible ?
_________________________________________________
Vous réussissez à mettre la main sur des dossiers sensibles accessible 
par certains utilisateurs comme suit :


|     Dossier     	| Utilisateur 	| Droits 	|
|:---------------:	|:-----------:	|:------:	|
|   chiffrement   	|    Ibende   	|   rw   	|
| codesmonitoring 	|    Ebode    	|    x   	|
| codebackeend    	| Xavier      	| rx     	|
| codebackeend    	| Ibende      	| rwx    	|
| chiffrement     	| Xavier      	| x      	|
| codesmonitoring 	| Xavier      	| rw     	|

2.

**Attaquants**

Trouvez un format simple pour vous permettre de représenter et manipuler facilement ces informations facilement.
Utiliser votre format pour illustrer le tableau que vous avez reçu.

Écrire un script qui prend en entrée un fichier qui a votre 
format et montre pour tous les dossiers disponible qu'elle est l'utilisateur 
avec le plus de droits. Cette information sera utile pour savoir 
quel utilisateur nous permettra d'avoir les informations sur un dossier 
précis. 

**Défenseurs**

Trouvez un format simple pour vous permettre ces informations simplement et les manipuler.
Utiliser votre format pour illustrer le tableau que vous avez reçu.

Écrire un script qui permet de s'assurer que le principe LLP (Least Level Privilege) est appliqué. Concrètement, servez-vous des informations ci-dessous pour vérifier le rôle de chaque utilisateur et s'assurer que ses droits correspondent. Si l'utilisateur possède plus de droits que prévu, alors, les modifier.

Ebode est un administrateur système. 
Il définit la longueur des clés de chiffrement et a besoin de tester les 
protocoles qui utilisent les clés. 

Ibende est un testeur backend. Il a juste pour rôle de vérifier si les 
codes du backend fonctionnent et génère un rapport. 
Par contre, il aide à maintenir le code du monitoring.

Xavier est le guru du code du monitoring. Il a plein pouvoir sur ce code.
Pour les autres activités (chiffrement et backend), il observe juste et 
donne un avis purement esthétique sur le code. 

3. 

**Attaquants**

Ecrire un script qui surveille les opérations de modification sur les dossiers du chiffrement, afin de détecter l'heure à laquelle ces fichiers sont modifiés et le nombre de fois ça change par jour. 
Concrètement, votre script doit surveiller ces fichiers et faire un reporting à la fin de chaque journée.

**Défenseurs**

Écrire un script qui surveille les opérations de lecture sur les dossiers du chiffrement, pour détecter l'heure à laquelle ces fichiers sont ouverts et le nombre de fois que ça change 
par jour. 
Concrètement, votre script doit surveiller ces fichiers et faire un reporting 
à la fin de chaque journée.

## Gestion de la base de donnée

À travers les informations collectées, vous déterminez que les caméras utilise une version modifiée du protocole WEP-512 (WEP-844) afin de pouvoir échanger avec la base de donnée. 

1. Sachant qu'on sait que l'algorithme RC4 est utilisé, quelle est la longueur 
de la clé utilisée par les caméras ?

_________________________________________________ 

2. Si les caméras doivent changer des clés, chaque une minute et garder les anciennes clés de la journée pour un souci de reporting, quelle est la taille en MBytes que consommerons le stockage des clés. 

_________________________________________________

3. Quelle est le nombre combinaisons pour une attaque de type force brute si on veut deviner la clé utilisée par les caméras ? 

_________________________________________________

4. Avec un CPU cadencé à 2.8 GHz par seconde, pourrais-je réussir à obtenir la clé utilisée 
par une caméra en moins d'une minute ? Sinon, quelle puissance de calcul me faudrait-il ?

_________________________________________________

5. En hexadécimal, réaliser la conversion des chiffres ci-dessous (en expliquant votre cheminement) : **(1044), (481), (844), (24)**

_________________________________________________


6. Vous avez mis la main sur les bouts de code suivant de monitoring ci-dessous. Analyser et déterminer à quelle faille il s'expose avec un exemple concret. Corrigez-les.   

```
char* normalize_color(uint32_t* img_pixels, uint32_t curr)
{
	tab_colors = ["#FF5733", "#452A24"];
	index = (img_pixels[curr]+1)+(img_pixels[curr]>>8))>>4);
	return tab_colors[index];
} 
```

```
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
	if(strcmp(monitoring,'y'))
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

7. Quelles sont les techniques dans la littérature 
pour contrer le type de problème que présentaient les deux codes précédents. 
_________________________________________________
## Génération de dictionnaire et force brute.

Vous avez réussi à obtenir les informations concernant 
la date d'anniversaire, la date d'anniversaire des enfants, 
le jour d'embauche, et la date de naissance des parents pour 
chaque employé sensible. 
Ces données sont compilés ici: [stolen_data.csv](./td-tp/srioeye_stolen_data.csv)

1. Écrire un script pour générer un dictionnaire en réalisant 
des permutations de noms, anniversaires, et date d'embauche de chaque employé. 

2. Vous devez communiquer ce dictionnaire à vos collègues 
de façon sécurisée. Pour cela, étendre le script pour qu'à la fin du dictionnaire, le dictionnaire soit chiffré avec le chiffrement de César à 4/3 caractères (4 pour les consonnes 
et 3 pour les voyelles). 

3. Tracer une courbe qui montre le temps que mets votre script pour générer un dictionnaire 
avec et sans chiffrement pour 200, 400, 600, 800, et 1000 employés.
Pour chaque catégorie, ressortir une moyenne, 95 et 99ᵉ percentile. 

## Déchiffrement d'un point d'accès et nettoyage des traces. 

Vous vous rendez compte que le temps d'une attaque par force brute risque être couteuse. 
Vous voulez interceptez les communications d'un point d'accès utilisé par SRIOEYE 
pour pouvoir choper la clé du réseau et pénétrer le réseau interne. 

À 10 h 30, deux réseaux seront disponibles dans la salle, "SRIOEYE1" et "SRIOEYE2".

1. Votre rôle sera d'écrire un script ou un ensemble de commandes pour récupérer le handshake d'un appareil et essayer la force brute avec le dictionnaire que vous avez à disposition.
Lorsque vous avez récupéré le handshake, essayez d'empêcher les autres équipes d'effectuer des manipulations sur les point d'accès cible. 

2. Pour éviter tout risque d'être pris la main dans le sac ou évitez des audits inutiles, éffacer les logs de votre historique (ou remplacer les par une suite de commandes factices).

## Analyse du tas d'un processus et utilisation de mmap/mprotect.

Votre informateur vous fournit le tas d'exécution de certains programmes sensibles et vous demande d'effectuer une analyse.

Les informations sont les suivantes : 
```
  address           perms offset  dev   inode       pathname
              00400000-00452000 r-xp 00000000 08:02 173521      /usr/bin/dbus-daemon
              00651000-00652000 r--p 00051000 08:02 173521      /usr/bin/dbus-daemon
              00652000-00655000 rw-p 00052000 08:02 173521      /usr/bin/dbus-daemon
              00e03000-00e24000 rw-p 00000000 00:00 0           [heap]
              00e24000-011f7000 rw-p 00000000 00:00 0           [heap]
              ...
              35b1800000-35b1820000 r-xp 00000000 08:02 135522  /usr/lib64/ld-2.15.so
              35b1a1f000-35b1a20000 r--p 0001f000 08:02 135522  /usr/lib64/ld-2.15.so
              35b1a20000-35b1a21000 rw-p 00020000 08:02 135522  /usr/lib64/ld-2.15.so
              35b1a21000-35b1a22000 rw-p 00000000 00:00 0
              35b1c00000-35b1dac000 r-xp 00000000 08:02 135870  /usr/lib64/libc-2.15.so
              35b1dac000-35b1fac000 ---p 001ac000 08:02 135870  /usr/lib64/libc-2.15.so
              35b1fac000-35b1fb0000 r--p 001ac000 08:02 135870  /usr/lib64/libc-2.15.so
              35b1fb0000-35b1fb2000 rw-p 001b0000 08:02 135870  /usr/lib64/libc-2.15.so
              ...
              f2c6ff8c000-7f2c7078c000 rw-p 00000000 00:00 0    [stack:986]
              ...
              7fffb2c0d000-7fffb2c2e000 rw-p 00000000 00:00 0   [stack]
              7fffb2d48000-7fffb2d49000 r-xp 00000000 00:00 0   [vdso]
```

1. Écrire un script qui vous affiche la rangée mémoire avec la plus grosse taille. 
2. Rajouter une fonction à votre script pour afficher les zones qui sont susceptibles à un buffer overflow et non protégées pour les processus fils.
3. Utiliser mmap et mprotect pour changer la protection de la librairie `/usr/lib64/ld-2.15.so` pour qu'elle soit protégée entièrement en exécution. 
