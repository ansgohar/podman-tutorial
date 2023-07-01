# Podman Tutorial

A Podman Tutorial

**Introduction**
This is an introduction for the podman tutorial.

# Podman Installation Instructions

[Please follow the installation process as per this link.](https://podman.io/docs/installation)
Nice to use [brew](https://brew.sh/) for the installtion process.

## Running Podman

```console
brew install podman
```

**Next,** create and start your first Podman machine:

```console
podman machine init
podman machine start
```

You can then verify the installation information using:

```console
podman info
```

Get Installed Podman version

```console
podman -v
```

## Pulling and Displaying Images

```console
podman pull <Registry>/<user>/<image name>:<tag/version>
podman pull registry.redhat.io/rhel7/rhel:7.9
```

After the execution of the pull command, the image is stored locally in your system. You can list the images in your system by using the podman images command.

```console
podman images
```

## Container and Container Images

### Running and Displaying Containers

The podman run command to create a new container that uses the image.

```console
podman run registry.redhat.io/rhel7/rhel:7.9 echo 'Red Hat'
```

When the container finishes the execution of echo, the container is stopped because no other process keeps it running.

```console
podman ps
```

However, stopping a container is not the same as removing a container. Although the container is stopped, Podman does not remove it. You can list all containers (running and stopped) by adding the --all flag to the podman ps command.

```console
podman ps --all
podman ps --all --format=json
```

You can also automatically remove a container when it exits by adding the --rm option to the podman run command.

```console
podman run --rm registry.redhat.io/rhel7/rhel:7.9 echo 'Red Hat'
```

You can assign a name to your containers by adding the --name flag to the podman run command.

```console
podman run --name podman_rhel7 registry.redhat.io/rhel7/rhel:7.9 echo 'Red Hat'
```

### Exposing Containers

```console
podman run -p 8080:8080 registry.access.redhat.com/ubi8/httpd-24:latest
```

If you want the container to run in the background, to avoid the terminal being blocked, then you can use the -d option.

```console
podman run -d -p 8080:8080 registry.access.redhat.com/ubi8/httpd-24:latest
```

### Port Forwarding

A container's network namespace is isolated, which means that a networked application is only accessible within the container. Port forwarding maps a port from the host machine where the container runs to a port inside of a container.

The -p option of the podman run command forwards a port. The option accepts the form HOST_PORT:CONTAINER_PORT.

For example, the following command maps port 80 inside the container to port 8075 on the host machine.

```console
podman run -p 8075:80 my-app
```

To publish a container to a specific host and to limit the networks it is accessible from, use the following form.
Port 80 in the my-app container is available from port 8075 only from the host machine, which is accessible via the localhost 127.0.0.1 IP address.

```console
podman run -p 127.0.0.1:8075:80 my-app
```

#### List Port Mappings

To list port mappings for a container, use the podman port command. For example, the following command reveals that port 8010 of the host machine is mapped to port 8008 within the container.

```console
podman port my-app
```

The --all option lists port mappings for all containers.

```console
podman port --all
```

### Using Environment Variables

You can pass environment variables to a container by using the -e option.

```console
podman run -d -e NAME='Red Hat' -p 8080:8080 registry.access.redhat.com/ubi8/httpd-24:latest
```

### Podman Desktop

Podman Desktop is a graphical user interface, which is used to manage and interact with containers in local environments. It uses the Podman engine by default, and supports other container engines as well, such as Docker.
You can download [Podman Desktop](https://podman-desktop.io/downloads/)

```console
podman-desktop
```

# Container Networking Basics

## Managing Podman Networks

Podman network management is done via the podman network subcommand. This subcommand includes the following operations:

### Creates a new Podman network

```console
 podman network create
 podman network create example-net
```

This command accepts various options to configure properties of the network, including gateway address, subnet mask, and whether to use IPv4 or IPv6.

#### Lists existing networks

```console
podman network ls
```

Lists existing networks and a brief summary of each. Options for this command include various filters and an output format to list other values for each network.

```console
podman network inspect
```

Outputs a detailed JSON object containing configuration data for the network.

### Removes a network

```console
podman network rm
```

### Removes any networks

Removes any networks that are not currently in use by any running containers.

```console
podman network prune
```

Connects an already running container to or from an existing network. Alternatively, connect containers to a Podman network on container creation by using the --net option. The disconnect command disconnects a container from a network.

```console
podman network connect
```

For example, the following command creates a new Podman network called example-net:

```console
podman network create example-net
```

To connect a new container to this Podman network, use the --net option. The following example command creates a new container called my-container, which is connected to the example-net network.

```console
podman run -d --name my-container --net example-net container-image:latest
```

When you create new containers, you can connect them to multiple networks by specifying network names in a comma-separated list. For example, the following command creates a new container called double-connector that connects to both the postgres-net and redis-net networks.

```console
podman run -d --name double-connector --net postgres-net,redis-net container-image:latest
```

Alternatively, if the my-container container is already running, then run the following command to connect it to the example-net network:

```console
podman network connect example-net my-container
```

#### Networking in Containers

Containers attached to Podman networks are assigned private IP addresses for each network. Other containers in the network can make requests to this IP address.
For example, a container called my-app is attached to the apps network. The following command retrieves the private IP address of the container within the apps network.

```console
podman inspect my-app -f '{{.NetworkSettings.Networks.apps.IPAddress}}'
```

### Enabling Domain Name Resolution

When you use the default Podman network, the domain name system (DNS) for other containers in that network is disabled. To enable DNS resolution between containers, create a Podman network and connect your containers to that network.

When using a network with DNS enabled, a container's hostname is the name assigned to the container. For example, if a container is started with the following command, then the other containers on the test-net network can make requests to the first container by using the basic-container hostname. The basic-container hostname resolves to the current IP address of the basic-container container.

```console
podman run --net test-net --name basic-container example-image
```

## Start Processes in Containers

Use the podman exec command to start a new process in a running container. The command uses the following syntax:

```console
podman exec [options] container [command ...]
podman exec httpd cat /etc/httpd/conf/httpd.conf
```

Additionally, podman exec provides a number of options, such as:

- Use --env or -e to specify environment variables.
- Use --interactive or -i to instruct the container to accept input.
- Use --tty or -t to allocate a pseudo terminal.
- Use --latest or -l to execute the command in the last created container.

The following command sets the ENVIRONMENT variable and then executes the env command to print all environment variables. In this example, the container name is not necessary because the -l option is used

```console
podman exec -e ENVIRONMENT=dev -l env
```

#### Open an Interactive Session in Containers

If you open a shell program with the --interactive option, the podman exec executes the shell program and receives your input, but the input is not captured by a terminal:

```console
podman exec -il /bin/bash
pwd 1
ls
```

BTW: The podman exec -il command is identical to podman exec -i -l.

to open an interactive shell in a running container, combine the --interactive and --tty options:

```console
podman exec -til /bin/bash
pwd
    /opt/app-root/src
exit
    $
```

#### Copy Files In and Out of Containers

Use the podman cp command to copy files to and from a running container. The command uses the following syntax:

```console
podman cp [options] [container:]source_path [container:]destination_path
```

Use the following command to copy the /tmp/logs file from a container with ID a3bd6c81092e to the current directory:

```console
podman cp a3bd6c81092e:/tmp/logs .
```

Use the following command to copy the nginx.conf file to the /etc/nginx/ directory in a container called nginx:

```console
podman cp nginx.conf nginx:/etc/nginx
```

Use the following command to copy the nginx.conf file from the nginx-test container to the nginx-proxy container:

```console
podman cp nginx-test:/etc/nginx/nginx.conf nginx-proxy:/etc/nginx
```

# Managing the Container Lifecycle

## Listing Containers

You can list running containers with the podman ps command.

```console
podman ps
```

You can include stopped containers in the output by adding the --all or -a flag to the podman ps command.

```console
podman ps --all
```

Inspecting Containers

```console
podman inspect 7763097d11ab
```

In the following command, the Status field of the State object is retrieved for a container called redhat.

```console
podman inspect --format='{{.State.Status}}' redhat
```

## Stopping Containers Gracefully

The following command stops a container with a container ID 1b982aeb75dd.

```console
podman stop 1b982aeb75dd
```

ou can stop all the running containers by using the --all or -a flag.

```console
podman stop --all
```

If a container does not respond to the SIGTERM signal, then Podman sends a SIGKILL signal to forcefully stop the container. Podman waits 10 seconds by default before sending the SIGKILL signal. You can change the default behavior by using the --time flag.

```console
podman stop --time=100
```

### Stopping Containers Forcefully

You can send the SIGKILL signal to the container by using the podman kill command. In the following example, a container called httpd is stopped forcefully.

```console
podman kill httpd
```

### Pausing Containers

Both podman stop and podman kill commands eventually send a SIGKILL signal to the container. The podman pause command suspends all processes in the container by sending the SIGSTOP signal.

```console
podman pause 4f2038c05b8c
```

The podman unpause command resumes a paused container.

```console
podman unpause 4f2038c05b8c
```

### Restarting Containers

Execute the podman restart command to restart a running container. You can also use the command to start stopped containers.
The following command restarts a container called nginx.

```console
podman restart nginx
```

### Removing Containers

Use the podman rm command to remove a stopped container. The following command removes a stopped container with the container ID c58cfd4b90df.

```console
podman rm c58cfd4b90df
```

You can add the --force (or -f) flag to remove the container forcefully.

```console
podman rm c58cfd4b90df --force
```

You can also add the --all (or -a) flag to remove all stopped containers. This flag fails to remove running containers. The following command removes two containers.

```console
podman rm --all
```

<!---
```console

```

```bat
var s = "JavaScript syntax highlighting";
alert(s);
```

```properties
var s = "JavaScript syntax highlighting";
alert(s);
```

```properties
var s = "JavaScript syntax highlighting";
alert(s);
```

```console
foo@bar:~$ whoami
foo
```

```js
function () { return "This code is highlighted as Javascript!"}
```

# H1
## H2
### H3
#### H4
##### H5
###### H6

Alternatively, for H1 and H2, an underline-ish style:

Alt-H1
======

Alt-H2
------

> Blockquotes are very handy in email to emulate reply text.
> This line is part of the same quote.
--->
