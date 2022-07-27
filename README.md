# GCP final task

# Task requirements and flowchart

![Untitled](GCP%20final%20task%200022e94ac697427581a53c9aa09667d4/Untitled.png)

![Untitled](GCP%20final%20task%200022e94ac697427581a53c9aa09667d4/Untitled.jpeg)

---

# Repo components:

- GCP-infra: this directory have terraform files that it used to build GCP resources.
- python-app: this directory have python application files that we need to deploy and docker file.
- python-app-chart: this directory has python application helm chart files that we need to deploy.

---

# Tools used:

- **Docker:** to build an image from python application.
- **Kubernetes**: container orchestration system for automating python application containers.
- **Helm**: helps in managing Kubernetes applications
- **GCP**: to define GKE cluster to deploy python application on it.
- **Terraform**: used in creating GCP resouces.

---

# Solution steps:

## 1- Create a docker file and build an image for python application:

> build a python app image and pull redis image

```bash
$ docker build -t python-app .
$ docker pull redis
```

---

## 2- Push docker images to GCR:

```bash
# to auth your docker to push the image to GCR
$ gcloud auth configure-docker

# make a new tags to the images
$ docker tag python-app eu.gcr.io/[PROJECT-ID]/python-app
$ docker tag redis eu.gcr.io/[PROJECT-ID]/redis

# push the images to GCR
$ docker push eu.gcr.io/[PROJECT-ID]/python-app
$ docker push eu.gcr.io/[PROJECT-ID]/redis
```

![Untitled](GCP%20final%20task%200022e94ac697427581a53c9aa09667d4/Untitled%201.png)

---

## 3- Build your GCP infrastructure by terraform:

```bash
# to initialize terraform
$ terraform init

# to build infrastructure by terraform
$ terraform apply
```

> check resources on GCP:

![Untitled](GCP%20final%20task%200022e94ac697427581a53c9aa09667d4/Untitled%202.png)

![Untitled](GCP%20final%20task%200022e94ac697427581a53c9aa09667d4/Untitled%203.png)

---

## 4- Connect to management instance and install gcloud, kubectl and helm:

```bash
# ssh on management instance
$ gcloud compute ssh --zone "europe-west1-b" "python-app-management-vm"  --tunnel-through-iap --project "mostafa-alnaggar-project"

# install helm
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh

# install gcloud-sdk and initialize gcloud
$ curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-394.0.0-linux-x86_64.tar.gz
$ tar -xf google-cloud-cli-394.0.0-linux-x86_64.tar.gz
$ ./google-cloud-sdk/install.sh
$ ./google-cloud-sdk/bin/gcloud init

# install gcloud packages and update repo
$ sudo apt-get install apt-transport-https ca-certificates gnupg
$ echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
$ curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -
$ sudo apt-get update

# install auth-plugin
$ sudo apt-get install google-cloud-sdk-gke-gcloud-auth-plugin

# install kubectl
$ sudo apt-get install kubectl

# connect to the cluster
$ gcloud container clusters get-credentials python-app-gke-cluster --zone europe-west1-b --project mostafa-alnaggar-project

# check cluster connection 
$ kubectl get nodes
```

![Untitled](GCP%20final%20task%200022e94ac697427581a53c9aa09667d4/Untitled%204.png)

---

## 5- Copy python app helm chart to the management instance and deploy the application:

```bash
# copy helm chart
$ gcloud compute scp --recurse python-app-chart/ python-app-management-vm:~/

# install helm chart
$ helm install python-app-release python-app-chart/

# check pods
$ kubectl get po

# check services
$ kubectl get svc

# check ingress
$ kubectl get ingress
```

![Untitled](GCP%20final%20task%200022e94ac697427581a53c9aa09667d4/Untitled%205.png)

![Untitled](GCP%20final%20task%200022e94ac697427581a53c9aa09667d4/Untitled%206.png)

---

## And finally we are here 💣:
![Untitled](GCP%20final%20task%200022e94ac697427581a53c9aa09667d4/Untitled%207.png)