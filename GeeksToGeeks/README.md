# System Design Tutorial 
[System Design Tutorial](https://www.geeksforgeeks.org/system-design-tutorial/)  

### Gettign Started with System Design
__System Design__    
When making design decisions you have to keep the following in mind: 
1. Scalability
2. Reliability 
3. Availability 
4. Maintainability 

__System Desing Approach__  
1. __Break Down th Problem__  
Break down the problem into smaller components like Services or Feature. Ask the interviewer what feature he likes to add. 
2. __Communicate your Ideas__  
Discuss your process out loud. Use flowchats and diagrams.  describe how you have planned to tackle the problem of scalability, database etc. 
3. __Make Assumptions that make sense__  
Whether it be number of requests processes per day, number of database calls per month or efficiency rate of your cahcing system, you assumption must be reasonable and based on solid figures.    


__Reliability in System, Design__  
A system is Reliable when it can meet the end-user requirement.  
A reliable system is one that can serve all the features for which it was designed for.   

__Availability in System Design__    
Availability is  measure of the _uptime_ of the system. A high availability is required in order to serve the user's requests.  
For a Social Media Application a delay of 5 to 10 seconds may be tolerated hence a high availability may not be needed but for a hospitals, Data Center or Bank, you should ensure that your system  is highly available. Becuse a delay in the sevice can lead to a huge loss.  

To ensure the availability of your system:  
* Your System should not have a Single Point of Failure. Your system should not depend on a single service in order to process all of its requests.  
*  Detecting the Failure and resolving it at the point.  

__Scalability in System Design__    
Scalability refers to the ability of the System to cope with increasing load and hande it efficiently.  If you design a systemfor load X then you shoudl plan to design it for 10X and Test it for 100X.    
You shoudl compute the load that your systemwill experince. These depends on various factors:  
* Number of request processed per day. 
* Number of Database calls  
* Amound of Cache Hit or Miss requests 
* Number of active users. 

#### System Design Concepts   
1. __Load Balancing__    
A load balancer's job is to distribute to many different servers to help with throughtput,perfomace,latency, and scalability.  The load balancer route the incoming request across multiple web servers.  Loadbalancers are traffic managers and they take respinsibility for the availability and throughtput of the system.    
Some common load balancers include _Nginx_, _Cisco_, _TP-Link_, _Barracuda_, _Citrix_, _AWS Elastic Load Balancer_.  

2. __Caching__  
Caching is a good technique to reduce the number of database calls. 
If you need to reply on certain piece of data often then cache the data and retrive it faster from the memory rather than disk. This process reduces the workload on the backend servers and reduces the network calls to the database.  
Some popular caching services are _Memcache_, _Redis_ and _Casandra_.  
Some common Caching techniques include:  
* Using caching server on the backend e.g Memcache 
* Using a Content Delivery Network (CDN) to deliver static assets. e.g CloudFlare or Amazon CloudFront
* Using client storage to store response data e.g browser local storage.  

3. __Proxies__    
A proxy is a piece of hardware/software that sits between a client and another server.  A proxy server receives requests from the client and transmits it to the  origin server, thenit forwards tthe received response from the server to theoriginator client.  
The term `proxy` and `forard poxy` may be used interchangably.   

A `reverse proxy` acts on behalf of a server. A reverse proxy can be assigned a lot of tasks to help the main server and it can act as a gatekeeper, a screener, a load-balancer, and an all-around assistant.  

4. __CAP Theorem__   
CAP stands for _Consistency_, _Availability_ and _Partition tolerance_.   
The CAP Theorem states that you cannot archieve all the properties at the best level in a single database, as there are natural trade-offs between the items.  You can only _pick two out of three_ at a time. Which 2 you ick should depend on yur priority which in turn depends on your requirements.    
For example, if your system needs to be available and partition tolerant, then you must be willing to accept some latency in your consistency requirement.   
Traditional relational databases are natural fit for __CA__ side whereas Non-relational databases engines mostly satisfy _AP_ and _CP_ . 
* __CA__: Oracle, SQL Server, MySQL 
* __AP__: Cassandra, RIAK, CouchDB
* __CP__: MongoDB, HBasem, MemCache, BigTable, Redis 

___Consistency__ means that the read request will return the most recent write.  
__Availability__  means that non-responding node must respond in a reasonable amount of time.  
__Parition Tolerance__ means that the system will continue to operate despite network or node failure.  

5. __Database__    
* __Database Indexing__    
* __Replication__   
* __Sharding or Data Partitioning__   

#### Links
[Getting Started with System Design](https://www.geeksforgeeks.org/getting-started-with-system-design/)    
[5 Common System Design Concepts for Interview Preparation](https://www.geeksforgeeks.org/5-common-system-design-concepts-for-interview-preparation/)  
[Indexing in Databases | Set 1](https://www.geeksforgeeks.org/indexing-in-databases-set-1/)