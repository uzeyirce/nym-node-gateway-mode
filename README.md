# <h1 align="center">NYM</h1>
# <h1 align="center">SETTING UP NYM GATEWAY MODE</h1>

Official Tools Provided by the Nym Team

* [Operators Guide: Running an Exit Gateway](https://nym.com/docs/operators/community-counsel/exit-gateway)
* [Operators Guide: Jurisdiction legal advices](https://nym.com/docs/operators/community-counsel/jurisdictions)
  
* [Twitter](https://twitter.com/nymproject)
* [Discord](https://discord.gg/nym)
* [Website](https://nymtech.net/)
* [Docs](https://nymtech.net/operators/nodes/nym-node.html)

* First, get a server with 4 CPUs and 8/16 GB of RAM. The most critical factor to consider when selecting a server is whether it supports IPv6. Some providers include IPv6 by default, while others do not.
* For instance, when purchasing a server from [https://pq.hosting](https://pq.hosting/?from=651056), it is necessary to add IPv6 as an additional feature. This option will be available during the server selection process—simply click “Add IPv6.” If opting for PQ Hosting, choose the Palladium plan with the 4/8 server configuration. However, more cost-effective 4/8 servers may be available through other providers. 
* When selecting the operating system, either Ubuntu 22.04 or 24.04 can be chosen, with 24.04 being the preferred option. The provider will present operating system options during the server purchase process, and Ubuntu should be selected.
For the server location, options include Hong Kong, Japan, Spain, Iceland, Italy, the United Kingdom, Belgium, etc.
* After purchasing the server, you will need to buy a domain. You can do so at https://njal.la/domains/. Once you've bought your domain, you’ll need to set up your hostname. Here’s how to do that:
* Navigate to the “My Domains” section on the platform where the domain was purchased
Click “Manage.”
* Locate the option to “Add Record” and select it.
* Set the record type to “A.”
* For the name, specify a label for your node, such as "gatewayturkey."
* Enter the IP address of the server you purchased and save the record.
* Your hostname will then be formatted as “gatewayturkey.yourdomain.com.” For instance, if your domain is “nymgateway” and you choose "pulse1" as the name, your hostname will be “pulse1.nymgateway.”
* Once the hostname is configured, verify it using https://www.whatsmydns.net. If the results appear green, the setup is complete.
* You can then proceed with configuring your Nym node in both exit and entry gateway modes.
* To execute commands, open a terminal application. On macOS, this application is called Terminal, while on Windows, it is typically referred to as Command Prompt or PowerShell.

* To log in to the terminal, execute the following command using your server's IP address and password:
``` ssh root@YOUR_IP ```

* Then, enter your password when prompted to complete the login process.

Let’s begin!
```bash
sudo apt update && sudo apt upgrade -y
```

```bash
sudo apt install ufw -y
 ```

```bash 
sudo ufw enable
```


 
```bash
sudo ufw allow 1789,1790,8000,9000,9001,22/tcp
``` 
```bash
sudo ufw allow 51822/udp
```
```bash
sudo ufw allow 8080
``` 
```bash
sudo ufw allow 80
```
```bash
sudo ufw allow 443
``` 
```bash
sudo apt install pkg-config build-essential libssl-dev screen curl jq git -y
```
```bash
mkdir ~/.nym
``` 
```bash
mkdir ~/.nym/nym-nodes
```
The following code represents a single command.
```bash
mkdir $HOME/nym-binaries; \
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
The code provided below is the latest release available at this time.
```bash
wget https://github.com/nymtech/nym/releases/download/nym-binaries-v2025.1-reeses/nym-node
```
```bash
chmod +x nym-node
```

Set your Node ID (which can be any identifier you prefer, such as "alexnym") and enter it in the corresponding field below. In the Hostname section, input the hostname you configured under your domain name.
```bash
./nym-node run --id YOURNODEID --init-only --mode entry-gateway --public-ips "$(curl -4 https://ifconfig.me)" --hostname "YOURHOSTNAME" --http-bind-address 0.0.0.0:8080 --mixnet-bind-address 0.0.0.0:1789 --accept-operator-terms-and-conditions --wireguard-enabled true
```
```bash
./nym-node run --id YOURNODEID --init-only --mode exit-gateway --public-ips "$(curl -4 https://ifconfig.me)" --hostname "YOURHOSTNAME" --http-bind-address 0.0.0.0:8080 --mixnet-bind-address 0.0.0.0:1789 --accept-operator-terms-and-conditions --wireguard-enabled true
``` 
```bash
sudo apt install nano
```
```bash
nano /etc/systemd/system/nym-node.service
```
After entering the code above, a page will open. Copy and paste the following into the page, ensuring to include your Node ID:
```bash
[Unit]
Description=Nym Node
StartLimitInterval=350
StartLimitBurst=10

[Service]
User=root
LimitNOFILE=65536
ExecStart=/root/nym-node run --mode exit-gateway --id NODEID --accept-operator-terms-and-conditions --wireguard-enabled true
KillSignal=SIGINT
Restart=on-failure
RestartSec=30

[Install]
WantedBy=multi-user.target
```
* To save the file, press CTRL + X, then Y, and hit ENTER.
* Next, assign a description for your node.
```bash
nano ~/.nym/nym-nodes/NODEID/data/description.toml
```
* After entering the code above, a page will open. Fill it in with any name you prefer. Once finished,
* Press CTRL + O and then ENTER to save the file.
* Press CTRL + X to exit the editor.

```bash
systemctl enable nym-node.service
```
```bash
systemctl daemon-reload
```
```bash
service nym-node start && journalctl -u nym-node -f -n 100
```
* Bonding process.
* To retrieve your Identity Key, enter the following command:
* ./nym-node bonding-information --id NODEID
* Follow the steps below to complete the bonding process:
* Create a wallet and ensure you save the mnemonics.
* Transfer at least 101 NYM to your wallet.
* Complete the settings as follows:
•	Start the bonding process within your wallet.
•	Enter your identity key.
•	Set the Operational Cost
•	Set the Profit Margin 
•	Enable Advanced Settings:
•	Port: 8080
•	Host: Enter your hostname.


* Upon clicking "Next," a lengthy command/code will be provided.
* Copy the command and paste it into the terminal on your server as follows:
* ./nym-node sign --id NODEID --contract-msg ……………………………….
* Paste the code from the terminal into your wallet to finalize the bonding process.
* Return to the terminal.

  

```bash
service nym-node stop
``` 
```bash
mkdir -p /var/www/YOURHOSTNAME
```
```bash
nano /var/www/YOURHOSTNAME/index.html
```
Copy and paste the following text into the extracted page, ensuring to replace the highlighted (EMAILADDRESS) part with your own contact information:
```bash
<!DOCTYPE html>
<html lang="en-US">
<head>
<meta charset="UTF-8">
<title>This is a NYM Exit Gateway</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="icon" type="image/png" href="">
<style>
:root {
  font-family: Consolas, "Ubuntu Mono", Menlo, "DejaVu Sans Mono", monospace;
}
:root{
--background-color: #121726;
--text-color: #f2f2f2;
--link-color: #fb6e4e;
}
html{
background: var(--background-color);
}
body{
margin-left: auto;
margin-right: auto;
padding-left: 5vw;
padding-right: 5vw;
max-width: 1000px;
}
h1{
font-size: 55px;
text-align: center;
color: var(--title-color)
}
p{
color: var(--text-color);
}
p, a{
font-size: 20px;
}
a{
color: var(--link-color);
text-decoration: none;
}
a:hover{
filter: brightness(.8);
text-decoration: underline;
}
.links{
display: flex;
flex-wrap: wrap;
justify-content: space-evenly;
}
.links > a{
margin: 10px;
white-space: nowrap;
}
</style>

</head>
<body>
<main>
<h1>This is a NYM Exit Gateway</h1>
<p style="text-align:center">
</p>

<p>
You are most likely accessing this website because you've had some issue with
the traffic coming from this IP. This router is part of the <a
href="https://nymtech.net/">NYM project</a>, which is
dedicated to <a href="https://nymtech.net/about/mission">create</a> outstanding
privacy software that is legally compliant without sacrificing integrity or
having any backdoors.
This router IP should be generating no other traffic, unless it has been
compromised.</p>

<p>
The Nym mixnet is operated by a decentralised community of node operators
and stakers. The Nym mixnet is trustless, meaning that no parts of the system
nor its operators have access to information that might compromise the privacy
of users. Nym software enacts a strict principle of data minimisation and has
no back doors. The Nym mixnet works by encrypting packets in several layers
and relaying those through a multi-layered network called a mixnet,  eventually
letting the traffic exit the Nym mixnet through an exit gateway like this one.
This design makes it very hard for a service to know which user is connecting to it,
since it can only see the IP-address of the Nym exit gateway:</p>

<p style="text-align:center;margin:40px 0">
<svg xmlns="http://www.w3.org/2000/svg" width="500" viewBox="0 0 490.28 293.73" style="width:100%;max-width:600px">
<desc>Illustration showing how a user might connect to a service through the Nym network. The user first sends their data through three daisy-chained encrypted Nym nodes that exist on three different continents. Then the last Nym node in the chain connects to the target service over the normal internet.</desc>
<defs>
<style>
.t{
fill: var(--text-color);
stroke: var(--text-color);
}
</style>
</defs>
<path fill="#6fc8b7" d="M257.89 69.4c-6.61-6.36-10.62-7.73-18.36-8.62-7.97-1.83-20.06-7.99-24.17-.67-3.29 5.85-18.2 12.3-16.87 2.08.92-7.03 11.06-13.28 17-17.37 8.69-5.99 24.97-2.87 26.1-10.28 1.04-6.86-8.33-13.22-8.55-2.3-.38 12.84-19.62 2.24-8.73-6.2 8.92-6.9 16.05-9.02 25.61-6.15 12.37 4.83 25.58-2.05 33.73-.71 12.37-2.01 24.69-5.25 37.39-3.96 13 .43 24.08-.14 37.06.63 9.8 1.58 16.5 2.87 26.37 3.6 6.6.48 17.68-.82 24.3 1.9 8.3 4.24.44 10.94-6.89 11.8-8.79 1.05-23.59-1.19-26.6 1.86-5.8 7.41 10.75 5.68 11.27 14.54.57 9.45-5.42 9.38-8.72 16-2.7 4.2.3 13.93-1.18 18.45-1.85 5.64-19.64 4.47-14.7 14.4 4.16 8.34 1.17 19.14-10.33 12.02-5.88-3.65-9.85-22.04-15.66-21.9-11.06.27-11.37 13.18-12.7 17.52-1.3 4.27-3.79 2.33-6-.63-3.54-4.76-7.75-14.22-12.01-17.32-6.12-4.46-10.75-1.17-15.55 2.83-5.63 4.69-8.78 7.82-7.46 16.5.78 9.1-12.9 15.84-14.98 24.09-2.61 10.32-2.57 22.12-8.81 31.47-4 5.98-14.03 20.12-21.27 14.97-7.5-5.34-7.22-14.6-9.56-23.08-2.5-9.02.6-17.35-2.57-26.2-2.45-6.82-6.23-14.54-13.01-13.24-6.5.92-15.08 1.38-19.23-2.97-5.65-5.93-6-10.1-6.61-18.56 1.65-6.94 5.79-12.64 10.38-18.63 3.4-4.42 17.45-10.39 25.26-7.83 10.35 3.38 17.43 10.5 28.95 8.57 3.12-.53 9.14-4.65 7.1-6.62zm-145.6 37.27c-4.96-1.27-11.57 1.13-11.8 6.94-1.48 5.59-4.82 10.62-5.8 16.32.56 6.42 4.34 12.02 8.18 16.97 3.72 3.85 8.58 7.37 9.3 13.1 1.24 5.88 1.6 11.92 2.28 17.87.34 9.37.95 19.67 7.29 27.16 4.26 3.83 8.4-2.15 6.52-6.3-.54-4.54-.6-9.11 1.01-13.27 4.2-6.7 7.32-10.57 12.44-16.64 5.6-7.16 12.74-11.75 14-20.9.56-4.26 5.72-13.86 1.7-16.72-3.14-2.3-15.83-4-18.86-6.49-2.36-1.71-3.86-9.2-9.86-12.07-4.91-3.1-10.28-6.73-16.4-5.97zm11.16-49.42c6.13-2.93 10.58-4.77 14.61-10.25 3.5-4.28 2.46-12.62-2.59-15.45-7.27-3.22-13.08 5.78-18.81 8.71-5.96 4.2-12.07-5.48-6.44-10.6 5.53-4.13.38-9.2-5.66-8.48-6.12.8-12.48-1.45-18.6-1.73-5.3-.7-10.13-1-15.45-1.37-5.37-.05-16.51-2.23-25.13.87-5.42 1.79-12.5 5.3-16.73 9.06-4.85 4.2.2 7.56 5.54 7.45 5.3-.22 16.8-5.36 20.16.98 3.68 8.13-5.82 18.29-5.2 26.69.1 6.2 3.37 11 4.74 16.98 1.62 5.94 6.17 10.45 10 15.14 4.7 5.06 13.06 6.3 19.53 8.23 7.46.14 3.34-9.23 3.01-14.11 1.77-7.15 8.49-7.82 12.68-13.5 7.14-7.72 16.41-13.4 24.34-18.62zM190.88 3.1c-4.69 0-13.33.04-18.17-.34-7.65.12-13.1-.62-19.48-1.09-3.67.39-9.09 3.34-5.28 7.04 3.8.94 7.32 4.92 7.1 9.31 1.32 4.68 1.2 11.96 6.53 13.88 4.76-.2 7.12-7.6 11.93-8.25 6.85-2.05 12.5-4.58 17.87-9.09 2.48-2.76 7.94-6.38 5.26-10.33-1.55-1.31-2.18-.64-5.76-1.13zm178.81 157.37c-2.66 10.08-5.88 24.97 9.4 15.43 7.97-5.72 12.58-2.02 17.47 1.15.5.43 2.65 9.2 7.19 8.53 5.43-2.1 11.55-5.1 14.96-11.2 2.6-4.62 3.6-12.39 2.76-13.22-3.18-3.43-6.24-11.03-7.7-15.1-.76-2.14-2.24-2.6-2.74-.4-2.82 12.85-6.04 1.22-10.12-.05-8.2-1.67-29.62 7.17-31.22 14.86z"/>
<g fill="none">
<path stroke="#cf63a6" stroke-linecap="round" stroke-width="2.76" d="M135.2 140.58c61.4-3.82 115.95-118.83 151.45-103.33"/>
<path stroke="#cf63a6" stroke-linecap="round" stroke-width="2.76" d="M74.43 46.66c38.15 8.21 64.05 42.26 60.78 93.92M286.65 37.25c-9.6 39.44-3.57 57.12-35.64 91.98"/>
<path stroke="#e4c101" stroke-dasharray="9.06,2.265" stroke-width="2.27" d="M397.92 162.52c-31.38 1.26-90.89-53.54-148.3-36.17"/>
<path stroke="#cf63a6" stroke-linecap="round" stroke-width="2.77" d="M17.6 245.88c14.35 0 14.4.05 28-.03"/>
<path stroke="#e3bf01" stroke-dasharray="9.06,2.265" stroke-width="2.27" d="M46.26 274.14c-17.52-.12-16.68.08-30.34.07"/>
</g>
<g transform="translate(120.8 -35.81)">
<circle cx="509.78" cy="68.74" r="18.12" fill="#240a3b" transform="translate(-93.3 38.03) scale(.50637)"/>
<circle cx="440.95" cy="251.87" r="18.12" fill="#240a3b" transform="translate(-93.3 38.03) scale(.50637)"/>
<circle cx="212.62" cy="272.19" r="18.12" fill="#240a3b" transform="translate(-93.3 38.03) scale(.50637)"/>
<circle cx="92.12" cy="87.56" r="18.12" fill="#240a3b" transform="translate(-93.3 38.03) scale(.50637)"/>
<circle cx="730.88" cy="315.83" r="18.12" fill="#67727b" transform="translate(-93.3 38.03) scale(.50637)"/>
<circle cx="-102.85" cy="282.18" r="9.18" fill="#240a3b"/>
<circle cx="-102.85" cy="309.94" r="9.18" fill="#67727b"/>
</g>
<g class="t">
<text xml:space="preserve" x="-24.76" y="10.37" stroke-width=".26" font-size="16.93" font-weight="700" style="line-height:1.25" transform="translate(27.79 2.5)" word-spacing="0"><tspan x="-24.76" y="10.37">The user</tspan></text>
<text xml:space="preserve" x="150.63" y="196.62" stroke-width=".26" font-size="16.93" font-weight="700" style="line-height:1.25" transform="translate(27.79 2.5)" word-spacing="0"><tspan x="150.63" y="196.62">This server</tspan></text>
<text xml:space="preserve" x="346.39" y="202.63" stroke-width=".26" font-size="16.93" font-weight="700" style="line-height:1.25" transform="translate(27.79 2.5)" word-spacing="0"><tspan x="346.39" y="202.63">Your service</tspan></text>
<text xml:space="preserve" x="34.52" y="249.07" stroke-width=".26" font-size="16.93" font-weight="700" style="line-height:1.25" transform="translate(27.79 2.5)" word-spacing="0"><tspan x="34.52" y="249.07">Nym network link</tspan></text>
<text xml:space="preserve" x="34.13" y="276.05" stroke-width=".26" font-size="16.93" font-weight="700" style="line-height:1.25" transform="translate(27.79 2.5)" word-spacing="0"><tspan x="34.13" y="276.05">Unencrypted link</tspan></text>
<path fill="none" stroke-linecap="round" stroke-width="1.67" d="M222.6 184.1c-2.6-15.27 8.95-23.6 18.43-38.86m186.75 45.61c-.68-10.17-9.4-17.68-18.08-23.49"/>
<path fill="none" stroke-linecap="round" stroke-width="1.67" d="M240.99 153.41c.35-3.41 1.19-6.17.04-8.17m-7.15 5.48c1.83-2.8 4.58-4.45 7.15-5.48"/>
<path fill="none" stroke-linecap="round" stroke-width="1.67" d="M412.43 173.21c-2.2-3.15-2.54-3.85-2.73-5.85m0 0c2.46-.65 3.85.01 6.67 1.24M61.62 40.8C48.89 36.98 36.45 27.54 36.9 18.96M61.62 40.8c.05-2.58-3.58-4.8-5.25-5.26m-2.65 6.04c1.8.54 6.8 1.31 7.9-.78"/>
<path fill="none" stroke-linecap="round" stroke-linejoin="round" stroke-width="2.44" d="M1.22 229.4h247.74v63.1H1.22z"/>
</g>
</svg>
</p>

<p>
<a href="https://nymtech.net/about/mixnet">Read more about how Nym works.</a></p>

<p>
Nym relies on a growing ecosystem of users, developers and researcher partners
aligned with the mission to make sure Nym software is running, remains usable
and solves real problems. While Nym is not designed for malicious computer
users, it is true that they can use the network for malicious ends. This
is largely because criminals and hackers have significantly better access to
privacy and anonymity than do the regular users whom they prey upon. Criminals
can and do build, sell, and trade far larger and more powerful networks than
Nym on a daily basis. Thus, in the mind of this operator, the social need for
easily accessible censorship-resistant private, anonymous communication trumps
the risk of unskilled bad actors, who are almost always more easily uncovered
by traditional police work than by extensive monitoring and surveillance anyway.</p>

<p>
In terms of applicable law, the best way to understand Nym is to consider it a
network of routers operating as common carriers, much like the Internet
backbone. However, unlike the Internet backbone routers, Nym mixnodes do not
contain identifiable routing information about the source of a packet and do
mix the user internet traffic with that of other users, making communications
private and protecting not just the user content but the metadata
(user's IP address, who the user talks to, when, where, from what device and
more) and no single Nym node can determine both the origin and destination
of a given transmission.</p>

<p>
As such, there is little the operator of this Exit Gateway can do to help you
track the connection further. This Exit Gateway maintains no logs of any of the
Nym mixnet traffic, so there is little that can be done to trace either legitimate or
illegitimate traffic (or to filter one from the other).  Attempts to
seize this router will accomplish nothing.</p>

<!-- FIXME: US-Only section. Remove if you are a non-US operator -->
<!--
<p>
Furthermore, this machine also serves as a carrier of email, which means that
its contents are further protected under the ECPA. <a
href="https://www.law.cornell.edu/uscode/text/18/2707">18
USC 2707</a> explicitly allows for civil remedies ($1000/account
<i>plus</i>  legal fees)
in the event of a seizure executed without good faith or probable cause (it
should be clear at this point that traffic with an originating IP address of
FIXME_DNS_NAME should not constitute probable cause to seize the
machine). Similar considerations exist for 1st amendment content on this
machine.</p>
-->
<!-- FIXME: May or may not be US-only. Some non-US tor nodes have in
     fact reported DMCA harassment... -->
<!--
<p>
If you are a representative of a company who feels that this router is being
used to violate the DMCA, please be aware that this machine does not host or
contain any illegal content. Also be aware that network infrastructure
maintainers are not liable for the type of content that passes over their
equipment, in accordance with <a
href="https://www.law.cornell.edu/uscode/text/17/512">DMCA
"safe harbor" provisions</a>. In other words, you will have just as much luck
sending a takedown notice to the Internet backbone providers.
</p>
-->

<p>To decentralise and enable privacy for a broad range of services, this
Exit Gateway adopts an <a href="https://nymtech.net/.wellknown/network-requester/exit-policy.txt">Exit Policy</a>
in accordance with the <a href="https://tornull.org/">Tor Null ‘deny’ list</a>
and the <a href="https://tornull.org/tor-reduced-reduced-exit-policy.php">Tor reduced policy</a>,
which are two established safeguards.
</p>

<p>
That being said, if you still have a complaint about the router, you may email the
 <a href="mailto:>EMAILADDRESS>">maintainer</a>. If complaints are related
 to a particular service that is being abused, the maintainer will submit that to the
 NYM Operators Community in order to add it to the Exit Policy cited above.
If approved, that would prevent this router from allowing that traffic to exit through it.
That can be done only on an IP+destination port basis, however. Common P2P ports are already blocked.</p>

<p>
You also have the option of blocking this IP address and others on the Nym network if you so desire.
 The Nym project provides a <a href="https://explorer.nymtech.net/network-components/gateways">
 web service</a> to fetch a list of all IP addresses of Nym Gateway Exit nodes that allow exiting to a
specified IP:port combination. Please be considerate when using these options.</p>

</main>
</body>
</html>
```
To save the file, press CTRL + X, then Y, and hit ENTER.
```bash
nano ~/.nym/nym-nodes/NODEID/config/config.toml
```
In the config file, locate the "landing" section and paste the following text:
```bash
'/var/www/YOURHOSTNAME'
```
In the config file, locate the following section, which is set to false by default, and change it to true:
```bash
# Specifies whether this node can operate in an exit mode.
exit = true
```
```bash
sudo apt install nginx
```
```bash
ufw allow 'Nginx Full'
```
```bash
ufw status
```
```bash
systemctl status nginx
```
```bash
unlink /etc/nginx/sites-enabled/default
```
```bash
systemctl restart nginx
``` 
```bash
nano /etc/nginx/sites-available/YOURHOSTNAME
```
Copy and paste the following text into the opened page, ensuring to include your hostname.
```bash
server {
    listen 80;
    listen [::]:80;

    # Replace <HOSTNAME> with your domain name
    server_name YOURHOSTNAME;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
To save the file, press CTRL + X, then Y, and hit ENTER.
```bash
ln -s /etc/nginx/sites-available/YOURHOSTNAME /etc/nginx/sites-enabled
```
```bash
nginx -t
```
The result of this command should display 'successful' or 'ok'.
```bash
systemctl restart nginx
```
```bash
apt install certbot python3-certbot-nginx
```
```bash
nano /etc/nginx/sites-available/wss-config-nym
```
Copy and paste the following text into the opened page, ensuring to include your hostname.
```bash
server {
    listen 9001 ssl http2;
    listen [::]:9001 ssl http2;

    server_name YOURHOSTNAME;

    ssl_certificate /etc/letsencrypt/live/YOURHOSTNAME/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/YOURHOSTNAME/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # Ignore favicon requests
    location /favicon.ico {
        return 204;
        access_log     off;
        log_not_found  off;
    }

    location / {

        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Credentials' 'true';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS, HEAD';
        add_header 'Access-Control-Allow-Headers' '*';

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header X-Forwarded-For $remote_addr;

        proxy_pass http://localhost:9000;
        proxy_intercept_errors on;
    }
}
```
To save the file, press CTRL + X, then Y, and hit ENTER.
```bash
ln -s /etc/nginx/sites-available/wss-config-nym /etc/nginx/sites-enabled
```
```bash
nginx -t
```
The result of this command should display 'successful' or 'ok'.
```bash
nano ~/.nym/nym-nodes/NODEID/config/config.toml
```
Change the following port, which is set to the default value of 0, to 9001.
* announce_wss_port = 9001
* To save the file, press CTRL + X, then Y, and hit ENTER.
```bash
 systemctl enable nym-node.service
```
```bash
systemctl daemon-reload
```
```bash
service nym-node start && journalctl -u nym-node -f -n 100
```
* If the packaging process has started, there are no issues.
* Press CTRL + C.
* Important note: CTRL + C is used to stop or interrupt a running command or process in the terminal.

```bash
sudo apt install node-ws
```
```bash
wscat -c wss://YOURHOSTNAME:9001
```
* If it shows green, you are good to go.
* Let's check both of these commands to ensure they don't produce any errors:

```bash
$HOME/nym-binaries/network_tunnel_manager.sh joke_through_the_mixnet
```
```bash
$HOME/nym-binaries/network_tunnel_manager.sh joke_through_wg_tunnel
```
For log check:
```bash
journalctl -u nym-node -f -n 100
```
For a status check:
```bash
service nym-node status
```
Here, you can check if your node is active. Press CTRL + F and paste your Identity to search for it.
```bash
https://validator.nymtech.net/api/v1/nym-nodes/described 
```























