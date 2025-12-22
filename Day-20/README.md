# Secrets Management with Azure Key Vault on AKS  
### Integrating Azure Key Vault with Secrets Store CSI Driver

This project demonstrates **secure secrets management on Azure Kubernetes Service (AKS)** by integrating **Azure Key Vault** using the **Secrets Store CSI Driver** with **Azure Workload Identity**.

The goal is to **avoid hardcoding secrets** in Kubernetes manifests and securely mount secrets from Azure Key Vault into pods.

---

## Key Concepts Covered

- Azure Kubernetes Service (AKS)
- Azure Key Vault
- Secrets Store CSI Driver
- Azure Workload Identity
- Azure RBAC
- Kubernetes Service Accounts
- Secure secret mounting in Pods

---

## Architecture Overview

Pod (ServiceAccount)
|
Workload Identity (OIDC)
|
Azure AD Managed Identity
|
Azure RBAC
|
Azure Key Vault
|
Secrets mounted into Pod (CSI Driver)

---

## Prerequisites

- Azure CLI installed and logged in
- kubectl installed
- Azure subscription with sufficient permissions
- Basic knowledge of AKS and Kubernetes

---

## AKS Setup Using Azure CLI

### Create Azure Resource Group

```bash
az group create --name keyvault-demo --location centralindia

Create AKS Cluster with Key Vault CSI Driver Support

az aks create \
  --name keyvault-demo-cluster \
  --resource-group keyvault-demo \
  --node-count 1 \
  --node-vm-size Standard_D2ps_v5 \
  --enable-addons azure-keyvault-secrets-provider \
  --enable-oidc-issuer \
  --enable-workload-identity \
  --generate-ssh-keys

This enables:

Secrets Store CSI Driver

Azure Key Vault provider

OIDC issuer (required for workload identity)

Get AKS Credentials

az aks get-credentials \
  --resource-group keyvault-demo \
  --name keyvault-demo-cluster

Verify CSI Driver Pods

kubectl get pods -n kube-system \
-l 'app in (secrets-store-csi-driver,secrets-store-provider-azure)' -o wide
You should see CSI driver pods running on each node.

Azure Key Vault Creation
Create a Key Vault with Azure RBAC enabled:

az keyvault create \
  --name aks-demo-keyvault \
  --resource-group keyvault-demo \
  --location centralindia \
  --enable-rbac-authorization

Connect Azure Identity with AKS (Workload Identity) and set Environment Variables:

export SUBSCRIPTION_ID=a597516f-863a-4318-9bbc-ebdaaa95e420
export RESOURCE_GROUP=keyvault-demo
export UAMI=azurekeyvaultsecretsprovider-keyvault-demo-cluster
export KEYVAULT_NAME=aks-demo-keyvault
export CLUSTER_NAME=keyvault-demo-cluster

az account set --subscription $SUBSCRIPTION_ID

Create User Assigned Managed Identity:

az identity create \
  --name $UAMI \
  --resource-group $RESOURCE_GROUP

Retrieve identity details:

export USER_ASSIGNED_CLIENT_ID=$(az identity show \
  -g $RESOURCE_GROUP \
  --name $UAMI \
  --query 'clientId' -o tsv)

export IDENTITY_TENANT=$(az aks show \
  --name $CLUSTER_NAME \
  --resource-group $RESOURCE_GROUP \
  --query identity.tenantId -o tsv)

Assign Key Vault Access (RBAC):

export KEYVAULT_SCOPE=$(az keyvault show \
  --name $KEYVAULT_NAME \
  --query id -o tsv)

az role assignment create \
  --role "Key Vault Administrator" \
  --assignee $USER_ASSIGNED_CLIENT_ID \
  --scope $KEYVAULT_SCOPE

Get AKS OIDC Issuer URL:

export AKS_OIDC_ISSUER=$(az aks show \
  --resource-group $RESOURCE_GROUP \
  --name $CLUSTER_NAME \
  --query "oidcIssuerProfile.issuerUrl" -o tsv)

echo $AKS_OIDC_ISSUER

Kubernetes Service Account Setup:

Define Service Account Details:

export SERVICE_ACCOUNT_NAME="workload-identity-sa"
export SERVICE_ACCOUNT_NAMESPACE="default"
Create Service Account with Annotation

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: ${SERVICE_ACCOUNT_NAME}
  namespace: ${SERVICE_ACCOUNT_NAMESPACE}
  annotations:
    azure.workload.identity/client-id: ${USER_ASSIGNED_CLIENT_ID}
EOF

Configure Federated Identity:

export FEDERATED_IDENTITY_NAME="aksfederatedidentity"

az identity federated-credential create \
  --name $FEDERATED_IDENTITY_NAME \
  --identity-name $UAMI \
  --resource-group $RESOURCE_GROUP \
  --issuer ${AKS_OIDC_ISSUER} \
  --subject system:serviceaccount:${SERVICE_ACCOUNT_NAMESPACE}:${SERVICE_ACCOUNT_NAME}

Create SecretProviderClass:
This defines which secrets/keys are fetched from Key Vault.

cat <<EOF | kubectl apply -f -
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-wi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    clientID: "${USER_ASSIGNED_CLIENT_ID}"
    keyvaultName: ${KEYVAULT_NAME}
    objects: |
      array:
        - |
          objectName: secret1
          objectType: secret
        - |
          objectName: key1
          objectType: key
    tenantId: "${IDENTITY_TENANT}"
EOF

Verify AKS & Key Vault Integration:

Create Sample Pod to Mount Secrets:

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: busybox-secrets-store-inline-wi
  labels:
    azure.workload.identity/use: "true"
spec:
  serviceAccountName: workload-identity-sa
  containers:
  - name: busybox
    image: registry.k8s.io/e2e-test-images/busybox:1.29-4
    command: ["/bin/sleep", "10000"]
    volumeMounts:
    - name: secrets-store
      mountPath: "/mnt/secrets-store"
      readOnly: true
  volumes:
  - name: secrets-store
    csi:
      driver: secrets-store.csi.k8s.io
      readOnly: true
      volumeAttributes:
        secretProviderClass: "azure-kvname-wi"
EOF

List Mounted Secrets:

kubectl exec busybox-secrets-store-inline-wi -- ls /mnt/secrets-store/

Read Secret Value:

kubectl exec busybox-secrets-store-inline-wi -- cat /mnt/secrets-store/secret1


Security Best Practices Followed:

1. No secrets stored in YAML files
2. No Kubernetes Secrets created manually
3. Azure RBAC used instead of access policies
4. Workload Identity used instead of service principal secrets
5. Least privilege access applied

This project demonstrates:

1. Secure secrets management on AKS
2. Integration of Azure Key Vault using CSI Driver
3. Use of Azure Workload Identity
4. Enterprise-grade Kubernetes security practices
5. This approach is recommended for production AKS workloads.
