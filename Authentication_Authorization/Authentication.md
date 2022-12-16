# Authentication

## Login OCP as kubeadmin
By default, only a kubeadmin user exists on your cluster. We can find access token for kube:admin on GUI.
```diff
# curl -H "Authorization: Bearer sha256~4fU2xrpbFcC8V_ccsRqrn86vz6g55kWR0W2Y_lRWwrA" "https://api.cu-compact-1.nokia5g.redhat.lab:6443/apis/user.openshift.io/v1/users/~" -k
{
  "kind": "User",
  "apiVersion": "user.openshift.io/v1",
  "metadata": {
    "name": "kube:admin",
    "creationTimestamp": null
  },
  "groups": [
    "system:authenticated",
    "system:cluster-admins"
  ]
}

# oc login --token=sha256~4fU2xrpbFcC8V_ccsRqrn86vz6g55kWR0W2Y_lRWwrA --server=https://api.cu-compact-1.nokia5g.redhat.lab:6443
The server uses a certificate signed by an unknown authority.
You can bypass the certificate check, but any data you send to the server could be intercepted by others.
Use insecure connections? (y/n): y

Logged into "https://api.cu-compact-1.nokia5g.redhat.lab:6443" as "kube:admin" using the token provided.

You have access to 73 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
Welcome! See 'oc help' to get started.


# cat .kube/config 
apiVersion: v1
clusters:
- cluster:
    insecure-skip-tls-verify: true
    server: https://api.cu-compact-1.nokia5g.redhat.lab:6443
  name: api-cu-compact-1-nokia5g-redhat-lab:6443
contexts:
- context:
    cluster: api-cu-compact-1-nokia5g-redhat-lab:6443
    namespace: default
    user: kube:admin/api-cu-compact-1-nokia5g-redhat-lab:6443
  name: default/api-cu-compact-1-nokia5g-redhat-lab:6443/kube:admin
current-context: default/api-cu-compact-1-nokia5g-redhat-lab:6443/kube:admin
kind: Config
preferences: {}
users:
- name: kube:admin/api-cu-compact-1-nokia5g-redhat-lab:6443
  user:
    token: sha256~4fU2xrpbFcC8V_ccsRqrn86vz6g55kWR0W2Y_lRWwrA

```


## Login OCP as user managed by HTPasswd


## Access OCP API with ServiceAccount Token


## Login OCP with X.509 certificates


## 
