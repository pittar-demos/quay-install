# Quay Install

## Installing *Multicloud Object Gateway* (Noobaa)

Quay uses object storage.  There are a number of supported S3 providers you can use, but for this demo I will install the *Multicloud Object Gateway* (Noobaa) component of *OpenShift Data Foundation*.

If you already have ODF (or just MCG/Noobaa) installed, you can skip this step.

```
oc apply -k https://github.com/redhat-cop/gitops-catalog/openshift-data-foundation-operator/operator/overlays/stable-4.11
```

Once the operator has been installed, create a new `StorageSystem`

```
oc apply -f manifests/mcg
```

It will take a few moments for Noobaa to fully deploy and the new `ObjectBucketClass` to become available.

## Install Quay Operator

```
oc apply -k https://github.com/redhat-cop/gitops-catalog/quay-registry-operator/operator/overlays/stable-3.8
```

Once the Operator has finished installing, you can create a `namespace`, init config `secret`, and a `quayregistry`.

```
oc apply -k manifests/quay
```

This will create a **quay** namespace, create the init secret, and then the quay instance.

Quay takes a while to fully deploy.  If you wants the pods or topology, you will see pods fail a number of times before they finally become available.  This is normal.

The contents of the init secret (if you base64 decode the value) are:

```
SUPER_USERS:
- quayadmin
FEATURE_USER_CREATION: true
FEATURE_PROXY_CACHE: true
```

At the Quay login screen, if you click "Create Account" and use the username `quayadmin`, this new user will become a "Super User".

The other features that are enabled with this config:

* `FEATURE_USER_CREATION`: Allow anyone to create an account.
* `FEATURE_PROXY_CACHE`: Proxy upstream image registries (e.g. Docker Hub, Quay.io, etc...)

