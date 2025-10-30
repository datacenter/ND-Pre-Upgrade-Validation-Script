# ND-Pre-Upgrade-Validation-Script

The script checks for issues that are known to have an impact to the success of a Nexus Dashboard upgrade. Identification and remediation of such issues prior to upgrade can lead to more successful outcomes.

The script can be run from any Linux server with the required dependencies. If ACI APICs are available and have connectivity towards the ND management address, the script can be run from an APIC.

## Table of Contents

- [Checks](#checks)
- [Dependencies and Installation](#dependencies-and-installation)
  - [Python Requirements](#python-requirements)
  - [Installing Dependencies](#installing-dependencies)
  - [Dependencies Overview](#dependencies-overview)
- [Usage](#usage)
  - [Compatibility Notes](#compatibility-notes)
  - [Example Run](#example-run)
- [Support](#support)

## Checks

**Note:** Some of the checks are version specific and might not apply to the current running version of Nexus Dashboard in your environment. In the event that the check is not relevant, the check will be a "PASS".

| # | Check | Description | Reference |
|---|-------|-------------|-----------|
| 1 | Techsupport | Verify the script could successfully extract the tech support to perform additional checks | |
| 2 | Version | Verify all cluster nodes are running the same version | |
| 3 | Node status | Verify all cluster nodes are in an Active state | |
| 4 | Ping check | Verify node can ping all Mgmt and Data IPs in the cluster | |
| 5 | Subnet check | Verify Mgmt and Data interfaces are on different subnets | [Link](https://www.cisco.com/c/en/us/td/docs/dcn/nd/3x/deployment/cisco-nexus-dashboard-and-services-deployment-guide-311/nd-prerequisites-platform-31x.html#concept_fdf_fxg_4mb) |
| 6 | Persistent IP check | Verify at lest 5 Persistent IP address are configured in data-external-services | [Link](https://www.cisco.com/c/en/us/td/docs/dcn/nd/4x/deployment/cisco-nexus-dashboard-deployment-guide-41x/nd-prerequisites-41x.html#concept_zkj_3hj_cgc) |
| 7 | Disk space | Verify all directories are under 70% utilization | |
| 8 | Pod status | Verify all Pods and Services are in a healthy state | |
| 9 | System health | Verify all nodes are healthy using 'acs health' command | |
| 10 | NXOS Discovery Service | Verify cisco-ndfc k8 App is not stuck in Disabling | [Link](https://bst.cisco.com/bugsearch/bug/CSCwm97680) |
| 11 | Certificate check | Verify no certificates have non-alphanumeric characters | [Link](https://bst.cisco.com/bugsearch/bug/CSCwm35992) |
| 12 | ISO check | Verify multiple ISOs aren't found in boothook | [Link](https://bst.cisco.com/bugsearch/bug/CSCwn94394) |
| 13 | Lvm Pvs check | Verify no empty ElasticSearch PVs are found | [Link](https://bst.cisco.com/bugsearch/bug/CSCwe91228) |
| 14 | atom0 NVME check | Verify no NVME drive hardware failures are present in Physical node setups | |
| 15 | atom0 vg check | Verify there is more than 50% free space in atom0 virtual group | [Link](https://bst.cisco.com/bugsearch/bug/CSCwr43515) |

## Dependencies and Installation

### Python Requirements
The main script requires Python 3.7+ while the worker script is compatible with Python 2.7+ to accommodate older ND node versions.

### Installing Dependencies
Most dependencies are part of Python's standard library. For the few external dependencies:

1. **Install Python dependencies using pip:**
   ```bash
   pip install -r requirements.txt
   ```
   
   Or for Python 3 specifically:
   ```bash
   pip3 install -r requirements.txt
   ```

2. **Install system dependencies:**
   - **Linux/Unix:** Install `sshpass` for SSH authentication:
     ```bash
     sudo apt-get update
     sudo apt-get install sshpass
     ```
   - **macOS:** Use Homebrew:
     ```bash
     brew install hudochenkov/sshpass/sshpass
     ```
   - **Windows:** Consider using WSL (Windows Subsystem for Linux) for best compatibility

### Dependencies Overview
- **ipaddr/ipaddress**: For IP address validation and manipulation
  - `ipaddr` package for Python 2.7 (worker script on older ND nodes)
  - Built-in `ipaddress` module for Python 3+ (main script and newer ND nodes)
- **sshpass**: External system tool for password-based SSH authentication
- All other dependencies are part of Python's standard library

## Usage

Place the main script `ND-Preupgrade-Validation.py` and the worker script `worker_functions.py` in the same directory and execute the main script with the command `python ND-Preupgrade-Validation.py` or `python3 ND-Preupgrade-Validation.py`

### Compatibility Notes
- The main script is currently only compatible with Python 3.7+ but will be modified in the future.
- The worker script is Python 2.7+ compatible to accommodate ND nodes on versions prior to 3.0.

### Example Run:
```
[root@localhost pre]# python3 ND-Preupgrade-Validation.py
Nexus Dashboard Pre-upgrade Validation Script (Distributed Version)
Running validation checks on 2025-10-20 20:19:12
Enter Nexus Dashboard IP address: 14.2.29.130
Enter password for rescue-user:

Connecting to Nexus Dashboard at 14.2.29.130...
Successfully connected to Nexus Dashboard at 14.2.29.130
Nexus Dashboard version: 3.2.2f
Discovering Nexus Dashboard nodes...
Found 3 Nexus Dashboard nodes
---------------------------------------
        ND1 (14.2.29.130)
        ND2 (14.2.29.131)
        ND3 (14.2.29.132)
---------------------------------------

PASS All 3 nodes are active and ready for validation.

System resource assessment:
  CPU cores: 40
  Memory: 62.36 GB
  Current load: 0.0
  Recommended concurrent nodes: 7

Do you want to generate new tech supports for analysis?
1. Generate new tech supports for analysis
2. Use existing tech supports on all nodes

Enter your choice (1/2): 1

================================================================================
  Tech Support Selection/Generation
================================================================================
Generating tech supports for all nodes in parallel. This may take several minutes...
Generating tech support on ND1...
Starting tech support collection on ND1. This may take several minutes...
Tech support collection started successfully on ND1
Waiting for tech support generation to complete on ND1. This may take several minutes...
Generating tech support on ND3...
Starting tech support collection on ND3. This may take several minutes...
Generating tech support on ND2...
Starting tech support collection on ND2. This may take several minutes...
Tech support collection started successfully on ND2
Waiting for tech support generation to complete on ND2. This may take several minutes...
Tech support collection started successfully on ND3
Waiting for tech support generation to complete on ND3. This may take several minutes...
Verifying tech support file stability for ND1...
Verifying tech support file stability for ND2...
Verifying tech support file stability for ND3...
Tech support not ready yet on ND1 (attempt 1/30), waiting 30 seconds...
Tech support not ready yet on ND2 (attempt 1/30), waiting 30 seconds...
Tech support not ready yet on ND3 (attempt 1/30), waiting 30 seconds...
Verifying tech support file stability for ND1...
Verifying tech support file stability for ND2...
Verifying tech support file stability for ND3...
Tech support generated on ND1: /techsupport/2025-10-21T00-45-25Z-system-ts-ND1.tgz
Tech support generated on ND2: /techsupport/2025-10-21T00-45-26Z-system-ts-ND2.tgz
Tech support generated on ND3: /techsupport/2025-10-21T00-45-24Z-system-ts-ND3.tgz
Generated tech support on ND3: 2025-10-21T00-45-24Z-system-ts-ND3.tgz
Generated tech support on ND1: 2025-10-21T00-45-25Z-system-ts-ND1.tgz
Generated tech support on ND2: 2025-10-21T00-45-26Z-system-ts-ND2.tgz

================================================================================
  Pre-Flight /tmp Space Validation
================================================================================
Checking /tmp disk space on all nodes before extraction...
This validation ensures sufficient space for tech support extraction (70% threshold).

Checking space on ND1... PASS
  Tech support: 1.45 GB | Current: 18.0% | Projected: 45.2%
Checking space on ND2... PASS
  Tech support: 1.66 GB | Current: 0.0% | Projected: 31.1%
Checking space on ND3... PASS
  Tech support: 0.76 GB | Current: 7.0% | Projected: 21.2%

PASS All nodes passed /tmp space validation!
Proceeding with worker script deployment...


================================================================================
  Deploying Worker Scripts and Starting Validation
================================================================================
Deploying worker script to node ND1...
Deploying worker script to node ND2...
Deploying worker script to node ND3...
PASS Worker script deployed to ND1
Starting validation on node ND1...
PASS Worker script deployed to ND2
Starting validation on node ND2...
PASS Worker script deployed to ND3
Starting validation on node ND3...
PASS Validation started on ND3
PASS Validation started on ND1
PASS Validation started on ND2

Monitoring validation progress on 3 nodes in batch 1...

Running validation checks on 3 Nexus Dashboard nodes...
[Node ND3] Validation complete
[Node ND1] Validation complete
[Node ND2] Validation complete
Collecting results and logs from all nodes...

Monitoring completed in 0 minutes 3 seconds


================================================================================
  Cleaning Up Temporary Files
================================================================================
Cleaning up temporary files from 3 nodes...
PASS Cleaned up temporary files on ND1
PASS Cleaned up temporary files on ND2
PASS Cleaned up temporary files on ND3

================================================================================
  Pre-upgrade Validation Report
================================================================================
Total validation time: 4 min 26 sec


Report generated on: 2025-10-20 20:23:57

[Check  1/14] Techsupport...                                                      PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      Tech support generated and extracted successfully
  ND2        PASS      Tech support generated and extracted successfully
  ND3        PASS      Tech support generated and extracted successfully

[Check  2/14] Version Check...                                                    PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      All cluster nodes are on the same version: 3.2.2f
  ND2        PASS      All cluster nodes are on the same version: 3.2.2f
  ND3        PASS      All cluster nodes are on the same version: 3.2.2f

[Check  3/14] Node Status...                                                      PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      All nodes in the cluster are in Active state
  ND2        PASS      All nodes in the cluster are in Active state
  ND3        PASS      All nodes in the cluster are in Active state

[Check  4/14] Ping Check...                                                       PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      Node can ping all mgmt and data ips in the cluster
  ND2        PASS      Node can ping all mgmt and data ips in the cluster
  ND3        PASS      Node can ping all mgmt and data ips in the cluster

[Check  5/14] Subnet Check...                                                     PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      Mgmt and Data interfaces are in different subnets
  ND2        PASS      Mgmt and Data interfaces are in different subnets
  ND3        PASS      Mgmt and Data interfaces are in different subnets

[Check  6/14] Persistent Ip Check...                                              PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      Found 10 persistent IP addresses (meets minimum requirement of 5)
  ND2        PASS      Found 10 persistent IP addresses (meets minimum requirement of 5)
  ND3        PASS      Found 10 persistent IP addresses (meets minimum requirement of 5)

[Check  7/14] Disk Space...                                                       PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      All directories are under 70% usage
  ND2        PASS      All directories are under 70% usage
  ND3        PASS      All directories are under 70% usage

[Check  8/14] Pod Status...                                                       PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      All Pods and Services are in a healthy state
  ND2        PASS      All Pods and Services are in a healthy state
  ND3        PASS      All Pods and Services are in a healthy state

[Check  9/14] System Health...                                                    PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      acs health indicates Node is healthy
  ND2        PASS      acs health indicates Node is healthy
  ND3        PASS      acs health indicates Node is healthy

[Check 10/14] Certificate Check...                                                PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      No certificate issues found in SM logs
  ND2        PASS      No certificate issues found in SM logs
  ND3        PASS      No certificate issues found in SM logs

[Check 11/14] Iso Check...                                                        PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      No multiple ISO issues found
  ND2        PASS      No multiple ISO issues found
  ND3        PASS      No multiple ISO issues found

[Check 12/14] Lvm Pvs Check...                                                    PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      No empty Elasticsearch PVs found
  ND2        PASS      No empty Elasticsearch PVs found
  ND3        PASS      No empty Elasticsearch PVs found

[Check 13/14] Atom0 Nvme Check...                                                 PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      NVME drive seen by ND in PVS file
  ND2        PASS      NVME drive seen by ND in PVS file
  ND3        PASS      NVME drive seen by ND in PVS file

[Check 14/14] Atom0 Vg Check...                                                   PASS
  Node       Status   Details
  ---------- -------- --------------------------------------------------
  ND1        PASS      atom0 vg has more than 50G free space
  ND2        PASS      atom0 vg has more than 50G free space
  ND3        PASS      atom0 vg has more than 50G free space


Detailed results are available in /root/Downloads/pre/final-results/

Results Bundle: /root/Downloads/pre/nd-preupgrade-validation-results_2025-10-20T20-23-57.tgz

[root@localhost pre]# ls -lh
total 332K
drwxr-xr-x. 2 root root  236 Oct 20 20:23 final-results
-rw-r--r--. 1 root root 162K Oct 20 16:29 ND-Preupgrade-Validation.py
-rw-r--r--. 1 root root 8.5K Oct 20 20:23 nd-preupgrade-validation-results_2025-10-20T20-23-57.tgz
-rw-r--r--. 1 root root 154K Oct 20 19:47 worker_functions.py

[root@localhost pre]# ls -lh final-results/
total 92K
-rw-r--r--. 1 root root 7.7K Oct 20 20:23 ND1_output.log
-rw-r--r--. 1 root root 2.0K Oct 20 20:23 ND1_results.json
-rw-r--r--. 1 root root 7.7K Oct 20 20:23 ND2_output.log
-rw-r--r--. 1 root root 2.0K Oct 20 20:23 ND2_results.json
-rw-r--r--. 1 root root 7.7K Oct 20 20:23 ND3_output.log
-rw-r--r--. 1 root root 2.0K Oct 20 20:23 ND3_results.json
-rw-r--r--. 1 root root  39K Oct 20 20:23 nd_validation_debug.log
-rw-r--r--. 1 root root 5.1K Oct 20 20:23 validation_details.json
-rw-r--r--. 1 root root 5.6K Oct 20 20:23 validation_summary.txt
```

## Support

- Feedback and questions are welcome.
- If you have feedback or are encountering an issue running the script, please send an email to nd-preupgrade-validation@cisco.com
- If the script is recommending to open a Cisco TAC SR for any specific issue, please open a Cisco TAC SR.
- Running this script is not a requirement but it is intended to provide additional reassurance when planning for a Nexus Dashboard upgrade.