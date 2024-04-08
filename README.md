# Docker Application for go-cart.io

This repository contains a Docker compose file that makes it easy to install and run go-cart.io website. Steps to run the application is as follow:

1. [Install Docker and Docker Compose](docs/docker.md).
2. Clone this repository.

```shell script
$ git clone https://github.com/go-cart-io/cartogram-docker.git
```

3. Copy `password.txt.dist` to `password.txt` and `.env.dist` to `.env`. You may modify the content in the files as needed.
4. Run the following command from the root directory of this repository (i.e., the folder containing this readme).

```shell script
cartogram-docker$ docker-compose up -d
```

The first time you run this command it may take a while to download and install dependencies. You can access the locally-running go-cart.io website at http://localhost:5001.

## Local development and testing

To start the go-cart.io web application for local development and testing, you will need to clone `cartogram-web` repository from https://github.com/go-cart-io/cartogram-web and put it inside `cartogram-docker`. The project stucture should be as follow:

```
cartogram-docker
├── cartogram-web
│   ├── frontend
│   ├── internal
│   ├── ...
├── docs
├── .env
├── docker-compose.yml
├── docker-compose-dev.yml
├── ...
```

Then, edit `.env` file as follow:

```
COMPOSE_FILE=docker-compose-dev.yml
CARTOGRAM_DEBUG=TRUE
```

You need to install [Node.js](https://nodejs.org), then run the following commands to start frontend development server:

```shell script
# After install Nodejs
cartogram-docker$ cd cartogram-web/frontend
cartogram-docker/cartogram-web/frontend$ npm install
cartogram-docker/cartogram-web/frontend$ npm run dev
```

Then, open anoter terminal and run the following command from the root directory of this repository (i.e., the folder containing this readme):

```shell script
cartogram-docker$ docker-compose up
```

After all of the Docker containers have started up, all of their output will be collected into the terminal window so you can see how the web application is responding to requests, and if it encounters any errors. You can access the locally-running go-cart.io website at http://localhost:5001.

When you make changes to the code in the `cartogram-web` repository, their respective servers will reload automatically when you save your changes.

When you would like to shut down the go-cart.io web application, simply press Ctrl-C in the terminal window you started Docker Compose in. After a few moments, the state of all the Docker containers will be saved and the application will gracefully come to a halt.

The following commands could be useful:

- `docker-compose up -d` to start docker containers in the background (so you can use command line for other thing);
- `docker exec -it cartogram-docker_web_1 /bin/bash` to access the cartogram-web container (so you can use python to run a file, such as `python addmap.py init test`);
- `docker start cartogram-docker_web_1` to start the cartogram-web container (which is useful when the container stops because of error in python code);
- `docker logs --follow cartogram-docker_web_1` to see the log in the cartogram-web container.

### Tablet testing via local network

First, you must allow other devices to access docker. If you use wsl on Windows, you can run the following command in command prompt (may need to run as administrator):

```shell script
netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=5001

bash.exe -c "ip -4 addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'"

netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=5001 connectaddress=<your-docker-ip> connectport=5001
```

Reference: https://stackoverflow.com/questions/61002681/connecting-to-wsl2-server-via-local-network

If you are running the application in development mode, you should allow other computher to access port 5173 (for Windows, you can see https://www.devopinion.com/access-localhost-from-another-computer-on-the-same-network/). If you cannot access 5173, check firewall, e.g., delete rules that block node.js.

You should be able to access the application using `http:\\your-local-ip:5001`

### Tablet testing via USB (Andriod)

Please follow this tutorial https://developer.chrome.com/docs/devtools/remote-debugging/. Then, setup port forwarding for port 5001 and 5173, see https://developer.chrome.com/docs/devtools/remote-debugging/local-server/.

### Publishing changes to production

Run the following command:

```shell script
cartogram-docker/cartogram-web/frontend$ npm run build
cartogram-docker$ docker-compose build web
```

You should found updated frontend code in `internal/static/dist`. The docker image should be updated with new code. You may also want to push docker image into docker repository.

```shell script
cartogram-docker$ docker login
cartogram-docker$ docker push gocartio/cartogram-web:latest
```

# Contact

If you encounter issues using this code, or have any questions, please contact Atima at atima.tharatipyakul@singaporetech.edu.sg.
