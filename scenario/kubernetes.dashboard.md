# Kubernetes Web UI Dashboard
Kubernetes Dashboard is an official web-based user interface (UI) designed especially for Kubernetes clusters. The dashboard can display all workloads running in the cluster. It also includes features that can help you control and modify your workloads, and can display logs of activity on pods.

<img src="https://d33wubrfki0l68.cloudfront.net/349824f68836152722dab89465835e604719caea/6e0b7/images/docs/ui-dashboard.png">

# Deploying the Dashboard UI
The Dashboard UI is not deployed by default. To deploy it, run the following command:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.6.1/aio/deploy/recommended.yaml
```
## check dashboard service

```
kubectl get service -A
```
## Change ClusterIP to NodePort Service

```
kubectl -n kubernetes-dashboard patch svc kubernetes-dashboard --type='json' -p '[{"op":"replace","path":"/spec/type","value":"NodePort"}]'
```
## check dashboard service

```
kubectl get service -A
```

## Get Node port assigned to service

```
kubectl -n kubernetes-dashboard get services kubernetes-dashboard -o go-template='{{(index .spec.ports 0).nodePort}}'
```

## Creating a Service Account
```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
EOF
```

## Creating a ClusterRoleBinding
```
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF
```

## check objects
```
kubectl get pod,service,deployment -n kubernetes-dashboard
```
## Getting a Bearer Token

```
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```


## Check service & pod & namespace dashboard status
```
kubectl get pod -A
watch "kubectl -n kubernetes-dashboard get pod"
kubectl -n kubernetes-dashboard describe pod kubernetes-dashboard-74d688b6bc-jvlr8
docker pull kubernetes/dashboard:v2.0.0
```

## login to dashboard ui
check dashboard port on master
```
netstat -ntlp | grep "dashboard port"
```
in browser type "https://"Maser-IP":"Dashboard-Port""
and you can see login page of kubernetes dashboard
![image](https://user-images.githubusercontent.com/57698853/195895156-3c58cb87-b334-44af-a784-68b76e027154.png)

then paste Token that creat abow in step "Getting a Bearer Token"
and then Login will be success.
