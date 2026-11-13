# penetration-testing-report
Kali Linux Lab Report: theHarvester and Nmap Topology
1. Summary
This week I set up my first Kali Linux virtual machine and used it to try out two reconnaissance tools. The first was theHarvester, an OSINT (open-source intelligence) tool that gathers emails, hostnames, and IP addresses about a domain from public sources. The second was Nmap, a network scanner, where I looked at the network topology map it produced. This report covers what I did, what I saw, the problems I ran into, and what I learned.

2. Lab Environment
Item	Details
Virtual machine	Kali Linux 2026.3 (amd64)
Hypervisor	Oracle VirtualBox
Host machine	Windows 11 laptop
Tool 1	theHarvester 4.11.1 (by Christian Martorella, Edge-Security Research)
Tool 2	Nmap (topology view)
Lab date	November 12, 2026

3. Part 1: theHarvester
3.1 What the tool does
theHarvester collects publicly available information about a target domain. It searches sources such as search engines and public databases for email addresses, subdomains and hosts, IP addresses, and employee names. This is called passive reconnaissance because it does not touch the target's systems directly. It only asks third-party sources what they already know. Security teams use it to see how much information about an organization is exposed to the public.
3.2 Run 1: single source search (about 6:21 PM)
My first run used microsoft.com as the target. The output showed it searching Baidu, and then it reported:
•	No IPs found
•	No emails found
•	No people found
•	No hosts found
So this run came back completely empty. My guess is that one search engine on its own gave very little, and that Baidu may have limited or blocked automated queries. I could not confirm the cause from the output alone, so I treated this as a sign that I needed to try more sources.
3.3 Run 2: all sources (about 6:27 PM)
For the second run I used this command:
theHarvester -d microsoft.com -l 50 -b all
Here is what each option does:
•	-d microsoft.com sets the target domain.
•	-l 50 limits the results to 50 per source.
•	-b all tells the tool to use every available data source.
The tool read its configuration files, proxies.yaml and api-keys.yaml, from /etc/theHarvester/. Then it started failing on sources one at a time with messages like these:
•	[!] Missing API key for bevigil.
•	[!] Missing API key for Bitbucket.
•	[!] Missing API key for bufferoverun.
The error format was the same for each one: Failed to process [source] search for word: 'microsoft.com'.
3.4 What I learned from theHarvester
1.	Many sources need API keys. -b all sounds like the most thorough option, but it also runs every source that requires a key. Without keys, those sources simply fail. The keys go in /etc/theHarvester/api-keys.yaml.
2.	Reading the errors matters. The "no results" message on the first run looked like the tool was broken, but the second run showed me the real reasons were setup and source limitations.
3.	Free sources are the place to start. Next time I plan to pick specific sources that do not need a key, instead of running everything, and get free API keys for the ones I want to use later.
4.	Passive does not mean unlimited. The results depend on what each source has indexed, so a large company can still return little if the sources are limited or blocked.

4. Part 2: Nmap Topology
4.1 What the topology shows
The topology map (a view in Zenmap, the graphical front end for Nmap) shows two nodes:
•	localhost (black node, center): this is my Kali VM, the machine running the scan.
•	192.168.56.1 (green node): the host that was scanned, connected to localhost by a single line.
The small ring around localhost is how Zenmap draws distance in network hops, and the scanned host sits right at the edge of the first ring. That means it is one hop away, with no routers in between.
4.2 Reading the results
•	The address 192.168.56.1 is, as far as I know, the default address VirtualBox gives the host computer on its Host-Only network. If that is the case here, the scanned device is my own Windows laptop, which is what I would expect for a lab setup.
•	The node is green. In Zenmap, green means the host has fewer than three open ports. Yellow means three to six, and red means more than six. A green node tells me this machine is exposing very little to the network, which is a good sign from a security point of view.
•	Only one host appears, so my lab network is just two machines: the Kali VM and the host.
4.3 What I learned from the topology
1.	A topology map is a fast way to see how devices are connected, but it only shows what the scan could reach.
2.	Node colors give a quick read on how many open ports a host has, which helps decide what deserves a closer look first.
3.	My lab network is small. To get more interesting maps, I would need more machines on the network, for example a second VM.

5. Problems and Fixes
Problem	What I found	Plan
theHarvester returned nothing on the Baidu run	One source gave no data	Try multiple sources instead of one
Many "Missing API key" errors on -b all	Sources need keys in api-keys.yaml	Use free sources first, add keys later
Topology only shows one host	The lab network has only two machines	Add another VM to scan

6. Safety and Ethics Notes
•	theHarvester only queries public sources, so it is passive and was a reasonable tool to practice on a well-known public domain. I did not attempt to access any systems.
•	My Nmap scan was against my own lab network (the VirtualBox host-only address). I should only scan systems that I own or have written permission to test. Scanning other people's networks without permission can be illegal and can get an account or connection banned.
---------
7. Next Steps
1.	Re-run theHarvester with specific free sources instead of -b all.
2.	Set up free API keys for a few sources and test whether results improve.
3.	Save theHarvester output with the -f option so I have a file to attach to future reports.
4.	Add a second VM to the lab network so the topology shows more than one host.
5.	Look at the detailed Nmap results for 192.168.56.1, including which ports are open and which services are running.
------
8. Conclusion
This was a good first hands-on experience with Kali Linux. theHarvester taught me that OSINT tools depend heavily on setup and data sources, and that the error messages are worth reading. The Nmap topology gave me a clear picture of how my VM connects to the network and how to read the results at a glance. My next goal is to collect better results from theHarvester and dig into the port-level details in Nmap.

9.Evidence of assignment completed 
<img width="1365" height="767" alt="Screenshot 2026-11-12 182142" src="https://github.com/user-attachments/assets/d4fcba25-95fe-4f54-9def-a44852c4a54a" />

<img width="1365" height="767" alt="Screenshot 2026-11-12 182727" src="https://github.com/user-attachments/assets/846f090f-70a4-40b0-b209-dc2037895438" />

<img width="503" height="441" alt="topolongy" src="https://github.com/user-attachments/assets/df2fa7d8-21f3-4dbb-a4f0-1bc831e428b6" />




