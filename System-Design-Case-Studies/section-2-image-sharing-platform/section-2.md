## Section 2: Design a Highly Scalable Image Sharing Social Media Platform

### System Design Step-by-Step Process
1. Functional Requirement (Set of features)
2. Non-Functional Requirements (Quality attribute of the system)
3. System API Definition
4. Software Architecture Diagram - For Functional Requirement
5. Software Architecture Diagram Update - For Non-Functional Requirement

#### 1. Functional Requirement
__Gathering Functions Requestions - Questions__  
* What kind of information do we need to store about each registered user?
* What type of media can the user share?
  - Only images?
  - Text and/or video in the future?
* What type of relationship do we have between users?
  - Bidirectional relationship? (automatic one-to-one friendship)
  - Unidirectional relationship? (Publisher and follows)
* What kind of operations can a user perform on the platform?


__In Scope__  
* When a user registers, they provide:
  - __Mandatory:__ First name, Last name, Email address, Password, Profile Image  
  - Optional: age, location, interest etc
* Users can
  - Post a new image
  - Search for other users by first name/ last name/ username etc
  - View other user's public info and share images
  - Follow / Unfollow other users
  - See a personalized timeline / newsfeed of the latest images, post by people they follow
    * The images need to be sorted by recency in a descending order
* Unidirectional relationship -
  - User A follows User B
  - User B may/may not follow User A


__Out of Scope__  
* Other post formats like text, video etc. But we can design the system to be able to accommodate this in the feature without too many changes.
* Other activities like reactions, comments, sharing etc.

#### 2. Non-Functional Requirements
Without the non-functional requirements, we may design a system that is functionally correct, but becomes completely useless for the workload we intend to use the system for.   

__Collecting non-functional requirements__   
* __Scalability__
  - Billions of active user (~ 1-2 Billion)
  - Hundreds of millions visits/day (100-500 Million)
  - Each user uploads ~ 1 image/day
  - Each image size ~2MB
  - Data Processing Volume: ~ 1PB / day
* __Availability__  
  - 99.99% uptime
* __Performance__  
  - Response time of < 500ms at 99pt (pt = Percentile)
  - Timeline / Newsfeed load time < 1000ms AT 99pt

#### 3. System API Definition
Base on the Sequence diagram our API will have the following interfaces:
* get_homepage() -> Registration Page
* register_new_user(firstname, lastname, username, password...) -> [user_id, auth_token]
* login() -> auth_token
* post_image(user_id, image) -> Confirmation
* search_user(name/lastname/username) ->  Page with Images
* follow_users(follower_user_id, target_user_id) -> Confirmation
* get_timeline(user_id) -> Images of all users, followed by User B

This is an abstract representation of the API which can be translated to a REST API or any other type of API before it can be implemented.  

#### 4. Software Architecture Diagram - Functional Requirements
__User Service vs Search Service__   
An _event driven architecture_ is used together with the _CQRS_ pattern to keep the users in the User Service in sync with the users in the Search Service.  
With this pattern, any registration or update of a new user on the User service will be published as an event to a message broker by the User Service while the Search Service will subscribe to the events to consume the data and updates it's users database record.  

__Followers Collection__  
We could have a Follower Service if we want to get granular with our MicroServices, but we can implement the feature by simple introducing a new collection in the User Service's database.
The new collection will represent a 1-to-1 mapping of target user to followers:
```
{"follower_user_id": "1", "target_user_id": "2"}
{"follower_user_id": "2", "target_user_id": "1"}
{"follower_user_id": "3", "target_user_id": "2"}
{"follower_user_id": "4", "target_user_id": "1"}
```

__Get User Timeline__  
We will use the _CQRS_ and the _Materialized View_ pattern to improve the performance performance of the timeline feature in the Timeline Service.  

Timeline Collection

User Id | Timeline - Sorted List of Post
--------|-------------------------------
user_a  | [post_by_user_b1, post_by_user_c1, post_by_user_b2, ...]
user_b  |
user_c  | [post_by)user_a1, post_by_user_a2, post_by_user_a3,...]

Every post record contains the image URL, the user Id of the author and it's timestamp.  

For this to be performant, we use an in-memory _Key-Value store_ to store the data for the Timeline Service store.

We will also use the event based architecture with a message broker to sync post messages from the Post Service to the Timeline Service. The Timeline Service will subscribe the the events.

Once the timeline service receives a event of a Post, it will send a request to the User Service to fetch all the followers of the User who made the Post.  
It will then add the post to the top of the _Timeline- List of Post_ of all the followers. The list will automatically push out the oldest post in the list.

Now when the user needs the Timeline, the request goes to the Timeline Service which already has a curated list of post ready to go.

