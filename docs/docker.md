# Installing Docker and Docker and Docker Compose

This document guides you how to install Docker and Docker Compose on Windows, Ubuntu, and MacOS.

## Ubuntu

First, you must install some dependencies for Docker and add the GPG keys for
the Docker software repository:

```shell script
$ sudo apt-get update
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Verify that you now have the key with the fingerprint
`9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88`,
by searching for the last 8 characters of the fingerprint.

```shell script
$ sudo apt-key fingerprint 0EBFCD88
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]

```

Now add the repository for Docker and install it:

```shell script
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Then, add yourself to the `docker` group so you do not have to run Docker commands as root:

```shell script
$ sudo usermod -aG docker your-username
```

Finally, download and install Docker Compose as a separate binary:

```shell script
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

## Windows

Before you begin, you must install the Windows Subsystem for Linux. Follow the instructions at https://solarianprogrammer.com/2017/04/15/install-wsl-windows-subsystem-for-linux/ to do this.

First, you will install Docker Desktop, which is a native Windows application that will run your Docker containers. Then, you will install the Docker client and Docker Compose on the WSL so you can start the go-cart.io web application from within the WSL.

To do these things, follow the instructions at https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly. You _should not_ follow the instructions under 'Ensure Volume Mounts Work'.

## macOS

Install both Docker and Docker Compose by installing Docker Desktop for Mac according to the instructions at https://docs.docker.com/docker-for-mac/install/.
