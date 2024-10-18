---
layout: post
title: How to Collect PerfView Traces for IIS Related Troubleshooting
date: 2024-10-01 12:12 +0000
---

Troubleshooting IIS (Internet Information Services) issues can be challenging, but with the right tools and approach, you can quickly identify and resolve problems. PerfView is a powerful performance analysis tool that can provide invaluable insights into IIS-related issues. This guide will walk you through the process of collecting PerfView traces effectively.

## Why Use PerfView for IIS and .NET Issues?

PerfView is particularly useful for IIS and .NET troubleshooting because:

*   It can capture detailed ETW (Event Tracing for Windows) data
*   It provides insights into CPU usage, memory allocation, exception details and thread activity
*   It can help identify performance bottlenecks, errros, slowness and resource-intensive operations

## Prerequisites

Before we begin, ensure you have:

*   Administrative access to the server
*   Sufficient disk space for trace files (at least 2GB recommended)

## Step-by-Step Guide

### 1\. Download and Install PerfView

1.  Visit the [PerfView GitHub releases page](https://github.com/microsoft/perfview/releases).
2.  Download the latest version of PerfView.exe.
3.  Copy the executable to your IIS server.

### 2\. Enable IIS Tracing

PerfView requires the IIS Tracing role service to be enabled for comprehensive data collection. Skip this step is the tracing is already enabled.

1.  Open Server Manager.
2.  Navigate to Roles > Web Server (IIS).
3.  Click "Add Role Services" in the Role Services section.
4.  Select "Tracing" and complete the installation wizard.

**Note**: Without tracing enabled, ETW traces will only include HTTP.sys information, limiting the usefulness of the collected data.

### 3\. Configure and Start PerfView Collection

1.  Launch PerfView as an administrator.
2.  Go to Collect > Collect.  
    ![](https://github.com/tripvik/tripvik.github.io/blob/main/assets/perfview1.png?raw=true)
3.  Configure the following settings:
    *   Check "Zip" to compress the output
    *   Set "Circular MB" to 1000 (or higher for longer traces)
    *   Enable "Merge" for consolidated output
    *   Check "Thread Time" for detailed thread analysis
    *   Ensure "IIS" is selected for IIS-specific events
        
        ![](https://github.com/tripvik/tripvik.github.io/blob/main/assets/perfview2.png?raw=true)
        
4.  Click "Start Collection" to begin tracing.

### 4\. Reproduce the Issue

While PerfView is collecting data:

1.  Reproduce the IIS-related issue you're investigating.
2.  Perform the problematic operation multiple times if possible.
3.  Note the exact times of key events for later analysis.

### 5\. Stop the Collection

1.  Once you've captured the issue, click "Stop Collection" in PerfView.
2.  PerfView will process and merge the collected ETL files.
3.  A ZIP file containing the trace data will be saved in the same directory as PerfView.exe.

## Best Practices

*   Keep tracing duration as short as possible while ensuring you capture the entire issue lifecycle.
*   Collect traces during both normal operation and when the issue occurs for comparison.
*   If possible, reduce server load from non-essential processes during tracing.
*   For memory-related issues, consider taking multiple snapshots over time.

## Next Steps

After collecting the PerfView trace:

1.  Analyze the trace using PerfView's built-in tools.
2.  Look for unusual CPU spikes, memory allocations, or long-running operations.
3.  Correlate events with the timestamps of the observed issues.

By following this guide, you'll be well-equipped to collect comprehensive PerfView traces for diagnosing and resolving IIS-related issues efficiently.
