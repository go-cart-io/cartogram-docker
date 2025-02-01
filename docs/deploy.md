# Deployment of go-cart.io with Docker

The go-cart.io web application consists of several components which must be deployed properly for the application to work for end-users. We suggest using [cartogram-ansible](https://github.com/go-cart-io/cartogram-ansible) for initial deployment. After that, deployment of updated versions of the application can be automated. This document provides step-by-step instructions for performing the following tasks:

- [Making changes to `cartogram-cpp`](#making-changes-to-cartogram-cpp)
- [Making changes to `cartogram-web`](#making-changes-to-cartogram-web)
- [Making changes to `cartogram-docker`](#making-changes-to-cartogram-docker)

## Making changes to `cartogram-cpp`

If you only want to make changes to the `cartogram-cpp` executable, then you may follow the steps below:

1. Make changes to a new branch in the `cartogram-cpp` repository.
2. Once you are satisfied with the changes and have tested that they work as expected, create a pull request to merge to `cartogram-cpp:main` on GitHub.
   - When your pull request is approved, a new binary release should automatically be built after a few minutes.
   - Before proceeding to step 3, please wait for the relevant run on [`cartogram-cpp`'s GitHub actions page](https://github.com/mgastner/cartogram-cpp/actions/workflows/build.yml) to complete successfully (ensuring the binary is built and pushed to GitHub releases). The run should be titled "Merge pull request #`X` from mgastner/`Y`" where `X` is the number of the pull request you created, and `Y` is the name of the branch you created.
3. Create a new branch on `cartogram-web` and follow the instructions on [go-cart-io/cartogram-web](https://github.com/go-cart-io/cartogram-web) to update the `cartogram` binary.
4. Treat this as a change to `cartogram-web` and follow the instructions in the [Making changes to `cartogram-web`](#making-changes-to-cartogram-web) section below, starting at step 3.

## Making changes to `cartogram-web`

If you only want to make changes to the website, then you may follow the steps below:

1. Make changes to a new branch in the `cartogram-web` submodule.
   - You can do this by running `git checkout -b <branch-name>` in the `cartogram-web` directory.
2. If you made changes to the frontend, run the following command to compile all frontend changes for distribution:
   ```
   npm run build
   ```
3. Once you are satisfied with the changes and have tested that they work as expected, create a pull request to merge to `cartogram-web:main` on GitHub.
   - When your pull request is approved, a new docker image of `cartogram-web` will automatically be created. This image will be tagged with the short SHA of the merge commit and pushed to Docker Hub.
   - Before proceeding to step 3, please wait for the relevant run on [`cartogram-web`'s GitHub actions page](https://github.com/go-cart-io/cartogram-web/actions/workflows/docker-publish.yml) to complete successfully (ensuring the image is built and pushed to Docker Hub). The run should be titled "Merge pull request #`X` from go-cart-io/`Y`" where `X` is the number of the pull request you created, and `Y` is the name of the branch you created.
4. In `cartogram-docker`, change the `cartogram-web` submodule to point to the new commit.
   - You can do this by running `git checkout main && git pull` in the `cartogram-web` directory (assuming your changes were merged to `main`).
5. Push this single change to `cartogram-docker:main`. If you do not have write access to that branch, you can create a pull request to merge your branch to `cartogram-docker:main`.
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
6. Treat this as a change to `cartogram-docker` and follow the instructions in the [Making changes to `cartogram-docker`](#making-changes-to-cartogram-docker) section below.

## Making changes to `cartogram-docker`

If you make changes to the cartogram-docker, follow these steps:

1. Test that everything works locally using:

```shell script
cd cartogram-docker
docker compose -f docker-compose.yml pull web
docker compose -f docker-compose.yml up
```

2. If there are no errors and the changes reflect on your local machine, the GitHub Action should automatically be able to deploy the changes to go-cart.io. Run the `pull-image.yml` action, which will ssh into the production server and pull the cartogram-docker and cartogram-web images specified by the submodule's HEAD commit.

**Important**: Remember to test generating a cartogram, not just running the application, before pushing the change. Some changes, such as changing the Postgres password, may cause the website to fail only when generating a cartogram.

3. If there are issues, you may need to manually deploy the changes to the production environment or redeploy the entire application using the instructions in [cartogram-ansible](https://github.com/go-cart-io/cartogram-ansible).
