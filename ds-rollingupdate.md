# DaemonSet Rolling Update on Kubernetes 1.6

Most of the steps are identical to those found at https://kubernetes.io/docs/tasks/manage-daemon/update-daemon-set/
To set the update strategy to Rolling Update on Kubernetes ver 1.5 or earlier, certain steps can be taken to avoid pods restarting and updating immediately upon setting the update scheme. These steps can be found in the documentation above. 

# To get the current update strategy

Get name of daemonset with
```
kubectl get ds -n <namespace-name>
```
Find current update strategy of daemonset with
```
kubectl get ds/<daemonset-name> -n <namespace-name> -o go-template='{{.spec.updateStrategy.type}}{{"\n"}}'
```
The default result of this if the strategy has not been set is "OnDelete"

# To set the update strategy to RollingUpdate

Find the compose file (yaml) of the deployment. Under spec, add the following:

```
updateStrategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
  minReadySeconds: 30
```
It is critical that the minReadySeconds not be 0, because there needs to be a time between the pods restarting to allow the new pods to be updated with the policy of the old pods, so that this information is not lost during updates. 

To apply the change, enter:
```
kubectl apply -f <name of compose file>.yaml
```
To view the status of the update, enter:
```
kubectl rollout status -n <namespace-name> ds/<daemonset-name> 
```
Any future apply commands will be executed through a RollingUpdate strategy. 

# Defining Readiness

It may be possible to define the readiness of the pods, in order to have more control of when rolling update triggers the next pod to restart. Readiness probe documentation can be found at https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/
