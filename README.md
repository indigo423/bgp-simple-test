# Simple Routing Test

![](simple-test.png)

## Startup, tests and usage

Start topology

```
docker-compose up -d
```


Enter shell

```
docker-compose exec r01 vtysh
```

Show routing

```
docker-compose exec r01 vtysh -c "show ip route"
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR, f - OpenFabric,
       > - selected route, * - FIB route, q - queued route, r - rejected route

K>* 0.0.0.0/0 [0/0] via 10.1.23.1, eth0, 00:59:07
C>* 10.1.23.0/24 is directly connected, eth0, 00:59:07
C>* 10.1.24.0/24 is directly connected, eth1, 00:59:07
C>* 10.1.25.0/24 is directly connected, eth2, 00:59:07
B>  10.1.42.0/24 [20/0] via 172.16.102.6 (recursive), 00:52:50
  *                       via 172.16.101.6, eth3, 00:52:50
B>  10.1.43.0/24 [20/0] via 172.16.102.6 (recursive), 00:52:50
  *                       via 172.16.101.6, eth3, 00:52:50
B>  10.1.44.0/24 [20/0] via 172.16.102.6 (recursive), 00:52:50
  *                       via 172.16.101.6, eth3, 00:52:50
C>* 172.16.42.0/24 is directly connected, eth4, 00:59:07
O   172.16.101.0/29 [110/10] is directly connected, eth3, 00:59:07
C>* 172.16.101.0/29 is directly connected, eth3, 00:59:07
O>* 172.16.102.0/29 [110/20] via 172.16.101.6, eth3, 00:57:02
```

Show BGP summary

```bash
docker-compose exec r01 vtysh -c "show bgp summary"
docker-compose exec r03 vtysh -c "show bgp summary"
```

Test connectivity

```bash
docker-compose exec r01 ping -c 5 172.16.101.6 # r02
docker-compose exec r01 ping -c 5 172.16.102.5 # r02
docker-compose exec r01 ping -c 5 172.16.102.6 # r03
docker-compose exec r01 ping -c 5 10.1.42.103 # r03. AS 65537 prefix 10.1.42/24
docker-compose exec r01 ping -c 5 10.1.43.103 # r03. AS 65537 prefix 10.1.43/24
docker-compose exec r01 ping -c 5 10.1.44.103 # r03. AS 65537 prefix 10.1.44/24

docker-compose exec r02 ping -c 5 172.16.101.5 # r01
docker-compose exec r02 ping -c 5 172.16.102.6 # r03
docker-compose exec r02 ping -c 5 10.1.23.101 # r01. AS 65536 prefix 10.1.23/24
docker-compose exec r02 ping -c 5 10.1.24.101 # r01. AS 65536 prefix 10.1.24/24
docker-compose exec r02 ping -c 5 10.1.25.101 # r01. AS 65536 prefix 10.1.25/24
docker-compose exec r02 ping -c 5 10.1.42.103 # r03. AS 65537 prefix 10.1.42/24
docker-compose exec r02 ping -c 5 10.1.43.103 # r03. AS 65537 prefix 10.1.43/24
docker-compose exec r02 ping -c 5 10.1.44.103 # r03. AS 65537 prefix 10.1.44/24

docker-compose exec r03 ping -c 5 172.16.102.5 # r02
docker-compose exec r03 ping -c 5 172.16.101.6 # r02
docker-compose exec r03 ping -c 5 172.16.101.5 # r01
docker-compose exec r03 ping -c 5 10.1.23.101 # r01. AS 65537 prefix 10.1.23/24
docker-compose exec r03 ping -c 5 10.1.24.101 # r01. AS 65537 prefix 10.1.24/24
docker-compose exec r03 ping -c 5 10.1.25.101 # r01. AS 65537 prefix 10.1.25/24
```
