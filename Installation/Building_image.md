# Building Image






## Example: 
Building new application http server:
```
$ oc project default
Now using project "default" on server "https://api.dm.fc38.fivegcn.com:6443".

$ oc new-app openshift/httpd:2.4-el8~https://github.com/sclorg/httpd-ex.git --name=http-server
--> Found image 08c6e94 (7 weeks old) in image stream "openshift/httpd" under tag "2.4-el8" for "openshift/httpd:2.4-el8"

    Apache httpd 2.4 
    ---------------- 
    Apache httpd 2.4 available as container, is a powerful, efficient, and extensible web server. Apache supports a variety of features, many implemented as compiled modules which extend the core functionality. These can range from server-side programming language support to authentication schemes. Virtual hosting allows one Apache installation to serve many different Web sites.

    Tags: builder, httpd, httpd-24

    * A source build using source code from https://github.com/sclorg/httpd-ex.git will be created
      * The resulting image will be pushed to image stream tag "http-server:latest"
      * Use 'oc start-build' to trigger a new build

--> Creating resources ...
    imagestream.image.openshift.io "http-server" created
    buildconfig.build.openshift.io "http-server" created
    deployment.apps "http-server" created
    service "http-server" created
--> Success
    Build scheduled, use 'oc logs -f buildconfig/http-server' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose service/http-server' 
    Run 'oc status' to view your app.

$ oc status
In project default on server https://api.dm.fc38.fivegcn.com:6443

svc/http-server - 172.30.131.254 ports 8080, 8443
  deployment/http-server deploys istag/http-server:latest <-
    bc/http-server source builds https://github.com/sclorg/httpd-ex.git on openshift/httpd:2.4-el8 
    deployment #2 running for about a minute - 1 pod
    deployment #1 deployed about a minute ago

svc/openshift - kubernetes.default.svc.cluster.local
svc/kubernetes - 172.30.0.1:443 -> 6443


1 info identified, use 'oc status --suggest' to see details.

$ oc get all
NAME                               READY   STATUS      RESTARTS   AGE
pod/http-server-1-build            0/1     Completed   0          2m40s
pod/http-server-8687bff9c7-whwjp   1/1     Running     0          2m14s

NAME                  TYPE           CLUSTER-IP       EXTERNAL-IP                            PORT(S)             AGE
service/http-server   ClusterIP      172.30.131.254   <none>                                 8080/TCP,8443/TCP   2m40s
service/kubernetes    ClusterIP      172.30.0.1       <none>                                 443/TCP             35d
service/openshift     ExternalName   <none>           kubernetes.default.svc.cluster.local   <none>              34d

NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/http-server   1/1     1            1           2m40s

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/http-server-5b854d94b6   0         0         0       2m40s
replicaset.apps/http-server-8687bff9c7   1         1         1       2m14s

NAME                                         TYPE     FROM   LATEST
buildconfig.build.openshift.io/http-server   Source   Git    1

NAME                                     TYPE     FROM          STATUS     STARTED         DURATION
build.build.openshift.io/http-server-1   Source   Git@744d904   Complete   2 minutes ago   29s

NAME                                         IMAGE REPOSITORY                                                       TAGS     UPDATED
imagestream.image.openshift.io/http-server   image-registry.openshift-image-registry.svc:5000/default/http-server   latest   2 minutes ago
```

Delete application http-server:
```
$ oc get all --selector app=http-server
NAME                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service/http-server   ClusterIP   172.30.131.254   <none>        8080/TCP,8443/TCP   6m30s

NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/http-server   1/1     1            1           6m30s

NAME                                         TYPE     FROM   LATEST
buildconfig.build.openshift.io/http-server   Source   Git    1

NAME                                     TYPE     FROM          STATUS     STARTED         DURATION
build.build.openshift.io/http-server-1   Source   Git@744d904   Complete   6 minutes ago   29s

NAME                                         IMAGE REPOSITORY                                                       TAGS     UPDATED
imagestream.image.openshift.io/http-server   image-registry.openshift-image-registry.svc:5000/default/http-server   latest   6 minutes ago

$ oc delete all --selector app=http-server
service "http-server" deleted
deployment.apps "http-server" deleted
buildconfig.build.openshift.io "http-server" deleted
imagestream.image.openshift.io "http-server" deleted

```


## Openshift Image and ImageStream Operations
Image Operations:
```
List all images available in the cluster:
$ oc get images
NAME                                                                      IMAGE REFERENCE
sha256:0155e28b6b10d6fba7144df9ce33105acbab32766836baacc67c76b900a58772   registry.redhat.io/redhat-sso-7/sso70-openshift@sha256:0155e28b6b10d6fba7144df9ce33105acbab32766836baacc67c76b900a58772
sha256:01920073bbd480bd34e2d8e17dced64d342257fa9a263d1843edf1cc45a50a7c   registry.redhat.io/jboss-datagrid-7/datagrid73-openshift@sha256:01920073bbd480bd34e2d8e17dced64d342257fa9a263d1843edf1cc45a50a7c
sha256:02b7619ced8942c4c017fad2f14e533807d12abdaafc3cff4dc3ca1a9089942e   registry.redhat.io/ubi8/nodejs-16-minimal@sha256:02b7619ced8942c4c017fad2f14e533807d12abdaafc3cff4dc3ca1a9089942e
sha256:1c781479a9d46307f611b9f9dbcd38e860a0ae25f282d0147bb218767fdb628d   image-registry.openshift-image-registry.svc:5000/httpd-rhcos-images/rhcos-images@sha256:1c781479a9d46307f611b9f9dbcd38e860a0ae25f282d0147bb218767fdb628d
sha256:d41d60d2b2d997bf2ab3360ac1c4a98cf9451940d47f43c5226bcdde0b36f497   image-registry.openshift-image-registry.svc:5000/httpd-rhcos-images/rhcos-images@sha256:d41d60d2b2d997bf2ab3360ac1c4a98cf9451940d47f43c5226bcdde0b36f497
sha256:edc0987f7991055d1e81ba8abbe2abdebc436f38f2a4d714a4d73c945db3e5ac   image-registry.openshift-image-registry.svc:5000/default/http-server@sha256:edc0987f7991055d1e81ba8abbe2abdebc436f38f2a4d714a4d73c945db3e5ac

$ oc delete image sha256:edc0987f7991055d1e81ba8abbe2abdebc436f38f2a4d714a4d73c945db3e5ac
image.image.openshift.io "sha256:edc0987f7991055d1e81ba8abbe2abdebc436f38f2a4d714a4d73c945db3e5ac" deleted

Tag an image from a project with a new tag in a different project:
$ oc tag <source-project>/<image-name>:latest <target-project>/<different-image-name>:<version-tag>
```

Image Stream Operations:
```

```
