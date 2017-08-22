# Kontena FAQ

## CLI

### How can I delete multiple nodes / stacks / services ?

With `xargs`, see https://github.com/kontena/kontena/issues/2410


## Docker

### Does kontena support the current latest docker version?

Propably not, see: See https://www.kontena.io/docs/getting-started/system-requirements.html

## Stacks

### Can I expose multiple services in the same stack?

No, but you can install a LB in the stack and expose that.

## Master

### If you have 3 masters how do the nodes connect to all of them?

Through a LB, see https://www.kontena.io/docs/getting-started/installing/ha-master.html

## Registry

### Can I declare an insecure Docker private registry (HTTP) in a Kontena YML stack file?

No, Docker requires registries to use HTTPS.

## Containers

### Connect to a specific instance of docker container?

    kontena service exec --instance NUM

## Users / Rights

### Can a user with "grid_admin" role create grids and nodes?

Can't create grids, but can create nodes.
