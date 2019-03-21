# RMap Installation v.2.0.0-beta (brief)

Although the installation documentation for version 2.0.0-beta of RMap is not fully updated, it is possible to work through an installation by following the official documentation for installing each non-RMap component in the software stack and then configuring all of those components in the rmap.properties file. The following resources may be useful for supporting the installation process described below:
 * The [installation documentation for version 1.0.0-beta](installation-1-0-0-beta.md) contains detailed line-by-line instructions for most of the components (with the exception of Zookeeper, Solr, and Kafka, which all require standard installs). 
 * The [software stack document](software-stack.md) lists the versions and specific configuration requirements for most of the components.
 * The [installation scripts for v.1.0.1-beta](installation-scripts-1-0-1-beta.md) contain useful information for many components, and the actual line-by-line bash commands can be found in the [installation script github project](https://github.com/rmap-project/rmap-shell-installer) for many components. This project includes a backup and restore script for GraphDB databases.
 * The [Terraform project on GitHub](https://github.com/rmap-project/rmap-terraform) contains code for configuring AWS machines.

Here is an outline of the steps you could take to complete an RMap 2.0.0-beta installation:

1.  Install Java 8 and set that version as default for the user(s) that will be controlling Tomcat and other Java-based software.
2.  Install and start up Apache Tomcat with Apache Server in front. Important: use the version and configuration options described in the Software Stack document for [Tomcat](software-stack.md#apache-tomcat) and [Apache Server](software-stack.md#apache-server).
3.  Install and start up standard installs of Solr, GraphDB, Zookeeper, Kafka, and MySQL (on the same or separate machines). Kafka is only used to manage Solr indexing, so a cluster size of 1 will work for low activity implementations of RMap.
4.  Create an empty GraphDB database to point to.
5.  Run the [createTables.sql](https://github.com/rmap-project/rmap/releases/download/v2.0.0-beta/createTables.sql) on a new SQL database.
6. Download the driver for the SQL database you selected, and make sure there is a copy of it the `/tomcat/libs` folder.
7.  Get EZID credentials for generating ARK IDs, or set up a local NOID minter per the [instructions in the version 1.0.0-beta installation](installation-1-0-0-beta.md#id-minter) documentation.
8.  If you wish to allow users to login using Twitter, Google, and/or ORCID, you will need to sign up with each of these and retrieve the OAuth keys through their developer sites. For a production key, ORCID requires a membership and some interaction with their support team, but you can get a temporary key from you individual user account. If you do not wish to allow users to login with OAuth, you can disable this and use the admin tool (make sure you enable it in the `rmap.properties` file). 
9.  There is a sample `rmap.properties` file in the [GitHub release](https://github.com/rmap-project/rmap/releases/tag/v2.0.0-beta). Take a copy of this and place it at the path you configured in Tomcat as your `rmap.configFile` setting. This will override the default properties. It is annotated with instructions, and will allow you to point to all of the pieces you have configured. Any changes to the `rmap.properties` will require a Tomcat restart. 
10.  Take a copy of the api and webapp .war file from the [RMap release](https://github.com/rmap-project/rmap/releases) , rename them according to your preferred path name (e.g. "api.war" and "ROOT.war") and deploy them to the Tomcat `/webapps` folder.

If you become stuck on any of these stages, please contact the RMap team at rmap.project@gmail.com or log an issue against this rmap-documentation project.
