---
title: Korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service
description: Dowiedz się, jak używać tożsamości zarządzanych w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: 836a5a003268a98dd8e63eed9bfdba741abcf4ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397049"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service

Obecnie klaster usługi Azure Kubernetes Service (AKS) (w odróżnieniu od dostawcy usług w chmurze Kubernetes) wymaga tożsamości do tworzenia dodatkowych zasobów, takich jak moduły równoważenia obciążenia i dyski zarządzane na platformie Azure. Ta tożsamość może być *tożsamością zarządzaną* lub jednostką *usługi*. W przypadku korzystania z jednostki [usługi](kubernetes-service-principal.md)należy podać jedną lub AKS w Twoim imieniu. Jeśli używasz tożsamości zarządzanej, zostanie ona utworzona automatycznie przez AKS. Klastry korzystające z jednostek usługi ostatecznie docierają do stanu, w którym należy przeprowadzić odnowienie jednostki usługi w celu zachowania działania klastra. Zarządzanie jednostkami usługi zwiększa złożoność, dlatego łatwiej jest używać zarządzanych tożsamości. Te same wymagania dotyczące uprawnień dotyczą zarówno jednostek głównych usługi, jak i zarządzanych tożsamości.

*Tożsamości zarządzane* są zasadniczo otoką dla podmiotów usługi i upraszczają zarządzanie nimi. Rotacja poświadczeń dla mnie odbywa się automatycznie co 46 dni zgodnie z ustawieniami domyślnymi Azure Active Directory. AKS używa typów zarządzanych tożsamości przypisanych do systemu i przypisanych przez użytkownika. Te tożsamości są obecnie niezmienne. Aby dowiedzieć się więcej, Przeczytaj o [zarządzanych tożsamościach dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz mieć zainstalowany następujący zasób:

- Interfejs wiersza polecenia platformy Azure w wersji 2.8.0 lub nowszej

## <a name="limitations"></a>Ograniczenia

* Klastry AKS z tożsamościami zarządzanymi można włączać tylko podczas tworzenia klastra.
* Nie można migrować istniejących klastrów AKS do zarządzanych tożsamości.
* W trakcie operacji **uaktualniania** klastra zarządzana tożsamość jest tymczasowo niedostępna.
* Dzierżawcy przeniesie/Migruj zarządzane Klastry obsługujące tożsamość nie są obsługiwane.
* Jeśli klaster został `aad-pod-identity` włączony, w WĘŹLE NMI (tożsamość zarządzana) są modyfikowane węzły dołączenie iptables do przechwytywania wywołań do punktu końcowego metadanych wystąpienia platformy Azure. Ta konfiguracja oznacza, że wszystkie żądania wysłane do punktu końcowego metadanych są przechwytywane przez NMI, nawet jeśli nie są używane `aad-pod-identity` . AzurePodIdentityException CRD można skonfigurować w taki sposób `aad-pod-identity` , aby informował, że wszelkie żądania kierowane do punktu końcowego metadanych pochodzące z elementu pod, które pasują do etykiet zdefiniowanych w CRD, powinny być przekazywane z serwerem proxy bez żadnego przetwarzania w NMI. Systemowy `kubernetes.azure.com/managedby: aks` wymiarname z etykietą w _polecenia —_ przestrzeń nazw systemu powinna zostać wykluczona w `aad-pod-identity` ramach konfigurowania AzurePodIdentityException CRD. Aby uzyskać więcej informacji, zobacz temat [wyłączanie usługi AAD-pod-Identity dla określonego elementu lub aplikacji](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md).
  Aby skonfigurować wyjątek, Zainstaluj polecenie [MIC-Exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Podsumowanie tożsamości zarządzanych

AKS używa kilku zarządzanych tożsamości dla wbudowanych usług i dodatków.

| Tożsamość                       | Nazwa    | Przypadek użycia | Uprawnienia domyślne | Korzystanie z własnej tożsamości
|----------------------------|-----------|----------|
| Płaszczyzna sterowania | niewidoczne | Używane przez AKS do zarządzanych zasobów sieciowych, w tym usług równoważenia obciążenia i AKS zarządzanych adresów IP | Rola współautora dla grupy zasobów węzła | Wersja zapoznawcza
| Kubelet | Nazwa klastra AKS — nieznanej obiektu agentpool | Uwierzytelnianie za pomocą Azure Container Registry (ACR) | NA (dla Kubernetes v 1.15 +) | Nie jest obecnie obsługiwana.
| Dodatek | AzureNPM | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | Monitorowanie sieci AzureCNI | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | azurepolicy (strażnik) | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | azurepolicy | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | Calico | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | Pulpit nawigacyjny | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | HTTPApplicationRouting | Zarządza wymaganymi zasobami sieciowymi | Rola czytnika dla grupy zasobów węzła, rola współautora dla strefy DNS | Nie
| Dodatek | Brama aplikacji przychodzącej | Zarządza wymaganymi zasobami sieciowymi| Rola współautora dla grupy zasobów węzła | Nie
| Dodatek | omsagent | Służy do wysyłania metryk AKS do Azure Monitor | Rola wydawcy metryk monitorowania | Nie
| Dodatek | Virtual-Node (ACIConnector) | Zarządza wymaganymi zasobami sieci dla Azure Container Instances (ACI) | Rola współautora dla grupy zasobów węzła | Nie
| Projekt OSS | AAD — pod — tożsamość | Umożliwia aplikacjom bezpieczne uzyskiwanie dostępu do zasobów w chmurze za pomocą usługi Azure Active Directory (AAD) | Nie dotyczy | Procedura przyznawania uprawnień w https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Tworzenie klastra AKS z tożsamościami zarządzanymi

Można teraz utworzyć klaster AKS z tożsamościami zarządzanymi przy użyciu następujących poleceń interfejsu wiersza polecenia.

Najpierw utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Następnie utwórz klaster AKS:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Pomyślne utworzenie klastra przy użyciu tożsamości zarządzanych zawiera informacje o profilu głównej usługi:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Użyj następującego polecenia, aby zbadać identyfikator objectid tożsamości zarządzanej płaszczyzny kontroli:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Wynik powinien wyglądać następująco:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Po utworzeniu klastra można wdrożyć obciążenia aplikacji w nowym klastrze i korzystać z niego w taki sam sposób, jak w przypadku klastrów AKS opartych na usłudze Service-Principal.

> [!NOTE]
> Aby utworzyć i korzystać z własnej sieci wirtualnej, statycznego adresu IP lub dołączonego dysku platformy Azure, na którym znajdują się zasoby poza grupą zasobów węzła roboczego, należy użyć PrincipalIDa przypisanej tożsamości zarządzanej przez system klastra w celu wykonania przypisania roli. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [delegowanie dostępu do innych zasobów platformy Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Wypełnianie uprawnień do tożsamości zarządzanej przez dostawcę w chmurze platformy Azure może potrwać do 60 minut.

Na koniec Uzyskaj poświadczenia, aby uzyskać dostęp do klastra:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="bring-your-own-control-plane-mi-preview"></a>Przesuwanie własnej płaszczyzny kontroli MI (wersja zapoznawcza)
Tożsamość niestandardowej płaszczyzny kontroli umożliwia dostęp do istniejącej tożsamości przed utworzeniem klastra. Pozwala to na takie scenariusze, jak używanie niestandardowej sieci wirtualnej lub niepowiązanego typu UDR z tożsamością zarządzaną.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Wymagane są następujące zasoby:
- Interfejs wiersza polecenia platformy Azure w wersji 2.9.0 lub nowszej
- Rozszerzenie AKS-Preview 0.4.57

Ograniczenia dotyczące przesuwania własnej płaszczyzny kontroli MI (wersja zapoznawcza):
* Azure Government nie jest obecnie obsługiwana.
* Nie jest to obecnie obsługiwane.

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu jako **zarejestrowanego**może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację `Microsoft.ContainerService` dostawcy zasobów przy użyciu polecenia [AZ Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Jeśli nie masz jeszcze tożsamości zarządzanej, należy to zrobić i utworzyć ją na przykład za pomocą polecenia [AZ Identity CLI][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Wynik powinien wyglądać następująco:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Jeśli zarządzana tożsamość jest częścią subskrypcji, możesz użyć [polecenia AZ Identity CLI][az-identity-list] , aby wykonać zapytanie.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Teraz można użyć następującego polecenia, aby utworzyć klaster z istniejącą tożsamością:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

Pomyślne utworzenie klastra przy użyciu tożsamości zarządzanych zawiera informacje o profilu Resourceidentity:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>Następne kroki
* Użyj [szablonów Azure Resource Manager (ARM) ][aks-arm-template] , aby utworzyć Klastry obsługujące tożsamość zarządzaną.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create&preserve-view=true
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list&preserve-view=true
