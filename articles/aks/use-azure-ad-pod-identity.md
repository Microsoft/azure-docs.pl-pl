---
title: Używanie Azure Active Directory tożsamości zarządzanych przez zasobniki w Azure Kubernetes Service (wersja zapoznawcza)
description: Dowiedz się, jak używać tożsamości zarządzanych przez zasobniki usługi AAD w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: f090f5e11688f35ce090bb07ec0d23530bf9d90e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777855"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Używanie Azure Active Directory tożsamości zarządzanych przez zasobniki w Azure Kubernetes Service (wersja zapoznawcza)

Azure Active Directory tożsamości zarządzane przez zasobniki skojarzą tożsamości zarządzane dla zasobów i tożsamości platformy [Azure][az-managed-identities] w usłudze Azure Active Directory (AAD) z zasobnikami przy użyciu elementów pierwotnych platformy Kubernetes. Administratorzy tworzą tożsamości i powiązania jako typy pierwotne platformy Kubernetes, które umożliwiają zasobnikom dostęp do zasobów platformy Azure, które korzystają z usługi AAD jako dostawcy tożsamości.

> [!NOTE]
> Jeśli masz istniejącą instalację programu AADPODIDENTITY, musisz usunąć istniejącą instalację. Włączenie tej funkcji oznacza, że składnik MIC nie jest potrzebny.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz mieć zainstalowany następujący zasób:

* Interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej
* Rozszerzenie `azure-preview` w wersji 0.5.5 lub nowszej

### <a name="limitations"></a>Ograniczenia

* W klastrze dozwolonych jest maksymalnie 200 tożsamości zasobników.
* Dla klastra dozwolonych jest maksymalnie 200 wyjątków tożsamości zasobników.
* Tożsamości zarządzane przez zasobniki są dostępne tylko w pulach węzłów systemu Linux.

### <a name="register-the-enablepodidentitypreview"></a>Zarejestruj `EnablePodIdentityPreview`

Zarejestruj `EnablePodIdentityPreview` funkcję:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Instalowanie interfejsu `aks-preview` wiersza polecenia platformy Azure

