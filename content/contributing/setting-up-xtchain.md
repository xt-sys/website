+++
title = 'Setting up XTchain'
date = 2025-07-16T19:29:14+02:00
+++
XTchain is a script-based compilation environment designed to streamline complex build processes across multiple platforms.
This guide explains how to set up and use XTchain efficiently on your system. You will learn how to launch the XTchain
environment, configure your terminal for convenience, and ensure that your development setup is ready for building projects
like ExectOS.

## Setting Up XTchain on Windows
On Windows, the simplest way to launch XTchain is by defining a dedicated profile in Windows Terminal that automatically
opens PowerShell, loads the XTchain environment, and remains interactive for further commands.

### 1. Create a Custom Windows Terminal Profile
A dedicated terminal profile ensures that every time you open XTchain, the environment is preconfigured and ready to use.

 1. Open **Windows Terminal**.
 2. Click the down-arrow next to a tab and select **Settings**.
 3. Choose **Add a new profile**.
 4. In the **Command line** field, enter:
```
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Bypass -NoExit -File "C:\Path\To\XTchain\xtchain.ps1"
```
Replace C:\Path\To\XTchain with the actual folder where xtchain.ps1 resides. Give the profile a name (for example, XTchain),
assign an icon if desired, and save the settings. Now when you select the XTchain profile, Windows Terminal will launch
PowerShell, bypass execution restrictions, run the initialization script, and keep the window open. The command works as follows:
it starts the built-in PowerShell executable (%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe), temporarily disables
the script execution policy with **-ExecutionPolicy Bypass**, prevents the terminal from closing after the script finishes using
**-NoExit**, and runs the XTchain initialization script specified with **-File "C:\Path\To\XTchain\xtchain.ps1"**. This setup
ensures that the XTchain environment is ready every time you open the terminal.

### 2. Handling Execution Policy Errors
When running the profile, you might see the following error message: **File ... cannot be loaded because running scripts is
disabled on this system.**. This indicates that your system’s execution policy is set to block all or unsigned scripts. To
permanently fix it, open PowerShell as Administrator and enter:
```
Set-ExecutionPolicy RemoteSigned
```
This policy allows:
 - All locally created scripts (including xtchain.ps1) to run without signing.
 - Scripts downloaded from the internet to require a trusted digital signature.
Warning: Only change execution policy if you trust the scripts you run.
