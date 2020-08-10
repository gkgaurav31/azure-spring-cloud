---
layout: post
title: Access Private GitHub Repository via SSH from Spring Cloud Config
date: 2020-08-10 13:22 +0530
---

Username/Passwords are nice. But SSH is better, since it avoids having to re-type and remember your passwords. In this blog, I would like to share the steps on how you can configure SSH access to your GitHub repository from a Spring Boot application which runs a Config Server.

#### CREATE A KEYPAIR USING SSH-KEYGEN COMMAND

```ssh
ssh-keygen -m PEM -t rsa -b 4096 -C "test@gmail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\gauk/.ssh/id_rsa): C:\Users\gauk/.ssh/id_rsa_spring
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\gauk/.ssh/id_rsa_spring.
Your public key has been saved in C:\Users\gauk/.ssh/id_rsa_spring.pub.
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx gkgaurav31@gmail.com
```

This will create a public and a private key under C:\Users\gauk/.ssh/.

#### COPY THE PUBLIC KEY TO GITHUB

Copy the contents from C:\Users\gauk/.ssh/id_rsa_spring.pub (Public Key) and add it to GitHub:

![ssh-key-github](/assets/ssh-key-github.png)

### CREATE A SPRING BOOT PROJECT WITH THE CONFIG SERVER DEPENDENCY:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

#### COPY THE PUBLIC KEY TO GITHUB

Now, you would need to add the Private Key (C:\Users\gauk/.ssh/id_rsa_spring) in your __application.yml__. Here is a sample which you can use:

```xml
spring:
  cloud:
    config:
      server:
        git:
          uri: git@github.com:gkgaurav31/myprivaterepo.git
          ignoreLocalSshSettings: true
          #host-key: someHostKey
          #host-key-algorithm: ssh-rsa
          skip-ssl-validation: true
          passphrase: mypassphrase
          private-key: |
                    -----BEGIN RSA PRIVATE KEY-----
                    Proc-Type: 4,ENCRYPTED
                    DEK-Info: AES-128-CBC,D3BEFE2A26EFDACA19C236BC3EC8B00A
                    
                    xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
                    xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
                    xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
                    xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
                    xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
                    xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
                    -----END RSA PRIVATE KEY-----
```



#### REFERENCES

- [Spring Documentation](https://cloud.spring.io/spring-cloud-static/spring-cloud-config/2.0.0.M6/single/spring-cloud-config.html#_git_ssh_configuration_using_properties)
- [GitHub Documentation](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
- [Invalid SSH key on a MacOS Mojave](https://skryvets.com/blog/2019/05/27/solved-issue-spring-cloud-config-server-private-ssh-key/)
- [Using SSH on GitHub](https://www.youtube.com/watch?v=WgZIv5HI44o)