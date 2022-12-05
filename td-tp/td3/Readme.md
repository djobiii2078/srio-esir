
# TP3

L'objectif de ce TP est d'éffectuer plusieurs attaques qui exploitent les failles connues des protocoles de communication.


## Hacker un point d'accès 
On fait appel à vous pour faire tomber le point d'accès d'une entreprise en menant une attaque de type \textbf{DOS}.

- Que signifie \textbf{DOS} ? Faîtes un schéma explicatif simple pour illustrer.
- Le point d'accès utilise WPA2 pour l'authentification et encrypter les communications. À quoi sert une PSK? Pour quelles raisons ?

Afin de réaliser la tâche qui vous ait demandé, installez la suite `aircrack-ng` et `wireshark`:

```
sudo apt update
sudo apt install aircrack-ng wireshark
```
- Maintenant, commencer par lister l'ensemble des interfaces réseaux de votre station/pc: `ipconfig` ou `ip a` 
- Démarrer airmon-ng sur votre interface wifi (ici on considère que l'interface se nomme `wlan1`) : `airmon-ng start wlan1`

Cette commande crée une nouvelle interface en mode monitor ayant comme nom: **wlan1mon** suivant votre système. 
Vérifier l'existence de cette interface. Il se peut que votre carte wifi ne supporte pas le mode **moniteur/injecteur**.  Dans ce cas, il faudrait passer par une antenne WiFi.

- Lancer l'analyse des réseaux wifi avoisinant à l'aide de airodump-ng: `airodump-ng wlan1mon`
- À partir de l'analyse de airodump-ng, récupérer le BSSID (@MAC du point d'accès) et le numéro de canal de fréquence utilisé par le point d'accès cible. 
- Arreter la commande précedente et ouvrez deux terminaux.
- Sur le premier terminal, capturez uniquement les informations concernant le point d'accès cible et sauvegarder le tout dans un fichier nommé **log**:
`airodump-ng --bssid <bssid_cible> -c <numero_channel_cible> -w log wlan1mon`.
Essayez de vous connecter sur le point d'accès cible avec un autre équipement pour vérifier que vous arrivez à le détecter.
- Sur le deuxième terminal, forcez la déconnexion de toutes les personnes connectées au point d'accès ciblé pour forcer leurs reconnexion:
`aireplay-ng --deauth 0 -a <bssid> wlan1mon`
- Lorsque le *WPA_HANDSHAKE*, est correctement identifié sur le premier terminal, arretez la capture.
- Vous pouvez analyser le fichier **log-01.cap** avec wireshark afin de recherchez le handshake.
Pour cela, ouvrez le fichier .cap avec wireshark : `wireshark log-01.cap` et recherchez **eapol** (extensible authentication protocol over lan).
Si, un handshake a été capturé, vous devriez voir des handshakes echangés en quatre phase **Message 1/4, 2/4, ...**.
- Ouvrez le message **Message 2/4** par exemple, et allez dans le champ **802.1X Authentication** pour regarder le paramètre *WPA Key Data*.
- Maintenant, essayons de déchiffrez le mot de passe correspondant au Handshake capturée pour obtenir le mot de passe du réseau Wifi. 
Nous allons exploiter un mini-dictionaire de *131MB*: `wget https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt`.
- Repassez vos carte wifi en mode normal et arretez tout monitoring. Puis exécuter: `aircrack-ng log-01.cap -w rockyou.txt` 
Si le mot de passe est contenu dans le fichier *rockyou.txt*, alors il sera découvert. Biensur il existe des dictionnaires plus robustes mais il est aussi possible de créer le votre.


## Couvrir ses traces 

Après votre aventure sur le point d'accès, il s'avère que vous avez laissé trop de traces sur votre station qui peuvent vous porter préjudice dans l'avenir. 
Il vous faut nettoyer vos traces en supprimant les logs de votre système.


- Pour vous, un fichier log représente quoi ?
- Où sont sauvegardés la plupart des logs dans une distribution Linux ? Et Windows ? Et MacOs ?
- Localisez et supprimer les logs qui concernent les démarrages de votre ordinateur, l'historique de vos commandes et les différentes connexions sur votre PC.
- Avez-vous Une méthode pour éviter de générer trop de logs sur votre station lors d'une attaque ? Décrivez là.

## Ransomware 

Finalement, l'entreprise ayant fait appel à vous se retourne contre vous et réalise une campagne de diffamation vous concernant.
Afin de leur donner une bonne leçon, vous voulez profitez d'un accès dérobé à leur service pour installer un ransomware personalisé.
Concrètement votre ransomware, doit être sous la forme d'un script (dans le langage de votre choix, $bash$ de préférence pour ne pas avoir à installé des dépendences chez la cible).
Votre ransomware entrera dans chaque dossier du repertoire et : 

    - Pour chaque dossier, changer le nom du dossier en appliquant l'algorithme de Cesar à trois décalages, donc un dossier avec le nom DJOB deviendra GMRE.
    - Pour chaque fichier, si le contenu est textuel, appliquer l'algorithme de Cesar à cinq décalages.

Par précaution pour vous-même, écrivez l'antidote de votre ransomware afin de vous prémunir en cas d'une attaque réflexive ou en marge de négotiation. 
Appelez vos deux programmes (virus et antidote) \\NOM\_PRENOM\_VIRUS.sh et NOM\_PRENOM\_CORR.sh où NOM et PRÉNOM correspondent à vos noms et prénoms.

D'après vous, quelles sont les principes de sécurités qu'il faut appliquer pour se prémunir de ce type d'attaques ? 


