% Etherpad: Private Network Edition Users Guide
% Drafted: 7/17/09
% 

# Introduction                                                   
The Private Network Edition (PNE) lets you run EtherPad on your servers inside
your organization's firewall for the highest level of security and data
privacy.  Your organization retains full control over all information on pads,
and have full control over the configuration.

Installation of the Private Network Edition of Etherpad is intended to be
performed by a qualified system administrator. If this is not an option, we
highly recommend that you consider the 
[Professional Edition of Etherpad](http://etherpad.com/ep/about/pricing-pro)
instead.

## System Requirements                                            
Etherpad Private Network Edition requires Java Standard Edition Version 6
(JavaSE 6). This means EtherPad will run on almost any platform out there! We
officially support the following operating systems:

Operating System   Versions Supported                          
----------------   --------------------------------------------
Mac OS X           Mac OS 10.5.4 and later[^1]                 
Windows            Windows 2000, XP, and Vista                 
Linux              Red Hat 2.1+, Suse Linux 8+, Ubuntu 8+, etc.
Solaris            Solaris 8+                                  


Etherpad comes with it's own installation of a database engine (named
"Derby"), but if you plan to have more than 100 simultaneous users, you will
need to configure Etherpad to connect to a SQL database.  If you plan to use
an external MySQL database, you will need to use a MySQL server version 4.1
or later.

[^1]: J2SE 6.0 on Mac OS X 10.5 requires a 64-bit intel processor.

