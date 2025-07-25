# bordel

Helper scripts to manage, build, and package OpenXT builds.

## Setting up a Docker container

NOTE: these instructions assume that you already have a basic build tree set up.
Follow the Quick Start instructions at https://github.com/OpenXT/openxt-manifest
to set this up.

- Use `./bordel docker list` and note the desired Dockerfile name.
- Run `./bordel docker create {my_container_name} {Dockerfile_name}` to create the container.

## Building OpenXT with Docker

- First, the build must be configured with `./bordel config [-t template_name] [-b branch]`.
    * The available template names can be found in `./templates` (the template names match the
      directory names in this dir).
    * You can optionally provide a build id using `./bordel -i BUILD_ID config ...`; the default
      build id is YYMMDD, today's date. Note that this build id must be passed in for the rest
      of the steps if you elect to use your own.
- Next, generate your certs with `./bordel [-i BUILD_ID] certs create all`.
- To run the full build, run `./bordel [-i BUILD_ID] docker build {my_container_name}`.
- You can optionally run partial build steps manually from inside the container:
    * Use `./bordel docker enter {my_container_name}` to enter the container as user `build`.
    * Navigate to `/home/build/openxt/<build-BUILD_ID>` to access your build tree.
    * Source the build_env file to get access to necessary OE variables (`. build_env`)
    * Build a single package by running `MACHINE=<machine_name> bitbake <package_name>`
    * You can run the full build if desired by running `~/openxt/openxt/bordel [-i BUILD_ID] build`
  - To create the ISO image after build, run `./bordel [-i BUILD_ID] docker deploy {my_container_name} iso`

## Build directory separation

There are situations where it is desirable to have the build directory under a separate directory
tree from the shared build artifacts, here on referred to as the build base directory. To configure
such an environment a top level`-b {Build Base Path}` flag must passed to bordel, this is not to be confused with the `-b` flag
passed to the config command. *NB*: the resulting configured build directory will have absolute path
symlinks to the shared directories in the build base directory.
