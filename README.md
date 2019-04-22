# Deploy a financial microservice on LinuxONE using IBM Cloud Private 

In this Code Pattern, you will learn how to build and deploy a banking microservice with IBM Cloud private running in the LinuxONE Community Cloud. 

IBM Cloud Private is a private cloud platform for developing and running workloads locally. It is an integrated environment that enables you to design, develop, deploy and manage on-premises, containerized cloud applications behind a firewall. It includes the container orchestrator Kubernetes, a private image repository, a management console and monitoring frameworks.

When you will complete this Code Pattern, you will understand how to:

* Build a Docker image from an existing application.
* Deploy a Docker image to IBM Cloud Private.
* Run the existing application using the IBM Cloud Private catalog.


# Architecture

This journey accesses a fictitious retail banking system called MPLbank. MPLbank integrates an Account Management System running on IBM Mainframe. On top of this component, an API layer based on z/OS Connect EE and IBM API Connect has been set up to deliver banking APIs. It makes banking services reachable through APIs from all kind of applications. IBM Cloud private has been configured into the LinuxONE  Community Cloud.

![alt text](images/arch-build-deploy-financial-microservice.png "Architecture")

1. The user deploys a Docker image (banking application based microservice) to the IBM Cloud Private Docker registry.
2. The user configures and runs a container based on the previous Docker image from IBM Cloud Private catalog. Once started, the application calls banking APIs published in API Connect.
3. API Connect calls the back-end Z Mainframe through a banking API published in z/OS Connect EE.
4. z/OS Connect EE calls the Account Management System (AMS) running in CICS. A COBOL program processes the request and returns banking data. Finally, banking data are sent back to the microservice application.


# Included components

