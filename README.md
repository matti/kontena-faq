# Kontena FAQ

## CLI

### How can I delete multiple nodes / stacks / services ?

With `xargs`, see https://github.com/kontena/kontena/issues/2410

## master

### If you have 3 masters how do the nodes connect to all of them?

Through a LB, see https://www.kontena.io/docs/getting-started/installing/ha-master.html

## registry

### Can I declare an insecure Docker private registry (HTTP) in a Kontena YML stack file?

No, Docker requires registries to use HTTPS.

## containers

### Connect to a specific instance of docker container?

    kontena service exec --instance NUM

## users / rights

### Can a user with "grid_admin" role create grids and nodes?

Can't create grids, but can create nodes.
