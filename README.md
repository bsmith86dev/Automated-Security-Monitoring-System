# Automated-Security-Monitoring-System
Automated Security Monitoring System

1. Setting Up the Environment on GCP
1.1. Creating Virtual Machines
Log in to Google Cloud Console: Google Cloud Console.

Create a new project:

Navigate to the project selector page.
Click on "New Project" and provide a name for your project.
Create VM Instances:

Navigate to "Compute Engine" > "VM instances".
Click "Create Instance" to create two VMs: one for Splunk and one for Cortex XSOAR.
Choose machine types according to the requirements (e.g., n1-standard-2).
Select "Ubuntu 20.04 LTS" as the operating system.
Allow HTTP and HTTPS traffic in the firewall settings.
Click "Create".
1.2. Set Up Networking and Firewall Rules
Configure Firewall Rules:
Navigate to "VPC Network" > "Firewall".
Click "Create Firewall Rule".
Create rules to allow traffic on necessary ports (e.g., 8000 for Splunk, 443 for Cortex XSOAR).
Apply the rules to the VMs.

2. Installing Splunk
2.1. Connect to the Splunk VM
  1. SSH into the Splunk VM:
      -Use the SSH button in the Google Cloud Console to connect to your VM.
  2. Download and Install Splunk:
       shell command: wget -O splunk-8.2.4-a7f645ddaf91-linux-2.6-amd64.deb 'https://www.splunk.com/page/download_track?file=8.2.4/linux/splunk-8.2.4-a7f645ddaf91-linux-2.6-amd64.deb'
                      sudo dpkg -i splunk-8.2.4-a7f645ddaf91-linux-2.6-amd64.deb
  3. Start Splunk:
       shell command: sudo /opt/splunk/bin/splunk start --accept-license
                      sudo /opt/splunk/bin/splunk enable boot-start

  4. Access Splunk Web Interface:

        -Open a web browser and navigate to http://[YOUR_VM_EXTERNAL_IP]:8000.
        -Log in with the default credentials and change the password as prompted.
2.2. Configure Splunk Data Inputs
Add Data Inputs:

Go to "Settings" > "Data Inputs".
Add inputs for syslog, files, etc., based on your data sources.
Example configuration for syslog:


[monitor:///var/log/auth.log]
disabled = false
index = security
sourcetype = authlog
Create Indexes:

Navigate to "Settings" > "Indexes".
Create new indexes for organizing your data (e.g., security).
3. Installing Cortex XSOAR
3.1. Connect to the Cortex XSOAR VM
SSH into the Cortex XSOAR VM:

Use the SSH button in the Google Cloud Console to connect to your VM.
Download and Install Cortex XSOAR:

Follow the installation guide on the Cortex XSOAR documentation page.
Basic installation steps:
sh

wget https://example.com/cortex-xsoar-installer.sh
chmod +x cortex-xsoar-installer.sh
sudo ./cortex-xsoar-installer.sh
Access Cortex XSOAR Web Interface:

Open a web browser and navigate to https://[YOUR_VM_EXTERNAL_IP].
Log in with the default credentials and change the password as prompted.
3.2. Configure Cortex XSOAR Integration with Splunk
Install Splunk Integration:
Navigate to "Settings" > "Integrations" > "Servers & Services".
Search for and add the Splunk integration.
Configure the integration with Splunk server details and API tokens.
4. Creating Dashboards and Alerts in Splunk
4.1. Creating Dashboards
Navigate to Dashboards:

Go to "Search & Reporting" > "Dashboards".
Click "Create New Dashboard".
Add Panels to the Dashboard:

Use saved searches or create new searches to populate the panels.
Example search for failed logins:


index=security sourcetype=authlog "failed password" | stats count by host
Save and Share the Dashboard:

Save the dashboard and set permissions as needed.

4.2. Creating Alerts
Navigate to Alerts:

Go to "Search & Reporting" > "Alerts".
Click "Create New Alert".
Define Alert Conditions:

Use a search query to define the alert condition.
Example search for high number of failed logins:


index=security sourcetype=authlog "failed password" | stats count by host | where count > 10
Set Alert Actions:

Configure actions such as email notifications or triggering a playbook in Cortex XSOAR.

5. Creating Playbooks in Cortex XSOAR
5.1. Playbook: Incident Response
Navigate to Playbooks:

Go to "Playbooks" in Cortex XSOAR.
Create a New Playbook:

Click "Create New Playbook".
Add Steps to the Playbook:

Define each step for parsing logs, identifying threats, and responding to incidents.
Example playbook YAML:
yaml

- name: Incident Response Playbook
  steps:
    - name: Parse logs
      action: parse
      params:
        input: ${incident.logs}
    - name: Identify threat
      action: threat_detection
      params:
        indicators: ${parsed_logs}
    - name: Contain threat
      action: isolate_device
      params:
        device: ${threat.device}
    - name: Notify team
      action: send_email
      params:
        to: security_team@example.com
        subject: Incident Response Alert
        body: >
          Threat detected and contained:
          ${threat.details}
Save and Activate the Playbook:

Save the playbook and activate it for automatic execution.

6. Testing and Optimization
Simulate Attacks:

Perform simulated attacks to test the effectiveness of the monitoring system and response playbooks.
Optimize Configurations:

Refine dashboards, alerts, and playbooks based on test results and performance metrics.
