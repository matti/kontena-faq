# Kontena FAQ

## what's the encryption scheme used by Kontena overlay network? (weave)

IPSec/ESP using AES in GCM mode
more details here: https://github.com/weaveworks/weave/blob/master/docs/fastdp-crypto.md

## Is the traffic encrypted between nodes in the same region that communicate with their private IP ?

by default yes. you can disable encryption for a certain private subnet using `kontena grid trusted-subnet add 1.2.3.4/24`

## weave
    2018-01-25T07:53:48.665796695Z Unable to find image 'weaveworks/weavedb:latest' locally
    2018-01-25T07:53:48.679919775Z latest: Pulling from weaveworks/weavedb
    2018-01-25T07:53:48.679950835Z 14fea4d3be35: Pulling fs layer
    2018-01-25T07:53:48.679971883Z 14fea4d3be35: Verifying Checksum
    2018-01-25T07:53:48.679997638Z 14fea4d3be35: Download complete
    2018-01-25T07:53:48.680012354Z 14fea4d3be35: Pull complete
    2018-01-25T07:53:48.680027006Z Digest: sha256:ee08afa34af8773ed4afe122822dad65e713833e59dc7a8e0864fc8bc8fb4a18
    2018-01-25T07:53:48.680041966Z Status: Downloaded newer image for weaveworks/weavedb:latest
    2018-01-25T07:53:48.680056767Z The weave container has died. Consult the container logs for further details.
    2018-01-25T07:53:48.680071696Z
    2018-01-25T07:53:58.838112088Z W, [2018-01-25T07:53:58.837783 #1]  WARN -- Kontena::NetworkAdapters::Weave: timeout after waiting 10.1s of 10.0s until: weave started
    2018-01-25T07:54:00.855737259Z E, [2018-01-25T07:54:00.854903 #1] ERROR -- Kontena::NetworkAdapters::WeaveExecutor: weaveexec exit 1: ["--local", "reset"]
    2018-01-25T07:54:00.855864791Z ERROR: weave is not running; unable to remove from cluster.
    2018-01-25T07:54:00.870385826Z Re-launch weave before reset or use --force to override.
    2018-01-25T07:54:00.870412063Z
    2018-01-25T07:54:08.435244982Z E, [2018-01-25T07:54:08.434921 #1] ERROR -- Kontena::NetworkAdapters::WeaveExecutor: weaveexec exit 1: ["--local", "launch-router", "--ipalloc-range", "", "--dns-domain", "kontena.local", "--password", "<redacted>", "--conn-limit", "0", "--trusted-subnets", ""]
    2018-01-25T07:54:08.435412281Z The weave container has died. Consult the container logs for further details.

Check that weave can bind ports like 53.

## etcd

### can kontena run a cluster without etcd?
### What is the consequence of the majority of the initial nodes going down ?

Internal networking or kontena/lb won't work and new deployments will fail.

### What does IpamError mean?

    Kontena::NetworkAdapters::IpamCleaner: Kontena::NetworkAdapters::IpamError: 503 "Service Unavailable"

Unable to connect to the etcd.

## kontena/lb

### Can I see the haproxy config?

`kontena service exec lb/ingress-lb cat /etc/haproxy/haproxy.cfg`

### Can I run have network_mode: host and still link to lb?

no, only containers with a `io.kontena.container.overlay_cidr` get registered, and `network_mode: host` containers don't get that label https://github.com/kontena/kontena/issues/2741

### Why isn't my link working with a custom image named my-kontena-lb

The magic relies on the name `kontena/lb`, if you want to wrap the Kontena LB or bring your own loadbalancer you should put special env variable `KONTENA_SERVICE_ROLE=lb` for the service.

## Agent

### How to restart all agents?

    for i in `seq 1 3`; do kontena container exec -it node-$i.grid.net/kontena-agent kill 1; done

### Agent logs warn about LogWorker queue size

`WARN -- Kontena::Workers::LogWorker: queue size is 1600`

Agent can not flush logs fast enough to master, queue size is capped to 1600

### waited Ns of 30.0s until...

    Kontena::RpcClient: waited 3.2s of 30.0s until: request /containers/save has response wth id=172071412 yielded Array

Master is slowing down

### Can I disable logs completely from choking my master?

Yes, just put:
```
logging:
  driver: none
```

If errors in agent logs:
<@jakolehm> ok, it seems that `none` is only available in more recent docker versions

## CLI

### How can I delete multiple nodes / stacks / services ?

With `xargs`, see https://github.com/kontena/kontena/issues/2410


## Docker

### Does kontena support the current latest docker version?

Probably not, see: See https://www.kontena.io/docs/getting-started/system-requirements.html

### No logs, but running (until killed because not healthy)

Missing ethwe?
```
$ docker exec -it e8400c1a57cd ipaddr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
162938: eth0@if162939: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP
    link/ether 02:42:ac:11:00:08 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.8/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:8/64 scope link
       valid_lft forever preferred_lft forever
```

should look like:

```
docker exec -it ca3867233285 ipaddr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
686: eth0@if687: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP
    link/ether 02:42:ac:11:00:04 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.4/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:4/64 scope link
       valid_lft forever preferred_lft forever
690: ethwe@if691: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1376 qdisc noqueue state UP
    link/ether 4a:60:5e:42:7e:cd brd ff:ff:ff:ff:ff:ff
    inet 10.81.138.108/16 scope global ethwe
       valid_lft forever preferred_lft forever
    inet6 fe80::4860:5eff:fe42:7ecd/64 scope link
       valid_lft forever preferred_lft forever
```

fix: ??

## Stacks

### Can I expose multiple services in the same stack?

No, but you can install a LB in the stack and expose that.

Alternatively when you expose a service (eg. "node"), all instances are accessible as `stack-N`

## How can I remove something, like secrets from stack?

Set it to something empty like `secrets: []`

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
