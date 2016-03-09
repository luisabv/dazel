# dazel
Run Google's bazel inside a docker container via a seamless proxy.

bazel is awesome at creating fast and reproducible builds on your own development environment.
The problem is that it works in an imperfect and non-portable environment.

Enter dazel.

dazel allows you to create your build environment as a Docker image, either via a Dockerfile or a prebuilt repository.
The tool itself is a simple python script that sends the command line arguments directly to bazel inside the container, and maps all of the necessary volumes to make it seamless to you.
It uses the 'docker exec' command to achieve this, and maps the current directory and the bazel-WORKDIR link directory so that the results appear on the host as if you ran the command locally on the host.

It is run the same way you would bazel:
```bash
dazel build //my/cool/package/...
dazel run //my/cool/package:target
```

This was a simple build and run.
The command line arguments were sent as-is into the docker container, and the output was run in the same manner inside the container.

Running the command for the first time will start the container on it's own, and it will automatically detect if there is need to rebuild or restart the container (if the Dockerfile is newer than the conatiner).
You can configure anything you need through the ".dazelrc" file in the same directory.
Take a look at the configuration section for information on how to write one.

## Installation

### Dependencies
```bash
apt-get install python python-pip
apt-get install docker
```

### Install dazel
```bash
pip install dazel
```

That's all there is to it.
Even bazel is not required!

## Configuration

You can configure dazel in two ways (or combine):
* A .dazelrc file in the current directory.
* Environment variables with the configuration parameters mentioned below.

Note that specific environment variables supercede the values in the .dazelrc file.

The possible parameters to set are:
* DAZEL_INSTANCE_NAME="name of the docker container to run" [Default: "dazel"]
* DAZEL_IMAGE_NAME="name of the dazel image to build or pull" [Default: "dazel"]
* DAZEL_DOCKERFILE="path to the Dockerfile to use to build the dazel image" [Default: "Dockerfile.dazel"]
* DAZEL_REPOSITORY="the repository to pull the dazel image from" [Default: "dazel"]
* DAZEL_DIRECTORY="the directory to build the dazel image in" [Default: $PWD]
* DAZEL_COMMAND="the command to run when building: [Default: "/bazel/output/bazel"]
* DAZEL_VOLUMES=["<host dir>:<docker dir>", ...] or "<host dir>:<docker dir>,..." [Default: ""]
* DAZEL_RUN_DEPS=["run_dependency/image_to_load:tag",...] or "another/image:tag,..." [Default: ""]
* DAZEL_NETWORK="the name of the network on which to load all run dependencies and dazel container" [Default: "dazel"]

