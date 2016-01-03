## docker node hello world

Simple example of building a docker image which contains a node.js project. To build the image run:
```
docker build -t my-node-project .
```

* `-t`: tags the image with "my-node-project"
* `.`: current directory, command must be run from project root where Dockerfile is defined

If the image was built successfully you'll see "my-node-project" when you run:
```
docker images
```

You can then start a docker container from the image by running:
```
docker run -d -p 8080:8080 --name running-node-project my-node-project
```

* `-d`: runs container as daemon process
* `-p [host port : container port]`: publishes container's port to host
* `--name`: names the started container "running-node-project"

If the container was started successfully you'll see "running-node-project" when you run:
```
docker ps
```

You can also visit the following URL in a browser to see that the container is running:
```
http://localhost:8080
```

The container can be stopped by running the command:
```
docker stop running-node-project
```

If you'd like to work on your project and see updates in real-time without having to repeatedly rebuild the docker image then you can start the container like so:
```
docker run -d -p 8080:8080 -v $(pwd):/app running-node-project my-node-project
```

* `-v`: mounts a "volume" from the host within the container

Now that the current directory is mounted into the container's app directory the container will be able to see all live changes. In case you need to restart node.js itself you can do so by restarting the container:
```
docker restart running-node-project
```

You can declare and install 3rd party dependencies via `npm` & `package.json` and make the project as complicated as you like, all of the above commands will work just fine as long as your project can be started by the command `node app.js` but even that can be changed by editing the `Dockerfile`. Happy coding.

## license

MIT
