# IPS_Configuration_Suricata
## Overview
In this repository we will learn about Intrusion Prevention System (IPS) and how to configure it.

**Intrusion Prevention System (IPS)**

**Definition:**
An IPS is an active system that not only detects malicious activities but also takes automated steps to block or mitigate the detected threats in real-time.

**Key Features:**
- **Active Prevention:** Automatically blocks traffic or actions deemed malicious.
- **Inline Deployment:** Typically deployed inline with network traffic, enabling it to intercept and respond to threats instantly.

**Pros:**
- Real-time threat mitigation reduces the risk of successful attacks.
- Automates responses, minimizing human intervention.

**Cons:**
- May disrupt legitimate traffic if not configured properly.
- Requires more processing power and can introduce latency.

**Use Case:** 
Suitable for environments where automated threat prevention is critical, such as financial institutions, healthcare, and e-commerce platforms.

## IPS Configuration

Change the directory for the configuration
```
cd /etc/suricata
```
Edit the configuration file to IPS
```
sudo nano suricata.yaml
```
Make the following changes in the **af-packet** section:
af-packet:
```
  - interface: eth0
  - threads: auto
  - cluster-id: 99
  - cluster-type: cluster_flow
  - defrag: yes
  - use-mmap: yes
  - buffer-size: 64535
  - copy-mode: ips
  - copy-iface: eth1
  - interface: default
```

Make the following changes in the **nfq** section:


  nfqueue:
  
mode: repeat

fail-open: yes

enabled: yes


Run a config change success test:
```
sudo suricata -T -c /etc/suricata/suricata.yaml
```

Configure iptables for ips:
```
sudo iptables -I INPUT -j NFQUEUE --queue-num 0
sudo iptables -I FORWARD -j NFQUEUE --queue-num 0
sudo iptables -I OUTPUT -j NFQUEUE --queue-num 0

```
Configure for the above af-packet mode:
```
sudo iptables -t raw -I PREROUTING -i eth0 -j NFQUEUE --queue-num 0
``` 

Configure Rules:
Change directory to rules:
```
cd /var/lib/suricata/rules
```
Delete the existing rules set for IDS and add the following rules present in [local.txt](https://github.com/raghu2301/IPS_Configuration_Suricata/blob/main/local.txt) to local.rules
```
sudo nano local.rules
```

Update suricata ruleset database:
```
sudo suricata-update
```

Run test for configuration file:
```
sudo suricata -T -c /etc/suricata/suricata.yaml
```

Suricata IPS Usage:
Start suricata in IPS mode 
```
sudo suricata -c /etc/suricata/suricata.yaml -i eth0 --af-packet
```

Checking logs:
Change directory:
```
cd /var/log/suricata
```
Check the logs:
```
sudo nano eve.json
```
```
sudo nano fast.log
```

## Conclusion
Intrusion Prevention Systems (IPS) are a vital component of modern cybersecurity frameworks, offering proactive protection by detecting and blocking malicious activities in real-time. IPS actively prevents threats by halting malicious traffic, thereby reducing the risk of data breaches, system compromises, and downtime.
IPS provides a robust defense against sophisticated cyberattacks. As organizations continue to face an increasing volume and complexity of threats, IPS will remain essential for maintaining secure and resilient systems in a dynamic digital landscape.
