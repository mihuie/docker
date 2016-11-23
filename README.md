# Docker

This project demonstrations some basic principles of [Docker](https://www.docker.com/) by creating a docker image that will allow development-to-deployment of an angular-spring generated app. This environment will contain java, npm, maven and grunt. 

You will need to have docker [installed](https://www.docker.com/products/overview) on your computer.

##Create a docker image from dockerfile

All docker images start with a base image, preferably one you trust. In this project the offical [centOS](https://hub.docker.com/_/centos/) image will be used. To download the centOS image from terminal run `docker pull centos`. This is how images are download from docker's online repository of images ([hub.docker.com](https://hub.docker.com)).

### Adding JAVA to image

In any from any text editor add the following:
```bash
FROM centos

ENV JAVA_VERSION 8u111
ENV BUILD_VERSION b14

# Install wget
RUN yum -y install wget

# Downloading Java
RUN wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/$JAVA_VERSION-$BUILD_VERSION/jdk-$JAVA_VERSION-linux-x64.rpm" -O /tmp/jdk-8-linux-x64.rpm

# Install java from extracted package
RUN yum -y install /tmp/jdk-8-linux-x64.rpm 

RUN alternatives --install /usr/bin/java jar /usr/java/latest/bin/java 200000
RUN alternatives --install /usr/bin/javaws javaws /usr/java/latest/bin/javaws 200000
RUN alternatives --install /usr/bin/javac javac /usr/java/latest/bin/javac 200000

RUN rm -f /tmp/jdk-8-linux-x64.rpm && yum clean all

# Create envs
ENV JAVA_HOME /usr/java/latest
ENV PATH $PATH:$JAVA_HOME/bin

# Set default command for container if none supplied at build
ENTRYPOINT ["/bin/bash", "-c", "java -version"]

```

Save the above as `Dockerfile`

### Building first image

From terminal, run the following:
```bash
$ docker build -t demo/java .
```

This will create an image named demo/java. Note the `-t` was used to specify the name of image and the `.` detects the Dockerfile and any content in folder. To confirm that image is on your system, in terminal execute `docker images` and a list of installed images will be displayed.

### Container

We can test our new image by creating a container. Create a project folder and then create a file called Main.java inside this folder with following content:
```java
public class Main
{
     public static void main(String[] args) {
        System.out.println("Hello, World");
    }
}
```


Now execute the following commands from the current project directory.
* To compile your Main.java file. 
```bash 
$ docker run --rm -v $PWD:/app -w /app demo/java javac Main.java
```

* To run your compiled Main.class file. 
```bash 
$ docker run --rm -v $PWD:/app -w /app demo/java java Main
```

`Hello, World output` should be displayed.

### Volumes

The last commands we ran made use of volumes. The `-v $PWD:/app'` in the commmand linked the working directory on your computer to a folder in container named `app`.
You can find more on volumes [here](https://docs.docker.com/engine/tutorials/dockervolumes/)


### Ports

When creating a container, Docker allows you to map a port on your computer or host enviroment to a port in the container. Example, `-p 9000:8080` will allow you to reach port 9000 from browser to reach port 8080 in container.

## Run app in container from the completed image

The completed dockerfile for this project is located [here](/demo). Docker allows you to build a image from a docker file hosted in a git repository. Complete the following to build complete image for the app container.

```bash
$ docker build https://github.com/mihuie/docker.git#:demo -t demo/app

```

Once the image `demo/app` has been built successfully, from working directory clone the sample project with command:

```bash
$ git clone https://github.com/mihuie/GitTraining.git
```

For more information on this project click [here](https://github.com/mihuie/GitTraining).
### Deploy app to container

From terminal run:
```bash
docker run -it -v $PWD/GitTraining:/app -p 8080:8080 demo/app /bin/bash -c "npm install && bower install; grunt; mvn tomcat7:run"
```

Here we have used `-it` which allows for an interactive terminal; `-v $PWD/GitTraining:/app` links the GitTraining folder, created from clone command, with app directory in container; `-p 8080:8080` ties port 8080 with port 8080 in container and `/bin/bash -c ` allows use to send the bash command `"npm install && bower install; grunt; mvn tomcat7:run"` to containter. Note that if we did not supply a command, the container would have started with `"tomcat7:run"` command. 


Now we can open [http://localhost:8080/GitTraining/](http://localhost:8080/GitTraining/) and see our app. 

### Stopping containers

The `docker rm <container id>` command will stop and remove specified container or `docker rm $(docker ps -a -q -f status=exited)` will remove all.