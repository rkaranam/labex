## Introduction

In Kubernetes, jobs and cronjobs are used to run tasks that are not part of a long-running application or service. Jobs are used for one-off tasks, while cronjobs are used for tasks that need to be run on a regular schedule.

### Run a Pod with a Job

The first step is to create a pod that runs a job. In this example, we will create a pod that runs a command to print "Hello, World!" to the console.

Create a file named  `job.yaml`in  `/home/labex/project/`  with the following contents:

```yml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello-job
spec:
  template:
    spec:
      containers:
        - name: hello
          image: busybox
          command: ["sh", "-c", 'echo "Hello, World!"']
      restartPolicy: Never
  backoffLimit: 4
```

In this file, we define a job named  `hello-job`  that runs a single container named  `hello`. The container runs the  `busybox`  image and executes a command to print "Hello, World!" to the console.

To create the job, run the following command:

```shell
kubectl apply -f job.yaml
```

You can check the status of the job by running the following command:

```shell
kubectl get jobs
```

Once the job is completed, you can view the logs of the pod by running the following command:

```shell
kubectl logs <POD_NAME>
```

Replace  `<POD_NAME>`  with the name of the pod that ran the job,and you can get the  `POD_NAME`  with the  `kubectl get pods |grep hello-job`  command.

### Run a Job with Multiple Pods

In some cases, you may need to run a job with multiple pods for better performance. In this example, we will create a job that runs multiple pods to download files from a remote server.

Create a file named  `multi-pod-job.yaml`  in  `/home/labex/project/`  with the following contents:

```yml
apiVersion: batch/v1
kind: Job
metadata:
  name: download-job
spec:
  completions: 3
  parallelism: 2
  template:
    spec:
      containers:
        - name: downloader
          image: curlimages/curl
          command: ["curl", "-o", "/data/file", "http://example.com/file"]
          volumeMounts:
            - name: data-volume
              mountPath: /data
      restartPolicy: Never
      volumes:
        - name: data-volume
          emptyDir: {}
  backoffLimit: 4
```

In this file, we define a job named  `download-job`  that runs multiple pods with the  `curlimages/curl`  image. Each pod downloads a file from  `http://example.com/file`  and saves it to a shared volume named  `data-volume`.

To create the job, run the following command:

```shell
kubectl apply -f multi-pod-job.yaml
```

You can check the status of the job by running the following command:

```shell
kubectl get jobs
```

Once the job is completed, you can view the logs of the pod by running the following command:

```shell
kubectl logs <POD_NAME>
```

Replace  `<POD_NAME>`  with the name of any pod that ran the job. You can see the download log of the file,and you can get the  `POD_NAME`  with the  `kubectl get pod |grep download-job`  command.

### Run a Cronjob

In addition to one-off jobs, Kubernetes also supports cronjobs for running tasks on a regular schedule. In this example, we will create a cronjob that runs a command every minute.

Create a file named  `cronjob.yaml`  in  `/home/labex/project/`  with the following contents:

```yml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cronjob
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: hello
              image: busybox
              command: ["sh", "-c", 'echo "Hello, World!"']
          restartPolicy: Never
  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 3
```

In this file, we define a cronjob named  `hello-cronjob`  that runs a command every minute. The command is the same as the one we used in the first example to print "Hello, World!" to the console.

To create the cronjob, run the following command:

```shell
kubectl apply -f cronjob.yaml
```

You can check the status of the cronjob by running the following command:

```shell
kubectl get cronjobs
```

Once the cronjob is running, you can view the logs of the pod by running the following command:

```shell
kubectl logs -f <POD_NAME>
```

Replace  `<POD_NAME>`  with the name of any pod that was created by the cronjob,and you can get the  `POD_NAME`  with the  `kubectl get pod |grep hello-cronjob`  command.

