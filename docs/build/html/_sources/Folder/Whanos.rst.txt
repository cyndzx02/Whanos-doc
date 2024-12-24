Whanos
======

This project encompasses three big parts such as:

1. Docker Images installations
2. Jenkins
3. Kubernetes deployment

To make it happen, 

A.  IMAGES: 
+++++++++++

The Whanos images are the images on which all application running on the Whanos infrastruc-
ture are based on.
They must have the following characteristics:

a. themselves based on an official image;
b. use the Bourne-Again shell for all image build instructions;
c. work in an app directory at the root of the image;
d. (if applicable) copy the dependency file(s) and install the dependencies;
e. copy (re)sources;
f. (if applicable) compile the application and get rid of the sources and other unnecessary files, in order to only leave what is needed for execution;
g. sets the execution command based on the one defined for the language (in the previous section).

Also We were suppost to take in the following images and make their installations:

1.  C images
2.  Java images
3.  JavaScript images
4.  Python images
5.  Befunge images

B. Jenkins:
+++++++++++

In order to make the automation successfull, we were asked to use jenkins.

C. Kubernetes:
++++++++++++++
For the deployment, Ansible was given to make it fully functional and for it to be able to work online as well.