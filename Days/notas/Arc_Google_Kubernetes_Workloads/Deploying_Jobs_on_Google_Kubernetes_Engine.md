# Deploying Jobs on Google Kubernetes Engine

1 hourFree

Rate Lab

## Overview

In this lab, you define and run Jobs and CronJobs.

In GKE, a Job is a controller object that represents a finite task. Jobs manage a task as it runs to completion, rather than managing an ongoing desired state such as the maintaining the total number of running Pods.

CronJobs perform finite, time-related tasks that run once or repeatedly at a time that you specify using Job objects to complete their tasks.

## Objectives

In this lab, you learn how to perform the following tasks:

- Define, deploy and clean up a GKE Job
- Define, deploy and clean up a GKE CronJob

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

## Task 1. Define and deploy a Job manifest

In GKE, a Job is a controller object that represents a finite task.

In this task, you create a Job, inspect its status, and then remove it.

### **Connect to the lab Google Kubernetes Engine cluster**

1. In Cloud Shell, type the following command to set the environment variable for the zone and cluster name.

```
export my_zone=us-central1-a
export my_cluster=standard-cluster-1
```

1. Configure kubectl tab completion in Cloud Shell.

```
source <(kubectl completion bash)
```

1. In Cloud Shell, configure access to your cluster for the kubectl command-line tool, using the following command:

```
gcloud container clusters get-credentials $my_cluster --zone $my_zone
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
cd ~/ak8s/Jobs_CronJobs
```

### **Create and run a Job**

You will create a job using a sample deployment manifest called `example-job.yaml` that has been provided for you. This Job computes the value of Pi to 2,000 places and then prints the result.

```
apiVersion: batch/v1
kind: Job
metadata:
  # Unique key of the Job instance
  name: example-job
spec:
  template:
    metadata:
      name: example-job
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl"]
        args: ["-Mbignum=bpi", "-wle", "print bpi(2000)"]
      # Do not restart containers after they exit
      restartPolicy: Never
```

1. To create a Job from this file, execute the following command:

```
kubectl apply -f example-job.yaml
```

Click *Check my progress* to verify the objective.

Create and run a Job

Check my progress



1. To check the status of this Job, execute the following command:

```
kubectl describe job example-job
```

You will see details of the job, including the Pod statuses indicating how many jobs are still running, how many completed successfully and how many failed.

**Output (do not copy)**

```
...
Start Time:     Thu, 20 Dec 2018 14:34:09 +0000
Pods Statuses:  0 Running / 1 Succeeded / 0 Failed
...
```

1. To view all Pod resources in your cluster, including Pods created by the Job which have completed, execute the following command:

```
kubectl get pods
```

Your Pod name might be different from the example output. Make a note of one of the Pod names.

**Output (do not copy)**

```
NAME                   READY     STATUS      RESTARTS   AGE
example-job-sqljc      0/1       Completed   0          1m
```

### **Clean up and delete the Job**

When a Job completes, the Job stops creating Pods. The Job API object is not removed when it completes, which allows you to view its status. Pods created by the Job are not deleted, but they are terminated. Retention of the Pods allows you to view their logs and to interact with them.

1. To get a list of the Jobs in the cluster, execute the following command:

```
kubectl get jobs
```

The output should look like the example.

**Output (do not copy)**

```
NAME           COMPLETIONS   DURATION       AGE
example-job    1/1           75s            2m5s  
```

1. To retrieve the log file from the Pod that ran the Job execute the following command. You must replace [POD-NAME] with the node name you recorded in the last task

```
kubectl logs [POD-NAME]
```

The output will show that the job wrote the first two thousand digits of pi to the Pod log.

1. To delete the Job, execute the following command:

```
kubectl delete job example-job
```

If you try to query the logs again the command will fail as the Pod can no longer be found.

## Task 2. Define and deploy a CronJob manifest

You can create CronJobs to perform finite, time-related tasks that run once or repeatedly at a time that you specify.

In this task, you create and run a CronJob, and then you clean up and delete the Job.

### **Create and run a CronJob**

The CronJob manifest file `example-cronjob.yaml` has been provided for you. This CronJob deploys a new container every minute that prints the time, date and "Hello, World!".

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo "Hello, World!"
          restartPolicy: OnFailure
```

**Note**

CronJobs use the required `schedule` field, which accepts a time in the Unix standard `crontab` format. All CronJob times are in UTC:

- The first value indicates the minute (between 0 and 59).
- The second value indicates the hour (between 0 and 23).
- The third value indicates the day of the month (between 1 and 31).
- The fourth value indicates the month (between 1 and 12).
- The fifth value indicates the day of the week (between 0 and 6).

The `schedule` field also accepts * and ? as wildcard values. Combining / with ranges specifies that the task should repeat at a regular interval. In the example, `*/1 * * * *` indicates that the task should repeat every minute of every day of every month.

1. To create a Job from this file, execute the following command:

```
kubectl apply -f example-cronjob.yaml
```

Click *Check my progress* to verify the objective.

Create and run a CronJob

Check my progress



1. To get a list of the Jobs in the cluster, execute the following command:

```
kubectl get jobs
```

The output should look like the example.

**Output (do not copy)**

```
NAME               COMPLETIONS    DURATION     AGE
hello-1545013620   1/1            2s           18s
```

1. To check the status of this Job, execute the following command, where `[job_name]` is the name of your job:

```
kubectl describe job [job_name]
```

You will see details of the job, including the Pod statuses showing that one instance of this job was run.

**Output (do not copy)**

```
...
Start Time:     Thu, 20 Dec 2018 15:24:03 +0000
Pods Statuses:  0 Running / 1 Succeeded / 0 Failed
...
...Created pod: hello-1545319920-twkhl
```

1. Make a note of the name of the Pod that was used by this job.
2. View the output of the Job by querying the logs for the Pod. Replace [POD-NAME] with the name of the Pod you recorded in the last step.

```
kubectl logs [POD-NAME]
```

This will display the output of the shell script configured in the CronJob:

**Output (do not copy)**

```
Thu Dec 20 15:31:16 WET 2018
Hello,World!
```

1. To view all job resources in your cluster, including all of the Pods created by the CronJob which have completed, execute the following command:

```
kubectl get jobs
```

Your job names might be different from the example output. By default Kubernetes sets the Job history limits so that only the last three successful and last failed job are retained so this list will only contain the most recent three of four jobs.

**Output (do not copy)**

```
NAME               COMPLETIONS   DURATION     AGE
hello-1545013680   1             1            2m
hello-1545013740   1             1            1m
hello-1545013800   1             1            42s
```

### **Clean up and delete the Job**

In order to stop the CronJob and clean up the Jobs associated with it you must delete the CronJob.

1. To delete all these jobs, execute the following command:

```
kubectl delete cronjob hello
```

1. To verify that the jobs were deleted, execute the following command:

```
kubectl get jobs
```

The output should look like the example.

**Output (do not copy)**

```
No resources found.
```

All the Jobs were removed.