
# System Design
### Stages of System Desgin 
__Step 1: Clarify the problem__  
Ask questions:
1. What are the features of the app / what are the use cases? 
2. How many users are expected / what is the likely traffic volume?
3. What is the average amount of data per request? 
4. How long does the data needs to be stored. 
5. How reliable does the system need to be?
6. How available does the system need to be?  

__Step2: Capacity Estimates__  
Using the infomation form step 1 to make rough estimates for things like storages and badwidth requirements.  
For example, you could multiply the number of users by the average request size and the amount of request each user is expected to make daily. 

__Step 3: Create a High Level Design__   
Draw out the core components of the system. For example: 
1. Load balancers
2. Web Servers
3. App Servers
4. Tasks Queues
5. Databases 
6. Caching 
7. File storage  

__Step 4: API Design__    
Write out some API endpoints for each component. 
If the interview question is very high level like "design Youtube", you can probably skip thid. part. 
But if you get a more focused question like "design YouTube's comment system", you may go more in depth. 

__Step 5: Create Database Schema__    
Weigh the costs and benefits of using a relational vs non-relational database.  
When modeling your data, account for things like potential data partitioning and replication.   

__Step 6: Details Component Design__   
You should show that you didn't just blindly make decisions and that you understand exactly what tradeoffs you were making.    
You should be able to propose alternare design decisions that could have been used and explain why you didn't use them. 



## Desing Patterns 
1. Creational Desing Patterns 
2. Structural Desing Patterns 
3. Behavioural Design Patterns 

__Creational Desing Patterns__  

__Strcutural Design Patterns__  

__Behavioural Desing Patterns__  

## Design Approach  
1. Top-down Design 
2. Bottom-up Desing 




## Amazon System Design Interview 
### Important Areas 
__Load__  
* How is the system scaled? 
* What breaks when the volumes increase? 
* How is the systemmeasures and monitored?  

__Correctness__  
* Does it satisfy the requirements?   
* How can the system fail?  
* Are there single points of failure?  

__Integration__   
* How are the components connnected? 
* What messages are interchanges?  
* How are messages interchanged?  
* What happens if a dependency breaks?  

__State__  
* Where does the state of the system live? 
* If stored on a server, what happens if that server dies? 
* If distributed across components, does the state have to be synchronized? 
* How does the CAP theorem apply to the system?  
* What needs to be stored?  
* What are the characteristics of the storage? 
* How does the storage scale? 
* How can the storage fail?  

__Common Mistakes in System Design__   
* Ypu should not focus on the technology but rather focus on the design. 
* Avoid rabbit holing. 
* Do not forget to discuss every tradeoff decision.  
* Do not forgetto askquestions to solve the right problem.   


__Example__  
