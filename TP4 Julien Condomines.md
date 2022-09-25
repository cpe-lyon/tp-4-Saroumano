# TP-4 Julien CONDOMINES


## Exercice 1. Commandes de base 


#### 1. Commencez par mettre à jour votre système avec les commandes vues dans le cours.

Pour mettre à jour notre système, il nous faut utiliser les deux commandes `apt update && apt upgrade -y`.

#### 2. Créez un  alias  “maj” de la ou des commande(s) de la question précédente. Où faut-il enregistrer cet  alias pour qu’il ne soit pas perdu au prochain redémarrage ?

Pour créer un alias sous Linux, il faut utiliser la commande `alias`, on rentre donc la commande `alias maj="apt update && apt upgrade -y"`. Afin qu'elle ne soit pas perdu au redémarrage, il faut la stocker dans un fichier particulier **.bashrc**. Cependant, il est risqué de modifier le fichier trop souvent on peut donc les stocker dans une sous parties de ce même fichier que le va créer appelé **.bash_aliases**.

#### 3. Utilisez le fichier  /var/log/dpkg.log  pour obtenir les 5 derniers paquets  installés  sur votre machine.

Afin de savoir les 5 derniers paquets installés sur notre machine, il faut se rendre dans le fichier **dpkg.log**, celui-ci contenant la liste de ce que l'on recherche. On va ensuite utiliser la commande `tail` afin de les trouver :

`grep install /var/log/dpkg.log | tail -n 5`

#### 4. Listez les derniers paquets qui ont été installés explicitement avec la commande  apt install


Afin de savoir les derniers paquets qui ont été installés explicitement avec la commande **apt install**, il faut utiliser la commande ``grep install /var/log/dpkg.log | dpkg -S apt update``dans le fichier regroupant les installations pour avoir comme résultat : 
`update-notifier-common: /usr/share/locale/kn/LC_MESSAGES/update-notifier.mo
update-notifier-common: /usr/lib/update-notifier/apt-check
linux-modules-extra-5.15.0-47-generic: /lib/modules/5.15.0-47-generic/kernel/drivers/platform/x86/intel/wmi/intel-wmi-sbl-fw-update.ko
linux-headers-5.15.0-47: /usr/src/linux-headers-5.15.0-47/include/linux/rcupdate.h
libpam-modules-bin: /sbin/unix_update`

#### 5. Utilisez les commandes  dpkg  et  apt  pour compter de deux manières différentes le nombre de total de  paquets installés sur la machine (ne pas hésiter à consulter le manuel !). Comment explique-t-on la  (petite) différence de comptage ? Pourquoi ne peut-on pas utiliser directement le fichier  dpkg.log  ?
Il faut tout d'abord repérer la commande `wc -l` qui permet de compter les lignes et vus qu'un paquet est renseigné comme une ligne on peut les compter de cette manière.

 Afin de compter le nombre de paquets présents sur la machine avec **dpkg**, il faut utiliser la commande `dpkg --list | wc --lines` ce qui va nous donner 611 en résultat. Avec la commande **apt** on va utiliser la commande `apt list --installed | wc -l` qui cette fois ci va nous donner 607 en résultat.
 
 La différence entre les deux comptes se situe dans les lignes d'informations qui sont prises en compte par **dpkg** mais pas part **apt**.  
 
#### 6. Combien de paquets sont disponibles en téléchargement sur les dépôts Ubuntu ?

Afin de savoir le nombre de paquet disponible en téléchargement, on va utiliser la commande `apt list | wc -l` . Grâce à elle, on peut voir qu'il y a 68744 paquets différents actuellement installable sur les dépôts Ubuntu. 

#### 7. A quoi servent les paquets  glances,  tldr  et  hollywood  ? Installez-les et testez-les.

Si l'on souhaite avoir des information sur un paquet on peut utiliser la commande `apt show nom_de_la_commande`, c'est ce que nous allons utiliser pour cet exercice. 

Tout d'abord, le paquet **glances** est un outil de surveillance basé sur la bibliothèque Curses. Celui-ci va surveiller le processeur, la charge système, la mémoire, la bande passante réseau, les E/S de disque, l'utilisation du disque ainsi que les processus.

