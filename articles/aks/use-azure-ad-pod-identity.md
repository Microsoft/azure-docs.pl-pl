---
title: Korzystanie z Azure Active Directory tożsamości zarządzanych w usłudze Azure Kubernetes Service (wersja zapoznawcza)
description: Dowiedz się, jak za pomocą usługi AAD zarządzane tożsamości zarządzane w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: f3d0db5b085fcdb9a24310cb2fe310d390b1790a
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574377"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Korzystanie z Azure Active Directory tożsamości zarządzanych w usłudze Azure Kubernetes Service (wersja zapoznawcza)

Azure Active Directory tożsamości zarządzanych przez program używają prymitywów Kubernetes do kojarzenia [zarządzanych tożsamości dla zasobów platformy Azure][az-managed-identities] i tożsamości w usłudze Azure Active Directory (AAD) przy użyciu zasobników. Administratorzy tworzą tożsamości i powiązania jako elementy podstawowe Kubernetes, które zezwalają na dostęp do zasobów platformy Azure korzystających z usługi AAD jako dostawcy tożsamości.

> [!NOTE]
> W przypadku istniejącej instalacji programu AADPODIDENTITY należy usunąć istniejącą instalację. Włączenie tej funkcji oznacza, że składnik MIC nie jest wymagany.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz mieć zainstalowany następujący zasób:

* Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
* `azure-preview`Rozszerzenie w wersji 0.5.5 lub nowszej

### <a name="limitations"></a>Ograniczenia

* Dla klastra można używać maksymalnie 200 tożsamości.
* Dla klastra można używać maksymalnie 200 wyjątków tożsamości.
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

## <a name="create-an-aks-cluster-with-azure-cni"></a>Tworzenie klastra AKS za pomocą usługi Azure CNI

> [!NOTE]
> Jest to domyślna konfiguracja zalecana

Tworzenie klastra AKS przy użyciu usługi Azure CNI i tożsamości zarządzanej pod kątem zarządzania. W poniższych poleceniach użyto polecenia [AZ Group Create][az-group-create] , aby utworzyć grupę zasobów o nazwie Moja *zasobów* i polecenie [AZ AKS Create][az-aks-create] , aby utworzyć klaster AKS o nazwie *myAKSCluster* w *grupie zasobów* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

Użyj [AZ AKS Get-Credentials][az-aks-get-credentials] , aby zalogować się do klastra AKS. To polecenie umożliwia również pobranie i skonfigurowanie `kubectl` certyfikatu klienta na komputerze deweloperskim.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Aktualizowanie istniejącego klastra AKS za pomocą usługi Azure CNI

Zaktualizuj istniejący klaster AKS za pomocą usługi Azure CNI, aby uwzględnić tożsamość zarządzaną pod względem tożsamości.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --network-plugin azure
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Korzystanie z wtyczki sieciowej korzystającą wtyczki kubenet z tożsamościami zarządzanymi Azure Active Directory pod 

> [!IMPORTANT]
> Uruchamianie usługi AAD-pod-Identity w klastrze z korzystającą wtyczki kubenet nie jest zalecaną konfiguracją z powodu implikacji zabezpieczeń. Wykonaj czynności zaradcze i skonfiguruj zasady przed włączeniem usługi AAD-pod-Identity w klastrze z korzystającą wtyczki kubenet.

## <a name="mitigation"></a>Ograniczanie ryzyka

Aby wyeliminować lukę w zabezpieczeniach na poziomie klastra, można użyć kontrolera OpenPolicyAgent Admission Control wraz z walidacją elementu webhook. Pod warunkiem, że masz strażnika już zainstalowany w klastrze, Dodaj ConstraintTemplate typu K8sPSPCapabilities:

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Dodaj szablon, aby ograniczyć jego duplikowanie z możliwością NET_RAW:

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Tworzenie klastra AKS za pomocą wtyczki sieciowej korzystającą wtyczki kubenet

Utwórz klaster AKS z wtyczką sieci korzystającą wtyczki kubenet i włączoną tożsamością zarządzaną pod.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Aktualizowanie istniejącego klastra AKS za pomocą wtyczki sieciowej korzystającą wtyczki kubenet

Zaktualizuj istniejący klaster AKS z wtyczką sieci Kubnet w celu uwzględnienia tożsamości zarządzanej pod.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
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

## <a name="assign-permissions-for-the-managed-identity"></a>Przypisywanie uprawnień dla tożsamości zarządzanej

Tożsamość zarządzana *IDENTITY_CLIENT_ID* musi mieć uprawnienia czytelnika w grupie zasobów zawierającej zestaw skalowania maszyn wirtualnych w klastrze AKS.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
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
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
