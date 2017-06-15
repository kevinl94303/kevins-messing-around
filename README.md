# Rolling Update on Kubernetes 1.6

Most of the steps are identical to those found at https://kubernetes.io/docs/tasks/manage-daemon/update-daemon-set/

```
updateStrategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
  minReadySeconds: 30
```
