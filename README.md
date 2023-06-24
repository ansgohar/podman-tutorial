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

<!---

```console
podman
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
