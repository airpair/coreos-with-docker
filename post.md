_NOTE: This post is geared towards those who want a basic overview of Docker "containers" and how using CoreOS & etcd & fleet on machines with these containers can make life for devs/devOps much easier_

## What is CoreOS & Docker

### CoreOS
[CoreOS](http://www.coreos.com) is a barebones Linux distribution designed to make large multiple-machine deployments, using different softwares and dependencies, easier to scale and easier to manage. It is built for high availability and security. It does not come with a [package manager](https://en.wikipedia.org/wiki/Package_manager) and thus requires containers such as those provided by Docker. It uses "fleet" for cluster management and "etcd" for service discovery and keeping configuration up to date across the cluster.

### Docker
[Docker](www.docker.com)'s ["What is Docker" page](https://www.docker.com/whatisdocker) explains: "Docker allows you to package an application with all of its dependencies into a standardized unit for software development."

Put another way, Docker is a platform built using Linux containers (LXC) that isolates processes into "containers". These containers allow you to distribute and isolate your resources so you can always be sure that you have a clean environment when deploying. 
  
For instance, you can install a Rails app and all its dependencies into one container, and then run that container from any OS without worrying about going through the time-consuming configuration and setup process on your own machine and worrying about other packages you may have installed conflicting with the app you are working on. Since containers take up a much lower memory footprint than VMs, you can also use Docker (with something like [Vagrant](http://docs.vagrantup.com/v2/docker/basics.html)) to replicate production environments on a development machine with less power than the production environment.

### etcd
[etcd](https://coreos.com/etcd/) is a distributed key/value store which is designed to have no single point of failure due to its multiple node architecture. 

![etcd cluster](https://coreos.com/assets/images/media/Etcd-Replication.png)

etcd distributes configuration details to every machine in your cluster and ensures a cluster will always have the correct configuration data.

etcd is also highly customizable and you can read and write to it in both curl and JSON. For example, one use-case is to run a microservice container for just PostgreSQL and have etcd store the database connection details to be picked up by other microservices.
    
Going off the example in the previous section about Docker, with CoreOS you can separate that Rails app into its various pieces, with one container running Rails, one container running PostgreSQL, one container running a Javascript MVC like Ember and have them all pull their configuration from etcd when they start.


### fleet
[fleet](https://coreos.com/using-coreos/systemd/) is how coreos provides seamless cluster-level service management. The CoreOS team describe fleet as "You can think of fleet as an extension of systemd that operates at the cluster level instead of the machine level. Systemd is a single machine init system; fleet is a cluster init system." ([source](https://coreos.com/using-coreos/clustering/)). 
Note that CoreOS does not ship with support for cron so those who love setting up cron jobs will have to look into [systemd timers](https://wiki.archlinux.org/index.php/Systemd/Timers) instead.

   To illustrate the simplicity of using fleet, here are some fleet commands you can use:
   
   `fleetctl list-units` -- lists units running on your cluster
   
   `fleetctl status <unit>` -- status of particular unit running in your cluster
   
   `fleetctl start <app>@<port>.service1` -- starts particular app at particular port
   
 
![fleet scheduling](https://coreos.com/assets/images/media/Fleet-Scheduling.png)
 


## Docker vs Docker and CoreOS

*Comparing two apps - one that is Docker only and another which is Docker and CoreOS*

The difference between Docker-only setups vs Docker and CoreOS setups can best be explained by looking at two Platform as a Service (PaaS) apps (both designed to replace Heroku at a much lower price point) - Dokku and Deis. In a nutshell, Dokku is a simple, single-host mini-PaaS for hobby-type apps that uses Docker while Deis is a multi-host full replacement for Heroku for enterprise type apps that uses Docker for containerization and CoreOS for clustering. Deis is highly available and provides more security out of the box due to its usage of CoreOS but it comes with a higher price point because you need at least 3 machines to set it up. The biggest selling point of Deis is that it provides easy scalability - you can add or remove machines from the platform at will, while with Dokku you are stuck to just a single machine. 


## 7 Reasons Docker & CoreOS Are Great

Finally, here are the top reasons to try out CoreOS:

1. Reason #1 to go with CoreOS is etcd which was mentioned above. As the CoreOS website says: "Configuration values are distributed within the cluster for your applications to read. Values can be changed programatically and smart applications can reconfigure automatically. You'll never have to run Chef on every machine in order to change a single config value ever again."

2. If you care about high availability at all, deploying using CoreOS is one of the best ways to achieve that with a relatively low price point. 

3. It lets you serve different versions of software on different machines and update machines without any downtime

4. Though Docker helps you provide similar environments in production and development, CoreOS goes one step further by replicating cluster and network settings between dev and prod as well. As discussed before it also makes sure that the infrastructure always has the right configuration and everything has the right versioning. 

5. New developers can get up and running quickly rather than waste time installing all the required software and versions on their machine and making sure that nothing conflicts.

6. It can drastically reduce cost by replicating software services like Heroku. Even though you need multiple machines to truly take advantage of CoreOS, CoreOS is now supported by every major cloud storage provider, including AWS and Digital Ocean, the latter of which provides machines for only $5/month.

7. It has a very active open source community with (at the time of this writing) 6764 stars, 172 contributors, and over 40 pull requests merged in within the last two weeks for etcd. This may potentially be a problem, as one of the central tenets of the security that CoreOS offers out of the box is automatic updates on all your machines. Though you cannot turn this feature off, you can select an [update strategy](https://coreos.com/docs/cluster-management/setup/update-strategies/) to control when and how your machines will update.




** Sources: **
1. [CoreOs official website](www.coreos.com)
2. [Docker official website](www.docker.com)
3. [Playstation talk](https://www.youtube.com/watch?v=M9hBsRUeRdg)
4. [Deis official website](http://deis.io/)
5. [Digital Ocean CoreOS tutorial](https://www.digitalocean.com/community/tutorials/an-introduction-to-coreos-system-components)