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
```
# oc login -u user1 -p Password123 https://api.cu-compact-1.nokia5g.redhat.lab:6443
Login successful.

$ cat ../.kube/config 
apiVersion: v1
clusters:
- cluster:
    insecure-skip-tls-verify: true
    server: https://api.cu-compact-1.nokia5g.redhat.lab:6443
  name: api-cu-compact-1-nokia5g-redhat-lab:6443
contexts:
- context:
    cluster: api-cu-compact-1-nokia5g-redhat-lab:6443
    user: user1/api-cu-compact-1-nokia5g-redhat-lab:6443
  name: /api-cu-compact-1-nokia5g-redhat-lab:6443/user1
- context:
    cluster: api-cu-compact-1-nokia5g-redhat-lab:6443
    namespace: default
    user: kube:admin/api-cu-compact-1-nokia5g-redhat-lab:6443
  name: default/api-cu-compact-1-nokia5g-redhat-lab:6443/kube:admin
current-context: /api-cu-compact-1-nokia5g-redhat-lab:6443/user1
kind: Config
preferences: {}
users:
- name: kube:admin/api-cu-compact-1-nokia5g-redhat-lab:6443
  user:
    token: sha256~4fU2xrpbFcC8V_ccsRqrn86vz6g55kWR0W2Y_lRWwrA
- name: user1/api-cu-compact-1-nokia5g-redhat-lab:6443
  user:
    token: sha256~6Pc8lD_CYbNUGF6rW_vCYNtlaERudpoknqfVt4wLUHg

$ oc get users
NAME    UID                                    FULL NAME   IDENTITIES
user1   2ee0aad6-bf6c-480f-90c3-153f6d453073               htpasswd_provider:user1
$ oc get identity
NAME                      IDP NAME            IDP USER NAME   USER NAME   USER UID
htpasswd_provider:user1   htpasswd_provider   user1           user1       2ee0aad6-bf6c-480f-90c3-153f6d453073

```

## Access OCP API with ServiceAccount Token
Prior to OCP4.11(k8s v1.24), token is generated while creating SA. Start on OCP4.11(k8s v1.24), token is not generated anymore.


### JWT token

## Login OCP with X.509 certificates

```
$ openssl genrsa -out user2.key 2048

$ openssl req -new -key user2.key -out user2.csr

$ cat user2.csr 
-----BEGIN CERTIFICATE REQUEST-----
MIICoTCCAYkCAQAwXDELMAkGA1UEBhMCVVMxCzAJBgNVBAgMAlRYMQ4wDAYDVQQH
DAVQbGFubzEPMA0GA1UECgwGUmVkaGF0MQ8wDQYDVQQLDAZSZWRoYXQxDjAMBgNV
BAMMBXVzZXIyMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAuo4msC3s
XdCQ8Cow6ZGEpQxjnNsZFTaRcE0qsJ9vRJjjit1zkkCVGf7CGrIJgsp+7sVFljwJ
aUqlTI9DP9rxLku2cyEhdbL/Z8U1WwwiyCBfJpFO+lszoygJYB9kPB9CheF1T2us
aFxpMemF+iNqE05h7a3Z0+ubaEgLqxGecwi4e+FvESCjJZjbqY/yNGFw+t2fS5AG
uSTIFAwLrS/zfRvIDZS9noDPwXVeZfLRaf/Q8CQ6gc93s/496BO1U8HdnB8Ua7Ye
DNvxJXKh8Ummoics6CrEErgD/ETjBdxZ9bUBdmkj+TeCOimsGO5yLvawqGFuV8Vf
Liv8EP5IEtAKGwIDAQABoAAwDQYJKoZIhvcNAQELBQADggEBAEBXBMn82VKYNrUk
mpdU65B1CvDJDCMNtgRfk02oOcY256kXytd77Z80EBWjVo+ZuPrcz2NEZVDNpCld
y+ht9vU7bTHMv2dw/CuQ+ZO4CuqFJDJLkCiU08MBvurO50zl1RXt/WZlftlXZOBJ
6uwP0d9rLPYp352C6VT2YSklgljReWLRAthAU+tc2Vf0CAAs7KRCkBIaLLmjPjx1
5O4ayq3CH6LszJor08IzA5PbT9wFunjovz/aivXF276fz5VmikunE+m/HTPo1LSg
NMFkfJc0R/EuMwYur0cDLqsNVjqYrkG1eYwj5Wsj0tltMWfBCHe3cA3MDTZgIFmN
IStDPrE=
-----END CERTIFICATE REQUEST-----

$ cat user2_csr.yaml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: user2
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ29UQ0NBWWtDQVFBd1hERUxNQWtHQTFVRUJoTUNWVk14Q3pBSkJnTlZCQWdNQWxSWU1RNHdEQVlEVlFRSApEQVZRYkdGdWJ6RVBNQTBHQTFVRUNnd0dVbVZrYUdGME1ROHdEUVlEVlFRTERBWlNaV1JvWVhReERqQU1CZ05WCkJBTU1CWFZ6WlhJeU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRkFBT0NBUThBTUlJQkNnS0NBUUVBdW80bXNDM3MKWGRDUThDb3c2WkdFcFF4am5Oc1pGVGFSY0UwcXNKOXZSSmpqaXQxemtrQ1ZHZjdDR3JJSmdzcCs3c1ZGbGp3SgphVXFsVEk5RFA5cnhMa3UyY3lFaGRiTC9aOFUxV3d3aXlDQmZKcEZPK2xzem95Z0pZQjlrUEI5Q2hlRjFUMnVzCmFGeHBNZW1GK2lOcUUwNWg3YTNaMCt1YmFFZ0xxeEdlY3dpNGUrRnZFU0NqSlpqYnFZL3lOR0Z3K3QyZlM1QUcKdVNUSUZBd0xyUy96ZlJ2SURaUzlub0RQd1hWZVpmTFJhZi9ROENRNmdjOTNzLzQ5NkJPMVU4SGRuQjhVYTdZZQpETnZ4SlhLaDhVbW1vaWNzNkNyRUVyZ0QvRVRqQmR4WjliVUJkbWtqK1RlQ09pbXNHTzV5THZhd3FHRnVWOFZmCkxpdjhFUDVJRXRBS0d3SURBUUFCb0FBd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFFQlhCTW44MlZLWU5yVWsKbXBkVTY1QjFDdkRKRENNTnRnUmZrMDJvT2NZMjU2a1h5dGQ3N1o4MEVCV2pWbytadVByY3oyTkVaVkROcENsZAp5K2h0OXZVN2JUSE12MmR3L0N1UStaTzRDdXFGSkRKTGtDaVUwOE1CdnVyTzUwemwxUlh0L1dabGZ0bFhaT0JKCjZ1d1AwZDlyTFBZcDM1MkM2VlQyWVNrbGdsalJlV0xSQXRoQVUrdGMyVmYwQ0FBczdLUkNrQklhTExtalBqeDEKNU80YXlxM0NINkxzekpvcjA4SXpBNVBiVDl3RnVuam92ei9haXZYRjI3NmZ6NVZtaWt1bkUrbS9IVFBvMUxTZwpOTUZrZkpjMFIvRXVNd1l1cjBjRExxc05WanFZcmtHMWVZd2o1V3NqMHRsdE1XZkJDSGUzY0EzTURUWmdJRm1OCklTdERQckU9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
  - client auth

$ oc apply -f user2_csr.yaml 
certificatesigningrequest.certificates.k8s.io/user2 created

$ oc get csr
NAME    AGE   SIGNERNAME                            REQUESTOR    REQUESTEDDURATION   CONDITION
user2   4s    kubernetes.io/kube-apiserver-client   kube:admin   24h                 Pending

$ oc adm certificate approve user2
certificatesigningrequest.certificates.k8s.io/user2 approved

$ oc get csr
NAME    AGE   SIGNERNAME                            REQUESTOR    REQUESTEDDURATION   CONDITION
user2   93s   kubernetes.io/kube-apiserver-client   kube:admin   24h                 Approved,Issued

$ oc get csr user2 -o jsonpath={'.status.certificate'} | base64 -d > user2.crt
-----BEGIN CERTIFICATE-----
MIIDUzCCAjugAwIBAgIRALE+lo8xRMHLdHLb54rRyowwDQYJKoZIhvcNAQELBQAw
JjEkMCIGA1UEAwwba3ViZS1jc3Itc2lnbmVyX0AxNjcwMjQ5MjczMB4XDTIyMTIx
NjE2NTMyNFoXDTIyMTIxNzE2NTMyNFowXDELMAkGA1UEBhMCVVMxCzAJBgNVBAgT
AlRYMQ4wDAYDVQQHEwVQbGFubzEPMA0GA1UEChMGUmVkaGF0MQ8wDQYDVQQLEwZS
ZWRoYXQxDjAMBgNVBAMTBXVzZXIyMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIB
CgKCAQEAuo4msC3sXdCQ8Cow6ZGEpQxjnNsZFTaRcE0qsJ9vRJjjit1zkkCVGf7C
GrIJgsp+7sVFljwJaUqlTI9DP9rxLku2cyEhdbL/Z8U1WwwiyCBfJpFO+lszoygJ
YB9kPB9CheF1T2usaFxpMemF+iNqE05h7a3Z0+ubaEgLqxGecwi4e+FvESCjJZjb
qY/yNGFw+t2fS5AGuSTIFAwLrS/zfRvIDZS9noDPwXVeZfLRaf/Q8CQ6gc93s/49
6BO1U8HdnB8Ua7YeDNvxJXKh8Ummoics6CrEErgD/ETjBdxZ9bUBdmkj+TeCOims
GO5yLvawqGFuV8VfLiv8EP5IEtAKGwIDAQABo0YwRDATBgNVHSUEDDAKBggrBgEF
BQcDAjAMBgNVHRMBAf8EAjAAMB8GA1UdIwQYMBaAFPZqP/CTM1Fz/urhz25QHmKW
vRQDMA0GCSqGSIb3DQEBCwUAA4IBAQCB02Lf1YrT1vomq2Cik0WvWXUhZnuMASnx
MKu/KYoxInjxLC6SMiitryBty5zWlsi8lL4IiFrvNqPOBFO0AOs7WxrBgzMXYm7w
qtlil4TpoGp2HEaU8xCMPuaL2QPlWZqz4L1OgLtNJUv5SaGu1R4HPCQ8MtH5/Qkc
mhVSY1FVrafJIanxWLyfdzXfCjr/RP6Nqipk8z6kbf7Wlqc7Y48KN1lvt1eVXgWA
kHex+Cmc/MA2zvsfP/EgmvdM4vhXhhPF3ItFKsZ+QOYn5sPJK9M/pE4LLrMNa8Io
ciiUVlZA8BkpI7qEDs2TyYHTQelj9MrzbW6r9r5bvtN2RidP8G+g
-----END CERTIFICATE-----

$ oc config set-credentials user2 --client-certificate=user2.crt --client-key=user2.key 
User "user2" set.

$ oc config set-context user2 --cluster=api-cu-compact-1-nokia5g-redhat-lab:6443  --user=user2
Context "user2" created.

$ oc config get-contexts 
CURRENT   NAME                                                          CLUSTER                                    AUTHINFO                                              NAMESPACE
          /api-cu-compact-1-nokia5g-redhat-lab:6443/user1               api-cu-compact-1-nokia5g-redhat-lab:6443   user1/api-cu-compact-1-nokia5g-redhat-lab:6443        
*         default/api-cu-compact-1-nokia5g-redhat-lab:6443/kube:admin   api-cu-compact-1-nokia5g-redhat-lab:6443   kube:admin/api-cu-compact-1-nokia5g-redhat-lab:6443   default
          user2                                                         api-cu-compact-1-nokia5g-redhat-lab:6443   user2     

$ oc config use-context user2
Switched to context "user2".


```
## 
