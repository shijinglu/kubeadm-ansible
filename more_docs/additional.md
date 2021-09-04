


# heterogeneous network interface

```
--iface wlo2 --iface etho0 ...
```


# Access denied error [Kubenetes Control Panel](https://www.edureka.co/community/34714/code-error-403-when-trying-to-access-kubernetes-cluster)

kubectrl cluster-info showed the control panel URL is https://192.168.1.190:6443, but access to the endpiont got access denial.

```shell
$ kubectl cluster-info
# Kubernetes control plane is running at https://192.168.1.190:6443
# CoreDNS is running at https://192.168.1.190:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

Solution:

`kubectl create clusterrolebinding cluster-system-anonymous --clusterrole=cluster-admin --user=system:anonymous`


# Important K8S configs

## kubelet configs
- /var/lib/kubelet/config.yaml

## CNI configs
- /etc/kubernetes/network/

## system files
- /etc/systemd/system/kubelet.service.d/
- /etc/systemd/system/etcd.service
- /var/lib/etcd
- /etc/apt/sources.list


# install dashboard web ui

```sh
$ helm install my-release kubernetes-dashboard/kubernetes-dashboard
# NAME: my-release
# LAST DEPLOYED: Thu Sep  2 01:55:58 2021
# NAMESPACE: default
# STATUS: deployed
# REVISION: 1
# ...
# 
# Get the Kubernetes Dashboard URL by running:
# export POD_NAME=$(kubectl get pods -n default -l "app.kubernetes.io/name=kubernetes-dashboard,app.kubernetes.io/instance=my-release" -o jsonpath="{.# items[0].metadata.name}")
# echo https://127.0.0.1:8443/
# kubectl -n default port-forward $POD_NAME 8443:8443
```

Get token:

```sh
# https://www.replex.io/blog/how-to-install-access-and-add-heapster-metrics-to-the-kubernetes-dashboard
$ kubectl create serviceaccount dashboard-admin-sa
$ kubectl create clusterrolebinding dashboard-admin-sa --clusterrole=cluster-admin --serviceaccount=default:dashboard-admin-sa
$ kubectl get secrets
$ kubectl describe secret my-release-kubernetes-dashboard-token-g9m9s

#Name:         my-release-kubernetes-dashboard-token-g9m9s
#Namespace:    default
#Labels:       <none>
#Annotations:  kubernetes.io/service-account.name: my-release-kubernetes-dashboard
#              kubernetes.io/service-account.uid: 51b95f8b-2e88-443e-a75b-cf6f69e28714
#
#Type:  kubernetes.io/service-account-token
#
#Data
#====
#ca.crt:     1099 bytes
#namespace:  7 bytes
#token:      ....

```

Problems:
```log
services is forbidden: User "system:serviceaccount:default:my-release-kubernetes-dashboard" cannot list resource "services" in API group "" in the namespace "default"
```


# according to https://maheshkumar.wordpress.com/2019/01/26/k8s-kubernetes-dashboard-access-warnings/

if 
```sh
# system:serviceaccount:kube-system:kubernetes-dashboard” cannot list configmaps in the namespace “default” k8rbac
```

then
```sh
$ kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Hence 
```sh
# system:serviceaccount:default:my-release-kubernetes-dashboard
```

should have:
```sh
# kubectl create clusterrolebinding my-release-kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=default:my-release-kubernetes-dashboard
```


