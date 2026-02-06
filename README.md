# SOC Automation Lab

## Objective

The SOC Automation Lab project was designed to create a controlled environment for simulating automated detection and response workflows for cyber threats. The lab focused on ingesting and analyzing security logs within a Security Information and Event Management (SIEM) system, forwarding relevant events to a case management platform, and enriching detected threats through cross-referencing with Open-Source Intelligence (OSINT). Upon detection, automated email notifications were sent to a simulated SOC analyst inbox. This project provided hands-on experience with real-world SOC automation, illustrating how individual tools, workflows, and analyst roles integrate to support efficient incident response.

### Skills Learned
- Advanced understanding of SIEM and XDR agent deployment, configuration, and real-world operational use
- Proficiency in configuring and integrating case management systems with SIEM log ingestion and alerting workflows
- Ability to design and implement SOAR workflows to parse security events, enrich data, and automate incident response and case creation
- Enhanced knowledge of cloud-based server setup, tooling, and supporting automation scripts
- Strengthened problem-solving and critical thinking skills through troubleshooting service errors and integration issues

### Tools Used
- Wazuh: SIEM and XDR
- TheHive: Case Management
- Shuffle: SOAR
- Vultr: Cloud Hosting
- Virustotal: OSINT

## Steps

### 1. Created and planned the overall structure of the project

<img width="1140" height="960" alt="SOC-Automation-Project" src="https://github.com/user-attachments/assets/31372ad1-bced-40de-9897-3c636b8a0167" />

Drawing a diagram and planning the workflow greatly increases productivity and prevents confusion on the overall goal of each component.


### 2. Cloud computing elements and virtual machine was set up

<img width="955" height="369" alt="Cloud-Computing-Elements" src="https://github.com/user-attachments/assets/37a765b0-54fe-4a3b-bb7a-f1debbd95aea" />

Two cloud machines were spun up with both running Ubuntu 24.04 LTS x64 on the Vultr platform
One machine was dedicated for TheHive and the other for Wazuh, each of the services were configured made to be up and running according to docs.

<img width="1916" height="1077" alt="Windows-11-Sysmon-" src="https://github.com/user-attachments/assets/2313ec47-7a80-451b-a40b-faf662aeb168" />

Virtual box was used on my personal host machine to virtualize a Windows 11 instance. This instance will act as the Windows 11 client to be infected with a threat.
Sysmon was also installed on the Windows 11 VM for detailed persistent logging of activity

### 3. Configured TheHive and Wazuh
<img width="1280" height="705" alt="TheHive-Running" src="https://github.com/user-attachments/assets/2ec5732e-8190-488b-bbf9-2880a1d946eb" />

On TheHive Server:
- Cassandra was set up and configured to the public IP of TheHive. Cassandra.service active and running
- Elasticsearch was set up and configured with one node and the network host of TheHive Server's IP and Port 9200
- TheHive itself was then configured to the public IP of TheHive's Server and matched the cluster name for elasticsearch and hostname as well.
- TheHive was successfully initialized!


<img width="1280" height="705" alt="Wazuh-Running" src="https://github.com/user-attachments/assets/58affc3d-a14a-43d7-a58c-a0964873bc74" />
<img width="1920" height="1080" alt="Wazuh-Agent" src="https://github.com/user-attachments/assets/a9ff303b-3b4c-43fa-a33f-8f46313225ce" />

For Wazuh:
- Wazuh Manager on the Cloud Server was enabled and set to log all in the ossec.conf file. Wazuh now logs everything under an archive. Filebeat's module for Wazuh, also had to be enabled for archives.
- Wazuh Agent was installed on the Windows 11 instance. The ossec.conf file for the Wazuh agent was then linked to Sysmon.


<img width="1198" height="799" alt="Wazuh-Archives-Index" src="https://github.com/user-attachments/assets/d7b80656-10cd-4514-88bc-adab0ca78ed4" />
<img width="1885" height="837" alt="Wazuh-rule" src="https://github.com/user-attachments/assets/78a4e669-fb71-4db7-ac47-d4265ef2c01b" />


A new index must be created on the Wazuh server in order to view the archive logs. This requires a new index pattern to be created in the Wazuh dashboard specifically for the archive logs.
A rule was also created to detect our threat on the Windows 11 machine. In this example we are using the popular Mimikatz threat.
This allows us to find the Mimikatz detection log under the alerts and confirm that detection and logging of the threat works properly.

### 4. Shuffle Automation Set up
<img width="1582" height="954" alt="Shuffle" src="https://github.com/user-attachments/assets/1313b66b-df25-4bc6-89ab-e20fa3da85a4" />
<img width="1916" height="915" alt="Hive-Alert" src="https://github.com/user-attachments/assets/7aebf421-d050-4f00-8c68-86e62d99b2de" />
- The Shuffle webhook's API was integrated into the Wazuh manager's ossec.conf file in order to send the alerts to Shuffle for the specific rule we specified earlier.
- A SHA-256 hash is then extracted from the log and ran into Virustotal, our OSINT provider for this project.
- After enriching the IOC with Virustotal, the data is parsed to an email sent to a SOC analyst and also a case is created in TheHive for an example user in the organization.



