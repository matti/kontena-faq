# Kontena FAQ

## kontena/lb

### Can I run have network_mode: host and still link to lb?

no, only containers with a `io.kontena.container.overlay_cidr` get registered, and `network_mode: host` containers don't get that label https://github.com/kontena/kontena/issues/2741

### Why isn't my link working with a custom image named my-kontena-lb

The magic relies on the name `kontena/lb`, you can not wrap it.

## Agent

### Agent logs warn about LogWorker queue size

`WARN -- Kontena::Workers::LogWorker: queue size is 1600`

Agent can not flush logs fast enough to master, queue size is capped to 1600

## CLI

### How can I delete multiple nodes / stacks / services ?

With `xargs`, see https://github.com/kontena/kontena/issues/2410


## Docker

### Does kontena support the current latest docker version?

Probably not, see: See https://www.kontena.io/docs/getting-started/system-requirements.html

## Stacks

### Can I expose multiple services in the same stack?

No, but you can install a LB in the stack and expose that.

Alternatively when you expose a service (eg. "node"), all instances are accessible as `stack-N`

## Services

### Does min_health work for services that do not have a healthcheck?

yes.

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
