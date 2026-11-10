# N3DSRadio v1.0

N3DSRadio est une version améliorée de **3DSRadio**, le projet open source
original de signxer : [github.com/signxer/3DSRadio](https://github.com/signxer/3DSRadio).
Cette version conserve les crédits et la licence MIT du projet d’origine,
tout en ajoutant des améliorations et des fonctions pour la Nintendo 3DS et
la New Nintendo 3DS.

## Fonctionnalités

- Interface en français ou en anglais, avec choix de la langue au premier
  lancement
- Navigation sur les deux écrans, au tactile et aux boutons
- Parcours des genres, stations populaires et recherche par nom
- Catalogue par pays et territoire (liste Radio Browser complète, stations
  les plus populaires en premier)
- Navigation rapide par pages avec L/R dans les listes
- Écran d’accueil simplifié et lecteur mieux adapté aux deux écrans
- Affichage plus net du favicon de la station (PNG/JPEG/ICO, texture 128 px),
  téléchargé en arrière-plan sans bloquer le décodage audio; un changement
  rapide de station annule l’ancienne demande et charge la dernière
- Réglages graphiques et tampon audio adaptés au modèle : mode complet et
  grand tampon par défaut sur New 3DS, effets réduits et avertissement sur
  3DS classique
- Informations sur les stations : codec détecté, débit, pays, langue, tags,
  état communiqué par le catalogue et page d’accueil
- Lecture MP3, AAC direct et prise en charge des flux HLS AAC en segments
  ADTS ou MPEG-TS; les flux sans codec déclaré sont détectés automatiquement
  lorsqu'ils sont MP3 ou AAC
- Désactivation des métadonnées ICY lors du téléchargement audio pour éviter
  qu'elles soient envoyées au décodeur comme des données sonores
- Commandes de lecture, volume et choix de la taille du tampon audio
- Fichier de langue enregistré sur la carte SD, avec reprise de l’ancien
  réglage `3DSRadio.cfg`

Les textes de sélection restent contrastés, y compris sur le fond clair de
surbrillance; les caractères non pris en charge par la police sont remplacés
par une version lisible plutôt que par un glyphe manquant.

Le codec annoncé par le catalogue est complété par des indices présents dans
l’adresse du flux (par exemple `.mp3`, `.aac` ou `.m3u8`). Le statut « actif »
est celui rapporté par radio-browser.info et ne garantit pas que le flux soit
disponible au moment de l’écoute. Le logo de l’écran de lecture vient du champ
favicon de Radio Browser; si ce lien manque ou échoue, l’application essaie
`/favicon.ico` sur le site officiel. Le chargement est limité à 1 Mio par image
et privilégie la demande la plus récente pour que les changements de station
ne bloquent pas l’interface. Les images PNG, JPEG et ICO sont redimensionnées
avec filtrage; certaines stations n'ont pas de logo ou utilisent un format
non pris en charge.

Le parcours par pays récupère la liste de pays et territoires du catalogue
(actuellement plus de 200 entrées), puis présente les stations MP3/AAC
déclarées actives en tête de liste. Le catalogue et les flux changent
continuellement; l'application ne peut pas garantir que chaque station sera
disponible ni prendre en charge les formats non décodés, notamment Ogg/Opus,
WMA ou les playlists HLS chiffrées.

Sur New Nintendo 3DS/New Nintendo 2DS XL, l’application sélectionne le grand
tampon et conserve les effets visuels complets. Sur les modèles classiques,
elle réduit certains effets et affiche un avertissement : la fluidité peut
être moindre. Le choix du tampon reste modifiable avec SELECT dans le menu.

## Installer

1. Copier `N3DSRadio.3dsx` dans le dossier `/3ds/` de la carte SD.
2. Lancer N3DSRadio depuis le Homebrew Launcher.
3. Vérifier que le Wi-Fi est activé, puis choisir une station.

Dans une liste, utiliser Haut/Bas pour se déplacer, L/R pour changer de page,
A pour écouter et Y pour afficher les détails. Dans l’écran de lecture, A
met en pause ou relance le flux, B arrête la lecture et revient à la liste,
et X/Y règlent le volume. Les quatre commandes tactiles sont disposées en
deux rangées pour garder leurs libellés dans les boutons.

## Compiler

Prérequis : devkitPro avec devkitARM, bannertool, makerom et Pillow pour
Python. Installer les paquets devkitPro nécessaires :

```sh
sudo dkp-pacman -S 3ds-dev 3ds-libcurl 3ds-mbedtls 3ds-libpng 3ds-libjpeg-turbo devkitpro-devtools
```

Depuis la racine du dépôt :

```sh
# Télécharger les certificats CA pour les flux HTTPS
./scripts/get_cacert.sh

# Compiler le fichier 3DSX
make

# Créer un CIA (nécessite bannertool et makerom)
make cia
```

La bannière, l’icône et le son fournis à la racine (`baniere.jpg`, `icone.png`
et `sons.wav`) sont convertis automatiquement pour le CIA. La compilation
produit `N3DSRadio.3dsx` et, avec `make cia`, `N3DSRadio.cia`.

## Formats audio et limites

- MP3 décodé avec minimp3
- AAC direct ADTS/ADIF (AAC LC et HE-AAC) décodé avec Helix
- HLS AAC non chiffré, avec segments MPEG-TS de 188 octets ou segments ADTS
- Réinitialisation du démultiplexeur sur les discontinuités HLS et retrait du
  remplissage MPEG-TS après les paquets audio AAC
- Les playlists HLS chiffrées, fMP4, par plages d’octets, AAC-LATM et les
  autres codecs ne sont pas pris en charge.
- La lecture dépend du format annoncé, de l’accessibilité de chaque station
  et de la connexion Internet. La lecture de toutes les radios ne peut pas
  être garantie.

Le choix de langue est enregistré dans
`sdmc:/3ds/N3DSRadio.cfg`. Si l’ancien fichier `sdmc:/3ds/3DSRadio.cfg` existe,
son réglage est repris lors du lancement.

## Origine, version et crédits

**N3DSRadio v1.0** est une version améliorée du projet d’origine
[3DSRadio de signxer](https://github.com/signxer/3DSRadio), qui reste
disponible à son adresse GitHub. Les améliorations de cette version incluent
la localisation, la recherche, la détection de codec renforcée, le décodage
AAC, une prise en charge limitée de HLS AAC et des corrections de stabilité
et de navigation.

Le code d’origine conserve ses crédits et sa licence MIT. Cette version
s’inspire également de l’architecture audio de
[ClouDS-Music](https://github.com/cadl/ClouDS-Music).

## Licences des composants

Le cœur C Helix AAC de `external/helix-aac/` est soumis à la RealNetworks
Public Source License (RPSL). Sa licence et sa provenance sont incluses
avec ses sources. La RPSL prévoit des conditions particulières pour la
distribution d’une œuvre combinée ; consulter cette licence avant de
redistribuer le programme. L’écran **À propos** affiche également la mention
Helix requise.

## Remerciements

- [signxer/3DSRadio](https://github.com/signxer/3DSRadio), projet d’origine
- [radio-browser.info](https://www.radio-browser.info/), catalogue de stations
- [devkitPro](https://devkitpro.org/), outils homebrew
- [ClouDS-Music](https://github.com/cadl/ClouDS-Music), références
  d’architecture

Le reste du code N3DSRadio est distribué sous licence MIT ; voir le fichier
de licence du [projet d’origine](https://github.com/signxer/3DSRadio).
"# n3dsradio" 
"# n3dsradio" 
