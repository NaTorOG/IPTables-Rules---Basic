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
and to now install webservices like Apache or NGIX.  

###### SSH Tips
Run SSH using **Keys** and not Password! It is very important because we will avoid SSH Bruteforces attemps and we can  
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


