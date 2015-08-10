# OpenShift WildFly Cartridge

These instructions will get you up and running with **WildFly 10.0.0.Beta1**. To run WildFly 9.0.1.Final instead, take a look at the documentation [here](https://github.com/openshift-cartridges/openshift-wildfly-cartridge/tree/wildfly-9). Alternatively, to run WildFly 8.2.1.Final, take a look at the documentation [here](https://github.com/openshift-cartridges/openshift-wildfly-cartridge/tree/wildfly-8).

This cartridge is based on the JBoss AS cartridge found in OpenShift Origin [here](https://github.com/openshift/origin-server/tree/master/cartridges/openshift-origin-cartridge-jbossas).  

Pretty much everything seems to be working fine, but it can still use some testing.  

If you find any issues, please log them in the [issues](https://github.com/openshift-cartridges/openshift-wildfly-cartridge/issues) section of the [github](https://github.com/openshift-cartridges/openshift-wildfly-cartridge) project.  

This cartridge will act very similarly to the jbossas-7 cartridge that you can create a gear with.  
You should be able to modify the code in the /src directory of the git repository, and do a git add, git commit, and git push and have it deployed as ROOT.war.  
You can also remove the pom.xml and /src directories and place a war file in the deployments directory and they should deploy like they do on JBoss AS 7  

### Create a new WildFly 10 app

The following command can be used to create a new WildFly 10 app using the OpenShift [command line tools](https://www.openshift.com/get-started):

	rhc app create wildfly -s https://raw.githubusercontent.com/openshift-cartridges/openshift-wildfly-cartridge/wildfly-10/metadata/manifest.yml
	
It will take a few minutes to build, so be patient.

We recommend to create a scalable application since the WildFly cartridge might consume more storage on free plans. With a scalable application, any additional cartridges, such as a database cartridge, will be installed on separate gears.

### Create a new WildFly 10 app based on an existing app

If you already have an existing WildFly 10 OpenShift application, you can create a new WildFly 10 app based on the existing one using the RHC --from-app command. First, make sure you have the latest version of the OpenShift command line tools:

    gem update rhc

Then, create your new WildFly app:

    rhc app create <NEW_WILDFLY_APP> --from-app <OLD_WILDFLY_APP>

This creates an application that's a clone of your existing one (i.e., same gear size, scaling configuration, environment variables, git repository, etc.). This means that your new WildFly 10 application will have the same configuration and deployment(s) as your existing application.

Because the --from-app command relies on an application snapshot, it will take some time to complete (be patient!).

### JBoss CLI

This cartridge provides an OpenShift compatible wrapper of the JBoss CLI tool on the gear PATH, located at $OPENSHIFT_WILDFLY_DIR/bin/tools/jboss-cli.sh. Use the following command to connect to the WildFly instance with the CLI tool:

        jboss-cli.sh -c --controller=$OPENSHIFT_WILDFLY_IP:$OPENSHIFT_WILDFLY_MANAGEMENT_HTTP_PORT

### Super Secret Hint (Don't tell anyone)

If you run the rhc port-forward command, you can access the WildFly management interface on port 9990.  
A username and password is created when you install this cartridge.  
If you don't write it down, fear not, the following environment variables will contain them.  
$OPENSHIFT_WILDFLY_USERNAME  
$OPENSHIFT_WILDFLY_PASSWORD

	corey$ rhc port-forward wildfly
	Checking available ports ... done
	Forwarding ports ...

	To connect to a service running on OpenShift, use the Local address

	Service Local               OpenShift
	------- -------------- ---- -------------------
	java    127.0.0.1:8080  =>  127.13.118.129:8080
	java    127.0.0.1:9990  =>  127.13.118.129:9990
	java    127.0.0.1:9999  =>  127.13.118.129:9999
	
In this example, you would visit 127.0.0.1:9990 to access the WildFly Admin Console from your local computer.

### Troubleshooting

**Q:** Can the following error that sometimes occurs on startup be ignored? My app deploys fine even when this error occurs in the server log file.


```
ERROR [org.jboss.as.controller.management-operation] 
(management-handler-thread - 4) JBAS014613: Operation ("read-resource") 
failed - address: ([("subsystem" => "deployment-scanner")]) - failure 
description: "JBAS014807: Management resource '[(\"subsystem\" => 
\"deployment-scanner\")]' not found"
```

**A:**  Yes, this error can safely be ignored. It just indicates that the deployment scanner wasn't yet available when the cartridge attempted to check if your app was deployed. Your app will still get deployed successfully. 

### Thanks to the following:
Stian Thorgersen for this blog article that helped get this going!
https://community.jboss.org/people/stianst/blog/2013/06/13/run-wildfly-on-openshift-using-the-diy-cartridge

Also thanks to the OpenShift team, and the users on the #wildfly channel on Freenode for helping answer questions and helping troubleshoot inter-gear management connection issues.


