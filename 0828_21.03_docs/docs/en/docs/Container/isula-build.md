# Container Image Building


<!-- TOC -->

* [Installation](#installation)
    * [Preparations](#preparations)
        * [Installing isula-build](#installing-isula-build)
* [Configuring and Managing the isula-build Service](#configuring-and-managing-the-isula-build-service)
    * [Configuring the isula-build Service](#configuring-the-isula-build-service)
    * [Managing the isula-build Service](#managing-the-isula-build-service)
        * [(Recommended) Using systemd for Management](#recommended-using-systemd-for-management)
        * [Directly Running isula-builder](#directly-running-isula-builder)
* [Usage Guidelines](#usage-guidelines)
    * [Prerequisites](#prerequisites)
    * [Overview](#overview)
    * [ctr-img: Container Image Management](#ctr-img-container-image-management)
        * [build: Container Image Build](#build-container-image-build)
        * [image: Viewing Local Persistent Build Images](#image-viewing-local-persistent-build-images)
        * [import: Importing a Basic Container Image](#import-importing-a-basic-container-image)
        * [load: Importing Cascade Images](#load-importing-cascade-images)
        * [rm: Deleting a Local Persistent Image](#rm-deleting-a-local-persistent-image)
        * [save: Exporting Cascade Images](#save-exporting-cascade-images)
        * [tag: Tagging Local Persistent Images](#tag-tagging-local-persistent-images)
        * [pull: Pulling an Image To a Local Host](#pull-pulling-an-image-to-a-local-host)
        * [push: Pushing a Local Image to a Remote Repository](#push-pushing-a-local-image-to-a-remote-repository)
    * [info: Viewing the Operating Environment and System Information](#info-viewing-the-operating-environment-and-system-information)
    * [login: Logging In to the Remote Image Repository](#login-logging-in-to-the-remote-image-repository)
    * [logout: Logging Out of the Remote Image Repository](#logout-logging-out-of-the-remote-image-repository)
    * [version: Querying the isula-build Version](#version-querying-the-isula-build-version)
    * [manifest: manifest list management (experiment feature)](#manifest-manifest list management)
    * [create: manifest list creation](#create-manifest list creation)
    * [annotate: manifest list update](#annotate-manifest list update)
    * [inspect: manifest list query](#inspect-manifest list query)
* [Directly Integrating a Container Engine](#directly-integrating-a-container-engine)
    * [Integration with iSulad](#integration-with-isulad)
    * [Integration with Docker](#integration-with-docker)
* [\<span id="Appendix"\>Appendix](#span-idappendixappendix)
    * [Command Line Parameters](#command-line-parameters)
    * [Communication Matrix](#communication-matrix)
    * [File and Permission](#file-and-permission)


<!-- /TOC -->

## Overview

isula-build is a container image build tool developed by the iSula container team. It allows you to quickly build container images using Dockerfiles.

The isula-build uses the server/client mode. The isula-build functions as a client and provides a group of command line tools for image build and management. The isula-builder functions as the server, processes client management requests, and functions as the daemon process in the background.

![isula-build architecure](./figures/isula-build_arch.png)

Note:

- Currently, isula-build supports only Docker images.

## Installation

### Preparations

To ensure that isula-build can be successfully installed, the following software and hardware requirements must be met:

- Supported architectures: x86_64 and AArch64
- Supported OS: openEuler
- You have the permissions of the root user.

#### Installing isula-build

Before using isula-build to build a container image, you need to install the following software packages:



**(Recommended) Method 1: Using YUM**

1. Configure the openEuler yum source.

2. Log in to the target server as the root user and install isula-build.

   ```
   sudo yum install -y isula-build
   ```



**Method 2: Using the RPM Package**

1. Obtain the isula-build-*.rpm installation package from the openEuler yum source, for example, isula-build-0.9.5-6.oe1.x86_64.rpm.

2. Upload the obtained RPM software package to any directory on the target server, for example, /home/.

3. Log in to the target server as the root user and run the following command to install isula-build:

   ```
   sudo rpm -ivh /home/isula-build-*.rpm
   ```

> **Note:** 
After the installation is complete, you need to manually start the isula-build service. For details about how to start the service, see "Managing the isula-build Service."

## Configuring and Managing the isula-build Service

### Configuring the isula-build Service

After the isula-build software package is installed, the systemd starts the isula-build service based on the default configuration contained in the isula-build software package on the isula-build server. If the default configuration file on the isula-build server cannot meet your requirements, perform the following operations to customize the configuration file: After the default configuration is modified, restart the isula-build server for the new configuration to take effect. For details, see "Managing the isula-build Service."

Currently, the isula-build server contains the following configuration file:

- /etc/isula-build/configuration.toml: general isula-builder configuration file, which is used to set the isula-builder log level, persistency directory, runtime directory, and OCI runtime. Parameters in the configuration file are described as follows:

| Configuration Item    | Mandatory or Optional | Description                         | Value                         |
| --------- | -------- | --------------------------------- | ----------------------------------------------- |
| debug | Optional | Indicates whether to enable the debug log function. | true: Enable the debug log function. false: Disable the debug log function. |
| loglevel | Optional | Sets the log level.          | debug<br/>info<br/>warn<br/>error                |
| run_root | Mandatory | Sets the root directory of runtime data. | For example, /var/run/isula-build/ |
| data_root | Mandatory | Sets the local persistency directory. | For example, /var/lib/isula-build/ |
| runtime | Optional | Sets the runtime type. Currently, only runc is supported. | runc                                            |
| group | Optional | Sets an owner group for the local socket file isula_build.sock so that non-privileged users in the group can use isula-build. | isula |

- /etc/isula-build/storage.toml: configuration file for local persistent storage, including the configuration of the storage driver in use.

| Configuration Item    | Mandatory or Optional | Description                         |
| ------ | -------- | ------------------------------ |
| driver | Optional | Storage driver type. Currently, overlay2 is supported. |

  For more settings, see [containers-storage.conf.5.md](https://github.com/containers/storage/blob/master/docs/containers-storage.conf.5.md).


- /etc/isula-build/registries.toml: configuration file for each image repository.

| Configuration Item    | Mandatory or Optional | Description                         |
| ------------------- | -------- | ------------------------------------------------------------ |
| registries.search | Optional | Search domain of the image repository. Only listed image repositories can be found. |
| registries.insecure | Optional | Accessible insecure image repositories. Listed image repositories cannot pass the authentication and are not recommended. |

  For more settings, see [containers-registries.conf.5.md](https://github.com/containers/image/blob/master/docs/containers-registries.conf.5.md).

- /etc/isula-build/policy.json: image pull/push policy file. Note: Currently, this parameter cannot be configured.

>![](./public_sys-resources/icon-note.gif) **Note:** 
>
> - isula-build supports the preceding configuration file with the maximum size of 1 MiB.
> - The persistent working directory dataroot cannot be configured on the memory disk, for example, tmpfs.
> - Currently, only overlay2 can be used as the underlying graphdriver.
> - Before setting the --group option, ensure that the corresponding user group has been created on a local OS and non-privileged users have been added to the group. After the isula-builder is restarted, non-privileged users can use the isula-build function. In addition, to ensure permission consistency, the array of the isula-build configuration file directory /etc/isula-build is set to the group specified by --group.



### Managing the isula-build Service

Currently, openEuler uses systemd to manage the isula-build service. The isula-build software package contains the systemd service file. After installing the isula-build software package, you can use the systemd tool to start or stop the isula-build service. You can also manually start the isula-builder software. Note that only one isula-builder process can be started on a node at a time.

>![](./public_sys-resources/icon-note.gif) **Note:** 
> Only one isula-builder process can be started on a node at a time.

#### (Recommended) Using systemd for Management

You can run the following systemd commands to start, stop, and restart the isula-build service:

- Run the following command to start the isula-build service:

  ```sh
  sudo systemctl start isula-build.service
  ```

- Run the following command to stop the isula-build service:

  ```sh
  sudo systemctl stop isula-build.service
  ```

- Run the following command to restart the isula-builder service:

  ```sh
  sudo systemctl restart isula-build.service
  ```

The systemd service file of the isula-build software installation package is stored in the `/usr/lib/systemd/system/isula-build.service` directory. If you need to modify the systemd configuration of the isula-build service, modify the file and run the following command to make the modification take effect. Then restart the isula-build service based on the systemd management command.

```sh
sudo systemctl daemon-reload
```

#### Directly Running isula-builder

You can also run the isula-builder command on the server to start the service. The isula-builder command can contain flags for service startup. The following flags are supported:

- -D, --debug: whether to enable the debugging mode.
- --log-level: log level. The options are debug, info, warn, and error. The default value is info.
- --dataroot: local persistency directory. The default value is /var/lib/isula-build/.
- --runroot: runtime directory. The default value is /var/run/isula-build/.
- --storage-driver: underlying storage driver type.
- --storage-opt: underlying storage driver configuration.
- --group: an owner group for the local socket file isula_build.sock so that non-privileged users in the group can use isula-build. The default owner group is "isula".

>![](./public_sys-resources/icon-note.gif) **Note:** 
> If the command line startup parameters contain the same configuration options as those in the configuration file, the command line parameters are preferentially used for startup.

Start the isula-build service. For example, to specify the local persistency directory /var/lib/isula-build and disable debugging, run the following command:

```sh
sudo isula-builder --dataroot "/var/lib/isula-build" --debug=false
```

## Usage Guidelines

### Prerequisites

isula-build depends on the executable file runc to build the RUN command in the Dockerfile. Therefore, the runc must be pre-installed in the running environment of isula-build. The installation method depends on the application scenario. If you do not need to use the complete docker-engine tool chain, you can install only the docker-runc RPM package.

```sh
sudo yum install -y docker-runc
```

If you need to use a complete docker-engine tool chain, install the docker-engine RPM package, which contains the executable file runc by default.

```sh
sudo yum install -y docker-engine
```

>![](./public_sys-resources/icon-note.gif) **Note:** 
>Users must ensure the security of OCI runtime (runc) executable files to prevent malicious replacement.



### Overview

The isula-build client provides a series of commands for building and managing container images. Currently, the isula-build client provides the following command lines:

- ctr-img: manages container images. The ctr-img command contains the following subcommands:
  - build: builds a container image based on the specified Dockerfile.
  - images: lists local container images.
  - import: imports a basic container image.
  - load: imports a cascade image.
  - rm: deletes a local container image.
  - save: exports a cascade image to a local disk.
  - tag: adds a tag to a local container image.
  - pull: pulls an image to a local host.
  - push: pushes a local image to a remote repository.
- info: displays the running environment and system information of isula-build.
- login: logs in to the remote container image repository.
- logout: logs out of the remote container image repository.
- version: displays the versions of isula-build and isula-builder.

>![](./public_sys-resources/icon-note.gif) **Note:** 
The isula-build completion and isula-builder completion commands are used to generate the bash command completion script. This command is implicitly provided by the command line framework and is not displayed in the help information.



The following describes how to use these commands in detail.




### ctr-img: Container Image Management

The isula-build command groups all container image management commands into the `ctr-img` command. The command is as follows:

```
isula-build ctr-img [command]
```

#### build: Container Image Build

The subcommand build of the ctr-img command is used to build container images. The command is as follows:

```
isula-build ctr-img build [flags]
```

The build command contains the following flags:

- --build-arg: string list, which contains variables required during the build process.
- --build-static: key value, which is used to build binary equivalence. Currently, the following key values are included:
   - build-time: string, which indicates that a fixed timestamp is used to build a container image. The timestamp format is YYYY-MM-DD HH-MM-SS.
- -f, --filename: string, which indicates the path of the Dockerfiles. If this parameter is not specified, the current path is used.
- --iidfile: string, which indicates the ID of the image output to a local file. 
- -o, --output: string, which indicates the image export mode and path.
- --proxy: Boolean, which inherits the proxy environment variable on the host. The default value is true.
- --tag: string, which indicates the tag value of the image that is successfully built. 
- --cap-add: string list, which contains permissions required by the RUN command during the build process.

** The following describes the flags in detail. **

**\--build-arg**

Parameters in the Dockerfile are inherited from the command lines. The usage is as follows:

```sh
$ echo "This is bar file" > bar.txt
$ cat Dockerfile_arg
FROM busybox
ARG foo
ADD ${foo}.txt .
RUN cat ${foo}.txt
$ sudo isula-build ctr-img build --build-arg foo=bar -f Dockerfile_arg
STEP  1: FROM busybox
Getting image source signatures
Copying blob sha256:8f52abd3da461b2c0c11fda7a1b53413f1a92320eb96525ddf92c0b5cde781ad
Copying config sha256:e4db68de4ff27c2adfea0c54bbb73a61a42f5b667c326de4d7d5b19ab71c6a3b
Writing manifest to image destinationStoring signatures
STEP  2: ARG foo
STEP  3: ADD ${foo}.txt .
STEP  4: RUN cat ${foo}.txt
This is bar file
Getting image source signatures
Copying blob sha256:6194458b07fcf01f1483d96cd6c34302ffff7f382bb151a6d023c4e80ba3050a
Copying blob sha256:6bb56e4a46f563b20542171b998cb4556af4745efc9516820eabee7a08b7b869
Copying config sha256:39b62a3342eed40b41a1bcd9cd455d77466550dfa0f0109af7a708c3e895f9a2
Writing manifest to image destination
Storing signatures
Build success with image id: 39b62a3342eed40b41a1bcd9cd455d77466550dfa0f0109af7a708c3e895f9a2
```

**\--build-static**

Specifies a static build. That is, when isula-build is used to build a container image, differences between all timestamps and other build factors (such as the container ID and hostname) are eliminated. Finally, a container image that meets the static requirements is built.

When isula-build is used to build a container image, assume that a fixed timestamp is given to the build subcommand and the following conditions are met:

- The build environment is consistent before and after the upgrade.
- The Dockerfile is consistent before and after the build.
- The intermediate data generated before and after the build is consistent.
- The build commands are the same.
- The versions of the third-party libraries are the same.

For container image build, isula-build supports the same Dockerfile. If the build environments are the same, the image content and image ID generated in multiple builds are the same.

--build-static supports the key-value pair option in the k=v format. Currently, the following options are supported:

- build-time: string, which indicates the fixed timestamp for creating a static image. The value is in the format of YYYY-MM-DD HH-MM-SS. The timestamp affects the attribute of the file for creating and modifying the time at the diff layer.

  Example:

  ```sh
  $ sudo isula-build ctr-img build -f Dockerfile --build-static='build-time=2020-05-23 10:55:33' .
  ```

  In this way, the container images and image IDs built in the same environment for multiple times are the same.

**\--iidfile**

Run the following command to output the ID of the built image to a file:

```
isula-build ctr-img build --iidfile filename
```

For example, to export the container image ID to the testfile file, run the following command:

  ```sh
$ sudo isula-build ctr-img build -f Dockerfile_arg --iidfile testfile
  ```

  Check the container image ID in the testfile file.

  ```sh
$ cat testfile
76cbeed38a8e716e22b68988a76410eaf83327963c3b29ff648296d5cd15ce7b
  ```



**\-o, --output**

Currently, -o and --output support the following formats:

- `isulad:image:tag`: directly pushes the image that is successfully built to iSulad, for example, `-o isulad:busybox:latest`. Pay attention to the following restrictions:

  - isula-build and iSulad must be on the same node.
  - The tag must be configured.
  - On the isula-build client, you need to temporarily save the successfully built image as `/var/tmp/isula-build-tmp-%v.tar` and then import it to iSulad. Ensure that the `/var/tmp/` directory has sufficient disk space.

- `docker-daemon:image:tag`: directly pushes the successfully built image to Docker daemon, for example, `-o docker-daemon:busybox:latest`. Pay attention to the following restrictions:
- isula-build and Docker must be on the same node.
  - The tag must be configured.
  
- `docker://registry.example.com/repository:tag`: directly pushes the successfully built image to the remote image repository, for example, `-o docker://localhost:5000/library/busybox:latest`.

- `docker-archive:<path>/<filename>:image:tag`: saves the successfully built image to the local host in Docker image format, for example, `-o docker-archive:/root/image.tar:busybox:latest`.

In addition to flags, the build subcommand also supports an argument whose type is string and meaning is context, that is, the context of the Dockerfile build environment. The default value of this parameter is the current path where isula-build is executed. This path affects the path retrieved by the ADD and COPY commands of .dockerignore and Dockerfile.

**\--proxy**

Specifies whether the container started by the RUN command inherits the proxy-related environment variables http_proxy, https_proxy, ftp_proxy, no_proxy, HTTP_PROXY, HTTPS_PROXY, and FTP_PROXY. The default value of NO_PROXY is true.

When a user configures proxy-related ARG or ENV in the Dockerfile, the inherited environment variables will be overwritten.

Note: If the client and daemon are not running on the same terminal, the environment variables that can be inherited are the environment variables of the terminal where the daemon is located.

**\--tag**

Specifies the tag of the image stored on the local disk after the image is successfully built.

**\--cap-add**

Run the following command to add the permission required by the RUN command during the build process:

```
isula-build ctr-img build --cap-add ${CAP}
```

Example:

```sh
$ sudo isula-build ctr-img build --cap-add CAP_SYS_ADMIN --cap-add CAP_SYS_PTRACE -f Dockerfile
```

> **Note:**
>
> - A maximum of 100 container images can be concurrently built.
> - isula-build supports Dockerfiles with a maximum size of 1 MiB.
> - isula-build supports the .dockerignore file with a maximum size of 1 MiB.
> - Ensure that only the current user has the read and write permissions on the Dockerfiles to prevent other users from tampering with the files.
> - During the build, the RUN command starts the container to build in the container. Currently, isula-build supports the host network only.
> - isula-build only supports the tar.gz compression format.
> - isula-build commits once after each image build stage is complete, instead of each time a Dockerfile line is executed.
> - isula-build does not support cache build.
> - isula-build starts the build container only when the RUN command is built.
> - Currently, the history function of Docker images is not supported.
> - The stage name can start with a digit.
> - The stage name can contain a maximum of 64 characters.
> - isula-build does not support resource restriction on a single Dockerfile build. If resource restriction is required, you can configure a resource limit on the isula-builder.
> - Currently, isula-build does not support a remote URL as the data source of the ADD command in the Dockerfile.
> - The local tarball exported using the 'docker-archive' type is not compressed, you can manually compress the file as required.



#### image: Viewing Local Persistent Build Images

You can run the images command to view the images in the local persistent storage.

```sh
$ sudo isula-build ctr-img images
----------------------------------------------  -----------  -----------------  --------------------------  ------------ 
REPOSITORY                                      TAG          IMAGE ID           CREATED                     SIZE
----------------------------------------------  -----------  -----------------  --------------------------  ------------ 
localhost:5000/library/alpine                   latest       a24bb4013296       2020-20-19 19:59:197        5.85 MB 
<none>                                          <none>       39b62a3342ee       2020-20-38 38:66:387        1.45 MB
----------------------------------------------  -----------  -----------------  --------------------------  ------------
```

**Note**: The image size displayed by running the `isula-build ctr-img images` command may be different from that displayed by running the `docker images` command. When calculating the image size, isula-build directly calculates the total size of .tar packages at each layer, while Docker calculates the total size of files by decompressing the .tar package and traversing the diff directory. Therefore, the statistics are different.



#### import: Importing a Basic Container Image

openEuler releases a basic container image, for example, openEuler-docker.x86_64.tar.xz, with the version. You can run the `ctr-img import` command to import the image to isula-build.

The command is as follows:

```
isula-build ctr-img import [flags]
```

Example:

```sh
$ sudo isula-build ctr-img import ./openEuler-docker.x86_64.tar.xz openeuler:21.03
Import success with image id: "aac8223a40e9c37558477671c4d66692c8ee5d37b28d947e10010911dd0549d8"
$ sudo isula-build ctr-img images
---------------  -----------  -----------------  ------------------------  ------------
 REPOSITORY       TAG          IMAGE ID           CREATED                   SIZE       
---------------  -----------  -----------------  ------------------------  ------------
 openeuler        21.03        aac8223a40e9       2021-04-02 00:56:46       210 MB     
---------------  -----------  -----------------  ------------------------  ------------
```

>![](./public_sys-resources/icon-note.gif) **Note** 
>isula-build supports the import of container basic images with a maximum size of 1 GiB.



#### load: Importing Cascade Images

Cascade images are images that are saved to the local computer by running the docker save or isula-build ctr-img save command. The compressed image package contains a layer-by-layer image package named layer.tar. You can run the ctr-img load command to import the image to isula-build.

The command is as follows:

```
isula-build ctr-img load [flags]
```

Currently, the following flags are supported:

- -i, --input: path of the local .tar package.

Example:

```sh
$ sudo isula-build ctr-img load -i ubuntu.tarGetting image source signatures
Copying blob sha256:cf612f747e0fbcc1674f88712b7bc1cd8b91cf0be8f9e9771235169f139d507c
Copying blob sha256:f934e33a54a60630267df295a5c232ceb15b2938ebb0476364192b1537449093
Copying blob sha256:943edb549a8300092a714190dfe633341c0ffb483784c4fdfe884b9019f6a0b4
Copying blob sha256:e7ebc6e16708285bee3917ae12bf8d172ee0d7684a7830751ab9a1c070e7a125
Copying blob sha256:bf6751561805be7d07d66f6acb2a33e99cf0cc0a20f5fd5d94a3c7f8ae55c2a1
Copying blob sha256:c1bd37d01c89de343d68867518b1155cb297d8e03942066ecb44ae8f46b608a3
Copying blob sha256:a84e57b779297b72428fc7308e63d13b4df99140f78565be92fc9dbe03fc6e69
Copying blob sha256:14dd68f4c7e23d6a2363c2320747ab88986dfd43ba0489d139eeac3ac75323b2
Copying blob sha256:a2092d776649ea2301f60265f378a02405539a2a68093b2612792cc65d00d161
Copying blob sha256:879119e879f682c04d0784c9ae7bc6f421e206b95d20b32ce1cb8a49bfdef202
Copying blob sha256:e615448af51b848ecec00caeaffd1e30e8bf5cffd464747d159f80e346b7a150
Copying blob sha256:f610bd1e9ac6aa9326d61713d552eeefef47d2bd49fc16140aa9bf3db38c30a4
Copying blob sha256:bfe0a1336d031bf5ff3ce381e354be7b2bf310574cc0cd1949ad94dda020cd27
Copying blob sha256:f0f15db85788c1260c6aa8ad225823f45c89700781c4c793361ac5fa58d204c7
Copying config sha256:c07ddb44daa97e9e8d2d68316b296cc9343ab5f3d2babc5e6e03b80cd580478e
Writing manifest to image destination
Storing signatures
Loaded image as c07ddb44daa97e9e8d2d68316b296cc9343ab5f3d2babc5e6e03b80cd580478e
```

>![](./public_sys-resources/icon-note.gif) **Note:** 
>
>The - isula-build load command can only be used to import a compressed image file that contains a single cascade image.
>
>- isula-build allows you to import a container image with a maximum size of 50 GB.



#### rm: Deleting a Local Persistent Image

You can run the rm command to delete an image from the local persistent storage. The command is as follows:

```
isula-build ctr-img rm IMAGE [IMAGE...] [FLAGS]
```

Currently, the following flags are supported:

- -a, --all: deletes all images stored locally.
- -p, --prune: deletes all images that are stored locally and do not have tags.

Example:

```sh
$ sudo isula-build ctr-img rm -p
Deleted: sha256:78731c1dde25361f539555edaf8f0b24132085b7cab6ecb90de63d72fa00c01d
Deleted: sha256:eeba1bfe9fca569a894d525ed291bdaef389d28a88c288914c1a9db7261ad12c
```



#### save: Exporting Cascade Images

You can run the save command to export the cascade images to the local disk. The command is as follows:

```
isula-build ctr-img save [REPOSITORY:TAG]|imageID -o xx.tar
```

The following example shows how to export an image in `image/tag` format:

```sh
$ sudo isula-build ctr-img save busybox:latest -o busybox.tar
Getting image source signatures
Copying blob sha256:50644c29ef5a27c9a40c393a73ece2479de78325cae7d762ef3cdc19bf42dd0a
Copying blob sha256:824082a6864774d5527bda0d3c7ebd5ddc349daadf2aa8f5f305b7a2e439806f
Copying blob sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef
Copying config sha256:21c3e96ac411242a0e876af269c0cbe9d071626bdfb7cc79bfa2ddb9f7a82db6
Writing manifest to image destination
Storing signatures
Save success with image: busybox:latest
```

The following example shows how to export an image in `ImageID` format:

```sh
$ sudo isula-build ctr-img save 21c3e96ac411 -o busybox.tar
Getting image source signatures
Copying blob sha256:50644c29ef5a27c9a40c393a73ece2479de78325cae7d762ef3cdc19bf42dd0a
Copying blob sha256:824082a6864774d5527bda0d3c7ebd5ddc349daadf2aa8f5f305b7a2e439806f
Copying blob sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef
Copying config sha256:21c3e96ac411242a0e876af269c0cbe9d071626bdfb7cc79bfa2ddb9f7a82db6
Writing manifest to image destination
Storing signatures
Save success with image: 21c3e96ac411
```

The following example shows how to export multiple images to the same tarball:

```sh
$ sudo isula-build ctr-img save busybox:latest nginx:latest -o all.tar
Getting image source signatures
Copying blob sha256:eb78099fbf7fdc70c65f286f4edc6659fcda510b3d1cfe1caa6452cc671427bf
Copying blob sha256:29f11c413898c5aad8ed89ad5446e89e439e8cfa217cbb404ef2dbd6e1e8d6a5
Copying blob sha256:af5bd3938f60ece203cd76358d8bde91968e56491daf3030f6415f103de26820
Copying config sha256:b8efb18f159bd948486f18bd8940b56fd2298b438229f5bd2bcf4cedcf037448
Writing manifest to image destination
Storing signaturesGetting image source signatures
Copying blob sha256:e2d6930974a28887b15367769d9666116027c411b7e6c4025f7c850df1e45038
Copying config sha256:a33de3c85292c9e65681c2e19b8298d12087749b71a504a23c576090891eedd6
Writing manifest to image destination
Storing signatures
Save success with image: [busybox:latest nginx:latest]
```

>![](./public_sys-resources/icon-note.gif) **NOTE:** 
>
>- save exports an image in .tar format by default. If necessary, you can save the image and then manually compress it.
>- When exporting an image, specify the image integrity in the format of IMAGE_NAME:IMAGE_TAG.


#### tag: Tagging Local Persistent Images

You can run the tag command to add a tag to a local persistent container image. The command is as follows:

```
isula-build ctr-img tag <imageID>/<imageName> busybox:latest
```

Example:

```sh
$ sudo isula-build ctr-img images
----------------------------------------------  -----------  -----------------  --------------------------  ------------ 
REPOSITORY                                      TAG          IMAGE ID           CREATED                     SIZE
----------------------------------------------  -----------  -----------------  --------------------------  ------------
alpine                                         latest       a24bb4013296       2020-05-29 21:19:46         5.85 MB
----------------------------------------------  -----------  -----------------  --------------------------  ------------
$ sudo isula-build ctr-img tag a24bb4013296 alpine:v1
$ sudo isula-build ctr-img images
----------------------------------------------  -----------  -----------------  --------------------------  ------------
REPOSITORY                                      TAG          IMAGE ID           CREATED                     SIZE
----------------------------------------------  -----------  -----------------  --------------------------  ------------
alpine                                           latest       a24bb4013296       2020-05-29 21:19:46         5.85 MB
alpine                                           v1           a24bb4013296       2020-05-29 21:19:46         5.85 MB
----------------------------------------------  -----------  -----------------  --------------------------  ------------
```

#### pull: Pulling an Image To a Local Host

Run the pull command to pull an image from a remote image repository to a local host. Command format:

```
isula-build ctr-img pull REPOSITORY[:TAG]
```

Example:

```sh
$ sudo isula-build ctr-img pull example-registry/library/alpine:latest
Getting image source signatures
Copying blob sha256:8f52abd3da461b2c0c11fda7a1b53413f1a92320eb96525ddf92c0b5cde781ad
Copying config sha256:e4db68de4ff27c2adfea0c54bbb73a61a42f5b667c326de4d7d5b19ab71c6a3b
Writing manifest to image destination
Storing signatures
Pull success with image: example-registry/library/alpine:latest
```

#### push: Pushing a Local Image to a Remote Repository

Run the push command to push a local image to a remote repository. Command format:

```
isula-build ctr-img push REPOSITORY[:TAG]
```

Example:

```sh
$ sudo isula-build ctr-img push example-registry/library/mybusybox:latest
Getting image source signatures
Copying blob sha256:d2421964bad195c959ba147ad21626ccddc73a4f2638664ad1c07bd9df48a675
Copying config sha256:f0b02e9d092d905d0d87a8455a1ae3e9bb47b4aa3dc125125ca5cd10d6441c9f
Writing manifest to image destination
Storing signatures
Push success with image: example-registry/library/mybusybox:latest
```

>![](./public_sys-resources/icon-note.gif) **NOTE:** 
>
>- Before pushing an image, log in to the corresponding image repository.


### info: Viewing the Operating Environment and System Information

You can run the isula-build info command to view the running environment and system information of isula-build. The command is as follows:

```
 isula-build info [flags]
```

The following flags are supported:

- -H, --human-readable: Boolean. The memory information is printed in the common memory format. The value is 1000 power.
- -V, --verbose: Boolean. The memory usage is displayed during system running.

Example:

```sh
$ sudo isula-build info -H
   General:  
     MemTotal:     7.63 GB  
     MemFree:      757 MB  
     SwapTotal:    8.3 GB  
     SwapFree:     8.25 GB  
     OCI Runtime:  runc  
     DataRoot:     /var/lib/isula-build/  
     RunRoot:      /var/run/isula-build/  
     Builders:     0  
     Goroutines:   12
   Store:  
     Storage Driver:     overlay  
     Backing Filesystem: extfs
   Registry:  
     Search Registries:   
       oepkgs.net
     Insecure Registries:    
       localhost:5000    
       oepkgs.net
   Runtime:
	 MemSys:           68.4 MB
     HeapSys:          63.3 MB
     HeapAlloc:        7.41 MB
     MemHeapInUse:     8.98 MB
     MemHeapIdle:      54.4 MB
     MemHeapReleased:  52.1 MB
```

### login: Logging In to the Remote Image Repository

You can run the login command to log in to the remote image repository. The command is as follows:

```
 isula-build login SERVER [FLAGS]
```

Currently, the following flags are supported:

```
 Flags:  
   -p, --password-stdin    Read password from stdin  
   -u, --username string   Username to access registry
```

Enter the password through stdin. In the following example, the password in creds.txt is transferred to the stdin of isula-build through a pipe for input.

```sh
 $ cat creds.txt | sudo isula-build login -u cooper -p mydockerhub.io
 Login Succeeded
```

Enter the password in interactive mode.

```sh
 $ sudo isula-build login mydockerhub.io -u cooper
 Password:
 Login Succeeded
```

### logout: Logging Out of the Remote Image Repository

You can run the logout command to log out of the remote image repository. The command is as follows:

```
 isula-build logout [SERVER] [FLAGS]
```

Currently, the following flags are supported:

```
 Flags:  
   -a, --all   Logout all registries
```

Example:

```sh
 $ sudo isula-build logout -a  
   Removed authentications
```

### version: Querying the isula-build Version

You can run the version command to view the current version information.

```sh
 $ sudo isula-build version
 Client:
   Version:       0.9.5-6
   Go Version:    go1.15.7
   Git Commit:    b82408f
   Built:         Tue Mar 30 11:08:00 2021
   OS/Arch:       linux/amd64

 Server:
   Version:       0.9.5
   Go Version:    go1.15.5
   Git Commit:    64dbad50
   Built:         Mon Apr 12 20:30:31 2021
   OS/Arch:       linux/amd64
```

### manifest: Manifest List Management

The manifest list contains the image information corresponding to different system architectures. You can use the same manifest (for example, openeuler:latest) in different architectures to obtain the image of the corresponding architecture. The manifest contains the create, annotate, inspect, and push subcommands.
> **Note:**
>
> - manifest is an experiment feature. When using this feature, you need to enable the experiment options on the client and server. For details, see Client Overview and Configuring Services.


#### create: Manifest List Creation

The create subcommand of the manifest command is used to create a manifest list. The command prototype is as follows:

```
isula-build manifest create MANIFEST_LIST MANIFEST [MANIFEST...]
```

You can specify the name of the manifest list and the remote images to be added to the list. If no remote image is specified, an empty manifest list is created.

A command example is as follows:

```sh
$ sudo isula-build manifest create openeuler localhost:5000/openeuler_x86:latest localhost:5000/openeuler_aarch64:latest
```

#### annotate: Manifest List Update

The annotate subcommand of the manifest command is used to update the manifest list. The command prototype is as follows:

```
isula-build manifest annotate MANIFEST_LIST MANIFEST [flags]
```

You can specify the manifest list to be updated and the images in the manifest list, and use flags to specify the options to be updated. This command can also be used to add new images to the manifest list.

The annotate contains the following flags:

- --arch： Applicable architecture of the rewritten image. The value is a string.
- --os： Indicates the applicable system of the image. The value is a string.
- --os-features： Specifies the OS features required by the image. This parameter is a string and rarely used.
- --variant： Variable of the image recorded in the list. The value is a string.

A command example is as follows:

```sh
$ sudo isula-build manifest annotate --os linux --arch arm64 openeuler:latest localhost:5000/openeuler_aarch64:latest
```

#### inspect: Manifest List Query

The inspect subcommand of the manifest command is used to query the manifest list. The command prototype is as follows:

```
isula-build manifest inspect MANIFEST_LIST
```

A command example is as follows:

```sh
$ sudo isula-build manifest inspect openeuler:latest
{
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
    "manifests": [
        {
            "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
            "size": 527,
            "digest": "sha256:bf510723d2cd2d4e3f5ce7e93bf1e52c8fd76831995ac3bd3f90ecc866643aff",
            "platform": {
                "architecture": "amd64",
                "os": "linux"
            }
        },
        {
            "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
            "size": 527,
            "digest": "sha256:f814888b4bb6149bd39ba8375a1932fb15071b4dbffc7f76c7b602b06abbb820",
            "platform": {
                "architecture": "arm64",
                "os": "linux"
            }
        }
    ]
}
```

#### push: Pushes the manifest list to the remote repository.

The manifest subcommand push is used to push the manifest list to the remote repository. The command prototype is as follows:

```
isula-build manifest push MANIFEST_LIST DESTINATION
```

A command example is as follows:

```sh
$ sudo isula-build manifest push openeuler:latest localhost:5000/openeuler:latest
```

## Directly Integrating a Container Engine

isula-build can be integrated with iSulad or Docker to import the built container image to the local storage of the container engine.

### Integration with iSulad

Images that are successfully built can be directly exported to the iSulad.

Example:

```sh
$ sudo isula-build ctr-img build -f Dockerfile -o isulad:busybox:2.0
```

Specify iSulad in the -o parameter to export the built container image to iSulad. You can query the image using isula images.

```sh
$ sudo isula images
isula images
REPOSITORY                     TAG        IMAGE ID             CREATED              SIZE
busybox                        2.0        2d414a5cad6d         2020-08-01 06:41:36  5.577 MB
```

>![](./public_sys-resources/icon-note.gif) **Note:** 
> - It is required that isula-build and iSulad be on the same node.
> - When an image is directly exported to the iSulad, the isula-build client needs to temporarily store the successfully built image as `/var/lib/isula-build/tmp/[buildid]/isula-build-tmp-%v.tar` and then import it to the iSulad. Ensure that the /var/tmp/ directory has sufficient disk space. If the isula-build client process is killed or Ctrl+C is pressed during the export, you need to manually clear the `/var/lib/isula-build/tmp/[buildid]/isula-build-tmp-%v.tar` file.

### Integration with Docker

Images that are successfully built can be directly exported to the Docker daemon.

Example:

```sh
$ sudo isula-build ctr-img build -f Dockerfile -o docker-daemon:busybox:2.0
```

Specify docker-daemon in the -o parameter to export the built container image to Docker. You can run the docker images command to query the image.

```sh
$ sudo docker images
REPOSITORY                                          TAG                 IMAGE ID            CREATED             SIZE
busybox                                             2.0                 2d414a5cad6d        2 months ago        5.22MB
```

>![](./public_sys-resources/icon-note.gif) **Note:**
>
> - The isula-build and Docker must be on the same node.

## \<span id="Appendix"\>Appendix


### Command Line Parameters

**Table 1** Parameters in the ctr-img build command

| **Command** | **Parameter** | **Description** |
| ------------- | -------------- | ------------------------------------------------------------ |
| ctr-img build | --build-arg | String list, which contains variables required during the build. |
| | --build-static | Key value, which is used to build binary equivalence. Currently, the following key values are included: - build-time: string, which indicates that a fixed timestamp is used to build a container image. The timestamp format is YYYY-MM-DD HH-MM-SS. |
| | -f, --filename | String, which indicates the path of the Dockerfiles. If this parameter is not specified, the current path is used. |
| | --iidfile | String, which indicates the ID of the image output to a local file. |
| | -o, --output | String, which indicates the image export mode and path.|
| | --proxy | Boolean, which inherits the proxy environment variable on the host. The default value is true. |
| | --tag | String, which indicates the tag value of the image that is successfully built. |
| | --cap-add | String list, which contains permissions required by the RUN command during the build process.|

**Table 2** Parameters in the ctr-img load command

| **Command** | **Parameter** | **Description** |
| ------------ | ----------- | --------------------------------- |
| ctr-img load | -i, --input | String, Path of the local .tar package to be imported|

**Table 3** Parameters in the ctr-img rm command

| **Command** | **Parameter** | **Description** |
| ---------- | ----------- | --------------------------------------------- |
| ctr-img rm | -a, --all | Boolean, which is used to delete all local persistent images. |
| | -p, --prune | Boolean, which is used to delete all images that are stored persistently on the local host and do not have tags. |

**Table 4** Parameters in the ctr-img save command

| **Command** | **Parameter** | **Description** |
| ------------ | ------------ | ---------------------------------- |
| ctr-img save | -o, --output | String, which indicates the local path for storing the exported images.|

**Table 5** Parameters in the login command

| **Command** | **Parameter** | **Description** |
| -------- | -------------------- | ------------------------------------------------------- |
| login | -p, --password-stdin | Boolean, which indicates whether to read the password through stdin. or enter the password in interactive mode. |
| | -u, --username | String, which indicates the username for logging in to the image repository.|

**Table 6** Parameters in the logout command

| **Command** | **Parameter** | **Description** |
| -------- | --------- | ------------------------------------ |
| logout | -a, --all | Boolean, which indicates whether to log out of all logged-in image repositories. |

### Communication Matrix

The isula-build component processes communicate with each other through the Unix socket file. No port is used for communication.

### File and Permission

- All isula-build operations must be performed by the root user. To perform operations as a non-privileged user, you need to configure the --group option.

- The following table lists the file permissions involved in the running of isula-build.

| **File Path** | **File/Folder Permission** | **Description** |
| ------------------------------------------- | ------------------- | ------------------------------------------------------------ |
| /usr/bin/isula-build                        | 551                 | Binary file of the command line tool.                                       |
| /usr/bin/isula-builder                      | 550                 | Binary file of the isula-builder process on the server.                          |
| /usr/lib/systemd/system/isula-build.service | 640                 | systemd configuration file, which is used to manage the isula-build service.                   |
| /usr/isula-build                            | 650                 | Root directory of the isula-builder configuration file. |
| /etc/isula-build/configuration.toml         | 600                 | General isula-builder configuration file, which sets the isula-builder log level, persistency directory, runtime directory, and OCI runtime. |
| /etc/isula-build/policy.json                | 600                 | Syntax file of the signature verification policy file.                                 |
| /etc/isula-build/registries.toml            | 600                 | Configuration file of each image repository, including the available image repository list and image repository blacklist. |
| /etc/isula-build/storage.toml               | 600                 | Configuration file for local persistent storage, including the configuration of the used storage driver.       |
| /etc/isula-build/isula-build.pub            | 444                 | Asymmetric encryption public key file. |
| /var/run/isula_build.sock                   | 660                 | Local socket of isula-builder.                            |
| /var/lib/isula-build                        | 700                 | Local persistency directory.                                             |
| /var/run/isula-build                        | 700                 | Local runtime directory.                                             |
| /var/lib/isula-build/tmp/[buildid]/isula-build-tmp-*.tar              | 644                 | Local directory for temporarily storing the images when they are exported to the iSulad.                           |

   
