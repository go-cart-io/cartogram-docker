# cartogram-docker: Docker Application for Web-based Cartogram Generator

cartogram-docker provides a Docker Compose setup for running [cartogram-web](https://github.com/go-cart-io/cartogram-web) locally or on-premise. For quick online use, visit https://go-cart.io instead.

## Prerequisites for Windows Users

1. Install Ubuntu on Windows using [Microsoft's WSL 2 Installation Guide](https://learn.microsoft.com/en-us/windows/wsl/install).

   📌 All subsequent instructions should be run in the Ubuntu WSL environment.

2. Add the following settings to `/etc/wsl.conf`:

   ```
   [automount]
   enabled = true
   mountfstab = true
   root = /mnt/
   options = metadata,uid=1000,gid=1000,umask=0022,fmask=11,case=off
   ```

## Setup Instructions

1. Install Docker and Docker Compose using [Official installation guide](https://docs.docker.com/engine/install/)

2. Clone repository

   ```shell
   git clone https://github.com/go-cart-io/cartogram-docker.git
   cd cartogram-docker
   ```

3. Copy configuration files

   ```shell script
   cp password.txt.dist password.txt && cp .env.dist .env
   ```

4. Modify `password.txt` and `.env` as needed, leaving `password.txt.dist` and `.env.dist` unchanged.

5. Run the following commands from the root directory of this repository (i.e., the folder containing this readme):

   ```shell script
   export TAG=":$(cd cartogram-web/ && git rev-parse --short HEAD)"
   echo "$TAG" # to make sure the tag is what you expect
   docker compose pull web
   docker compose up -d
   ```

   The first time you run this command, it may take a while to download and install dependencies. Once everything stats up, you should be able to access the locally-running instance of go-cart.io website at [http://localhost:5001](http://localhost:5001).

6. When you would like to stop the go-cart.io local instance, use the command:

   ```shell script
   docker compose stop
   ```

## Contributing

We welcome and encourage contributions! For details on setting up a development environment and contribution guidelines, please visit our [Developer Guide](https://guides.go-cart.io/developers).