## Pre-Installation                                           
To install Java on any of the supported platforms, go to
[java.com/getjava](http://www.java.com/getjava/).

In order to import and export Etherpad documents, you will also need to
install [OpenOffice](http://download.openoffice.org/). Once installed, you
will need to configure EtherPad to use OpenOffice.

## About this Guide                                              

EtherPad installation and configuration varies slightly depending on the
platform. If you are not familiar with using the command line, we highly
reccommend using [Etherpad Pro](http://etherpad.com/ep/about/pricing-pro)
instead.

#  Installation                                                 
##  Quick-Start                                              

Start EtherPad by running the following command in the shell.

~~~~~~~~~
    java -Xmx512M -cp etherpad-pne-1.1.3.jar com.etherpad.main \
                --listen=8080 --listenSecure=8443
~~~~~~~~~

If all goes well, EtherPad will be serving on port 8080 (https on port 8443).
Note that if you want to run on the normal ports (80 and 443), then you will
have to run this command as the root user (on unix systems) or as an
Administrator (on Windows).

The usual way to shutdown the server is to send it the TERM signal (Control-C
from the shell). The server is designed so that if the process is restarted
quickly (within a few seconds), clients will reconnect without interrupting
users' typing.


## Configuration                                                 

There are a number of parameters that can be configured. A full list of server
options is available at the end of this document. The following sections
describe common configuration options in detail.

A given parameter foo can be given the value bar in two equivalent ways.

1.  By passing --foo=bar on the command-line. 
2.  By adding the line "foo = bar" to a configuration file, and passing
--configFile=[path-to-file] on the command line.

###  Port Setup                                               

When running Etherpad on a host already running a web-server, you will need to
configure it to use a different port. This is controlled by two separate
parameters: listen, and listenSecure, which corrispond to the HTTP and HTTPS
ports. When accessing an Etherpad server running on an HTTP port other than
the default port of 80, you will need to append ':' followed by the port
number to the end of the host name: for example, if you are running Etherpad
on corp.mycompany.com, on the HTTP port 8080, you will access etherpad in the
browser at the following url: http://corp.mycompany.com:8080 .

If you attempting to set up etherpad on a server configured with vhosts, you can
prepend the hostname, followed by a colon (:) to the --listen and --listenSecure parameters: for example --listen=mycorphost.com:8080 .

###  Wild-card Sub-domains                                   

**NOTE:** We strongly recommend you do this step.

For a significantly more responsive editing experience, you should modify the
DNS record for your server so that all "wildcard" subdomains point to the same
IP address as the main domain.

For example, if your are hosting it internally on etherpad.mycompany.com, then
make sure *.etherpad.mycompany.com points to the same IP address as
etherpad.mycompany.com.

If you have a systems administrator, then your best bet is to politely ask him
or her to perform this step for you. If you run your own DNS servers, the
configuration will vary based on which DNS server you have.

Once you perform this update to the DNS records, you can run the server with
the --transportUseWildcardSubdomains=true option. For example:

~~~~~~~~~
    java -Xmx512M -cp etherpad-pne-1.1.3.jar com.etherpad.main \
                --transportUseWildcardSubdomains=true
~~~~~~~~~

Please feel free to contact us at <support@etherpad.com> if you encounter
difficulty with this step.

The reason for this step is that some web browsers, specifically some versions
of Internet Explorer, impose very low connection limits on the client that
interfere with EtherPad's real-time experience. These connection limits, which
exist for historical reasons, are commonly worked around by real-time web apps
such as EtherPad by using multiple host names that resolve to the same server.

###  Enable File Conversion                             

EtherPad uses OpenOffice to convert documents to and from the Microsoft Word,
PDF, and OpenDocument formats. To enable import and export of these formats,
you must download the OpenOffice application and install it on the same
machine that runs your EtherPad server. EtherPad will automatically run
OpenOffice with the necessary options. Use the etherpad.soffice option to tell
EtherPad where to find OpenOffice's soffice command. For example:

~~~~~~~~~
    java -Xmx512M -cp etherpad-pn-1.1.3.jar com.etherpad.main \
        --etherpad.soffice=/usr/bin/soffice
~~~~~~~~~

If you're using Linux, keep in mind that many distributions contain OpenOffice
2, which doesn't support the new Microsoft docx format. To import docx files,
you'll need OpenOffice 3.

In Linux, OpenOffice requires an X server to run. If your server doesn't have
X installed, you may be able to avoid installing Gnome or KDE by using a
virtual X server like xvfb. To use xvfb with OpenOffice and EtherPad, set
'--etherpad.soffice=xvfb-run /usr/bin/soffice' (quotes necessary to keep the
space character in the command).

###  Alternative Database Engines                             

When you run EtherPad without specifying a database, it uses a bult-in
database engine called "Derby". This should be fine for small numbers of users
(fewer than 100), but for better server performance and data management you
may wish to connect EtherPad to a MySQL 5 database. To do this, set the
configuration variable etherpad.SQL_JDBC_URL and the database
username/password. For example:

~~~~~~~~~
    java -Xmx512M \
      -cp ./etherpad-pne-1.1.3.jar:mysql-connector-java-5.1.7-bin.jar com.etherpad.main \
      --etherpad.SQL_JDBC_DRIVER=com.mysql.jdbc.Driver \
      --etherpad.SQL_JDBC_URL=jdbc:mysql://localhost:3306/etherpad \
      --etherpad.SQL_USERNAME=etherpad \
      --etherpad.SQL_PASSWORD=PASSWORD
~~~~~~~~~

Note that you will have to download MySQL Connector/J and include it in your
classpath, as indicated above.

You will have to configure a MySQL database with the name specified in the URL
to accept connections from the username/password you provide. This database
will be used for all persistent data storage such as pad text, history, and
metadata.

There is currently no way to move data between Derby and MySQL.

###  Email Configuration                                          
Etherpad can send invite emails to other pads.  To configure the email account
these invites are sent from, you can pass in the SMTP server, username and
password used by the server using the smtpServer, smtpUser, and smtpPass
configuration options, respectively.  Note that if your SMTP server does not
require authentication, you may leave the smtpUser and smtpPass parameters
unset.

Parameter   Description
----------  --------------------------------------------------------------
smtpServer  Location of SMTP server for sending email invitations to pads.
smtpUser    (Optional) SMTP username
smtpPass    (Optional) SMTP password

###  LDAP Integration                                              
LDAP Integration

Etherpad now allows you to specify an external directory server for running
user queries. To configure etherpad to authenticate against an existing LDAP
server, set etherpad.useLdapConfiguration to a file containing the ldap
configuration file. This configuration file should look be a valid JSON
dictionary:

~~~~~~~~~
  {
    "url"           : "ldap://localhost:10389",
    "principal"     : "uid=admin,ou=system", 
    "password"      : "secret", 
    "rootPath"      : "ou=users,ou=system", 
    "userClass"     : "person", 
    "nameAttribute" : "displayname", 
    "ldapSuffix"    : "@ldap" 
  }
~~~~~~~~~

Note that this example file should work out of the box for a default
configuration of [Apache Directory Server](http://directory.apache.org/).

Here is a description of each of the configuration options:

------------------------------------------------------------------------------
Parameter            Description
-----------------    ---------------------------------------------------------
url                  An ldap:// url pointing to the server-name and port
                     number of the ldap server to connect to.

principle            The full path to the UID used to connect to the LDAP 
                     server. This is used to check if a given user exists on 
                     the system.

password             Password to the principle account

rootPath             A path to the DN entry parent to all user accounts.

userClass            The objectClass common to all user accounts -- only user
                     accounts with this objectClass will be allowed to 
                     authenticate.

nameAttribute        Attribute of a user account that specifies the full 
                     name of the user.

ldapSuffix           Due to the fact that all user accounts in etherpad are 
                     emails, a suffix is applied to each ldap username to turn
                     it into an email address. Users will be required to type
                     in this ldap suffix when logging in. For example, if a 
                     user 'alice' with an ldap account uid=alice wants to log
                     in, and ldapSuffix is '@ldap', she would log in with the
                     email 'alice@ldap'. This is convenient if ldap user
                     accounts always correspond to an email account on an
                     organizations network: if Alice is an employee at
                     'acmecorp.com', with email address 'alice@acmecorp.com',
                     configure the ldapSuffix to '@acmecorp.com', and she will
                     be able to log in with her usual email account.
------------------------------------------------------------------------------

### Single-Sign-On Support                                          

Etherpad Private Network Edition can be configured to integrate with your
organization's Single-Sign-On (SSO) configuration: however, it requires expert
knowledge of your organization's SSO mechanism. 

**Note:** Do not attempt to configure this option without an expert knowledge
of your organization's authentication system, as well as expert knowledge in a
scripting language such as python or perl.

To configure SSO authentication, ensure that etherpad is running in a
subdomain under an already existing SSO domain, so that the authentication
cookie is shared (so if you're SSO cookie is sent to all subdomains of
mycompany.com, make sure etherpad is being accessed from one of those
subdomains). Next you will need to write a small script that checks the
validitity of any of these cookies.

This script will be passed in a [JSON](http://www.json.org/) object of each
cookie sent from the browser each time a user attempts to access Etherpad. If
these cookies represent a valid user session, and that user is allowed access
to Etherpad, the script is expected to write a JSON object back to stdout with
identity information about the user. Specifically, the following keys are
expected:

------------------------------------------------------------------------------
JSON Object Key  Default Value Description
---------------  ------------- -----------------------------------------------
email            (required)    Email address for the authenticated user.
                               This key must exist in the json object in order
                               for the user to be authenticated.

fullname         "unnamed"     The full name of the authenticating user.

password         ""            A blank password will only allow the user to
                               log in via the SSO script.
------------------------------------------------------------------------------

Once your script is prepared, set the etherpad.SSOScript configuration option
to the location of your script, and make sure it has executable permissions
(chmod +x script on a unix system).

###  Other Configuration Options                           

If you have a license key from a previous installation of Etherpad, you may
use the etherpad.licenseKey option to point to the location of the licenseKey.
This option defaults to file:./data/license.key.

If you believe you have found a bug in your installation of Etherpad, it may
be benefitial to us if you included a verbose log as part of your bug report.
To enable verbose logging, set the verbose option to true.

###  Quick Reference                                         

A list of all the server configuration options. Default values, if they exist, are in _(italics)_

listen
:   _(80)_
:   Main port to listen for HTTP requests 
listenSecure
:   _(443)_
:   Main port to listen for HTTPS requests 
transportUseWildcardSubdomains
:    _(false)_
:    Set to true to enable much faster edit syncs. This requires configuring your DNS server as described above. 
sslKeyStore
:    _(built-in snakeoil cert)_
:    Path on disk to SSL keystore for HTTPS credentials. See the Java KeyTool Documentation. 
sslKeyPassword
:    **(Optional)** Password to the SSL certs in the keystore. 
etherpad.soffice
:    Command to run OpenOffice for Microsoft Word, PDF, and OpenDocument file conversion. 
etherpad.SQL\_JDBC\_DRIVER
:    _(org.apache.derby.jdbc.EmbeddedDriver)_
:    Java SQL Driver Class 
etherpad.SQL\_JDBC\_URL
:    _(jdbc:derby:etherpad;create=true)_
:    Host, port, and database name. 
etherpad.SQL\_USERNAME
:    SQL Username (for MySQL)
etherpad.SQL\_PASSWORD
:    SQL Password (for MySQL)
smtpServer
:    _(localhost:25)_
:    Location of SMTP server for sending email invitations to pads. 
smtpUser
:    **(Optional)** Some SMTP servers require a username. 
smtpPass
:    **(Optional)** Some SMTP servers require a password. 
etherpad.adminPass
:   Password to access admin pages. 
configFile
:   Properties file for storing configuration parameters. 
etherpad.licenseKey
:   _(file:./data/license.key)_
:   License key string (or file) that contains the license key. 
verbose
:   _(false)_
:   Set to true to enable verbose logging to the console. 



#  Using Etherpad Private Network Edition                         

Usage of Etherpad on a day-to-day basis is generally straight forward.  The
following sections will help you get acquainted with some of the more advanced
features of Etherpad PNE.

##  Startup                                                     

Once you have configured etherpad using all the applicable command-line flags,
hit return to run etherpad on the command line.  In the future we reccommend
storing all parameters in the configFile option, and many of our users prefer
to wrap the whole startup process with a shell/batch script.

## Administration                                                 

Administration of PNE is done through the Admin section, available to Admin
users upon logging in.  Each section allows configuration of various portions
of etherpad:

### Server Dashboard                                         

From here one can view usage statistics for etherpad.

### Manage License                                       

If you need to view or edit your EtherPad license, use this section.

### Manage Accounts                                            

From here you can manage the current accounts on your system. To create a new
account, click on the "Create new account" link. To grant Admin access to
other users, or to change their email and visible name, use the "Manange" link
next to each name.

### Recover Pad Text                                        

This section allows you to recover the text of a pad that you accidentally
deleted.

### Application Configuration                                

Using this section you can set the displayed server name, and default pad
text. If you need to restrict users to use only secure connections, you may
enable that in this pane as well.

## Using Etherpad

### Share                                                       

Share your pad with others using the Share Link at the top right of the pad
page. Click the arrow next to "or send an email invitation..." to send an
email invitation.

### Pad Options                                                

Configure a number of page-wide options from here.  Changing settings here will
affect everyone's view of the pad page.

### Import / Export                                             

Import rich-text format documents (from word, pdfs, etc) into Etherpad, and
export them back.

### Saved Revisions                                           

Use the save icon, or "Save Now" from the Saved revisions tab to create a new
saved revision of the pad. These will keep a snapshot of the pad around
forever.

#   Upgrading                                                   

## Version Numbering                                           

EtherPad PNE versions contain three parts: the major number, the minor number,
and the patch number. The format is: {major}.{minor}.{patch}.

Upgrades within the same major and minor version can be performed at any time.
For example, moving between 1.2.3 and 1.2.5 is not a problem.

Upgrades within the same major version number may be performed, but minor
version number upgrades must be performed consecutively. For example, you can
upgrade from 1.1.* to 1.2.*, but in order to upgrade from 1.2.* to 1.4.*, you
must first upgrade 1.2.* to 1.3.*, and then 1.3.* to 1.4.*.

## Performing the Upgrade                                     

To perform an upgrade, follow these steps:

1. Shut down the running EtherPad PNE server by sending it the TERM signal
(Control-C in the shell).
2. Important: Make a backup of the database! If you've configured MySQL you
may wish to use a tool such as mysqldump. If you're using the default Derby
database, make a backup of the data/derbydb directory.
3. Replace the old jar (etherpad-pne-OLD.jar) with the new jar
(etherpad-pne-NEW.jar), where OLD is the old version number and NEW is the new
version number.
4. Start the server back up with the same configuration parameters as before.

Database migration will be performed automatically on start-up. Once the
database has been migrated to accommodate a new version of the software, you
can no longer run an earlier version of the software on the same database.
