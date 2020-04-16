# Working with Google Kubernetes Engine Secrets and ConfigMaps

1 hourFree

Rate Lab

## Overview

In this lab, you set up configuration information, both encrypted and unencrypted. Encrypted configuration information is stored as secrets. Unencrypted configuration information is stored as ConfigMaps. This approach avoids hard coding such information into code bases. Credentials (like API keys) that belong in secrets should never travel inside code repositories like GitHub (unless they are encrypted before going in, but even then it is better to keep them separate).

## Objectives

In this lab, you learn how to perform the following tasks:

- Create secrets by using the `kubectl` command and manifest files
- Create ConfigMaps by using the `kubectl` command and manifest files
- Consume secrets in containers by using environment variables or mounted volumes
- Consume ConfigMaps in containers by using environment variables or mounted volumes

## Task 0. Lab Setup

### **Access Qwiklabs**

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

## Task 1. Work with Secrets

In this task, you authenticate containers with Google Cloud Platform (GCP) in order to access GCP services. You set up a Cloud Pub/Sub topic and subscription, try to access the Cloud Pub/Sub topic from a container running in GKE, and see that the access request fails. To properly access the pub/sub topic, you create a service account with credentials, and pass those credentials through Kubernetes Secrets.

### **Prepare a service account with no permissions**

