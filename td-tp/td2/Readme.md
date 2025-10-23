# SQLi testing et déni de service

L'objectif de cet exercice est d'implémenter une attaque SQLi qui exploite les vulnérabilités logicielles d'une application. 

## La plateforme 

1. Développez un client en ligne de commande qui reçoit une connexion simple avec les options (**Email** et **Mot de Passe**). 
2. Écrivez le code qui sera appelé pour authentifier un(e) utilisateur(ice). La fonction devra regarder dans une base de données SQL (Mysql, Postgres, ou SQLite), pour authentifier un utilisateur. 

![Graphique pour les courbes](./graphs.PNG)


## Déni de service

À présent, nous allons essayer d'effectuer un déni de service sur votre application d'authentification simple. 

1. Écrire un script qui démarre plusieurs clients en parallèle avec chaque client qui effectue 100 requêtes d'authentification par seconde. Pour chaque requête, une valeur aléatoire de l'email et du mot de passe doit être générée. 
2. Exploitez le script que vous avez écrit plus haut pour obtenir les métriques (latence moyenne, 95 et 99ᵉ percentiles) pour évaluer à partir de quel nombre de clients, la latence et les percentiles chutent de plus de 40% (comparé à quand il y a un seul client).
3. Modifier le code serveur pour utiliser un pool de connexions.
4. Refaites la question 2, pour voir si le nombre de clients (nécessaire pour faire chuter la latence et les percentiles de plus de 40 %) a augmenté ou baissé.
5. Quel impact l'utilisation du pool a-t-elle eu sur la latence de votre service d'authentification ?



