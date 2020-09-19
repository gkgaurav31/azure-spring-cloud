---
layout: post
title: Encryption/Decryption of Config Server properties
date: 2020-09-20 01:11 +0530
---

There are two types of Encryptions primarily which we can use:
- Symmetric Encryption: Same key will be used to encrypt and decrypt. 
- Asymmetric Encryption: uses ___keytool___ to store the public and private keys

In this blog, we will look into Symmetric Encryption.

To start with, you would need to enable __Java Cryptographic Extension__. Some versions already have it enabled. If not, you can follow these steps:

- Download the JARs from here:
[JCE](https://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html)

- Copy the JARs named local_policy.jar and US_export_policy.jar to your JAVA_HOME/jre/lib/security

__NOTE:__ Ensure that your Java Project is using the version of JDK where we enabled JCE.

Now, follow these steps to Encrypt/Decrypt the properties:

- In your Config Server Microservice, create a __bootstrap.properties__ file and add the following:

```properties
encrypt.key=somerandomkey
```

This will be our Symmetric Key.

- Test Encryption:

Send a HTTP POST request to the /encrypt API of the Config Server. Example:

```bash
curl --location --request POST "http://localhost:8888/encrypt" --header "Content-Type: text/plain" --data-raw "ThisIsASecret"

#RESPONSE
988e6846913b21ace9c47d1e16992178b9d1ce2bb563ccf62b02fb17b360f838

```

- Test Decryption:

```bash
curl --location --request POST "http://localhost:8888/decrypt" --header 'Content-Type: text/plain' --data-raw "f44026df410d7267ccbe809545c8d7c65f4d93463e73d244512d15ca9bd43b7f"

#RESPONSE
ThisIsASecret
```

So, Config Server is able to decrypt using the Encryption key provided earlier. Now, we just need to add the encrypted data in our Microservice Apps and annotate it in a way that the application knows that the value in encrypted. This is how you would do it:

- In any of your Microservice Application's __application.properties__ file, add the following:

```properties
message={cipher}f44026df410d7267ccbe809545c8d7c65f4d93463e73d244512d15ca9bd43b7f
```

Your application can now use this property which is encrypted. 

That's it. :v:
