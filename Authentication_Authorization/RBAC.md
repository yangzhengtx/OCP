# RBAC

- **Rule**    Allowed actions for objects or groups of objects.
- **Role**  	Sets of rules. Users and groups can be associated with multiple roles.
- **Rolebinding** 	Assignment of users or groups to a role.
- **Clusterrole**   Can be re-used in multiple namespaces.
- **Clusterrolebinding**   Assign clusterrole to user or serviceaccount.
![image](https://user-images.githubusercontent.com/75378303/206585384-d228abd9-fbe5-40a3-af34-c012954f981d.png)

## Role
Createing new role:
```yaml
# oc create role name1 --verb create,patch --resource secrets,pods -namespace ns1

# cat <<EOF | oc apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: bookinfo
  name: role1
rules:
- apiGroups: [""]
  resources: ["pods","deployments"]
  verbs: ["create","update"]
EOF
```

## Rolebinding

```yaml
# oc policy add-role-to-user basic-user user2 -n test_project

```


## Clusterrole
Default clusterrole:
- admin
- edit
- view
- cluster-admin
- cluster-status
- cluster-reader

Creating new Clusterrole:
```yaml
# oc create clusterrole podview --verb get --resource pods

# cat <<EOF | oc apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: podview
rules:
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - get  
```


## Clusterrolebinding
```yaml
# oc adm policy add-cluster-role-to-user cluster-admin user1
clusterrole.rbac.authorization.k8s.io/cluster-admin added: "user1"

# oc adm policy remove-cluster-role-from-user cluster-admin user1

```

> [Reference](https://docs.openshift.com/container-platform/4.11/authentication/using-rbac.html)
