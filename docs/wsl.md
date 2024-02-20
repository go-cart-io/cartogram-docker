# Setting Up the go-cart.io Web Application on Windows Using the Windows Subsystem for Linux

The go-cart.io web application consists of two major parts:

1. The main, user-facing web application that users interact with to
   generate and view cartograms (`cartogram-web`), and

2. The AWS Lambda function that actually generates cartograms from user data
   using a small Python wrapper for the C code at 
   https://github.com/Flow-Based-Cartograms/go_cart (`cartogram-serverless`).
   
   For local development, a small wrapper using Python Flask
   is provided to simulate the HTTP API endpoint to the Lambda function on 
   the developer's local machine.

In addition, the main web application also uses PostgreSQL and Redis for data storage.

This repository contains a Docker compose file that makes it easy to install and
run all of the components necessary to develop the go-cart.io website locally
on your computer. This readme file contains instructions on how to go about this on Windows. We will make use of the native Docker Desktop application for Windows, but will use the Windows Subsystem for Linux to store the website code, invoke the Docker client, and run scripts that interact with the running Docker containers.

## Installing the Windows Subsystem for Linux

Before you begin, you must install the Windows Subsystem for Linux. Follow the instructions at https://solarianprogrammer.com/2017/04/15/install-wsl-windows-subsystem-for-linux/ to do this.

## Installing Docker and Configuring Docker-WSL Integration

First, you will install Docker Desktop, which is a native Windows application that will run your Docker containers. Then, you will install the Docker client and Docker Compose on the WSL so you can start the go-cart.io web application from within the WSL and invoke the scripts within the `cartogram-docker` repository to interact with running containers.

To do these things, follow the instructions at https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly. You *should not* follow the instructions under 'Ensure Volume Mounts Work'.

## Installing `cartogram-web` and `cartogram-serverless`

The code for the go-cart.io web application is contained within two GitHub repositories: 
[cartogram-web](https://github.com/jansky/cartogram-web) and 
[cartogram-serverless](https://github.com/jansky/cartogram-serverless). Before you continue, you will need to set up Git on the WSL. First, install the git client with `apt`:

```shell script
$ sudo apt install git
```

Then, set up an SSH key so you push and pull code from GitHub without needed to constantly reënter your password:

```shell script
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
$ cat ~/.ssh/id_rsa.pub
```

Copy all the text starting with `ssh-rsa` and ending with your email. Then, follow the instructions at https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account to add this SSH key to your GitHub account.

Finally, set your name and email address that will be associated with your commits:

```shell script
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

Now, fork these two repositories so you have your own version of them. Note the URL that you will need to use to clone both repositories.

Then, clone this (i.e., `cartogram-docker`) repository:

```shell script
$ git clone git@github.com:jansky/cartogram-docker.git
```

Now, clone your forked `cartogram-web` and `cartogram-serverless` into the root directory
of *this* repository (i.e., the folder containing this readme):

```shell script
$ cd cartogram-docker
$ git clone git@github.com:your-github-username/cartogram-web.git
$ git clone git@github.com:your-github-username/cartogram-serverless.git
``` 

## Starting the go-cart.io Web Application

To start the go-cart.io web application for local development and testing, run the following
command from the root directory of this repository (i.e., the folder containing this readme):

```shell script
$ docker-compose up
```

The first time you run this command it may take a while to download and install dependencies.

After all of the Docker containers have started up, all of their output will be collected
into the terminal window so you can see how the web application is responding to requests,
and if it encounters any errors. You can access the locally-running go-cart.io website at
http://localhost:5000. When you make changes to the code in either the
`cartogram-web` or `cartogram-serverless` repositories, their respective servers will reload 
automatically when you save your changes.

When you would like to shut down the go-cart.io web application, simply press Ctrl-C in the
terminal window you started Docker Compose in. After a few moments, the state of all the
Docker containers will be saved and the application will gracefully come to a halt.

**NOTE:** If you make a breaking change to the code (e.g., you introduce a syntax error), 
you will need to shut down the web application and start it again.

## Developing `cartogram-web` and `cartogram-serverless` with PyCharm

You should install the Linux version of PyCharm in the WSL to develop `cartogram-web` and `cartogram-serverless`. You can find instructions on how to do this in `pycharm.md`.