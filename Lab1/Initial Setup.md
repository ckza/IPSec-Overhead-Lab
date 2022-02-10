Lab 1 - Initial Setup to get general connectivity.
----------------------------------------------------------------------------------

! ---R1---
en
conf t
hostname R1
no ip domain-lookup
line con 0
logging synchronous
ip dhcp excluded-address 10.1.1.1
ip dhcp excluded-address 172.10.55.1
ip dhcp excluded-address 192.168.1.1
ip dhcp pool 10.1.1.0
  network 10.1.1.0 255.255.255.0
  default-router 10.1.1.1
ip dhcp pool 172.10.55.0
  network 172.10.55.0 255.255.255.0
  default-router 172.10.55.1
ip dhcp pool 192.168.1.0
  network 192.168.1.0 255.255.255.0
  default-router 192.168.1.1
int g0/0
ip address 66.31.56.2 255.255.255.252
no shut
int g0/1
ip address 10.1.1.1 255.255.255.0
no shut
int g0/2
ip address 172.10.55.1 255.255.255.0
no shut
int g0/3
ip address 192.168.1.1 255.255.255.0
no shut
ip route 0.0.0.0 0.0.0.0 66.31.56.1
end
wr


! ---R2---
en
conf t
hostname R2
no ip domain-lookup
line con 0
logging synchronous
ip dhcp excluded-address 10.2.1.1
ip dhcp excluded-address 172.10.54.1
ip dhcp excluded-address 192.168.2.1
ip dhcp pool 10.2.1.0
  network 10.2.1.0 255.255.255.0
  default-router 10.2.1.1
ip dhcp pool 172.10.54.0
  network 172.10.54.0 255.255.255.0
  default-router 172.10.54.1
ip dhcp pool 192.168.2.0
  network 192.168.2.0 255.255.255.0
  default-router 192.168.2.1
int g0/0
ip address 66.31.57.2 255.255.255.252
no shut
int g0/1
ip address 10.2.1.1 255.255.255.0
no shut
int g0/2
ip address 172.10.54.1 255.255.255.0
no shut
int g0/3
ip address 192.168.2.1 255.255.255.0
no shut
ip route 0.0.0.0 0.0.0.0 66.31.57.1
end
wr


! ---INTERNET---
en
conf t
hostname Internet
no ip domain-lookup
line con 0
logging synchronous
int g0/0
ip address 66.31.56.1 255.255.255.252
no shut
int g0/1
ip address 66.31.57.1 255.255.255.252
no shut
! Static Route for CMAP Lab 
ip route 10.1.1.0 255.255.255.0 66.31.56.2
ip route 10.2.1.0 255.255.255.0 66.31.57.2
end
wr


set pcname PC1_1_A
ip dhcp
wr

set pcname PC1_2_A
ip dhcp
wr

set pcname PC1_3_A
ip dhcp
wr

set pcname PC2_1_A
ip dhcp
wr

set pcname PC2_2_A
ip dhcp
wr

set pcname PC2_3_A
ip dhcp
wr



Lab 2 - Get packet captures (P2P Clean)
----------------------------------------------------------------------------------
! 
! ---TEST1---
! Ping from R1 to R2 and check packet size
! TEST1 Result: 114 bytes / ICMP = 72
!
! ---TEST2---
! Ping from PC1_1_A to PC2_1_A and check packet size
! TEST2 Result: 98 bytes / ICMP = 56
!
! Question - why is ICMP 16 bytes bigger Router to Router?
