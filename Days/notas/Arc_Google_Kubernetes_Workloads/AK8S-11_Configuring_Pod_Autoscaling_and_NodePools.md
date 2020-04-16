# AK8S-11 Configuring Pod Autoscaling and NodePools

1 hourFree

Rate Lab

## Overview

In this lab, you set up an application in Google Kubernetes Engine (GKE), and then use a HorizontalPodAutoscaler to autoscale the web application. You then work with multiple node pools of different types, and you apply taints and tolerations to control the scheduling of Pods with respect to the underlying node pool.

## Objectives

In this lab, you learn how to perform the following tasks:

- Configure autoscaling and HorizontalPodAutoscaler
- Add a node pool and configure taints on the nodes for Pod anti-affinity
- Configure an exception for the node taint by adding a toleration to a Pod's manifest

## Task 0. Lab Setup

### Access Qwiklabs

#### Before you click the Start Lab button

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long Cloud resources will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access the Google Cloud Platform for the duration of the lab.

#### What you need

To complete this lab, you need:

- Access to a standard internet browser (Chrome browser recommended).
- Time to complete the lab.

***Note:\*** If you already have your own personal GCP account or project, do not use it for this lab.

After you complete the initial sign-in steps, the project dashboard appears.

### Activate Google Cloud Shell

Google Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Google Cloud Shell provides command-line access to your GCP resources.

