## System Design
### Reservation and Payment System for a Packing Garage

#### Clarify the requirements
* A single garage or multiple garages?
* Can reserve a packing spot
* Receive a ticket after reservation
* Pay for you packing spot
* Two people cannot reserve the same packing spot (System must have a high consistency)
* Are all packing spot the same?
  * There could be larger spots?
  * There could be smaller spots?
  * There could be handicap spots?
* Would there be flat rate, rate based on time, different rate for different spot?
* Should be Web App or mobile app.
* Consideration of scale:
  * If there are more than one garage, like how many?
  * How many spot per garage


#### Product requirement
* Need to handle multiple garages
* Need to be able to reserve a spot
* Need to be able to pay for the spot
* Need to get a ticket after reservation
* System needs to have a high consistency - no two people should be able to reserve the same spot at the same time.
* Need 3 types of spots: regular, compact and large
* Needs a flat rate but different rates depending on type of packing spot. And flat rate is based on time.
* A packing garage may have about 200 spots per flow
* A packing garage may have up to ten floors
* We may have about 500 Garages

#### Define the Subinterfaces and APIs

__Public Endpoints__  
The API does not necessarily have to be a restful API, it could be a GraphQL Server.

  Endpoint  |          Params              |       Returns         | Note
------------|------------------------------|-----------------------|-------
/reserve    | garageId, startTime, endTime | spotId, reservationId |
/payment    | reservationId                |                       | Payment processing will be handled by a third party.
/cancel     | reservationId                |                       |

__Internal Endpoints__  

  Endpoint         |          Params              |       Returns        | Note
-------------------|------------------------------|----------------------|--------
/calculate-payment | reservationId                |                      |  
/free-spots        | garadeId, vehicleType, time  |                      | Smaller vehicle may fit into larger spot.
/allocate-spot     | garadeId, vehicleType, time  |                      |
/create-account    | email, password, firstname(opt) lastname(opt) |     | Single Sign-on option may be used
/login             | email, password              |                      |

#### Consideration of Scale
The system may have up to 2000 packing spot per garage. Even if there is a million garage, it doe not fall in the realm of big data.
This scale of the system does not warrant a distributed system design.

#### Database Schema Design
For this type of problem, a relational database may be used.

__Reservations__

  Field        | Type           | Key          
---------------|----------------|-------
reservation_id |   int          | Primary Key
garage_id      |   int          | Foreign Key
sopt_id        |   int          | Foreign Key
start_time     | timestamp      |
end_time       | timestamp      |

__Garages__   

  Field      | Type    | Key
-------------|---------|--------------
garage_id    | int     | Primary Key
zipcode      | varchar |
rate_compact | float   |
rate_regular | float   |
rate_large   | float   |

__Spots__   

  Field      | Type | Key  
-------------|------|---------
spot_id      | int  | Primary Key
vehicle_type | enum |
status       | enum |

__Users__   

  Field      | Type    | Key  
-------------|---------|---------
userId       | int     | Primary Key
email        | varchar |  
password     | varchar |
firstname    | varchar |  
lastname     | varchar |  

__Vehicles__  

  Field      | Type    | Key  
-------------|---------|---------
vehicle_id   |  int    | Primary Key
user_id      |  int    | Foreign Key
licence      | varchar |  
vehicle_type |  enum   |  

#### High Level Architecture
* Application UI (Web and/or Mobile)
* Backend Server
* Optionally we could spilt the backed into a reservation server and a slot server but may be unnecessary
* The system may have us reading more than writing and so we may want to create a couple of read replicas for the DB  

![High Level Architecture Design Image](https://raw.githubusercontent.com/Tochukz/Systems-Design/master/Packing-Garage/PakcingGarage.png)


#### Online References 
* [Amazon System Design Interview - Exponent](https://www.youtube.com/watch?v=NtMvNh0WFVM)