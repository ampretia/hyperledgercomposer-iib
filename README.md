# How to get the loopback-connector-composer working with IIB

Produce a flow within IIB that includes the LoopbackRequestNode that uses loopback-connector-composer  to interact with the Hyperledger Composer.

## Sofwtare used
- All this was performed on Ubuntu 16
- Hyperledger Composer v0.7.2
- Hyperledger Fabric v1.0alpaha.1 (see the composer-tools)
- IIB v10 Developers Edition

## Setup

- IIB 10 Developer's Edition is very simple to install. 
*Very important - that this needs to be IIB 10.0.0.8 to get an updated version of node. *
- Hyperledger Composer - establish a working scenario, for example the getting started digitalproperty-network. First time, use the getting-started guide from the Hyperledger Composer site.

- IIB Profile
```
matthew@matthew-VirtualBox:~/Downloads/iib-10.0.0.8/server/bin$ . ./mqsiprofile

MQSI 10.0.0.8
/home/matthew/Downloads/iib-10.0.0.8/server

matthew@matthew-VirtualBox:~/Downloads/iib-10.0.0.8/server/bin$ which node
/home/matthew/Downloads/iib-10.0.0.8/common/node/bin/node
matthew@matthew-VirtualBox:~/Downloads/iib-10.0.0.8/server/bin$ node --version
v4.7.2
matthew@matthew-VirtualBox:~/Downloads/iib-10.0.0.8/server/bin$ npm --version
4.5.0
matthew@matthew-VirtualBox:~/Downloads/iib-10.0.0.8/server/bin$ 

```

### Loopback Connector

1. Open a command prompt and run the mqsiprofile command
2. Update the version of npm
3. `cd ~/iibconfig/node_modules`
4. `npm install loopback-connector-composer`
5. `cd ~/iibconfig/connector/loopback`
6. create this file - changing the business network id, etc as required.
```
{
  "composer":
  {
    "name": "composer",
    "connector": "composer",
    "connectionProfileName" : "hlfv1",
    "businessNetworkIdentifier" : "digitalproperty-network",
    "participantId" : "admin",
    "participantPwd" : "adminpw"
  }
}
```

### IIB Flows
IID Flow configuration is critical.

Update and Delete operations require the field that identifies the asset (eg titleID in the property network) is moved to the correct place in the broker message tree.  Other operations can work correctly without this.
In [this flow JSON](IIB-Loopback.zip) files are used describing new LandTitles to be created, updated, retrieved and updated. 


### Debug help
There are three locations where it is very helpful to have debug information coming from
Using the developer's edition, things are started using the `iib tookit` command

Ensure that these environment variables are set BEFORE starting.

```
$ export DEBUG=* && export MQSI_NODEJS_LOG_DESTINATION=/home/matthew/log4js.txt
$ ./iib toolkit
```

I found having some windows opened with the following files being tailed was very useful.

IIB Loopback logs are very helpful..

```
$ tail -f /home/matthew/log4js.txt
```

The stdout/stderr have the output from the loopback-connector-composer code

```
$ ~/iibconfig/components/TESTNODE_matthew/e41409c8-564b-41b3-9ef1-661ae79b0efc$ ls
config  direct  iibswitch  repository  startup.js  stderr  stdout
$ tail -f stderr
```

The hex string `e414l....` will vary on your machine but is the 'Execution Group ID'

Other errors related to the IIB functionar are reported to the syslog. 

```
$ tail -f /var/log/syslog | grep Integration
```

Finally, the Hyperledger Composer log files (triggered by the `DEBUG` environment variable) will go to

```
$ tail -f ~/iibconfig
```


