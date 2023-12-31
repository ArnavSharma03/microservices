
# Book-Microservice

This project aims to implement  microservices  which is used to manage books and provide books as content


# Runing Instructions
 Run the docker compose yaml file to start.

`docker-compose -f docker-compose.yaml up`



## Features
 - Built a scalable backend system to serve books as contents.
  
 - Dockerized entire application including all services and databases for easy deployement.
   
 - Used Nginx as a reverse proxy to cater requests to different services.
   
 - Used RabbitMQ as the Message-Broker to publish message from User Service to Email Service after which an email 
   will be sent to the newly regsitered user.
   
 - User can filter books based on the basis of likes or reads interactions.
 - Implemented database using Mongodb for all three services.




    



## Database Schema

| Users            |
| -----------------|
| \_id:ObjectId    |
| name:string      |
| email:string     |
| password:Number  |
| phoneno:ObjectId |

| Contents            |
| --------------------|
| \_id:ObjectId       |
| user_id:String      |
| title:String        |
| story:String        |
| created_at:Datetime |


| User_events              |
| -------------------------|
| \_id:ObjectId            |
| content_id:ObjectId      |
| liked_by:list_of_strings |
| read_by:list_of_strings  |

## System Architecture

![new_system_design_template drawio](https://user-images.githubusercontent.com/37933427/153756157-34581434-bb5a-470e-ad82-9b0f2511425c.png)

## User Service

1) User service deals with user registration and login.

2) Jwt tokens is used to authorize the incoming user's request in all the services.


- Request
 ```
  curl -X POST -d '{"name":"aryan","email":"arnav@example.com","password":"rohan123","phoneno":234565778}' \
  http://localhost:80/api/v1/users/register \
  --header 'Content-Type: application/json'
 ```
 
- Response Body: 201
 ```json
  {
   "result": {
       "name": "aryan",
        "email": "arnav@example.com",
        "password": "rohan123",
        "phoneno": 8123456789,
        "_id": "61f77e267aceb6f9e51c25b3",
      }        
  }
 ```

 ## Email Service
 1) Email service sends greeting mail to the newly registered users.
    
 2) It consumes events from Message queue published by User service after new registration.
    
 3) Email is sent using node mailer.
 

 
## Content Service
1)Serving books as content. A content will have a story and title as contents.

2)Content service should have atleast the title, story, date published and the user id stored.

3) A user will be able to create, update and delete contents.

### Rest Apis

     post new content             POST    http:://api/v1/contents/

     get newly added contents     GET     http:://api/v1/users/new

     get top liked contents       GET     http:://api/v1/contents/top/liked

     get top read contents        GET     http:://api/v1/contents/top/read

     get content by id            GET     http:://api/v1/contents/:id
     
     update story                 PATCH   http:://api/v1/contents/:id/update-story
     
     update title                 PATCH   http:://api/v1/contents/:id/update-title
     
     delete content               DELETE  http:://api/v1/contents/:id
     
 - Request     
  ```
  curl -H "Authorization: Bearer <Token>" http://localhost:80/api/v1/contents/new \
  --header 'Content-Type: application/json'
  ```   
 - Response
  ```json
  "result": 
  {
    [
      {
         "_id": "61f82deddaa122263e6f0c22",
         "title": "Arnav",
         "story": "No Time for caution",
         "user_id": "61f82b9d2dfca828d5238e2c",
         "createdAt": "2022-01-31T18:43:57.980Z",
      },
      {
         "_id": "61f82ba9daa122263e6f0c1d",
         "title": "Hello Aryan",
         "story": "Random story",
         "user_id": "61f82b9d2dfca828d5238e2c",
         "createdAt": "2022-01-31T18:34:17.596Z"
      },
      {
         "_id": "61f82ba8daa122263e6f0c1b",
         "title": "Story",
         "story": "To infity and beyond",
         "user_id": "61f82b9d2dfca828d5238e2c",
         "createdAt": "2022-01-31T18:34:16.177Z"
      }
    ]
  }
  ```
 ## User interaction service
 1) User Interaction service is basically to record events done by the user. In this case the service records 2 types of
   events - Like and Read.
   
 2) Like - The content liked by the current user.
 
 3) Read - The content i.e book completely read by the user.
 
 4) This service exposes API for content service inorder to fetch top contents based on likes and reads count since these 2 events data is 
   stored in User_events database.
     
### Rest Apis
   
     Like a content                          PUT    http:://api/v1/interact/like

     Comepleted reading a content            PUT    http:://api/v1/interact/read

     get like interaction on given content   GET    http:://api/v1/interact/:content_id/likes
     
     get read interaction on given content   GET    http:://api/v1/interact/:content_id/read

   
- Request 
 ```
  curl -X PUT -H "Authorization: Bearer <Token>" http://localhost:80/api/v1/interact/like?content_id=61f82ba9daa122263e6f0c1d \
  --header 'Content-Type: application/json'
 ```
     
- Response
 ```json
 {
    "success": true
 }
 ```
