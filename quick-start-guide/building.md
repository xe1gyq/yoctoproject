# Building

Building a minimal image for the minnowboard

```bash
building.1> cd $HOME/intro/poky-krogoth-15.0.0
building.2> source oe-init-build-env $HOME/build
building.3> cat $HOME/intro/conf/bblayers.conf >> conf/bblayers.conf 
building.4> # edit conf/bblayers.conf and set the correct pathname
building.5> cat $HOME/intro/conf/local.conf >> conf/local.conf
building.6> # Inspect the conf/local.conf, this is the file where user can configure generic things
building.7> bitbake core-image-minimal # this may take some time
building.8> # you can explore what bitbake is doing: ps, pstree, top, htop, etc.
```

Now you got an image under tmp/deploy!

