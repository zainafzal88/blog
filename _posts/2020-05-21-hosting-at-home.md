---
layout: post
title:  "Turning Raspberry Pi into Web Server and Building CI/CD pipeline"
description: Turn your Raspberry Pi into a server and host your website/blog from home with custom domain using CI/CD pipeline for fast deployments
date:   2020-05-21
---

I have always been into self development and learning new technologies. So, I asked my brain, why not use these interesting times of isolation to bump it up. Within a millisecond, my brain replied , Hell Yeahhhhh. That’s where all the action began!

I was invited to a small community group called “A Slice of Pi” looking for enthusiasts that want to push their knowledge and come up with some projects to help those get there head around the Raspberry Pi.

Via recommendation from the group I ordered a Raspberry Pi Zero. A few hours later, I recalled that I already have Raspberry Pi 3 that someone lent to me a while ago but was sitting unused. A huge smile came on my face just because I didn’t have to wait for the new one to come in the post.  The idea from the group was to setup a server at home “just because” is as a learning experience.

Full Disclosure: The idea was my mentor’s ( Paul Kukiel[link to linked in] ) and he held numerous hands-on sessions (over zoom) with me and few other developer friends to teach us how to set up the server. After, that I got busy with other life’s stuff and couldn’t come back to it.

At the end, our architecture will look like this which includes your website hosted on Raspberry Pi and accessible via the public internet.

![](/assets/images/2020-05-21/piarchitecture.png)

Let’s go on the mission together.

## Get Equipped:

You will need the following

* Micro USB

* Power cable

* HDMI cable

* Micro-SD card (32GB recommended)

* Keyboard

* Raspberry Pi

* Monitor with HDMI input

* Router connect to WiFi

* Mouse

* SD Card Adapter

## Prepare the SD Card

Hope you have a SD card reader. Insert the microSD card in the adapter and then in your laptop/PC and format it completely.

