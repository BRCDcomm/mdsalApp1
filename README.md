# mdsalApp1
This repository is intended to teach you some very basic concepts around creating an MD-SAL application for OpenDaylight enabled SDN controllers.  It may seem complex at first, but remember that OpenDaylight is a platform that allows numerous individuals and companies to contribute features simultaneously with very little interaction between those teams.  This means that beating at the heart of OpenDaylight is an engine to keep all the features separate, but able to communicate data to one another as necessary.  When you create an application for OpenDaylight it is one small piece of a much larger system.  As such, when an application is created there are many points between your application and the OpenDaylight engine that need to be 'wired up'.  The good news is that most of these connections can be done automatically for you, but they all require numerous Java classes and boiler plate code, all of which is part of your application...even if you never need to touch it.  So, the trick to creating MD-SAL applications is 1) know how to get all the connections and associated code to be created automatically for you and 2) know exactly which files you *do* need to modify and why.  This aims to walk you through that process so you can learn a few tricks.  It will take a few more before you are good at it, but we all start at the beginning.

The ExampleApp in this repository will provide a remote procedure call, available via RESTCONF, that allows you to set a bandwidth configuration.

This repository contains:

1. A set of folders that is the skeleton for an OpenDaylight MD-SAL application
that supports a remote procedure call (RPC).  It contains the bare minimum set of files you need in order to get OpenDaylight to generate all the connections for your application.  Note, we do not use an archetype tool to generate these, we just give them to you in this repository.  In a later tutorial we will introduce the archetype tool.
1. This README.md that walks you through a tutorial to:
	1. Add logic to the skeleton so that it actually responds to RPC calls.
	1. Install it into an OpenDaylight controller including the Brocade SDN Controller 1.3.0

### Prerequisites
1. OpenDaylight enabled SDN controller such as the Brocade SDN Controller.  This has been tested with Helium release of OpenDaylight as well as Brocade SDN Controller 1.3.0
2. Maven

### Step 0:  Clone this repository to your machine (virtual machine) where you run OpenDaylight enabled SDN controller

```bash
git clone https://github.com/Elbrys/mdsalApp1.git
```
	
### Step 1:  ExampleApp File Review
First, just look through the skeleton folders/files of ExampleApp.  This is the bare minimum set of folders/files we need in order to get OpenDaylight to auto-generate a lot of other files for us. 


1. Yang
	1. API
		1. exampleapp-api/src/main/yang/exampleapp.yang
		1. This yang file defines the API for your MD-SAL application.  This includes remote
		procedure calls as well as data elements (this example only has RPCs)
	1. Dependencies
		1. exampleapp-impl/src/main/yang/exampleapp-impl.yang
   		1. This yang file defines the modules on which the exampleapp is dependent:
			1. binding-rpc-registry: RPC registration to allow bandwidth configuration
			1. binding-broker-osgi-registryDat broker Used for consumer registration.
1. Config
	1. exampleapp-config/src/main/resources/initial/49-exampleapp-sample.xml
	1. Configuration specific to the ExampleApp
1. Feature
	1. exampleapp-features/src/main/resources/features.xml
	1. Describes the ExampleApp feature for inclusion in the module management system of ODL (Karaf)
1. pom.xml files
	1. pom files are taken from ArcheType Toolkit project:  https://wiki.opendaylight.org/view/OpenDaylight_Toolkit:MD-SAL-Simple_Archetype
  	
### Step 2:  Build the project 
Go ahead and do a build on the project.  This will cause auto-generation of many files for you.

```bash
mvn clean install 
```

Observe that the following java files are auto-generated for you based on the information in the original set files (above).

