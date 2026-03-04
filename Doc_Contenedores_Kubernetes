# Kubernetes + Docker + Monitoreo + Administración de Clúster

Este documento reúne comandos útiles para operar Kubernetes en GKE, AKS y EKS; además de administración de Docker, kubeconfig, despliegues, debugging, monitoreo, networking, RBAC, nodos, almacenamiento y herramientas complementarias como Helm.

---

## 🧭 Autenticación y conexión a Kubernetes (GKE, AKS, EKS)

### Google Cloud (GKE)
```bash
gcloud auth login
gcloud config set project <PROJECT_ID>
gcloud config set compute/region <REGION>
gcloud config set compute/zone <ZONE>
gcloud auth application-default login
gcloud container clusters get-credentials <CLUSTER_NAME> --zone <ZONE> --project <PROJECT_ID>
```

### Azure AKS
```bash
az login
az account set --subscription "<SUBSCRIPTION>"
az aks get-credentials --resource-group <RG_NAME> --name <CLUSTER_NAME> --overwrite-existing
```

### AWS EKS
```bash
aws configure
aws eks update-kubeconfig --name <CLUSTER_NAME> --region <REGION> --alias <ALIAS>
```

---

## 🔐 kubeconfig y contextos
```bash
kubectl config get-contexts
kubectl config current-context
kubectl config use-context <CONTEXT>
kubectl config set-context --current --namespace=<NS>
export KUBECONFIG=~/.kube/config:~/extra/kubeconfig1:~/extra/kubeconfig2
kubectl config view --merge --flatten > ~/.kube/config-merged
mv ~/.kube/config-merged ~/.kube/config
unset KUBECONFIG
```

---

## 📦 Docker (local + registries)

### Básicos
```bash
docker build -t myapp:1.0 .
docker tag myapp:1.0 <REGISTRY>/<REPO>/myapp:1.0
docker run --rm -p 8080:8080 myapp:1.0
docker images
docker ps -a
docker system prune -af
```

### Google Artifact Registry / GCR
```bash
gcloud auth configure-docker
gcloud auth configure-docker <REGION>-docker.pkg.dev
docker push <REGION>-docker.pkg.dev/<PROJECT>/<REPO>/myapp:1.0
```

### Azure ACR
```bash
az acr login --name <ACR_NAME>
docker tag myapp:1.0 <ACR_NAME>.azurecr.io/myapp:1.0
docker push <ACR_NAME>.azurecr.io/myapp:1.0
```

### Amazon ECR
```bash
aws ecr get-login-password --region <REGION> \
 | docker login --username AWS --password-stdin <ACCOUNT>.dkr.ecr.<REGION>.amazonaws.com

docker tag myapp:1.0 <ACCOUNT>.dkr.ecr.<REGION>.amazonaws.com/myapp:1.0
docker push <ACCOUNT>.dkr.ecr.<REGION>.amazonaws.com/myapp:1.0
```

---

## ⚙️ kubectl — administración esencial

### Recursos generales
```bash
kubectl cluster-info
kubectl get nodes -o wide
kubectl get ns
kubectl get all -n <NS>
kubectl api-resources
```

### Listados y filtros
```bash
kubectl get pods -n <NS> -w
kubectl get pods -o wide -n <NS>
kubectl get pods -l app=<LABEL> -n <NS>
kubectl get pod <POD> -o yaml
kubectl get svc my-svc -n <NS> -o jsonpath='{.spec.clusterIP}'
```

### Despliegues y rollouts
```bash
kubectl apply -f k8s/ -n <NS>
kubectl rollout status deployment/<DEPLOY> -n <NS>
kubectl rollout history deployment/<DEPLOY> -n <NS>
kubectl rollout undo deployment/<DEPLOY> -n <NS>
kubectl scale deployment/<DEPLOY> --replicas=3 -n <NS>
kubectl set image deployment/<DEPLOY> <CONT>=<IMAGE>:<TAG> -n <NS>
```

---

## 🐚 Conexión a Pods y debugging
```bash
kubectl exec -it <POD> -n <NS> -- bash
kubectl logs <POD> -n <NS> -f --tail=500
kubectl logs <POD> -c <CONTAINER> -n <NS>
kubectl port-forward pod/<POD> 8080:80 -n <NS>
kubectl port-forward svc/<SERVICE> 8080:80 -n <NS>
kubectl cp <LOCAL> <NS>/<POD>:/tmp/
kubectl attach -it <POD> -c <CONTAINER> -n <NS>
kubectl debug -it <POD> -n <NS> --image=nicolaka/netshoot --target=<CONTAINER>
```

---

## 🌐 Networking y DNS
```bash
kubectl get endpoints <SERVICE> -n <NS> -o wide
kubectl run -it dnsutils --image=ghcr.io/kubernetes-sigs/e2e-test-images/dnsutils:1.3 \
  --restart=Never --rm -- nslookup kubernetes.default
```

---

## 🔑 RBAC y seguridad
```bash
kubectl auth can-i get pods -n <NS>
kubectl auth can-i '*' '*' --all-namespaces

kubectl create serviceaccount dev-sa -n <NS>
kubectl create rolebinding dev-sa-rb \
  --clusterrole=view --serviceaccount=<NS>:dev-sa -n <NS>

kubectl create token dev-sa -n <NS> --duration=24h
```

---

## 🖥️ Nodos y mantenimiento
```bash
kubectl cordon <NODE>
kubectl drain <NODE> --ignore-daemonsets --delete-emptydir-data
kubectl uncordon <NODE>
kubectl taint nodes <NODE> env=prod:NoSchedule
kubectl taint nodes <NODE> env-
```

---

## 📚 ConfigMaps, Secrets y almacenamiento
```bash
kubectl create configmap appcfg --from-file=config.yaml -n <NS>
kubectl create secret generic appsec --from-literal=DB_PASS=<PWD> -n <NS>
kubectl get pvc,pv -n <NS>
```

---

## 📊 Métricas, eventos y salud
```bash
kubectl get events -n <NS> --watch
kubectl top nodes
kubectl top pods -n <NS>
kubectl -n kube-system get pods -o wide
```

---

## 🧩 Helm
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install myrel bitnami/nginx -n <NS> --create-namespace
helm upgrade myrel bitnami/nginx -n <NS> -f values.yaml
helm rollback myrel 1 -n <NS>
helm uninstall myrel -n <NS>
```

---

## 🛡️ Info rápida por nube

### GKE
```bash
gcloud container clusters list
gcloud container node-pools list --cluster <CLUSTER> --zone <ZONE>
```

### AKS
```bash
az aks show -g <RG> -n <CLUSTER>
```

### EKS
```bash
aws eks list-clusters --region <REGION>
aws eks describe-cluster --name <CLUSTER> --region <REGION>
```

---

## 🧪 Flujo E2E de despliegue rápido
```bash
kubectl apply -f k8s/ -n <NS>
kubectl rollout status deployment/<DEPLOY> -n <NS>
kubectl get svc -n <NS>
kubectl port-forward svc/<SERVICE> 8080:80 -n <NS>
curl -i http://localhost:8080/health
kubectl top pods -n <NS>
kubectl get events -n <NS> --sort-by=.lastTimestamp | tail -50
```

---

## 📝 Ejemplos rápidos
```bash
ssh -i ./key.pem ubuntu@ec2-xx-xx-xx.compute.amazonaws.com
kubectl -n production get pods -w
kubectl exec -it transact-app-12345 -n transact -- bash
```
