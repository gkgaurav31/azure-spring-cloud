---
layout: post
title: Azure Spring Cloud - Config Server with SSH Authentication
date: 2020-09-19 21:32 +0530
---

Here are the steps you can follow to configure the Config Server on Azure Spring Cloud which uses SSH authentication to connect to a git repository on GitHub.

#### Create your keys using the following command on your terminal

```bash
ssh-keygen -m PEM -t rsa -b 4096 -C "test@mymail.com"
```
This will create a public key (.pub) and a private key.

#### Copy the public key on GitHub

Go to your GitHub account -> Settings -> SSH and GPG keys
Click on __New SSH key__ and copy the public key here.

#### Configure the Config Server on Azure Spring Cloud

Go to the Azure Spring Cloud Resource -> Config Server
Specify the GitHub URL, Label etc. and choose SSH as the Authentication protocol.
Copy your private key in the text box and save.

### (Optional) To ensure that the key works, you can test it on the local system as well. 

Reference: [Clone GitHub Repository using SSH](https://www.toolsqa.com/git/clone-repository-using-ssh/)

```bash
eval "$(ssh-agent -s)"

ssh-add <private key>

git clone <GIT_URL>
#Example of the URL: git@github.com:gkgaurav31/testconfig.git
```

You should now be able to access your private GitHub Repository via SSH from Azure Spring Cloud. :v:

 