```bash
./exampleapp-api/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/api/rev150617/ExampleAppOutputBuilder.java
./exampleapp-api/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/api/rev150617/ExampleAppInput.java
./exampleapp-api/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/api/rev150617/ExampleAppInputBuilder.java
./exampleapp-api/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/api/rev150617/ExampleAppOutput.java
./exampleapp-api/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/api/rev150617/$YangModelBindingProvider.java
./exampleapp-api/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/api/rev150617/ExampleappService.java
./exampleapp-api/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/api/rev150617/$YangModuleInfoImpl.java
./exampleapp-impl/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/Exampleapp.java
./exampleapp-impl/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/$YangModelBindingProvider.java
./exampleapp-impl/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/$YangModuleInfoImpl.java
./exampleapp-impl/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/modules/module/configuration/ExampleappBuilder.java
./exampleapp-impl/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/modules/module/configuration/Exampleapp.java
./exampleapp-impl/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/modules/module/configuration/exampleapp/BrokerBuilder.java
./exampleapp-impl/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/modules/module/configuration/exampleapp/Broker.java
./exampleapp-impl/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/modules/module/configuration/exampleapp/RpcRegistryBuilder.java
./exampleapp-impl/src/main/yang-gen-sal/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/modules/module/configuration/exampleapp/RpcRegistry.java
./exampleapp-impl/src/main/java/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/ExampleappModule.java
./exampleapp-impl/src/main/java/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/ExampleappModuleFactory.java
./exampleapp-impl/src/main/yang-gen-config/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/AbstractExampleappModuleFactory.java
./exampleapp-impl/src/main/yang-gen-config/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/ExampleappModuleMXBean.java
./exampleapp-impl/src/main/yang-gen-config/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/AbstractExampleappModule.java
```

### Step 3 (optional):  Create an Eclipse project if you prefer to use Eclipse.
If you use Eclipse as your preferred development environment that have Maven create you a project

```bash
mvn eclipse:eclipse
```

Once the project is created, open Eclipse and import the project into an Eclipse workspace.

### Step 4:  Modify created Java files
The auto-generated files will need some modification to implement the actual logic for your application.  Remember, those files just wire your application into OpenDaylight.  You still need to actually write your application code that makes it do something unique.  The key to doing that is knowing which of the auto-generated files you need to modify.  The skeleton connects all the parts, but does not know what to actually do to make your application do its core logic.  You need to know the places to change and then make those changes.

1. create package com.elbrys.sdn.impl in exampleapp-impl/src/main/java

	```bash
	cd exampleapp-impl/src/main/java
	mkdir -p com/elbrys/sdn/impl
	```
1. create an empty file named ExampleappImpl.java in the new folder

	```bash
	touch ExampleappImpl.java
	```
1. copy this into ExampleappImpl.java

	```java
package com.elbrys.sdn.impl;

import java.util.concurrent.Future;

import org.opendaylight.controller.sal.binding.api.BindingAwareBroker.ConsumerContext;
import org.opendaylight.controller.sal.binding.api.BindingAwareConsumer;
import org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.api.rev150617.ExampleAppInput;
import org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.api.rev150617.ExampleAppOutput;
import org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.api.rev150617.ExampleAppOutputBuilder;
import org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.api.rev150617.ExampleappService;
import org.opendaylight.yangtools.yang.common.RpcResult;
import org.opendaylight.yangtools.yang.common.RpcResultBuilder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.google.common.util.concurrent.Futures;

public class ExampleappImpl implements BindingAwareConsumer, ExampleappService {
    private static final Logger LOG = LoggerFactory.getLogger(ExampleappImpl.class);
    private static long DEFAULT_BANDWIDTH = 50;

    private Long bandwidth;

    public ExampleappImpl() {
        LOG.info("ExampleAppImpl contructor");
        this.bandwidth = DEFAULT_BANDWIDTH;
    }

    @Override
    public Future<RpcResult<ExampleAppOutput>> exampleApp(ExampleAppInput input) {
        LOG.info("ExampleAppImpl exampleRpc");
        // Build output message
        ExampleAppOutputBuilder outBuilder = new ExampleAppOutputBuilder();
        outBuilder.setBandwidth(input.getBandwidth());
        // Modify bandwidth
        this.bandwidth = input.getBandwidth();
        return Futures.immediateFuture(RpcResultBuilder.success(outBuilder.build()).build());
    }

    public void close() {
        LOG.info("ExampleAppImpl close");
    }

    @Override
    public void onSessionInitialized(ConsumerContext context) {
        LOG.info("ExampleAppImpl onSessionInitialized");
    }

}
```
1.  Modify ./exampleapp-impl/src/main/java/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/ExampleAppModule.java to register RPC

