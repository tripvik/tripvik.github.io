---
layout: post
title: Collecting Crash Dumps with Windows Error Reporting
date: 2024-10-18 11:32 +0000
---

When troubleshooting crashes in .NET applications hosted on Internet Information Services (IIS), obtaining a memory dump is crucial for analysis. While tools like ProcDump and Debug Diagnostic are commonly used, sometimes they may not be suitable or available. In such cases, Windows Error Reporting (WER) can be a valuable alternative. This guide will walk you through the process of using WER to generate memory dumps when a .NET application crashes on IIS.

> **Note:** Although this guide is primarily focused on .NET and IIS applications, the process described can be adapted for other Windows processes as well.

## Step-by-Step Guide

### 1. Prepare the Environment

Create a folder to store the crash dump files:

```
mkdir c:\dumps
```

Ensure that the IIS application pool has write access to this folder.

### 2. Enable Dump Collection

Use the following PowerShell script to enable dump collection for your .NET application:

```powershell
param(
    [Parameter(Position=0,mandatory=$true)]
    $application,
    [Parameter(Position=1,mandatory=$true)]
    $location
)

$werHive = "HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting";
$ldHive = "$werHive\LocalDumps";
$applicationHive = "$ldHive\$application";

if (!(Test-Path $ldHive))
{
    New-Item -Path $werHive -Name "LocalDumps";
}

if (!(Test-Path $applicationHive))
{
    New-Item -Path $ldHive -Name $application;
}

New-ItemProperty $applicationHive -Name "DumpFolder" -Value $location -PropertyType "ExpandString" -Force;
# Allow maximum 5 dumps
New-ItemProperty $applicationHive -Name "DumpCount" -Value 5 -PropertyType "DWORD" -Force;
# 2 - Full Dump
New-ItemProperty $applicationHive -Name "DumpType" -Value 2 -PropertyType "DWORD" -Force;
```

Save this script as `EnableDumps.ps1`.

#### For .NET Applications Using In-Process Hosting Model:

If your .NET application uses the in-process hosting model in IIS, run:

```
.\EnableDumps w3wp.exe c:\dumps
```

#### For .NET Core Applications Using Out-of-Process Hosting Model:

If your .NET Core application uses the out-of-process hosting model in IIS, run:

```
.\EnableDumps dotnet.exe c:\dumps
```

### 3. Reproduce the Crash

Run your .NET application under the conditions that typically cause the crash to occur.

### 4. Disable Dump Collection

After collecting the necessary dumps, use the following PowerShell script to disable dump collection:

```powershell
param(
    [Parameter(Position=0,mandatory=$true)]
    $application
)

$werHive = "HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting";
$ldHive = "$werHive\LocalDumps";
$applicationHive = "$ldHive\$application";

if (Test-Path $applicationHive)
{
    Remove-Item $applicationHive -Force;
}
```

Save this script as `DisableDumps.ps1`.

#### For .NET Applications Using In-Process Hosting Model:

```
.\DisableDumps w3wp.exe
```

#### For .NET Core Applications Using Out-of-Process Hosting Model:

```
.\DisableDumps dotnet.exe
```

## Important Notes

- The PowerShell scripts configure WER to collect up to five dumps per application.
- After a crash and successful dump collection, the application will be allowed to terminate normally.
- Ensure you disable dump collection after gathering the necessary information to prevent unnecessary resource usage.
- While this guide focuses on .NET and IIS applications, you can adapt the process for other Windows processes by replacing `w3wp.exe` or `dotnet.exe` with the appropriate process name.

By following these steps, you can effectively use Windows Error Reporting to collect memory dumps when traditional methods are unavailable or unsuitable. These dumps can be helpful in diagnosing and resolving application crashes in your .NET and IIS environment, as well as for other Windows processes.
