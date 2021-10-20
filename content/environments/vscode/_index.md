---
title: "Visual Studio Code"
date: 2021-10-06
weight: 30
---

This Article will go through the process of setting up Visual Studio Code and utilizing it to
work on the leafriver repository and any container that plays host to that given repo. Here we will connect a docker container that is hosting a flask application.

## Perquisites

 Completion of all tasks under:
   - [*Setting Up Docker*](/environments/docker/)
   - [*Flask Development Container*](/environments/flask/)

 ---

### Step 1. Installing Visual Studio Code

Visual Studio Code is free and built on open source. Go to the download page that can be found [Here.](https://code.visualstudio.com/Download)

For our purposes we will be downloading Visual Studio Code for Windows.

![download-options](/images/environments/vscode/download-options.png)

From your browser run the downloaded executable and follow the prompts. The default for all options are acceptable.

![download-complete](/images/environments/vscode/download-complete.png)

### Step 2. VSCode's Extension To Develop Within The PowerShell

We now need to integrate our WSL Ubuntu environment with VSCode. Go to the extensions tab on the left of the window. Search for **`Remote - WSL`**. The extension will appear. Click **Install**.

Press the hotkey command `CTRL + SHIFT + P` to open the command dialog. Begin to type in `Remote -WSL` and options shall appear. Select Remote-WSL: New WSL Window. This will allow you to open and connect to the WSL Ubuntu Environment.

We now should open a new terminal window. Select **Terminal** from the top of the window. and **New Terminal** from the drop down menu.

![Terminal](/images/environments/vscode/terminal.png)

We should now have our Ubuntu 20.04 Distro's terminal available.

### Add The Code Base

We now want to add our existing code base. Click the file Explorer button, and Open Folder.

![file-explorer](/images/environments/vscode/file-explorer.png)

Select the *show local* button and navigate to `C:\Users\james\PycharmProjects\leafriver` and add the leafriver folder.
