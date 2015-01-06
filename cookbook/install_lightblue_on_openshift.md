# Install lightblue on OpenShift

## Create an OpenShift Account
If you don't have an OpenShift account, don't worry.  It's FREE!

Check out this [Getting Started](https://developers.openshift.com/en/getting-started-overview.html) guide provided by [OpenShift](https://www.openshift.com/).


## Install lightblue 1.1+

With the lightblue-openshift-cart (a downloadable cart) it's really simple to install lightblue.  For now, I'm documenting the way to do it with the ```rhc``` command.

```bash
rhc app create https://raw.githubusercontent.com//lightblue-platform/openshift-lightblue-cart/master/metadata/manifest.yml -a <YOUR APP NAME>
```

That's it.  This deploys JBoss EAP and MongoDB then installs and configures lightblue.  After this command completes you have a working instance of lightblue!  Check out the README.md at the root of your gear's source for some quick things to do to try out lightblue and links to documentation, forums, and source.


# Install lightblue SNAPSHOT
If you want a non-release version (aka the latest) of lightblue simply add an environment variable `SNAPSHOT` with a value of `true` to the app creation command:

```bash
rhc app create -e SNAPSHOT=true https://raw.githubusercontent.com//lightblue-platform/openshift-lightblue-cart/master/metadata/manifest.yml -a <YOUR APP NAME>
```

