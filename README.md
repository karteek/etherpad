# Karteek's Etherpad Fork
### Package
* Etherpad source from http://code.google.com/p/etherpad
* Some minor fixes/changes here and there
* Oo.org integration for file exports and file imports from DOC/RTF format
* Commented code for File imports due to license restrictions

### How to
1. Grab the code from github
	$ git clone git://github.com/karteek/etherpad.git
2. Set up the environment in env.sh and source the file. The settings inside are 
from my MBP. Do update them to meet your machine's settings.
	$ source env.sh
3. Create a database "etherpad", add a user "etherpad" with password "password" on a MySQL database server. Grant all privileges for on database etherpad to etherpad@localhost
4. Update etherpad.SQL_JDBC_URL, etherpad.SQL_USERNAME, etherpad.SQL_PASSWORD and
etherpad.adminPass in the file "etherpad/etc/etherpad.localdev-default.properties"
5. Change dir to etherpad, compile the jar and start the server
	$ cd etherpad
	$ bin/rebuildjar.sh
	$ bin/run-local.sh
6. You are done. Visit http://localhost:9000 to check your instance

### How to Enable File Imports
1. File Imports depend on cos.jar (http://www.servlets.com/cos/) - com.oreilly.servlet
2. Do read the license http://www.servlets.com/cos/license.html
3. Even now, if you want to continue with imports just apply http://gist.github.com/273808 or
	* Download cos.jar from the site and copy it to infrastructure/lib folder
	* In infrastructure folder, find out the files, where imports functionality is commented
		$ grep -R "REMOVED_COS_OF_COS" *
	* Uncomment and recompile JAR

### How to Enable Exports
1. Make sure that there is a "etherpad.soffice" entry in the file
"etherpad/etc/etherpad.localdev-default.properties"
2. You need Openoffice.org to be running on your machine as service. It usually on linux is
	$ /path/to/openoffice/program/soffice.bin -headless -nofirststartwizard 
	           -accept="socket,host=localhost,port=8100;urp;StarOffice.Service"
3. Check the file "infrastructure/com.etherpad.openofficeservice/importexport.scala" 
for more info

### How to deploy the same on example.com
1. Edit your dns and point *.example.com to the same server where example.com resides
2. Make sure that your SMTP is working
3. Open etherpad/src/main.js, and update domain in line #273
4. Open etherpad/src/etherpad/globals.js and change the domain in variable 
SUPERDOMAINS found at line #30
5. Open etherpad/src/static/crossdomain.xml and add your domain to crossdomains.xml
6. Look into the src folder and update your domain where etherpad.com is used (especially emails)
	$ grep -ir "etherpad.com" *
and update domain in all the files.
7. Open etherpad/etc/etherpad.localdev-default.properties and update
	* devMode to false
	* etherpad.isProduction to true
	* listen to example.com:80
and change passwords too.
8. Now start the server. You will need root access on OS X to bind to ports < 1000.
	$ bin/run-local.sh

### How to do the same on example.com but with a reverse proxy on Apache
1. Do steps 1 thru 7 in above. But dont update listen as told in 7, rather update
listen to example.com:9000, and set hidePorts to true
2. Create a vhost entry. It might look like .. http://gist.github.com/273814
3. Restart apache, and things should work

