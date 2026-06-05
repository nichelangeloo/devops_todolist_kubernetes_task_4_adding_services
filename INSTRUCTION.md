# ToDo Application Testing Guide

## Prerequisites

Verify that all resources are running:

```bash
kubectl get pods -n todoapp
kubectl get svc -n todoapp
```

Expected resources:

- todoapp
- todoapp-1
- busybox
- todoapp-service (ClusterIP)
- todoapp-nodeport-service (NodePort)

---

## Test ClusterIP Service Using BusyBox

Connect to the BusyBox pod:

```bash
kubectl exec -it busybox -n todoapp -- sh
```

Call the application through the ClusterIP Service DNS name:

```bash
curl http://todoapp-service
```

or

```bash
curl http://todoapp-service:80
```

Expected result:

- The ToDo application returns a valid HTTP response.
- Requests are balanced between both application pods (`todoapp` and `todoapp-1`).

To verify service endpoints:

```bash
kubectl get endpoints todoapp-service -n todoapp
```

Expected result:

- Two pod IP addresses are listed.

---

## Test Application Using Port Forward

Forward local port 8080 to the ClusterIP Service:

```bash
kubectl port-forward svc/todoapp-service 8080:80 -n todoapp
```

Open a browser and navigate to:

```text
http://localhost:8080
```

Expected result:

- The ToDo application is accessible from the local machine.

---

## Test Application Using NodePort Service

Display the Node IP address:

```bash
kubectl get nodes -o wide
```

Find the NodePort:

```bash
kubectl get svc todoapp-nodeport-service -n todoapp
```

Expected output:

```text
PORT(S)
80:30008/TCP
```

Open a browser and navigate to:

```text
http://<NODE_IP>:30008
```

Example:

```text
http://192.168.1.100:30008
```

Expected result:

- The ToDo application is accessible through the NodePort Service.

---

## Verify Service Configuration

Check ClusterIP Service:

```bash
kubectl describe svc todoapp-service -n todoapp
```

Check NodePort Service:

```bash
kubectl describe svc todoapp-nodeport-service -n todoapp
```

Verify that both services select pods with the label:

```yaml
app: todolist
```
