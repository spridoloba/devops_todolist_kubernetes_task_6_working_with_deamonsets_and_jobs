Both manifests belong to the mateapp namespace.
If it doesn’t exist yet, create it:

kubectl create namespace mateapp


This DaemonSet runs a container based on busyboxplus:curl,
which performs a curl request to the todoapp ClusterIP service every 5 seconds.

Key configuration details:

Namespace: mateapp

Image: busyboxplus:curl

Command: Executes an infinite loop that runs curl every 5 seconds.

Resources:

Requests: cpu: 50m, memory: 64Mi

Limits: cpu: 100m, memory: 128Mi

Deployment steps:
kubectl apply -f .infrastructure/daemonset.yml



Verification

Check that pods are running on all nodes:

kubectl get pods -n mateapp -o wide


View logs from any pod:

kubectl logs -n mateapp <pod-name> -f


Expected log output:

Running curl
<html> ... response from todoapp ... </html>


CronJob Deployment - cronjob.yml

This CronJob runs every 4 minutes and performs a single curl command to check the health endpoint of the todoapp service.

Key configuration details:

Namespace: mateapp

Schedule: "*/4 * * * *" (every 4 minutes)

Image: busyboxplus:curl

Command: Executes curl once per job run

Resources:

Requests: cpu: 50m, memory: 64Mi

Limits: cpu: 100m, memory: 128Mi

Snippet:

cronjob

curl -s http://todoapp-service.todoapp.svc.cluster.local/api/health

 Deployment steps
kubectl apply -f .infrastructure/cronjob.yml



Validation and Logs:

kubectl get cronjobs -n mateapp
kubectl get jobs -n mateapp


To view logs of the latest job:

kubectl logs -n mateapp job/todoapp-curl-cronjob



Check DaemonSet Pods

A DaemonSet runs one Pod per node.
So you check logs from one (or all) of its Pods.

1List all Pods of the DaemonSet:

kubectl get pods -l app=<label-name> -n <namespace>


2Get logs from a specific Pod:

kubectl logs <pod-name> -n <namespace>


Or get logs from all Pods managed by the DaemonSet:

kubectl logs daemonset/<daemonset-name> -n <namespace>



Logs from a CronJob

A CronJob creates a Job, and that Job creates one or more Pods.
You check logs from the Pod created by the latest Job.

1List Jobs:

kubectl get jobs -n <namespace>


2List Pods created by a specific Job:

kubectl get pods -l job-name=<job-name> -n <namespace>


Show logs from the Pod:

kubectl logs <pod-name> -n <namespace>

