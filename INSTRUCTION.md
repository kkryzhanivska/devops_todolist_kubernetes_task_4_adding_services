# ToDo Application Testing Instructions

This file describes how to test the ToDo application using Kubernetes Services.

## 1. Apply Kubernetes manifests

First, create all Kubernetes resources from the `.infrastructure` directory.

```bash
kubectl apply -f .infrastructure/
```

Check that the pods are running:

```bash
kubectl get pods -n todoapp
```

Pods todoapp and todoapp-2 should have the status **_Running_**.

---

## 2. Test the application 
* ### Using ClusterIP Service from the busybox container

The ClusterIP Service can be accessed inside the cluster using the service DNS name.

Execute the following command from the busybox container:

```bash
kubectl exec -it busybox -n todoapp -- wget -qO- http://todoapp-service
```

If everything works correctly, the command will return the HTML page of the ToDo application.

You can also explicitly specify the service port:

```bash
kubectl exec -it busybox -n todoapp -- wget -qO- http://todoapp-service:80
```


* ### Using Service port-forward
Run port-forward for the ClusterIP Service:

```bash
kubectl port-forward service/todoapp-service 8080:80 -n todoapp
```

After that open the application in your browser:

http://localhost:8080

---

## 3. Access the application using NodePort Service

Check the NodePort Service:
```bash
kubectl get svc -n todoapp
```

You should see something similar to:

`todoapp-nodeport-service   NodePort   80:30000/TCP`

Open the application in your browser using the NodePort:

http://localhost:30000

---

## Useful verification commands
_Check services:_
```bash
kubectl get svc -n todoapp
```

_Check service endpoints (should contain two pod IP addresses):_
```bash
kubectl get endpoints -n todoapp
```

_Check logs if needed:_
```bash
kubectl logs todoapp -n todoapp
kubectl logs todoapp-2 -n todoapp
```