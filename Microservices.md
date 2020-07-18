## Disadvantages of Microservices
* As like anything in life, microservice architecture has also its price and a fair share of disadvantages
* it is by no means a Golden Hammer which can solve all sort of Problems in a Software Application
* There are scenarios in which moving to µservice architecture from monolithic architecture without proper consideration will leads to nightmarish condition
### Design Complexity
* Monolithic Architecture often gives “One size fits for all” solution for Business applications
* But in µservice architecture, there are many solutions possible depending on the applications and use cases
* if the wrong solution is taken for wrong application size/type (e.g. put a kid's clothes on a full-grown man or vice versa), then µservice architecture is bound to fail
* Also, designing µservices is challenging as there are far more moving parts compared to monoliths
* Usually, a badly designed µservice is worse than a monolith
### Distributed Systems Complexity
* Microservices are distributed system; which are complex and has a unique set of challenges compared to single Machine systems
* Following problems can arise in Distributed Microservices:
   * Overall System latency is higher
   * Network failure or Individual Node failure can bring the whole system down
   * Operational complexities are higher
### Operational Complexity
* Once the Monolithic application is decomposed into µservices, the complexity moves from source code to operations
* Simple operations like Logging, Monitoring became more complex because instead of one Systems, many more need to be handled
* Sometimes existing Logging/Monitoring tools don’t fit with µservices and new once are needed
* Tracing is also very important in µservices to measure the performance/latency of individual µservices for a Service Request
* The complete System test is likewise quite complex in µservices compared to monolithic applications
* A renowned computer scientist Martin Flower states : "_the initial Development Velocity of Microservice Architecture is lower compared to Monolithic Architecture due to the Operational Complexities_"
### Security
* Security in software systems is that elephant in the room what everybody can see but nobody wants to talk about
* Securing one software application is hard
* Securing hundreds of µservices which are often distributed systems is quite challenging
### Data Sharing and Data Consistency
* Ideally, every µservices should have its own data store
* Downside is that the µservices need to share data between themselves to fulfill the business goal
* Data consistency is another challenge
* To support consistency in the distributed databases is not recommended for two reasons:
    * It does not Scale and many Modern Data Store does not support it
    * Most of the modern NoSQL Databases only offers Eventual Consistency which needs careful design
### Communication Complexities
* Microservices achieves strict modularity and development autonomy via process/network boundaries
* Downside is that the services can only communicate via the physical network which eventually leads to higher network latency

### Conclusion
* Designing and implementing µservices architecture is challenging compared to monolithic software architecture
* Microservice architecture is by no means a silver bullet which can solve the complexity issues of all sorts of applications
* Even after different arguments, it is believed that µservices architecture is a very useful and handy tool for modern software development
* Specially for large Enterprises which normally develop complex softwares, µservices architecture is the only way to tackle complexity and to be competitive in the market
