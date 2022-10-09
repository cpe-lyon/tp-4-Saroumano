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

Après installation, on peut voir que Emacs est un éditeur de texte et que Lynx est un navigateur internet qui fonctionne tout deux dans un terminal.

## Exercice 6 
#### 1) Installer la version Oracle de Java (avec l’ajout des PPA)
#### 2) Vérifiez qu’un nouveau fichier a été créé dans /etc/apt/sources.list.d. Que contient-il ?

Le fichier contient les réferences vers les logiciels de gestion de paquets :
deb http://ppa.launchpad.net/linuxuprising/java/ubuntu focal main
#deb-src http://ppa.launchpad.net/linuxuprising/java/ubuntu focal main

## Exercice 7

## Exercice 8
### Création d’un paquet Debian avec dpkg-deb
#### 1) Dans le dossier scripts créé lors du TP 2, créez un sous-dossier origine-commande où vous créerez un sous-dossier DEBIAN, ainsi que l’arborescence usr/local/bin où vous placerez le script écrit à l’exercice 2

Pour créer le sous dossier origine-commande, ainsi que le sous dossier DEBIAN et la bonne arborescence il faut réaliser ces commandes :
```
mkdir origin-commande
mkdir origin-commande/DEBIAN
mkdir origin-commande/usr
mkdir origin-commande/usr/local
mkdir origin-commande/usr/local/bin
touch origin-commande/usr/local/bin/script.sh
``` 
#### 2) Dans le dossier DEBIAN, créez un fichier control avec les champs suivants :

On créer le fichier puis on y ajoute les différents champs comme demandés.

#### 3) Revenez dans le dossier parent de origine-commande (normalement, c’est votre $HOME) et tapez la commande suivante pour construire le paquet :

`dpkg-deb: building package 'origine-commande' in 'origine-commande.deb'.`

Le pacquet est crée !

### Création du dépôt personnel avec reprepro
#### 1) Dans votre dossier personnel, commencez par créer un dossier repo-cpe. Ce sera la racine de votre dépôt

Pour créer le dossier repo-cpe, on se rend tout d'abord dans notre dossier personnel puis on utilise la commande `mkdir repo-cpe`

#### 2) Ajoutez-y deux sous-dossiers : conf (qui contiendra la configuration du dépôt) et packages (qui contiendra nos paquets)
On créer les 2 sous dossiers en rentrant les commandes: `mkdir /repo-cpe/conf` et `mkdir /repo-cpe/packages`

#### 3) Dans conf, créez le fichier distributions suivant :

On créer le fichier et on rentre les différents champs:
```
Origin: Un nom, une URL, ou tout texte expliquant la provenance du dépôt
Label: Nom du dépôt
// Suite: stable
Codename: focal #!! A MODIFIER selon la distribution cible !!
Architectures: i386 amd64 #(architectures cibles)
Components: universe #(correspond à notre cas)
Description: Une description du dépôt
``` 
#### 4) Dans le dossier repo-cpe, générez l’arborescence du dépôt avec la commande

La commande `reprepro -b . export`nous génère donc notre arborescence du dépôt.

#### 5) Copiez le paquet origine-commande.deb créé précédemment dans le dossier packages du dépôt, puis, à la racine du dépôt, exécutez la commande

On va utiliser la commande : `cp origine-commande.deb ../repo-cpe/packages` pour copier le paquet depuis le dossier script puis on va exécuter la commande : `reprepro -b . includedeb cosmic origine-commande.deb`

#### 6) Il faut à présent indiquer à apt qu’il existe un nouveau dépôt dans lequel il peut trouver des logiciels. Pour cela, créez (avec sudo) dans le dossier /etc/apt/sources.list.d le fichier repo-cpe.list contenant :

On se rend dans /etc/apt/sources.list.d avec la commande cd, puis on crée le fichier avec la commande sudo touch repo-cpe.list et on l'édite avec sudo nano.

#### 7) Lancez la commande sudo apt update.

### Signature du dépôt avec GPG
#### 1) Commencez par créer une nouvelle paire de clés avec la commande :

On crée la paire de clés avec la commande `gpg --gen-key`, puis on rentre un nom, une adresse mail et une passphrase à retenir.

#### 2) Ajoutez à la configuration du dépôt fichier distributions la ligne suivante :

On retourne dans le dossier conf avec cd /repo-cpe/conf et on édite le fichier distributions pour rajouter SignWith: yes

#### 3) Ajoutez la clé à votre dépôt :

On se met dans le dossier repo-cpe puis on tape la commande : `reprepro --ask-passphrase -b . export`

#### 4) Ajoutez votre clé publique à votre dépôt avec la commande :

Toujours dans le dossier on tape la commande `gpg --export -a "auteur" > public.key`

#### 5) Enfin, ajoutez cette clé à la liste des clés fiables connues de apt 

Puis pour finir on ajoute cette clé dans la liste apt avec la commande `sudo apt-key add public.key`

