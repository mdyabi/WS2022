# Lab Exercise: Windows Server 2022 Storage Spaces Direct Configuration
## Objective:
The objective of this lab exercise is to guide participants through the process of setting up a Storage Spaces Direct (S2D) cluster on Windows Server 2022. In this exercise, you will install the required roles on cluster nodes, validate cluster requirements, create a cluster, set the cluster quorum, enable Storage Spaces Direct, and create a virtual disk.

## Prerequisites:

1. Two or more Windows Server 2022 machines: CL1-LAB and CL2-LAB.
2. A domain controller (DC-LAB) for Active Directory and DNS services.
3. Network connectivity between the servers.
4. Sufficient storage resources on each node.

### Task 1: Install Required Roles on Nodes

Open PowerShell on your management machine.
Install required roles on cluster nodes (CL1-LAB and CL2-LAB):

> Invoke-Command CL1-LAB, CL2-LAB { Install-WindowsFeature FS-FileServer, Failover-Clustering, Hyper-V -IncludeAllSubFeature }

### Task 2: Validate Cluster Requirements

Validate cluster requirements on both nodes:

> Test-Cluster -Node CL1-LAB, CL2-LAB -Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"

### Task 3: Create Cluster Without Storage

Create a cluster without storage:

> New-Cluster -Name S2D-LAB -Node CL1-LAB, CL2-LAB -StaticAddress 192.168.1.150 -NoStorage

### Task 4: Set Cluster Quorum

Set the cluster quorum using a file share witness on a domain controller:

> Set-ClusterQuorum -Cluster S2D-LAB -FileShareWitness "\\dc-lab\witness"

### Task 5: Enable Storage Spaces Direct

Enable Storage Spaces Direct on the cluster:

> Enable-ClusterStorageSpacesDirect -CimSession S2D-LAB -CacheState Disabled

### Task 6: Create Virtual Disk, Partition, Format Volume, and Add to CSV

Create a virtual disk, partition, format the volume, and add it to Cluster Shared Volumes (CSV):

> New-Volume -CimSession S2D-LAB -FriendlyName S2DvDisk -StoragePoolFriendlyName S2D* -FileSystem CSVFS_ReFS -Size 100GB

### Task 7: Verify Configuration

Verify the Storage Spaces Direct configuration:

> Get-ClusterStorageSpacesDirect -CimSession S2D-LAB
> Get-ClusterSharedVolume -CimSession S2D-LAB



## Conclusion:

This lab exercise provides hands-on experience in configuring Storage Spaces Direct on Windows Server 2022. Participants should be familiar with installing roles, validating cluster requirements, creating clusters, setting quorum, enabling Storage Spaces Direct, and managing virtual disks. The exercise aims to demonstrate the steps required to set up a Storage Spaces Direct cluster for scalable and fault-tolerant storage solutions.







