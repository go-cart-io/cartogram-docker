# Docker Application for go-cart.io

This repository contains a Docker compose file that makes it easy to install and run go-cart.io website. Steps to run the application is as follow:

1. [Install Docker and Docker Compose](docs/docker.md).
2. Clone this repository with the `--recurse-submodules` flag like so:

```shell script
git clone --recurse-submodules https://github.com/go-cart-io/cartogram-docker.git
```

3. Copy `password.txt.dist` to `password.txt` and `.env.dist` to `.env` using:

```shell script
cp password.txt.dist password.txt && cp .env.dist .env
```

4. Modify `password.txt` and `.env` as needed, leaving `password.txt.dist` and `.env.dist` unchanged

5. Depending on whether you want to develop the app locally, or run it in production mode, follow the instructions under [Development](#development) or [Production](#production) respectively.
   - Development mode is for when you want to make changes to the code and see the changes reflected in real-time (hot-reloading enabled).
   - Production mode instructions are helpful when you would like to deploy the application to a server, or when you would like to test the application in a production-like environment.

## Production

1. Run the following commands from the root directory of this repository (i.e., the folder containing this readme).

```shell script
export TAG=":$(git -C cartogram-web/ rev-parse --short HEAD)"
docker compose up -d
```

This will start all the services listed in `docker-compose.yml` (`web`, `redis`, and `postgres`), while ensuring the same image of `cartogram-web` is pulled as the submodule's version. We achive this by making sure the short SHA of the submodule's HEAD is the same as the docker image's tag. The first time you run this command, it may take a while to download and install dependencies. Once everything stats up, you should be able to access the locally-running instance of go-cart.io website at [http://localhost:5001](http://localhost:5001).

## Development

1. Install [Node.js](https://nodejs.org).

2. Then, run the following commands to start frontend development server:

```shell script
cd cartogram-web/frontend
npm install # Install dependencies listed in package.json
npm run dev # Start development server
```

3. Next, open another terminal and run the following commands in the root directory of this repository (i.e., the folder containing this readme):

```shell script
bash cartogram-web/tools/pull-executable.sh
docker compose -f docker-compose-dev.yml --profile web up -d
```

The first command pulls the same version of the executable (provided by [cartogram-cpp](https://github.com/mgastner/cartogram-cpp)) as required by currently checked-out HEAD of the `cartograb-web` submodule.

The second command starts all the services (i.e. docker containers) listed in `docker-compose-dev.yml` under the `web` profile (`web`, `redis`, and `postgres`).

Once all the Docker containers have started, their output will be displayed in the terminal window you opened in step 3. This window will let you monitor how the web application responds to requests and identify any errors. You can access the locally running go-cart.io website at [http://localhost:5001](http://localhost:5001).

When you make changes to the code in the `cartogram-web` repository, their respective servers will reload automatically when you save your changes.

When you would like to shut down the go-cart.io web application, simply press `Ctrl-C` in the terminal window you started Docker Compose in. After a few moments, the state of all the Docker containers will be saved and the application will gracefully come to a halt.

You may find the following commands useful when developing:

- `docker exec -it cartogram-docker-web-1 /bin/bash` to access the cartogram-web container (so you can use the command line in the container, say to run `python addmap.py init test`);
- `docker start cartogram-docker-web-1` to start the cartogram-web container (which is useful when the container stops because of, say, an error in your python code);
- `docker logs --follow cartogram-docker-web-1` to see the logs in the cartogram-web container.
- `docker compose up -d` to start docker containers in the background (so you can use command line for other things);

# Contributing

Contributions are highly encouraged, and we love pull requests! Feel free to take a stab at any of the open issues. If there are no issues, don't hesitate to suggest new features, or report bugs under the issues tab (and, optionally, even subsequently send in pull requests with said enhancements or fixes). If you need help getting setup or more guidance contributing, please @ any of the main contributors under the issue you'd like to help solve, and we'll be happy to guide you!
