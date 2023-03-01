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
It is highly recommended to run all the services in localhost(127.0.0.1) like MySQL or Backend Servers(Spigot servers not Proxy BungeeGuard/Velocity).  