1. In the GCP Console, on the **Navigation menu** (![9a951fa6d60a98a5.png](https://cdn.qwiklabs.com/fXo8j8i%2FKJoMkZ7FeHUYU7Vvu2PQfFZtFbLISSnYEaY%3D)), click **IAM & admin** > **Service accounts**.
2. Click **+ Create Service Account**.
3. In the **Service Account Name** text box, enter `no-permissions` and then click **Create**.
4. Click **Continue** and then click **Done**.
5. Find the **no-permissions** service account in the list, and copy the email address associated with it to a text file for later use.

### **Create a GKE cluster**

When you create this cluster you will specify the service account you created earlier. To illustrate the need for service accounts with proper permissions, that service account has no permissions to any other GCP services, and therefore will be unable to connect to the Cloud Pub/Sub test application you will later deploy. You will fix this later in the lab.

1. In Cloud Shell, type the following command to create environment variables for the GCP zone and cluster name that will be used to create the cluster for this lab.

```
export my_zone=us-central1-a
export my_cluster=standard-cluster-1
```

1. Configure tab completion for the kubectl command-line tool.

```
source <(kubectl completion bash)
```

1. In Cloud Shell, type the following command to set the environment variable for the service account name, where`[MY-SERVICE-ACCOUNT-EMAIL]` is the email address of the service account that you created earlier.

```
export my_service_account=[MY-SERVICE-ACCOUNT-EMAIL]
```

**Example (Do not copy):**

```
export my_service_account=no-permissions@qwiklabs-gcp-4ee5983fdd675469.iam.gserviceaccount.com
```

1. In Cloud Shell, type the following command to create a Kubernetes cluster.

```
gcloud container clusters create $my_cluster \
  --num-nodes 2 --zone $my_zone \
  --service-account=$my_service_account
```

1. Configure access to your cluster for kubectl:

```
gcloud container clusters get-credentials $my_cluster --zone $my_zone
```

### **Set up Cloud Pub/Sub and deploy an application to read from the topic**

1. In Cloud Shell, type the following command to set the environment variables for the pub/sub components.

```
export my_pubsub_topic=echo
export my_pubsub_subscription=echo-read
```

1. To create a Cloud Pub/Sub topic named echo and a subscription named echo-read that is associated with that topic, execute the following `gcloud` commands:

```
gcloud pubsub topics create $my_pubsub_topic
gcloud pubsub subscriptions create $my_pubsub_subscription \
 --topic=$my_pubsub_topic
```

### **Deploy an application to read from Cloud Pub/Sub topics**

You create a deployment with a container that can read from Cloud Pub/Sub topics. Since specific permissions are required to subscribe to, and read from, Cloud Pub/Sub topics this container needs to be provided with credentials in order to successfully connect to Cloud Pub/Sub.

The Deployment manifest file called `pubsub.yaml` is provided for you.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pubsub
spec:
  selector:
    matchLabels:
      app: pubsub
  template:
    metadata:
      labels:
        app: pubsub
    spec:
      containers:
      - name: subscriber
        image: gcr.io/google-samples/pubsub-sample:v1
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
cd ~/ak8s/Secrets/
```

1. Deploy the application.

```
kubectl apply -f pubsub.yaml
```

1. After the application is deployed, query the Pods by executing the following command:

```
kubectl get pods -l app=pubsub
```

This command filters the list of Pods, only including those that have a matching label of `app: pubsub`.

**Output (Do not copy):**

```
NAME                      READY     STATUS              RESTARTS   AGE
pubsub-74d4d96ddb-lqkt8   0/1       CrashLoopBackOff   0          6s
```

1. Wait for about a minute. The application is starting, but it will not succeed.
2. List the Pods again:

```
kubectl get pods -l app=pubsub
```

**Output (Do not copy):**

```
NAME                      READY     STATUS    RESTARTS   AGE
pubsub-74d4d96ddb-lqkt8   0/1       Error     4          2m
```

Notice the status of the Pod. It has an error and has restarted several times.

1. To inspect the logs from the Pod, execute the following command:

```
kubectl logs -l app=pubsub
```

The error message displayed at the end of the log indicates that the application doesn't have permissions to query the Cloud Pub/Sub service.

```
StatusCode.PERMISSION_DENIED, User not authorized to perform this action.
```

### **Create service account credentials**

You will now create a new service account and grant it access to the pub/sub subscription that the test application is attempting to use. Instead of changing the service account of the GKE cluster nodes, you will generate a JSON key for the service account, and then securely pass the JSON key to the Pod via Kubernetes Secrets.

1. In the GCP Console, on the **Navigation menu**, click **IAM & admin** > **Service Accounts**.
2. Click **+ Create Service Account**.
3. In the **Service Account Name** text box, enter `pubsub-app` and then click **Create**.
4. In the **Role** drop-down list, select **Pub/Sub > Pub/Sub Subscriber**.
5. Confirm the role is listed, and then click **Continue.**
6. Click **+ Create Key**.
7. Select **JSON** as the key type, and then click **Create**.
8. A JSON key file containing the credentials of the service account will download to your computer. You can see the file in the download bar at the bottom of your screen. You will use this key file to configure the sample application to authenticate to Cloud Pub/Sub API.
9. Click **Close** and then click **Done**.
10. On your hard drive, locate the JSON key that you just downloaded and rename the file to `credentials.json`.

### **Import credentials as a secret**

1. In Cloud Shell, click the three dots ( ![b4af82f98f85f64f.png](https://cdn.qwiklabs.com/5OEwfJEfsSg8zDSsYjmqhsK7fRiAcDrW50FJ0Axw%2Fk8%3D)) icon in the Cloud Shell toolbar to display further options.

![8c6d3554108c8912.png](https://cdn.qwiklabs.com/MmUXLL7zLY0GNWCC%2FdOCrczwKSgrYKLbMxmyDanWlfQ%3D)

1. Click **Upload file** and upload the `credentials.json` file from your local machine to the Cloud Shell VM.
2. In Cloud Shell, enter the following command to confirm that the file was uploaded.

```
ls ~/
```

You should see the credentials file that has been uploaded along with the lab files directory you cloned earlier.

1. To save the `credentials.json` key file to a Kubernetes Secret named `pubsub-key` execute the following command.

```
kubectl create secret generic pubsub-key \
 --from-file=key.json=$HOME/credentials.json
```

This command creates a secret named `pubsub-key` that has a `key.json` value containing the contents of the private key that you downloaded from the GCP Console.

1. Remove the `credentials.json` file from your computer.

```
rm -rf ~/credentials.json
```

### **Configure the application with the secret**

You now update the deployment to include the following changes:

- Add a volume to the Pod specification. This volume contains the secret.
- The secrets volume is mounted in the application container.
- The `GOOGLE_APPLICATION_CREDENTIALS` environment variable is set to point to the key file in the secret volume mount.

The `GOOGLE_APPLICATION_CREDENTIALS` environment variable is automatically recognized by Cloud Client Libraries, in this case, the Cloud Pub/Sub client for Python.

The updated Deployment file called `pubsub-secret.yaml` has been provided for you.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pubsub
spec:
  selector:
    matchLabels:
      app: pubsub
  template:
    metadata:
      labels:
        app: pubsub
    spec:
      volumes:
      - name: google-cloud-key
        secret:
          secretName: pubsub-key
      containers:
      - name: subscriber
        image: gcr.io/google-samples/pubsub-sample:v1
        volumeMounts:
        - name: google-cloud-key
          mountPath: /var/secrets/google
        env:
        - name: GOOGLE_APPLICATION_CREDENTIALS
          value: /var/secrets/google/key.json
```

1. Deploy the `pubsub-secret.yaml` configuration file.

```
kubectl apply -f pubsub-secret.yaml
```

1. After the configuration is deployed, execute the following command to display the Pod status.

The Pod status should be *Running*. The status change might take several seconds to appear.

```
kubectl get pods -l app=pubsub
```

**Output (Do not copy):**

```
NAME                      READY     STATUS        RESTARTS   AGE
pubsub-c4f48b868-xk27w    1/1       Running       0          35s
```

### **Test receiving Cloud Pub/Sub messages**

1. Now that you configured the application, publish a message to the Cloud Pub/Sub topic you created earlier in the lab.

```
gcloud pubsub topics publish $my_pubsub_topic --message="Hello, world!"
```

**Output (Do not copy): Your message ID will differ.**

```
messageIds:
- '328977244395410'
```

Within a few seconds, the message should be picked up by the application and printed to the output stream.

1. To inspect the logs from the deployed Pod, execute the following command:

```
kubectl logs -l app=pubsub
```

The output should look like the example.

**Output (do not copy)**

```
Pulling messages from Pub/Sub subscription...
[2018-12-17 22:01:06.860378] Received message: ID=328977244395410 Data=b'Hello, world!'
[2018-12-17 22:01:06.860736] Processing: 328977244395410
[2018-12-17 22:01:09.863973] Processed: 328977244395410
```

Click Check my progress to verify the objective.

Work with Secrets

Check my progress

## Task 2. Work with ConfigMaps

ConfigMaps bind configuration files, command-line arguments, environment variables, port numbers, and other configuration artifacts to your Pods' containers and system components at runtime. ConfigMaps enable you to separate your configurations from your Pods and components. But ConfigMaps aren't encrypted, making them inappropriate for credentials. This is the difference between secrets and ConfigMaps: secrets are encrypted and are therefore better suited for confidential or sensitive information such as credentials. ConfigMaps are better suited for general configuration information such as port numbers.

### Use the kubectl command to create ConfigMaps

You use `kubectl` to create ConfigMaps by following the pattern `kubectl create configmap [NAME] [DATA]` and adding a flag for file (`--from-file`) or literal (`--from-literal`).

1. Start with a simple literal in the following `kubectl` command:

```
kubectl create configmap sample --from-literal=message=hello
```

1. To see how Kubernetes ingested the ConfigMap, execute the following command:

```
kubectl describe configmaps sample
```

The output should look like the example.

**Output (do not copy)**

```
Name:         sample
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
message:
----
hello
Events:  <none>
```

Next you will create a ConfigMap from a file. The file `sample2.properties` has been provided for you and contains some sample data:

**do not copy**

```
message2=world
foo=bar
meaningOfLife=42
```

1. To create the ConfigMap from `sample2.properties`, execute the following command:

```
kubectl create configmap sample2 --from-file=sample2.properties
```

1. To see how Kubernetes ingested the ConfigMap, execute the following command:

```
kubectl describe configmaps sample2
```

The output should look like the example.

**Output (do not copy)**

```
Name:         sample2
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
sample2.properties:
----
message2=world
foo=bar
meaningOfLife=42

Events:  <none>
```

### **Use manifest files to create ConfigMaps**

You can also use a YAML configuration file to create a ConfigMap. The `config-map-3.yaml` file contains a ConfigMap definition called `sample3`. You will use this ConfigMap later to demonstrate two different ways to expose the data inside a container.

```
apiVersion: v1
data:
  airspeed: africanOrEuropean
  meme: testAllTheThings
kind: ConfigMap
metadata:
  name: sample3
  namespace: default
  selfLink: /api/v1/namespaces/default/configmaps/sample3
```

1. Create the ConfigMap from the YAML file:

```
kubectl apply -f config-map-3.yaml
```

1. To see how Kubernetes ingested the ConfigMap, execute the following command:

```
kubectl describe configmaps sample3
```

The output should look like the example.

**Output (do not copy)**

```
Name:         sample3
Namespace:    default
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"v1","data":{"airspeed":"africanOrEuropean","meme":"testAllTheThings"},"kind":"ConfigMap","metadata":{"annotations":{
},"name...

Data
====
airspeed:
----
africanOrEuropean
meme:
----
testAllTheThings
Events:  <none>
```

Now you have some non secret, unencrypted, configuration information properly separated from your application and available to your cluster. You've performed this task using ConfigMaps in three different ways to demonstrate the various options, but in practice you typically pick one method, most likely the YAML configuration file approach. Configuration files provide a record of the values that you've stored so that you can easily repeat the process in the future.

Next, you'll learn how to access this information from within your application.

### **Use environment variables to consume ConfigMaps in containers**

In order to access ConfigMaps from inside Containers using environment variables the Pod definition must be updated to include one or more `configMapKeyRefs`. The file `pubsub-configmap.yaml` is an updated version of the Cloud Pub/Sub demo Deployment that includes the following additional `env:` setting at the end of the file to import environmental variables from the ConfigMap into the container.

```
        - name: INSIGHTS
          valueFrom:
            configMapKeyRef:
              name: sample3
              key: meme
```

1. To reapply the updated configuration file, execute the following command:

```
kubectl apply -f pubsub-configmap.yaml
```

Now your application has access to an environment variable called `INSIGHTS`, which has a value of `testAllTheThings`.

1. To verify that the environment variable has the correct value, you must gain shell access to your Pod, which means that you need the name of your Pod. To get the name of the Pod, execute the following the command:

```
kubectl get pods
```

The output should look like the example.

**Output (do not copy)**

```
NAME                     READY     STATUS    RESTARTS   AGE
pubsub-77df8f8c6-krfl2   1/1       Running   0          4m
```

1. To start the shell session, execute the following command, substituting the name of your Pod for `[MY-POD-NAME]`:

```
kubectl exec -it [MY-POD-NAME] -- sh
```

**Example (do not copy)**

```
kubectl exec -it pubsub-77df8f8c6-krfl2 -- sh
```

1. To print a list of environment variables, execute the following command:

```
printenv
```

`INSIGHTS=testAllTheThings` should appear in the list.

1. To exit the container's shell session, execute the following command:

```
exit
```

### **Use mounted volumes to consume ConfigMaps in containers**

You can populate a volume with the ConfigMap data instead of (or in addition to) storing it in an environment variable.

In this Deployment the ConfigMap named `sample-3` that you created earlier in this task is also added as a volume called `config-3` in the Pod spec. The `config-3` volume is then mounted inside the container on the path `/etc/config`. The original method using Environment variables to import ConfigMaps is also configured.

The updated Deployment file called `pubsub-configmap2.yaml` has been provided for you.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pubsub
spec:
  selector:
    matchLabels:
      app: pubsub
  template:
    metadata:
      labels:
        app: pubsub
    spec:
      volumes:
      - name: google-cloud-key
        secret:
          secretName: pubsub-key
      - name: config-3
        configMap:
          name: sample3
      containers:
      - name: subscriber
        image: gcr.io/google-samples/pubsub-sample:v1
        volumeMounts:
        - name: google-cloud-key
          mountPath: /var/secrets/google
        - name: config-3
          mountPath: /etc/config
        env:
        - name: GOOGLE_APPLICATION_CREDENTIALS
          value: /var/secrets/google/key.json
        - name: INSIGHTS
          valueFrom:
            configMapKeyRef:
              name: sample3
              key: meme
```

1. Reapply the updated configuration file.

```
kubectl apply -f pubsub-configmap2.yaml
```

1. Reconnect to the container's shell session to see if the value in the ConfigMap is accessible. The Pod names will have changed. To get the name of the Pod, execute the following the command:

```
kubectl get pods
```

The output should look like the example.

**Output (do not copy)**

```
NAME                      READY     STATUS    RESTARTS   AGE
pubsub-747cf8c545-ngsrf   1/1       Running   0          30s
pubsub-df6bc7b87-vb8cz    1/1    Terminating  0          4m     
```

1. To start the shell session, execute the following command, substituting the name of your Pod for `[MY-POD-NAME]`:

```
kubectl exec -it [MY-POD-NAME] -- sh
```

**Example (do not copy)**

```
kubectl exec -it pubsub-747cf8c545-ngsrf -- sh
```

1. Navigate to the appropriate folder.

```
cd /etc/config
```

1. List the files in the folder.

The filenames as keys from `sample3` should be listed.

```
ls
```

**Output (do not copy)**

```
airspeed  meme
```

1. To view the contents of one of the files, execute the following command:

```
cat airspeed
```

**Output (Do not copy):**

```
africanOrEuropean#
```

Note that the value of airspeed did not include a carriage return, therefore the command prompt (the # sign) is at the end of the returned value.

1. To exit the container's shell, execute the following command:

```
exit
```

Click Check my progress to verify the objective.