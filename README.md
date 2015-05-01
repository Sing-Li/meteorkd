## MeteorKD - Lean and Flexible Kubernetes / Docker Container for Meteor Apps

There are many hobbiest docker containers out there for running Meteor Apps. Some containers attempt to be everything to everyone, and have a uber-rich (and often complex) deployment time configurable features set.

meteorkd takes a different goal-focused approach to Meteor container crafting.  A very pragmatic one.

Our goal is to tune our container for optimal image size, Kubernetes and Docker container-linking flexibility, plus build and runtime performance.

The meteorkd container is not for you if you are:

1. experimenting with writing Meteor apps
2. looking for a one-container solution to all your Meteor deployment needs

You will find other containers on the hub that may satisfy your needs.

### Kubernetes deployment, Docker container linking, and Mongo replica sets
If you are using K8s to distribute tens, hundreds, or thousands of instances on bare-metal farms - you may find this container useful.  (Further optimizations are always possible, but need to be considered on a case-by-case basis.)

Even if you're not using k8s, but working with a custom cloud solution via docker container linking, you might find this container useful.

We know support for Mongo oplog tailing on arbitrary topology replica sets is important for many target users - and this container is built to facilitate (meaning: not hinder) it.  It works well with the official mongo image on docker hub - via container linking OR  k8s services.

### Building a Docker image for your app with meteorkd
Your app will be converted into a Docker image. Then you can distribute that image on k8s.  

You use `singli/meteorkd` as your base image. That's the only thing you've to do. Here's how to do it.

Add following `Dockerfile` into the root of your app:

~~~shell
FROM singli/meteorkd
MAINTAINER Your Name
ENV ROOT_URL  http://yourapp.com
~~~

In your app directory, add a `set_env.sh` file and set all the environment varaibles that you need for k8s services references, or docker container linking.   `set_env.sh` will be parsed at runtime - just before startup of the meteor app.

Then you can build the docker image with:

~~~shell
docker build -t yourname/app .
~~~

Then you can run your meteor image with

~~~shell
docker run -d \
    -p 8099:80 \
    yourname/app 
~~~

Then you can access your app from the port 8099 of the host system.

### Result of goal-focused container tuning
Using one of the popular containers, we built our sample Meteor app image. The image size was:
~~~shell
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
meteorsales         latest              58766aba8974        2 days ago          816.2 MB
~~~
The same app with meteorkd:
~~~shell
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
meteorkd            latest              f40487b9e733        3 minutes ago       121.6 MB
~~~

Both apps were tested on a 50 instances over 3 bare-iron-minions k8s cluster and performed equally.
