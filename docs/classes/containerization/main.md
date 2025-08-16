![It Works on My Machine](itworksonmymachine.png){ width="40%" align="right" }

How to avoid the classical..

The answer is: **CONTAINERIZATION**.

Docker is a platform and tool that enables developers to automate the deployment of applications inside lightweight, portable containers. Containers are a form of virtualization that packages an application and its dependencies together, ensuring consistency across different environments, from development to testing and production.

Here are some key concepts and components of Docker:

- **Containerization:** Containers are lightweight, standalone, and executable packages that include everything needed to run a piece of software, including the code, runtime, libraries, and system tools. Containers isolate applications from their environment, making them portable and consistent across various systems.
- **Docker Engine:** This is the core component of Docker. It is a lightweight and portable runtime that can run containers on various operating systems, including Linux and Windows. The Docker Engine consists of a server, a REST API, and a command-line interface.
- **Docker Image:** An image is a lightweight, standalone, and executable package that includes everything needed to run a piece of software, including the code, a runtime, libraries, environment variables, and config files. Images are used to create containers.
- **Dockerfile:** A Dockerfile is a text file that contains instructions for building a Docker image. It specifies the base image, sets up the environment, installs dependencies, and configures the application.
- **Registry:** Docker images can be stored in registries, which are repositories for sharing and distributing container images. Docker Hub is a popular public registry, and organizations often use private registries to store and manage their proprietary images.
- **Container Orchestration:** Docker can be used in conjunction with container orchestration tools like Kubernetes or Docker Swarm to manage the deployment, scaling, and orchestration of containerized applications in production environments.
- **Portability:** One of Docker's key advantages is its portability. Since containers encapsulate everything an application needs to run, they can run consistently across different environments, reducing the "it works on my machine" problem often encountered in software development.

Docker has become a widely adopted technology in the software development and deployment space due to its ease of use, portability, and the efficiency it brings to the development and deployment lifecycle. It has revolutionized the way applications are packaged, shipped, and deployed, making it easier for developers to build, test, and deploy applications in a more reliable and consistent manner.

## Differences between Docker and Virtual Machines

Docker containers and virtual machines (VMs) are both technologies used for virtualization, but they operate at different levels and have distinct characteristics. Here are the key differences between Docker containers and virtual machines:

| Aspect | Docker Containers | Virtual Machines |
|:-|:-|:-|
| Architecture | Containers share the host operating system's kernel and isolate the application processes from each other. Each container runs in its own user space but uses the host's kernel. | VMs, on the other hand, run a complete operating system, including its own kernel, on top of a hypervisor. Each VM is essentially a full-fledged virtualized computer with its own resources. |
| Resource Efficiency | Containers are more lightweight and share the host OS kernel, which makes them more resource-efficient compared to VMs. Containers can start up quickly and consume fewer system resources. | VMs have more overhead because each VM requires a full operating system and has its own kernel. This makes VMs less resource-efficient than containers. |
| Isolation | Containers provide process-level isolation, meaning that each container runs in its own process space, but they share the same OS kernel. This isolation is generally sufficient for most applications. | VMs provide stronger isolation since each VM runs its own operating system and has its own kernel. This makes VMs a better choice in situations where strong isolation is a critical requirement. |
| Portability | Containers are highly portable because they encapsulate the application and its dependencies, ensuring consistency across different environments. | VMs are less portable due to the larger size and complexity associated with bundling a full operating system with the application. |
| Startup Time | Containers can start up very quickly, typically in seconds, making them well-suited for microservices architectures and dynamic scaling. | VMs generally have longer startup times, often measured in minutes, due to the time required to boot a full operating system. |
| Resource Utilization | Containers share the host OS resources, which can lead to higher density and more efficient resource utilization. | VMs have a higher resource overhead because each VM requires its own set of resources, including memory, disk space, and CPU. |
| Use Cases | Containers are well-suited for microservices architectures, continuous integration/continuous deployment (CI/CD) pipelines, and scenarios where rapid deployment and scalability are crucial. | VMs are suitable for scenarios that require strong isolation, compatibility with various operating systems, and where applications rely on specific OS configurations. |

<figure markdown>
  ![Docker vs VM](difference-vm-containers.png){ width="100%" }
  <figcaption><i>Source: <a href="https://dockerlabs.collabnix.com/beginners/difference-vm-containers.html" target="_blank">Docker Labs - Difference between VM and Containers</a></i></figcaption>
</figure>

