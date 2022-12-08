# Audit logs in OCP
Logs generated by auditd, the node audit system, which are stored in the /var/log/audit/audit.log file, and the audit logs from the Kubernetes apiserver and the OpenShift apiserver.

- CoreOS: /var/log/audit/audit.log
- openshift-apiserver: /var/log/openshift-apiserver/audit.*
- openshift-kube-apiserver: /var/log/kube-apiserver/audit.*
- openshift-oauth-apiserver: /var/log/oauth-apiserver/audit.*

To view the audit logs in OCP cluster:
```
oc adm node-logs --role=master --path=openshift-apiserver/

oc adm node-logs --role=master --path=kube-apiserver/

oc adm node-logs --role=master --path=oauth-apiserver/
```

Brief introduction for api servers:
- openshift-apiserver: The Openshift specific APIs, like Routes, BuildConfig. It's Kubernetes API Aggregation Layer, which allows Kubernetes to be extended with additional APIs, beyond what is offered by the core Kubernetes APIs. 
- openshift-kube-apiserver: The Kubernetes API server validates and configures data for the api objects which include pods, services, replicationcontrollers, and others. The API Server services REST operations and provides the frontend to the cluster's shared state through which all other components interact.
- openshift-oauth-apiserver: The OpenShift Container Platform master includes a built-in OAuth server. Users obtain OAuth access tokens to authenticate themselves to the API. When a person requests a new OAuth token, the OAuth server uses the configured identity provider to determine the identity of the person making the request. It then determines what user that identity maps to, creates an access token for that user, and returns the token for use.


## Configuring the audit log policy [^1]
Audit log profiles define how to log requests that come to the OpenShift API server, the Kubernetes API server, and the OAuth API server.

OpenShift Container Platform provides the following predefined audit policy profiles:
- Default: Logs only metadata for read and write requests; does not log request bodies except for OAuth access token requests. This is the default policy.
- WriteRequestBodies: In addition to logging metadata for all requests, logs request bodies for every write request to the API servers (create, update, patch). This profile has more resource overhead than the Default profile. [1]
- AllRequestBodies: In addition to logging metadata for all requests, logs request bodies for every read and write request to the API servers (get, list, create, update, patch). This profile has the most resource overhead. [1]
- None: No requests are logged; even OAuth access token requests and OAuth authorize token requests are not logged.


### Configuring the audit log policy
```diff
$ oc get apiservers.config.openshift.io cluster -o yaml
apiVersion: config.openshift.io/v1
kind: APIServer
metadata:
  annotations:
    include.release.openshift.io/ibm-cloud-managed: "true"
    include.release.openshift.io/self-managed-high-availability: "true"
    include.release.openshift.io/single-node-developer: "true"
    oauth-apiserver.openshift.io/secure-token-storage: "true"
    release.openshift.io/create-only: "true"
  creationTimestamp: "2022-06-17T16:32:13Z"
  generation: 1
  name: cluster
  ownerReferences:
  - apiVersion: config.openshift.io/v1
    kind: ClusterVersion
    name: version
    uid: ece3d73d-d900-4b2a-8ea0-4b09ecf3f50e
  resourceVersion: "1137"
  uid: 96fc0f20-0215-48e6-8d6b-362a479eb3b1
spec:
  audit:
    profile: Default
```

### Configuring the audit log policy with custom rules
```diff
$ oc edit apiserver cluster
apiVersion: config.openshift.io/v1
kind: APIServer
metadata:
...
spec:
  audit:
    customRules:                        
    - group: system:authenticated:oauth
      profile: WriteRequestBodies
    - group: system:authenticated
      profile: None
    profile: Default           

```

**customRules:** These custom rules take precedence over the top-level profile field. The custom rules are evaluated from top to bottom, and the first that matches is applied.

**group:** group is a name of group a request user must be member of in order to this profile to apply.
- system:authenticated
- system:authenticated:oauth
- system:serviceaccounts
- system:serviceaccounts:open-cluster-management

**profile:** 
- Default
- WriteRequestBodies
- AllRequestBodies
- None


Verify that a new revision of the Kubernetes API server pods is rolled out. 
```
$ oc get kubeapiserver -o=jsonpath='{range .items[0].status.conditions[?(@.type=="NodeInstallerProgressing")]}{.reason}{"\n"}{.message}{"\n"}'
```

>Reference:
[^1]: https://docs.openshift.com/container-platform/4.10/security/audit-log-policy-config.html

[^2]: test