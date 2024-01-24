# Lab Exercise: Managing Windows Server Core

## Objective:
The objective of this lab exercise is to familiarize participants with managing Windows Server Core using PowerShell. In this exercise, you will perform various tasks remotely on a Windows Server Core machine, including viewing and installing features, configuring remote management for IIS, renaming a computer, and executing remote commands.

## Prerequisites:
Two Windows Server 2022 machines: CORE-LAB and WEB-LAB
Ensure both machines are part of the same domain (for the Rename-Computer cmdlet).
Access to domain credentials with administrative privileges.

### Task 1: Remote Management and Feature Installation

Open PowerShell on your local machine.

Enter a PowerShell session on the remote machine (CORE-LAB) using the Enter-PSSession cmdlet:

> Enter-PSSession -ComputerName CORE-LAB

View all available features on the remote machine using the Get-WindowsFeature cmdlet:

> Get-WindowsFeature

Install the Web Server (IIS) role and Management Service:

> Install-WindowsFeature -Name Web-Server, Web-Mgmt-Service

View the installed features to confirm the successful installation:

> Get-WindowsFeature | Where-Object Installed -eq True

### Task 2: Configure Remote Management for IIS

Configure remote management for IIS using the Set-ItemProperty cmdlet:

> Set-ItemProperty -Path "HKLM:\Software\Microsoft\WebManagement\Server" -Name "EnableRemoteManagement" -Value 1

Configure the Remote Management Service (WMSVC) to start automatically:
> Set-Service WMSVC -StartupType Automatic

### Task 3: Rename the Computer

Rename the computer to "WEB-LAB" using the Rename-Computer cmdlet:
> Rename-Computer -NewName WEB-LAB -DomainCredential "MCTlab\administrator" -Force -Restart

### Task 4: Exit Remote PowerShell Session

Exit the remote PowerShell session on CORE-LAB:

> Exit-PSSession

### Task 5: Execute Remote Commands on Another Machine (WEB-LAB)

Send a command to the remote machine (WEB-LAB) to get information about specific services (W3SVC, WMSVC) using Invoke-Command:
powershell

> Invoke-Command -ComputerName WEB-LAB -ScriptBlock { Get-Service W3SVC, WMSVC }

### Task 6: Use Built-in ComputerName Parameter

Use the built-in ComputerName parameter to retrieve service information from the renamed computer (WEB-LAB) using the Get-Service cmdlet:

> Get-Service -ComputerName WEB-LAB

## Conclusion:
This lab exercise provides hands-on experience in managing Windows Server Core remotely using PowerShell. Participants should be familiar with entering remote sessions, installing features, configuring remote management, renaming computers, and executing commands on remote machines.