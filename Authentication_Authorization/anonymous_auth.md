# Anonymous user

The system:anonymous user is enabled by default.


## Disable unauthenticated user
By default, requests to the k8s HTTPS endpoint that are not rejected by other configured authentication methods are treated as anonymous requests, and given a username of **system:anonymous** and a group of **system:unauthenticated**.

```
controlplane $ curl -k https://172.30.1.2:6443
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {},
  "status": "Failure",
  "message": "forbidden: User \"system:anonymous\" cannot get path \"/\"",
  "reason": "Forbidden",
  "details": {},
  "code": 403
}
```
By setting kube-apiserver with --anonymous-auth=false

```
controlplane $ curl -k https://172.30.1.2:6443
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {},
  "status": "Failure",
  "message": "Unauthorized",
  "reason": "Unauthorized",
  "code": 401
}
```






> https://learnk8s.io/authentication-kubernetes
