**Deploy Ghost CMS with MySQL server to Kubernetes**

These manifests can be used also in any Kubernetes cluster, but make sure to change storage, MySQL root password and Ghost CMS credentials.

Manifests work with Minikube out-of-the-box (https://minikube.sigs.k8s.io/docs/start/).

**Create namespace**
```
D:\Ghost>minikube kubectl -- create namespace ghost
namespace/ghost created
```

**MySQL deployment**
```
D:\Ghost>**minikube kubectl -- apply -f mysql-server.yaml -n ghost**
secret/mysqlserver created
persistentvolume/mysql-pv-volume created
persistentvolumeclaim/mysql-pv-claim created
service/mysql created
deployment.apps/mysql created

D:\Ghost>minikube kubectl -- get pv -n ghost
NAME              CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
mysql-pv-volume   20Gi       RWO            Retain           Bound    ghost/mysql-pv-claim   manual                  34s

D:\Ghost>minikube kubectl -- get pvc -n ghost
NAME             STATUS   VOLUME            CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mysql-pv-claim   Bound    mysql-pv-volume   20Gi       RWO            manual         51s

D:\Ghost>minikube kubectl -- get services -n ghost
NAME    TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
mysql   ClusterIP   None         <none>        3306/TCP   67s

D:\Ghost>minikube kubectl -- get pods -n ghost
NAME                     READY   STATUS    RESTARTS   AGE
mysql-5bdcf88f4f-zrhk9   1/1     Running   0          78s
```
**Start Minikube tunnel (in an another shell)**
```
D:\Ghost>minikube tunnel
Status:
        machine: minikube
        pid: 8000
        route: 10.96.0.0/12 -> 192.168.1.174
        minikube: Running
        services: []
    errors:
                minikube: no errors
                router: no errors
                loadbalancer emulator: no errors
```
**Ghost CMS deployment**
```
D:\Ghost>minikube kubectl -- apply -f ghost-deployment.yaml -n ghost
secret/dbdata created
persistentvolume/pv0001 created
persistentvolumeclaim/pvc-ghost created
service/ghost-service created
deployment.apps/ghost created

D:\Ghost>minikube kubectl -- get pv -n ghost
NAME              CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
mysql-pv-volume   20Gi       RWO            Retain           Bound    ghost/mysql-pv-claim   manual                  3m59s
pv0001            5Gi        RWO            Retain           Bound    ghost/pvc-ghost        manual                  39s

D:\Ghost>minikube kubectl -- get pvc -n ghost
NAME             STATUS   VOLUME            CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mysql-pv-claim   Bound    mysql-pv-volume   20Gi       RWO            manual         3m49s
pvc-ghost        Bound    pv0001            5Gi        RWO            manual         29s

D:\Ghost>minikube kubectl -- get pods -n ghost
NAME                     READY   STATUS    RESTARTS   AGE
ghost-67d6d44769-4xvst   1/1     Running   0          70s
mysql-5bdcf88f4f-zrhk9   1/1     Running   0          4m29s

D:\Ghost>minikube kubectl -- get services -n ghost
NAME            TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)          AGE
ghost-service   LoadBalancer   10.110.72.132   10.110.72.132   8080:32205/TCP   58s
mysql           ClusterIP      None            <none>          3306/TCP         4m18s
```
**Minikube on another shell is now showing services tunneled**
```
Status:
        machine: minikube
        pid: 8000
        route: 10.96.0.0/12 -> 192.168.1.174
        minikube: Running
        services: [ghost-service]
    errors:
                minikube: no errors
                router: no errors
                loadbalancer emulator: no errors
```                

**Use Minikube to open Ghost CMS in a webpage**
```
D:\Ghost>minikube service ghost-service -n ghost
|-----------|---------------|-------------|----------------------------|
| NAMESPACE |     NAME      | TARGET PORT |            URL             |
|-----------|---------------|-------------|----------------------------|
| ghost     | ghost-service |        8080 | http://192.168.1.174:32205 |
|-----------|---------------|-------------|----------------------------|
* Opening service ghost/ghost-service in default browser...
```

Admin panel is available at http://yourghostaddres:port/ghost.
