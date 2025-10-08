# cartogram-docker: Docker Application for Web-based Cartogram Generator

Local Docker deployment allows you to run [cartogram-web](https://github.com/go-cart-io/cartogram-web) on your own machine or private server using the provided Docker Compose setup. This is ideal for development, testing, or self-hosted deployments.

For a hassle-free online experience, you can also use the hosted version at https://go-cart.io.

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

1. Install Docker and Docker Compose using [Official installation guide](https://docs.docker.com/engine/install/).

   For Linux and Windows wsl users:

   - Add your user to the docker group by using a terminal to run:

   ```shell script
   sudo usermod -aG docker $USER
   ```

   - Sign out and back in again so your changes take effect.

2. Clone repository:

   ```shell script
   git clone https://github.com/go-cart-io/cartogram-docker.git
   cd cartogram-docker
   ```

3. Copy configuration files:

   ```shell script
   cp password.txt.dist password.txt && cp .env.dist .env
   ```

4. Modify `password.txt` and `.env` as needed, leaving `password.txt.dist` and `.env.dist` unchanged.

5. Run the following commands from the root directory of this repository (i.e., the folder containing this readme):

   ```shell script
   export TAG=":latest"
   docker compose pull web
   docker compose up -d
   ```

   The first time you run this command, it may take a while to download and install dependencies. Once everything stats up, you should be able to access the locally-running instance of go-cart.io website at [http://localhost:5001](http://localhost:5001).

   You can also change the TAG to a specific version. For example:

   ```shell script
   export TAG=":1.0.0"
   ```

6. When you would like to permanently stop the go-cart.io local instance, use the command:

   ```shell script
   docker compose down
   ```

## Usage

Please follow instructions on https://guides.go-cart.io/#/tutorials/online, but access your locally-running instance at [http://localhost:5001](http://localhost:5001) instead of https://go-cart.io.

## Contributing

We welcome and encourage contributions! For details on setting up a development environment and contribution guidelines, please visit our [Developer Guide](https://guides.go-cart.io/#/developers).