Potrzebne jest również rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* w wersji 0.4.64 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* za pomocą [polecenia az extension add.][az-extension-add] Możesz też zainstalować wszystkie dostępne aktualizacje za pomocą [polecenia az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-cni"></a>Tworzenie klastra usługi AKS za pomocą Azure CNI

> [!NOTE]
> Jest to domyślna zalecana konfiguracja

Utwórz klaster usługi AKS z włączoną Azure CNI tożsamości zarządzanej zasobnika. Następujące polecenia używają polecenia [az group create][az-group-create] do utworzenia grupy zasobów o nazwie *myResourceGroup* i [polecenia az aks create][az-aks-create] w celu utworzenia klastra usługi AKS o nazwie *myAKSCluster* w grupie zasobów *myResourceGroup.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

Użyj [az aks get-credentials,][az-aks-get-credentials] aby zalogować się do klastra usługi AKS. To polecenie również pobiera i konfiguruje `kubectl` certyfikat klienta na komputerze dewelopera.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Aktualizowanie istniejącego klastra usługi AKS za pomocą Azure CNI

Zaktualizuj istniejący klaster usługi AKS przy użyciu Azure CNI, aby uwzględnić tożsamość zarządzaną przez zasobnik.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --network-plugin azure
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Używanie wtyczki Sieci Kubenet z Azure Active Directory tożsamości zarządzane przez zasobnik 

> [!IMPORTANT]
> Uruchamianie tożsamości aad-pod-identity w klastrze z platformą Kubenet nie jest zalecaną konfiguracją ze względu na implikacje dotyczące bezpieczeństwa. Wykonaj kroki zaradcze i skonfiguruj zasady przed włączeniem tożsamości zasobnika aad w klastrze za pomocą usługi Kubenet.

## <a name="mitigation"></a>Ograniczanie ryzyka

Aby ograniczyć tę lukę w zabezpieczeniach na poziomie klastra, można użyć kontrolera danych OpenPolicyAgent wraz z zweryfikowaną wartością webhook programu Gatekeeper. Jeśli w klastrze jest już zainstalowany program Gatekeeper, dodaj klaster ConstraintTemplate typu K8sPSPCapabilities:

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Dodaj szablon, aby ograniczyć dudowanie zasobników z NET_RAW możliwością:

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

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Tworzenie klastra AKS z wtyczką sieci Kubenet

Utwórz klaster AKS z włączoną wtyczką sieci Kubenet i tożsamością zarządzaną przez zasobnik.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Aktualizowanie istniejącego klastra usługi AKS za pomocą wtyczki sieci Kubenet

Zaktualizuj istniejący klaster usługi AKS za pomocą wtyczki sieci Kubnet, aby uwzględnić tożsamość zarządzaną przez zasobnik.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Tworzenie tożsamości

Utwórz tożsamość za pomocą [narzędzia az identity create][az-identity-create] i ustaw *IDENTITY_CLIENT_ID* i *IDENTITY_RESOURCE_ID* zmienne.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Przypisywanie uprawnień dla tożsamości zarządzanej

Tożsamość *IDENTITY_CLIENT_ID* musi mieć uprawnienia Czytelnik w grupie zasobów zawierającej zestaw skalowania maszyn wirtualnych klastra usługi AKS.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Tworzenie tożsamości zasobnika

Utwórz tożsamość zasobnika dla klastra przy użyciu narzędzia `az aks pod-identity add` .

> [!IMPORTANT]
> Aby utworzyć tożsamość i powiązanie roli, musisz mieć odpowiednie uprawnienia, takie jak , w `Owner` subskrypcji.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Po włączeniu tożsamości zarządzanej zasobnika w klastrze usługi AKS do przestrzeni nazw *kube-system* dodawany jest wyjątek AzurePodIdentityException o nazwie *aks-addon-exception.* Element AzurePodIdentityException umożliwia zasobnikom z określonymi etykietami uzyskiwanie dostępu do punktu końcowego usługi Azure Instance Metadata Service (IMDS) bez przechwytywania przez serwer tożsamości zarządzanej przez węzeł (NMI). Wyjątek *aks-addon-exception* umożliwia działanie dodatków usługi AKS, takich jak tożsamość zarządzana przez zasobniki usługi AAD, bez konieczności ręcznego konfigurowania wyjątku AzurePodIdentityException. Opcjonalnie możesz dodać, usunąć i zaktualizować element AzurePodIdentityException przy użyciu `az aks pod-identity exception add` , `az aks pod-identity exception delete` , lub `az aks pod-identity exception update` `kubectl` .

## <a name="run-a-sample-application"></a>Uruchamianie przykładowej aplikacji

Aby zasobnik używać tożsamości zarządzanej przez zasobnik usługi AAD, zasobnik musi mieć etykietę *aadpodidbinding* z wartością, która odpowiada selektorowi z właściwości *AzureIdentityBinding.* Aby uruchomić przykładową aplikację przy użyciu tożsamości zarządzanej zasobnika usługi AAD, `demo.yaml` utwórz plik z następującą zawartością. Zastąp *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* i *IDENTITY_RESOURCE_GROUP* wartościami z poprzednich kroków. Zastąp *SUBSCRIPTION_ID* identyfikatorem subskrypcji.

> [!NOTE]
> W poprzednich krokach utworzono zmienne *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* i *IDENTITY_RESOURCE_GROUP* zmienne. Można użyć polecenia, takiego jak , aby wyświetlić wartość `echo` ustawioną dla zmiennych, na przykład `echo $IDENTITY_NAME` .

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

Zwróć uwagę, że definicja zasobnika ma etykietę *aadpodidbinding* z wartością, która odpowiada nazwie tożsamości zasobnika, która została uruchomiona `az aks pod-identity add` w poprzednim kroku.

Wdrażanie `demo.yaml` w tej samej przestrzeni nazw co tożsamość zasobnika przy użyciu elementu `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Sprawdź, czy przykładowa aplikacja została pomyślnie uruchomiona przy `kubectl logs` użyciu .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Sprawdź, czy dzienniki pokazują, że token został pomyślnie uzyskany, a operacja *GET* zakończyła się pomyślnie.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Czyszczenie

Aby usunąć tożsamość zarządzaną zasobnika usługi AAD z klastra, usuń z klastra przykładową aplikację i tożsamość zasobnika. Następnie usuń tożsamość.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.][az-managed-identities]

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
