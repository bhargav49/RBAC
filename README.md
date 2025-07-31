# RBAC(Role based access control)

## Description
This project demonstrates how to create roles and how to bind the role to users

## RBAC flow
Create serviceAccount --> create role --> create rolebinding to assing the role to serviceAccount

## Implementation
kubernetes get nodes
cd kubernetes
ls
cd apache/
kubectl get ns
kubectl get pods -n apache
kubectl delete -f .
kubectl auth whoami
kubectl auth can-I get pods
kubectl apply -f namespace.yml
kubectl auth can-I get pods -n apache
kubectl apply -f deployment.yml
kubectl auth can-I get deployment -n apache
"vi role.yml

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: apache
  name: apache-manager
rules:
- apiGroups: ["""",""apps"",""batch""]
  resources: [""pods"",""deployment"",""service""]
  verbs: [""get"", ""watch"", ""list"",""delete"",""create"",""patch""]"
kubectl apply -f role.yml
kubectl get role -n apache
"vi serviceaccount.yml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: apache-user
  namespace: apache"
kubectl apply -f serviceaccount.yml
kubectl get serviceaccount -n apache
kubectl auth can-I get pods - n apache --as=apache-user
"vi role-binding.yml

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: apache-manager-rolebinding
  namespace: apache
subjects:
- kind: User
  name: apache-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: apache-manager
  apiGroup: rbac.authorization.k8s.io"
kubectl apply -f role-binding.yml
kubectl get rolebinding -n apache
kubectl auth can-i get pods --as=apache-user -n apache
kubectl auth can-I get deployment --as=apache-user -n apache
*** now change the role script as allow only to pods and verbs as get
vi role.yml
"vi role.yml

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: apache
  name: apache-manager
rules:
- apiGroups: [""""]    #,""apps"",""batch""]
  resources: [""pods""]       #,""deployment"",""service""]
  verbs: [""get""]          #, ""watch"", ""list"",""delete"",""create"",""patch""]"
kubectl apply -f role.yml
kubectl auth can-i get pods --as=apache-user -n apache
kubectl auth can-I get deployment --as=apache-user -n apache
*** again change the role.yml, assign all apiGroups and Resources and verbs as get only
vi roles.yml
"apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: apache
  name: apache-manager
rules:
- apiGroups: ["""",""apps"",""batch""]
  resources: [""pods"",""deployment"",""service""]
  verbs: [""get""]          #, ""watch"", ""list"",""delete"",""create"",""patch""]"
kubectl apply -f role.yml
kubectl auth can-i get pod --as=apache-user -n apache
kubectl auth can-I get deployment --as=apache-user -n apache
kubectl auth can-i get service --as=apache-user -n apache
kubectl auth can-i get hpa --as=apache-user -n apache
kubectl auth can-i delete pod --as=apache-user -n apache
vi servicea-account-hari.yml
"vi serviceaccount.yml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: hari
  namespace: apache"
** Now add this user hari to role bound to assign the role to hari
"vi role-binding.yml

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: apache-manager-rolebinding
  namespace: apache
subjects:
- kind: User
  name: apache-user
  apiGroup: rbac.authorization.k8s.io
- kind: User
  name: hari
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: apache-manager
  apiGroup: rbac.authorization.k8s.io"
cd ..
mkdir dashboard
ls
cd dashboard
** creating kubectl dashboard, search kubernetes dashboard manifest file
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
vi dashboard-admin-user.yml
"
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user-binding
  namespace: kubernetes-dashboard
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io"
kubectl apply -f dashboard-admin-user.yml
kubectl -n kubernetes-dashboard create token admin-user
**store the token somewhere
kubectl proxy --port=8001 --address=0.0.0.0 --accept-hosts='.*'
**add the port number in the ASW instance inbound rule
<img width="957" height="4309" alt="image" src="https://github.com/user-attachments/assets/84f5f21e-1143-4154-9c3b-1101bfc2f0eb" />

