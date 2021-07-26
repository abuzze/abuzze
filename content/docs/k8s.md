---
title: "K8S Cheatsheet"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---
# K8S cheatsheet
## Useful tools

Colorize the output of kubectl. Use [kubecolor](https://github.com/dty1er/kubecolor) instead.

    brew install dty1er/tap/kubecolor

htop for k8s. Just run [k9s](https://k9scli.io/)

    brew install derailed/k9s/k9s

Faster context switching between clusters with [kubectx](https://github.com/ahmetb/kubectx)

    brew install kubectx

## Install
### Install on bare metal (Ubuntu)
Used [guide](https://linuxconfig.org/how-to-install-kubernetes-on-ubuntu-20-04-focal-fossa-linux).
Also a good source is [this](https://jhooq.com/kubernetes-index/)

    $ sudo apt update
    $ sudo apt install docker.io apt-transport-https curl -y
    $ sudo systemctl start docker
    $ sudo systemctl enable docker
    $ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
    $ sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
    $ sudo apt install kubeadm kubelet kubectl kubernetes-cni -y
    $ sudo swapoff -a

Disable the swapfile permanently

    $ sudo vim /etc/fstab

Rename the VMs

    $ sudo hostnamectl set-hostname kubernetes-master
    $ sudo hostnamectl set-hostname kubernetes-worker

    kubernetes-master:~$ sudo kubeadm init --pod-network-cidr=10.244.0.0/16
    kubernetes-master:~$ mkdir -p $HOME/.kube
    kubernetes-master:~$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    kubernetes-master:~$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
    kubernetes-master:~$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
    kubernetes-master:~$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/k8s-manifests/kube-flannel-rbac.yml
    kubernetes-master:~$ kubectl get pods --all-namespaces

    kubernetes-worker:~$ sudo kubeadm join 192.168.1.220:6443 --token 1exb8s.2t4k3b5syfc3jfmo --discovery-token-ca-cert-hash    sha256:72ad481cee4918cf2314738419356c9a402fb609263adad48c13797d0cba2341
    kubernetes-master:~$ kubectl get nodes

To join a node at a later print, you can print the join code in the master. Don't forget to add sudo to the kubeadm command or you will
end up [here](https://jhooq.com/kubernetes-error-execution-phase-preflight-preflight/).

    kubeadm token create --print-join-command

Check Component status

    $ kubectl get cs
    NAME                 STATUS    MESSAGE             ERROR
    scheduler            Healthy   ok
    controller-manager   Healthy   ok
    etcd-0               Healthy   {"health":"true"}

If the controller manager is unhealty >  comment the line - --port=0 in  /etc/kubernetes/manifests/kube-controller-manager.yaml and restart k8s.

    systemctl restart kubelet

### MetalLB

To access pods, you need a load balancer in front. MetalLB can be created like a service and will automatically assign one
of the defined ip to this service. Example:

    kubectl expose pod kubia --type=LoadBalancer --name kubia-http --target-port=8080 --port=80

Target-port is the app, the app listens. Port is the outside port of the metalLB.

Check the "external ip" via

    kubectl get svc
    kubectl describe kubia-http

The pod, rc or deployment must have a label. You can add one with this command:

    kubectl label pod kubia-manual run=kubia-manual

Read the MetalLB [Guide](https://metallb.universe.tf/installation/) first.

    kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/namespace.yaml
    kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/metallb.yaml

Change the iprange for the LB in the config.yml
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 192.168.178.50-192.168.178.100
```
Apply the config

    kubectl apply -f config.yml

### Port-Forward

For debugging a port forward can be done, even on the client. This would forward traffic from the client,
where the command is run on port 8888 to the pod in port 8080.


    kubectl port-forward kubia-manual 8888:8080


### Labels

Get all labels:

    kubectl get po --show-labels

Get specific labels

    kubectl get po -L env

Get all pod with a specific label value

    kubectl get pod -l run=kubia

Get all pod without a specific label

    kubectl get pod -l '!run'

Get all pod without a specific label and value

    kubectl get pod -l 'run!=kubia'

Get all pod with a selector

    kubectl get pod -l 'run in (test,dev,kubia)'

Get all pod without a selector

    kubectl get pod -l 'run notin (test,dev,kubia)'

Add a label to a pod

    kubectl label po kubia-manual creation_method=manual


To use labels and nodeSelectors to schedule pods on a specific not use this the the pods config.

    kubectl label node node3 speed=slow

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: kubia-gpu
spec:
    nodeSelector:
    speed: "slow"
    containers:
    - image: luksa/kubia
    name: kubia
```

### Namespaces

To create a new namespace:

    kubectl create namespace custom-namespace

New namespace via yaml:
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: custom-namespace
```

Deleting almost all resources in a namespace (pods,rc, service, etc). Does not delete secrets.

    kubectl delete all --all

### Probes

#### Liveness Probe

A liveness probe checks via http, tcp or exec check if the container is still working and restarts it, if the probe
fails.

```yaml
spec:
  containers:
  - image: luksa/kubia-unhealthy
    name: kubia
    livenessProbe:
       httpGet:
       path: /
       port: 8080
       initialDelaySeconds: 15 ## set initial delay to prevent an unwanted restart when the container is created
```

Check if the pod was restarted, because the probe failed. Exit Code 137 and 143 means killed externally.

    kubectl describe pod <podname>

```yaml
    State:          Running
      Started:      Sun, 04 Jul 2021 10:52:43 +0200
    Last State:     Terminated
      Reason:       Error
      Exit Code:    137
      Started:      Sun, 04 Jul 2021 10:50:53 +0200
      Finished:     Sun, 04 Jul 2021 10:52:41 +0200
```

### ReplicaController, ReplicaSets, DaemonSets, Deployments

The ReplicaController is deprecated and can run any number of pods that match a label. The ReplicaSets
is the successor and can run any number of pods, with different labels. The pods are run randomly and will
be moved to the next node after 5 minutes if a nodes is down. The DaemonSets ensures that one
pod is run on each node.

### Jobs, CronJob

#### Jobs

Run once tasks. RestartPolicy must be set or the default will restart the job.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: batch-job
spec:
  template:
    metadata:
      labels:
        app: batch-job
    spec:
      # Default is Always, which would restart the job. Switch to Never or OnFailure
      restartPolicy: OnFailure
      containers:
      - name: main
        image: luksa/batch-job
```

#### CronJobs

Running a pod at a scheduled time. Cronjobs can overlap.

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: batch-job-every-fifteen-minutes
spec:
  schedule: "0,15,30,45 * * * *"
  jobTemplate:
    spec:
      template:
        metadata:
          labels:
            app: periodic-batch-job
        spec:
          restartPolicy: OnFailure
          containers:
          - name: main
            image: luksa/batch-job
```

### Ingress

#### NGINX Ingress

[Baremetal Instructions](https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-manifests/)

Install the Ingress Controller first and then point the LB to the ingress.

Investigate if this is working https://vincentlauzon.com/2020/02/11/ingress-rules-in-different-kubernetes-namespaces