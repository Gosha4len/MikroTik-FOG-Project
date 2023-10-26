# MikroTik + FOG Project
#### A guide on how to connect MikroTik equipment and the FOG Project.
![image](/scr/MF.jpg)

```
In this project, the following software versions and distributions will be used:
```

<br> ![Static Badge](https://img.shields.io/badge/MikroTik-v.7.11.2-293239) ![Static Badge](https://img.shields.io/badge/FOG%20Project-v1.5.10-00567a) ![Static Badge](https://img.shields.io/badge/Debian-12.2.0-a80030)
 
# Table of Contents
<!-- TOC toc.levels=2 -->
- [Platform preparation](#platform-preparation)
- [Deploying FOG](#deploying-fog)
- [Configuring MikroTik equipment](#configuring-mikrotik-equipment)
    - [WinBox method](#winbox-method)
    - [Terminal method](#terminal-method)
- [Final stage of installation](#final-stage-of-installation)
- [Checking the loader](#checking-the-loader)
<br>

### Platform preparation
```
Go to the next website by clicking and download the .tar.gz archive to your computer:
```
##### &#127757; [fogproject.org](https://fogproject.org/download)  <----Here

![site](/scr/Screenshot_2.png)

```
Then connect to the device on which you will implement FOG 
(in my case, it is a virtual machine running Debian 12.2.0), 
and transfer the archive there: 
```

![termius](/scr/Screenshot_1.png)
###### <p style="text-align: center;"> I used the [Termius](https://termius.com/) application, switched to the SFTP tab, selected the archive and just dragged it</p>

<br><br>

```
Now let's check the file on the host:
```
```
ls
```
![ls](/scr/Screenshot_3.png)
```
Unpack the downloaded archive using the following command with arguments:
```
```
tar xvzf fogproject-1.5.10.tar.gz
```
```
Move to the directory containing the executable file for installing FOG:
```
```
cd fogproject-1.5.10/bin
```

<br><br>

### Deploying FOG

```
Run the script with the following command:
```
```
./installfog.sh

Question about the version:
```
![fog](/scr/Screenshot_4.png)
```
Question about the type of installation:
```
![fog](/scr/Screenshot_5.png)
```
Question about the network adapter:
```
![fog](/scr/Screenshot_6.png)
```
Question about changing the network adapter settings:
```
![fog](/scr/Screenshot_7.png)
```
Question about the router address (DHCP):
```
![fog](/scr/Screenshot_8.png)
```
Question about DNS processing:
```
![fog](/scr/Screenshot_9.png)
```
Question about setting up FOG as a DHCP server:
```
![fog](/scr/Screenshot_10.png)
```
Question about setting up FOG as a DHCP server:
```
![fog](/scr/Screenshot_11.png)
```
The question of enabling HTTPS on the FOG site:
```
![fog](/scr/Screenshot_12.png)
```
The question of changing the hostname:
```
![fog](/scr/Screenshot_13.png)
```
The question about data collection:
```
![fog](/scr/Screenshot_14.png)
```
Display the selected settings, as well as a question about proceeding:
```
![fog](/scr/Screenshot_15.png)
```
At this stage, please pay attention to the contents inside the green square (↑) 
```
![fog](/scr/Screenshot_17.png)
```
While the packages installation is in progress, let's move on to configuring MikroTik
```
<br><br>

### Configuring MikroTik equipment

```
Need to specify in the MikroTik DHCP server settings that you want to load specific files from 
a designated server, specifically using two options: option 66 and option 67.

Code 66 (TFTP Server Name): 
This code is used to specify the name of the TFTP (Trivial File Transfer Protocol) server that the client 
should use to download configuration files or other data. Typically, this field contains the name of the 
TFTP server that will provide the necessary files to the client.

Code 67 (Bootfile Name): 
This code is used to specify the name of the file that the client should attempt to download from
the TFTP server specified in option 66. This file can be, for example, a configuration file for loading 
the operating system or other data that the client needs during startup.

And also need to add the "Next server" parameter and enable the added options
```


#### WinBox method
```
IP:
```
![winbox](/scr/Screenshot_18.png)
```
DHCP Server:
```
![winbox](/scr/Screenshot_19.png)
```
Options:
```
![winbox](/scr/Screenshot_20.png)
```
Plus:
```
![winbox](/scr/Screenshot_21.png)
```
Return to the green square from the "Deploying FOG" chapter.
Create option 66 based on the parameters from the executable file:
```
![winbox](/scr/Screenshot_25.png)
```
The settings for option 66 should have the following format; 
You can name the "Name" parameter as desired, and don't forget the quotes around the "Value":

Name: [Your Choice]
Code: [66]
Value: "[Value With Quotes]"
```
![winbox](/scr/Screenshot_22.png)
```
OK:
```
![winbox](/scr/Screenshot_23.png)
```
Plus:
```
![winbox](/scr/Screenshot_24.png)
```
Return to the green square from the "Deploying FOG" chapter.
Create option 67 based on the parameters from the executable file:
```
![winbox](/scr/Screenshot_26.png)
```
The settings for option 67 should have the following format; 
You can name the "Name" parameter as desired, and don't forget the quotes around the "Value":

Name: [Your Choice]
Code: [67]
Value: "[Value With Quotes]"
```
![winbox](/scr/Screenshot_27.png)
```
OK:
```
![winbox](/scr/Screenshot_28.png)
```
Verify the correctness of the entered data:
```
![winbox](/scr/Screenshot_29.png)
```
Networks:
```
![winbox](/scr/Screenshot_30.png)
```
Select the appropriate network:
```
![winbox](/scr/Screenshot_31.png)
```
Return to the green square from the "Deploying FOG" chapter.

Fill in the following parameters, such as:
- Next Server
- Boot File Name
- DHCP Options
```
![winbox](/scr/Screenshot_32.png)
```
Verify the correctness of the entered data:
```
![winbox](/scr/Screenshot_33.png)
```
OK:
```
![winbox](/scr/Screenshot_34.png)
<br><br>

#### Terminal method

1) Creating the necessary options

```
/ip/dhcp-server/option/
add name=next-server code=66 value="'192.168.77.249'"
add name=filename code=67 value="'undionly.kkpxe'"
```

```
Сheck the created information:
```

```
/ip/dhcp-server/option/print
```

![terminal](/scr/Screenshot_43.png)

2) Connecting the created options and adding information

```
/ip/dhcp-server/network
set 0 next-server=192.168.77.249
set 0 boot-file-name=undionly.kkpxe
set 0 dhcp-option=filename,next-server
```

```
Сheck the created information:
```

![terminal](/scr/Screenshot_44.png)
<br><br>

### Final stage of installation
```
Return to the terminal with FOG. The installation should have been stopped when updating the database.
Open a browser and go to the link to update the database: 
```
![mysql](/scr/Screenshot_36.png)
```
Install/Update Now:
```
![mysql](/scr/Screenshot_37.png)
```
Done, now let's go back to the terminal with FOG:
```
![mysql](/scr/Screenshot_38.png)
```
Enter:
```
![mysql](/scr/Screenshot_39.png)
```
Wait for the installation to complete:
```
![complete](/scr/Screenshot_40.png)
<br><br>

### Checking the loader

```
Set the BIOS to boot from PXEBoot/Lan Boot ROM/PXE ROM/PXE Network:
```
![complete](/scr/Screenshot_16.png)
```
Waiting for a download using PXE:
```
![end](/scr/Screenshot_41.png)
```
Done, you can use it:
```
![end](/scr/Screenshot_42.png)
