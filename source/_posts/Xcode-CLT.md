---
layout:     post
title:      "Xcode-CLT"
date:       2019-09-22
author:     "weijie"
catalog:    true
tags:
    - 工具
---


![image](https://miro.medium.com/max/2320/0*glm2SHyTdpQ-SBod)
# gyp: No Xcode or CLT version
Did you recently update your macOS Catalina to version 10.15.2? If you did, then you probably in the same boat too. What I immediately noticed from my terminal is this new shinny error about gyp: No Xcode or CLT version detected any time I ran either yarn install or npm install

```
gyp: No Xcode or CLT version detected!
gyp ERR! configure error
```

Well if you are wondering if that was my entire error? It is not even close. The line goes on and on. The funny thing is am so sure I have command line tools installed. The result of xcode-select --install should start the reinstallation process but if you get the result in the image below then you already have command line tools installed
xcode-select: error: command line tools are already installed, use “Software Update” to install updates  
![image](https://miro.medium.com/max/1540/0*wIhKeLDe-Jz_1DMh)
# Solution
Reinstall command line tools by removing the previously installed version
## Step 1
First, get the location of the installed command line tools by running the command below:

```
xcode-select --print-path
```

the result of the above command /Library/Developer/CommandLineTools
## Step 2
Knowing the path to the currently installed command line tools from the previous step, You can now go ahead and remove it from the system. For the next set of commands, you need sudo privileges to run successfully.

```
sudo rm -r -f /Library/Developer/CommandLineTools
```

Provide the root password to remove the command line tools. If you already have git installed, you would get a prompt to guide you through the installation of command line developer tools.


Prompt to reinstall command line developer tools
Click on install and follow the rest of the instructions in prompt to reinstall command line developer tools. If for some reasons, you do not get the prompt right after uninstalling your previous command line developer tools, no need to panic. Run the following command to get the prompt.  
![image](https://miro.medium.com/max/1540/0*s8rdlR3j3xVHcl95)
```
xcode-select --install
```

Prompt after running xcode-select -install
Click on install and then agree to the licence to proceed with the installation. Depending on your internet speed it will take some time for the system to complete the download of the command line developer tools. The installation process should proceed immediately after a successful download. Look out for the done button as shown in the image below to confirm command line tools is successfully installed.
Done screen  
![image](https://miro.medium.com/max/1540/0*UURlqdam5Xyrp7DD)  
With the reinstallation of the command line developer tools the gyp: No Xcode or CLT version detected error message should disappear when you run any yarn or npm commands from the command line.
