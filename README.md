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

This will start all the services listed in `docker-compose.yml`  (`web`, `redis`, and `postgres`), while ensuring the same image of `cartogram-web` is pulled as the submodule's version. We achive this by making sure the short SHA of the submodule's HEAD is the same as the docker image's tag. The first time you run this command, it may take a while to download and install dependencies. Once everything stats up, you should be able to access the locally-running instance of go-cart.io website at [http://localhost:5001](http://localhost:5001).

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

### Tablet testing via local network

#### Windows

First, you must allow other devices to access docker. If you use wsl on Windows, you can run the following command in command prompt (may need to run as administrator):

```shell script
netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=5001

bash.exe -c "ip -4 addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'"

netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=5001 connectaddress=<your-docker-ip> connectport=5001
```

Reference: https://stackoverflow.com/questions/61002681/connecting-to-wsl2-server-via-local-network

If you are running the application in development mode, you should allow other computher to access port 5173 (for Windows, you can see https://www.devopinion.com/access-localhost-from-another-computer-on-the-same-network/). If you cannot access 5173, check firewall, e.g., delete rules that block node.js.

You should be able to access the application using `http:\\your-local-ip:5001`

#### macOS (and iPad)

Work in progress. Contact @adisidev for help getting set up.

### Tablet testing via USB (Andriod)

Please follow this tutorial https://developer.chrome.com/docs/devtools/remote-debugging/. Then, setup port forwarding for port 5001 and 5173, see https://developer.chrome.com/docs/devtools/remote-debugging/local-server/.

## Pushing changes to production

### Making changes to `cartogram-docker`

The `cartogram-docker` repository is simply meant to
1. provide a replicable development environment for you to make changes to the `cartogram-web` repository;
2. provide a way to easily deploy `cartogram-web` (and its dependencies, like `redis` and `postgres`) to production.

Thus, we recommend making changes to `cartogram-docker` only when you need to make changes to deployment process.
If you've changed something in the deployment process, you're likely going to have to either:
1. Manually deploy the changes to the production environment, or
2. Redeploy the entire application, using the instructions in `cartogram-ansible`.

### Making changes to `cartogram-web`

If you only want to make changes to the website, then you may follow the steps below:
1. Make changes to a new branch in the `cartogram-web` submodule.
   - You can do this by running `git checkout -b <branch-name>` in the `cartogram-web` directory.
2. Once you are satisfied with the changes and have tested that they work as expected, create a pull request to merge to `cartogram-web:main` on GitHub.
   - When your pull request is approved, a new docker image of `cartogram-web` will automatically be created. This image will be tagged with the short SHA of the merge commit and pushed to Docker Hub.
   - Before proceeding to step 3, please wait for the relevant run on [`cartogram-web`'s GitHub actions page](https://github.com/go-cart-io/cartogram-web/actions/workflows/docker-publish.yml) to complete successfully (ensuring the image is built and pushed to Docker Hub). The run should be titled "Merge pull request #`X` from go-cart-io/`Y`" where `X` is the number of the pull request you created, and `Y` is the name of the branch you created.
3. In `cartogram-docker`, change the `cartogram-web` submodule to point to the new commit.
   - You can do this by running `git checkout main && git pull` in the `cartogram-web` directory (assuming your changes were merged to `main`).
4. Push this single change to `cartogram-docker:main`. If you do not have write access to that branch, you can create a pull request to merge your branch to `cartogram-docker:main`.
   - NOTE: The only change in this commit should be a revision of the `cartogram-web` submodule's SHA reference. Running `git diff` in `cartogram-docker`'s root should show something like this
   ```diff
   diff --git a/cartogram-web b/cartogram-web
   index c55e268..dd67e84 160000
   --- a/cartogram-web
   +++ b/cartogram-web
   @@ -1 +1 @@
   -Subproject commit c55e2689c1ba50515a1b6646159ac23bc6a41a03
   +Subproject commit dd67e840dcd8982186e2c6215740d76d3f93dfb8
   ```
   - When any changes are merged to `main` in `cartogram-docker`, a github action will automatically `ssh` into the production server and pull the `cartogram-web` image specified by the submodule's `HEAD` commit.

### Making changes to `cartogram-cpp`

If you only want to make changes to the `cartogram-cpp` executable, then you may follow the steps below:

1. Make changes to a new branch in the `cartogram-cpp` repository.
2. Once you are satisfied with the changes and have tested that they work as expected, create a pull request to merge to `cartogram-cpp:main` on GitHub.
   - When your pull request is approved, a new binary release should automatically be built after a few minutes.
   - Before proceeding to step 3, please wait for the relevant run on [`cartogram-cpp`'s GitHub actions page](https://github.com/mgastner/cartogram-cpp/actions/workflows/build.yml) to complete successfully (ensuring the binary is built and pushed to GitHub releases). The run should be titled "Merge pull request #`X` from mgastner/`Y`" where `X` is the number of the pull request you created, and `Y` is the name of the branch you created.
3. Create a new branch on `cartogram-web` and follow the instructions on `cartogram-web/README.md` to update the `cartogram` binary.
4. Treat this as a change to `cartogram-web` and follow the instructions in the [Making changes to `cartogram-web`](#making-changes-to-cartogram-web) section above, starting at step 2.

# Contact

If you encounter issues using this code, or have any questions, please contact Atima at atima.tharatipyakul@singaporetech.edu.sg.

# Contributing

Contributions are highly encouraged, and we love pull requests! Feel free to take a stab at any of the open issues. If there are no issues, don't hesitate to suggest new features, or report bugs under the issues tab (and, optionally, even subsequently send in pull requests with said enhancements or fixes). If you need help getting setup or more guidance contributing, please @ any of the main contributors under the issue you'd like to help solve, and we'll be happy to guide you!