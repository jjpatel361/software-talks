
# Docker 

### Rise of Virtual Machine
- to run each and every application we put it inside a server and manage it. 
- so to deploy an app you need MySQL, MongoDB, REDIS and NGINX for load balancing etc.
- This was an era where we couldn't run multiple apps inside one single server
- Every app running is not fully utilizing the underlying server. < 10% usage of server
- Enter VMs.
	- Hypervisor acts like a compartmentalization. So if you have a 20 GB underlying machine. Hypervisor can distribute RAM efficiently to different apps without causing resource fights among apps.
	- Also security between apps is maintained
- now utilization of the server is up. well over >50 % Voila!
- Drawbacks of VMs
	- Every app needs an OS to run
	- OS are resource hogging piece of code that add little business value.
	- running more number of OS doesn't make money but running more Apps does.
	- OS licensing is a huge trouble to manage.
- VM > better than traditional approach. But also inefficient
- Enter Containers
	- Application run time environment 
	- much more lightweight version of VMs doesn't need OS but provides equal security as VMs.
	- nothing new Linux had this concept of LXCs we just started calling it containers. Thank you docker!
- Components of a Container  
	- User Namespace
		- So multiple apps might need a lib. How do we ensure every app gets the lib file version file it needs?
		- More concretely, say 2 apps running on same server and needs Python 2.7and Python 3.3. How do we create boundaries for this ?
		- Namespace for rescue! Every namespace gets its own File system: dev etc lib and proc
		- Every name space gets its own networking process tree. And the process tree inside a namespace cannot kill another process in some other namespace.(_If that was possible you can write a code to access underlying process tree and just kill random processes to create nucances of course i need to run it with privilege_) 
		- This feature is provided by Kernel called Kernel Namespace. this namespace acts as boundary wall to protect cross boundary actions

	- Control groups 
		- another kernel feature that is extremely useful
		- allows to tweak resource utilization of each username space
		- allows safety between apps. Like app-x cannot do DDoS on app-y or app-z

	- Capabilities
		- another good kernel feature that controls privileges to namespace
		- Like if you need the app to just bind a port address you can provide CAP_NET_BIND_SERVICE privilege and ignore everything else. 

- Containers provided all of these 
    - namespace : run apps with different versions on single machine 
    - control groups cgroups : define how much of memory each container can eat from main memory 
    - capabilities : define exposure and port mappings etc. 

With these new powers we can now deploy containers in world and decimate the VMs!        
 
___ 

## Rise of Docker

