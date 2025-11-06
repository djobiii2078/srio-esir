# TP4: Differential privacy / Détecter des vulnérabilités à partir du tas des processus

## Differential privacy 

1. Écrire un programme qui vérifie un mot de passe entré en exploitant la méthode optimisée consistant à arrêter la vérification dès qu'une différence entre le mot de passe entré et le mot de passe sauvegardé est détectée.
2. Écrire un script qui mesure le temps nécessaire à votre programme pour valider ou non un mot de passe. À l'aide d'un script, réalisez 1000 itérations où vous injecterez un mot de passe généré aléatoirement (de même longueur que le vrai mot de passe), et tracez sur une courbe la distribution du temps de ces 1000 itérations. Répétez l'opération pour 1000 itérations avec le bon mot de passe et superposez sa distribution sur la précédente courbe. Qu'observez-vous ?
3. Écrivez du code qui réalise une force brute sur votre vérificateur de mot de passe, en construisant pas à pas le bon mot de passe en itérant sur chaque position du mot de passe, en conséquence du temps nécessaire à chaque caractère.
4. Quel est le temps nécessaire pour votre script pour ces différents mots de passe : **etabafia**, **etaBafia**, **etaBafia1**, **etaBafia1@** ? Qu'observez-vous ?


## Tas des processus 
   
1. Ecrire un programme dans le langage de votre choix (je vous conseille en bash) qui analysera periodiquement le tas de tous les processus en cours d'exécution et affichera les zones vulnérable à un buffer overflow (les non-protégées en ecriture), les zones vulnérables à de la corruption (non-protégées en lecture), et susceptible à être détourner par les processus fils (les protected).

Concrètement, votre programme doit avoir le même style d'affichage que `top`, c.-à-d. une en-tête :

`<pid> <chemin_du_processus> <buffer_overflow_zones> <corrupted_zones> <child_zones>`

Où 

```
pid: pid du processus dont les infos vont suivre sur la ligne 

chemin_du processus: Le chemin du binaire qui représente le processus 

buffer_overflow_zones: les adresses (les zones) qui correspondent susceptible à un buffer overflow avec la taille de chaque zone. 
Exemple: 55eac3b7c000-55eac3b7d000, 4KB
				  559b8c418000-559b8c41a000, 12KB
				  
corrupted_zones et child_zones: similaire à buffer_overflow_zones
					
```

Par défaut la périodicité doit être de chaque `5 secondes` mais vous devez donner la possibilité à l'utilisateur de spécifier sa période.

2. Étendez votre programme pour donner la possibilité de surveiller uniquement un seul programme en passant en paramètre un `pid` du processus à surveiller. Dans ce cas, en plus du tableau qui se met à jour régulièrement, afficher un diagramme de chaleur montrant les zones susceptibles d’attaques en rouge et les zones saines en blanc. Le diagramme de chaleur se met à jour en même temps que le tableau.
