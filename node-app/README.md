#node-app

##Purpose

This image provides a skeleton from which custom production images can quickly be build.

##Usage

Take the dockerfile-template file and customize it to your needs. Once that is done, use the modified dockerfile to build your production image.

##Customizations

- UID Environment Variable: Determines the user and group ID that the node app will run under in the container
- app content: This should contain your node application (location of package.json file and app-specific entrypoint and dependencies)
- shared modules content: This should contain any recurring private modules that tend to be used accross apps (or accross containers in the same application if you split it into several containers). They can be required in the code with ```require('shared_modules/<some shared module>')```

##Tag Convention

The first number appearing in tag names refers to the version of node the image uses. 

The second number appearing in tag names after the 'v' refers to the revision of the image (which increments as improvements are made).

 
