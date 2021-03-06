# hdb-bto IS4302-Tut3Grp1
HDB BTO housing grant and flat allocation process in blockchain

# Web Application

**Software Requirements:**

•	Download and Install NetBeans IDE Download Bundles :link: https://netbeans.org/downloads/index.html under Java EE

•	Download and Install MySQL Community Server from :link: http://dev.mysql.com/downloads/mysql (Use the all-in-one installer which will also install the Workbench if you are using Windows)

•	Download MySQL Connector/J version 5.1.45 from :link: http://dev.mysql.com/downloads/connector/j (Click on "Looking for previous GA versions?" if you are unable to find your OS)

•	For the MySQL Connector/J, extract and copy mysql-connector-java-5.1.45-bin.jar into these two folders: 1) NetBeans installation folder - "C:\Program Files\NetBeans 8.2\ide\modules\ext"; and 2) GlassFish installation folder - "C:\glassfish-4.1.1\glassfish\domains\domain1\lib". Your installation folders might differ from mine. To use this JAR file in NetBeans, replace the existing driver JAR file in NetBeans under "Services > Databases > Drivers > MySQL (Connector/J driver) > Customize"

**Deploying Web Application:**

•	Startup NetBeans. 
- For Windows Users: (Ensure that your MySQL56 is running by checking on Services under Windows Adminstrative Tools)
- For Mac Users: (Ensure that your SQL is running by pressing "Command + Spacebar" and search for mysql) 

•	Once you are in NetBeans, click on the Services tab, you should see MySQL Server at localhost:3306 under Databases. Right click on it and select "Properties". Ensure that the fields are as follows:
- Server Host Name: localhost
- Server Port Number: 3306
- Administrator User Name: root 
- Adminstrator Password: password

•	Once you are in NetBeans, click on the Services tab, you should see MySQL Server at localhost:3306 under Databases. Right click on it and select "Create Database". Name this Database "hdb-bto" and check "Grant Full Access To:" and select "mysql.sys@localhost" 

•	After which, a jdbc should be created. Ensure that it's connected by right clicking on the jdbc and select "Connect". You should be prompted with a popup asking for credentials. Simply enter "root" as User name and "password" as Password.

•	Download the project from this repo at this :link: https://github.com/QI-XING/hdb-bto/blob/master/hdb-bto.zip. 
  
•	In your NetBeans, click on "Open Project" and select the project downloaded above. (To check if the project has been successfully opened, you should be able to see 3 project files being opened under the Projects tab, namely 1) HDB-BTO 2) HDB-BTO-ejb 3) HDB-BTO-war)

•	Right click on HDB-BTO and select "Deploy". You should be able to see "BUILD SUCCESSFUL". 

•	The web application can then be reached with this url: http://localhost:8080/HDB-BTO-war/
  
•	We have pre-define some credentials: 
- HDB ADMIN: Username: HDB1, Password: alvinhdb
- CPF ADMIN: Username: CPF1, Password: aaroncpf
- User: Register a user through the web application

**_At any point if the web application crashes, simply redeploy the project (HDB-BTO)_** :+1:

# Hyperledger Preparation

**Software Requirements:**

•	Download and install vagrant (include virtualbox installation) on the host system from vagrantup.com

•	If you are using windows 7, please use vagrant 1.9.6 only and Virtual Box 5.1.x

•	Download the Vagrantfile from this repo at this :link: https://github.com/QI-XING/hdb-bto/blob/master/Vagrantfile to an empty folder

**Pre-requisites:**

•	2 cores (enable intel-vt or amd-v in BIOS)

•	4GB RAM

•	vagrant (include virtualbox)

# Provisioning

### If you are new to Vagrant: ###

-	change directory to the folder containing the downloaded Vagrantfile

-	run vagrant up from the commandline(OSX: terminal, Win: cmd.exe) to provision the entire system. Download and provisioning system can take a long up (up to 10-20mins)

- Once you reach the success screen output,

```
==> default:
==> default: tern_from_ts@0.0.1 node_modules/tern_from_ts
==> default: --------------------------------------------------------------------
==> default: Success!
==> default: run 'node server.js -p 8080 -a :' to launch Cloud9
==> default: ++ echo 'cd /cloud9 ; su ubuntu -c "nodejs server.js -l 0.0.0.0 -w /home/ubuntu --auth root:secret" &'
==> default: ++ cd /cloud9
==> default: ++ su ubuntu -c 'nodejs server.js -l 0.0.0.0 -w /home/ubuntu --auth root:secret'
```

- and the terminal command exits, you can open the browser based IDE at http://localhost:7171

- login into the IDE using the default credentials -> Username: root and Password: secret

