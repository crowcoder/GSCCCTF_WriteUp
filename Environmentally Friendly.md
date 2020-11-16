# Environmentally Friendly

The clue... is just a link to a Docker Hub image.

So, what do you do with this? Well you pull the image and run it and see what happens.  

If you don't have a Docker client available you can use [the docker playground](https://labs.play-with-docker.com/) right from the browser.

Here is an example of running this challenge from the online playground. You start by doing a `docker pull`. Next you would try to run it - `docker run ~image name~`. That tells you to set "Help" to "true". Well, the challenge title gives you a hint - "environmentally". So, set an environment variable in the command - `docker run --env HELP=true ~image name~`. That goes on to tell you to set the PASSWORD. So, you make a wild guess - `docker run --env PASSWORD=guess ~image name~`. You are then told that the password changes every minute. This is a hint that the password is the value of the minute in what time it is - (assuming it is now xx:58 o'clock) `docker run --env PASSWORD=58 ~image name~`. And that finally reveals the flag.

```
[node1] (local) root@192.168.0.13 ~
$ docker pull contrivedex/environmentally-friendly
Using default tag: latest
latest: Pulling from contrivedex/environmentally-friendly
bf5952930446: Pull complete 
95f9f5484a21: Pull complete 
ebc43d54b0d9: Pull complete 
eb8b3fc30ae1: Pull complete 
83aca4d90069: Pull complete 
f604034abe8a: Pull complete 
Digest: sha256:ef481f9d92542f16f7c048532fa86d59f37c8e1de5d2527838015ad384fd8f76
Status: Downloaded newer image for contrivedex/environmentally-friendly:latest
docker.io/contrivedex/environmentally-friendly:latest

[node1] (local) root@192.168.0.13 ~
$ docker image ls
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
contrivedex/environmentally-friendly   latest              2d110a335dce        2 months ago        190MB

[node1] (local) root@192.168.0.13 ~
$ docker run contrivedex/environmentally-friendly
Access denied. You might want to set HELP to true

[node1] (local) root@192.168.0.13 ~
$ docker run --env HELP="true" contrivedex/environmentally-friendly
To capture this flag you must set the PASSWORD.

[node1] (local) root@192.168.0.13 ~
$ docker run --env PASSWORD="password" contrivedex/environmentally-friendly
Access denied Unfortunately the password changes every minute.

[node1] (local) root@192.168.0.13 ~
$ docker run --env PASSWORD="58" contrivedex/environmentally-friendly
stone-feeder-option
```
