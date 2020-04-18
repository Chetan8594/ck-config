### Configuration Server

The Spring Cloud Configuration Server allows for a central place to manage external properties for applications across all environments.

#### How to use Config Server
> **Prerequisite** - To run application locally or in the cloud the discovery service should be already up and running.

1. Add spring-cloud-starter-config dependency to build.gradle file

2. Create properties file with name matching spring.application.name. If properties are specific to an environment, append '-<system, perf, uat, or prod>' to the end of the file name. Add properties to file like 'message=This is my System Message'

3. Move file into config/src/main/resources/config. If the file is environment specific, move into appropriate folder.

4. Read value in application
```
@Value("${message:default if no connection to config server}")
String message;
```

##### Useful Links:
[Spring Cloud Config Documentation](https://cloud.spring.io/spring-cloud-config/spring-cloud-config.html)
[Spring Guide: Centralized Configuration](https://spring.io/guides/gs/centralized-configuration/)

##### Useful commands:

> **Prerequisites:** To use the encryption and decryption features you need the full-strength JCE installed in your JVM (it&rsquo;s not there by default). You can download the [Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html) from Oracle, and follow instructions for installation (essentially replace the 2 policy files in the JRE lib/security directory with the ones that you downloaded). PCF JavaBuildpack has these files in it, so no changes required for PCF.

Generate a keystore:
```
keytool -genkeypair -keysize 2048 -alias mytestkey -keyalg RSA -dname "CN=ilch-d495385.ent.ad.ntrs.com,OU=WMH,O=Northern Trust,L=Chicago,S=IL,C=US" -keypass changeme -keystore server.jks -storepass letmein
```

Make sure application.yml includes the following:
```
encrypt:
  key-store:
    location: classpath:/server.jks
    password: letmein
    alias: mytestkey
    secret: changeme
```

Encrypt a message:
```
curl localhost:8888/encrypt -d mysecret
```

or use Spring Cloud CLI:
```
spring encrypt mysecret --key foo
```

Make sure to include single quotes when adding encrypted message to .yml file:
```
message: '{cipher}AQCLJG5FvxQrLAC6Q9iDmkegrIh1yJ4K2BeRzNdu3PkMZXv1cMSJ/C3flllHITinxgKecX3I8MPIf6sSsS’
```