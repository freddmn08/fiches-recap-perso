# OAuth 2 avec Symfony

Package `KnpUOAuth2ClientBundle`, configuration et installation dans le cas de GitHub

## Installation

`composer require knpuniversity/oauth2-client-bundle`

## Enregistrement d'une nouvelle application OAuth

Création d'une appli OAuth afin d'accéder à GitHub API : [ici](https://github.com/settings/applications/new)

* Application name : `tick'ateliers-test`

* Homepage URL : `http://freddmn.fr/tick-ateliers`

* Callback URL : `http://freddmn.fr/tick-ateliers/callback`

Client ID : `3c87bf3af54fc9d6274e`

Client Secret : `948ca85669517bf3e7a1a690347b64043d45c221`

## Configuration du client

Dans le cas de GitHub OAuth2 server

* Téléchargement de la librairie client : `composer require league/oauth2-github`

* Configuration du provider : créer un nouveau fichier `config/packages/knpu_oauth2_client.yaml`

```
# config/packages/knpu_oauth2_client.yaml
knpu_oauth2_client:
    clients:
        # the key "facebook_main" can be anything, it
        # will create a service: "knpu.oauth2.client.facebook_main"
        facebook_main:
            # this will be one of the supported types
            type: facebook
            client_id: '%env(OAUTH_FACEBOOK_ID)%'
            client_secret: '%env(OAUTH_FACEBOOK_SECRET)%'
            # the route that you're redirected to after
            # see the controller example below
            redirect_route: connect_facebook_check
            redirect_params: {}
            graph_api_version: v2.12
```

* Configuration des credentials dans le `.env` :

```

```