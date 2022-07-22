---
title: "Use Cluster Connect to connect to Azure Arc-enabled Kubernetes clusters"
services: azure-arc
ms.service: azure-arc
ms.date: 06/03/2022
ms.topic: article
description: "Use Cluster Connect to securely connect to Azure Arc-enabled Kubernetes clusters"
---

# Use Cluster Connect to connect to Azure Arc-enabled Kubernetes clusters

With Cluster Connect, you can securely connect to Azure Arc-enabled Kubernetes clusters without requiring any inbound port to be enabled on the firewall.

Access to the `apiserver` of the Azure Arc-enabled Kubernetes cluster enables the following scenarios:

- Interactive debugging and troubleshooting.
- Cluster access to Azure services for [custom locations](custom-locations.md) and other resources created on top of it.

A conceptual overview of this feature is available in [Cluster connect - Azure Arc-enabled Kubernetes](conceptual-cluster-connect.md).

## Prerequisites

### [Azure CLI](#tab/azure-cli)

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Install](/cli/azure/install-azure-cli) or [update](/cli/azure/update-azure-cli) Azure CLI to version >= 2.16.0.

- Install the `connectedk8s` Azure CLI extension of version >= 1.2.5:

  ```azurecli
  az extension add --name connectedk8s
  ```

  If you've already installed the `connectedk8s` extension, update the extension to the latest version:

  ```azurecli
   az extension update --name connectedk8s
   ```

