# Configuring Google Kubernetes Engine (GKE) Networking

1 hourFree

Rate Lab

## Overview

In this lab, you will create a private cluster, add an authorized network for API access to it, and then configure a network policy for Pod security.

## Objectives

In this lab, you learn how to perform the following tasks:

- Create and test a private cluster
- Configure a cluster for authorized network master access
- Configure a Cluster network policy

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

## Task 1. Create a private cluster

In this task, you create a private cluster, consider the options for how private to make it, and then compare your private cluster to your original cluster.

In a private cluster, the nodes have internal RFC 1918 IP addresses only, which ensures that their workloads are isolated from the public Internet. The nodes in a non-private cluster have external IP addresses, potentially allowing traffic to and from the internet.

### Set up a private cluster

1. On the **Navigation menu** (![8c24be286e75dbe7.png](https://cdn.qwiklabs.com/tkgw1TDgj4Q%2BYKQUW4jUFd0O5OEKlUMBRYbhlCrF0WY%3D)), click **Kubernetes Engine > Clusters**.
2. Click **Create cluster**.
3. Name the cluster `private-cluster`.
4. Select `us-central1-a` as the zone.
5. Click on **default-pool** under **NODE POOLS** section and then enter `2` in **Number of nodes** section.
6. Click on **Networking** section, select **Enable VPC-native traffic routing (uses alias IP)**.
7. In the **Networking** section, select **Private cluster** and select **Access master using its external IP address**.
8. For **Master IP Range**, enter **172.16.0.0/28**.

**Note:** This step assumes that you don't have this range assigned to a subnet. Check VPC Networks in the GCP Console, and select a different range if necessary. Behind the scenes, GCP uses VPC peering to connect the VPC of the cluster master with your default VPC network.

1. Deselect **Enable master authorized networks**.

This setting allows you to define the range of addresses that can access the cluster master externally. When this checkbox is not selected, you can access `kubectl` only from within the GCP network. In this lab, you will only access `kubectl` through the GCP network but you will modify this setting later.

1. Click **Create**.

### Inspect your cluster

1. In the Cloud Shell, enter the following command to review the details of your new cluster:

```
gcloud container clusters describe private-cluster --region us-central1-a
```

The following values appear only under the private cluster:

- privateEndpoint, an internal IP address. Nodes use this internal IP address to communicate with the cluster master.
- publicEndpoint, an external IP address. External services and administrators can use the external IP address to communicate with the cluster master.

You have several options to lock down your cluster to varying degrees:

- The whole cluster can have external access.
- The whole cluster can be private.
- The nodes can be private while the cluster master is public, and you can limit which external networks are authorized to access the cluster master.

Without public IP addresses, code running on the nodes can't access the public internet unless you configure a NAT gateway such as Cloud NAT.

You might use private clusters to provide services such as internal APIs that are meant only to be accessed by resources inside your network. For example, the resources might be private tools that only your company uses. Or they might be backend services accessed by your frontend services, and perhaps only those frontend services are accessed directly by external customers or users. In such cases, private clusters are a good way to reduce the surface area of attack for your application.

Click *Check my progress* to verify the objective.

Create a private cluster

Check my progress



## Task 2. Add an authorized network for cluster master access

After cluster creation, you might want to issue commands to your cluster from outside GCP. For example, you might decide that only your corporate network should issue commands to your cluster master. Unfortunately, you didn't specify the authorized network on cluster creation.

In this task, you add an authorized network for cluster master access.

In this task, you make the Kubernetes master API accessible to a specific range of network addresses. In a real-world use of GKE, this connection would be used by IT staff and automated processes, not end-users.

1. In the GCP Console **Navigation menu** (![8c24be286e75dbe7.png](https://cdn.qwiklabs.com/tkgw1TDgj4Q%2BYKQUW4jUFd0O5OEKlUMBRYbhlCrF0WY%3D)), click **Kubernetes Engine > Clusters**.
2. Click **private-cluster** to open the Clusters details page.
3. Click on **Edit** button.
4. In the **Master authorized networks** drop-down list, select **Enabled**.
5. Click **Add authorized network**.
6. For **Name**, type the name for the network, use `Corporate`.
7. For **Network**, type a CIDR range that you want to grant whitelisted access to your cluster master. As an example, you can use `192.168.1.0/24`.
8. Click **Done**.

Multiple networks can be added here if necessary, but no more than 50 CIDR ranges.

Outside this lab environment, a practical example might be to whitelist only the public, outside address of your corporate firewall. For example, if your corporate firewall's IP address were 8.8.8.14, you could whilelist access to 8.8.8.14/32.

1. Click **Save** at the bottom of the menu.

Click *Check my progress* to verify the objective.

Add an authorized network for cluster master access

Check my progress



## Task 3. Create a cluster network policy

In this task, you create a cluster network policy to restrict communication between the Pods. A zero-trust zone is important to prevent lateral attacks within the cluster when an intruder compromises one of the Pods.

### Create a GKE cluster

1. In Cloud Shell, type the following command to set the environment variable for the zone and cluster name.

```
export my_zone=us-central1-a
export my_cluster=standard-cluster-1
```

1. Configure kubectl tab completion in Cloud Shell.

```
source <(kubectl completion bash)
```

1. In Cloud Shell, type the following command to create a Kubernetes cluster. Note that this command adds the additional flag `--enable-network-policy` to the parameters you have used in previous labs. This flag allows this cluster to use cluster network policies.

```
gcloud container clusters create $my_cluster --num-nodes 3 --enable-ip-alias --zone $my_zone --enable-network-policy
```

1. In Cloud Shell, configure access to your cluster for the kubectl command-line tool, using the following command:

```
gcloud container clusters get-credentials $my_cluster --zone $my_zone
```

1. Run a simple web server application with the label `app=hello`, and expose the web application internally in the cluster.

```
kubectl run hello-web --labels app=hello \
  --image=gcr.io/google-samples/hello-app:1.0 --port 8080 --expose
```

1. In Cloud Shell enter the following command to clone the repository to the lab Cloud Shell.

```
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
```

1. Create a soft link as a shortcut to the working directory.

```
ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
```

1. Change to the directory that contains the sample files for this lab.

```
cd ~/ak8s/GKE_Networks/
```

### Restrict incoming traffic to Pods

A sample NetworkPolicy manifest file called `hello-allow-from-foo.yaml` has been provided for you. This manifest file defines an ingress policy that allows access to Pods labeled `app: hello` from Pods labeled `app: foo`.

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: hello-allow-from-foo
spec:
  policyTypes:
  - Ingress
  podSelector:
    matchLabels:
      app: hello
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: foo
```

1. Create an ingress policy.

```
kubectl apply -f hello-allow-from-foo.yaml
```

1. Verify that the policy was created.

```
kubectl get networkpolicy
```

**Output (Do not copy)**

```
NAME                   POD-SELECTOR   AGE
hello-allow-from-foo   app=hello      7s
```

### Validate the ingress policy

1. Run a temporary Pod called `test-1` with the label `app=foo` and get a shell in the Pod.

```
kubectl run test-1 --labels app=foo --image=alpine --restart=Never --rm --stdin --tty
```

**Note:** The kubectl switches used here in conjunction with the run command are important to note.

`--stdin` ( alternatively `-i` ) creates an interactive session attached to STDIN on the container.

`--tty` ( alternatively `-t` ) allocates a TTY for each container in the pod.

`--rm` instructs Kubernetes to treat this as a temporary Pod that will be removed as soon as it completes its startup task. As this is an interactive session it will be removed as soon as the user exits the session.

`--label` ( alternatively `-l` ) adds a set of labels to the pod.

`--restart` defines the restart policy for the Pod

1. Make a request to the hello-web:8080 endpoint to verify that the incoming traffic is allowed.

```
wget -qO- --timeout=2 http://hello-web:8080
```

**Output (Do not copy)**

```
If you don't see a command prompt, try pressing enter.
/ # wget -qO- --timeout=2 http://hello-web:8080
Hello, world!
Version: 1.0.0
Hostname: hello-web-8b44b849-k96lh
/ #
```

1. Type **exit** and press **ENTER** to leave the shell.
2. Now you will run a different Pod using the same Pod name but using a label, `app=other`, that does not match the podSelector in the active network policy. This Pod should not have the ability to access the hello-web application.

```
kubectl run test-1 --labels app=other --image=alpine --restart=Never --rm --stdin --tty
```

1. Make a request to the hello-web:8080 endpoint to verify that the incoming traffic is not allowed.

```
wget -qO- --timeout=2 http://hello-web:8080
```

The request times out.

**Output (Do not copy)**

```
If you don't see a command prompt, try pressing enter.
/ # wget -qO- --timeout=2 http://hello-web:8080
wget: download timed out
/ #
```

1. Type **exit** and press ENTER to leave the shell.

### Restrict outgoing traffic from the Pods

You can restrict outgoing (egress) traffic as you do incoming traffic. However, in order to query internal hostnames (such as hello-web) or external hostnames (such as www.example.com), you must allow DNS resolution in your egress network policies. DNS traffic occurs on port 53, using TCP and UDP protocols.

The NetworkPolicy manifest file `foo-allow-to-hello.yaml` file that has been provided for you defines a policy that permits Pods with the label `app: foo` to communicate with Pods labeled `app: hello` on any port number, and allows the Pods labeled `app: foo` to communicate to any computer on UDP port 53, which is used for DNS resolution. Without the DNS port open, you will not be able to resolve the hostnames.

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: foo-allow-to-hello
spec:
  policyTypes:
  - Egress
  podSelector:
    matchLabels:
      app: foo
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: hello
  - to:
    ports:
    - protocol: UDP
      port: 53
```

1. Create an egress policy.

```
kubectl apply -f foo-allow-to-hello.yaml
```

1. Verify that the policy was created.

```
kubectl get networkpolicy
```

**Output (Do not copy)**

```
NAME                   POD-SELECTOR   AGE
foo-allow-to-hello     app=foo        7s
hello-allow-from-foo   app=hello      5m
```

### Validate the egress policy

1. Deploy a new web application called hello-web-2 and expose it internally in the cluster.

```
kubectl run hello-web-2 --labels app=hello-2 \
  --image=gcr.io/google-samples/hello-app:1.0 --port 8080 --expose
```

1. Run a temporary Pod with the `app=foo` label and get a shell prompt inside the container.

```
kubectl run test-3 --labels app=foo --image=alpine --restart=Never --rm --stdin --tty
```

1. Verify that the Pod can establish connections to hello-web:8080.

```
wget -qO- --timeout=2 http://hello-web:8080
```

**Output (Do not copy)**

```
If you don't see a command prompt, try pressing enter.
/ # wget -qO- --timeout=2 http://hello-web:8080
Hello, world!
Version: 1.0.0
Hostname: hello-web-8b44b849-k96lh
/ #
```

1. Verify that the Pod cannot establish connections to hello-web-2:8080.

```
wget -qO- --timeout=2 http://hello-web-2:8080
```

This fails because none of the Network policies you have defined allow traffic to Pods labelled `app: hello-2`.

1. Verify that the Pod cannot establish connections to external websites, such as www.example.com.

```
wget -qO- --timeout=2 http://www.example.com
```

This fails because the network policies do not allow external http traffic (tcp port 80).

1. Type **exit** and press **ENTER** to leave the shell.

Click *Check my progress* to verify the objective.

Run web server applications

Check my progress