#node-app

##Purpose

This image provides a skeleton from which custom production images can quickly be build.

##Usage

###Manual Dockerfile Creation From base image and dockerfile-template

Take the dockerfile-template file and customize it to your needs. Once that is done, use the modified dockerfile to build your production image.

####Customizations

- UID Environment Variable: Determines the user and group ID that the node app will run under in the container
- app content: This should contain your node application (location of package.json file and app-specific entrypoint and dependencies)
- shared modules content: 

This should contain any recurring private modules that tend to be used accross apps (or accross containers in the same application if you split it into several containers). 

Those modules can be expressed as a dependency elsewhere by adding their name to the 'localDependencies' entry in the package.json file of the dependent (which is an array of shared module names, as named in their respectif package.json files, see the example).

Afterwards, they can be required by the dependend by their module name (as defined in their package.json file).

####Tag Convention

The first number appearing in tag names refers to the version of node the image uses. 

The second number appearing in tag names after the 'v' refers to the revision of the image (which increments as improvements are made).

####Examples

The 'base-image-example' directory contain an example of a manually generated dockerfile for a project with shared dependencies.

The 'base-image-example' directory contain an example for a basic 'hello-world' hapi app.

###Automated Dockerfile Creation Using the dockerfile-builder image and dockerfile-builder-template docker-compose file

Take the dockerfile-builder-template.yml docker-compose file and customize is for your needs (any parts between <<...>> should be customised).

####Customizations

* container-name: Name of the container that builds the dockerfile (mostly useful to avoid name clashed with already running containers)
* environment
    * UID: Running user ID of the image that will be created from the generated dockerfile
    * OUTPUT_UID: User ID of the outputed dockerfile and other dependent files
    * OUTPUT_IMAGE: Full image name of the image that will be created from the generated dockerfile
    * IGNORE: Regex pattern identifiying files that the dockerfile should not copy (for example, my text editor always creates backup files ending with ~ that I don't want included in a production image)
* volume mapping
    * App directory: Change the LHS of the volume mapping to map to the directory where your app is contained
    * Shared directory: Change the LHS of the volume mapping to map to the directory where your local shared modules are located
    * Ouput directory: Change the LHS of the volume mapping to map to the directory where you would like your dockerfile, build script and other dependencies generated

####Output

The dockerfile should appear in the specified output directory along with a build.sh bash script to build it.

####Examples

The 'dockerfile-builder-image-example' directory contain an example, adapted to have its dockerfile automatically generated.

###Automated Image Creation Using the image-builder image and image-builder-template docker-compose file

Take the image-builder-template.yml docker-compose file and customize is for your needs (any parts between <<...>> should be customised).

####Build Note

Because the docker client runs in a container and the docker daemon that actually builds the image runs on the local machine outside the container the client runs on, I had to do a work-around to make the app files available to the daemon.

Basically, the client runs an http file server and passes in the dockerfile wget commands to the daemon to fetch the files from the server. This means that the build container must map a port to the local machine.

Furthermore, it implies an additional delay at build time, but it also makes the resulting image more efficient (all files are imported in a single RUN statement resulting in a single layer).

####Customizations

* container-name: Name of the container that builds the image (mostly useful to avoid name clashed with already running containers)
* environment
    * UID: Running user ID of the image
    * OUTPUT_IMAGE: Full image name of the resulting image
    * IGNORE: Regex pattern identifiying files that the dockerfile should not copy (for example, my text editor always creates backup files ending with ~ that I don't want included in a production image)
    * EXTERNAL_PORT: Port on your local machine that your container maps to. It's extremely important that this value corresponds to the mapping that you give in the "ports" section.
    * DOCKER_LOCALHOST: Address if your localhost machine on the docker networking interface (in Linux, run ifconfig to find it, you should have a 'docker0' interface by default with an 'inet addr' entry)
    * CACHE: Whether to use cached intermediate images or not when building anew (should be yes or no) 
* volume mapping
    * App directory: Change the LHS of the volume mapping to map to the directory where your app is contained
    * Shared directory: Change the LHS of the volume mapping to map to the directory where your local shared modules are located
* ports: Change the LHS entry to a free port on your host. Don't forget to give the same value to the EXTERNAL_PORT environment variable.
    
####Output

The corresponding image should have been built by the docker-daemon on your machine. 

####Examples

The 'image-builder-image-example' directory contain an example, adapted to have its image automatically generated (port 8080 will need to be free on your machine for the example to work).
