# Spring XD base image
This serves as the basis of the other Spring XD images. End users need not interact with
this docker image directly.

This image 

 * creates a springxd user and group
 * pulls a Spring XD release
 * unzips it, as well as the ZooKeeper release embedded inside
 * symlinks the install directory to `/opt/spring-xd` (so that a version-less path can be used onwards)