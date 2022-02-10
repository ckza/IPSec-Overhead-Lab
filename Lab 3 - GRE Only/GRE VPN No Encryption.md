# GRE VPN - No Encryption

## R1
```
int tun1
  ip address 100.1.1.1 255.255.255.0
  tunnel source g0/0
  tunnel destination 66.31.57.2
  no shut
```

## R2
```
int tun1
  ip address 100.1.1.2 255.255.255.0
  tunnel source g0/0
  tunnel destination 66.31.56.2
  no shut
```

# Inject routes with EIGRP

# R1
```
router eigrp 100
  ! GRE Tunnel
  network 100.1.1.0 255.255.255.0
  ! Normal Routes
  network 10.1.1.0 255.255.255.0
  network 172.10.55.0 255.255.255.0
  network 192.168.1.0 255.255.255.0
```

## R2
```
router eigrp 100
  ! GRE Tunnel
  network 100.1.1.0 255.255.255.0
  ! Normal Routes
  network 10.2.1.0 255.255.255.0
  network 172.10.54.0 255.255.255.0
  network 192.168.2.0 255.255.255.0
```
