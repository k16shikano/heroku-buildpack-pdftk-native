# Heroku Buildpack for pdftk

[pdftk](https://gitlab.com/pdftk-java/pdftk) binary built with [GraalVM](https://www.graalvm.org/) for use in Heroku.

```sh
$ heroku buildpacks:set https://github.com/k16shikano/heroku-buildpack-pdftk-native.git
```

## Development

Modify `docker/Dockerfile` so as to use the latest Heroku stack. Then build and run the docker image to get pdftk binary. 

```sh
$ sudo docker build -t pdftk-graalvm ./docker
$ sudo docker run -it --user root -v $(pwd)/binaries:/app/binary pdftk-graalvm
```
