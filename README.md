# nmap scan script
quick and dirty nmap host enumeration and versionscan with readable output.

Maybe you need to open the file in vi and write "set ff=unix" in order that the script can work on a linux

## Example commands:
sudo ./scan.py -r 8.8.8.8

sudo ./scan.py -r 0.0.0.0/24

sudo ./scan.py -l [Path to file]/hosts.txt

sudo ./scan.py -r 192.168.80.20 -f test -sf internal -tp 333

sudo ./scan.py -r 192.168.80.20 -f test -sf external -tp 5039 -up 569

## Example output:
### result/tcp_scan.txt & result/udp_scan.txt
There you can find all alive hosts. The hosts are seperated with a new line and those files will be used for the versionscan
### result/overview.txt
A quick overview over all found ports. Example:

45.33.32.156:

	Port		Protocol	State		Confidence	Name		Product		version
  
	22		tcp		open		10		ssh		OpenSSH		6.6.1p1 Ubuntu 2ubuntu2.13
  
	80		tcp		open		10		http		Apache httpd	2.4.7
  
	9929		tcp		open		10		nping-echo	Nping echo	no_version
  
	31337		tcp		open		8		tcpwrapped	no_product	no_version
  
### result/openvas_preparation_tcp.txt & result/openvas_preparation_tcp.txt
Grepped result to just copy paste into openvas
### result/nmap/*
All files gotten from the nmap scan in .nmap, .gnmap and .xml

### result/testssl/*
Result from testssl... tbh not sure how the files or this file will be stored, if there is more than one host or port to scan with testssl

## Params:
-r -> is to give one range or host to the script

-l -> is to give a file to the script with several hosts or ranges or whatever

-Pn -> skip the host discovery and scan all TCP and the top-thousand UDP ports on given range/ip

-tp -> got exotic ports? Add them to the TCP discovery port list with this param

-up -> got exotic ports? Add them to the UDP discovery port list with this param

-f -> recurring scan? Save it under a name with a timestamp so the script can detect differences (in developing)

-sf -> recurring scan on different ranges? Save it in a subfolder (in developing)

## Prozess:
First of all, the script makes an directory called "result", at the same place where it stays.

After it scans the given hosts, ip or ranges for those ports as a host-enumeration process to find out which hosts are up and which are not:

tcp -> 21,22,23,25,49,53,80,88,110,123,135,138,143,389,443,445,464,465,514,515,544,546,547,591,636,902,989,990,992,993,994,995,1433,1434,1503,1521,1630,1720,2375,2376,3128,3268,3389,3544,3939,5000,5061,5666,5667,5722,5986,6000,6443,6881,6882,6883,6884,6885,6886,6887,6888,6889,8080,8200,8443,8530,8531,9100,9800,10250,10259,10257,27017

udp -> 53,67,69,88,123,137,138,161,162,389,445,464,514,1812,1813,3544

The script additionally scans the ports, which are defined under -tp (for TCP) or under -up (for UDP).

After that, the scripts makes a file with all open IP's and makes a tcp full port scan and udp top thousand port scan with versiondetection.

The result of the versionscan and portdetection will be also written in a quick overview.

The result of every scan is stored in the previous mentioned "result" directory as .nmap, .gnmap and xml. Selfmade results like the list of hosts and the overview are stored as .txt

The Script afterwards compare the new scan with the old scan and writes differences into a txt file.

### Testssl
As last step, the script uses the result and runs testssl on every port which uses TLS. But bevor it prints to the console that the result is here and you could start the investigation or openvas :)

## Directory structure
```
    │
  result
    │
    └ -f argument (if not given just skip it)
			│
			└ -sf argument (if not given just skip it)
					│
					└ TIMESTAMP (of starting time)
							│
							└ nmap_result_difference.txt (if there was already a scan with thos -f and -sf args)
							└ openvas_preparation_tcp.txt
							└ openvas_preparation_udp.txt
							└ overview.txt
							└ tcp_scan.txt
							└ udp_scan.txt
							└ nmap
							│	│
							│	└ alive_hosts_tcp.gnmap
							│	└ alive_hosts_tcp.nmap
							│	└ alive_hosts_tcp.xml
							│	└ alive_hosts_udp.gnmap
							│	└ alive_hosts_udp.nmap
							│	└ alive_hosts_udp.xml
							│	└ versionscan_tcp.gnmap
							│	└ versionscan_tcp.nmap
							│	└ versionscan_tcp.xml
							│	└ versionscan_udp.gnmap
							│	└ versionscan_udp.nmap
							│	└ versionscan_udp.xml
							│
							└ testssl
								│
								└ testssl_result.html
```
