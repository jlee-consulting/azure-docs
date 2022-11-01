---
title: Modernize your Azure Kubernetes Service (AKS) application with a workload identity sidecar
description: In this Azure Kubernetes Service (AKS) article, you learn how to configure your Azure Kubernetes Service pod to authenticate with the workload identity sidecar.
services: container-service
ms.topic: article
ms.date: 09/29/2022
---

# Modernize application authentication with workload identity sidecar

If your Kubernetes application runs on Azure Kubernetes Service (AKS) and is using a managed identity to securely access resources in Azure, you can set up a migration sidecar ensuring a smooth transition using the new Azure Identity SDK and minimize downtime. This sidecar intercepts Instance Metadata Service (IMDS) traffic and routes them to Azure Active Directory (Azure AD) using OpenID Connect (OIDC). This enables you to migrate from using managed identity with pod identity to workload identity, until you can migrate your applications to use the latest version of Azure Identity SDK.

This article shows you how to set up your application pod to authenticate using managed identity with workload identity as a short-term migration solution.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## Before you begin

- The Azure CLI version 2.40.0 or later. Run `az --version` to find the version, and run `az upgrade` to upgrade the version. If you need to install or upgrade, see [Install Azure CLI][install-azure-cli].

## Create a managed identity

If you don't have a managed identity created and assigned to your pod, perform the following steps to create and grant the necessary permissions to storage, Key Vault, or whatever resources your application needs to authenticate with in Azure.

1. Use the Azure CLI [az account set][az-account-set] command to set a specific subscription to be the current active subscription. Then use the [az identity create][az-identity-create] command to create a managed identity.

    ```azurecli
    az account set --subscription "subscriptionID"
    ```

    ```azurecli
    az identity create --name "userAssignedIdentityName" --resource-group "resourceGroupName" --location "location" --subscription "subscriptionID"
    ```

    ```bash
    export USER_ASSIGNED_CLIENT_ID="$(az identity show --resource-group "resourceGroupName" --name "userAssignedIdentityName" --query 'clientId' -otsv)"
    ```

2. Grant the managed identity the permissions required to access the resources in Azure it requires.

3. To get the OIDC Issuer URL and save it to an environmental variable, run the following command. Replace the default values for the cluster name and the resource group name.

    ```bash
    export AKS_OIDC_ISSUER="$(az aks show -n myAKSCluster -g myResourceGroup --query "oidcIssuerProfile.issuerUrl" -otsv)"
    ```

## Create Kubernetes service account

If you don't have a dedicated Kubernetes service account created for this application, perform the following steps to create and then annotate it with the client ID of the managed identity created in the previous step. Use the [az aks get-credentials][az-aks-get-credentials] command and replace the values for the cluster name and the resource group name.

```azurecli
az aks get-credentials -n myAKSCluster -g "${RESOURCE_GROUP}"
```

Copy and paste the following multi-line input in the Azure CLI.

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    azure.workload.identity/client-id: ${USER_ASSIGNED_CLIENT_ID}
  labels:
    azure.workload.identity/use: "true"
  name: ${SERVICE_ACCOUNT_NAME}
  namespace: ${SERVICE_ACCOUNT_NAMESPACE}
