+++
title = 'Setting up XTchain'
date = 2025-07-16T19:29:14+02:00
+++
XTchain is a script-based compilation environment designed to streamline complex build processes across multiple platforms.
This guide explains how to set up and use XTchain efficiently on your system. You will learn how to launch the XTchain
environment, configure your terminal for convenience, and ensure that your development setup is ready for building projects
like ExectOS.

## Setting Up XTchain on Linux
Setting up XTchain on Linux is designed to be as straightforward and hassle-free as possible. Once you have downloaded and
extracted the XTchain archive, there is virtually no additional configuration required to get started. Simply navigate to the
extracted directory and launch the main executable script:
```
/path/to/xtchain/xtchain
```
This single command initializes the entire XTchain environment, automatically setting up all necessary paths, environment variables,
and dependencies behind the scenes. The script ensures that your terminal session is fully prepared for building ExectOS right away,
so you can focus on development rather than setup.

Because Linux inherently supports scripting and shell environments, XTchain takes advantage of native shell features to provide
a seamless experience. The environment is preconfigured to recognize common build tools and utilities, minimizing any need for
manual intervention.

For convenience, you might consider either creating an alias in your shell configuration file (such as .bashrc or .zshrc):
```
alias xtchain="/path/to/xtchain/xtchain"
```
or adding the XTchain directory to your PATH environment variable:
```
export PATH="/path/to/xtchain:$PATH"
```
After making either change, reload your shell configuration with source ~/.bashrc (or source ~/.zshrc) to make the modification
effective immediately. This way, you can start XTchain from anywhere simply by typing xtchain, without navigating to its folder
each time.

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
disabled on this system**. This indicates that your system's execution policy is set to block all or unsigned scripts. To
permanently fix it, open PowerShell as Administrator and enter:
```
Set-ExecutionPolicy RemoteSigned
```
This policy allows:
 - All locally created scripts (including xtchain.ps1) to run without signing.
 - Scripts downloaded from the internet to require a trusted digital signature.
Warning: Only change execution policy if you trust the scripts you run.

### 3. Use Git Bash for a Unix-like Environment
As an alternative, if you have installed Git for Windows along with the bundled MSYS2 tools, you can leverage its Bash shell and
Unix-style scripts to initialize XTchain exactly as on Linux. During the installation process, you have the option to include a set
of additional Unix utilities that, combined with the Bash shell, provide a powerful and familiar development environment. This setup
allows you to run XTchain's shell scripts seamlessly on Windows, mirroring the Linux workflow and ensuring consistency across
platforms without the need for platform-specific adjustments.

For added convenience, you can also configure a dedicated profile in Windows Terminal that automatically launches Git Bash with the
XTchain environment pre-initialized. This makes it easy to jump straight into a ready-to-use shell session. The process for creating
this terminal profile is analogous to the one described in section 1.
