# Element Personal Hub (Umbrel)

**App ID:** `abyoai-element-hub`  
**Version:** `0.1.0`  
**Status:** Initial community-app template — review the security notes before using it with real conversations.

This package contains Element Web, Matrix Synapse, PostgreSQL, and an Nginx gateway behind Umbrel's `app_proxy`.

## Important before installation

1. **Change the PostgreSQL password.** In `docker-compose.yml`, replace the default value `CHANGE_THIS_PASSWORD_BEFORE_INSTALL` with a long random password. Keep it private. The Synapse database configuration must use the same password; see the configuration note below.
2. This first template uses `matrix.home.arpa` as its permanent Matrix server name. Matrix IDs will look like `@alice:matrix.home.arpa`. Do not change this after creating accounts or rooms.
3. Element is configured for `http://umbrel.local:8085`. If Umbrel assigns/uses a different address or port, update `element-config.json` before installation.
4. **LAN-only is not the same as encrypted transport.** This template is HTTP-only and is not suitable for sensitive conversations on an untrusted/shared LAN. Do not expose it to the internet, do not configure router port forwarding, and do not use a public tunnel. HTTPS should be added before relying on it for sensitive use.
5. Federation is not exposed by this package. Do not add a public federation listener without deliberately redesigning the network and security configuration.

## First-run limitation / Synapse configuration

The Synapse image needs a generated `homeserver.yaml` with PostgreSQL settings. This starter package deliberately does not claim to be a fully validated one-click production release. Before first start, initialize Synapse once and configure its database:

```bash
sudo mkdir -p ~/umbrel/app-data/abyoai-element-hub/synapse
sudo docker run --rm \
  -e SYNAPSE_SERVER_NAME=matrix.home.arpa \
  -e SYNAPSE_REPORT_STATS=no \
  -v ~/umbrel/app-data/abyoai-element-hub/synapse:/data \
  matrixdotorg/synapse:v1.165.0 generate
```

Then edit `~/umbrel/app-data/abyoai-element-hub/synapse/homeserver.yaml` and set the `database:` section to:

```yaml
database:
  name: psycopg2
  args:
    user: synapse
    password: YOUR_POSTGRES_PASSWORD
    database: synapse
    host: abyoai-element-hub-postgres-1
    cp_min: 5
    cp_max: 10
```

The actual Compose-generated container hostname may vary. Check it with `sudo docker ps --format '{{.Names}}'` and use the PostgreSQL service container's DNS name (`postgres`) when both services share the Compose network. In this package, `postgres` is the Compose service name and should normally resolve from Synapse.

Also ensure these settings exist in `homeserver.yaml`:

```yaml
enable_registration: false
enable_metrics: false
```

Then install/start the app from the AbyoAI community store. The first admin account should be created locally from inside the Synapse container using `register_new_matrix_user` with a registration shared secret configured in `homeserver.yaml`; do not enable public registration.

## App store upload

Copy the entire `abyoai-element-hub` folder into the root of:

`https://github.com/AbyoAI/Abyo-s-UmbrelOS-Apps`

Do not rename the folder. The folder name must match the `id` in `umbrel-app.yml`.

After pushing, refresh the AbyoAI app store in Umbrel.

## Known next steps before calling this stable

- Automate first-run Synapse configuration and secure secret generation.
- Verify the current Umbrel app proxy URL/origin on the target UmbrelOS release.
- Add trusted local HTTPS and test browser security requirements.
- Validate image architecture support on the target Umbrel hardware.
- Run the Umbrel app-store linter and a clean install/upgrade test.
