# Lab Exercise: Windows Server 2022 Storage Spaces Direct Configuration with Tiers

## Objective:

The objective of this lab exercise is to guide participants through the process of configuring Storage Spaces Direct (S2D) with storage tiers on Windows Server 2022. In this exercise, you will create a cluster without storage, set the cluster quorum, enable Storage Spaces Direct, reset existing disks, create a storage pool, set the media type, create storage tiers, and create a virtual disk with tiers.

## Prerequisites:

1. Two or more Windows Server 2022 machines: CL1-LAB and CL2-LAB.
2. A domain controller (DC-LAB) for Active Directory and DNS services.
3. Network connectivity between the servers.
4. Sufficient storage resources on each node.

### Task 1: Create Cluster Without Storage

Open PowerShell on your management machine.
Create a cluster without storage:

> New-Cluster -Name S2D-LAB -Node CL1-LAB, CL2-LAB -StaticAddress 192.168.1.150 -NoStorage

### Task 2: Set Cluster Quorum

Set the cluster quorum using a file share witness on a domain controller:

> Set-ClusterQuorum -Cluster S2D-LAB -FileShareWitness "\\dc-LAB\witness"

### Task 3: Enable Storage Spaces Direct and Reset Existing Disks

Enable Storage Spaces Direct and reset existing disks on the cluster nodes:

> Enable-ClusterStorageSpacesDirect -CimSession S2D-LAB -Autoconfig $false

### Task 4: Reset Existing Disks

> Invoke-Command (Get-Cluster -Name S2D-LAB | Get-ClusterNode) {
    Update-StorageProviderCache
    Get-StoragePool | Where-Object IsPrimordial -eq $false | Set-StoragePool -IsReadOnly:$false -ErrorAction SilentlyContinue
    Get-StoragePool | Where-Object IsPrimordial -eq $false | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$false -ErrorAction SilentlyContinue
    Get-StoragePool | Where-Object IsPrimordial -eq $false | Remove-StoragePool -Confirm:$false -ErrorAction SilentlyContinue
    Get-PhysicalDisk | Reset-PhysicalDisk -ErrorAction SilentlyContinue
    Get-Disk | Where-Object Number -ne $null | ? IsBoot -ne $true | ? IsSystem -ne $true | ? PartitionStyle -eq RAW | Group -NoElement -Property FriendlyName
> } | Sort -Property PsComputerName,Count



### Task 5: Create Storage Pool 

Create a storage pool 

> $disks = Get-PhysicalDisk -CimSession S2D-LAB | Where-Object CanPool -eq True
> New-StoragePool -CimSession S2D-LAB -StorageSubSystemFriendlyName "*cluster*" -FriendlyName "S2D Pool" -PhysicalDisks $disks

Set Media Type

> Invoke-Command CL1-LAB,CL2-LAB { 
    Get-PhysicalDisk | Where-Object Size -eq 50GB | Set-PhysicalDisk -MediaType SSD
    Get-PhysicalDisk | Where-Object Size -eq 100GB | Set-PhysicalDisk -MediaType HDD
> }


### Task 6: Create Storage Tiers

Create storage tiers on the cluster nodes:

> New-StorageTier -CimSession S2D-LAB -StoragePoolFriendlyName S2D* -FriendlyName Capacity -MediaType HDD
> New-StorageTier -CimSession S2D-LAB -StoragePoolFriendlyName S2D* -FriendlyName Performance -MediaType SSD

### Task 7: Create Virtual Disk with Tiers and Volume

Create a virtual disk with storage tiers and a volume on the cluster nodes:

> New-Volume -CimSession S2D-LAB -FriendlyName S2DvDisk -StoragePoolFriendlyName S2D* -FileSystem CSVFS_ReFS -StorageTierFriendlyNames Capacity,Performance -StorageTierSizes 100GB,25GB

### Task 8: Verify Configuration

Verify the Storage Spaces Direct configuration:

> Get-ClusterStorageSpacesDirect -CimSession S2D-LAB
> Get-PhysicalDisk -CimSession S2D-LAB
> Get-StoragePool -CimSession S2D-LAB
> Get-StorageTier -CimSession S2D-LAB
> Get-VirtualDisk -CimSession S2D-LAB
> Get-Volume -CimSession S2D-LAB

## Conclusion:
This lab exercise provides hands-on experience in configuring Storage Spaces Direct with storage tiers on Windows Server 2022. Participants should be familiar with creating clusters, setting quorum, enabling Storage Spaces Direct, resetting disks, creating storage pools, setting media types, creating storage tiers, and creating virtual disks with tiers. The exercise aims to demonstrate the flexibility and performance optimization capabilities of Storage Spaces Direct with storage tiers.