- Docker is just abstraction that is built to provide the above components user namespace, C Groups, capabilities and control privileges
- It is an abstraction over the above kernel powers. 
- Execution driver: LXC vs libcontainer. This is similar to what JDBC is to Java for MySQL connection. Docker uses LXC for kernel communication 
- LXC is maintained by an independent org so Docker wanted something stable and controlled by Docker. This led to libcontainer (OSS) very similar to LXC designed for Docker
- Since lib container is also supports Windows Execution! so this is a massive win. (_I don't know how they managed to do that_)

Commands

- docker info
- docker version

Setup

- Download docker and run above commands to ensure the docker service is running in background. 
- If you try to start a docker container now then it will throw you a permission denied error 
- This is because the docker service belongs to docker group and the current user it not part of that group. So, you cannot start / stop / create any containers just yet. Two possible solution
	- slap sudo in front of docker run every time you are doing docker DDL commands 
	- add your account to the docker user group

Fun with Containers
-   `docker run -it centos bin/bash` 
	- `-it`  - interactive  
	- `centos` - name of the image 
	- `bin/bash` command to run inside that image
- docker pull -a image_name
	- `image_name` => fedora, ubuntu, cent os etc. 
	- `-a` tells to download all images available on docker hub 
	- `docker pull` fedora defaults to latest image available

### Comparison to Shipping Yards

- Docker Engine is like an abstraction on underlying Linux Kernel. 
- Shipping Yards have berths, ship to shore cranes, rail terminal, truck lanes,container yards etc. Docker has these containerization capabilities networking, name spacing, control groups and provides a nice API over it. So, doesn't matter if you run it on Ubuntu or Cent OS or any Linux flavor the Docker Engine is all you need to care about.

### Containers
- images are build time constructs and containers are run time constructs
- images are light weight versions of the actual OS and not the entire OS itself.
- For example, log into a container and run command pstree, iostat, man page etc. these are fancy commands and not provided inside the image
- image is way to provide a bare minimum run time needed

### Images
- read only build time constructs 
- it has OS files and objects, app files and manifest JSON file
- images are stacked items and manifest describes how these are stacked together
- this is like package.json which tells what are dependencies for making an image
- trust only official images for official usage

### Registry
- Registry is like App store where we can host private and public docker images. 
- make sure you trust and public ones since they are not guaranteed. Only trust official publishers

---

### Containerizing an App 

- Docker also allows us to run code on remote Docker Daemon.
- Imagine adding a remote docker daemon as EC2 instance on AWS and deploying docker remote.
- You can also point docker to a remote git. Under hood
	- Pull from git repository 
	- docker builds an image from git repo 
	- docker creates a container as specified inside the Docker file in git repo 
	- you now have a container running code from remote git repo! 
- Starting and stopping a container does not throw away files.
- every image has a default command it runs if not provided with `-it` flag. 
- We can override this default command, with CMD command inside Dockerfile. 

### Logging in Containers
- Docker daemon logs : are logged to systemd and for non systemd can be found inside var/log/messages
- In Windows docker daemon logs can be found in `AppData/Local/Docker`
- App data logs are different. They are logged to stdout or stderr. You can mount volumes on container so app logs are available when container is destroyed
- If you are using an existing logging solution like splunk or fluentd then use Docker driver.  Docker has support for drivers which can source stderr and stdout and pump to your existing solution. You can say use "awslogs" under driver field so it can ship to cloudwatch.
- You can set the default logging driver in daemon.json or do `docker container run --log-driver --log-opts` etc.
- Do `docker logs <containerId>` to see logs container specific 

### Docker Swarm Cluster
- Cluster of docker nodes. Major elements of a cluster are  
	- Managers : nodes which are responsible of cluster management. 
	- Workers: nodes which are actually doing work.
	- Cluster store: shared data store for all managers and it is encrypted.

- All of the communication inside docker swarm is Mutual TLS (MTLS) auth. Hence, secured internally among them
- You can add linux and windows workers to the swarm! multiple platform apps
    - you have a python app running linux container talking to another container running on windows os. Both are connected over a single network!
- docker swarm init - 
	- creates a new swarm cluster
	- appoints the first docker node as the leader 
	- generates the CA file for authentication. You can also use custom CA and pass it via CLI 
	- generates tokens for joining manager and worker. These tokens are used to add docker containers to the cluster with their roles. 
	- Newly added managers get certificates and are followers 
	- When giving commands, the followers are proxying request to root manager
	- If root manager fails then a consensus is built. using RAFT. To make consensus valid always ensure you are having 1,3,5 or 7 managers. This makes it possible to generate consensus. 
	- The root manager also does certificate key rotation! 
	- Keep your node (instances) as geo close as possible! distributed systems

### Docker Cluster Orchestration
- Managing swarm is difficult and not manual when we deploy. So orchestration comes to the rescue. It is a config based application that manages swarm and keeps it healthy. Kubernetes, Mesos, Docker Swarm are also solutions to do the same problem. Kubernetes have won this war.

---

### Container Networking

Container networking is about how 2 docker containers deployed on separate nodes/machines can talk to each other. There are multiple ways we can setup this.

- Bridge Network  
This is natively supported driver that allows us to connect docker containers visible. A bridge network is default in docker. This allows containers to talk only on same hosts and not across machines. If we want to do that, we need to setup port to port mapping. This is difficult to automate on cloud deployments etc. Also one to one mapping of ports is hard to manage

Bridge network is for Linux machines and NAT drivers are for Windows machines

Overlay Network
This is a better version over bridge network. No 1:1 port mapping since all containers are joining a single network "overlay". This is default encrypted and available only for docker containers. Meaning you cannot attach anything other than docker daemons. (Not a major hurdle to run it on cloud). Also control and data planes are encrypted. so no snooping. Scoping is SWARM level. Bridge are scoped to LOCAL only

---

Deploying Production stacks 

If you need to deploy and entire fleet of services of infrastructure then you need to use docker stacks. Just like a docker compose or Dockerfile you can describe entire infrastructure as a single stackfile.yml 
- Components of stacks are docker services
- Docker stacks is only available in swarm mode
- You can define replicas and nature of restart inside the stackfile.yml 
- This allows us to manage replicas of service, auto restart if node fails etc. 

```
docker stack deploy -c stack file.yml 
```