EOF
```

The following output resembles successful creation of the identity:

```output
Serviceaccount/workload-identity-sa created
```

## Establish federated identity credential

Use the [az identity federated-credential create][az-identity-federated-credential-create] command to create the federated identity credential between the managed identity, the service account issuer, and the subject. Replace the values `resourceGroupName`, `userAssignedIdentityName`, `federatedIdentityName`, `serviceAccountNamespace`, and `serviceAccountName`.

```azurecli
az identity federated-credential create --name federatedIdentityName --identity-name userAssignedIdentityName --resource-group resourceGroupName --issuer ${AKS_OIDC_ISSUER} --subject system:serviceaccount:${SERVICE_ACCOUNT_NAMESPACE}:${SERVICE_ACCOUNT_NAME}
```

> [!NOTE]
> It takes a few seconds for the federated identity credential to be propagated after being initially added. If a token request is made immediately after adding the federated identity credential, it might lead to failure for a couple of minutes as the cache is populated in the directory with old data. To avoid this issue, you can add a slight delay after adding the federated identity credential.

## Deploy the workload

If your application is using managed identity and still relies on IMDS to get an access token, you can use the workload identity migration sidecar to start migrating to workload identity. This sidecar is a migration solution and in the long-term applications, you should modify their code to use the latest Azure Identity SDKs that support client assertion.

To update or deploy the workload, add these pod annotations only if you want to use the migration sidecar. You inject the following [annotation][pod-annotations] values to use the sidecar in your pod specification:

* `azure.workload.identity/inject-proxy-sidecar` - value is `true` or `false`
* `azure.workload.identity/proxy-sidecar-port` - value is the desired port for the proxy sidecar. The default value is `8080`.

When a pod with the above annotations is created, the Azure Workload Identity mutating webhook automatically injects the init-container and proxy sidecar to the pod spec.

The webhook that is already running adds the following YAML snippets to the pod deployment. The following is an example of the mutated pod spec:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: httpbin-pod
  labels:
    app: httpbin
spec:
  serviceAccountName: workload-identity-sa
  initContainers:
  - name: init-networking
    image: mcr.microsoft.com/oss/azure/workload-identity/proxy-init:v0.13.0
    securityContext:
      capabilities:
        add:
        - NET_ADMIN
        drop:
        - ALL
      privileged: true
      runAsUser: 0
    env:
    - name: PROXY_PORT
      value: "8080"
  containers:
  - name: nginx
    image: nginx:alpine
    ports:
    - containerPort: 80
  - name: proxy
    image: mcr.microsoft.com/oss/azure/workload-identity/proxy:v0.13.0
    ports:
    - containerPort: 8080
```

This configuration applies to any configuration where a pod is being created. After updating or deploying your application, you can verify the pod is in a running state using the [kubectl describe pod][kubectl-describe] command. Replace the value `podName` with the image name of your deployed pod.

```bash
kubectl describe pods podName -c azwi-proxy
```

To verify that pod is passing IMDS transactions, use the [kubectl logs][kubelet-logs] command. Replace the value `podName` with the image name of your deployed pod:

```bash
kubectl logs podName
```

The following log output resembles successful communication through the proxy sidecar. Verify that the logs show a token is successfully acquired and the GET operation is successful.

```output
I0926 00:29:29.968723       1 proxy.go:97] proxy "msg"="starting the proxy server" "port"=8080 "userAgent"="azure-workload-identity/proxy/v0.13.0-12-gc8527f3 (linux/amd64) c8527f3/2022-09-26-00:19" 
I0926 00:29:29.972496       1 proxy.go:173] proxy "msg"="received readyz request" "method"="GET" "uri"="/readyz" 
I0926 00:29:30.936769       1 proxy.go:107] proxy "msg"="received token request" "method"="GET" "uri"="/metadata/identity/oauth2/token?resource=https://management.core.windows.net/api-version=2018-02-01&client_id=<client_id>" 
I0926 00:29:31.101998       1 proxy.go:129] proxy "msg"="successfully acquired token" "method"="GET" "uri"="/metadata/identity/oauth2/token?resource=https://management.core.windows.net/api-version=2018-02-01&client_id=<client_id>"
```

## Remove pod-managed identity

After you've completed your testing and the application is successfully able to get a token using the proxy sidecar, you can remove the Azure AD pod-managed identity mapping for the pod from your cluster, and then remove the identity.

1. Run the [az aks pod-identity delete][az-aks-pod-identity-delete] command to remove the identity from your pod. This should only be done after all pods in the namespace using the pod-managed identity mapping have migrated to use the sidecar.

    ```azurecli
    az aks pod-identity delete --name podIdentityName --namespace podIdentityNamespace --resource-group myResourceGroup --cluster-name myAKSCluster
    ```

## Next steps

This article showed you how to set up your pod to authenticate using a workload identity as a migration option. For more information about Azure AD workload identity (preview), see the following [Overview][workload-identity-overview] article.

<!-- INTERNAL LINKS -->
[pod-annotations]: workload-identity-overview.md#pod-annotations
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-account-set]: /cli/azure/account#az-account-set
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[workload-identity-overview]: workload-identity-overview.md
[az-identity-federated-credential-create]: /cli/azure/identity/federated-credential#az-identity-federated-credential-create
[az-aks-pod-identity-delete]: /cli/azure/aks/pod-identity#az-aks-pod-identity-delete

<!-- EXTERNAL LINKS -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe