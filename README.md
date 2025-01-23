# <h1 align="center">NYM</h1>
# <h1 align="center">SETTING UP NYM GATEWAY MODE</h1>

* [Twitter](https://twitter.com/nymproject)
* [Discord](https://discord.gg/nym)
* [Website](https://nymtech.net/)
* [Docs](https://nymtech.net/operators/nodes/nym-node.html)

* First, we’ll be getting a server with 4 CPUs and 8/16 GB RAM. The most important thing to check when selecting a server is whether it supports IPv6. Some providers include IPv6 by default, while others do not.
* For example, if you're purchasing a server from [https://pq.hosting](https://pq.hosting/?from=651056), you'll need to add IPv6 as an extra feature. This option appears when you're purchasing the server; just click “Add IPv6.” If you choose PQ Hosting, select the Palladium option with the 4/8 server configuration. You may find cheaper 4/8 servers at Hetzner, but running an exit gateway there can be challenging. Be cautious to avoid having your account banned. I received a warning but wasn’t banned and ended up shutting down the server. However, the risk is entirely yours.
* When selecting the operating system, you can choose either Ubuntu 22.04 or 24.04. 24.04 is bettet. The provider will suggest options during the server purchase, and you should select Ubuntu.
For location, you can choose from Hong Kong, Japan, Spain, Iceland, Italy, the UK, or Belgium.
* After purchasing the server, you will need to buy a domain. You can do so at https://njal.la/domains/. Once you've bought your domain, you’ll need to set up your hostname. Here’s how to do that:
* Go to “My Domains” where you purchased the domain.
Click “Manage.”
*Once inside, you’ll see the option to “Add Record.” Click on it.
*Choose the record type as “A.”
*For the name, enter a label for your node, like "gatewayturkey."
*For the IP address, use the IP address of the server you just bought, then save it.
*Your hostname will then be something like “gatewayturkey.yourdomain.com.” For example, my domain is “nymgateway” and I chose "pulse1" as the name. My hostname is “pulse1.nymgateway.”
*Once your hostname is set up, check it at https://www.whatsmydns.net. If it shows green, everything is set.
*Now, you can proceed with setting up your Nym node in exit and entry gateway mode.
*To enter commands, open the "Terminal" application. On a Mac, it's called Terminal. I'm not sure what it's called on Windows, but it should be a similar program like Command Prompt or PowerShell.

*To log in to the terminal, use the following command with your server's IP and password
``` ssh root@YOUR_IP ```

*Then, enter your password when prompted.

Let’s begin!

* ``` sh sudo apt update && sudo apt upgrade -y
 
* ```sudo apt install ufw -y ``` 
* ```sudo ufw enable``` 
```sudo ufw allow 1789,1790,8000,9000,9001,22/tcp``` 
```sudo ufw allow 51822/udp```
```sudo ufw allow 8080 ``` 
```sudo ufw allow 80 ```
```sudo ufw allow 443``` 
```sudo apt install pkg-config build-essential libssl-dev screen curl jq git -y ```
```mkdir ~/.nym ``` 
```mkdir ~/.nym/nym-nodes```
/////////////////////////// The code below is a single command.
``` mkdir $HOME/nym-binaries; \
 
curl -L https://raw.githubusercontent.com/nymtech/nym/refs/heads/develop/scripts/network_tunnel_manager.sh -o $HOME/nym-binaries/network_tunnel_manager.sh && \
chmod +x $HOME/nym-binaries/network_tunnel_manager.sh; \
 
$HOME/nym-binaries/network_tunnel_manager.sh check_nymtun_iptables  ; \
$HOME/nym-binaries/network_tunnel_manager.sh remove_duplicate_rules nymtun0 ;\
$HOME/nym-binaries/network_tunnel_manager.sh remove_duplicate_rules nymwg;\
$HOME/nym-binaries/network_tunnel_manager.sh check_nymtun_iptables ; \
$HOME/nym-binaries/network_tunnel_manager.sh adjust_ip_forwarding ; \
$HOME/nym-binaries/network_tunnel_manager.sh apply_iptables_rules ; \
$HOME/nym-binaries/network_tunnel_manager.sh check_nymtun_iptables ; \
$HOME/nym-binaries/network_tunnel_manager.sh apply_iptables_rules_wg ; \
$HOME/nym-binaries/network_tunnel_manager.sh configure_dns_and_icmp_wg ; \
$HOME/nym-binaries/network_tunnel_manager.sh adjust_ip_forwarding ; \
$HOME/nym-binaries/network_tunnel_manager.sh check_ipv6_ipv4_forwarding
``` 
``` ```
``` ```
``` ``` 
``` ```
``` ``` 
``` ```
``` ``` 
``` ```
``` ``` 
``` ```
``` ``` 
``` ```
``` ```
``` ``` 
``` ```
``` ``` 
``` ```
``` ``` 
``` ```
``` ``` 
``` ```
``` ``` 
``` ```
``` ``` 
``` ```
``` ``` 
``` ```





























