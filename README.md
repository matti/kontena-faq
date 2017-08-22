# Kontena FAQ


## registry

### Can I declare an insecure Docker private registry (HTTP) in a Kontena YML stack file?

No, Docker requires registries to use HTTPS.

## containers

### Connect to a specific instance of docker container?

    kontena service exec --instance NUM

### What's exit code 143?

Container just exited with the code 143

## users / rights

### Can a user with "grid_admin" role create grids and nodes?

Can't create grids, but can create nodes.
