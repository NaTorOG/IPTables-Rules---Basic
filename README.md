# IPTables-Rules - Basic
Basic IPTables Rules for a Minecraft Server

In this guide, you will find basic IPTables rules useful for a minecraft server!

## What we need
1 - Full root access to our machine (this guide won't work for Panels like Pterodactyl or Multicraft.  
2 - IPTables package.  
3 - NeTFilter Persistent package.  
4 - IPSET package (optional).  

## Installing the packages (root required)  
$ apt update  
###### IPTables
$ apt install iptables  
###### IPTables Persistent  
$ apt install iptables-persistent  
###### IPSET (optional, read below)  
$ apt install ipset (optional, read below)  
###### Enable the processes on systemd
$ systemctl enable iptables  
$ systemctl enable netfilter-persistent  
$ systemctl enable ipset  

## Applying the Rules and Basic Concepts
If you are reading this guide, is probabily because you don't have a good knowledge  
about firewalling your server and secure the processes running on it.  
It is **highly recommended** to run all the services in localhost(127.0.0.1) like MySQL or  
Backend Servers(Spigot servers not Proxy BungeeGuard/Velocity)  
and to not install webservices like Apache or NGIX.  

###### SSH Tips
Run SSH using **Keys** and not Password! It is very important because we will avoid SSH Bruteforces attempts and we can  
better manage the access to our server generating SSH Keys for our trusted members.  
For even further protection, we can use ipset and create an **IP List of trusted IPS** that can attempt a connection to the SSH Port.  

###### Basic Users Management
All the exposed services(for exposed I mean **externally accessible services**, like your Minecraft server)  
they should always be **RUNNED ONLY on a NON sudo User.**    
Do not ever and ever use root or any sudo user to run externally accessible services.  

###### Basic Rules
We aim to prevent your server from being bypassed by **UUID Spoofing.**

**Minecraft Servers**  
Set every Minecraft Backend server(spigot servers) to run in 127.0.0.1 in server.properties(server-ip).  
Set only the ProxyServer(Bungee/Waterfall/Velocity) to run in 0.0.0.0 in config.yml(host: 0.0.0.0:25565).  
Set also in the Proxy config.yml the servers with 127.0.0.0.1 address.  
Example:  

servers:  
  hub:  
    motd: 'Hub Server'  
    address: 127.0.0.1:25566  
    restricted: false  
    
**Policies**             
We aim with this guide to have a **DROP Policy** on INPUT and FORWARD chains.  
By defaults these chains policies are on ACCEPT, meaning that **all the connections** towards every port are accepted.  
Changing the policies to DROP, we will only grant access to the ports we want and so to the services we want.  

**Ports**  
I assume that you have only installed SSH and Minecraft Servers on your server so we will open only the necessary  
ports to let these services work without problems.  
Remember to respect the order of the rules and commands!  

$ iptables -A INPUT -i lo -j ACCEPT  
*( The loopback interface is also used if you configure your application server to connect to a database server with a localhost address. As such, you will want to be sure that your firewall is allowing these connections.)* 

$ iptables -A INPUT -m conntrack --ctstate INVALID -j DROP  
*(Sometimes it can be useful to log this type of packet but often it is fine to drop them.)*

$ iptables -A INPUT -p tcp -m tcp --dport 22 -j ACCEPT  
*(This rule will allow the traffic towards SSH, change the port from 22 to another if you don't use 22)*  

$ cat /proc/sys/net/ipv4/ip_local_port_range  
* This will prompt the kernel range ports that we have to allow to let the kernel works without problems.*  
A1= First Number A2=Second Number  
$ iptables -A INPUT -p tcp -m tcp --dport A1:A2 -j ACCEPT  
$ iptables -A INPUT -p udp -m udp --dport A1:A2 -j ACCEPT  
*(Replace A1 and A2 with the ports numbers displayed with the cat command executed above.)*  

$ iptables -A INPUT -p tcp -m tcp --dport 25565 -j ACCEPT  
*(Replace if necessary 25565 with the port used by the Proxy server)*    
It is usually a good practice to limit per second the connections per IP, so we can limit and reduce some bot attacks  
specifically some very **poor designed** bot attacks.  
$ iptables -A INPUT -p tcp --syn --dport 25565 -m connlimit --connlimit-above 3 -j REJECT  
*(This rule will limit to 3connections/sec from the same IP towards port 25565)*  

$ iptables -A INPUT -p icmp -m icmp --icmp-type 8 -m limit --limit 1/sec --limit-burst 1 -j ACCEPT  
*(OPTIONAL - This rule will allow icmp requests with a limit of 1/sec)*  

$ iptables -P INPUT DROP  
$ iptables -P FORWARD DROP  
*(As stated above, we have now configured our firewall to accept connections only towards ports/services we want.  
Changing the policy to DROP, will DROP all the connections that we have not defined.)*

$ netfilter-persistent save
*(This command is used to save the rules of IPTables and make them persistent, otherwise after a server rebooting,  
the rules will have been resetted)*

