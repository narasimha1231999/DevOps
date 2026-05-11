# 🚀 Google Cloud Kubernetes Jenkins Setup Guide

## ✅ Step 1: Create Your Google Cloud Account

- Once the account is created, log in to the [Google Cloud Console](https://console.cloud.google.com/).
---

## ✅ Optional Step

- create a prject and set the project in the CLI
  
  ```bash
    gcloud config set project projectid
    ````
  
## ✅ Step 2: Create the Kubernetes Cluster

### Open the Cloud Shell.

### To check existing clusters:
```bash
gcloud container clusters list
````

> *If no clusters are listed, proceed to create one.*

### To create a cluster:

```bash
gcloud container clusters create my-cluster \
    --zone us-central1-a \
    --disk-type=pd-standard
```

> ⚠️ *Cluster creation may take **5 to 10 minutes**.*

---

### Once created:

* Go to **Kubernetes Engine → Clusters** in the Cloud Console.
* You should see **`my-cluster`** in **Running** state.

---

## ✅ Step 3: Connect to the Cluster

```bash
gcloud container clusters get-credentials my-cluster --zone us-central1-a
```

---

## ✅ Step 4: View Nodes

```bash
kubectl get nodes
```

---

## ✅ Step 5: Create a Pod

### Quick way using a command:

```bash
kubectl run --image=tomcat webserver
```

---

## ✅ Step 6: View Pod Status

```bash
kubectl get pods
```

### To see details like IP address and node info:

```bash
kubectl get pods -o wide
```

---

## ✅ Step 7: Create Pod Using YAML Definition

### Create a file:

```bash
vim pd-df1.yaml
```

### Paste the following YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: jenkins-pod
spec:
  containers:
  - name: myjenkins
    image: jenkins/jenkins
    ports:
    - containerPort: 8080
      hostPort: 8080
```

---

## ✅ Step 8: Open Port 8080 in Firewall

```bash
gcloud compute firewall-rules create rule2 --allow tcp:8080
```

---

## ✅ Step 9: Deploy Jenkins Pod

```bash
kubectl create -f pd-df1.yaml
```

### Check Pod Status:

```bash
kubectl get pods -o wide
```

### Check Node Details:

```bash
kubectl get nodes -o wide
```

---

## ✅ Step 10: Access Jenkins

1. Take the **External IP** of your node.
2. Open a browser.
3. Navigate to:

```
http://<EXTERNAL-IP>:8080
```

✅ You should now see the Jenkins UI!

---

## 🔐 (Optional) Get Jenkins Initial Admin Password

If prompted for an admin password in the Jenkins UI:

### 1. Find the Jenkins pod name:

```bash
kubectl get pods
```

### 2. Access the pod’s shell:

```bash
kubectl exec -it jenkins-pod -- /bin/bash
```

> Or use `/bin/sh` if `/bin/bash` is unavailable.

### 3. View the admin password:

```bash
cat /var/jenkins_home/secrets/initialAdminPassword
```

Copy this password and paste it into the Jenkins setup page.

---
## ✅ Done