Replace the contents of the file exampleapp-impl/src/main/java/org/opendaylight/yang/gen/v1/urn/opendaylight/params/xml/ns/yang/exampleapp/impl/rev150617/ExampleAppModule.java with the following.

```java
package org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.impl.rev150617;

import org.opendaylight.controller.sal.binding.api.BindingAwareBroker;
import org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.api.rev150617.ExampleappService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.elbrys.sdn.impl.ExampleappImpl;

public class ExampleappModule
        extends
        org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.impl.rev150617.AbstractExampleappModule {

    private static final Logger LOG = LoggerFactory.getLogger(ExampleappModule.class);

    public ExampleappModule(org.opendaylight.controller.config.api.ModuleIdentifier identifier,
            org.opendaylight.controller.config.api.DependencyResolver dependencyResolver) {
        super(identifier, dependencyResolver);
    }

    public ExampleappModule(
            org.opendaylight.controller.config.api.ModuleIdentifier identifier,
            org.opendaylight.controller.config.api.DependencyResolver dependencyResolver,
            org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.impl.rev150617.ExampleappModule oldModule,
            java.lang.AutoCloseable oldInstance) {
        super(identifier, dependencyResolver, oldModule, oldInstance);
    }

    @Override
    public void customValidation() {
        // add custom validation form module attributes here.
    }

    @Override
    public java.lang.AutoCloseable createInstance() {
        LOG.info("ExampleappModule Create Instance.");
        final ExampleappImpl exampleApp = new ExampleappImpl();

        LOG.info("ExampleappModule Regiser RPC.");
        @SuppressWarnings("unused")
        final BindingAwareBroker.RpcRegistration<ExampleappService> rpcRegistration = getRpcRegistryDependency()
                .addRpcImplementation(ExampleappService.class, exampleApp);

        getBrokerDependency().registerConsumer(exampleApp, null);

        // Wrap exampleApp as AutoCloseable and close registrations to md-sal at
        // close(). The close method is where you would generally clean up
        // thread pools
        // etc.
        final class AutoCloseableExampleApp implements AutoCloseable {

            @Override
            public void close() throws Exception {
                exampleApp.close();
            }
        }
        return new AutoCloseableExampleApp();
    }

}
```


If you are interested, this shows the specific lines changed in the file in order to register your ExampleApp's rpc with the OpenDaylight system.  The lines beginning with '+' are added lines.

```java
package org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.impl.rev150617;

import org.opendaylight.controller.sal.binding.api.BindingAwareBroker;
+import org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.api.rev150617.ExampleappService;
+import org.slf4j.Logger;
+import org.slf4j.LoggerFactory;

import com.elbrys.sdn.impl.ExampleappImpl;

public class ExampleappModule
        extends
        org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.impl.rev150617.AbstractExampleappModule {

+    private static final Logger LOG = LoggerFactory.getLogger(ExampleappModule.class);

    public ExampleappModule(org.opendaylight.controller.config.api.ModuleIdentifier identifier,
            org.opendaylight.controller.config.api.DependencyResolver dependencyResolver) {
        super(identifier, dependencyResolver);
    }

    public ExampleappModule(
            org.opendaylight.controller.config.api.ModuleIdentifier identifier,
            org.opendaylight.controller.config.api.DependencyResolver dependencyResolver,
            org.opendaylight.yang.gen.v1.urn.opendaylight.params.xml.ns.yang.exampleapp.impl.rev150617.ExampleappModule oldModule,
            java.lang.AutoCloseable oldInstance) {
        super(identifier, dependencyResolver, oldModule, oldInstance);
    }

    @Override
    public void customValidation() {
        // add custom validation form module attributes here.
    }

    @Override
    public java.lang.AutoCloseable createInstance() {
+        LOG.info("ExampleappModule Create Instance.");
+        final ExampleappImpl exampleApp = new ExampleappImpl();
+
+        LOG.info("ExampleappModule Regiser RPC.");
+        final BindingAwareBroker.RpcRegistration<ExampleappService> rpcRegistration = getRpcRegistryDependency()
+                .addRpcImplementation(ExampleappService.class, exampleApp);
+
+        getBrokerDependency().registerConsumer(exampleApp, null);
+
+        // Wrap exampleApp as AutoCloseable and close registrations to md-sal at
+        // close(). The close method is where you would generally clean up
+        // thread pools
+        // etc.
+        final class AutoCloseableExampleApp implements AutoCloseable {
+
+            @Override
+            public void close() throws Exception {
+                exampleApp.close();
+            }
+        }
+        return new AutoCloseableExampleApp();
    }
}
```


