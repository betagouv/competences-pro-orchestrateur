# eva orchestration

Pour voir les autres projets de eva:

- [la partie cliente](https://github.com/betagouv/eva)
- [la partie serveur](https://github.com/betagouv/eva-serveur)
- [le site web](https://github.com/betagouv/eva-www)

## Configuration

Pour accéder à l'api, il faut rajouter une entrée dans votre fichier `/etc/hosts` :

    127.0.0.1 api.localhost

## Usage

Pour démarrer l'environnement de développement :

Pre-requis Mac : `docker-sync` (install avec `gem install docker-sync`)


    git clone git@github.com:betagouv/eva.git client
    git clone git@github.com:betagouv/eva-serveur.git serveur

    ./script/demarre

Et se rendre sur http://localhost:4000

## Déploiement

Pour déployer l'application, des *playbooks* [ansible][] sont fournis.

Pre-requis : `ansible`

Créer un fichier `hotes` en rajoutant l'adresse du serveur déployé :

    example.net:22

### Configuration initiale de la machine

La configuration initiale s'attend a pouvoir se connecter en root avec une clef publique.

Lancer cette commande ansible:

    ansible-playbook --inventory=hotes \
                     --extra-vars="utilisateur_sudo=utilisateursudo" \
                     --extra-vars="utilisateur_deploiement=utilisateur" \
                     --extra-vars="prepare_machine=true" \
                     installation.yml

Cela fera principalement :
 - fermeture de l'accès à l'utilisateur root,
 - création de 2 utilisateurs : le premier pour les opérations d'administration de la machine et le deuxième pour déployer l'application.
 - installation de docker

Lors des opérations de maintenance ultérieur, l'accès root étant fermé, il
faudra utiliser la commande suivante :

    ansible-playbook --inventory=hotes \
                     --extra-vars="utilisateur_sudo=utilisateursudo" \
                     --extra-vars="utilisateur_deploiement=utilisateur" \
                     installation.yml

### Déploiement de traefik

Nous utilisons [Traefik][] pour exposer les différents containers vers l'extérieur. Vous pouvez personaliser sa configuration en éditant le fichier `traefik.toml`.

Puis lancer le déploiement (avec l'utilisateur distant `utilisateur`):

    ansible-playbook --user=utilisateur --inventory=hotes traefik.yml

### Déploiement du site site web vitrine

    ansible-playbook --user=utilisateur --inventory=hotes site.yml

### Déploiement de l'application

#### Pre-requis de la machine cible

La machine cible doit avoir *docker*, *docker-compose* et *git* installés.
De plus, un fichier de configuration doit également être présent.

Un fichier `.env.serveur.prod`. `SECRET_KEY_BASE` et `DATABASE_URL` devraient être modifiés.

    SECRET_KEY_BASE=ICI_METTRE_UNE_VRAI_SECRET_KEY_BASE
    RAILS_SERVE_STATIC_FILES=true
    RAILS_LOG_TO_STDOUT=true
    DATABASE_URL=postgres://postgres:@postgres/postgres
    JETON_SERVEUR_ROLLBAR=
    EMAIL_DEFAULT_FROM=
    SMTP_ADDRESS=
    SMTP_PORT=
    SMTP_USER_NAME=
    SMTP_PASSWORD=
    SMTP_AUTHENTICATION=
    SMTP_TLS=

    ## information de connexion pour la sauvegarde BDD
    POSTGRES_USER=postgres
    POSTGRES_PASSWORD=
    POSTGRES_DB=postgres

#### Sur la machine qui fait le déploiement

Puis lancer le déploiement (avec l'utilisateur distant `utilisateur`):

    ansible-playbook --user=utilisateur --inventory=hotes deploiement.yml

Il est également possible de personnaliser les chemins, les branches ou les hôtes :

    ansible-playbook --inventory=hotes --user=utilisateur --extra-vars="chemin_racine={{ansible_env.HOME}}/preprod hote_client=preprod.eva.beta.gouv.fr hote_serveur=apipreprod.eva.beta.gouv.fr" deploiement.yml

Voici la liste des variables typique à personnaliser :

- `chemin_racine`
- `version_orchestrateur`
- `version_serveur`
- `version_client`
- `hote_client`
- `hote_serveur`
- `hote_metabase`

**Attention : il faut impérativement spécifier un `chemin_racine` différent de `{{ ansible_env.HOME }}` si on souhaite déployer d'autres versions que `master`. Dans le cas contraire, l'application pointera vers la base de données de production, avec tous les risques de corruption que cela peut entraîner.**

## Licence

Ce logiciel et son code source sont distribués sous [licence AGPL](https://www.gnu.org/licenses/why-affero-gpl.fr.html).

[ansible]: https://www.ansible.com/
[traefik]: https://traefik.io/
