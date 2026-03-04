# Terraform – Comandos útiles y administración de infraestructura

Este documento resume los comandos esenciales para administrar proyectos Terraform: ciclo de trabajo, state, backends, autenticación con nubes, importación de recursos y trabajo con workspaces.

---

## 🚀 Ciclo de trabajo básico
```bash
terraform init
terraform fmt -recursive
terraform validate
terraform plan -var-file="env/dev.tfvars" -out=plan.tfplan
terraform apply plan.tfplan
terraform destroy -var-file="env/dev.tfvars"
```

---

## 🧩 Workspaces
```bash
terraform workspace list
terraform workspace new dev
terraform workspace select dev
```

---

## 🗂️ Gestión del estado (state)
```bash
terraform state list
terraform state show <ADDRESS>
terraform state rm <ADDRESS>
terraform state mv <SRC> <DST>
terraform refresh
terraform output
```

---

## 📥 Importar recursos existentes
```bash
terraform import <ADDRESS> <REAL_ID>

# Ejemplos:

# AWS
terraform import aws_iam_role.myrole myrole

# GCP
terraform import google_storage_bucket.bucket my-bucket

# Azure
terraform import azurerm_resource_group.rg /subscriptions/<SUB>/resourceGroups/<RG>
```

---

## 🏗️ Ejecución no interactiva / CI/CD
```bash
terraform plan -input=false -var-file="env/prod.tfvars" -out=plan.tfplan
terraform apply -input=false -auto-approve plan.tfplan
```

---

## 🔒 Providers y versiones
```bash
terraform providers
terraform providers lock -platform=linux_amd64 -platform=darwin_amd64
terraform version
```

---

## 🗃️ Backends remotos (S3 / GCS / Azure Storage)

### AWS S3 (con bloqueo DynamoDB)
```hcl
terraform {
  backend "s3" {
    bucket         = "tf-state-bucket"
    key            = "project/infra.tfstate"
    region         = "us-west-2"
    dynamodb_table = "tf-locks"
    encrypt        = true
  }
}
```

### GCP GCS
```hcl
terraform {
  backend "gcs" {
    bucket = "tf-state-bucket"
    prefix = "project"
  }
}
```

### Azure Storage
```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "rg-tf"
    storage_account_name = "sttfstate"
    container_name       = "tfstate"
    key                  = "project/infra.tfstate"
  }
}
```

---

## 🔐 Autenticación para Terraform

### AWS
```bash
aws configure
export AWS_PROFILE=dev

# O variables de entorno:
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_DEFAULT_REGION=us-west-2
```

### GCP
```bash
gcloud auth application-default login
export GOOGLE_PROJECT=<PROJECT_ID>
export GOOGLE_APPLICATION_CREDENTIALS="$HOME/.config/gcloud/application_default_credentials.json"
```

### Azure
```bash
az login
az ad sp create-for-rbac --name sp-tf --role Contributor --scopes="/subscriptions/<SUB_ID>"

export ARM_CLIENT_ID=<APP_ID>
export ARM_CLIENT_SECRET=<PASSWORD>
export ARM_TENANT_ID=<TENANT_ID>
export ARM_SUBSCRIPTION_ID=<SUB_ID>
```

---

## ✔️ Checklist recomendado
```txt
[ ] Configurar backend remoto (S3/GCS/Azure)
[ ] Definir variables por entorno (*.tfvars)
[ ] Bloquear versiones de providers
[ ] Crear pipeline: fmt → validate → plan → apply
[ ] Revisar cambios en state y outputs
```
