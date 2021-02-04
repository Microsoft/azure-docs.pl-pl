---
title: Korzystanie z Azure Active Directory tożsamości zarządzanych w usłudze Azure Kubernetes Service (wersja zapoznawcza)
description: Dowiedz się, jak za pomocą usługi AAD zarządzane tożsamości zarządzane w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 22b7a03a8598aa6e4b7c392567905d467776360c
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99557355"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Korzystanie z Azure Active Directory tożsamości zarządzanych w usłudze Azure Kubernetes Service (wersja zapoznawcza)

Azure Active Directory tożsamości zarządzanych przez program używają prymitywów Kubernetes do kojarzenia [zarządzanych tożsamości dla zasobów platformy Azure][az-managed-identities] i tożsamości w usłudze Azure Active Directory (AAD) przy użyciu zasobników. Administratorzy tworzą tożsamości i powiązania jako elementy podstawowe Kubernetes, które zezwalają na dostęp do zasobów platformy Azure korzystających z usługi AAD jako dostawcy tożsamości.

> [!NOTE]
> W przypadku istniejącej instalacji programu AADPODIDENTITY należy usunąć istniejącą instalację. Włączenie tej funkcji oznacza, że składnik MIC nie jest wymagany.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz mieć zainstalowany następujący zasób:

* Interfejs wiersza polecenia platformy Azure w wersji 2.8.0 lub nowszej
* `azure-preview`Rozszerzenie w wersji 0.4.68 lub nowszej

### <a name="limitations"></a>Ograniczenia

* Dla klastra można używać maksymalnie 50 tożsamości.
* Dla klastra można używać maksymalnie 50 wyjątków tożsamości.
* Tożsamości zarządzane pod względem systemu są dostępne tylko w pulach węzłów systemu Linux.

### <a name="register-the-enablepodidentitypreview"></a>Zarejestruj `EnablePodIdentityPreview`

Zarejestruj `EnablePodIdentityPreview` funkcję:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Instalowanie `aks-preview` interfejsu wiersza polecenia platformy Azure

Wymagany jest również *AKS — wersja zapoznawcza* interfejsu wiersza polecenia platformy Azure w wersji 0.4.64 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej AKS* , używając polecenie [AZ Extension Add][az-extension-add] . Lub zainstalować wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Tworzenie klastra AKS z tożsamościami zarządzanymi

Utwórz klaster AKS z zarządzaną tożsamością i tożsamością zarządzaną pod. W poniższych poleceniach użyto polecenia [AZ Group Create][az-group-create] , aby utworzyć grupę zasobów o nazwie Moja *zasobów* i polecenie [AZ AKS Create][az-aks-create] , aby utworzyć klaster AKS o nazwie *myAKSCluster* w *grupie zasobów* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity --network-plugin azure
```

Użyj [AZ AKS Get-Credentials][az-aks-get-credentials] , aby zalogować się do klastra AKS. To polecenie umożliwia również pobranie i skonfigurowanie `kubectl` certyfikatu klienta na komputerze deweloperskim.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-an-identity"></a>Tworzenie tożsamości

Utwórz tożsamość przy użyciu polecenia [AZ Identity Create][az-identity-create] i ustaw zmienne *IDENTITY_CLIENT_ID* i *IDENTITY_RESOURCE_ID* .

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="create-a-pod-identity"></a>Tworzenie tożsamości pod

Utwórz tożsamość pod dla klastra przy użyciu programu `az aks pod-identity add` .

> [!IMPORTANT]
> Musisz mieć odpowiednie uprawnienia, `Owner` na przykład w subskrypcji, aby utworzyć powiązanie i tożsamość.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Po włączeniu tożsamości zarządzanej przez program w klastrze AKS AzurePodIdentityException o nazwie AKS- *dodatek-Exception* zostanie dodany do przestrzeni nazw *polecenia-system* . AzurePodIdentityException umożliwia zasobnikom z niektórymi etykietami dostęp do punktu końcowego platformy Azure Instance Metadata Service (IMDS) bez przechwycenia przez serwer tożsamości zarządzanej przez węzeł (NMI). *AKS-dodatek-Exception* zezwala na korzystanie z aksów pierwszej strony, takich jak tożsamość zarządzana przez usługi AAD ze względu na to, że nie trzeba ręcznie konfigurować AzurePodIdentityException. Opcjonalnie można dodawać, usuwać i aktualizować AzurePodIdentityException przy użyciu `az aks pod-identity exception add` , `az aks pod-identity exception delete` , `az aks pod-identity exception update` , lub `kubectl` .

## <a name="run-a-sample-application"></a>Uruchamianie przykładowej aplikacji

W przypadku korzystania z tożsamości zarządzanej w usłudze AAD pod musi być *aadpodidbinding* etykieta o wartości zgodnej z selektorem z *AzureIdentityBinding*. Aby uruchomić przykładową aplikację przy użyciu tożsamości zarządzanej w usłudze AAD pod, Utwórz `demo.yaml` plik z następującą zawartością. Zastąp *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* i *IDENTITY_RESOURCE_GROUP* wartościami z poprzednich kroków. Zastąp *SUBSCRIPTION_ID* identyfikatorem subskrypcji.

> [!NOTE]
> W poprzednich krokach zostały utworzone zmienne *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* i *IDENTITY_RESOURCE_GROUP* . Możesz użyć polecenia, takiego jak `echo` , aby wyświetlić wartość ustawioną dla zmiennych, na przykład `echo $IDENTITY_NAME` .

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Należy zauważyć, że definicja pod ma etykietę *aadpodidbinding* o wartości odpowiadającej nazwie tożsamości pod, która została uruchomiona `az aks pod-identity add` w poprzednim kroku.

Wdróż w tej `demo.yaml` samej przestrzeni nazw, w której znajduje się tożsamość pod, przy użyciu `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Sprawdź, czy Przykładowa aplikacja została pomyślnie uruchomiona przy użyciu polecenia `kubectl logs` .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Sprawdź, czy dzienniki pokazują, że token został pomyślnie pobrany i operacja *pobrania* zakończyła się pomyślnie.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Czyszczenie

Aby usunąć tożsamość zarządzaną usługi AAD pod z klastra, Usuń przykładową aplikację i tożsamość pod z klastra. Następnie usuń tożsamość.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [zarządzane tożsamości dla zasobów platformy Azure][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create
