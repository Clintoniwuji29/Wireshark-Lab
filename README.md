# Wireshark-Lab
Hunting Cleartext Credentials

The goal of this lab is to identify and extract cleartext passwords from a packet capture file. Detected credentials can be viewed by navigating to Tools → Credentials, which opens a separate window listing any credentials found. The list includes the packet number, protocol, username, and related details. The entries are interactive, selecting a packet number highlights the packet containing the password, while selecting the username takes you to the packet where the username appears.
<img width="853" height="334" alt="image" src="https://github.com/user-attachments/assets/ffa70671-8d0d-4f68-aaed-e9676ea356b4" />
Detecting ICMP and DNS Anomalies

The purpose of this exercise is to demonstrate how Wireshark can be used to capture and analyze network traffic in order to identify malicious or abnormal behavior.

ICMP Analysis:
ICMP is commonly used for network diagnostics and is a normal part of everyday traffic. Because it is generally trusted, attackers may abuse ICMP for denial-of-service attacks or command-and-control data exfiltration. In such cases, adversaries embed data from protocols such as HTTP, TCP, or SSH within ICMP packets. An unusually high volume of ICMP traffic can be a strong indicator of ICMP tunneling and potential malicious activity. Wireshark display filters can be applied to effectively isolate and analyze ICMP traffic.
<img width="1459" height="367" alt="image" src="https://github.com/user-attachments/assets/262c1fc2-2a7d-4f6f-9205-324f3cb0058b" />
DNS Analysis

The Domain Name System (DNS) is responsible for translating human-readable domain names into IP addresses, making it a critical component of normal internet communication. Because DNS traffic is constant and typically trusted, it is often overlooked during security monitoring. This makes it an attractive channel for attackers to conceal malicious activity, including command-and-control communications and denial-of-service attacks.

Adversaries may abuse DNS by generating abnormal query patterns, excessive request volumes, or encoded data within DNS queries and responses to evade detection. By analyzing DNS traffic in Wireshark, analysts can identify suspicious behaviors such as unusual domain names, high-frequency queries, unexpected response types, or traffic to known malicious domains. Applying appropriate Wireshark display filters helps isolate DNS traffic and enables effective detection of anomalies that may indicate underlying security threats.
<img width="2000" height="2034" alt="image" src="https://github.com/user-attachments/assets/c0c3b8c1-709d-4ed8-9f52-41b681aa1cda" />
Investigation

The first step is to filter ICMP traffic to determine which protocol is being abused and embedded within the malicious packets. Given the typically large volume of ICMP traffic, manually reviewing each packet would be inefficient and impractical. Under normal conditions, ICMP packets are relatively small, usually only a few bytes in size.

To identify potentially malicious activity, a display filter is applied to highlight unusually large ICMP packets, which may indicate tunneling or data exfiltration. The filter icmp and data.len > 64 is used to isolate ICMP traffic with abnormal payload sizes and allow focused inspection of packet contents.

Wireshark’s ability to display raw packet data is especially valuable during this phase of the investigation. By examining the payload of the filtered packets, embedded SSH protocol data is revealed, confirming that ICMP traffic is being exploited to conceal unauthorized communications.
<img width="2000" height="1579" alt="image" src="https://github.com/user-attachments/assets/443052c6-4924-461d-acb3-c99c00055cf5" />
The investigation can then shift to suspicious DNS activity by applying the Wireshark display filter dns.qry.name.len > 15, which helps identify abnormally long DNS query names. In this scenario, the goal is to determine the destination domain receiving the malicious DNS traffic. Analysis shows that the attackers made minimal attempts to obscure their activity beyond using DNS as a masking technique. Multiple large DNS query packets are observed being sent to dataexfil[.]com, indicating DNS-based data exfiltration.
<img width="2000" height="1838" alt="image" src="https://github.com/user-attachments/assets/b5ede716-6cbd-4a2f-bbd1-03403a1775b1" />

