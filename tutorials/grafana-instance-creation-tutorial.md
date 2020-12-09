---
title: Grafana Instance Creation tutorial
description: This tutorial explains how create Instances for your Grafana Operator.
---

### Grafana Operator Instance Creation

### Create this CR which will create a Grafana Instance

```
cat <<'EOF' > GrafanaInstance.yaml
apiVersion: integreatly.org/v1alpha1
kind: Grafana
metadata:
  name: example-grafana
spec:
  ingress:
    enabled: true
  config:
    auth:
      disable_signout_menu: true
    auth.anonymous:
      enabled: true
    log:
      level: warn
      mode: console
    security:
      admin_password: secret
      admin_user: root
  dashboardLabelSelector:
    - matchExpressions:
        - key: app
          operator: In
          values:
            - grafana
EOF
```

Execute below command to create Grafana instance

```
kubectl create -f GrafanaInstance.yaml -n my-grafana-operator
```

Get the associated Pods:

```
kubectl get pods -n my-grafana-operator
```

You will see the following resources to be created:

```output
grafana.integreatly.org/example-grafana created
```

Get the associated Pods:

```execute
kubectl get pods -n operators
```

You will be able to see the below output:

```output
NAME                                  READY   STATUS    RESTARTS   AGE
grafana-deployment-549c685ddc-b6dq7   1/1     Running   0          83s
grafana-operator-7574bbdbc9-skdk8     1/1     Running   0          6m4s
```

### Create this for Grafana Service of type NodePort

```
cat <<'EOF' > GrafanaService.yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-svc
spec:
  type: NodePort
  ports:
  - name: grafana
    nodePort: 30200
    port: 3000
    protocol: TCP
    targetPort: grafana-http
  selector:
    app: grafana
EOF
```

Execute below command to create Grafana Service

```
kubectl create -f GrafanaService.yaml -n my-grafana-operator
```

Now get the IP Address of the VM by executing the below command:

```execute
export ip_addr=$(ifconfig eth1 | grep inet | awk '{print $2}' | cut -f2 -d:)
```

See what is your VM IP Address:

```execute
echo $ip_addr
```

Now copy the output of the above command, paste in place of `$ip_addr` in the next command and you can access the service from your browser using the below link:

```
http://$ip_addr:30200
```

You will see the Grafana page as below :