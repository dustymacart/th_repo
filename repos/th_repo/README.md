This is a lab environment for Tuttle House. Every machine will start with th to show the location.
This is meant to learn how to use ansible and other technologies to automate tasks such as
patching, service restarts, iis restarts, apache restarts, opening ports, firewall rules 
and anything you can think of. Pull requests will need to be approved by dustymacart.
Please message on google im using arthursdustin@gmail.com.

####### Getting Started ###########
Clone this repository down to your local system.
Any ansible work that you need to do will be done through a docker container.
Install docker on your machine and then run the following commands:
## Build out the container from the Dockerfile
docker build --no-cache --progress=plain -t my-rhel9-dev .
## Launch the Container
MSYS_NO_PATHCONV=1 docker run --rm -it \
  -v "$(pwd):/workspace" \
  my-rhel9-dev