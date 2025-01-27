# Create a Minecraft Server on Amazon LightSail

This document will walk you through setting up a Minecraft Server on Amazon LightSail. It includes all of the steps for creating an Amazon Lightsail instance, connecting to the instance, installing a Minecraft server, then terminating and restoring the Amazon LightSail instance.

The instructions below will show you how to setup:
* An Amazon Lightsail instance running either
  * Microsoft Windows which has a popular Windows user interface but runs at higher cost reflecting additional licensing fees
  * Ubuntu open source operating system
* A Minecraft server
  * Minecraft: Java Edition Server that supports computer clients and plugins for customization. This is the original version of Minecraft.
  * Minecraft: Bedrock Edition Server that supports consoles, mobile devices and Windows 10. The current release is an early alpha release and is released for evaluation purposes.

Pick one combination:

| Operating System | Minecraft Type |
| -- | -- |
| Microsoft Windows | Minecraft: Java Edition |
| Microsoft Windows | Minecraft: Bedrock Edition |
| Ubuntu | Minecraft: Java Edition |
| Ubuntu | Minecraft: Bedrock Edition |

We have also included some cost optimization advice to optimize the cost of running your Minecraft server.

## Table of Contents

1. About Amazon Lightsail
2. Create an Amazon Lightsail Instance
3. Connect to an Amazon Lightsail Instance
4. Setup Minecraft: Java Edition Server
6. Extending Minecraft: Java Edition Server with Plugins
7. Setup Minecraft: Bedrock Edition Server
8. Connecting Minecraft Clients
9. Create an Amazon Lightsail Snapshot
10. Delete an Amazon Lightsail Instance
11. Restore an Amazon Lightsail Snapshot

## About Amazon Lightsail

Amazon Lightsail is a virtual private server (VPS) provider and is the easiest way to get started with AWS for developers, small businesses, students, and other users who need a solution to build and host their applications on cloud. Lightsail provides developers compute, storage, and networking capacity and capabilities to deploy and manage websites and web applications in the cloud. Lightsail includes everything you need to launch your project quickly – virtual machines, containers, databases, CDN, load balancers, DNS management etc. for a low, predictable monthly price. There are many benefits to using a virtual private server, including affordability, scalability, security, and customizable resources.

### Amazon Lightsail Pricing

Lightsail plans are billed on an on-demand hourly rate, so you pay only for what you use. For every Lightsail plan you use, we charge you the fixed hourly price, up to the maximum monthly plan cost. The advertised monthly price is an hourly-rate cap.

Your Lightsail instances are charged only when they're in the running or stopped state. If you delete your Lightsail instance before the end of the month, AWS will charge you a prorated cost, based on the total number of hours that you used your Lightsail instance. 

AWS pricing for Amazon Lightsail is documented here: https://aws.amazon.com/lightsail/pricing/

### Price Optimizing

Lightsail plans are billed on an on-demand hourly rate. So Start your Lightsail instance when you are playing, snapshot and delete the instance when you have finished to reduce running costs. You can then restore the instance from the snapshot.

* Instance and disk point-in-time snapshots help back up the data on your Amazon Lightsail instances cost $0.05 USD GB/month

### How do I remove the Amazon Lightsail resources from my AWS account?

To remove all your Lightsail resources, delete your Lightsail instances and resources attached to these instances, such as static IP addresses, snapshots, or block storage. Lightsail resources are billed incrementally in hours or in fractions of GB-months. When all Lightsail resources are deleted, you receive no further billing related to Lightsail. For details on deleting your Lightsail resources see: https://aws.amazon.com/premiumsupport/knowledge-center/shut-down-lightsail/

## Create an Amazon Lightsail Instance

### Minecraft System Requirements

Details of minimum requirements can be found here: https://minecraft.fandom.com/wiki/Server/Requirements/Dedicated#Win_Server_2008.2F2012.2F2016.2F2022

