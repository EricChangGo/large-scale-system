# [Design Web Service] Scalability

## Scalability
* Writing notes for learning how to develop a large scale web service.
* Compare feartures, understand there pros and cons.

## Senario

> Before we started, try to imagine the product that your going to build and maintain. For example, one day the 5G has become pervasive, the user devices will be dramatically increase. People can easily enter your website by phones, watches, bags, and even on their autonomous vehicle.

## Key Problems
- Can your system handle large amount of requests in a short moment? - Load Balance
- What if a single point die in the system? - Replicate data, redundancy.

## Key Features
- Load balancer 

  We let the black box balancer do the data routing to the backend server S1, S2... (ex. Load balancing with BIND DNS server -> return different IP address)
    
  How to decide whom we should send the request to?
  1. File type according to url
  2. Server load
  3. Sequence (Round Robin)
    
    For instance, using nginx to set the routing round Robin [NGINX RR Setting](https://www.nginx.com/resources/glossary/round-robin-load-balancing/)

  - Software: NGINX, ELB, HAProxy
  - Hardware: Barracuda, Cisco, Citrix
  
  ***Q. What if a single load balancer goes down?***
  
  - (HA, High availability) Multiple load balancer 
    - Using heart beets to monitor each other
  
- Caches
  
  Our operating system might cache the response of the request. There might not be another DNS request every time we click a website. 

  - ***DNS TTL (time-to-live)*** is the expiration time for a DNS response. For example, power users often will have a long TTL that assign them to the same backend server, instead of reschedule them to a another backend server.
  - ***Sticky sessions*** lets user would not have to relogin if we implement sticky sessions, by cookie or store on user devices. When the user enter the system again, the load balancer will route the particular sssion to the same physical machine.
  - Consider a ***session state server***. Otherwise, for instance a php session superglobal load balancer store sessions on single machine. This will cause problem when assigning same user to other machines.
  
  - Etc. Apache - HTML, MYSQL cache, Memcached (Redis)

  ***Q. What if the caches goes down? What should we prepare for this?***
    - A. Using RAID to recover data lost.
    - B. Using multiple server for sharing data
      - FC, iSCSI (using internet protocal), MySQL, NFS

  ***Q. What information should we store in the user side cache (cookies)?***
    - Avoid reveal public IP. Try to encypt the important info.

  ***Q. What will we have to do if we run out of cache?***
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
		
        - MyISAM - No Storage limit, and with a table Locking granularity.
        - InnoDB - Row lock, enable transactions.
        - Memory
        - Archive - Compressed data, suitable for storing log files.
        - Cluster/NDB

  ![Storage Engines](https://i.imgur.com/fGG5dtt.png)
     
    
    - Master-Slave topology
    
      Also can implement load balance. For example, it's benefit to read heavy systems. When the read request comes it goes to specific databases.
    
    - Master-Master topology
      
      Avoid the only master goes down, we have mutiple master which implement both read and write.
     
    - Partition
      
      Might be a choice for general cases. For example, some user in server 1, some in server 2.
    