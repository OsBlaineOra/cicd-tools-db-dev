# CI/CD Tools for Database Developers

## Create an Oracle Always-Free Cloud Account

1. Go to [https://www.oracle.com/cloud/free/](https://www.oracle.com/cloud/free/)
2. Click "Start for free"
3. Populate the forms and create an account.
4. Once your account is created, [log in](https://www.oracle.com/cloud/sign-in.html) and go to the dashboard.  
   ![OCI Cloud Dashboard](images/cloudDashboard.png)

## Create a [compartment](https://docs.cloud.oracle.com/iaas/Content/Identity/Tasks/managingcompartments.htm)  

Compartments are useful when you want to organize and isolate your cloud resources.  Create a compartment for the objects used in this lab.

1. Click the menu icon in the upper left corner.
1. Scroll to the bottom, under Identity, click "Compartments".
   ![Compartment menu item](images/compartmentMenu.png)
1. Click "Create Compartment".  
   ![Create compartment button](images/createCompartment.png)
1. Populate the Name and Description.
1. Leave the parent compartment set to (root).
1. Click "Create Compartment"  
   ![Create compartment form](images/compartmentForm.png)
1. Click the "Oracle Cloud" logo to return to the dashboard.

## Create an ATP instance

You will need a database to complete the exercises.  An Oracle Autonomous Database handles a lot of the background admin tasks for you so you can focus on your project.

1. Click "Create an ATP database" in the Autonomous Transaction Processing box.  
   ![OCI Cloud Dashboard](images/cloudDashboard.png)
1. Choose your new compartment.
1. Enter `MyAtpDb` in Display name
1. Enter  `MyAtpDb` in Database name
1. Make sure "Transaction Processing" is selected.
1. Make sure "Shared Infrastructure" is selected.  
   ![Create ATP form 1](images/createATPForm1.png)
1. Scroll down to "Configure the database" select "Show only Always Free configuration options"  
   ![Create ATP form 2](images/createATPForm2.png)
1. Scroll down to "Create administrator credentials".  Enter and confirm the ADMIN password.  
   **Note:**  the Admin account is the top level user for your new database.  Create a strong password and keep it secure.
1. Scroll to the bottom and click "Create Autonomous Database".  
   ![Create ATP form 3](images/createATPForm3.png)  
   You will receive an email when your new ATP Database instance has been provisioned.
1. Locate your new database's OCID and click Copy.
   ![Copy Database OCID](images/dbOcid.png)  

## Cloud Shell

Click on the Cloud Shell icon.  
![Open Cloud Shell](images/cloudShell.png)  
This will open a preconfigured VM that you will use to access and setup your project.

### Nano Save and Exit

* The instructions in this lab use nano to edit files.  (You are welcome to use another editor if you prefer.)  
To close and save a file in nano do the following.
   1. Ctrl-X
   1. Y
   1. Enter
* When using nano in the cloud shell use `Ctrl-Shift-V` or `Shift-Insert` to paste, rather than `Ctrl-V`.
* At times you will see the following instruction link  

   [SaveLink]: index.html?lab=lab-1-cloud-infrastructure-configuration#NanoSaveandExit "Save and exit Nano&#10;Ctrl-X&#10;Y&#10;Enter)  

   [Git add/commit/push][GitLink]  

   [GitLink]: index.html?lab=lab-1-cloud-infrastructure-configuration#GitAddCommitPush "Run the following in your Cloud Shell &#40;ssh&#41;&#10;cd ~/cicd-tools-db-dev&#10;git add .&#10;git commit -m&quot;your commit message&quot;&#10;git push)  

   You may click the link to return to the below instructions, if you would like to copy and paste the commands.  
   You may also hover the link to see the commands displayed in the popup text.

### Git Add Commit Push

   Run the following in your **Cloud Shell(ssh)**

   ```bash
   cd ~/cicd-tools-db-dev
   git add .
   git commit -m"your commit message"
   git push
   ```

### Create an environment variable for your Database OCID

Once the Cloud Shell is running, create an environment variable for your Database OCID you copied above.

```bash
export DB_OCID=<pasteYourOCIDhere>
```

![Create environment variable](images/envVarDbOcid.png)  

The Oracle Autonomous Database uses an extra level of security in the form of a wallet containing access keys for your new Database.  

Once your ATP Database status is Available (the yellow box turns green) you can download the wallet inside the Cloud Shell using the pre-configured [OCI-CLI](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/cliconcepts.htm).

You should change the password value in this command to something more secure.  
**Note:** This password is for the .zip file, not your database.

In your **Cloud Shell**  
Enter the following.

```bash
oci db autonomous-database generate-wallet --autonomous-database-id ${DB_OCID} --password Pw4ZipFile --file ~/Wallet_MyAtpDb.zip
```

### Open SQLDeveloper Web

Later, after everything is setup, you will use SQLDeveloper Web to access your database.

1. Click Tools
1. In the Database Actions box, click the "Open Database Actions" button  
   ![Open SQL Developer Web](images/OpenSqlDevWeb.png)  
1. Log in as admin using the admin password you created for your Database.  
   ![Login to SQL Developer Web](images/sqlDevWebLogon.png)  
1. Under Development, click the SQL box.  
   This will open SQL Developer Web in a new browser tab.
   ![Login to SQL Developer Web](images/sqlDevWebLaunch.png)  
1. Switch back to the Oracle Cloud browser tab.
   ![SQL Developer Web](images/sqlDevWeb.png)  

Click the "Oracle Cloud" logo on the left of the menu bar to return to the dashboard.

## Create a Compute instance

An Oracle Compute instance is a Cloud VM that you will use to install and run all of the software for the lab.  

1. Click "Create a VM instance" in the Compute box.
   ![OCI Cloud Dashboard](images/cloudDashboard.png)
1. Populate the name or keep the default.
   ![Create Compute instance form](images/createComputeForm1.png)
1. Scroll down the the "Add SSH keys" section.
1. Select "Paste public keys".
1. In your **Cloud Shell**
   1. Generate a new RSA key pair.

      ```bash
      ssh-keygen -t rsa -N "" -b 2048 -C "cloud_shell" -f ~/.ssh/id_rsa
      ```

   1. Display the public key and copy it.

      ```bash
      cat ~/.ssh/id_rsa.pub
      ```

1. In the **Create Compute form**, paste the public key in the SSH KEYS box.
   ![Create Compute instance form](images/createComputeForm2.png)
   If you intend to SSH into your compute instance from any other machine, you may click the "+ Another Key" button and enter the public key for that machine.  
   (you may also want to save a copy of the Cloud Shell private key '~/.ssh/id_rsa' on your local machine.)  
   **DO NOT SHARE your private key**.  This key allows access to your compute instance.
1. Click "Create".
1. Once the Compute instance is Running, locate the Public IP Address and click Copy.  
Keep this IP address handy, it will be used throughout the lab and referred to as \<YourPublicIP>.
1. In your **Cloud Shell**  
   Create an environment variable to store the IP.

   ```bash
   export COMPUTE_IP=<YourPublicIP>
   ```

   ![Save Public IP](images/saveComputeIp.png)

1. Next, you will open ports 8080 and 8000 in your cloud Virtual Network (VNIC).
   1. Click "Public Subnet"
      ![Click Public Subnet](images/openPort1.png)
   1. Click the Security List name.  
      ![Open Security List](images/openPort2.png)  
   1. Click Add Ingress Rule.  
      ![Add Ingress Rule](images/openPort3.png)  
   1. In the SOURCE CIDR box enter

      ```text
      0.0.0.0/0
      ```

   1. In the DESTINATION PORT RANGE box enter

      ```text
      8080
      ```

   1. Click Add Ingress Rule.
      ![Add Ingress Rule](images/openPort4.png)
   1. Repeat for 'DESTINATION PORT RANGE' 8000.

   **Be Aware**  
   **This will open ports 8080 8000 for any instance using the default security list**  

1. In your **Cloud Shell**  
   Use SCP to upload the wallet .zip file (downloaded earlier) to new Compute instance.

   ```bash
   scp Wallet_MyAtpDb.zip opc@${COMPUTE_IP}:/home/opc/
   ```

   ![Upload wallet](images/scpWallet.png)

1. Maximize the Cloud Shell.
1. Use SSH to access your Compute instance from the Cloud Shell.

   ```bash
   ssh opc@${COMPUTE_IP}
   ```

   ![SSH to Compute instance](images/sshToCompute.png)  
**Note:** After this, any steps to be completed in your **Cloud Shell**, **Cloud Shell (ssh)** or in your **Compute Instance**, will be completed in the ssh session.
You can tell which instance you are connected to by looking at the prompt.

* `<user>@cloudshell:` is in the cloud shell but **not** the ssh session.
* `[opc@<yourComputeName> <dir>]` is in the ssh session.  
If you lose your ssh connection, return to the above step and reconnect.  
Feel free to use your own ssh client if you prefer.

## Setup your Compute Instance

Add the software needed for the lab.

In your **Cloud Shell (ssh)**

### Install Git

```bash
sudo yum install -y git
git --version
```

### Setup the Database Wallet

```bash
sudo mkdir -p /opt/oracle/wallet
sudo mv Wallet_MyAtpDb.zip /opt/oracle/wallet/
sudo unzip /opt/oracle/wallet/Wallet_MyAtpDb.zip -d /opt/oracle/wallet/
echo 'export TNS_ADMIN=/opt/oracle/wallet/' >> ~/.bashrc
source ~/.bashrc
```

Newer versions of Oracles ojdbc driver make it much easier to access a database using the extra wallet security.  To enable these features, edit the wallet/ojdbc.properties file.

```bash
sudo nano /opt/oracle/wallet/ojdbc.properties
```

1. Comment line 2
1. Un-comment the last 4 lines that start with '#javax.net.ssl'
1. Replace <password_from_console> with the password you used when you downloaded the wallet .zip file.  
   (The password must be changed on in TWO places in the file)  

   ```text
   # Connection property while using Oracle wallets.
   # oracle.net.wallet_location=(SOURCE=(METHOD=FILE)(METHOD_DATA=(DIRECTORY=${TNS_ADMIN})))
   # FOLLOW THESE STEPS FOR USING JKS
   # (1) Uncomment the following properties to use JKS.
   # (2) Comment out the oracle.net.wallet_location property above
   # (3) Set the correct password for both trustStorePassword and keyStorePassword.
   # It's the password you specified when downloading the wallet from OCI Console or the Service Console.
   javax.net.ssl.trustStore=${TNS_ADMIN}/truststore.jks
   javax.net.ssl.trustStorePassword=Pw4ZipFile
   javax.net.ssl.keyStore=${TNS_ADMIN}/keystore.jks
   javax.net.ssl.keyStorePassword=Pw4ZipFile
   ```

1. [Save the file][SaveLink]

### Download the Oracle Database Driver ojdbc8.jar

```bash
sudo wget https://repo1.maven.org/maven2/com/oracle/ojdbc/ojdbc8/19.3.0.0/ojdbc8-19.3.0.0.jar -O /opt/oracle/ojdbc8.jar
```

### Install Java 8

```bash
sudo yum install -y --enablerepo=ol7_ociyum_config oci-included-release-el7
sudo yum install -y jdk1.8
java -version
```

### Install [SQLcl](https://www.oracle.com/database/technologies/appdev/sqlcl.html)

You will use SQLcl to execute some setup scripts from your Compute instance.  

```bash
sudo yum install -y sqlcl
alias sql="/opt/oracle/sqlcl/bin/sql"
sql -v
```

### Install [utPLSQL](https://github.com/utPLSQL/utPLSQL)

Download the utPLSQL testing framework

```bash
curl -LOk $(curl --silent https://api.github.com/repos/utPLSQL/utPLSQL/releases/latest | awk '/browser_download_url/ { print $2 }' | grep ".tar.gz\"" | sed 's/"//g')
```

Extract the downloaded "tar.gz" file

```bash
tar xvzf utPLSQL.tar.gz
```

Use SQLcl to install utPLSQL  
(Replace 'n0tMyPassword' with your Database Admin Password  
'XNtxj8eEgA6X6b6f' is the default utPLSQL password.  You should change it.)

```bash
sql admin/n0tMyPassword@MyAtpDb_TP @utPLSQL/source/install_headless_with_trigger.sql ut3 XNtxj8eEgA6X6b6f DATA
```

### Install [utPLSQL-cli](https://github.com/utPLSQL/utPLSQL-cli)

utPLSQL-cli is a Java command-line client for utPLSQL v3  

```bash
curl -LOk $(curl --silent https://api.github.com/repos/utPLSQL/utPLSQL-cli/releases/latest | awk '/browser_download_url/ { print $2 }' | grep ".zip\"" | sed 's/"//g')
sudo unzip utPLSQL-cli.zip -d /opt/ && sudo chmod -R u+x /opt/utPLSQL-cli
sudo cp /opt/oracle/ojdbc8.jar /opt/utPLSQL-cli/lib
```

### Install [Liquibase](https://github.com/liquibase/liquibase)

Liquibase is a schema migration tool you will use to make changes to your database

```bash
wget https://github.com/liquibase/liquibase/releases/download/liquibase-parent-3.6.3/liquibase-3.6.3-bin.tar.gz
sudo mkdir /opt/liquibase
sudo tar xvzf liquibase-3.6.3-bin.tar.gz -C /opt/liquibase/
echo 'export PATH=$PATH:/opt/liquibase' >> ~/.bashrc
source ~/.bashrc
liquibase --version
```

### Install [Jenkins](https://www.jenkins.io/)

Jenkins is an automation server you will use to build and deploy your project

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key

sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key

sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install -y jenkins

sudo systemctl start jenkins
sudo systemctl status jenkins
sudo systemctl enable jenkins
```

### Open ports 8080 and 8000 in your compute instance's internal firewall

```bash
sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp
sudo firewall-cmd --permanent --zone=public --add-port=8000/tcp
sudo firewall-cmd --reload
```

### Generate an rsa key pair in your compute instance

This rsa key pair will be used to access your GitHub repository from the compute instance.  
(This is a different key than the one used in your Cloud Shell to access this compute instance.)

1. Generate a new RSA key pair

   ```bash
   ssh-keygen -t rsa -N "" -b 2048 -C "CiCd-Compute-Instance" -f ~/.ssh/id_rsa
   ```

1. Display the public key, copy it and save it for the GitHub step below.

   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

### Setup GitHub repository

1. **In your browser**  
   Go to [https://github.com/OsBlaineOra/cicd-tools-db-dev](https://github.com/OsBlaineOra/cicd-tools-db-dev)
1. Click the 'Fork' button  
   ![Fork the repo](images/GitHub-Fork.png)  
1. **In your new repository**  
   Click Settings  
   ![Open Settings](images/GitHub-Settings.png)  
1. Add your public key
   1. On the left, click 'Deploy keys'
   1. Click the 'Add deploy key' button  
   ![Add Deploy Key](images/GitHub-DeployKey.png)  
   1. Enter a title for your key 'HoL Compute Instance'
   1. In the 'Key' field, past the public key you generated for this compute instance.
   1. Check 'Allow write access'
   1. Click 'Add key'  
   ![Add Deploy Key](images/GitHub-AddKey.png)  
1. Add a Webhook
   1. On the left, click 'Webhooks'
   1. Click the Add webhook button  
   ![Add webhook button](images/GitHub-Webhooks.png)  
   (You may be asked to re-enter your GitHub password)
   1. Use your Compute instance public IP to populate the Payload URL

      ```text
      http://<YourPublicIP>:8080/github-webhook/
      ```

   1. Click the Add webhook button. (Ignore the error for now)  
   ![Add webhook button](images/GitHub-AddWebhook.png)  
1. Click the 'Code' tab
1. Click the 'Clone or download' button
1. If it doesn't say 'Clone with SSH' click the 'Use SSH' link
1. Click the button with a clipboard icon next to the clone string to copy it  
   ![Clone with SSH](images/GitHub-CloneURL.png)  
1. In your **Cloud Shell(ssh)**  
   Clone your new Git repository

   ```bash
   git clone <The SSH string copied above>
   cd cicd-tools-db-dev
   ```

1. Use SQLcl to create the database schemas  
   (Replace 'n0tMyPassword' with your Database Admin Password)  

   ```bash
   sql admin/n0tMyPassword@MyAtpDb_TP @create_schema.sql
   ```  

   You should see the following if the script was successful.  

   ```text
    Connected.

                        DID_IT_WORK
    _______________________________
    HOL_DEV created successfully

    Connected.

                        DID_IT_WORK
    ________________________________
    HOL_TEST created successfully
   ```

[Next Section](index.html?lab=lab-2-jenkins-configuration)