* [IBM Cloud private](https://www.ibm.com/cloud/private)
* [IBM API Connect](http://www-03.ibm.com/software/products/en/api-connect)
* [IBM z/OS Connect Enterprise Edition](https://www.ibm.com/us-en/marketplace/connect-enterprise-edition)
* [IBM CICS Tansaction Server](https://www.ibm.com/us-en/marketplace/cics-transaction-server#product-header-top)
* [IBM Db2](https://www.ibm.com/analytics/db2/zos)

# Featured technologies

* [Docker](https://www.Docker.com/)
* [Microservice](https://www.ibm.com/cloud/garage/architectures/microservices/)
* [IBM LinuxONE](https://www.ibm.com/it-infrastructure/LinuxONE)
* [IBM Z Mainframe](https://www.ibm.com/it-infrastructure/z)

# Credentials for the workshop

| Github account | Github respository | API Developer Portal account | ICP account |
| -------------- | ------------------ | ---------------------------- | ----------- |
| techu2019      | techu01            | techu01                      | techu01     |
| techu2019      | techu02            | techu02                      | techu02     |
| techu2019      | techu03            | techu03                      | techu03     |
| techu2019      | techu04            | techu04                      | techu04     |
| techu2019      | techu05            | techu05                      | techu05     |
| techu2019      | techu06            | techu06                      | techu06     |
| techu2019      | techu07            | techu07                      | techu07     |
| techu2019      | techu08            | techu08                      | techu08     |
| techu2019      | techu09            | techu09                      | techu09     |
| techu2019      | techu10            | techu10                      | techu10     |
| techu2019      | techu11            | techu11                      | techu11     |
| techu2019      | techu12            | techu12                      | techu12     |
| techu2019      | techu13            | techu13                      | techu13     |
| techu2019      | techu14            | techu14                      | techu14     |
| techu2019      | techu15            | techu15                      | techu15     |
| techu2019      | techu16            | techu16                      | techu16     |
| techu2019      | techu17            | techu17                      | techu17     |
| techu2019      | techu18            | techu18                      | techu18     |
| techu2019      | techu19            | techu19                      | techu19     |
| techu2019      | techu20            | techu20                      | techu20     |


# Steps

<!-- https://ecotrust-canada.github.io/markdown-toc/ -->

### Step 1 - Discover and locally run the banking application

- [Part 1 - Discover the banking application](#part-1---discover-the-banking-application)
- [Part 2 - Subscribe to the banking API through the API Developer Portal](#part-2---subscribe-to-the-banking-api-through-the-api-developer-portal)
- [Part 3 - Run the banking application with Node.js](#part-3---run-the-banking-application-with-nodejs)
- [Part 4 - Push the banking application to your GitHub repository](#part-4---push-the-banking-application-to-your-github-repository)

### Step 2 - Build and deploy a Docker image to IBM Cloud private

- [Part 1 - Build the Docker image from the LinuxONE Community Cloud](#part-1---build-the-docker-image-from-the-LinuxONE-community-cloud)
- [Part 2 - Deploy the Docker image to IBM Cloud private](#part-2---deploy-the-docker-image-to-ibm-cloud-private)

### Step 3 - Instantiate the banking microservice from the IBM Cloud private catalog

- [Part 1 - Discover the Helm chart from the calalog](#part-1---discover-the-helm-chart-from-the-calalog)
- [Part 2 - Configure and install your banking microservice](#part-2---configure-and-install-your--microservice)
- [Part 3 - Access your banking microservice](#part-3---access-your-banking-microservice)

---

# Step 1 - Discover and locally run the banking application

The objective is to discover the banking application located in the *banking-application* folder. This application is a Node.js application. It will be locally tested before packaging it into a Docker image for IBM Cloud private.

## Part 1 - Discover the banking application

1. Launch a terminal (Command Prompt).

2. Start a Git Bash session.

    for x86

    `start "" "%SYSTEMDRIVE%\Program Files (x86)\Git\bin\sh.exe" --login`

    for x64

    `start "" "%PROGRAMFILES%\Git\bin\sh.exe" --login`

3. Clone your GitHub repository *techu<nn>-icp-banking-microservices* to create a local copy of the banking application:

   `git clone https://github.com/techu2019/techu<nn>-icp-banking-microservices`
  	
5. Take a look at the *banking-application* folder:

   `cd techu<nn>-icp-banking-microservices/banking-application`
   `ls -lR`
       
  	* *app.js*: the application server code.
	* *public/index.html*: the application client code (banking dashboard).
	* *public/css*: the application stylesheet.
	* *public/js*: the JavaScript libraries. 
	* *public/js/bankingAPI.js*: will be modified later to connect to a corebanking system through API calls.
	* *package.json*: the package dependencies file.
	* *Dockerfile*: will be used later to build the Docker image.

## Part 2 - Subscribe to the banking API through the API Developer Portal

1. From a web browser, go to the [API Developer Portal].

3. Log in to your API Developer portal account.

5. Create a new application.
	![alt text](images/createApplication.png "Create Application")
	* Click **Apps** from the menu.
	* Click **Create new App**.
	* Fill in all the required fields.
	* Click **Submit**.
	
	Make a note of the *client ID* and *client Secret*. You will need them to access the API later.
	![alt text](images/keyApplication.png "API Keys")

6. Before working with the banking API, you need to subscribe to it first. Display the list of available API products.
	![alt text](images/bankingProduct.png "Choose the default plan")
	* Click **API Products** from the top menu.
	* Click **Banking Product** in the list.

7. Subscribe to the Banking API.
	![alt text](images/APISubscription.png "Subscribe")
	* Click **Subscribe** to the Default Plan.
	
	![alt text](images/APISubscription2.png "Banking Product")
	* Select the App that you have just created before.
	* Click **Subscribe**.
	
8. Return to your Git Bash session. Modify the *public/js/bankingAPI.js* file.

	![alt text](images/client_id_secret.png "javascript code")
	
	'vi public/js/bankingAPI.js'
	
	* Replace *YOUR_CLIENT_ID_HERE* with the client ID value from the IBM API developer application subscription.
	* Replace *YOUR_CLIENT_SECRET_HERE* with the client Secret value from the IBM API developer application subscription.

## Part 3 - Run the banking application with Node.js

1. Go to the banking application directory, if you are not already.   

    `cd ICp-banking-microservices/banking-application`

2. Install Node.Js package dependencies of the banking application using : 

    `npm install`
    
	![alt text](images/npm-install.png "npm install")
	
   As a result, dependencies are installed in your project folder.

3. Run the banking application using : 
    
    `node app.js` :

        ![alt text](images/node-app-js.png "node app.js")
	
   As a result, The banking application is started.
	
4. To view your application, open this link in a web browser: 

     `http://localhost:3000` 
   
   The banking application appears.
    
	![alt text](images/banking_app.png "Banking application")

5. Test your application.

	![alt text](images/banking_app_test.png "Banking application")
    
    * Select a customer ID.
    * Please wait while the application calls banking data from the Mainframe through API Connect and z/OS Connect EE.
    * The result is displayed in a JSON structure.
    
6. The banking application works locally. Stop the Node.js server with the following command:

    `SIGINT (CTRL+C)`


## Part 4 - Push the banking application to your GitHub repository

1. Add the *bankingAPI.js* file you just modified to the current content index:

   `git add public/js/bankingAPI.js`

2. Commit the fresh code you modified to add changes to the local repository:

   `git commit -m "Update of bankingAPI.js"`

3. Push the code you commited to transfer the last commit to your GitHub repository *ICp-banking-microservices*:

   `git push`
   
   Enter github user credentials if prompted.

4. Go back to your online Github repository *techu<nn>-icp-banking-microservices* using the web browser. 

   ![alt text](images/commit-push-repo.png "git push")
	
5. Check that your code has been updated with commit label *Update of BankingAPI.js*

---

:thumbsup: Congratulations! Your banking application works locally. Modifications have been pushed to your GitHub repository! Ready for IBM Cloud private?

---

# Step 2 - Build and deploy a Docker image to IBM Cloud private

The objective is to build a Docker image for the banking application from your github repository and then deploy it to the IBM Cloud private local docker registry.

In this workshop, an automatic process has been set up for you to build and deploy the Docker image to ICp. 

The process is as follow:

1. Login to the IBM Cloud private portal, https://148.100.111.67:8443, with your assigned credentials,

2. Access the catalog.

   * Click on **Catalog**

   ![alt text](images/icp-catalog-users.png "ICp catalog")
	
3.  Locate the **mplbank-imagebuild** chart.

   * Filer on **openmpl**
   
4. Click on the **mplbank-imagebuild** chart.

5. Click on **Configure**.

6. Fill in the following fields:
   
   * Release name: Your assigned ICP id (Ex: techu01)
   * Target name space: techu 
   * Git_repo Name:  your assigned github repository (Ex: techu01-icp-banking-microservices)
   
7. Click on **Install". 

   This will initate a build of the banking application from the github repository you specified. The process pulls the source code from your github repository and builds the docker image based on the Dockerfile. The resulted docker image, **techu/techunn-icp-banking-microservices** , will then be deployed to the ICP Docker image repository.
	
8. Click on **View Helm Release**

9. When the Pod Status is **Complete**, the process is done and the docker image is built successfully.

---

:thumbsup: Congratulations! Your banking application has been packaged into a Docker image using a DevSecOps approach! Ready to use it from IBM Cloud private?

---

# Step 3 - Instantiate the banking microservice from the IBM Cloud private catalog

The objective is to discover the IBM Cloud private catalog in order to instantiate a container from your Docker image containing your banking application. In this way, you will be able to test your banking application from ICp.

## Part 1 - Discover the Helm chart from the calalog


1. Access the Catalog. 

2. Locate the Helm Chart called **openmplbank**.

3. Click on the **openmplbank** chart.

3. click on **Configure** to create your container.

   ![alt text](images/icp-banking-microservices-config.png "ICp catalog")

4. Configure the container:

   ![alt text](images/icp-install.png "Banking service configuration")
	
   * Release name: Your assigned ICP id (Ex: techu01)
   * Target name space: techu 
   * Image:  techunn-icp-banking-microservices
	
5. Click on **Install**. 

6. Click on **View Helm Release**.

	

## Part 3 - Access your banking microservice

1. From the Helm release view, the container details are displayed.

   ![alt text](images/run-app-icp.png "Banking service configuration")
   
   * Click on **Launch** to display the banking microservice.

2. Test your application:
	
   ![alt text](images/icp-banking-app-test.png "Banking application")
   
   * Select a customer ID.
   * Please wait during the application calls banking data from the Mainframe through API Connect and z/OS Connect EE.
   * The result is displayed in a JSON structure.
    

---

:thumbsup: Congratulations! Your banking application has been instantiated from IBM Cloud Private as container. Your banking application succeeded to call banking APIs to call the Mainframe for banking business services.

---

# Licence

This code pattern is licensed under the Apache Software License, Version 2.  Separate third party code objects invoked within this code pattern are licensed by their respective providers pursuant to their own separate licenses. Contributions are subject to the [Developer Certificate of Origin, Version 1.1 (DCO)](https://developercertificate.org/) and the [Apache Software License, Version 2](http://www.apache.org/licenses/LICENSE-2.0.txt).

[Apache Software License (ASL) FAQ](http://www.apache.org/foundation/license-faq.html#WhatDoesItMEAN)

# Links

* [IBM Cloud private](https://www.ibm.com/cloud/private)
* [IBM Cloud private - Knowledge Center](https://www.ibm.com/support/knowledgecenter/en/SSBS6K/product_welcome_cloud_private.html)

[IBM ID]: https://www.ibm.com/account/us-en/signup/register.html
[API Developer Portal]: https://developer-contest-spbodieusibmcom-prod.developer.us.apiconnect.ibmcloud.com/
