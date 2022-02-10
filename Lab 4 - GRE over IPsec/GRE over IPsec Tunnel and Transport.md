# Continue from LAB 3 where initial GRE tunnel was created and EIGRP added. 

Below we'll define ISAKMP and IPsec parameters, create IPsec Profile and apply that to the tunnel

## R1
```
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
! Very important - the default for the transform-set is tunnel

! ---IPsec Profile
crypto ipsec profile IPROF1
  set transform-set TSET1

! ---Tunnel Protection
interface Tunnel1
  tunnel protection ipsec profile IPROF1
```

## R2
```
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
! Very important - the default for the transform-set is tunnel

! ---IPsec Profile
crypto ipsec profile IPROF1
  set transform-set TSET1
  
! ---Tunnel Protection
interface Tunnel1
  tunnel protection ipsec profile IPROF1
```

Now we have GRE over IPsec. But what's happening is that IPsec is the outer header and the inner header repeats the outer header source and destination IP addresses.
We can change transform-set to Transport and we'll drop 16bytes from the inner header (see image below)

R1 and R2
```
! ---Change mode
crypto ipsec  transform-set TSET1 esp-aes 128 esp-sha256-hmac
  mode transport

! ---We'll drop the Crypto SA's so they tunnel can be re-established
crypto clear sa
```
![Tunnel vs Transport IPsec](https://user-images.githubusercontent.com/33652862/153455478-55fc1b8e-4652-4d88-af4c-ee42fcb32309.png)
