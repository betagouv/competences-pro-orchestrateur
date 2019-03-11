# Compétences pro orchestration

## Configuration

Pour accéder à l'api, il faut rajouter une entrée dans votre fichier `/etc/hosts`:

    127.0.0.1 api.localhost

## Usage

Pour démarrer l'environnement de développement:

    git clone git@github.com:betagouv/competences-pro.git client
    git clone git@github.com:betagouv/competences-pro-serveur.git serveur

    ./script/demarre

Et se rendre sur http://localhost:4000

## Déploiement

Pour déployer l'application, un *playbook* [ansible][] est fourni. La machine cible doit avoir docker, docker-compose et git installé.

Créer un fichier `hotes` en rajoutant l'adresse du serveur déployé:

    example.net:22

Puis lancer le déploiement:

    ansible-playbook --inventory=hotes deploiement.yml

## Licence

Ce logiciel et son code source sont distribués sous [licence AGPL](https://www.gnu.org/licenses/why-affero-gpl.fr.html).

[ansible]: https://www.ansible.com/
