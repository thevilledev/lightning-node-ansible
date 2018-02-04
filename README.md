# lightning-node-ansible

This repository contains configuration for creating automated Bitcoin Core + Lightning Network
installations by utilizing Ansible. A blog post by @dougvk outlines the manual setup very nicely:
https://medium.com/@dougvk/run-your-own-mainnet-lightning-node-2d2eab628a8b

Each node runs the official Bitcoin Core binaries as described in the blog post, except I have
built both containers myself by utilizing multi-stage builds. This minimizes the amount of data Docker has to
pull upon container startup. This is about 30 MB per container. Container configuration can be found from
[vtorhonen/lightning-node](https://github.com/vtorhonen/lightning-node) and from [my Dockerhub registries:

- [bitcoind](https://hub.docker.com/r/vtorhonen/bitcoind)
- [lightningd](https://hub.docker.com/r/vtorhonen/lightningd)

# Prequisites

Deploy a fleet of Debian hosts. Make sure you have plenty of storage space available under `/data` directory on each host. This playbook expects that.

Next create a file at `inventories/lnd-nodes.yml` and define your target hosts. See [inventories/example.yml](inventories/example.yml) for reference.

As a final step install Ansible to your control machine. This playbook has been tested by using Ansible 2.4.3.

# Installing bitcoind nodes

First run the playbook `deploy-bitcoind.yml`, which installs Docker and runs the Bitcoin Core
container.

```
$ ansible-playbook -i inventories/lnd-nodes.yml deploy-bitcoind.yml
```

You now have a Bitcoin Core node running, which exposes itself to the network through port `tcp/8333`. Node start synchronizing the blockchain. Use the following command to see
it's progress.

```
$ ansible all -b -i inventories/lnd-nodes.yml -a "docker logs --tail=1 bitcoind"
foohost | SUCCESS | rc=0 >>
2018-02-02 20:15:07 UpdateTip: new best=0000000000000007b4511e0e0fe84f191bf6451e2e31e33da5445ec298e40c6c height=263627 version=0x00000002 log2_work=72.758038 tx=25379178 date='2013-10-14 16:04:00' progress=0.086696 cache=570.9MiB(4120711txo)
```

A wrapper script is installed at `/usr/local/bin/bitcoin-cli`, which you can use to send commands
to the RPC interface of the node.

```
$ ansible all -b -i inventories/lnd-nodes.yml -a "bitcoin-cli getnetworkinfo"
foohost | SUCCESS | rc=0 >>
{
  "version": 150100,
  "subversion": "/Satoshi:0.15.1/",
  "protocolversion": 70015,
  "localservices": "000000000000000d",
  "localrelay": true,
  "timeoffset": -1,
  "networkactive": true,
  "connections": 21,
  "networks": [ ... ]
}
```

# Installing lightning network nodes

Once the Bitcoin blockchain is synchronized you can deploy a c-lightning Lightning Network daemon. It shares the same Docker network configuration with the Bitcoin node and is exposed
to the network through port `tcp/9735`.

```
$ ansible-playbook -i inventories/lnd-nodes.yml deploy-lnd.yml
```

A similar wrapper script is installed at `/usr/local/bin/lightning-cli`.

```
$ ansible all -b -i inventories/lnd-nodes.yml -a "lightning-cli getinfo"
foohost | SUCCESS | rc=0 >>
{ "id" : "", "port" : 9735, "address" :
	[  ], "version" : "v0.5.2-2016-11-21-1726-gfc73b1e", "blockheight" : 507557, "network" : "bitcoin" }
````

That's it. Next setup your payment channels and start buying.