In summary, Docker containers and virtual machines have different levels of abstraction and are suitable for different use cases. Containers are lightweight, portable, and efficient, making them popular for modern application development and deployment practices. Virtual machines provide stronger isolation and are more suitable for scenarios where running multiple instances of different operating systems is necessary. The choice between Docker containers and virtual machines depends on the specific requirements of the application and the environment in which it will be deployed. To install Docker Engine, see [Install Docker Engine](https://docs.docker.com/engine/install/).

## Creating a Simple Docker

| Command | Description |
|:-|:-|
| `docker run <image>` | Runs a Docker container from an image. |
| `docker ps` | Lists running Docker containers. |
| `docker ps -a` | Lists all Docker containers, both running and stopped. |
| `docker stop <container>` | Stops a running Docker container. |
| `docker rm <container>` | Removes a Docker container. |
| `docker images` | Lists Docker images. |
| `docker rmi <image>` | Removes a Docker image. |
| `docker pull <image>` | Pulls a Docker image from a Docker registry. |
| `docker build -t <tag> .` | Builds a Docker image from a Dockerfile in the current directory. |
| `docker exec -it <container> <command>` | Executes a command in a running Docker container. |
| `docker logs <container>` | Fetches the logs of a Docker container. |


## Docker Compose

Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, you can use a YAML file to configure your application's services, networks, and volumes, making it easier to manage complex applications. All the services defined in the `compose.yaml` file can be started with a single command, allowing you to run multiple containers as a single application.

``` {.yaml title="compose.yaml"}
name: myapp
services:
  web:
    image: nginx
    ports:
      - "80:80"
  app:
    build: .
    volumes:
      - .:/app
    depends_on:
      - db
  db:
    image: postgres
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
```

To run the application defined in the `compose.yaml` file, you can use the following command:

``` shell
docker compose up -d --build # (1)!
```

1. `-d` runs the containers in detached mode, allowing them to run in the background.<br>
`--build` forces a rebuild of the images before starting the containers.

This command will start all the services defined in the `compose.yaml` file, creating a subnetwork for them to communicate with each other. You can then access the web service on port 80 of your host machine. The illustration below shows how the services are connected:

``` mermaid
flowchart LR
    user[User] -->|HTTP| web[Web]
    subgraph myapp [172.18.0.0/16]
        web[Web]
        app[App]
        db[(Database)]
    end
    web -->|API| app
    app -->|Connection| db
```

!!! tip "Environment Variables"
    
    Docker Compose allows you to define environment variables in the `compose.yaml` file or in a separate `.env` file. This is useful for passing configuration values, such as database credentials or API keys, to your containers without hardcoding them in the Dockerfile or application code.

    Therefore, **to facilitate the correction**, you can pass the environment variables directly in the `compose.yaml`, which Docker Compose will automatically read and use when starting the containers. Example:

    ``` { .yaml title="compose.yaml" }
    name: app

      db:
        image: postgres:17
        environment:
          POSTGRES_DB: ${POSTGRES_DB:-projeto} # (1)!
          POSTGRES_USER: ${POSTGRES_USER:-projeto}
          POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-projeto}
        ports:
          - 5432:5432 #(2)!
    ```

    1. If the `POSTGRES_DB` environment variable does not exist or is null - if it is not defined in the `.env` file - the default value will be `project`. See [documentation](https://docs.docker.com/reference/compose-file/interpolation/){target='_blank'}.

    2. Here, a tunnel is created from the database container's port 5432 to the host's port 5432 (in this case, localhost). In a production environment, this port should not be exposed, as no one outside the compose should access the database directly.

    ``` { .env title=".env" }
    POSTGRES_DB=superproject
    POSTGRES_USER=myproject
    POSTGRES_PASSWORD=S3cr3t
    ```

    When you run `docker compose up`, Docker Compose will automatically read the `.env` file in the same directory as the `compose.yaml` file and use the defined environment variables. If a variable is not defined in the `.env` file, it will use the default value specified in the `compose.yaml` file.

    !!! warning "Security"

        **NEVER** store sensitive information, such as passwords or API keys, directly in the `compose.yaml` file or in the code. Instead, use environment variables to pass sensitive information securely.

        Different environments (development, testing, production) can have different `.env` files, allowing you to manage configurations without changing the code or the `compose.yaml` file.

        **NEVER** store credentials in the repository, even if it is a private repository. That is, **NEVER** place a `.env` file in the repository (GitHub).

        **NEVER** leave ports exposed in production unless absolutely necessary.

## Additional Information

### Private Networks

Private networks are networks that are not directly accessible from the public internet. They are used to isolate resources and provide a secure environment for communication between devices. In the context of Docker, private networks allow containers to communicate with each other without exposing their services to the outside world.

<center>
``` mermaid
---
title: Network Address Translation (NAT)
---
flowchart TB
    host1@{ shape: docs, label: "Host 1"} ---|Public IP| internet([Internet])
    host2@{ shape: docs, label: "Host 2"} ---|Public IP| internet([Internet])
    host3@{ shape: docs, label: "Host 3"} ---|Public IP| internet([Internet])
    internet((Internet)) ---|Public IP| router1((Border Router<br>NAT<br>10.0.0.0/8))
    router1 ---|Private IP| device1([Device 1])
    router1 ---|Private IP| device2([Device 2])
    router1 ---|Private IP| device3([Device 3])
    router1 ---|Private IP| router2((Router<br>NAT<br>172.16.0.0/12))
    router2 ---|Private IP| router3((Router<br>NAT<br>192.168.0.0/16))
    router2 ---|Private IP| router4((Router<br>NAT<br>192.168.0.0/16))
    router2 ---|Private IP| device4([Device 4])
    router2 ---|Private IP| device5([Device 5])
    router3 ---|Private IP| device6([Device 6])
    router3 ---|Private IP| device7([Device 7])
    router4 ---|Private IP| device8([Device 8])
    router4 ---|Private IP| device9([Device 9])
    classDef internet fill:#ccf
    classDef router fill:#fcc
    classDef device fill:#cfc
    class internet internet
    class router1,router2,router3,router4 router
    class device1,device2,device3,device4,device5,device6,device7,device8,device9 device
```
<i>The diagram illustrates a network setup with multiple routers and devices, where each router uses Network Address Translation (NAT) to manage private IP addresses. The internet is connected to the first router (the border router), which has a public IP address, while the other routers and devices use private IP addresses within their respective subnets.</i>
</center>

Private networks are defined by specific IP address ranges that are reserved for private use. These ranges are not routable on the public internet, ensuring that devices within a private network can communicate securely without interference from external networks.

### Reserved IPv4 Addresses [^5]

#### General Reserved IPv4 Addresses

| Address block (CIDR)| Address range | Number of addresses | Scope | Description
|----------------------|----------------|--------------------:|-------|-------------
| 0.0.0.0/8            | 0.0.0.0<br>0.255.255.255 | 16.777.216 | Software | Current (local, "this") network |
| 10.0.0.0/8           | 10.0.0.0<br>10.255.255.255 | 16.777.216 | Private network | Used for local communications within a private network |
| 100.64.0.0/10       | 100.64.0.0<br>100.127.255.255 | 4.194.304 | Private network | Shared address space for communications between a service provider and its subscribers when using a carrier-grade NAT |
| 127.0.0.0/8         | 127.0.0.0<br>127.255.255.255 | 16.777.216 | Host | Used for loopback addresses to the local host |
| 169.254.0.0/16      | 169.254.0.0<br>169.254.255.255 | 65.536 | Subnet | Used for link-local addresses between two hosts on a single link when no IP address is otherwise specified, such as would have normally been retrieved from a DHCP server |
| 172.16.0.0/12      | 172.16.0.0<br>172.31.255.255 | 1.048.576 | Private network | Used for local communications within a private network |
| 192.0.0.0/24       | 192.0.0.0<br>192.0.0.255 | 256 | Private network | IETF Protocol Assignments, DS-Lite (/29) |
| 192.0.2.0/24       | 192.0.2.0<br>192.0.2.255 | 256 | Documentation | Assigned as TEST-NET-1, documentation and examples |
| 192.88.99.0/24     | 192.88.99.0<br>192.88.99.255 | 256 | Internet | Reserved. Formerly used for IPv6 to IPv4 relay (included IPv6 address block 2002::/16). |
| 192.168.0.0/16     | 192.168.0.0<br>192.168.255.255 | 65.536 | Private network | Used for local communications within a private network |
| 198.18.0.0/15      | 198.18.0.0<br>198.19.255.255 | 131.072 | Private network | Used for benchmark testing of inter-network communications between two separate subnets |
| 198.51.100.0/24    | 198.51.100.0<br>198.51.100.255 | 256 | Documentation | Assigned as TEST-NET-2, documentation and examples |
| 203.0.113.0/24     | 203.0.113.0<br>203.0.113.255 | 256 | Documentation | Assigned as TEST-NET-3, documentation and examples |
| 224.0.0.0/4        | 224.0.0.0<br>239.255.255.255 | 268.435.456 | Internet | In use for multicast (former Class D network) |
| 233.252.0.0/24     | 233.252.0.0<br>233.252.0.255 | 256 | Documentation | Assigned as MCAST-TEST-NET, documentation and examples (This is part of the above multicast space.) |
| 240.0.0.0/4       | 240.0.0.0<br>255.255.255.254 | 268.435.455 | Internet | Reserved for future use (former Class E network) |
| 255.255.255.255/32 | 255.255.255.255           | 1   | Subnet      | Reserved for the "limited broadcast" destination address |


#### Private IPv4 Addresses [^3]

| Address block (CIDR) | Address range | Number of addresses | Scope | Description |
|----------------------|----------------|--------------------:|-------|-------------
| 10.0.0.0/8           | 10.0.0.0<br>10.255.255.255 | 16.777.216 | Private network | Used for local communications within a private network |
| 172.16.0.0/12      | 172.16.0.0<br>172.31.255.255 | 1.048.576 | Private network | Used for local communications within a private network |
| 192.168.0.0/16     | 192.168.0.0<br>192.168.255.255 | 65.536 | Private network | Used for local communications within a private network |



[^1]: [Docker vs. Virtual Machines: Differences You Should Know](https://cloudacademy.com/blog/docker-vs-virtual-machines-differences-you-should-know/){:target="_blank"}

[^2]: [Docker Networking](https://docs.docker.com/engine/network/){:target="_blank"}

[^3]: [RFC 1918 - Address Allocation for Private Internets](https://datatracker.ietf.org/doc/html/rfc1918){:target="_blank"}

[^4]: [Private Network](https://en.wikipedia.org/wiki/Private_network){:target="_blank"}

[^5]: [Reserved IP Addresses](https://en.wikipedia.org/wiki/Reserved_IP_addresses){:target="_blank"}