### Step 5: Build again (with your new content)
Now that you have modified the files that define how your application works, build it.

```bash
mvn clean install
```

### Step 6: Add your feature to OpenDaylight (Brocade SDN Controller)

1. copy ~/.m2/repository/com/elbrys/sdn/exampleapp-features/1.0.0-SNAPSHOT/exampleapp-features-1.0.0-SNAPSHOT-features.xml to deploy folder in BVC

	```bash
	cp ~/.m2/repository/com/elbrys/sdn/exampleapp-features/1.0.0-SNAPSHOT/exampleapp-features-1.0.0-SNAPSHOT-features.xml /opt/bvc/controller/deploy
	```

1. run OpenDaylight (Brocade SDN Controller)

	```bash
	/opt/bvc/bin/start
	```

1. run Karaf console 

	```bash
	/opt/bvc/bin/client
	```

1. verify that your ExampleApp feature is running by entering this command in the Karaf console

	```bash
	feature:list|grep xamp
	```

1. If the feature is not running then install it by entering this command in the Karaf console

    ```bash
    feature:install exampleapp
    ```
    
1. Verify that the log contains lines in it from ExampleApp by entering this command in the Karaf console

	```bash
	log:display |grep xam
	```

You should see output something like this:

```bash
2015-06-16 17:17:35,544 | INFO  | Local user karaf | FeaturesServiceImpl              | 24 - org.apache.karaf.features.core - 3.0.1 | Installing feature exampleapp 1.0.0-SNAPSHOT
2015-06-16 17:17:35,872 | INFO  | Local user karaf | FeaturesServiceImpl              | 24 - org.apache.karaf.features.core - 3.0.1 | Installing feature exampleapp-api 1.0.0-SNAPSHOT
2015-06-16 17:17:38,629 | INFO  | config-pusher    | ExampleappModule                 | 292 - com.elbrys.sdn.exampleapp-impl - 1.0.0.SNAPSHOT | ExampleappModule Create Instance.
2015-06-16 17:17:38,630 | INFO  | config-pusher    | ExampleappImpl                   | 292 - com.elbrys.sdn.exampleapp-impl - 1.0.0.SNAPSHOT | ExampleAppImpl contructor
2015-06-16 17:17:38,630 | INFO  | config-pusher    | ExampleappModule                 | 292 - com.elbrys.sdn.exampleapp-impl - 1.0.0.SNAPSHOT | ExampleappModule Regiser RPC.
2015-06-16 17:17:38,634 | INFO  | config-pusher    | ExampleappImpl                   | 292 - com.elbrys.sdn.exampleapp-impl - 1.0.0.SNAPSHOT | ExampleAppImpl onSessionInitialized
```

### Step 7: Test your ExampleApp

1.  open URL http://localhost:8181/apidoc/explorer/index.html
1.  goto exampleApp, open its list of operations by clicking on it
1.  select post and enter this as the JSON body

	```json
	 {"exampleapp:input":{"bandwidth":"101"}}
	 ```
 
1.  click 'Try It'
1.  verify that the returned response body is something like this

	```json
	    {
	      "output": {
	        "bandwidth": 101
	      }
	    }
	```

1. Verify log has an entry output by your application by entering this command in Karaf console

	```bash
	log:display |grep xam|grep Rpc
	```

1. You should see the following log entry.  If you check the code in "exampleapp-impl/src/main/java/com/elbrys/sdn/implExampleappImpl.java" you wil find a Log.info() call that outputs 'ExampleAppImpl exampleRpc'

	```bash
	2015-06-16 17:18:52,865 | INFO  | qtp250359406-489 | ExampleappImpl                   | 292 - com.elbrys.sdn.exampleapp-impl - 1.0.0.SNAPSHOT | ExampleAppImpl exampleRpc
	```
