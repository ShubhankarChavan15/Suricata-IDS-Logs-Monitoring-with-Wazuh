# Suricata-IDS-Logs-Monitoring-with-Wazuh
In this project, you will integrate Suricata IDS logs with Wazuh SIEM for centralized monitoring. You will configure Wazuh to ingest Suricata’s eve.json output, which includes DNS, TLS, and SSH logs. This enables detection and visualization of network-based threats in real-time. 
## Objective
Integrate Suricata IDS logs with Wazuh to monitor `eve.json` outputs, including DNS and TLS traffic events for network threat detection.

---

## Lab Setup

| Component | Role |
|-----------|------|
| Suricata IDS | Network Intrusion Detection System generating `eve.json` |
| Wazuh Manager | Centralized SIEM for log collection and alert correlation |
| Wazuh Dashboard | Visualization layer for Suricata events |
| Linux System (Ubuntu/CentOS) | Hosting both Wazuh and Suricata (or separate hosts) |
| Kali Linux | For attacking and simulating the attacks |

---

## Pre-requisites
- Completed installation of Suricata IDS

---

## Task #1: Verify Suricata Logging Configuration

1. Locate the Suricata configuration file:

```bash
sudo nano /etc/suricata/suricata.yaml
```

2. Ensure EVE JSON output is enabled. Find and edit the section:

```outputs:
  - eve-log:
      enabled: yes
      filetype: regular
      filename: /var/log/suricata/eve.json
      types:
        - alert
        - dns
        - tls
        - flow
```      
     
3. Restart Suricata to apply changes:
```
sudo systemctl restart suricata
```

4. Verify log generation:
```
tail -f /var/log/suricata/eve.json
```

You should see JSON-formatted logs with keys like "event_type":"dns" or "event_type":"tls".

---

## Task #2: Configure Wazuh Agent to Collect Suricata Logs

1. Locate the Wazuh Agent configuration file:
```
sudo nano /var/ossec/etc/ossec.conf
```

2. Add a localfile entry for eve.json:
```
<localfile>
  <location>/var/log/suricata/eve.json</location>
  <log_format>json</log_format>
</localfile>
```

3. Restart the Wazuh Agent:
```
sudo systemctl restart wazuh-agent
```

4. Confirm Wazuh is reading the file:
```
tail -f /var/ossec/logs/ossec.log | grep suricata
```

---

## Task #3: Create Custom Wazuh Rules for Suricata Alerts

1. Open the Wazuh local rules file:
```
sudo nano /var/ossec/etc/rules/local_rules.xml
```

2. Add a rule for Suricata DNS alerts:
```
<rule id="100100" level="5">
  <if_group>suricata,dns,</if_group>
  <description>Suricata DNS Event Detected</description>
  <group>suricata,dns,network,</group>
</rule>
```

3. Add a rule for TLS anomalies:
```
<rule id="100101" level="7">
  <if_group>suricata,tls,</if_group>
  <description>Suricata TLS Anomaly Detected</description>
  <group>suricata,tls,network,</group>
</rule>
```

4. Restart Wazuh Manager:
```
sudo systemctl restart wazuh-manager
```

---

## Conclusion 
You have successfully integrated Suricata IDS with Wazuh SIEM to monitor real-time network threat data from eve.json, including DNS and TLS logs. This integration enhances visibility into network-level activity and provides a strong foundation for intrusion detection, forensics, and threat hunting.

---

## Screenshots

## Suricata & Wazuh Lab Screenshots
## Suricata & Wazuh Lab Screenshots

### 1. Suricata actively generating logs
![Suricata actively generating logs](Suricata%20actively%20generating%20logs.png)

### 2. Suricata logs reaching Wazuh Manager
![Suricata logs reaching Wazuh Manager](Suricata%20logs%20reaching%20Wazuh%20Manager.png)

### 3. Wazuh agent collecting eve.json
![Wazuh agent collecting eve.json](Wazuh%20agent%20collecting%20eve.json.png)

### 4. Wazuh Threat Hunting tab
![Wazuh Threat Hunting tab](Wazuh%20Threat%20Hunting%20tab.png)

