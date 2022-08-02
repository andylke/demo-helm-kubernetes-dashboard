# Install Kubernetes Dashboard with NGINX Ingress using Helm

## Create namespace k8s-dashboard

```
kubectl create namespace k8s-dashboard
```

## Install NGINX Ingress

```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

```
helm repo update
```

```
helm -n k8s-dashboard install ingress-nginx ingress-nginx/ingress-nginx --set=controller.service.ports.http=8001,controller.service.ports.https=8443,controller.service.loadBalancerIP=localhost
```

```
NAME: ingress-nginx
LAST DEPLOYED: Tue Aug  2 11:33:46 2022
NAMESPACE: k8s-dashboard
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace k8s-dashboard get services -o wide -w ingress-nginx-controller'

An example Ingress that makes use of the controller:
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: example
    namespace: foo
  spec:
    ingressClassName: nginx
    rules:
      - host: www.example.com
        http:
          paths:
            - pathType: Prefix
              backend:
                service:
                  name: exampleService
                  port:
                    number: 80
              path: /
    # This section is only required if TLS is to be enabled for the Ingress
    tls:
      - hosts:
        - www.example.com
        secretName: example-tls

If TLS is enabled for the Ingress, a Secret containing the certificate and key must also be provided:

  apiVersion: v1
  kind: Secret
  metadata:
    name: example-tls
    namespace: foo
  data:
    tls.crt: <base64 encoded cert>
    tls.key: <base64 encoded key>
  type: kubernetes.io/tls
```

```
curl http://localhost:8001
```

```
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

## Install Kubernetes Dashboard

```
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
```

```
helm repo update
```

```
helm -n k8s-dashboard install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --set=ingress.enabled=true,ingress.className=nginx -f kubernetes-dashboard-enable-skip-login.yaml
```

```
NAME: kubernetes-dashboard
LAST DEPLOYED: Tue Aug  2 11:36:39 2022
NAMESPACE: k8s-dashboard
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
*********************************************************************************
*** PLEASE BE PATIENT: kubernetes-dashboard may take a few minutes to install ***
*********************************************************************************
From outside the cluster, the server URL(s) are:
```

```
kubectl -n k8s-dashboard get all
```

```
NAME                                            READY   STATUS    RESTARTS   AGE
pod/ingress-nginx-controller-6bf7bc7f94-wwbd5   1/1     Running   0          3m5s
pod/kubernetes-dashboard-7db96b7648-fr8vd       1/1     Running   0          18s

NAME                                         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                         AGE
service/ingress-nginx-controller             LoadBalancer   10.104.51.67    localhost     8001:32373/TCP,8443:30003/TCP   3m6s
service/ingress-nginx-controller-admission   ClusterIP      10.99.171.162   <none>        443/TCP                         3m6s
service/kubernetes-dashboard                 ClusterIP      10.104.240.57   <none>        443/TCP                         18s

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           3m6s
deployment.apps/kubernetes-dashboard       1/1     1            1           18s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-6bf7bc7f94   1         1         1       3m5s
replicaset.apps/kubernetes-dashboard-7db96b7648       1         1         1       18s
```

```
kubectl -n k8s-dashboard get ingress
```

```
NAME                   CLASS   HOSTS   ADDRESS     PORTS   AGE
kubernetes-dashboard   nginx   *       localhost   80      13s
```

```
curl http://localhost:8001
```

```
<!--
Copyright 2017 The Kubernetes Authors.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
--><!DOCTYPE html><html lang="en" dir="ltr"><head>
  <meta charset="utf-8">
  <title>Kubernetes Dashboard</title>
  <link rel="icon" type="image/png" href="assets/images/kubernetes-logo.png">
  <meta name="viewport" content="width=device-width">
<style>html,body{height:100%;margin:0}*::-webkit-scrollbar{background:transparent;height:8px;width:8px}</style><link rel="stylesheet" href="styles.243e6d874431c8e8.css" media="print" onload="this.media='all'"><noscript><link rel="stylesheet" href="styles.243e6d874431c8e8.css"></noscript></head>

<body>
  <kd-root></kd-root>
<script src="runtime.4e6659debd894e59.js" type="module"></script><script src="polyfills.5c84b93f78682d4f.js" type="module"></script><script src="scripts.2c4f58d7c579cacb.js" defer></script><script src="en.main.0d231cb1cda9c4cf.js" type="module"></script>


</body></html>
```

## Uninstall Kubernetes Dashboard

```
helm -n k8s-dashboard uninstall ingress-nginx
```

```
helm -n k8s-dashboard uninstall kubernetes-dashboard
```

```
kubectl create namespace k8s-dashboard
```
