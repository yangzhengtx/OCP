# Image vs ImageStream vs ImageContentSourcePolicy

### Image

```
$ oc get images
NAME                                                                      IMAGE REFERENCE
sha256:0155e28b6b10d6fba7144df9ce33105acbab32766836baacc67c76b900a58772   registry.redhat.io/redhat-sso-7/sso70-openshift@sha256:0155e28b6b10d6fba7144df9ce33105acbab32766836baacc67c76b900a58772
sha256:01920073bbd480bd34e2d8e17dced64d342257fa9a263d1843edf1cc45a50a7c   registry.redhat.io/jboss-datagrid-7/datagrid73-openshift@sha256:01920073bbd480bd34e2d8e17dced64d342257fa9a263d1843edf1cc45a50a7c
sha256:02b7619ced8942c4c017fad2f14e533807d12abdaafc3cff4dc3ca1a9089942e   registry.redhat.io/ubi8/nodejs-16-minimal@sha256:02b7619ced8942c4c017fad2f14e533807d12abdaafc3cff4dc3ca1a9089942e
```


### ImageStream
An ImageStream is a set of tags which point to Docker images; It's like a shortcut or reference as a pointer to a set of images.

#### Why use ImageStream?
##### A single place to reference a set of related images
- An image stream is a single pointer to a set of related images.
- One image stream can contain many different tags (latest, develop, 1.0, etc), each of which points to an image in a registry.
- Whenever you want to deploy an application in a project, instead of having to hardcode the registry URL and tag, you can just refer to the image stream tag.
- If the source image changes location in future, you just need to update the image stream definition, instead of updating all your deployments individually!

This is how the Red Hat images work in OpenShift. The Red Hat images are installed as image streams in the openshift project. You can reference them from any other project in the cluster.

##### When an image changes, trigger a new build or deployment
Image streams can also be used as triggers. This means that you can set an image stream as a source for a Build or a Deployment. When the image changes, this can trigger a new Build, or trigger a redeployment of your app.

##### Access control for images
- You can lock away your images in a private registry and control access to them through image streams.
- Because image streams are like other objects in OpenShift, they can be restricted, if you like. So you can restrict images to only be used by specific service accounts.

#### How do you create an image stream?
- Create an image stream using the oc import-image command. Give the image stream a name and then the image you want to import. 
- Then the image will be pulled into OpenShift’s internal registry.
```
oc import-image test-imagestream:1.0 --from=quay.io/apache:2.4 --confirm

apiVersion: image.openshift.io/v1
kind: ImageStream
metadata:
  name: test-imagestream
spec:
  lookupPolicy:
    local: false
  tags:
  - name: "1.0"
    from:
      kind: DockerImage
      name: quay.io/apache:2.4
    referencePolicy:
      type: Source
```

ImageStream to Image in Openshift

```
$ oc get imagestream must-gather -n openshift -o yaml
apiVersion: image.openshift.io/v1
kind: ImageStream
metadata:
  annotations:
    include.release.openshift.io/ibm-cloud-managed: "true"
    include.release.openshift.io/self-managed-high-availability: "true"
    include.release.openshift.io/single-node-developer: "true"
    openshift.io/image.dockerRepositoryCheck: "2022-07-19T16:10:57Z"
  creationTimestamp: "2022-06-17T17:15:48Z"
  generation: 4
  name: must-gather
  namespace: openshift
  ownerReferences:
  - apiVersion: config.openshift.io/v1
    kind: ClusterVersion
    name: version
    uid: ece3d73d-d900-4b2a-8ea0-4b09ecf3f50e
  resourceVersion: "119601885"
  uid: 5f2d68d4-ae15-4591-9427-eec76bf621c9
spec:
  lookupPolicy:
    local: false
  tags:
  - annotations: null
    from:
      kind: DockerImage
      name: quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:03a06499c3efae948535eb61c340efb8511d3ac45db1ca9fccfe5515e49a70ac
    generation: 4
    importPolicy:
      scheduled: true
    name: latest
    referencePolicy:
      type: Source
status:
  dockerImageRepository: image-registry.openshift-image-registry.svc:5000/openshift/must-gather
  tags:
  - items:
    - created: "2022-07-19T16:10:57Z"
      dockerImageReference: quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:03a06499c3efae948535eb61c340efb8511d3ac45db1ca9fccfe5515e49a70ac
      generation: 4
      image: sha256:03a06499c3efae948535eb61c340efb8511d3ac45db1ca9fccfe5515e49a70ac
    - created: "2022-06-17T17:15:48Z"
      dockerImageReference: quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:319e2c4215311a1b8c50b7cb4445f49bb79fc22dcfc120d07adcc3d6cfe6db9f
      generation: 2
      image: sha256:319e2c4215311a1b8c50b7cb4445f49bb79fc22dcfc120d07adcc3d6cfe6db9f
    tag: latest


$ oc get images |grep 49a70ac
NAME                                                                      IMAGE REFERENCE
sha256:03a06499c3efae948535eb61c340efb8511d3ac45db1ca9fccfe5515e49a70ac   quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:03a06499c3efae948535eb61c340efb8511d3ac45db1ca9fccfe5515e49a70ac
```


### ImageContentSourcePolicy
ImageContentSourcePolicy holds cluster-wide information about how to handle registry mirror rules. When multiple policies are defined, the outcome of the behavior is defined on each field.
```yaml
---
apiVersion: operator.openshift.io/v1alpha1
kind: ImageContentSourcePolicy
metadata:
  labels:
    operators.openshift.org/catalog: "true"
  name: olm-index-redhat-operator-index-0
spec:
  repositoryDigestMirrors:
  - mirrors:
    - quayregistry-quay-quay-enterprise.apps.dm.fc38.fivegcn.com/ocp4/openshift4-ose-csi-external-snapshotter
    source: registry.redhat.io/openshift4/ose-csi-external-snapshotter
  - mirrors:
    - quayregistry-quay-quay-enterprise.apps.dm.fc38.fivegcn.com/ocp4/rhacm2-management-ingress-rhel8
    source: registry.redhat.io/rhacm2/management-ingress-rhel8
```
