# penetration-testing-report
Kali Linux Lab Report: theHarvester and Nmap Topology
Prepared by: IT Intern | Date: November 12, 2026
1. Summary
This week I set up my first Kali Linux virtual machine and used it to try out two reconnaissance tools. The first was theHarvester, an OSINT (open-source intelligence) tool that gathers emails, hostnames, and IP addresses about a domain from public sources. The second was Nmap, a network scanner, where I looked at the network topology map it produced. This report covers what I did, what I saw, the problems I ran into, and what I learned.
The short version: theHarvester returned no results on my first run, and my second run showed that most of its data sources need API keys that I had not set up yet. The Nmap topology showed eight devices on my network, all one hop from my VM and all with few open ports.

2. Lab Environment
Item
Details
Virtual machine
Kali Linux 2026.3 (amd64)
Hypervisor
Oracle VirtualBox
Host machine
Windows 11 laptop
Tool 1
theHarvester 4.11.1 (by Christian Martorella, Edge-Security Research)
Tool 2
Nmap (topology view)
Lab date
November 12, 2026
Kali was running inside VirtualBox as my first Kali VM. I did all of my work from the Kali terminal.

3. Part 1: theHarvester
3.1 What the tool does
theHarvester collects publicly available information about a target domain. It searches sources such as search engines and public databases for email addresses, subdomains and hosts, IP addresses, and employee names. This is called passive reconnaissance because it does not touch the target's systems directly. It only asks third-party sources what they already know. Security teams use it to see how much information about an organization is exposed to the public.
3.2 Run 1: single source search (about 6:21 PM)
My first run used microsoft.com as the target. The output showed it searching Baidu, and then it reported:
• No IPs found
• No emails found
• No people found
• No hosts found
So this run came back completely empty. My guess is that one search engine on its own gave very little, and that Baidu may have limited or blocked automated queries. I could not confirm the cause from the output alone, so I treated this as a sign that I needed to try more sources.
3.3 Run 2: all sources (about 6:27 PM)
For the second run I used this command:
theHarvester -d microsoft.com -l 50 -b all
Here is what each option does:
• -d microsoft.com sets the target domain.
• -l 50 limits the results to 50 per source.
• -b all tells the tool to use every available data source.
The tool read its configuration files, proxies.yaml and api-keys.yaml, from /etc/theHarvester/. Then it started failing on sources one at a time with messages like these:
• [!] Missing API key for bevigil.
• [!] Missing API key for Bitbucket.
• [!] Missing API key for bufferoverun.
The error format was the same for each one: Failed to process [source] search for word: 'microsoft.com'.
3.4 What I learned from theHarvester
1.  Many sources need API keys. -b all sounds like the most thorough option, but it also runs every source that requires a key. Without keys, those sources simply fail. The keys go in /etc/theHarvester/api-keys.yaml.
2.  Reading the errors matters. The "no results" message on the first run looked like the tool was broken, but the second run showed me the real reasons were setup and source limitations.
3.  Free sources are the place to start. Next time I plan to pick specific sources that do not need a key, instead of running everything, and get free API keys for the ones I want to use later.
4.  Passive does not mean unlimited. The results depend on what each source has indexed, so a large company can still return little if the sources are limited or blocked.

4. Part 2: Nmap Topology
4.1 What the topology shows
The new topology map (a view in Zenmap, the graphical front end for Nmap) shows eight devices connected to localhost, which is my Kali VM and the machine running the scan. Every device is a green node on the first ring around localhost, which means each one is one hop away.
• localhost (black node, center): my Kali VM.
• Eight green nodes: the devices the scan found on my network, each linked to localhost by its own line.
• Readable labels: two Roku Express streaming devices on 192.168.1.x addresses, and one device at 192.168.1.1, which looks like the router (its name is partly cut off).
Because the labels overlap on the map, I could only read some of the device names and IP addresses.
4.2 Reading the results
• The addresses I could read all start with 192.168.1, so these devices share one private network, and 192.168.1.1 is most likely the router.
• Every node is green. In Zenmap, green means a host has fewer than three open ports (yellow is three to six, red is more than six), so none of these devices exposes much.
• All nodes sit on the same ring, so there are no routers between my VM and the devices. It is a flat network.
4.3 What I learned from the topology
1.  A topology map is a fast way to see how many devices are on a network and how they connect, but it only shows what the scan could reach.
2.  Node colors give a quick read on open ports, which helps decide which device deserves a closer look first.
3.  My network has more devices than I expected, including streaming players, so it is worth knowing what is connected.
4.  Overlapping labels make a crowded map hard to read, so I should use a larger view or the host list for exact names.

5. Problems and Fixes
Problem
What I found
Plan
theHarvester returned nothing on the Baidu run
One source gave no data
Try multiple sources instead of one
Many "Missing API key" errors on -b all
Sources need keys in api-keys.yaml
Use free sources first, add keys later
Topology labels overlap and are hard to read
Eight devices sit close together in a small view
Zoom in or check the Zenmap Hosts tab for exact names

6. Safety and Ethics Notes
• theHarvester only queries public sources, so it is passive and was a reasonable tool to practice on a well-known public domain. I did not attempt to access any systems.
• My Nmap scan was against my own local network. I should only scan systems that I own or have written permission to test. Scanning other people's networks without permission can be illegal and can get an account or connection banned.

7. Next Steps
1.  Re-run theHarvester with specific free sources instead of -b all.
2.  Set up free API keys for a few sources and test whether results improve.
3.  Save theHarvester output with the -f option so I have a file to attach to future reports.
4.  Zoom in on the topology map or use the Zenmap Hosts tab so every device name and IP address is readable.
5.  Look at the detailed Nmap results for each of the eight devices, including which ports are open and which services are running.

8. Conclusion
This was a good first hands-on experience with Kali Linux. theHarvester taught me that OSINT tools depend heavily on setup and data sources, and that the error messages are worth reading. The Nmap topology gave me a quick picture of the eight devices on my network and how my VM connects to them and how to read the results at a glance. My next goal is to collect better results from theHarvester and dig into the port-level details in Nmap.

9.Assignment 
<img width="503" height="441" alt="map" src="https://github.com/user-attachments/assets/f4eb5e2d-0952-4472-8f68-34b4d62589f8" />

<img width="1365" height="767" alt="Screenshot 2026-11-12 182142" src="https://github.com/user-attachments/assets/220b79f7-56cb-4698-ab3e-08b7cd836ace" />

<img width="1365" height="767" alt="Screenshot 2026-11-12 182727" src="https://github.com/user-attachments/assets/4151f569-b655-41b3-84e0-0dcce55a376b" />

