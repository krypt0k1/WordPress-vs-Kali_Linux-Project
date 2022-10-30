WordPress vs Kali WK7 & 8 - Codepath Cybersecurity 

The goal of this assignment was to create a WordPress server to find and exploit vulnerabilities. Through Vagrant I succesfully created a working server with an with IP of 192.168.33.100 which was reachable by my Kali Linux machine. 

There were several attacks that could be discovered such as:	

XSS,
SLQI, 
User Enumeration,
Privilige Escalation,
and others

Admin rights were previously granted by Codepath. After loggin into the admin panel I started tinkering with some of the settings to see how WordPress works. I noticed that there was a section to create users to which I created 2 users, Tom Riddle & Harry Potter. Before logging out I tested for any Insecure Direct Object Reference(IDOR)on url http://wpdistillery.vm/wp-login.php?, came unsuccessful. While researching I found two WordPress vulnerability scanners, WPScan & WPSekku. 

User Enumeration Exploit :

WPScan was used with the following commands an d parameters:

wpscan --url 192.168.33.10 -e u, vp --api-token KTpzJJ3dlGP1tLHsDh3g19bmMmIAx3jF35B1lDO0lw8

Parameters legend:
-e u Enumerate Users
vp  Plugins
--api-token An API token was required prior to the scan, said token can be found by registering on wpscan.com. 

The scan returned several vulnerabilities and at the bottom it found the users I had recently created. 
![User Enum](https://user-images.githubusercontent.com/111711434/198854367-7f60c115-e869-47f0-ab40-86406e318ec3.gif)


XML-RPC Exploit 

After performing the scan, I noticed that XML-RPC was left enabled for this WordPress server. 
![image](https://user-images.githubusercontent.com/111711434/198854816-36c9caf2-84a0-4573-827f-5f92b422871d.png)

I then visited the page in question '192.168.33.10/xmlrcp.php' to which it responded with: " XML-RCP Server accepts POST request only"
![image](https://user-images.githubusercontent.com/111711434/198854974-23dc802e-6b7d-4007-b997-eb2734cb3018.png)

From there I loaded up up Burp Suite and sent the GET request to Repeater and changed the request method to POST. Upon submitting the POST request I was fed the following response: 

![image](https://user-images.githubusercontent.com/111711434/198855024-7b25fc7f-3748-480c-ad30-c624c0165163.png)

XMLcan be inserted onto the POST request as seen below.
![image](https://user-images.githubusercontent.com/111711434/198856171-279cb7a1-7441-43da-9bbd-3efff3f0650d.png)


The following requested revelead all available services on the server. 
![XML-RPC Exploit Services](https://user-images.githubusercontent.com/111711434/198855198-b15a7104-d735-4854-af41-838b11763d39.gif)



After trial an error with some services I noticed that wp.getUserBlogs could be fed username and password parameters that could possibly exploited for user authentication. There were no time-outs for any number of tries made. 
![image](https://user-images.githubusercontent.com/111711434/198855961-63031c51-8535-4628-ba90-a3c202615dc8.png)

The GIF below demonstrates how XML-RPC can be exploited to obtain user credentials. Admin credentials are confirmed by the <name>(user) as isAdmin and the boolean check set to 1.  
![XML-RPC Exploit Admin ](https://user-images.githubusercontent.com/111711434/198855985-ed866247-f082-4678-8ccf-de34baa6976f.gif)


Sources used for this exploit:                            																																																	

WPScan                     

http://codex.wordpress.org/XML-RPC_Pingback_API
https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

External:                                                                                                                                  
https://codex.wordpress.org/XML-RPC/system.listMethods#Availability																																													
https://www.javatpoint.com/xml-example