Ensuite, le paquet **tldr** qui lui va permettre de repérer l'ensemble des pages tldr qui sont exécuté par notre machine.

Enfin, le paquet **hollywood** va lui simuler un situation de hacking comme on peut en voir dans les films hollywoodiens, avec de nombreuse fenêtres ouvertes qui défilent très vite. 

#### 8. Quels paquets proposent de jouer au sudoku ?

Si l'on souhaite jouer au sudoku, on peut rechercher grâce à la commande `apt search ` si un tel paquet est disponible au téléchargement. 
On peut donc voir qu'il y a plusieurs retour favorables tel que : 
`fltk1.1-games` / `gnome-sudoku` / `hitori` ou encore `fltk1.3-games`

## Exercice 2

#### A partir de quel paquet est installée la commande ls ? Comment obtenir cette information en une seule commande, pour n’importe quel programme ? Utilisez la réponse à cette question pour écrire un script appelé origine-commande (sans l’extension  .sh) prenant en argument le nom d’une commande, et indiquant quel paquet l’a installée.

Afin de savoir à partir de quel paquet est installée la commande **ls**, on va tout d'abord utilisé la commande **which**, qui permet de savoir le chemin d'exécution d'une commande. On va enfin indiquer que l'on veut transformer la commande **dpkg** qui n'est pas sur la bonne sortie en argument avec la commande xargs. En effet, pour les commandes qui réclament un argument tel que **rm**, il est nécessaire d'attribuer des arguments à la suite de la commande , auquel cas celle ci ne fonctionnera pas. C'est pourquoi on va utiliser **xargs** qui va prendre l'entré qu'elle recoit de la commande `which -a "hollywood"`  précédant le pype  et l'envoyé à la suite de la commande `dpkg -S`  qiu nécessite des arguments tout come **rm** :

`which -a "$1" | xargs dpkg -S 2>/dev/null | cut -f1 -d:`
`coreutils`

On va donc maintenant écrire un script appelé **origine_commande** qui prendra en compte le nom d'une commande et indiquera quel paquet la installé :
```bash
#!/bin/bash
which -a "$1" | xargs dpkg -S 2>/dev/null | cut -f1 -d:

```
## Exercice 3 :
#### Écrire une commande qui aﬀiche “INSTALLÉ” ou “NON INSTALLÉ” selon le nom et le statut du package  spécifié dans cette commande :

La commande && ne s’exécute que si un succès 
La commande || que si se finit sur un échec 
Les messages d'erreur ne sont jamais transmis dans le pipe

La commande **grep** est utilisé lorsque l'on veut filtrer une partie de texte, on sait ici que si un paquet est installé, alors celui ci sera précédé de la mention *ii*, on peut donc limiter la recherche grâce à cela. 

Afin de créer la commande demandé, nous allons utiliser une propriété de la commande **dpkg** plus particulièrement la **-l NOM_PAQUET** qui va nous indiqué si le paquet rentré en paramètre est installé sur notre machine ou non. 

Enfin, afin de rendre le résultat plus lisible, il va falloir rediriger les différentes sorties afin d'éliminer le superflu tel que le message d'erreur de la commande **dpkg** si il a lieu ou bien le résultat de la commande **grep** qui va afficher l'ensemble des données de l’installation. 
```
dpkg -l "hollywood" 2>/dev/null | grep "^ii" >/dev/null && echo "installé" || echo "non installé" 
installé
dpkg -l "regnoer" 2>/dev/null | grep "^ii" >/dev/null && echo "installé" || echo "non installé" 
non installé
```

## Exercice 4
#### Lister les programmes livrés avec coreutils. En particulier, on remarque que l’un d'eux se nomme [. De quoi s’agit-il ?

Si l'on souhaite lister les programmes livrés avec coreutils, on doit utiliser la commande `dpkg -L coreutils`.

Le programme **[** est un programme de test, il nous permet décrire des conditions dont on peut voir le résultat à l'aide des opérateurs `||` et `&&`.

## Exercice 5 
#### Installez les paquets emacs et lynx à l’aide de la version graphique d’aptitude  (et prenez deux minutes pour vous renseigner et tester ces paquets). 