The Downside the the Timeline Design is that the post will not appear in real time on the user's timeline because we only have _Eventual Consistency_.

#### Summary  
* Places images in an Object Store, instead of in a database
* Used the CQRS Pattern to separate:
  - Command - User registration / Updates go to the User Service
  - Query - Search operations go to the Search Service
* Used the CQRS + Materializes View Pattern to
  - Avoid:
    * Requests to Users Service + Post Service
    * Complex SQL Query
  - Provide a cached Timeline view for each user

#### 5. Software Architecture Diagram - Non-Functional Requirements
__Scalability__  
Now we iterate on the Diagram to address the non functional requirement which are the quality attribute of the system.  

We can scale our services by having multiple identical instances of the required service behind a _load balancer_ and the instance can increase in decrease dynamically as needed.    

Our users record are stored in a NoSQL database store. We may not be able to store Billions of user records in a single NoSQL database instance. So we use _Sharding_ to distribute the data across multiple physical instances.  
The simples way to _Shard_ data would be by using the _hash strategy_ applied on the user ID.  This will evenly distribute the users among as many shards as we went and if we keep getting more users, we can add more shards and redistribute the users among all of them. We must make sure that the `user_id` are randomly generated using a uniform distribution, otherwise, some shards may get overloaded with more data than others.,

Similarly, on the Followers collection, we can use the _hash strategy_ on the `follower_user_id`.

The Post Service users an SQL Database and we can _Partition_ the database using the Post Id across different database instances.
We must choose a Database type that supports this type of _Partitioning_.  

We use an Object store to store the images.
Object stores are particularly optimized for a high volume of data due to the fact that they run a distributed system of multiple instances under the hood and also their model where each object is basically a key/value pair which allows for very easy partitioning of data across multiple machines internally.

We may introduce an _Image Processing Pipeline_ to compress the images to an acceptable quality before being stored in the object store.  

__Availability__  
We do not need to do anything else to address high availability in our Services since we already have multiple instances running behind a Load balancer.
However, for our database instance, we need to have replication for our database instances so that if one database instance goes down, the other database instance can continue to support the system. 

### New Concept Learnt
#### The CQRS Pattern  
CQRS (Command Query Responsibility Segregation) is an architectural pattern that separates read and write operations into distinct models and pathways.
__Core Concepts__  
* __Commands (Write Side)__: Handle actions that change state (create, update, delete). They focus on business rules, validation, and data consistency.
* __Queries (Read Side)__: Handle requests that retrieve data without modifying system state. They return lightweight data transfer objects optimized for fast rendering.
* __Separation Level__: Can range from using different logical models on a shared database to completely separate physical databases (such as a relational database for writes and a NoSQL database for reads).

__Why Use CQRS__  
* __Independent Scaling__: Scale the read-heavy query side separately from the resource-intensive write side.
* __Optimized Performance__: Tailor database schemas, indexes, and caching strategies specifically for reading or writing.
* __Simplified Complexity__: Avoid awkward, overly joined database models trying to serve both fast reads and strict transactional writes.

__Drawbacks__  
* __Eventual Consistency__: Synchronizing changes from the write database to the read database via events or message brokers introduces a slight processing delay.
* __Increased Complexity__: Adds architectural overhead that is unnecessary for simple applications or basic CRUD operations.

#### The Materialized View Pattern
The Materialized View Pattern generates and stores precomputed data ahead of time to make complex queries fast and efficient.
__Core Concept__
* __Precomputation__: Instead of running heavy calculations, joins, or aggregations on the fly, the system computes the data once and saves the physical result in a table or cache.
* __Read Optimization__: Applications query this pre-built read-only view directly rather than hitting slow source tables or multiple normalized databases.
* __Disposable Nature__: The view is entirely derived data. If it gets corrupted or outdated, you can completely delete and rebuild it from the source of truth.  

__How It Works__  
* Maintenance: A background process, database engine, or event listener updates the view when the source data changes.
* Refresh Strategies: Updates happen incrementally (processing only new data), on a scheduled refresh (like a cron job), or on-demand manually.
* Isolation: Applications never write directly to a materialized view; they only read from it.

__Common Use Cases__  
* Dashboards and Reporting: Speeding up heavy analytics queries that aggregate millions of rows.
* Microservices: Storing a local, denormalized copy of data from other services to avoid slow synchronous network calls.
* Data Reshaping: Transforming data stored by a write-heavy key (like customer ID) into a format optimized for reads (like product ID).

__Trade-offs__   
* Data Staleness: The view can lag slightly behind the actual source data (eventual consistency).
* Storage Overhead: Storing duplicate physical data increases disk usage.
* Write Complexity: You must manage the pipeline or event triggers that keep the view fresh.


### Question for Consideration
1. When to use NoSQL versus RDBMS?
