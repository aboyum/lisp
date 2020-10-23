# LISP
## Locator ID Separation Protocol (LISP)
## LISP mobility example

Have you ever heard that it is not possible to have the same IP range in two different locations?
Or have you ever tried to understand how you can move your phone in a car, and still be connected to your ISP? Without your phone ever needed to get a new IP address lease from the DHCP?

By using LISP mobility you can use the same IP whenever wherever. And it can be in the same IP range as another item on the other side of the globe.
There are a number of different ways to set up LISP, this article is only about LISP mobility.

![Lisp-example1](/lisp1.png)

In the drawing there are two pc's that can reach each other. However, the closest router to the pc's are not able to ping the other router, nor the pc on the oposite router, since the source-ip is the gateway of both the routers.

![Lisp-example2](/lisp2.png)

The configuration of lisp isn't very large. It's mostly about connecting to the map-server. The configuration are similar on all edge-nodes. If there are multiple map-servers, lisp mus map to all of them. You don't need a dedicated map-server either, but the configuration become easier by doing that.

Edge node1 | map-server | Edge node2
------------ | ------------- | -------------
hostname R1|hostname R2|hostname R3
vrf definition VRF3|!(Doesn't have any vrf's)|vrf definition VRF3
 !||!
 address-family ipv4|| address-family ipv4
 exit-address-family|| exit-address-family
||
interface Loopback0|interface Loopback0|interface Loopback0
 ip address 10.0.0.1 255.255.255.255|ip address 5.1.1.1 255.255.255.255| ip address 10.0.0.3 255.255.255.255
||
interface GigabitEthernet0/2||interface GigabitEthernet0/2
 ip address 40.1.0.1 255.255.255.0|| ip address 40.1.0.1 255.255.255.0
 lisp mobility VRF3|| lisp mobility VRF3
||
router lisp|router lisp|router lisp
 locator-set Site1||locator-set Site2
  IPv4-interface Loopback0 priority 10 weight 10|| IPv4-interface Loopback0 priority 10 weight 10
  exit| | exit
 !| |!
 site Site1|site Felles|site Site2
  authentication-key cisco|authentication-key cisco |  authentication-key cisco
  exit|eid-prefix 40.1.0.0/24 accept-more-specifics | exit
 !|exit |!
 dynamic-eid VRF3|ipv4 map-server| dynamic-eid VRF3
  database-mapping 40.1.0.0/24 locator-set Site1|ipv4 map-resolver| database-mapping 40.1.0.0/24 locator-set Site2
  exit|exit|exit
 !||!
 ipv4 use-petr 5.1.1.1||ipv4 use-petr 5.1.1.1
 ipv4 itr map-resolver 5.1.1.1||ipv4 itr map-resolver 5.1.1.1
 ipv4 itr||ipv4 itr
 ipv4 etr map-server 5.1.1.1 key cisco||ipv4 etr map-server 5.1.1.1 key cisco
 ipv4 etr||ipv4 etr
 exit||exit
