# Installation

## Pré-installation

Avant de lancer le docker compose, il faut générer le fichier de configuration via la commande suivante :

```bash
docker run -it --rm -v ./synapse_data:/data -e SYNAPSE_SERVER_NAME=matrix.djenkoit.fr -e SYNAPSE_REPORT_STATS=no docker.io/matrixdotorg/synapse:latest generate
```

## Matrix

Modification du fichier `homeserver.yaml` :

```yaml
database:
  # name: sqlite3
  # args:
  #   database: /data/homeserver.db
  name: psycopg2
  args:
    user: synapse
    password: aComplexPassphraseNobodyCanGuess
    database: synapse
    host: synapse_db
    cp_min: 5
    cp_max: 10
```

### SSO avec Authentik :

```yaml
oidc_providers:
  - idp_id: authentik
    idp_name: authentik
    discover: true
    issuer: "https://authentik.company/application/o/app-slug/"
    client_id: "*client id*"
    client_secret: "*client secret*"
    scopes:
      - "openid"
      - "profile"
      - "email"
    user_mapping_provider:
      config:
        localpart_template: "{{ user.preferred_username }}"
        display_name_template: "{{ user.name|capitalize }}"
```

## Element

Modifier la configuration pour avoir la connexion par défaut sur votre serveur :

```
data/config.json
```

```json
{
  "default_server_config": {
    "m.homeserver": {
      "base_url": "https://matrix.djenkoit.fr",
      "server_name": "matrix.djenkoit.fr"
    }
  }
}
```

## Finalisation

Lancer le docker compose :

```bash
docker compose up -d
```