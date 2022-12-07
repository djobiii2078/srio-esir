# TP2: Détecter des vulnérabilités à partir du tas des processus

1. Ecrire un programme dans le langage de votre choix (je vous conseille en bash) qui analysera periodiquement le tas de tous les processus en cours d'exécution et affichera les zones vulnérable à un buffer overflow (les non-protégées en ecriture), les zones vulnérables à de la corruption (non-protégées en lecture), et susceptible à être détourner par les processus fils (les protected).

Concrètement votre programme doit avoir le même style d'affichage que `top`, c.à.d une entête:

`<pid> <chemin_du_processus> <buffer_overflow_zones> <corrupted_zones> <child_zones>`

Où 

```
pid: pid du processus dont les infos vont suivre sur la ligne 

chemin_du processus: Le chemin du binaire qui représente le processus 

buffer_overflow_zones: les addresses (les zones) qui correspondent susceptible au buffer overflow avec la taille de chaque zone. 
Exemple: 55eac3b7c000-55eac3b7d000, 4KB
				  559b8c418000-559b8c41a000, 12KB
				  
corrupted_zones et child_zones: similaire à buffer_overflow_zones
					
```

Par défaut la périodicité doit être de chaque `5 secondes` mais vous devez donner la possibilité à l'utilisateur de spécifier sa période.

2. Etendez votre programme pour donner la possiblité de surveiller uniquement un seul programme en passant en paramètre un `pid` du processus à surveiller. Dans ce cas, en plus du tableau qui se met à jour régulièrement, afficher un diagramme de chaleur qui montre les zones susceptible à des attaques en rouge et les zones saines en blanc. Le diagramme de chaleur se mets à jour en même temps que le tableau.
