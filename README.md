# secure-custom-dockerregistry-poc
Configure Custom Docker registry with Nginx SSL configuration. Expose registry over https instead of http.


#### Steps to set up custom secure docker registry.

* Set up docker registry username and password.
```
docker run --entrypoint htpasswd registry:2 -Bbn admin admin > nginx/certs/nginx.htpasswd
```

* Let's run the registry
```
docker-compose up -d
```

* Let's login to docker registry
```
$ docker login localhost
Username: admin
Password: 
Login Succeeded
```

* Pull hello-world docker images
```
$docker pull hello-world
Using default tag: latest
latest: Pulling from library/hello-world
1b930d010525: Pull complete 
Digest: sha256:f9dfddf63636d84ef479d645ab5885156ae030f611a56f3a7ac7f2fdd86d7e4e
Status: Downloaded newer image for hello-world:latest
docker.io/library/hello-world:latest

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               alpine              377c0837328f        2 weeks ago         19.7MB
registry            2                   708bc6af7e5e        8 weeks ago         25.8MB
hello-world         latest              fce289e99eb9        14 months ago       1.84kB
```

* tag hello-world image and push to local docker registry.
```
$docker tag hello-world localhost/hello-world
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
nginx                   alpine              377c0837328f        2 weeks ago         19.7MB
registry                2                   708bc6af7e5e        8 weeks ago         25.8MB
hello-world             latest              fce289e99eb9        14 months ago       1.84kB
localhost/hello-world   latest              fce289e99eb9        14 months ago       1.84kB
```

* Push to localhost registry
```
$ docker push localhost/hello-world
The push refers to repository [localhost/hello-world]
af0b15c8625b: Pushed 
latest: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
```

* Remove both hello-world images and pull back from localhost registry.
```
$docker rmi -f fce289e99eb9
Untagged: hello-world:latest
Untagged: hello-world@sha256:f9dfddf63636d84ef479d645ab5885156ae030f611a56f3a7ac7f2fdd86d7e4e
Untagged: localhost/hello-world:latest
Untagged: localhost/hello-world@sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
Deleted: sha256:fce289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e
Deleted: sha256:af0b15c8625bb1938f1d7b17081031f649fd14e6b233688eea3c5483994a66a3

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               alpine              377c0837328f        2 weeks ago         19.7MB
registry            2                   708bc6af7e5e        8 weeks ago         25.8MB

```

* Pull hello-world back from localhost registry.
```
$ docker pull localhost/hello-world
Using default tag: latest
latest: Pulling from hello-world
1b930d010525: Pull complete 
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
Status: Downloaded newer image for localhost/hello-world:latest
localhost/hello-world:latest

$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
nginx                   alpine              377c0837328f        2 weeks ago         19.7MB
registry                2                   708bc6af7e5e        8 weeks ago         25.8MB
localhost/hello-world   latest              fce289e99eb9        14 months ago       1.84kB
```