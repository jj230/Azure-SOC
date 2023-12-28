# Building a SOC + Honeynet in Azure (Live Traffic)
![Screenshot 2023-12-27 at 4 41 23 PM](https://github.com/jj230/Azure-SOC/assets/93885534/6e195d27-ee5a-40a8-95b0-5476f3e06b69)


## Introduction

In this project, I created a small honeynet in Azure. I then setup a Log Aanalytics workspace to ingest the resources' logs and connected that to Microsoft Sentinel. Within Sentinel, I built attack maps and trigger alerts. I initially set up the network so that all traffic was allowed through. After a 72 hour period (Friday afternoon - Monday afternoon), I analyzed the incidents and events in Sentinel, responded to them, and gathered the attack metrics. Next, I setup Azure to monitor the safety score of the network in comparison to NIST 800-53, then followed some of its recommendations for how to harden the environment. After hardening, I waited until the following weekend to take the after-hardening attack metrics. 

The metrics I analyzed were:
- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
![NSG-Malicious-Allowed-In (alltime)](https://github.com/jj230/Azure-SOC/assets/93885534/5b8bb2fb-ffc5-4aa0-a8c0-5aa3d60aa6ad)
 <br>
![Linux_SSH_Auth_Fail (all time?)](https://github.com/jj230/Azure-SOC/assets/93885534/248fdc7a-5286-4c7c-aead-434d843dd970)<br>
![Windows-RDP-Auth-Fail (alltime?)](https://github.com/jj230/Azure-SOC/assets/93885534/9852eeac-bc84-490f-8e17-331bfccee7d8)<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:

Day 1: Friday -> Saturday                 

| Metric                   | Time (EST)
| ------------------------ | ---------------------
| Start Time               | 12/8/2023, 4:23:11 PM
| Stop Time                | 12/9/2023, 4:23:11 PM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 76895
| Syslog                   | 18629
| SecurityAlert            | 1
| SecurityIncident         | 238
| AzureNetworkAnalytics_CL | 3270

Day 2: Saturday -> Sunday
| Metric                   | Time (EST)
| ------------------------ | ---------------------
| Start Time               | 12/9/2023, 4:23:11 PM
| Stop Time                | 12/10/2023, 4:23:11 PM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 137187
| Syslog                   | 18163
| SecurityAlert            | 13
| SecurityIncident         | 241
| AzureNetworkAnalytics_CL | 5050

Day 3: Sunday -> Monday

| Metric                   | Time (EST)
| ------------------------ | ---------------------
| Start Time               | 12/10/2023, 4:23:11 PM
| Stop Time                | 12/11/2023, 4:23:11 PM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 24331
| Syslog                   | 36363
| SecurityAlert            | 8
| SecurityIncident         | 237
| AzureNetworkAnalytics_CL | 5307

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:

Day 1: Friday -> Saturday                 

| Metric                   | Time (EST)
| ------------------------ | ---------------------
| Start Time               | 12/15/2023, 4:49:51 PM
| Stop Time                | 12/16/2023, 4:49:51 PM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 11295
| Syslog                   | 0
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0
| Malicious Flows Denied   | 3259

Day 2: Saturday -> Sunday
| Metric                   | Time (EST)
| ------------------------ | ---------------------
| Start Time               | 12/16/2023, 4:49:51 PM
| Stop Time                | 12/17/2023, 4:49:51 PM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 8337
| Syslog                   | 0
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0
| Malicious Flows Denied   | 2708

Day 3: Sunday -> Monday

| Metric                   | Time (EST)
| ------------------------ | ---------------------
| Start Time               | 12/17/2023, 4:49:51 PM
| Stop Time                | 12/18/2023, 4:49:51 PM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 8172
| Syslog                   | 0
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0
| Malicious Flows Denied   | 2399

## Results

| Metric                     | Change after hardening
| ------------------------   | ----------------------
| Security Events 	          | -66.41%
| Syslog                     |	-100.00%
| SecurityAlert              | -100.00%
| Security Incident          | -100.00%
| AzureNetworkAnalytics_CL   |	-100.00%


## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
