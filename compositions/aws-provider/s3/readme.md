# Build and release a config package

Crossplane packages are opinionated OCI images that contain a stream of YAML that can be parsed by the Crossplane package manager. See [crossplane.io > concepts](https://crossplane.io/docs/v1.9/concepts/packages.html) and [crossplane.io > xpkg specification](https://crossplane.io/docs/v1.9/reference/xpkg.html) for more information.

## Prerequisites
* A cluster with Crossplane installed
* [Install Crossplane CLI](https://crossplane.io/docs/v1.9/getting-started/install-configure.html#install-crossplane-cli) 

```bash
# point kubectl to a cluster with crossplane installed
# verify that crossplane is running
$ helm list -n crossplane-system
$ kubectl get pods -n crossplane-system
```

## Build a config package
```bash
# change to this directory

# build the config package
$ kubectl crossplane build configuration

# examine the created package
$ file crossplane-aws-blueprints-s3-*.xpkg
$ tar -xvf crossplane-aws-blueprints-s3-*.xpkg
$ tar -xvf <some-sha>.tar.gz
$ cat package.yaml
```

## Push to Docker hub
```bash
export REPO=<some docker repo>

$ kubectl crossplane push configuration $REPO/crossplane-aws-blueprints-s3:v0.0.7
$ open https://hub.docker.com/r/$REPO/crossplane-aws-blueprints-s3
```

## Use that configuration
```bash

# install configuration package package:
$ kubectl crossplane install configuration $REPO/crossplane-aws-blueprints-s3:v0.0.7

# verify that a configuration is installed and healthy
$ kubectl get configurations
kubectl get configurations
NAME                                   INSTALLED   HEALTHY   PACKAGE                                       AGE
xyz-crossplane-aws-blueprints-s3   True        True      xyz/crossplane-aws-blueprints-s3:v0.0.6   23s
$ kubectl get configurationrevision
kubectl get configurationrevision
NAME                                                HEALTHY   REVISION   IMAGE                                         STATE    DEP-FOUND   DEP-INSTALLED   AGE
xyz-crossplane-aws-blueprints-s3-62f2d60647f8   True      1          xyz/crossplane-aws-blueprints-s3:v0.0.6   Active   1           1               99s

# verify that the aws provider listed as a dependency is created 
$ kubectl get providers
NAME                      INSTALLED   HEALTHY   PACKAGE                           AGE
crossplane-provider-aws   True        True      crossplane/provider-aws:v0.32.0   19m

# verify that the crossplane XRDs and compositions are installed
$ kubectl get xrds
NAME                               ESTABLISHED   OFFERED   AGE
xobjectstorages.awsblueprints.io   True          True      2m45s

$ kubectl get compositions
NAME                                     AGE
s3bucket-multi-tenant.awsblueprints.io   3m6s
s3bucket.awsblueprints.io                3m6s

# for any troubleshooting describe the configuration and configurationrevision
$ kubectl describe configuration
$ kubectl describe configurationrevision

# cleanup
$ kubectl delete configurations $REPO-crossplane-aws-blueprints-s3
```