1. In GCP console, on the top right toolbar, click the Open Cloud Shell button.

   ![Cloud Shell icon](https://cdn.qwiklabs.com/vdY5e%2Fan9ZGXw5a%2FZMb1agpXhRGozsOadHURcR8thAQ%3D)

2. Click **Continue**. ![cloudshell_continue.png](https://cdn.qwiklabs.com/lr3PBRjWIrJ%2BMQnE8kCkOnRQQVgJnWSg4UWk16f0s%2FA%3D)

It takes a few moments to provision and connect to the environment. When you are connected, you are already authenticated, and the project is set to your *PROJECT_ID*. For example:

![Cloud Shell Terminal](https://cdn.qwiklabs.com/hmMK0W41Txk%2B20bQyuDP9g60vCdBajIS%2B52iI2f4bYk%3D)

**gcloud** is the command-line tool for Google Cloud Platform. It comes pre-installed on Cloud Shell and supports tab-completion.

You can list the active account name with this command:

```
gcloud auth list
```

Output:

```output
Credentialed accounts:
 - <myaccount>@<mydomain>.com (active)
```

Example output:

```Output
Credentialed accounts:
 - google1623327_student@qwiklabs.net
```

You can list the project ID with this command:

```
gcloud config list project
```

Output:

```output
[core]
project = <project_ID>
```

Example output:

```Output
[core]
project = qwiklabs-gcp-44776a13dea667a6
```

Full documentation of **gcloud** is available on [Google Cloud gcloud Overview ](https://cloud.google.com/sdk/gcloud).

## Task 1. Deploy a GKE cluster and sample workload

In this task, you create a GKE cluster and a deployment manifest for a set of Pods within the cluster that you will then use to test DNS resolution of Pod and service names.

### Create a GKE cluster

1. In Cloud Shell, type the following command to create environment variables for the GCP zone and cluster name that will be used to create the cluster for this lab.

```
export my_zone=us-central1-a
export my_cluster=standard-cluster-1
```

1. Configure tab completion for the kubectl command-line tool.

```
source <(kubectl completion bash)
```

1. Create a VPC-native Kubernetes cluster.

```
gcloud container clusters create $my_cluster \
   --num-nodes 2 --enable-ip-alias --zone $my_zone
```

1. Configure access to your cluster for kubectl:

```
gcloud container clusters get-credentials $my_cluster --zone $my_zone
```

### Deploy a sample web application to your GKE cluster

You will deploy a sample application to your cluster using the `web.yaml` deployment file that has been created for you:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      run: web
  template:
    metadata:
      labels:
        run: web
    spec:
      containers:
      - image: gcr.io/google-samples/hello-app:1.0
        name: web
        ports:
        - containerPort: 8080
          protocol: TCP
```

This manifest creates a deployment using a sample web application container image that listens on an HTTP server on port 8080.

1. In Cloud Shell enter the following command to clone the repository to the lab Cloud Shell.

```
git clone https://github.com/GoogleCloudPlatformTraining/training-data-analyst
```

1. Change to the directory that contains the sample files for this lab.

```
cd ~/training-data-analyst/courses/ak8s/11_Autoscaling/
```

1. To create a deployment from this file, execute the following command:

```
kubectl create -f web.yaml --save-config
```

1. Create a service resource of type NodePort on port 8080 for the web deployment.

```
kubectl expose deployment web --target-port=8080 --type=NodePort
```

1. Verify that the service was created and that a node port was allocated:

```
kubectl get service web
```

Your IP address and port number might be different from the example output.

**Output (do not copy)**

```
NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
web       NodePort   10.11.246.185   <none>        8080:32056/TCP   12s
```

Click *Check my progress* to verify the objective.

Deploy a sample web application to GKE cluster

Check my progress



## Task 2. Configure autoscaling on the cluster

In this task, you configure the cluster to automatically scale the sample application that you deployed earlier.

### Configure autoscaling

1. Get the list of deployments to determine whether your sample web application is still running.

```
kubectl get deployment
```

The output should look like the example.

**Output (do not copy)**

```
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
web       1         1         1            1           35m
```

If the web deployment of your application is not displayed, return to task 1 and redeploy it to the cluster.

1. To configure your sample application for autoscaling (and to set the maximum number of replicas to four and the minimum to one, with a CPU utilization target of 1%), execute the following command:

```
kubectl autoscale deployment web --max 4 --min 1 --cpu-percent 1
```

When you use `kubectl autoscale`, you specify a maximum and minimum number of replicas for your application, as well as a CPU utilization target.

1. Get the list of deployments to verify that there is still only one deployment of the web application.

```
kubectl get deployment
```

**Output (do not copy)**

```
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
web       1         1         1            1           37m
```

### Inspect the HorizontalPodAutoscaler object

The `kubectl autoscale` command you used in the previous task creates a `HorizontalPodAutoscaler` object that targets a specified resource, called the scale target, and scales it as needed. The autoscaler periodically adjusts the number of replicas of the scale target to match the average CPU utilization that you specify when creating the autoscaler.

1. To get the list of HorizontalPodAutoscaler resources, execute the following command:

```
kubectl get hpa
```

The output should look like the example.

**Output (do not copy)**

```
NAME      REFERENCE        TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
web       Deployment/web   0%/1%     1         4         1          1m
```

1. To inspect the configuration of `HorizontalPodAutoscaler` in YAML form, execute the following command:

```
kubectl describe horizontalpodautoscaler web
```

The output should look like the example.

**Output (do not copy)**

```
Name:                                       web
Namespace:                                  default
Labels:                                     <none>
Annotations:                                <none>
CreationTimestamp:                          Tue, 13 Nov 2018...
Reference:                                  Deployment/web
Metrics:                                    ( current / target )
  resource cpu on pods  (as a %...equest):  0% (0) / 1%
Min replicas:                               1
Max replicas:                               4
Conditions:
  Type            Status  Reason            Message
  ----            ------  ------            -------
  AbleToScale     True    ReadyForNewScale  the last scale time [...]
  ScalingActive   True    ValidMetricFound  the HPA was able to [...]
  ScalingLimited  True    TooFewReplicas    the desired replica [...]
Events:           <none>
```

1. To view the configuration of HorizontalPodAutoscaler in YAML form, execute the following command:

```
kubectl get horizontalpodautoscaler web -o yaml
```

The output should look like the example.

**Output (do not copy)**

```
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  annotations:
    autoscaling.alpha.kubernetes.io/conditions:  [...]   autoscaling.alpha.kubernetes.io/current-metrics: [...]
  creationTimestamp: 2018-11-14T02:59:28Z
  name: web
  namespace: default
  resourceVersion: "14588"
  selfLink: /apis/autoscaling/v1/namespaces/[...]
spec:
  maxReplicas: 4
  minReplicas: 1
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: web
  targetCPUUtilizationPercentage: 1
status:
  currentCPUUtilizationPercentage: 0
  currentReplicas: 1
  desiredReplicas: 1
```

### Test the autoscale configuration

You need to create a heavy load on the web application to force it to scale out. You create a configuration file that defines a deployment of four containers that run an infinite loop of HTTP queries against the sample application web server.

You create the load on your web application by deploying the loadgen application using the `loadgen.yam`l file that has been provided for you.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: loadgen
spec:
  replicas: 4
  selector:
    matchLabels:
      app: loadgen
  template:
    metadata:
      labels:
        app: loadgen
    spec:
      containers:
      - name: loadgen
        image: k8s.gcr.io/busybox
        args:
        - /bin/sh
        - -c
        - while true; do wget -q -O- http://web:8080; done
```

1. To deploy this container, execute the following command:

```
kubectl apply -f loadgen.yaml
```

After you deploy this manifest, the web Pod should begin to scale.

Click *Check my progress* to verify the objective.

Deploying the loadgen application

Check my progress



1. Get the list of deployments to verify that the load generator is running.

```
kubectl get deployment
```

The output should look like the example.

**Output (do not copy)**

```
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
loadgen   4         4         4            4           5s
web       1         1         1            1           39m
```

1. Inspect HorizontalPodAutoscaler.

```
kubectl get hpa
```

Once the loadgen Pod starts to generate traffic, the web deployment CPU utilization begins to increase. In the example output, the targets are now at 35% CPU utilization compared to the 1% CPU threshold.

**Output (do not copy)**

```
NAME      REFERENCE        TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
web       Deployment/web   35%/1%    1         4         1          8m
```

1. After a few minutes, inspect the HorizontalPodAutoscaler again.

```
kubectl get hpa
```

The autoscaler has increased the web deployment to four replicas.

**Output (do not copy)**

```
NAME      REFERENCE        TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
web       Deployment/web   88%/1%   1         4         4          9m
```

1. To stop the load on the web application, scale the loadgen deployment to zero replicas.

```
kubectl scale deployment loadgen --replicas 0
```

1. Get the list of deployments to verify that loadgen has scaled down.

```
kubectl get deployment
```

The loadgen deployment should have zero replicas.

**Output (do not copy)**

```
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
loadgen   0         0         0            0           2m
web       4         4         4            4           41m
```

1. Wait 2 to 3 minutes, and then get the list of deployments again to verify that the web application has scaled down to the minimum value of 1 replica that you configured when you deployed the autoscaler.

```
kubectl get deployment
```

You should now have one deployment of the web application.

**Output (do not copy)**

```
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
loadgen   0         0         0            0           4m
web       1         1         1            1           43m
```

## Task 3. Manage node pools

In this task, you create a new pool of nodes using preemptible instances, and then you constrain the web deployment to run only on the preemptible nodes.

### Add a node pool

1. To deploy a new node pool with three preemptible VM instances, execute the following command:

```
gcloud container node-pools create "temp-pool-1" \
--cluster=$my_cluster --zone=$my_zone \
--num-nodes "2" --node-labels=temp=true --preemptible
```

If you receive an error that no preemptible instances are available you can remove the `--preemptible` option to proceed with the lab.

1. Get the list of nodes to verify that the new nodes are ready.

```
kubectl get nodes
```

You should now have `4` nodes.

Your names will be different from the example output.

**Output (do not copy)**

```
NAME                           STATUS    ROLES     AGE       VERSION
gke-cluster1-default-pool...f1 Ready     <none>    1h        v1.11.9-gke.3
gke-cluster1-default-pool...2b Ready     <none>    1h        v1.11.8-gke.6
gke-cluster1-temp-pool-1-...2g Ready     <none>    7m        v1.11.8-gke.6
gke-cluster1-temp-pool-1-...z7 Ready     <none>    7m        v1.11.8-gke.6
```

All the nodes that you added have the `temp=true` label because you set that label when you created the node-pool. This label makes it easier to locate and configure these nodes.

1. To list only the nodes with the `temp=true` label, execute the following command:

```
kubectl get nodes -l temp=true
```

You should see only the two nodes that you added.

Your names will be different from the example output.

**Output (do not copy)**

```
NAME                           STATUS    ROLES     AGE       VERSION
gke-cluster1-temp-pool-1-...2g Ready     <none>    7m        v1.11.8-gke.6
gke-cluster1-temp-pool-1-...z7 Ready     <none>    7m        v1.11.8-gke.6
```

### Control scheduling with taints and tolerations

To prevent the scheduler from running a Pod on the temporary nodes, you add a taint to each of the nodes in the temp pool. Taints are implemented as a key-value pair with an effect (such as NoExecute) that determines whether Pods can run on a certain node. Only nodes that are configured to tolerate the key-value of the taint are scheduled to run on these nodes.

1. To add a taint to each of the newly created nodes, execute the following command.

You can use the `temp=true` label to apply this change across all the new nodes simultaneously.

```
kubectl taint node -l temp=true nodetype=preemptible:NoExecute
```

To allow application Pods to execute on these tainted nodes, you must add a tolerations key to the deployment configuration.

1. Edit the `web.yaml` file to add the following key in the template's `spec` section:

```
tolerations:
- key: "nodetype"
  operator: Equal
  value: "preemptible"
```

The spec section of the file should look like the example.

```
...
    spec:
      tolerations:
      - key: "nodetype"
        operator: Equal
        value: "preemptible"
      containers:
      - image: gcr.io/google-samples/hello-app:1.0
        name: web
        ports:
        - containerPort: 8080
          protocol: TCP
```

1. To force the web deployment to use the new node-pool add a nodeSelector key in the template's `spec` section. This is parallel to the tolerations key you just added.

```
     nodeSelector:
        temp: "true"
```

**Note:** GKE adds a custom label to each node called `cloud.google.com/gke-nodepool` that contains the name of the node-pool that the node belongs to. This key can also be used as part of a nodeSelector to ensure Pods are only deployed to suitable nodes.

The full web.yaml deployment should now look as follows.

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      run: web
  template:
    metadata:
      labels:
        run: web
    spec:
      tolerations:
      - key: "nodetype"
        operator: Equal
        value: "preemptible"
      nodeSelector:
        temp: "true"
      containers:
      - image: gcr.io/google-samples/hello-app:1.0
        name: web
        ports:
        - containerPort: 8080
          protocol: TCP
```

1. To apply this change, execute the following command:

```
kubectl apply -f web.yaml
```

If you have problems editing this file successfully you can use the pre-prepared sample file called `web-tolerations.yaml` instead.

Click *Check my progress* to verify the objective.

Manage node pools

Check my progress



1. Get the list of Pods.

```
kubectl get pods
```

Your names might be different from the example output.

**Output (do not copy)**

```
NAME                   READY     STATUS    RESTARTS   AGE
web-7cb566bccd-pkfst   1/1       Running   0          1m
```

1. To confirm the change, inspect the running web Pod(s) using the following command

```
kubectl describe pods -l run=web
```

A `Tolerations` section with `nodetype=preemptible` in the list should appear near the bottom of the (truncated) output.

**Output (do not copy)**

```
<SNIP>

Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 nodetype=preemptible
Events:

<SNIP>
```

The output confirms that the Pods will tolerate the taint value on the new preemptible nodes, and thus that they can be scheduled to execute on those nodes.

1. To force the web application to scale out again scale the loadgen deployment back to four replicas.

```
kubectl scale deployment loadgen --replicas 4
```

You could scale just the web application directly but using the loadgen app will allow you to see how the different taint, toleration and nodeSelector settings that apply to the web and loadgen applications affect which nodes they are scheduled on.

1. Get the list of Pods using thewide output format to show the nodes running the Pods

```
kubectl get pods -o wide
```

This shows that the loadgen app is running only on `default-pool` nodes while the web app is running only the preemptible nodes in `temp-pool-1.`

The taint setting prevents Pods from running on the preemptible nodes so the loadgen application only runs on the default pool. The toleration setting allows the web application to run on the preemptible nodes and the nodeSelector forces the web application Pods to run on those nodes.

```
NAME        READY STATUS    [...]         NODE
Loadgen-x0  1/1   Running   [...]         gke-xx-default-pool-y0
loadgen-x1  1/1   Running   [...]         gke-xx-default-pool-y2
loadgen-x3  1/1   Running   [...]         gke-xx-default-pool-y3
loadgen-x4  1/1   Running   [...]         gke-xx-default-pool-y4
web-x1      1/1   Running   [...]         gke-xx-temp-pool-1-z1
web-x2      1/1   Running   [...]         gke-xx-temp-pool-1-z2
web-x3      1/1   Running   [...]         gke-xx-temp-pool-1-z3
web-x4      1/1   Running   [...]         gke-xx-temp-pool-1-z4
```