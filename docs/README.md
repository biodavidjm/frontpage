# Strategy for containerization
![frontend_docker](https://cloud.githubusercontent.com/assets/48740/7523505/d090d86a-f4c1-11e4-9ca0-e76fdf5bab99.png)

The strategy is based on the principle of
[radial](https://github.com/radial/docs) topology. The concepts from radial
topology allows a  consistent management and separation of application code and
data(config/log/data etc) logics with docker containers. The data part is
handled by [docker data
containers](http://docs.docker.com/userguide/dockervolumes/) that uses a
consistent file system hierarchy to manage various kinds of data. A data
container(called hub) aggregates all the various data containers. The
application containers will use the hub container to access and share data with
other containers in the application stack.

## Application containers
* Build: It will be a container that will check out the source code from github
  and run the appropiate build tool(nodejs based) and write the output to
  `/www` folder.
* Server: It will run a [golang](http://golang.org) based server that runs on port
  `9596` and will serve files out of `/www` folder.

## Data containers
* `/log` : To hosts all the logs from `build` and the `server` containers.
* `/config` : To handle any configuration files. However, specifics yet to be decided.
* `/www` : To contain the output of build container. The content of this folder will also be served by the `server` container.
* Hub: The data container that will have access to the above three containers.

# Images
At this point, it seems no standalone and reusable images are necessary, so all
could be build during the creation of application stack. Data containers not
needed to be build under dictybase namespace. All source `Dockerfile`s should
be kept under their respective folders.  The folder names should be prefixed
with the word __wheel__ to signify the radial based topology. Check out the
[JBrowse docker repository](https://github.com/dictybase-docker/wheel-jbrowse/)
for inspiration.

* __dictybase/frontpage-builder__: Based on the official
  [node](https://registry.hub.docker.com/_/node/) image. Then add all the
  [npm](http://npmjs.org) build tools that are needed to build the
  [frontpage](https://github.com/dictyBase/frontpage-dictybase).

* __dictybase/frontpage-server__: Based on the offical
  [golang](https://registry.hub.docker.com/_/golang) image with the onbuild
  tag. Then add the server code. It will open port `9596` and will serve files
  from `/www` by default. At this point start with [server side
  code](https://github.com/dictybase-docker/jbrowse/blob/master/1.11.6/command.go)
  from JBrowse docker repository and modify accordingly.

* __Data containers__: All of them will be based on [alpine
  linux](https://registry.hub.docker.com/u/gliderlabs/alpine/) image.
