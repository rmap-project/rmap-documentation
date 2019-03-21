# Installation Script v.1.0.1-beta
**_Note: This installer script has not been updated to reflect the latest release, which now includes an indexer integration (2.0.0-beta). Please consult the [RMap project on GitHub](https://github.com/rmap-project) for updates. There you will find a Docker instance, an integration environment, and some other notes that may be of use until this document is updated. If you have any questions, feel free to contact the team at rmap.project@gmail.com._**

This document describes the use of the [RMap](http://rmap-hub.org) installer scripts, which provide an easy-to-use mechanism for creating and maintaining the components that make up an RMap server. The scripts currently install version 1.0.1-beta-SNAPSHOT of the RMap server components.

-   [Introduction](#introduction)
-   [Requirements](#requirements)
-   [Configuring](#configuring)
-   [Installing Components](#installing-components)
-   [Installing a Certificate](#installing-a-certificate)
-   [Testing the Installation](#testing-the-installation)
-   [Backing Up and Restoring the Triplestore](#backing-up-and-restoring-the-triplestore)

## Introduction
An RMap installation consists of three main parts:
-   The RMap web applications, which run in [Apache Tomcat](https://tomcat.apache.org/) and require supporting functionality like the [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) and [NOID](http://search.cpan.org/dist/Noid/noid).
-   An [Ontotext GraphDB](http://graphdb.ontotext.com/) server, a [triplestore](https://en.wikipedia.org/wiki/Triplestore) that contains the [DiSCO](http://rmap-project.atlassian.net/wiki/spaces/RMAPPS/pages/3244036/RMap+DiSCO+Media+Type) data.
-   A [MariaDB](https://mariadb.org/) server that contains user registration data.

These components can be installed on the same computer or on separate computers, but their installations must be coordinated so that the components can find and communicate with each other. The RMap installation scripts provide an easy way to configure and perform an initial installation of all components. They can also be used later to update component installations as needed.

## Requirements

Before running the installer scripts, several requirements must be met to provide a suitable environment for the RMap server components.

### Computer

The physical or virtual computer(s) that host the server components must have sufficient processing ability and memory to support the servers during times of peak load. This requirement will obviously be dependent on the expected database size and number of users for a given installation. While insufficient processing power will merely result in longer response times, insufficient memory can lead to catastrophic failure of one or more system components. At present, the minimum amount of RAM for a new (empty) installation appears to be 2 GB, though in practice an active system is likely to require more RAM than this.

For cloud-based systems, the computer that hosts the RMap components and Tomcat must open ports to allow HTTP (80) and HTTPS (443) communication. Additional requirements for secondary servers are outlined below.

### CentOS

The installation script development and testing was targeted only at the [CentOS 7](https://www.centos.org/) Linux operating system. While it may be possible to adapt the scripts to similar OS types and versions, it is strongly recommended that CentOS 7 be used for an RMap installation. The scripts were tested on [Google Cloud](https://cloud.google.com/) and [Amazon Web Services](https://aws.amazon.com/) using their most popular CentOS 7 images.

### Download Scripts

The installation scripts should be [downloaded from GitHub](https://github.com/rmap-project/rmap-shell-installer) onto all computers that will be hosting RMap components. They can be placed in any convenient location, and that location must be the current working directory from which the scripts are executed.

### User Account

The installation scripts will create new files under `/rmap` on the computer system, and those files will be owned by the user that ran the scripts. That user must have `sudo` privileges on the computer. If desired, a new user `rmap` can be created using an included script:
```
>sudo ./create_rmap_user.sh
```
This script must be run by a `sudo` user, will prompt twice for the new user's password and will assign `sudo` privileges to the new user.

### Domain Name

To support both HTTPS access to the server components and [OAuth 2.0](https://oauth.net/2/) authorization for user registrations, every computer that hosts an RMap component must be reachable through a stable domain name. These names will be specified during the [configuration process](https://rmap-project.atlassian.net/wiki/spaces/RMAPPS/pages/67698689/Using+the+Installer+Scripts#UsingtheInstallerScripts-DomainNames) before the scripts are run.

### Public Key Certificate

To support both HTTPS access to the RMap server components and OAuth 2.0 authorization for user registrations, the computer hosting the RMap components must have a [public key certificate](https://en.wikipedia.org/wiki/Public_key_certificate) installed in its Tomcat keystore. This certificate can be supplied by the organization that is hosting the server or can be generated using an RMap installation scripts, as [described below](https://rmap-project.atlassian.net/wiki/spaces/RMAPPS/pages/67698689/Using+the+Installer+Scripts#UsingtheInstallerScripts-CreatingaCertificate). The certificate must be stored in a PFX file. A separate script, [described below](https://rmap-project.atlassian.net/wiki/spaces/RMAPPS/pages/67698689/Using+the+Installer+Scripts#UsingtheInstallerScripts-AddingaCertificatetotheKeystore), can be run to install the certificate into the keystore.

### OAuth 2.0 Credentials

In order to enable OAuth authorization for user registrations, an account must be created with each of the desired OAuth providers that are integrated into RMap. These accounts must be configured so that requests for user identification that come from the RMap server will be processed correctly. The RMap web interface allows users to identify themselves using accounts from [Google](https://developers.google.com/identity/protocols/OAuth2), [ORCID](https://members.orcid.org/api/oauth2) and [Twitter](https://dev.twitter.com/oauth). Each of these OAuth providers will require a separate and different configuration in order to work. The specific example of using Google OAuth is outlined here.

#### Setting up Google OAuth

Assuming you already have a [Google account](https://accounts.google.com/SignUp?hl=en), do the following:

-   Sign in to the [Google Cloud Platform](https://console.developers.google.com).
-   From the dropdown in the top toolbar, [create a new project](https://cloud.google.com/resource-manager/docs/creating-managing-projects) for use with your RMap server.
-   Enable the Google+ API, which provides the user login functionality:
    -   In your project's dashboard, on the left under APIs & Services, select Library
    -   Under "Social" on the left, click Google+ API, then click "Manage".
    -   In the toolbar at the top click Enable
-   Create a client ID, which RMap will use when requesting an OAuth login:
    -   In your project's dashboard, on the left under APIs & Services, select Credentials
    -   In the Credentials tab, find the Create Credentials dropdown and select OAuth Client ID
    -   Select the Web Application radio button, give the ID a name and click Create
    -   From the popup dialog, copy your "client ID" and "secret" and save them somewhere safe.
-   Add a redirect URI, which is the address Google will call back to after a login attempt:
    -   In the Credentials page, click the name of your OAuth Client ID
    -   Enter an Authorized Redirect URI of the form: `https://[yourrmapserverdomain]/user/googlecallback`.
    -   Click Save, which returns you to the Credentials page.
-   Add a domain verification, which specifies a computer from which login requests are allowed:
    -   In the Domain Verification tab, click Add Domain.
    -   Click Add Domain. In the popup dialog, enter the URI of your RMap server and click Add Domain.
-   Optional: Customize the appearance of the page Google shows when users log in from RMap:
    -   In the OAuth Consent Screen tab, add a product name, homepage URL and/or logo image, etc.
    -   Click Save to commit the changes.

## Configuring

Before running any of the scripts that install server components, the installation process must be configured by editing the file `configuration.sh`. The property values in this file will be used when installing various components. Those values should not be changed between the execution of different scripts or before any script is re-executed to perform an upgrade. The same configuration file should be used on any computer where server components are being installed.

Some of the configuration values will also be copied to the file /rmap/properties/rmap.properties, which is created when the RMap installation script is run. This file is read whenever the Tomcat server is restarted and the RMap web apps are reinitialized.

### Domain Names

At the top of `configuration.sh` there are three properties for specifying the domain names of the server components. The `TOMCAT_DOMAIN_NAME` property is the public facing address for the RMap web site. If the GraphDB and MariaDB components are also hosted on the same computer as the RMap components, `localhost` can be specified as their domain name. These domain names must be up and working before running the installation scripts.

### Keystore Configuration

The `KEYSTORE_PASSWORD` property specifies the password that will be used when the new Tomcat installation's keystore is first created. This password can be used to access the keystore through command line programs like the [JDK's `keytool`](https://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html).

The security certificate for Tomcat that is added to the keystore will use name `KEY_NAME` and `KEY_PASSWORD`. The security certificate will be installed from the file specified by `CERTIFICATE_PATH`. If an existing certificate is being used it should be placed in this location, otherwise a file will be generated in this location by an installation script, as [described below](https://rmap-project.atlassian.net/wiki/spaces/RMAPPS/pages/67698689/Using+the+Installer+Scripts#UsingtheInstallerScripts-CreatingaCertificate).

The passwords for both the keystore and the certificate key must be at least six characters long.

### Database Names, User Names and Passwords

The properties beginning with `MARIADB_` and `GRAPHDB_` specify the database names, user names and passwords that RMap will use to communicate with the database servers. If installer scripts are being ued to install these components, the named databases and users will be created on the servers. If the specified passwords are changed on the database servers after the installation scripts have been run, they must also be changed in the file `/rmap/properties/rmap.properties`.

### Backup and Restore of Triplestore

The properties in this section have no impact on installations, only on the behavior of the scripts that back up and restore the contents of GraphDB. They can be changed as needed between invocations of those scripts. `GRAPHDB_BACKUP_PATH` specifies the local path where backup files will be created and in which uploaded files will be sought. If it does not exist, it will be created when a backup is performed. `GRAPHDB_URL` points to the root of the GraphDB server and can be left at its default value unless the RMap installation is working with a pre-existing GraphDB server.

The `GRAPHDB_BACKUP_MIME_TYPE` and `GRAPHDB_BACKUP_FILE_EXT` values determine the data type and name extension of the file that is saved when a backup is performed. The GraphDB documentation is a bit inconsistent on this matter, but [this list of MIME types](http://graphdb.ontotext.com/documentation/6.6/standard/quick-start-guide.html#supported-export-download-formats) can be a guide to available export formats.

### OAuth 2.0 Credentials

The `GOOGLE_OAUTH_` properties should contain the "user ID" and "secret" values that were provided during the creation of the Google OAuth client ID. This is the only OAuth mechanism that is included in the configuration script. To specify the user ID and secret values for ORCID and/or Twitter, the `rmap.properties` file must be edited directly. This editing is best done to the file in the installation scripts folder _before_ any installation scripts are run, which will allow the changes to be maintained even if the RMap component installation script is run again.

## Installing Components

Once the configuration is complete, the installation scripts can be executed to create the server components. The easiest type of installation is to put all components on the same computer, but it is possible to install GraphDB and MariaDB on separate computers from the one that hosts the RMap components and Tomcat.

### All Components on One Server

To install all components on one computer, execute this command:
```
>sudo ./install_all.sh
```
The script will report its progress as it installs the various components. All of the visible reporting, as well as lower level output from each component, is also accumulated in the file `installer.log` in the installer script folder. In case of errors, be sure to check this file for more detailed information. When the script completes, the RMap and GraphDB web sites should be available via HTTP only (not HTTPS).

### Components on Multiple Servers

To install either or both the GraphDB and MariaDB components on different servers from the RMap components and Tomcat, first ensure that the same `configuration.sh` file is present on all computers where components will be installed. It is recommended that the database components be installed before the RMap components, with either or both of these commands:
```
>sudo ./install_graphdb.sh
>sudo ./install_mariadb.sh
```
On the system that will host the RMap Components and Tomcat, instead of running the script to install all components, first run any database installer scripts that were not run on the other system(s) and then run:
```
>sudo ./install_rmap.sh
```
Note that for such an installation to work on cloud-based systems, the security protocols must be set up to allow the following communication:

-   For a separate GraphDB server, open port 7200 to the RMap server's computer.
-   For a separate MariaDB server, open port 3306 to the RMap server's computer.

Opening these ports might also be desirable on single-system installations in order to use the GraphDB web dashboard and to access the MariaDB database using programs like [MySQL Workbench](https://www.mysql.com/products/workbench/).

### Installation Upgrades

The RMap installation scripts can be rerun in cases where the installation has become corrupted or new versions of the scripts have been released. If the version of a component is has not changed, a minimal refresh of its installation conditions will be performed. When re-installing GraphDB, the current installation is backed up and its databse is restored after the GraphDB component is updated. Note that if the GraphDB reinstallation fails for some reason, the backup copy of the GraphDB installation will be lost when the reinstallation is attempted again, destroying its copy of the existing database. In this situation, an administrator will need to manually make a copy of the database and restore it after reinstallation.

### Information About GraphDB

The installer scripts install GraphDB under the system's `/rmap` folder. They also create a service for GraphDB so that it is restarted when the computer reboots. The GraphDB server provides a web interface that can be used to examine and adminster the database and user profiles. This web site can be accessed by HTTP only (not HTTPS) at port 7200 on the system where the server is installed.

### Information About MariaDB

The installer scripts perform some common security measures after installing MariaDB (setting the root password, deleting the temporary user and removing the test database).

### Information About Tomcat

The installer scripts install Tomcat under the system's `/rmap` folder. They also create a service for Tomcat so that it is restarted when the computer reboots. Firewall settings open the ports for HTTP and HTTPS traffic and then redirect them to Tomcat's default ports of 8080 and 8443, respectively. Tomcat's administration page can be accessed at `/tomcat` on the server.

### Information About RMap Components

All RMap components are installed as web apps in Tomcat. The RMap web administrator site is the home page of the server. The [RMap API](https://github.com/rmap-project/rmap-documentation) is reachable at `/api` on the site.

## Installing a Certificate

The RMap server installation requires that a security certificate be added to the Tomcat keystore in order to provide HTTPS access to the site and to support OAuth user logins. Administrators can provide an existing certificate or generate one using one of the installer scripts. In either case, the certificate must then be installed into the keystore using another installer script.

### Providing a Certificate

An existing security certificate may be placed in the file location specified by the `CERTIFICATE_PATH` property of the `configuration.sh` installation script. The certificate must be in the PFX format. Certificates in other formats can be converted to PFX using tools like [openssl](https://www.openssl.org/).

### Creating a Certificate

If there is no existing security certificate, a new certificate can be generated using free functionality from [LetsEncrypt](https://letsencrypt.org/). After the RMap components have been successfully installed, and with Tomcat running, execute the following command on the system hosting Tomcat:
```
>sudo ./create_letsencrypt_cert.sh
```
This script will install and run `[certbot](https://certbot.eff.org/)`, which asks several questions about the user for whom the certificate will be generated. After collecting information, it causes a web request to be made against the Tomcat server and if this succeeds, the certificate file is generated. The script copies the certificate to the `CERTIFICATE_PATH` location specified in `configuration.sh`.

### Adding a Certificate to the Keystore

Once a certificate file is in place, the administrator can install it with this command:
```
>sudo ./install_certificate.sh
```
This script adds the certificate to Tomcat's keystore, after which HTTPS access to the site should work and it should be possible for users to log in using the OAuth providers.

## Testing the Installation

Once the installation scripts have completed successfully, it can be useful to verify the installation and functionality. The first level of verification involves visiting the RMap web site to ensure that the components are accessible. A secondary verification can be performed by adding test data to the site, which exercises the interactions between components. After running such a test, it may be desirable to remove the data that was created by the test.

### Visiting the Web Site

This test is best performed from a computer other than the ones that are hosting the RMap components. If any of the following URLs cannot be reached and the components are installed on cloud-based systems, check the security rules for those systems to ensure that all necessary ports are open. In the following examples, `yourdomain.com` represents the domain name where the RMap components are installed and it is assumed that all components are installed on the same system.

-   Visit http://yourdomain.com to test that HTTP access is working and that Tomcat and the RMap account manager are running.
-   Visit https://yourdomain.com to test that HTTPS access is working.
-   Visit http://yourdomain.com/api/discos to test that the RMap API is working. The page should display the text `{"description":"Follow header link to read documentation."}`
-   Optional: Visit http://yourdomain.com:7200 to see the GraphDB dashboard. This test requires that port 7200 be open.

### Test MariaDB by Creating a User and ApiKey

An registered RMap user can create an API key, which can be used to alter the triplestore contents through the API. The easiest way to create a new ApiKey is through the web interface using an OAuth login such as Google:

-   Visit https://yourdomain.com and click on "Sign in" in the upper right, then choose "Sign in with Google"
-   Enter information for a valid Google user (unrelated to the Google user from the OAuth client ID steps above) and click "Sign up".
-   If the sign-in is successful, in the "Step 1" section of the resulting page, click "Settings Page".
-   Under "Synchronize: RMap Agent", select "Yes" and click "Save Changes".
-   In the dropdown at the upper right, select "Manage API keys".
-   On the next page, click "Create new key".
-   In the next page, enter "Test" for Key label and Key description, select Status = ACTIVE and click "Save".
-   In the next page, click download to save the new Test key to the file `rmap.key`. This will be used below when creating a DiSCO.
-   To view the changes in MariaDB, open a shell on the computer where MariaDB is installed.
-   Start the MariaDB command line client using the `mysql` command and the root password you provided in the installer configuration. Then, view the contents of the `rmap` database's `Users` or `ApiKeys` tables to see the user or ApiKey entries that were created.
```
> mysql -s -u root -prmap
MariaDB [(none)]> use rmap;
MariaDB [rmap]> select * from ApiKeys;
```

### Test GraphDB by Adding a DiSCO through the API

With the API key produced in the previous test, it is possible to upload existing DiSCO data to the triplestore through the RMap API.

-   These steps are performed in a shell in a folder containing the rmap.key file from the previous tests.
-   Download the sample data file [discoA.rdf](https://github.com/rmap-project/rmap/blob/master/testdata/src/main/resources/discos/discoA.rdf) to the same directory.
-   Issue a command like the following Linux POST, which uses the credentials from `rmap.key` to upload th file `discoA.rmap` to RMap's triplestore.
	```
	> curl -u $(<rmap.key) -X POST -d @discoA.rdf --header "Content-Type:application/rdf+xml" https://<yourdomain>/api/discos
	```
-   If successful, the command will print the ID for new DiSCO, in the form `rmap:<id>`.
-   In a web browser, view the new DiSCO at `http://<yourdomain>/discos?uri=rmap:<id>`.

### Cleaning Up After the Tests

If the previous two tests are performed, possibly unwanted data will have been added to the MariaDB and GraphDB databases. Use the following steps to clean up those databases after these tests.

-   In a shell on the computer that hosts MariaDB, use the `mysql` command as before to delete the entries that were added to tables in the `rmap` database.
	```
	> mysql -s -u root -prmap
	MariaDB [(none)]> use rmap;
	MariaDB [rmap]> delete * from ApiKeys;
	MariaDB [rmap]> delete * from Users;
	```
-   If the GraphDB dashboard has been made accessible by opening port 7200, visit its web api interface in a web browser at `http://<graphdbdomain>:7200/webapi`.
-   If necessary, log in with the user name and password set up during configuration (by default, `rmap/rmap`).
-   In the `RDF4J API` section, expand the `sparql:SPARQL` section and the `DELETE` command within it.
-   Enter "rmap" as the repositoryID, leave the remaining entries blank and click "Try it out!".
-   Visit the GraphDB dashboard at `http://<graphdbdomain>:7200` and select the `rmap` repository. The details display should indicate that the repository contains zero "statements".

## Backing Up and Restoring the Triplestore

Several scripts provide the ability to take snapshots of the GraphDB database and upload such snapshots to replace the contents of the database.

### Backing Up the Triplestore

Running the script `backup_triplestore.sh` will download the full contents of the database specified by the configuration property `GRAPHDB_DBNAME` into a file in the folder specified by `GRAPHDB_BACKUP_PATH`. Any missing folders in the path will be created. The file name will include the database name and the current date and time and have content type and a file name extension as specified in the file `configuration.sh`.

### Online Restore of the Triplestore

An RDF file can be uploaded to a running GraphDB instance using the `restore_triplestore.sh` script. The script takes one parameter, the name of the file to upload, which must exist in the folder specified by `GRAPHDB_BACKUP_PATH`. The script first clears the contents of the database, then requests an upload of the specified file and then terminates. The progress of the upload can be monitored using the Import tab of the GraphDB web console (`/import`).

This upload mechanism is best used with files of moderate size. Uploading a very large file can require substantial heap size or the upload will fail. Additional heap space can be specified using the `GRAPHDB_HEAP_SIZE` property in `configuration.sh`. Additionally, since the database's index is updated throughout the process, the upload of a very large file can take a long time to complete. An alternative to uploading large files directly is to break them into multiple smaller files using the Linux `split` command and then uploading each of those files in turn.

### Offline Restore of the Triplestore

An alternative to the online upload mechanism is the script `restore_offline.sh`. Here, GraphDB is stopped, its LoadRDF utility functionality is used to upload the data and then GraphDB is restarted. It is invoked in the same way as the online script, with the single argument being the name of the file to upload. This script prints progress information to the console. This form of uploading is much faster than the online version, but can suffer from memory limitations as LoadRDF incurs a sudden increase in memory in its final stages of commiting the uploaded statements. The `GRAPHDB_HEAP_SIZE` property controls the heap size for LoadRDF.