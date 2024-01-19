W repozytorium zmieniono plik values.yaml
Orginalne wartości parametrów w pliku values:
```yaml
service.type: LoadBalancer
service.ports.http: ""
service.ports.https: ""
ingress.enabled: false
ingress.ingressClassName: ""
```
Zmieniono na:
```yaml
service.type: NodePort
service.ports.http: 30080
service.ports.https: 30443
ingress.enabled: true
ingress.ingressClassName: nginx
```
Instalacja
```console
djkrnl@djkrnl-VirtualBox:~/Pobrane/bitnami nginx$ helm install -f nginx/values.yaml my-release ./nginx
NAME: my-release
LAST DEPLOYED: Fri Jan 19 23:04:37 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: nginx
CHART VERSION: 15.7.0
APP VERSION: 1.25.3

** Please be patient while the chart is being deployed **
NGINX can be accessed through the following DNS name from within your cluster:

    my-release-nginx.default.svc.cluster.local (port 80)

To access NGINX from outside the cluster, follow the steps below:

1. Get the NGINX URL and associate its hostname to your cluster external IP:

   export CLUSTER_IP=$(minikube ip) # On Minikube. Use: `kubectl cluster-info` on others K8s clusters
   echo "NGINX URL: http://nginx.local"
   echo "$CLUSTER_IP  nginx.local" | sudo tee -a /etc/hosts
```
Sprawdzenie działania (elementy z prefiksem my-release-nginx)
```console
djkrnl@djkrnl-VirtualBox:~/Pobrane/bitnami nginx$ kubectl get all
NAME                                    READY   STATUS    RESTARTS        AGE
pod/my-release-nginx-649c5864c4-rjbdx   1/1     Running   0               11s
pod/myweb-764bfbfc68-8bspj              1/1     Running   1 (4h51m ago)   2d14h
pod/newdb-58cd55c999-m4hgv              1/1     Running   1 (4h51m ago)   2d14h

NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/kubernetes         ClusterIP   10.96.0.1        <none>        443/TCP        30d
service/my-release-nginx   NodePort    10.106.187.173   <none>        80:30080/TCP   11s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-release-nginx   1/1     1            1           11s
deployment.apps/myweb              1/1     1            1           30d
deployment.apps/newdb              1/1     1            1           30d

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/my-release-nginx-649c5864c4   1         1         1       11s
replicaset.apps/myweb-764bfbfc68              1         1         1       30d
replicaset.apps/newdb-58cd55c999              1         1         1       30d
replicaset.apps/newdb-6b688c56c5              0         0         0       30d
djkrnl@djkrnl-VirtualBox:~/Pobrane/bitnami nginx$ kubectl get ingress
NAME                   CLASS           HOSTS         ADDRESS        PORTS   AGE
my-release-nginx       nginx           nginx.local   192.168.49.2   80      36m
zad2-ingress-service   nginx-example   zad2.lab      192.168.49.2   80      6d2h
```
Dodanie przekierowania do /etc/hosts:
```
192.168.49.2 nginx.local
```
Próba połączenia curlem przez link
```console
djkrnl@djkrnl-VirtualBox:~/Pobrane/bitnami nginx$ curl nginx.local
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
