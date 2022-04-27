# Beginner’s Guide to Containers and Orchestration

Containers are great for:

 - Software Portability;

 - Isolation;

 - Scaling

 - Automation

 - Efficient Resource Usage



Advantages of containers:

 - Isolation and portability of VMs;

 - More lightweight than VMs - Less resource usage;

 - Faster startup than VMs;

 - Smaller than VMs - Container images are smaller than VM images;

 - Faster and simpler automation;



Disadvantages of containers:

 - Less flexibility than VMs;

 - Introduces new challenges around orchestration and automation;



Docker is a container runtime, which is a piece of software that is design to implement, support and run containers.

A container is a concept, and Docker is one container runtime software, but there are others.

Other container runtimes are:

 - RKT (Pronounced Rocket)

    - Similar to Docker

 - Containerd

    - Simpler than Docker, but does not include Docker features, such as image management;
    - Can be an advantage to Docker if coupled with tools that perform the tasks that Containerd doesn't;

## Container Use Cases

### Microservices

Microservices involve splitting the application into a series of small, independent services.

This allows each service to be built, modified and scaled separately, with relatively little impact on one another.

### Cloud Transformation

Cloud transformation is the process of migrating existing IT infrastructure to the cloud.

Advantages:

 - Wraping software into a container is relatively easy, simplyfing the migration to the cloud;

 - Using containers allow for taking advantage of the flexibility and automatability of containers;

 - Since containers use fewer resources, they are less expensive than VMs;
