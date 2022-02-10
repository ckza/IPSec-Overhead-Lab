# Lab 2 - Configure P2P IPsec using Crypto Maps on Interface

This is R1 to R2 Crypto Map IPsec VPN applied on the interface. 

- We need Crypto ACL for interesting traffic
- IPsec is in Tunnel mode
- There's no NAT on R1 or R2, but if there was you'd have to add Nat Excemption
- There's also no inbound ACL. If there was you'd have to add the following to permit ISAKMP and ESP



```
ip access-list extended inbound-R1
 permit tcp any any established
 permit icmp any any
 permit udp any eq domain any
 permit udp any eq isakmp host <<ip>> eq isakmp
 permit esp any host <<ip>>
```

## R1
```
enable 
conf t

! ---Interesting traffic
ip access-list extended VPNTraffic
  permit ip 10.1.1.0 0.0.0.255 10.2.1.0 0.0.0.255
  
! ---IKEv1 Phase 1 (HAGLE)
crypto isakmp key VpnKey123! address 66.31.57.2 
crypto isakmp policy 10
  authentication pre-share
  encryption aes 128
  hash sha256
  group 14
  lifetime 86400
  
! ---IKEv1 Phase 2
crypto ipsec  transform-set TSET1 esp-aes 128 esp-sha256-hmac

! ---Crypto Map (PAT)
crypto map CMAP1 10 ipsec-isakmp
  set peer 66.31.57.2
  set transform-set TSET1
  match address VPNTraffic
  
! ---Crypto Map on Interface  
int g0/0
  crypto map CMAP1
end
```

## R2
```
enable 
conf t

! ---Interesting traffic
ip access-list extended VPNTraffic
  permit ip 10.2.1.0 0.0.0.255 10.1.1.0 0.0.0.255
  
! ---IKEv1 Phase 1 (HAGLE)
crypto isakmp key VpnKey123! address 66.31.56.2 
crypto isakmp policy 10
  authentication pre-share
  encryption aes 128
  hash sha256
  group 14
  lifetime 86400
  
! ---IKEv1 Phase 2
crypto ipsec  transform-set TSET1 esp-aes 128 esp-sha256-hmac

! ---Crypto Map (PAT)
crypto map CMAP1 10 ipsec-isakmp
  set peer 66.31.56.2
  set transform-set TSET1
  match address VPNTraffic
  
! ---Crypto Map on Interface  
int g0/0
  crypto map CMAP1
end
```
