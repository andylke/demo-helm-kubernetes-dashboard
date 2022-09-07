# Install Kubernetes Dashboard with NGINX Ingress using Helm

## Edit `/etc/hosts` to map `dashboard.example.com`

```
127.0.0.1 example.com
127.0.0.1 dashboard.example.com
```

## Configure Skip Login and Ingress

Create `values.yaml`

```
extraArgs:
  - --enable-skip-login
  - --disable-settings-authorizer
ingress:
  enabled: true
  className: nginx
  hosts:
  - dashboard.example.com
```

```
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
helm repo update
helm -n kubernetes-dashboard install --create-namespace kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard -f values.yaml
```

Verify status
`kubectl -n kubernetes-dashboard get pod,svc,ingress`

```
NAME                                       READY   STATUS    RESTARTS   AGE
pod/kubernetes-dashboard-646dddfcc-c2cbl   1/1     Running   0          88s

NAME                           TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/kubernetes-dashboard   ClusterIP   10.105.8.57   <none>        443/TCP   88s

NAME                                             CLASS   HOSTS                   ADDRESS     PORTS   AGE
ingress.networking.k8s.io/kubernetes-dashboard   nginx   dashboard.example.com   localhost   80      88s
```

Verify exposed site
`curl http://dashboard.example.com:8080`

```
StatusCode        : 200
StatusDescription : OK
Content           : <!--
                    Copyright 2017 The Kubernetes Authors.

                    Licensed under the Apache License, Version 2.0 (the "License");
                    you may not use this file except in compliance with the License.
                    You may obtain a copy of t...
```

## Uninstall Kubernetes Dashboard

Uninstall
`helm -n kubernetes-dashboard uninstall kubernetes-dashboard`

Delete Namespace
`kubectl create namespace kubernetes-dashboard`
