Spring Boot - Microservices 

How to create spring boot app;

spring starter io url

server port - default is 8080

------------------create rest template ---------------------- (how it works?)

1. we should not be hardcoding the url, should be discovery 
 sol -> in property (needs to change in property), config server

 2. we should not create resttemplate every api call 
 
solution
2. bean to create rest template - singleton pattern

1. in SpringBootApplication class / @Configuration class or @Component class but in class path

@Bean 
@LoadBalanced ---- client side load balnacing with eureka
public RestTemplate getClient(){
	return new RestTemplate();
}


Using :

@Autowired 
private RestTemplate restTemplate;

.
.
.
restTemplate.getForObjects(.......);
.
.
.


Note:

marshal and unmarshal needs no args constructer

bean can be eager or lazy initialized 

why we should not send list in rest api:
it is better to send object wrapped it makes easy to add new property it will not break client code

--------WebClient------(use from Spring 5 version) -- build pattern

cons - RestTemplate:
doesn't support Asynchronous and non blocking communication

pros:
Asynchronous and non blocking communication

dependency:

Reactive Web 

-----------------------------------------------------------------------
--------------------------service discovery :------------- pattern
Eureka: how it works 
called client side service discovery

Eureka server:
create spring boot app - dependency Eureka server

add @EnableEurekaServer in main class

add in application.properties

server.port=8761 (optional)
eureka.client-register-with-server=false
eureka.client-fetch-rigistry=false

client:
dependency Eureka client

add @EnableEurekaClient in main class (not madatory, should be in classpath)

add in application.properties

server.port=8761 (optional)
spring.application.name=my_server1
we can have eureka server port


Note:
jaxb needs to be added as dependency from java 9 or higher versions

we can have multiple eureka server

spring cloud version is global version for all dependency below

spring boot is not under spring cloud

every time client doesn't call eureka it has cache

clint side load balnacing:
happens implicitly(uses Ribbon) by @LoadBalanced ---- client side load balnacing with eureka

we can customize this :
by using Discovery client interface

------------------------------------------------------------------------

-----------------how fault tolerrance works-------------
eureka works based on heartbeat -> client sends haertbeat to server 

when server is down -> cache works:

challenges with availability:

what:
fault tollerance- how much tollerance app have for fault
resilence- how many faults system can tollerate

what if, system goes down:

issue -> 
instance goes down: 
solution -> 
create multiple instances,handled by eureka

issue -> 
instance is slow: it makes other dependent services also slow in a call
problem in multithreaded env -> 
many threads will be created and consumes all the reaources and 
they quickly don't release resources
solution ->
increase the  thread pool in server -> by time it also fill 
timeout -> remove slow threads
we can set in RestTemplate
way 1(2 ways): (temporary solution)

@Bean 
@LoadBalanced
public RestTemplate getClient(){
	HttpComponentsClientHttpRequestFactory crf = new HttpComponentsClientHttpRequestFactory();
	crf.setConnectTimeout(3000);//3sec
	return new RestTemplate(crf);
}

cons:
if frequnecy of requests is more 

partly solves the isssue:

solution: fault tollerance pattern -> circuit breaker pattern
if server is slow, 
	take pause for some time, 
	try after some time 
	
when it should break:
req 1 -> success
req 2 -> timeout
req 3 -> success
req 4 -> error
req 5 -> timeout
req 6 -> timeout
req 7 -> timeout

pattern should be decided accourding to requirements based on the last n requests

parameters: (we can use performance test to identify these below values)
last n requests: 5
how many failed out of n: 3
time out for failure: 2s
how long to wait bfore next hit: 10s

how to handle incoming requests when circuit breaks:
1. throw an error (not recommended)
2. fallback default response (not ideal)
3. save previous response - cache (but not valid reaponse)

why circuit breaks:
fail fast - good thing 
fallback functionality 
automatic recovery

pattern:
1. when to break 
2. what to do when breaks 
3. when to resume requests

how to implement:

by using Hystrix framework





