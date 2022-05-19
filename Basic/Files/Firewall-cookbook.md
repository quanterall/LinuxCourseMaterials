# Firewall cookbook 

```yaml
||===============================================||
|| ANOTHER FIREWALL COOKBOOK by Ivan Ignatov     ||
||===============================================||

What is a firewall: a wall that prevents the spread of fire
When data moves in and out of server its packet information is tested againts
the firewall rules to see if it should be allowed or not.


(LAN - LOCAL AREA NETWORK)
(WAN - WIDE  AREA NETWORK)

(LAN computers) ---------|FIREWALL|-----------(WAN)
								      (server b sends conenction via SSH=22)
(SERVER A)<----------/server a firewall|check if rules allow TRAFFIC via port 22|/<-----------( traffic )<(SERVER B)

In simple words, a firewall is like a watchman, a bouncer, or a shield
that has a set of rules given and based on that rule they decide who can enter
and leave. 

There are 2 types of firewalls in IT:

1. Software = runs on operating systems
2. Hardware = a dedicated appliance with firewall software

=====================================================================================================================================
There are 2 tools to manage firwall in most of the Linux distributions
1. iptables = for older Linux versions but still widely used
2. firewalld = for newer versions like CentOS7 and up 

You can run one or the other. You cannot run both at the same time. 
SYSTEMCTL EXAMPLES:

systemctl status firewalld = to check if running or stopped or general status
systemctl start firewalld  = to start firewalld
systemctl stop  firewalld  = to stop the service
systemctl disable firewalld = to prevent from starting at boot time
systemctl mask firwalld = to prevent it from running by other programs

/same for iptables/
======================================================================================================================================
IPTABLES

to check if you have iptables-services package installed

rpm -qa | grep iptables-services
rpm -qa | grep iptables 

if not istalled use (yum install iptables-services) (yum install iptables)
systemctl start iptables 

to check iptables rules do
iptables -L

to flush iptables and its rules
iptables -F 

//////////////////////////////////////////////////////////////////////////////////////////////////

The funtion of iptables tool is packet filtering.
The packet filtering mechanism is organized into three different kinds of structures: tables, chains and targets. 

1. Tables = table is something that allows you to process packets in specific ways.
There are 4 different types of tables:
*filter, mangle, nat and raw.

2. Chains = The chains are attached to tables. These chains allow you to inspect traffic at various points. 
There are 3 main chains used in iptables:
*INPUT= incoming traffic / * FORWARD = going to a router, from one device to another / * OUTPUT = outgoing traffic.
+++chains allow you to filter traffic by adding rules to them+++
+++Rule = if traffic is coming from 192.168.1.35 then go to defined target+++

3. Targets = target decides the fate of a packet, such as allowing or rejecting it. 
There are 3 different type of targets.
*ACCEPT = connection accepted
*REJECT = send a reject response
*DROP = drop connection without sending any response 


Let's draw it out:


TABLE has >>>>>	+++++++++++++++++++++++++++++++++++++++++
 		+ INPUT   -rule - ACCEPT/DROP/REJECT	+					
		+ FORWARD -rule - ACCEPT/DROP/REJECT	+					
		+ OUTPUT  -rule - ACCEPT/DROP/REJECT	+			
		+ ^^^^^^   ^^^^   ^^^^^^^^^^^^^^^^^^^   +			
		+ chains   filter       targets		+				
		+++++++++++++++++++++++++++++++++++++++++              


===========================================================================================================================

[root@wildwesthost ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
[root@wildwesthost ~]#

ABOVE EXPLAINED: 
--------------------------------------------------
Chain INPUT (policy ACCEPT)
target     prot opt source               destination = this whole thing is a CHAIN
--------------------------------------------------
INPUT = types of chain 
--------------------------------------------------
target = TARGET 
--------------------------------------------------
prot = protocol such as tcp, udp, icmp, or all
--------------------------------------------------
opt = rarely used, this column indicates IP options
--------------------------------------------------
source = The source IP address or subnet of the traffic or anywhere 
--------------------------------------------------
destination = the destination IP address or subnet of the traffic or anywhere
==================================================================================================================================

FIREWALLD

*firewalld works the same way as iptables but of course it has its own commands:	
	firewall-cmd 

*it has a few pre-defined service rules that are very easy to turn on and off:
	services such as: NFS, NTP, HTTPD etc. they are already there in an XML format. 
	All you have to do is add those services into the firwalld table and the rules

*firewalld also has the following:
	TABLE	
	CHAINS
	RULES	
	TARGETS

!!!AGAIN!!!! to start firewalld, iptables must be stopped/masked/disabled.

To check firewalld package: rpm -qa | grep firewalld 

To check the rules of firewalld:
firewall-cmd --list-all

To get the listing of all services firewalld is aware of:
firewall-cmd --get-services

To make firewalld re-read the configuration added
( meaning every time you add a new rule to firewalld you must always run the below command):
firewall-cmd --reload

==========================================================================================================================
The firewalld has multiple zones, to get a list of all zones do
	firewall-cmd --get-zones

OUTPUT:
[root@wildwesthost ~]# firewall-cmd --get-zones
block dmz docker drop external home internal public trusted work
[root@wildwesthost ~]#

--------------------------------
To get a list of active zones:
	firewall-cmd --get-active-zones

OUTPUT:
[root@wildwesthost ~]# firewall-cmd --get-active-zones
public
  interfaces: enp0s3 enp0s8
[root@wildwesthost ~]#
--------------------------------
To get firewall rules for public zone
	firewall-cmd --zone=public --list-all
		OR	
	firewall-cmd --list-all

OUTPUT:
[root@wildwesthost ~]# firewall-cmd --zone=public --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
-------------------------------------------------------------------------------------------
All services are pre-defined by firewalld. What if you want to add a 3rd party service?
Then you need to go to /usr/lib/firewalld/services = all those pre-defined services are in that directory.
All  you have to do is copy any .xml file and change the service and the port number 

EXAMPLE:

[root@wildwesthost services]# cat snmp.xml
<?xml version="1.0" encoding="utf-8"?>         -------> VERSION OF XML
<service>
  <short>SNMP</short> 
  <description>Simple Network Management Protocol is an "Internet-standard protocol for managing devices on IP networks". Enable this service if you run SNMP agent (server).</description>
  <port protocol="tcp" port="161"/>
  <port protocol="udp" port="161"/>
</service>


THE ENTIRE SECTION STARTING WITH <service> AND ENDING WITH </service>  WILL GIVE YOU THE INFORMATION ABOUT:
WHAT TYPE OF SERVICE IS THAT, IN THIS CASE ITS SNMP, DESCRIPTION, PORT NUMBER.

----------------------------------------------------------------------------------
to add a service for example (http) 
	firewall-cmd --add-service=http

Check if the http service is added in the firewalld
	firewall-cmd --list-all

OUTPUT:

[root@wildwesthost services]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
-------------------------
In my case it is not, so if I go to the browser and type my IP address (192.168.0.103) 
it wont show the default HTTP page
I will have to add the service on my firewalld with:
	firewall-cmd --add-service=http


first check if httpd is running with: systemctl status httpd

after that:

[root@wildwesthost services]# firewall-cmd --add-service=http
success
[root@wildwesthost services]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: dhcpv6-client http ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
[root@wildwesthost services]#


+++The default HTTP page should be visible in the browser now+++

****If you want to remove the service  you can simply do: firewall-cmd --remove-service=http

****To reload the firewalld configuration do: firewall-cmd --reload ( this will remove the added services as well)
	this works like flushing (iptables has: iptables -F)

****To add a service and make it permanent (even after reload to stay) you must do:
	firewall-cmd --add-service=http --permanent
	firewall-cmd --remove-service=http --permanent
	rememer after adding a permanent service to the firewalld you must do: 
	firewall-cmd --reload 

========================================================================================================
To add a service that is not pre-defined by firewalld

	*go to /usr/lib/firwalld/services - where all xml files are for the services.
	*Simply do cp any .xml file sap.xml and change the name of it with the needed name.
	*Change the port and the service
	*firwall-cmd --get-services (to verify the new service)
	*firwall-cmd --add-service=sap

EXAMPLES:

cd /user/lib/firewalld/services

cp ssh.xml sap.xml

ls -ltr

nano sap.xml

[root@wildwesthost services]# cat sap.xml
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>SAP</short>
  <description>This is a 3rd party application service.</description>
  <port protocol="tcp" port="32"/>
</service>
--------------
short was channged from SSH to SAP
description was changed
port was changed from 22 to 32

if you want to see your service when you do: firwall-cmd --get-services 
you must first do: systemctl restart firewalld

Now go ahead and add it as a service to the firewalld with:
	firewall-cmd --add-service=sap
	OR	
	firewall-cmd --add-service=sap --pernament

EXAMPLE: 
[root@wildwesthost services]# firewall-cmd --add-service=sap
success
[root@wildwesthost services]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: dhcpv6-client http sap ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[root@wildwesthost services]#




=======================================================================================================
To add a port 

	firewall-cmd --add-port=1110/tcp
	OR
	firewall-cmd --add-port=1110/tcp --permanent

To remove a port
	
	firewall-cmd --remove-port=1110/tcp
	OR
	firewall-cmd --remove-port=1110/tcp --permanent

 

==========================================================================================================

To reject incoming traffic from an IP address:

	firewall-cmd --add-rich-rule='rule family="ipv4" source address="194.153.145.104" reject'

EXAMPLE:

[root@wildwesthost services]# firewall-cmd --add-rich-rule='rule family="ipv4" source address="194.153.145.104" reject'
success
[root@wildwesthost services]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: dhcpv6-client http ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
        rule family="ipv4" source address="194.153.145.104" reject


==============================================================================================

To block or unblock ICMP incoming traffic ( what if you do not want people to ping you?)

	firewall-cmd --add-icmp-block-inversion

EXAMPLES:

[root@wildwesthost services]# firewall-cmd --add-icmp-block-inversion
success
[root@wildwesthost services]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: yes
  interfaces: enp0s3 enp0s8
  sources:
  services: dhcpv6-client http ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:


+++Now if you go to your windows machine and open CMD type ping 192.168.0.103 ( in your case it will be your IP address).
you will see this:

C:\Users\User>ping 192.168.0.103

Pinging 192.168.0.103 with 32 bytes of data:
Reply from 192.168.0.103: Destination host unreachable.
Reply from 192.168.0.103: Destination host unreachable.
Reply from 192.168.0.103: Destination host unreachable.
Reply from 192.168.0.103: Destination host unreachable.

Ping statistics for 192.168.0.103:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

===========================================================================================

How to block outgoing traffic to websites:

First find out what's the IP address of that website.

Commands for that can be: host -t a abv.bg (im using abv.bg for this example)
or
dig abv.bg 
or 
nslookup

OUTPUT:

[root@wildwesthost services]# host -t a abv.bg
abv.bg has address 194.153.145.104

----------------------------------------------------------------------
[root@wildwesthost services]# dig abv.bg

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.9 <<>> abv.bg
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 30470
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;abv.bg.                                IN      A

;; ANSWER SECTION:
abv.bg.                 83300   IN      A       194.153.145.104

;; Query time: 9 msec
;; SERVER: 192.168.0.1#53(192.168.0.1)
;; WHEN: Mon May 09 19:11:10 EEST 2022
;; MSG SIZE  rcvd: 40
----------------------------------------------------------------------------------
[root@wildwesthost services]# nslookup abv.bg
Server:         192.168.0.1
Address:        192.168.0.1#53

Non-authoritative answer:
Name:   abv.bg
Address: 194.153.145.104

---------------------------------------------------------
To block outgoing traffic to the abv.bg do:
	
	firewall-cmd --direct --add-rule ipv4 filter OUTPUT 0 -d 194.153.145.104 -j DROP


After you add the rule you will see by doing ping on 194.153.145.104 that the connection no longer works.

Example:

[root@wildwesthost services]# ping 194.153.145.104
PING 194.153.145.104 (194.153.145.104) 56(84) bytes of data.
ping: sendmsg: Operation not permitted
ping: sendmsg: Operation not permitted
ping: sendmsg: Operation not permitted
ping: sendmsg: Operation not permitted
ping: sendmsg: Operation not permitted
ping: sendmsg: Operation not permitted
ping: sendmsg: Operation not permitted
ping: sendmsg: Operation not permitted
^C
--- 194.153.145.104 ping statistics ---
8 packets transmitted, 0 received, 100% packet loss, time 7001ms


------------------------------------------------To bring the connection back you can again:--------------------------------------
firewall-cmd --realod 
then
systemctl restart firewalld (there might be something cached)
check by pinging 

EXAMPLES:
[root@wildwesthost services]# firewall-cmd --reload
success
[root@wildwesthost services]# systemctl restart firewalld
[root@wildwesthost services]# ping 194.153.145.104
PING 194.153.145.104 (194.153.145.104) 56(84) bytes of data.
64 bytes from 194.153.145.104: icmp_seq=1 ttl=59 time=11.0 ms
64 bytes from 194.153.145.104: icmp_seq=2 ttl=59 time=8.55 ms
64 bytes from 194.153.145.104: icmp_seq=3 ttl=59 time=7.95 ms
64 bytes from 194.153.145.104: icmp_seq=4 ttl=59 time=11.3 ms
64 bytes from 194.153.145.104: icmp_seq=5 ttl=59 time=7.42 ms
64 bytes from 194.153.145.104: icmp_seq=6 ttl=59 time=6.44 ms
^C
--- 194.153.145.104 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5014ms
rtt min/avg/max/mdev = 6.441/8.794/11.394/1.819 ms


```