### To change Vagrant Settings: ###

•	Default setting: Cloud9 IDE (localhost:7171), playground composer (localhost: 4040), guest (localhost:3000, localhost:3001)

>*You may edit the original Vagrantfile, near the last lines, and add additional port mapping pairs(one for each rest-server instance). eg:*
```
config.vm.network :forwarded_port, guest: 3000 host: 3000
config.vm.network :forwarded_port, guest: 3001 host: 3001
```
to add a new port mapping of 3000, 3001

### Update the vagrant changes using ###
```
vagrant halt
vagrant up
```
>*If you do not have the updated Vagrant version yet,*

### 1. Update the code in composer-playground (inside the ~/composer-playground folder): ###
```
git pull
```
(If there is a conflict error with script.sh and createPeerAdminTLSCard.sh, just remove those files and run git pull)

### 2. Restart playground using (important step, even if you have started playground before) (note the different -f option) ###
```
./playground.sh down
./playground.sh -f docker-compose-deploy.yaml up
```
# Build up the network

•	This is the deployable executable file within the Composer runtime. If you are not familiar with Cloud9 IDE, it has a built-in terminal window, you may choose to use it to issue commands, it does simplify navigating between terminal windows

### 1. Setting up the network. Open the terminal at Cloud9 IDE. Navigate to the ~/composer-playground folder. Run the playground.sh script to launch docker images and start the composer playground container ###
```
cd composer-playground
./playground.sh
```

Open the playground web UI in a browserby going to http://localhost:4040


If a connection error occurs, you might need to clear your local browser cache.

### 2. Create a registrar user account on the certificate authority using the command: ###
```
docker exec -it ca.org1.example.com fabric-ca-client enroll -M registrar -u http://admin:adminpw@localhost:7054
```
### 3. Create peer admin (hdbAdmin) and note down the given password. ###
```
docker exec -it ca.org1.example.com fabric-ca-client register -M registrar -u http://localhost:7054 --id.name hdbAdmin --id.affiliation org1 --id.attrs '"hf.Registrar.Roles=client"' --id.type user

Password: <randomString>
```
# Generate and Deploy Business Network Archive (BNA) file

- Download the bna file at this :link: https://github.com/QI-XING/hdb-bto/blob/master/hdb-bto.bna

### 1. Open the cloud9 IDE and drop the exported BNA file into the composer-playground folder. ###
#Generates a hbd-bto.bna file within the /hdb-bto directory 
```
mkdir hdb-bto-bna
cd hdb-bto-bna
sudo apt install unzip
unzip ../hdb-bto.bna
```

### 2. Enter the CLI container using: ###

```
docker exec -it cli bash
```
#See that the composer credentials are stored in the /root/.composer directory as cards.
```
ls /root/.composer/cards/
```

### 3. Generate RESTful API to localhost ###
```
composer-rest-server -c hdbadmin@hdb-bto
```

### 4. Run the following command to verify by noting the success response ###
```
~$ composer network ping -n hdb-bto -p hlfv1 -i hdbAdmin -s <randomString>
```

### 5. Shutdown network (optional) ###
```
./byfn.sh -m down
```

# CLI Tool and Identity Management

### 1.	Issuing Identities. Open a terminal and run the following command: ###
```
composer identity issue -c hdbAdmin@hdb-bto -u hdbAdmin -a org.acme.hdb-bto.HDB#hdbAdmin@hdb.com 
```

The grammar is as follows:

>composer identity issue –c < admin card > -u < username to be issued > -a < participant to be attached to >

### 2.	Use the import function to save the card into the identity wallet:  ###
```
composer card import -f hdbAdmin\@hdb-bto-hdbAdmin.card
```

### 3.	There is also a “list” command to show the status of all issued identities: ###
```
composer identity list –c hdbAdmin@hdb-bto
```

### 4.	Deploy your BNA, create new participants and identity using the playground or command line. ###

### 5.	Create instances of composer-rest-server using: ###
```
./start_rest_server.sh -i 1 -p 3000 -c hdbAdmin@hdb-bto
./start_rest_server.sh -i 2 -p 3001 -c cpfAdmin@hdb-bto

(-i option for a backend index. any running and unique integer)
(-p option for port number. needs to match one of the port mapping in Vagrantfile)
(-c card name of identity created and installed in step 4)
```
  after this step, the composer-rest-server is accessible inside vagrant and also on the host (windows or mac)
  You can either run your backend inside vagrant using nodeJS, or use your host computer to run the backend using netbeans.

### 6.	(optional) to destroy a rest server, call 'down' on respective backend index numbers: ###
```
./start_rest_server.sh -i 1 down
./start_rest_server.sh -i 2 down
```
