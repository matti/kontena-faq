# Kontena FAQ

## etcd

### can kontena run a cluster without etcd?

kind of, internal networking or kontena/lb just won't work.

## kontena/lb

### Can I run have network_mode: host and still link to lb?

no, only containers with a `io.kontena.container.overlay_cidr` get registered, and `network_mode: host` containers don't get that label https://github.com/kontena/kontena/issues/2741

### Why isn't my link working with a custom image named my-kontena-lb

The magic relies on the name `kontena/lb`, if you want to wrap the Kontena LB or bring your own loadbalancer you should put special env variable `KONTENA_SERVICE_ROLE=lb` for the service.

## Agent

### Agent logs warn about LogWorker queue size

`WARN -- Kontena::Workers::LogWorker: queue size is 1600`

Agent can not flush logs fast enough to master, queue size is capped to 1600

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