* Minecraft server requires at least 1 GB of RAM allocated for the server to run. If you are using Windows or a desktop-based Linux/Unix distribution, you should have at least 1 GB of additional physical RAM in the computer, so the graphics on the desktop don't become laggy.
* A CPU with good single-core performance. The server (as of 1.14) does use additional cores for other operations, but typically three cores are used at most. 
* Windows-based instance prices reflect additional licensing fees. 

| Requirements | Number of Players | Operating System | Recommended Memory | Lightsail Instance Plan | Approximate Price per hour |
| -- | -- |  -- |  -- | -- | -- |
| Minimum | 1-2 | Ubuntu | 2 GB | USD$10 per month | $0.014 per hour |
| Recommended | 2-5 |  Ubuntu | 4 GB | USD$20 per month | $0.027 per hour |
| Optimal | 6+ |  Ubuntu | 8 GB | USD$40 per month | $0.055 per hour |
| Minimum | 1-2 | Windows Server 2019 | 2 GB | USD$20 per month | $0.027 per hour |
| Recommended | 2-5 |  Windows Server 2019 | 4 GB | USD$40 per month | $0.055 per hour |
| Optimal | 6+ |  Windows Server 2019 | 8 GB | USD$70 per month | $0.096 per hour |

Assuming 730.5 hours per month, prices are in USD.

### Setup an Amazon Lightsail instance

* Log into your the AWS management console
* Search for `Lightsail`
* Select `Create instance`
* Select the instance location in the region that is closest to your users
* For Microsoft Windows
  * Under pick your instance image, select `Microsoft Windows`
  * Under select a blueprint, select `OS Only`
  * Select `Windows Server 2019`
* For Ubuntu
  * Under pick your instance image, select `Linux/Unix`
  * Under select a blueprint, select `OS Only`
  * Select `Ubuntu 20.04 LTS`
  * Select the default SSH key pair for connecting to your instance
* Select the instance plan based on the requirements table above
* Identify your instance with a unique name, e.g. minecraft-v1
* A best practice is to tag your AWS resources to organize your billing and control
  * Add a Key-value tags, e.g. Project -> Minecraft
* Select `Create instance`

Lightsail will return you to the Instances page and you should see your new server in a Pending state while it is being created. When the server is ready for use, select its name to view its connect details.

### Create Static IP

You can create static IP addresses to keep the same IP address every time you reboot your instance. For more information, see [Static IP addresses in Amazon Lightsail](https://lightsail.aws.amazon.com/ls/docs/en_us/articles/lightsail-create-static-ip). This makes it easier for your Minecraft clients to connect to your Minecraft server if you restart the instance.

* Open the Amazon Lightsail management console
* Open your server instance
* Select `+ Create static IP`
* Identify your static IP with a unique name, e.g. minecraft-static-ip-v1
* Select `Create`
* Under Attach to an instance, select the name of your new server instance
* Record the static IP address for the connection step below

### Configure Amazon Lightsail Networking

You need to create Lightsail networking rules to open ports to the internet, or to a specific IPv4 address or range.

* Open the Amazon Lightsail management console
* Open your server instance
* Select `Networking`
* Under IPv4 Firewall, select `+ Add rule`
* For Minecraft: Java Edition
  * Add Application = Custom, Protocol = TCP, Port = 25565 
  * Add Application = Custom, Protocol = UDP, Port = 25565 
* For Minecraft: Bedrock Edition
  * Add Application = Custom, Protocol = UDP, Port = 19132

### Create DNS Zone (optional)

If you have registered your own domain name, you can 

* In the Amazon Lightsail management console, select the `Networking` tab
* Enter the domain name you want to manage in Amazon Lightsail, e.g. example.com
* A best practice is to tag your AWS resources to organize your billing and control
  * Add a Key-value tags, e.g. Project -> Minecraft
* Select `Create DNS zone`
* Select `+ Add record`
* Select `A record`, this is to associate your domain or a subdomain with an IPv4 address
* Enter a subdomain, e.g. minecraft
* Under resolves to, select your static ip address
* Select the `Green tick`

You need to configure your domain provider to use the name servers that are listed under `Name servers`. If you setup an A record to your static IP address, then you can use this instead of the IP address for the remote desktop connection and for connecting to the server with your Minecraft clients. We recommend testing with these connections with the IP address first before switching to using a subdomain.

More details on how to configure your DNS hosting to support Amazon Lightsail are here: https://lightsail.aws.amazon.com/ls/docs/en_us/articles/lightsail-how-to-create-dns-entry

## Connect to an Amazon Lightsail Instance

Lightsail offers a 1-click secure connection to your instance's terminal right from your browser, supporting SSH access for Linux/Unix-based instances and RDP access for Windows-based instances. To use 1-click connections, launch your instance management screens, click Connect using SSH or Connect using RDP, and a new browser window opens and automatically connects to your instance.

### SSH for Ubuntu Servers

You can connect to your Ubuntu instance using the browser-based SSH client built into Amazon Lightsail or connect using your own SSH client. For more details on connecting to Amazon Lightsail with SSH see: [Connect to your Linux or Unix instance using SSH in terminal](https://lightsail.aws.amazon.com/ls/docs/en_us/articles/amazon-lightsail-ssh-using-terminal)

You will need the static IP address of your Ubuntu instance.

* Open the Amazon Lightsail management console
* Select `Account` in the top menu and select `Account`
* Select the `SSH keys` tab
* Download the Default ssh key for the AWS region of your instance

* Open a terminal window on your local machine.
* Enter the following command to make the private key of the key pair readable and writable only by you. This is a security best practice required by some operating systems.

```bash
sudo chmod 600 /path/to/private-key.pem
```

Update this command with the public static ip and location of your ssh .pem file.

```bash
ssh ubuntu@##.###.##.### -i ./Downloads/minecraft.pem
```

### Microsoft Remote Desktop for Windows Servers

Gather connection details for your Amazon Lightsail instance. 

* In the Amazon Lightsail management console, open your new Windows Server instance
* Under the Connect tab, record the details:
  * Connect to static ip address, e.g. ##.##.###.###
  * User name, e.g. Administrator
  * Select `Show default password`, copy the default password from the popup

Enter the connection details in your Microsoft Remote Desktop application.

* Microsoft Remote Desktop
* Select `Add PC`
* For the PC name, enter the static IP address from the the Amazon Lightsale management console for your Windows server
* Enter a friendly name, e.g. minecraft-java-v1
* Select `Connect to an admin session`
* Select `Save`

Initate the remote desktop connection

* Open (double click) the remote desktop connection
* Enter the Username
* Enter the Password
* Select `Continue`

The Windows Server 2019 desktop should open.

### Internet Explorer Browser Security for Window Servers

*We strongly recommend you consult your network administrator before performing the any security related steps. Making such configuration changes without consulting administrator may put your organization in a risk.*

Microsoft disables file downloads by default in some versions of Internet Explorer as part of its security policy. To allow file downloads in Internet Explorer, follow these steps:

* Open `Internet Explorer`.
* From the `Tools` menu, select `Internet Options`.
* In the `Internet Options` dialog box, click the `Security` tab.
* Click `Custom Level`.
* In the `Security Settings` dialog box, scroll to the `Downloads` section.
* Under `File download`, select `Enable`, and then click `OK`.
* In the confirmation dialog box, click `Yes`.
* Click `OK` > `Apply` > `OK`.

You can now download files.

## Setup a Minecraft: Java Edition Server

Minecraft Java edition is the original version of Minecraft that supports computer clients.

### Install Java

Download and install Java for Windows or Ubuntu with the links below.

To test the Java installation, open a terminal window
```bash
java -version
```

#### Windows Only - Install Java 17 LTS

Java 17 LTS is the latest long-term support release for the Java SE platform. JDK 17 binaries are free to use in production and free to redistribute, at no cost, under the Oracle No-Fee Terms and Conditions License. Download and install the latest version of Java from https://www.oracle.com/java/technologies/downloads/#jdk17-windows

#### Ubuntu Only - Install Java

Install with:
```bash
sudo apt install default-jre
```

### Download Minecraft: Java Edition Server

You can download the official Minecraft: Java Edition server from the Minecraft website or there are many forks of Minecraft. The installation and running is similar. To support plugins we recommend the Paper version.

| Minecraft Server | Website |
| -- | -- |
| Official Minecraft Server | https://www.minecraft.net/en-us/download/server |
| Paper | https://papermc.io/downloads |

Download the .jar files for each and save into a new folder. e.g. "C:\Program Files\minecraft"

### Windows Only - Configure Server Firewall for Minecraft: Java Edition

If Window's firewall are set incorrectly, it will black the connection to your Minecraft server. 

* Search for and open Window's `Control Panel`
* Open `System and Security`
* Under `Windows Defender Firewall`, select `Allow an app through Windows Defender Firewall`
* Search for `Java (TM) Platform SE binary`
  * If not visible, select `Allow another app...`
  * Browse for the 'java.exe' that you just installed. e.g. Look for `C:\Program Files\Java\jdk-17\bin\java.exe`
  * Select `Add`
  * Tick the `Private` and `Public` checkboxes.
  * Select `OK` and close Control Panel.

### Start Minecraft: Java Edition Server

* Open a Terminal window
* Use cd commands to move to the minecraft folder
* Start the minecraft server with the command: `java -jar [name-of-minecraft-jar]`
  * Minecraft e.g. `java -jar server.jar
  * Paper e.g. `java -jar paper-1.1#.#-###.jar





## Extending Minecraft: Java Edition Server with Plugins

To install these plugins, create a `Plugins` folder inside your Minecraft folder and add the .jar file for the plugin.

| Plugin | Description | URL | Notes |
| -- | -- | -- | -- |
| WorldEdit | A Minecraft map editor | https://www.curseforge.com/minecraft/mc-mods/worldedit | -- |
| WorldGuard | Lets players guard areas of land | https://dev.bukkit.org/projects/worldguard | -- |
| Dynmap | A Google Maps-like map for your Minecraft server | https://dev.bukkit.org/projects/dynmap | Need to open port 8213 |






## Setup Minecraft: Bedrock Edition Server

### Windows Only - Configure Server Firewall for Minecraft: Bedrock Edition

If Window's firewall are set incorrectly, it will black the connection to your Minecraft server. 

* Search for and open Window's `Control Panel`
* Open `System and Security`
* Select `Windows Defender Firewall`
* Select `Advanced settings`, this will open a Windows Defender Firewall with Advanced Security popup
* Select `Inbound Rules`
* Under `Actions`, select `New Rule...`
* Under `Rule Type`
 * Select `Port`
 * Select `Next`
* Under `Protocols and Ports`
  * Select `UDP`
  * Select `Specific local ports` and enter `19132`
  * Select `Next`
* Under `Action`
  * Select `Allow the connection`
  * Select `Next`
* Under `Profile`
  * Tick the `Domain`, `Private`, `Public` checkboxes
  * Select `Next`
* Under `Name`
  * Enter a name, e.g. minecraft-bedrock
  * Select `Finish`

### Windows Only - Install and Start Minecraft: Bedrock Edition Server

You can download the official Minecraft: Bedrock Edition server for Windows from the Minecraft website: [Download Bedrock Server Software for Minecraft](https://www.minecraft.net/en-us/download/server/bedrock).

Download the .zip file and save into a new folder. e.g. "C:\Program Files\minecraft"
Unzip the container file into an empty folder. Start the server by executing the bedrock_server.exe file.
Follow the bundled how to guide in `bedrock_server_how_to.html` to configure the server.

### Ubuntu Only - Install and Start Minecraft: Bedrock Edition Server

Update the script below using the latest version of the Minecraft: Bedrock Edition Server for Ubuntu. You can copy the link to the official Minecraft: Bedrock Edition server for Ubuntu from the Minecraft website: [Download Bedrock Server Software for Minecraft](https://www.minecraft.net/en-us/download/server/bedrock).

Connect to the Ubuntu instance using SSH and run these commands:

```bash
apt-get update
apt-get install -y unzip
cd /home/ubuntu
wget https://minecraft.azureedge.net/bin-linux/bedrock-server-1.17.32.02.zip
unzip bedrock-server-1.17.32.02.zip -d /home/ubuntu
echo "LD_LIBRARY_PATH=. ./bedrock_server" > minecraft.sh
chmod +x minecraft.sh
apt-get -y upgrade
```

Start Minecraft with this command:

```bash
minecraft.sh
```

You can close the script with the `CTRL+c` command or it will close when the SSH connection terminates.

#### Linux nohup command

The [`nohup`](https://www.computerhope.com/unix/unohup.htm) command executes another command, and instructs the system to continue running it even if the session is disconnected.

```bash
nohup ./minecraft.sh &
```

When using &, you see the bash job ID in brackets, and the PID (process ID) listed after, e.g. [1] 25132

You can terminate the process using the PID, e.g.
```bash
kill -9 25132
```

## Connecting Minecraft Clients

Minecraft Java edition is the original version of Minecraft that supports computer clients. Minecraft Bedrock edition supports consoles, mobile devices and Windows 10.

[Differences Between Minecraft: Java Edition and Minecraft](https://help.minecraft.net/hc/en-us/articles/360058534412-Differences-Between-Bedrock-and-Java)

Every Minecraft client connecting to your Minecraft server will need a licensed copy of Mincecraft. You can purchase the application from the Minecraft store: https://www.minecraft.net/en-us/get-minecraft#

### Minecraft: Java Edition Clients

The original version of Minecraft! Java Edition has cross-platform play between Windows, Linux and macOS, and also supports user-created skins and mods. Includes a decade’s worth of updates, with much more to come! You can purchase this application from the Minecraft website: https://www.minecraft.net/en-us/store/minecraft-java-edition

### Minecraft: Bedrock Edition Clients

You can purchase the Bedrock versions of Minecraft for mobiles, consoles, and Windows 10 from the Minecraft store: https://www.minecraft.net/en-us/get-minecraft#

### Connecting to your Minecraft: Java Edition Server

* Open your Minecraft client (Java client for Java server, Bedrock client for Bedrock server)
* Select `Play`, this will open the Minecraft: Java Edition game
* Select `Multiplayer`
* Select `Add Server`
  * Enter a Server Name
  * Enter the Static IP address for your Minecraft: Java Edition Server, e.g. ##.##.###.###
  * Select `Done`
* Select the Minecraft server name and select `Join Server`

## Securing Minecraft

The names and ids of players can be discovered in the Minecraft server console when the players first join the server.

### Server Properties

`server.properties` is the file that stores all the settings for a multiplayer server for both Java and Bedrock editions. For more details see: https://minecraft.fandom.com/wiki/Server.properties

### OP - Operator Status Permission List

When settting up a server you need to edit a json file to grant a user operator status. The /op command can be used by existing operators to give other players operator status. When a player has been granted operator status, they can run game commands such as changing the gamemode, time, weather, etc.

#### Permission List Example for Minecraft: Java Edition

For Minecraft: Java Edition:
* In the Minecraft folder, edit the `ops.json` file

```json
[
  {
    "uuid": "####-replace-with-uuid-####",
    "name": "####-replace-with-name-####"
    "level": 4,
    "bypassesPlayerLimit": false
  },
  {
    "uuid": "####-replace-with-uuid-####",
    "name": "####-replace-with-name-####"
    "level": 4,
    "bypassesPlayerLimit": false
  }
]
```

#### Permission List Example for Minecraft: Bedrock Edition

You can adjust player specific permissions by assigning them roles in the permissions.json that is placed in the same directory as the server executable. The file contains a simple JSON object with XUIDs and permissions. Valid permissions are: operator, member, visitor. Every player that connects with these accounts will be treated according to the set premission.

For Minecraft: Bedrock Edition:
* In the Minecraft folder, edit `permissions.json` file:

```json
[
    {
        "permission": "operator",
        "xuid": "451298348"
    },
    {
        "permission": "member",
        "xuid": "52819329"
    },
    {
        "permission": "visitor",
        "xuid": "234114123"
    }
]
```

### Allow List

To only allow named players to connect to your Minecraft sever, you need to enforce an allow list. As well as manually editing the file, the command `/whitelist` can also be used by operators. 

In the Minecraft folder:
* Edit the `allowlist.json` file
  * Add the names of the users you want to allow access, see the examples below.
* Edit the `server.properties` file
  * Change the setting `enforce-whitelist=true`

#### Allow List Example for Minecraft: Java Edition

For Minecraft: Java Edition

```json
[
  {
    "uuid": "####-replace-with-uuid-####",
    "name": "####-replace-with-name-####"
  },
  {
    "uuid": "####-replace-with-uuid-####",
    "name": "####-replace-with-name-####"
  }
]
```

A longer explanation can be found here: https://minecraft.fandom.com/wiki/Whitelist.json

#### Allow List Example for Minecraft: Bedrock Edition

For Minecraft: Bedrock Edition

```json
[
  {
    "xuid": "####-replace-with-uuid-####",
    "name": "####-replace-with-name-####",
    "ignoresPlayerLimit":false
  },
  {
    "xuid": "####-replace-with-uuid-####",
    "name": "####-replace-with-name-####",
    "ignoresPlayerLimit":false
  }
]
```

A longer explanation can be found here: https://www.gtxgaming.co.uk/clientarea/knowledgebase/628/How-to-whitelist-on-your-Bedrock-server.html

## Create an Amazon Lightsail Snapshot

When you are done playing Minecraft, stop your server instance, create a manual snapshot, and delete your instance. Lightsail plans are billed on an on-demand hourly rate. So Start your Lightsail instance when you are playing, snapshot and delete the instance when you have finished to reduce running costs. You can then restore the instance from the snapshot.

For Windows instances, you must stop the instance before creating a snapshot. This is necessary to ensure a new administrator password is generated for the new instance you create from the snapshot. For more information, see [Creating a snapshot of your Windows Server instance in Amazon Lightsail](https://lightsail.aws.amazon.com/ls/docs/en_us/articles/prepare-windows-based-instance-and-create-snapshot).

* Open the Amazon Lightsail management console
* Open your Windows Server instance
* Select `Stop`
* Select `Snapshots`
* Under manual snapshots, select `+ Create snapshot`
* Give your snapshot a name
* Select `Create`

The snapshot process takes a few minutes to complete. 

After the snapshot is created, you can choose `Start` at the top of the instance management page to start your instance again.

## Delete an Amazon Lightsail Instance

*Deleting this instance will permanently destroy it, including all of its data. Make sure you have created a manual snapshot of your instance before deleting.*

If you delete your Lightsail instance before the end of the month, AWS will charge you a prorated cost, based on the total number of hours that you used your Lightsail instance.  

* Open the Amazon Lightsail management console
* Select the `Delete` tab
* Select `Delete instance`
* Select `Yes, delete`

You still be charged for unattached resources if you do not delete them such as as the Static IP address and also for snapshot storage. 

### Delete a Static IP address

* Open the Amazon Lightsail management console
* Select the `Networking` tab
* Select the name of your static up instance. You should see a message `This static IP is not attached to an instance.`
* Select the `Delete` tab
* Select `Delete static IP`
* Select `Yes, delete`

## Restore an Amazon Lightsail Snapshot

* Open the Amazon Lightsail management console
* Select the `Snapshots` tab
* Expand the arrow listing the number of snapshots that you created for your instance, you should see a list of snapshots ordered by their creation date
* Select the three vertical dot menu button
* Select `Create new instance`
* Follow the steps above for `Setup a Windows Lightsail instance`