- An existing Azure Arc-enabled Kubernetes connected cluster.
  - If you haven't connected a cluster yet, use our [quickstart](quickstart-connect-cluster.md).
  - [Upgrade your agents](agent-upgrade.md#manually-upgrade-agents) to version >= 1.5.3.

- Enable the below endpoints for outbound access in addition to the ones mentioned under [connecting a Kubernetes cluster to Azure Arc](quickstart-connect-cluster.md#meet-network-requirements):

  | Endpoint | Port |
  |----------------|-------|
  |`*.servicebus.windows.net` | 443 |
  |`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com` | 443 |

- Replace the placeholders and run the below command to set the environment variables used in this document:

  ```azurecli
  CLUSTER_NAME=<cluster-name>
  RESOURCE_GROUP=<resource-group-name>
  ARM_ID_CLUSTER=$(az connectedk8s show -n $CLUSTER_NAME -g $RESOURCE_GROUP --query id -o tsv)
  ```

### [Azure PowerShell](#tab/azure-powershell)

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Install [Azure PowerShell version 6.6.0 or later](/powershell/azure/install-az-ps).

- An existing Azure Arc-enabled Kubernetes connected cluster.
  - If you haven't connected a cluster yet, use our [quickstart](quickstart-connect-cluster.md).
  - [Upgrade your agents](agent-upgrade.md#manually-upgrade-agents) to version >= 1.5.3.

- Enable the below endpoints for outbound access in addition to the ones mentioned under [connecting a Kubernetes cluster to Azure Arc](quickstart-connect-cluster.md#meet-network-requirements):

  | Endpoint | Port |
  |----------------|-------|
  |`*.servicebus.windows.net` | 443 |
  |`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com` | 443 |

- Replace the placeholders and run the below command to set the environment variables used in this document:

  ```azurepowershell
  $CLUSTER_NAME = <cluster-name>
  $RESOURCE_GROUP = <resource-group-name>
  $ARM_ID_CLUSTER = (az connectedk8s show -n $CLUSTER_NAME -g $RESOURCE_GROUP --query id -o tsv)
  ```

---

## Azure Active Directory authentication option

### [Azure CLI](#tab/azure-cli)

1. Get the `objectId` associated with your Azure AD entity.

   - For an Azure AD user account:

     ```azurecli
     AAD_ENTITY_OBJECT_ID=$(az ad signed-in-user show --query userPrincipalName -o tsv)
     ```

   - For an Azure AD application:

     ```azurecli
     AAD_ENTITY_OBJECT_ID=$(az ad sp show --id <id> --query objectId -o tsv)
     ```

1. Authorize the entity with appropriate permissions.

   - If you are using Kubernetes native ClusterRoleBinding or RoleBinding for authorization checks on the cluster, with the `kubeconfig` file pointing to the `apiserver` of your cluster for direct access, you can create one mapped to the Azure AD entity (service principal or user) that needs to access this cluster. Example:

      ```console
      kubectl create clusterrolebinding demo-user-binding --clusterrole cluster-admin --user=$AAD_ENTITY_OBJECT_ID
      ```

   - If you are using Azure RBAC for authorization checks on the cluster, you can create an Azure role assignment mapped to the Azure AD entity. Example:

     ```azurecli
     az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee $AAD_ENTITY_OBJECT_ID --scope $ARM_ID_CLUSTER
     ```

### [Azure PowerShell](#tab/azure-powershell)

1. Get the `objectId` associated with your Azure AD entity.

   - For an Azure AD user account:

     ```azurepowershell
     $AAD_ENTITY_OBJECT_ID = (az ad signed-in-user show --query objectId -o tsv)
     ```

   - For an Azure AD application:

     ```azurepowershell
     $AAD_ENTITY_OBJECT_ID = (az ad sp show --id <id> --query objectId -o tsv)
     ```

1. Authorize the entity with appropriate permissions.

   - If you are using Kubernetes native ClusterRoleBinding or RoleBinding for authorization checks on the cluster, with the `kubeconfig` file pointing to the `apiserver` of your cluster for direct access, you can create one mapped to the Azure AD entity (service principal or user) that needs to access this cluster. Example:

      ```console
      kubectl create clusterrolebinding demo-user-binding --clusterrole cluster-admin --user=$AAD_ENTITY_OBJECT_ID
      ```

   - If you are using Azure RBAC for authorization checks on the cluster, you can create an Azure role assignment mapped to the Azure AD entity. Example:

     ```azurecli
     az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee $AAD_ENTITY_OBJECT_ID --scope $ARM_ID_CLUSTER
     ```

---

## Service account token authentication option

### [Azure CLI](#tab/azure-cli)

1. With the `kubeconfig` file pointing to the `apiserver` of your Kubernetes cluster, create a service account in any namespace (the following command creates it in the default namespace):

   ```console
   kubectl create serviceaccount demo-user
   ```

1. Create ClusterRoleBinding to grant this [service account the appropriate permissions on the cluster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding). Example:

    ```console
    kubectl create clusterrolebinding demo-user-binding --clusterrole cluster-admin --serviceaccount default:demo-user
    ```

1. Create a service account token:

    ```console
    kubectl apply -f - <<EOF
    apiVersion: v1
    kind: Secret
    metadata:
      name: demo-user-secret
      annotations:
        kubernetes.io/service-account.name: demo-user
    type: kubernetes.io/service-account-token
    EOF
    ```

    ```console
    $TOKEN=(kubectl get secret demo-user-secret -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

### [Azure PowerShell](#tab/azure-powershell)

1. With the `kubeconfig` file pointing to the `apiserver` of your Kubernetes cluster, create a service account in any namespace (the following command creates it in the default namespace):

   ```console
   kubectl create serviceaccount demo-user
   ```

1. Create ClusterRoleBinding or RoleBinding to grant this [service account the appropriate permissions on the cluster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding). Example:

    ```console
    kubectl create clusterrolebinding demo-user-binding --clusterrole cluster-admin --serviceaccount default:demo-user
    ```

1. Create a service account token by :

    ```console
    kubectl apply -f demo-user-secret.yaml
    ```
    
    Contents of `demo-user-secret.yaml`:

    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: demo-user-secret
      annotations:
        kubernetes.io/service-account.name: demo-user
    type: kubernetes.io/service-account-token
    ```

    ```console
    $TOKEN = ([System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String((kubectl get secret demo-user-secret -o jsonpath='{$.data.token}'))))
    ```

---

## Access your cluster

1. Set up the Cluster Connect based kubeconfig needed to access your cluster based on the authentication option used:

   - If using Azure Active Directory authentication option, after logging into Azure CLI using the Azure AD entity of interest, get the Cluster Connect `kubeconfig` needed to communicate with the cluster from anywhere (from even outside the firewall surrounding the cluster):

     ```azurecli
     az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP
     ```

   - If using the service account authentication option, get the Cluster Connect `kubeconfig` needed to communicate with the cluster from anywhere:

     ```azurecli
     az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP --token $TOKEN
     ```

1. Use `kubectl` to send requests to the cluster:

   ```console
   kubectl get pods
   ```

You should now see a response from the cluster containing the list of all pods under the `default` namespace.

## Known limitations

When making requests to the Kubernetes cluster, if the Azure AD entity used is a part of more than 200 groups, the following error is observed as this is a known limitation:

`You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported.`

To get past this error:

1. Create a [service principal](/cli/azure/create-an-azure-service-principal-azure-cli), which is less likely to be a member of more than 200 groups.
1. [Sign in](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) to Azure CLI with the service principal before running the `az connectedk8s proxy` command.

## Next steps

- Set up [Azure AD RBAC](azure-rbac.md) on your clusters.
- Deploy and manage [cluster extensions](extensions.md).
