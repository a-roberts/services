This folder contains an example script that will compile the services binary and name it in such a way that it can be used as a plugin to `oc` - using `oc services promote ...` for example.

A Dockerfile is also provided that will pull in `oc` and copy the built plugin binary into that image. You could then push that image to your own testing place on DOckerhub for use later - be it as a standalone container or as part of a Tekton Task.