Now let us install the operating system(OS) for Raspberry Pi from [here](https://www.balena.io/etcher/). Once done, burn it on your microSD card using any of your favourite tool(mine is [BalenaEtcher](https://www.balena.io/etcher/). If you don’t know how to burn, follow _Burn OS image to microSD card_ section.

If you have an existing SD card with data that you don’t use, you can also format it by following the instructions below

**Linux OS GUI:**

1. Insert your microSD card into a SD adapter
2. Right click on the SD card and click _Properties_
3. Click on _Open Disk_
4. Select the Parttition you want to format.
5. Click the _Setting_ icon(2 wheels)
6. Select _Format Parition_
7. Choose _Erase_
8. Choose _For all devices using……_

The time taken for formatting will vary on how much data you have. Meanwhile, grab a coffee.

## Burn OS image to microSD card

1.  Unzip the zipped Raspbian OS image you downloaded. 
2.  Open BalenaEtcher.
3.  Select the OS image you want to burn on the SD card
4.  Select the target to be SD Card.
5.  Click _Flash_

## Installing OS onto microSD Card

Insert the microSD card into Pi and turn the Pi on. It boot and install the OS by itself.

Error Tip - If you insert the SD card and the monitors screen stay black. 
1.  Take the SD card out, 
2.  Pull the black part(shown below) up gently until it stops itself
3.  Push it back in.
4.  Insert the SD card in

![](/assets/images/2020-05-21/pi-screen-black-error.jpeg)

##  Connect Pi to the Internet

Open terminal by pressing. CTRL + ALT + T and switch to root user by running

```
sudo su
```

where

`su` -  stand for Super User

Then, run

```
raspi-config
```

This will open Raspberry Pi configuration tool

Go to `Network Option` > `WiFi` > `[Enter your network name]` > Click `OK` > `[Enter you Network Password]` > Cilck `OK`

Now connect to the internet using your Network name in the GUI.
 
##  SSH into Pi

You can use Pi from Linux, Mac or another Pi. I will tell you about Mac in this blog.

First, we need to know the IP address of the Pi. To find this, type hostname -I from your Pi terminal. If you are running Pi  headless (without a monitor), you can find the IP address from the the device list on your router. 

Secondly, install ufw (firewall) on Pi by running 
```
sudo apt-get install ufw
```
In order to use `ufw` we need to enable it to by running
```
ufw enable
```
To allow SSH (port 22) inbound connection, run
```
sudo ufw allow 22
```
By default, ufw denies all incoming connection. We just have one more step to take and that is enabling SSH from the rasp-config
Open terminal up again, switch to super user by:
```
sudo su
```
Then, run 

```
raspi-config
```

Go to `Interface Option` and enable SSH.

Now to SSH, open up the Terminal on your client and type

```
ssh [piUser]@[Pi_IP_ADDRESS]
```

**_Security tip: Make sure to change the default password for security reason._**

**Code explanation:**

`ssh` -  a keyword used for sshing

`[piUser]` - Whatever your username is (don’t include square brackets)

`[Pi_IP_ADDRESS]`- IP address of your Pi (don’t include square brackets)

For example : 
```
ssh pi@111.111.1.1
```

Upon pressing enter you’ll be prompted for password and that’s it, you’re in.


## Setting up SSH keys

1.  On your client, generate ssh key by running `ssh-keygen`
2.  Provide answers to the questions asked.
3.  Two keys will be generated, one public(with .pub extension) and one private(with no extension)
4.  Copy the public key to Pi’s .`.ssh` directory (if none, create one).

Now to SSH, open up the Terminal on your client and type 
```
ssh [piUser]@[Pi_IP_ADDRESS]
```

Note: the default username is `pi` and password is `raspberry` . Once you’re in, you can change your password.

OPTIONAL -  If you don’t want to enter the password every time you ssh into Pi, rename the public key on Pi to `authorized_keys`.

## Install Nginx onto Pi

First thing is first, make sure all the packages are updated by running

```
sudo apt update
```

Now, install nginx by running:

```
sudo apt install nginx
```

**Code explanation:**`

`sudo` - Get administrative rights

`apt` - Packaging system of Ubuntu

`install` -  Keyword to install

`nginx` - Install the service named

Once that’s installed successfully, we need to enable the firewall(`ufw`):
```
ufw enable
```

Now the firewall needs to be adjusted for the webserver. For that we need to allow traffic on port 80 by running:

```
sudo ufw allow 80
```

Then verify the change by running:

```
sudo ufw status
```

You should see HTTP traffic is allowed.

## Testing Web Server

By now, the web server should already be up and running. To check run:

```
sudo systemctl status nginx
```

The output should display _active (running)_ in green colour

This being said, the best way of testing is by requesting a page in the following way:

1.  `cd /var/www/html` - Navigate to html folder
2.  `touch index.html` - Create a file called index.html
3.  `vim index.html` - Open the file (if `vim` does work install it)
4.  Insert basic html to test the server work
5.  Go to your browser 
6.  Type in the IP address of the pi ( http://111.111.1.1 )

If all went well , you have your own server at home displaying the page you created. Your Pi is ready to host.

## Uploading to Pi via CI/CD Pipeline

Create a CI/CD pipeline in AWS with the source, Github and use CodeBuild to build the project. So, when a commit is made to the Github, that will automatically trigger the pipeline.

### Step 1:

Login to AWS  > Services 

![](/assets/images/2020-05-21/services.png)

### Step 2:

Go to AWS Code Pipeline

![](/assets/images/2020-05-21/pipeline.png)

## Step 3:

Create a Pipeline

![](/assets/images/2020-05-21/create-pipeline.png)

### Step 4:

Fill in the details

Make sure the role has read access to S3, KMS and SSM for the below reasons:

S3 will be storing our encrypted SSH Private keys and we need to get the private key from S3

KMS will be decrypting the ssh keys fetch from S3

SSM will store our domain name

Click Next

![](/assets/images/2020-05-21/pipeline-details.png)

### Step 5:

Select Github as a Source Stage. Once selected, click on button that say’s “Connect to Github“

![](/assets/images/2020-05-21/source-stage.png)

Once, connected, fill in the repository details

![](/assets/images/2020-05-21/github-details.png)

Click Next.

### Step 6: 

Choose AWS CodeBuild as a Build provider from the dropdown and fill in the details. If you haven’t created a build project yet, click on the Create Project (follow steps from 6.1). If you already have(skip to step 7), it should in the dropdown of “Project Name“.

Once done, click Next

![](/assets/images/2020-05-21/codebuild-details.png)

### Step 6.1

Click build project:

![](/assets/images/2020-05-21/create-build-project.png)

### Step 6.2

Name the project and scroll down

![](/assets/images/2020-05-21/build-project-details.png)

### Step 6.3:

Choose a Source provider as Github from the dropdown and Connect to your GitHub account. Select whether the repo you are connecting in public or it’s your own. Then Scroll down.

![](/assets/images/2020-05-21/build-source-details.png)

### Step 6.4:

Fill in environment details and scroll down

![](/assets/images/2020-05-21/build-environment-details.png)

### Step 6.5

Add a Service Role

![](/assets/images/2020-05-21/role-name.png)

### Step 6.6

Choose _Use a buildspec file_(see the code after the below image for your buildspec file). This buildspec file must be in the root of your project directory

![](/assets/images/2020-05-21/buildspec.png)

The below is the code is the buildspec.yml

version: 0.1

env:
  parameter-store:
    PI_DNS: /codebuild/piRoarcoder

```
phases:
  install:
    commands:
      - gem install jekyll bundler jekyll-paginate jekyll-sitemap jekyll-gist
  build:
    commands:
      - echo "******** Building Jekyll site ********"
      - jekyll build
      - echo "******** Get ssh key from S3 ********"
      - aws s3 cp s3://ssh-keys-zain/encrypted_id_rsa $HOME/encrypted_id_rsa
      - aws kms decrypt --ciphertext-blob fileb://$HOME/encrypted_id_rsa --output text --query Plaintext | base64 --decode > $HOME/decrypted_id_rsa
      - chmod 400 $HOME/decrypted_id_rsa
      - echo "******** Upload to Raspberry Pi ********"
      - scp -r -oStrictHostKeyChecking=no -i $HOME/decrypted_id_rsa ./* pi@$PI_DNS:/var/www/roarcoder.dev/html/
      - rm $HOME/decrypted_id_rsa
      - rm $HOME/encrypted_id_rsa`
```

**Code Explanation:**

`version: 0.1` - Represents what version of buildspec is being used.

`env` - Represents information for custom environment variables

`parameter-store` - To retrieve custom environment variable from AWS System Manager Parameter Store (SSM). Store values as key: value pairs

`BUCKET` - Denotes the use of S3 bucket

`/codebuild/piRoarcoder` - Naming convention to name parameters in SSM such as /{serviceName}/{parameterName}

`phases` - CodeBuild has different phases however, we only use install and build. Each of these phases contain their respective commands to run.

`install` - Sections runs commands to install all the necessary tools needed to build installed for the project

`gem install jekyll bundler jekyll-paginate jekyll-sitemap jekyll-gist` - Install Jekyll and all its dependencies as I am using Jekyll static website generator

`build` - Section that runs commands required to build the project

`jekyll build` - Outputs the production ready version of the application in a separate folder _site which doesn’t contain source code.

`aws s3 cp s3://ssh-keys-zain/encrypted_id_rsa $HOME/encrypted_id_rsa` - Getting my encrypted private ssh key from my S3 bucket storing it in the container

`aws kms decrypt --ciphertext-blob fileb://$HOME/encrypted_id_rsa --output text --query Plaintext | base64 --decode > $HOME/decrypted_id_rsa` - Decrypting the encrypting key to use it

`chmod 400 $HOME/decrypted_id_rsa` - Giving permission for the key to be readable

`scp -r -oStrictHostKeyChecking=no -i $HOME/decrypted_id_rsa ./* pi@$PI_DNS:/var/www/roarcoder.dev/html/` - Copy all contents from the current directory securely to Pi’s html directly of my virtual host.

`rm $HOME/decrypted_id_rsa` - Remove the decrypted key private key from the container

`rm $HOME/encrypted_id_rsa` - Remove the encrypted key private key from the container

### Step 6.7

Choose “No Artifacts” from the dropdown.

Artifacts is the output produced by the build. We don’t want to save them within AWS, hence we choose “No Artifacts”. Our command line in buildspec file will transfer the file straight onto Pi.

Scroll down

![](/assets/images/2020-05-21/build-artifacts.png)

### Step 6.8: 

Leave everything default and Click “Create Build Project“

![](/assets/images/2020-05-21/build-project-logs.png)

### Step 7:

We won’t be using anything for the deploy stage, so click “Skip deploy stage”. 

![](/assets/images/2020-05-21/deploy-stage.png)

You will be asked to confirm it, click Skip

![](/assets/images/2020-05-21/skip-deployment-stage.png)

### Step 8:

Review the pipeline, scroll to the bottom of the page, and click “Create Pipeline”

![](/assets/images/2020-05-21/review-pipeline.png)

### Step 9:

Your pipeline will be and the execution will be started automatically. (i have had to stop my execution as I just wanted to show you.

![](/assets/images/2020-05-21/pipeline-execution.png)

Now as soon as you commit a change to your Github repository, this pipeline will be triggered automatically.

Securely copy (`scp`) the directory’s file from your computer(client) to your pi(host) by the running from the location of your private key:

Assumption: You already know how to set ssh keys between client and host

```
scp -i your-private-key /path/to/website/folder/* pi@111.111.1.1:/var/www/html
```

where:

`scp` -  Secure copy
`-i` - To specify the private key
`your-private-key` - name of your private key
`/path/to/website/folder` - Your website’s folder’s path

`*` -  All the files within the folder

`pi@111.111.1.1`  - user and Ip address of the Pi

`:` - ip address and path separator

`/var/www/html` - Path to HTML directory where the content will be uploaded

If everything went smooth, you should see your site on the IP address 111.111.1.1 (this will be your own ip address of the ip)

##  Setting SSL Certificates in Pi

We will use Certbot to get our SSL certificates. If you don’t know what Certbot

> Certbot is a free, open source software tool for automatically using Let’s Encrypt certificates on manually-administrated websites to enable HTTPS.

1.  Ssh into your pi
2.  Install certbot uby running
```
apt-get install certbot
```
3. Get the certificate
```
certbot certonly --standalone -d [your-domain-name]
```

**Code explanation:**

`certbot` -   service that generates certificates

`certonly` - Only get the certificates, nothing else. As certbot can install the certificates itself but i don’t want it to make changes to my config files to have SSL enabled. I want to be make config changes myself.

`--standalone` -  Certbot will use its own web server to pass the process of verification of the domain

`-d` -  argument to specifies the domain name 

`[your-domain-name]` - Specify your domain name. for example: example.com or www.example.com without the square brackets

4. Answer all the questions asked

5. Once generated, it will show a section of “IMPORTANT NOTES” and do as it says. 

6. Copy and paste the paths of fullchain.pem and privkey.pem. You will need it shortly.

7. Go to /etc/nginx/sites-available and copy the default page and rename it as your domain name

8. Go to sites-available folder 

```
cd /etc/nginx/sites-available
```

9. Open default page

```
vi default
```

10. Look for `listen 80;` and `server_name [some-domain]` and below them enter:
```
listen 443 ssl default_server;
ssl_certificate  [copy and past the line from step 6 that has fullchain.pem at the end]
ssl_certificate_key [copy and past the line from step 6 that has privkey.pem at the end]

root /var/www/[folder-where-you-uploaded-your-site]
```
11. Save and quit the file by press `ESC` and typing `:wq`
12. Check if you synatx error in the config files by running
```
nginx -t
```

**Code Explanation**

`nginx` - server

`-t` - test

You shouldn’t receive any errors

14. Reload Nginx

```
sudo systemctl reload nginx
```

where

`systemctl` - controls the service manager and systemd system

`reload` -  Doesn't stop the server (used to avoid downtime)

`nginx` - server name 

15. Enable HTTPS (port 443) on Pi by running:
```
sudo ufw allow 443
```

## Access Pi Over The Public Internet

As your Pi is in your private network, you can’t connect to it from the public internet with the current configuration. To do this, the router must be configured for Port Forwarding. 

Port Forwarding allows you to forward the inbound traffic to your private network on a specific port connecting them to the local IP address of your Pi. Every router is different. 

1. Go to your router IP address

2. Login with admin credentials

3. Go to your Dynamic Domain Name Server (DDNS) setting and enable it. For me it was in _Internet_ → _Internet Services_

![](/assets/images/2020-05-21/enable-ddns.png)

4. Depending on your router, you’ll have a provider. I had DynDns.org and No-IP.com. I chose DynDns.org as my router wasn’t supported by No-IP.com

5. Sign up to service and enter the details in the DDNS setting like shown above in the screenshot.

6. Click Save.

7. Go to Port Forwarding and enable the following ports

    * Port 22 for SSH

    * Port 80 for HTTP

    * Port 443 for HTTPS

Below is how to forward Port 22, the others are similar

![](/assets/images/2020-05-21/port-mapping-ssh.png)

`Mapping Name` : Give your port a name

`Application` : Protocol name (SecureShellServer for SSH, SecureWebServer for HTTPS, Web Server for HTTP)

`Internal Host` : The device name (your Pi)

Then, click _Save_.

At the end, your Port Mapping section should look like this

![](/assets/images/2020-05-21/port-mapping.png)

That’t it. Now you can access your website/blog through public internet.

To test it, connect to your **Home Network WIFI** and enter your **Pi’s private IP** address in the browser.

To test from the public internet, disconnect from your home WIFI and enter the `[hostname].[domainName]` in the URL and you should see the `index.html` you uploaded to Pi.

If it didn’t work, feel free to contact me from my [website](https://roarcoder.dev) . I’ll be happy to help :)