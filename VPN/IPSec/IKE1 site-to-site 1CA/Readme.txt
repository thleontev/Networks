IKE1 IPsec site-to-site (tunnel mode)

Authentication of the CA

The certificate of the CA must be authenticated before the device will be issued its own certificate and before
certificate enrollment can occur. Authentication of the CA typically occurs only when you initially configure
PKI support at your router. To authenticate the CA, issue the crypto pki authenticate command, which
authenticates the CA to your router by obtaining the self-signed certificate of the CA that contains the public
key of the CA.

Trustpoint = CA


===  generate self signed certificate ===
mkdir ~/certs
cd ~/certs

!Generate a private key for the CA:
openssl genrsa 2048 > ca-key.pem

!Generate the X509 certificate for the CA:
openssl req -new -x509 -sha1 -nodes -days 365 \
   -key ca-key.pem \
   -out ca-cert.pem
   
!Generate the X509 certificate for the router:
vi router-req.pem

! CHANGE Serial number !
openssl x509 -req -days 365 -sha1  -set_serial 03 \
   -in router-req.pem \
   -out router-cert.pem \
   -CA ca-cert.pem \
   -CAkey ca-key.pem

! openssl x509  -in certificate.pem -out certificate.der

!Check Private key info: 
openssl rsa -text -in privateKey.key -noout
!Check CSR info: 
openssl req -text -in CSR.csr -noout
!View SSL certificate info: 
openssl x509 -text -in router-cert.pem -noout
!Check a PKCS#12 file (.pfx or .p12)
openssl pkcs12 -info -in keyStore.p12
===

! Create RSA key
crypto key generate rsa general-keys label R3 modulus 2048 exportable
! Elliptic Curve
crypto key generate ec keysize 256 label R1 exportable
! sh crypto key mypubkey all

! Create Trustpoint for Your Router
crypto pki trustpoint TP1
enrollment terminal pem 
subject-name CN=R3, OU=IT,O=TT,C=CA,ST=XMAO,L=Surgut
rsakeypair R3
!eckeypair R3
fqdn R3
revocation-check none
exit

! Install Intermediate / Root Certificate(s)
crypto pki authenticate TP1
show crypto ca certificates
! delete 
! no crypto pki trustpoint TP1

! This displays the Certificate Signing Request (CSR) to the terminal
! CSR сохранить в файл router-req.pem
crypto pki enroll TP1

! Install Signed SSL Certificate on Cisco Router
crypto pki import TP1 certificate



=== настройка с профилем ===
! будет работать и без профиля
crypto pki certificate map CMAP 1
 subject-name co cn=R3
 
crypto isakmp profile ISAPROFILE
   self-identity fqdn
   ca trust-point TP1
   match certificate CMAP

crypto map LAB-VPN 10 ipsec-isakmp 
 set isakmp-profile ISAPROFILE
 
 
=== цепочка сертификатов ===
crypto pki trustpoint SUB-CA1
 chain-validation continue CA-SERVER
==


! Проверка
show clock
show crypto ca certificates
show crypto key mypubkey rsa
show crypto key pubkey-chain rsa
show crypto pki trustpoints
show crypto pki certificates

!
debug crypto isakmp
debug crypto pki validation


Интерфейс NVI0 - создается автоматически (тунельный режим).

=== R1 ===
crypto isakmp identity dn

!ISAKMP Policy
crypto isakmp policy 1
 encr aes
 hash sha512
 authentication rsa-sig
 group 24
!show crypto isakmp policy

!Transform Set
crypto ipsec transform-set ESP-AES-SHA esp-aes esp-sha-hmac 
 mode tunnel

!Crypto map
crypto map LAB-VPN 10 ipsec-isakmp 
 set peer 20.0.0.2
 set transform-set ESP-AES-SHA 
 match address 101


!Apply the crypto map
interface GigabitEthernet0/0
 ip address 10.0.0.2 255.255.255.252
 duplex auto
 speed auto
 media-type rj45
 crypto map LAB-VPN

interface GigabitEthernet0/1
 ip address 192.168.1.1 255.255.255.0
 duplex auto
 speed auto
 media-type rj45

access-list 101 permit ip 192.168.1.0 0.0.0.255 172.16.1.0 0.0.0.7


=== R3 ===
crypto isakmp policy 1
 encr aes
 hash sha512
 authentication pre-share
 group 24
crypto isakmp key test123 address 10.0.0.2       

crypto ipsec transform-set ESP-AES-SHA esp-aes esp-sha-hmac 
 mode tunnel

crypto map LAB-VPN 10 ipsec-isakmp 
 set peer 10.0.0.2
 set transform-set ESP-AES-SHA 
 match address 101

interface GigabitEthernet0/0
 no shutdown
 ip address 20.0.0.2 255.255.255.252
 duplex auto
 speed auto
 media-type rj45
 crypto map LAB-VPN

interface GigabitEthernet0/1
 no shutdown
 ip address 192.168.2.1 255.255.255.0
 duplex auto
 speed auto
 media-type rj45

access-list 101 permit ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255


=== diagnostics ===
show crypto session
show crypto isakmp sa
show crypto ipsec sa

show crypto map
show crypto ipsec transform-set

ping 20.0.0.2 source 10.0.0.2

!debug crypto isakmp
!debug crypto ipsec
!debug crypto condition peer ipv4 <IP>

clear crypto ipsec sa      !deletes the active IPSec security associations.
clear crypto ipsec sa peer !deletes the active IPSec security associations for the specified peer.
clear crypto isakmp sa     !deletes the active IKE security associations.
clear crypto isakmp        !deletes the active IKE security associations.    
clear crypto sa            !deletes the active IPSec security associations.    
