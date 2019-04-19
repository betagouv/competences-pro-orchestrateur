# Compétences pro orchestration

## Configuration

Pour accéder à l'api, il faut rajouter une entrée dans votre fichier `/etc/hosts` :

    127.0.0.1 api.localhost

## Usage

Pour démarrer l'environnement de développement :

Pre-requis Mac : `docker-sync` (install avec `gem install docker-sync`)


    git clone git@github.com:betagouv/competences-pro.git client
    git clone git@github.com:betagouv/competences-pro-serveur.git serveur

    ./script/demarre

Et se rendre sur http://localhost:4000

## Déploiement

Pour déployer l'application, un *playbook* [ansible][] est fourni.

### Pre-requis de la machine cible

La machine cible doit avoir *docker*, *docker-compose* et *git* installés.
De plus, un fichier de configuration doit également être présent.

Un fichier `.env.serveur.prod`. `SECRET_KEY_BASE` et `DATABASE_URL` devraient être modifiés.

    SECRET_KEY_BASE=ICI_METTRE_UNE_VRAI_SECRET_KEY_BASE
    RAILS_SERVE_STATIC_FILES=true
    RAILS_LOG_TO_STDOUT=true
    DATABASE_URL=postgres://postgres:@postgres/postgres

### Sur la machine qui fait le déploiement

Pre-requis : `ansible`

Créer un fichier `hotes` en rajoutant l'adresse du serveur déployé :

    example.net:22

Puis lancer le déploiement :

    ansible-playbook --inventory=hotes deploiement.yml

## Licence

Ce logiciel et son code source sont distribués sous [licence AGPL](https://www.gnu.org/licenses/why-affero-gpl.fr.html).

[ansible]: https://www.ansible.com/
