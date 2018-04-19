
# Anypoint Template: Salesforce to Salesforce User Bidirectional Synchronization

+ [License Agreement](#licenseagreement)
+ [Use Case](#usecase)
+ [Considerations](#considerations)
	* [Salesforce Considerations](#salesforceconsiderations)
+ [Run it!](#runit)
	* [Running on premise](#runonopremise)
	* [Running on Studio](#runonstudio)
	* [Running on Mule ESB stand alone](#runonmuleesbstandalone)
	* [Running on CloudHub](#runoncloudhub)
	* [Deploying your Anypoint Template on CloudHub](#deployingyouranypointtemplateoncloudhub)
	* [Properties to be configured (With examples)](#propertiestobeconfigured)
+ [API Calls](#apicalls)
+ [Customize It!](#customizeit)
	* [config.xml](#configxml)
	* [businessLogic.xml](#businesslogicxml)
	* [endpoints.xml](#endpointsxml)
	* [errorHandling.xml](#errorhandlingxml)


# License Agreement <a name="licenseagreement"/>
Note that using this template is subject to the conditions of this [License Agreement](AnypointTemplateLicense.pdf).
Please review the terms of the license before downloading and using this template. In short, you are allowed to use the template for free with Mule ESB Enterprise Edition, CloudHub, or as a trial in Anypoint Studio.

# Use Case <a name="usecase"/>
This Template should serve as a foundation for setting an online bi-directional sync of Users between two Salesforce instances with ability to specify filtering criteria.
The main behavior of this template is fetching data for changes (new or modified Users) using scheduler or http component that have occurred in any of the Salesforce instances during a certain defined period of time. For those Users that both have not been updated yet the integration triggers an upsert (update or create depending the case) taking the last modification as the one that should be applied.

# Considerations <a name="considerations"/>

There are a couple of things you should take into account before running this kick:

1. **Users cannot be deleted in SalesForce:** For now, the only thing to do regarding users removal is disabling/deactivating them, but this won't make the username available for a new user.
2. **Each user needs to be associated to a Profile:** SalesForce's profiles are what define the permissions the user will have for manipulating data and other users. Each SalesForce account has its own profiles. Check out the next section to define a map between Profile Ids (from the source account to the ones in the target account and the other way around).
3. **Working with sandboxes for the same account**: Although each sandbox should be a completely different environment, Usernames cannot be repeated in different sandboxes, i.e. if you have a user with username *bob.dylan* in *sandbox A*, you will not be able to create another user with username *bob.dylan* in *sandbox B*. If you are indeed working with Sandboxes for the same SalesForce account you will need to map the source username to a different one in the target sandbox, for this purpose, please refer to the processor labeled *assign ProfileId and Username to the User*.



## Salesforce Considerations <a name="salesforceconsiderations"/>

There may be a few things that you need to know regarding Salesforce, in order for this template to work.

In order to have this template working as expected, you should be aware of your own Salesforce field configuration.

### FAQ

 - Where can I check that the field configuration for my Salesforce instance is the right one?

    [Salesforce: Checking Field Accessibility for a Particular Field][1]

- Can I modify the Field Access Settings? How?

    [Salesforce: Modifying Field Access Settings][2]


[1]: https://help.salesforce.com/HTViewHelpDoc?id=checking_field_accessibility_for_a_particular_field.htm&language=en_US
[2]: https://help.salesforce.com/HTViewHelpDoc?id=modifying_field_access_settings.htm&language=en_US

### As source of data

If the user configured in the template for the source system does not have at least *read only* permissions for the fields that are fetched, then a *InvalidFieldFault* API fault will show up.

```
java.lang.RuntimeException: [InvalidFieldFault [ApiQueryFault [ApiFault  exceptionCode='INVALID_FIELD'
exceptionMessage='
Account.Phone, Account.Rating, Account.RecordTypeId, Account.ShippingCity
^
ERROR at Row:1:Column:486
No such column 'RecordTypeId' on entity 'Account'. If you are attempting to use a custom field, be sure to append the '__c' after the custom field name. Please reference your WSDL or the describe call for the appropriate names.'
]
row='1'
column='486'
]
]
```

### As destination of data

There are no particular considerations for this Anypoint Template regarding Salesforce as data destination.









# Run it! <a name="runit"/>
Simple steps to get Salesforce to Salesforce User Bidirectional Synchronization running.


## Running on premise <a name="runonopremise"/>
In this section we detail the way you should run your Anypoint Template on your computer.


### Where to Download Mule Studio and Mule ESB
First thing to know if you are a newcomer to Mule is where to get the tools.

+ You can download Mule Studio from this [Location](http://www.mulesoft.com/platform/mule-studio)
+ You can download Mule ESB from this [Location](http://www.mulesoft.com/platform/soa/mule-esb-open-source-esb)


### Importing an Anypoint Template into Studio
Mule Studio offers several ways to import a project into the workspace, for instance: 

+ Anypoint Studio Project from File System
+ Packaged mule application (.jar)

You can find a detailed description on how to do so in this [Documentation Page](http://www.mulesoft.org/documentation/display/current/Importing+and+Exporting+in+Studio).


### Running on Studio <a name="runonstudio"/>
Once you have imported you Anypoint Template into Anypoint Studio you need to follow these steps to run it:

+ Locate the properties file `mule.dev.properties`, in src/main/resources
+ Complete all the properties required as per the examples in the section [Properties to be configured](#propertiestobeconfigured)
+ Once that is done, right click on you Anypoint Template project folder 
+ Hover you mouse over `"Run as"`
+ Click on  `"Mule Application"`


### Running on Mule ESB stand alone <a name="runonmuleesbstandalone"/>
Complete all properties in one of the property files, for example in [mule.prod.properties] (../master/src/main/resources/mule.prod.properties) and run your app with the corresponding environment variable to use it. To follow the example, this will be `mule.env=prod`. 


## Running on CloudHub <a name="runoncloudhub"/>
While [creating your application on CloudHub](http://www.mulesoft.org/documentation/display/current/Hello+World+on+CloudHub) (Or you can do it later as a next step), you need to go to Deployment > Advanced to set all environment variables detailed in **Properties to be configured** as well as the **mule.env**.


### Deploying your Anypoint Template on CloudHub <a name="deployingyouranypointtemplateoncloudhub"/>
Mule Studio provides you with really easy way to deploy your Template directly to CloudHub, for the specific steps to do so please check this [link](http://www.mulesoft.org/documentation/display/current/Deploying+Mule+Applications#DeployingMuleApplications-DeploytoCloudHub)


## Properties to be configured (With examples) <a name="propertiestobeconfigured"/>
In order to use this Mule Anypoint Template you need to configure properties (Credentials, configurations, etc.) either in properties file or in CloudHub as Environment Variables. Detail list with examples:
### Application configuration
#### Application configuration

+ scheduler.frequency `10000`  
This are the milliseconds that will run between two different checks for updates in either Salesforce instance

+ scheduler.startDelay `0`

+ watermark.default.expression `2018-02-25T11:00:00.000Z`  
This property is an important one, as it configures what should be the start point of the synchronization. If the use case includes synchronization of every 
user created from the begining of the times, you should use a date previous to any user creation (perhaphs `1900-01-01T08:00:00.000Z` is a good choice). 
If you want to synchronize the users created from now on, then you should use a default value according to that requirement (for example, 
if today is April 21st of 2018 and it's eleven o'clock in London, then you could use the following value `2018-04-21T11:00:00.000Z`).

+ page.size `1000`


#### SalesForce Connector configuration for company A

+ sfdc.a.username `jorge.drexler@mail.com`
+ sfdc.a.password `Noctiluca123`
+ sfdc.a.securityToken `avsfwCUl7apQs56Xq2AKi3X`
+ sfdc.a.integration.user.id `A0ed000BO9T`

	**Note:** To find out the correct *sfdc.a.integration.user.id* value, please, refer to example project **Salesforce Data Retrieval** in [Anypoint Exchange](http://www.mulesoft.org/documentation/display/current/Anypoint+Exchange).

#### SalesForce Connector configuration for company B

+ sfdc.b.username `mariano.cozzi@mail.com`
+ sfdc.b.password `LaRanitaDeLaBicicleta456`
+ sfdc.b.securityToken `ces56arl7apQs56XTddf34X`
+ sfdc.b.integration.user.id `B0ed000BO9T`

	**Note:** To find out the correct *sfdc.b.integration.user.id* value, please, refer to example project **Salesforce Data Retrieval** in [Anypoint Exchange](http://www.mulesoft.org/documentation/display/current/Anypoint+Exchange).

SalesForce's profiles are what define the permissions the user will have for manipulating data and other users.
Each SalesForce account has its own profiles.

This should be a map that associates a profile in sfdc A with a profile in sfdc B
for example: ['SOME_PROFILE_IN_A': 'SOME_PROFILE_IN_B', 'ANOTHER_PROFILE_IN_A': 'ANOTHER_PROFILE_IN_B']

+ sfdc.a.profile.id  `['00e200000015oKF': '00e20000001UzDx']`

This should be a map that associates a profile in sfdc B with a profile in sfdc A
for example: ['SOME_PROFILE_IN_B': 'SOME_PROFILE_IN_A', 'ANOTHER_PROFILE_IN_B': 'ANOTHER_PROFILE_IN_A']

+ sfdc.b.profile.id `['00e200000015oKF': '00e20000001UzDx']`

The meaning of the properties above is defined in the second consideration on [the previous section](#afewconsiderations)

#### Test configuration
email.a.b 'test@test.com'

# API Calls <a name="apicalls"/>
Not relevant for this use case.


# Customize It!<a name="customizeit"/>
This brief guide intends to give a high level idea of how this Anypoint Template is built and how you can change it according to your needs.
As mule applications are based on XML files, this page will be organized by describing all the XML that conform the Anypoint Template.
Of course more files will be found such as Test Classes and [Mule Application Files](http://www.mulesoft.org/documentation/display/current/Application+Format), but to keep it simple we will focus on the XMLs.

Here is a list of the main XML files you'll find in this application:

* [config.xml](#configxml)
* [endpoints.xml](#endpointsxml)
* [businessLogic.xml](#businesslogicxml)
* [errorHandling.xml](#errorhandlingxml)


## config.xml<a name="configxml"/>
Configuration for Connectors and [Configuration Properties](http://www.mulesoft.org/documentation/display/current/Configuring+Properties) are set in this file. **Even you can change the configuration here, all parameters that can be modified here are in properties file, and this is the recommended place to do it so.** Of course if you want to do core changes to the logic you will probably need to modify this file.

In the visual editor they can be found on the *Global Element* tab.


## businessLogic.xml<a name="businesslogicxml"/>
This file holds the functional aspect of the template. Its main component is a [Batch Job](http://www.mulesoft.org/documentation/display/current/Batch+Processing), and it includes steps for both executing the synchronization from Salesforce A to Salesforce B, and the other way around.



## endpoints.xml<a name="endpointsxml"/>
This file should contain every inbound and outbound endpoint of your integration app. 
In this particular template, this file contains a scheduler endpoint that query Salesforce A and Salesforce B for updates using watermark and http endpoint for push operation.



## errorHandling.xml<a name="errorhandlingxml"/>
This is the right place to handle how your integration will react depending on the different exceptions. 
This file holds a [Error Handling](http://www.mulesoft.org/documentation/display/current/Error+Handling) that is referenced by the scheduler flow in the endpoints xml file.



