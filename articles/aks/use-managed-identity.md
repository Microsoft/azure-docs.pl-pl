---
title: Używanie tożsamości zarządzanych w programie Azure Kubernetes Service
description: Dowiedz się, jak używać tożsamości zarządzanych w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 58813504c5de057e06433b2e955931b37560d825
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600662"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Używanie tożsamości zarządzanych w programie Azure Kubernetes Service

Obecnie klaster usługi Azure Kubernetes Service (AKS) (w szczególności dostawca chmury Kubernetes) wymaga tożsamości w celu utworzenia dodatkowych zasobów, takich jak usługi równoważenia obciążenia i dyski zarządzane na platformie Azure. Ta tożsamość może być tożsamością *zarządzaną lub* *jednostką usługi.* Jeśli używasz [jednostki usługi](kubernetes-service-principal.md), musisz podać jedną z nich lub usługa AKS utworzy jednostkę w Twoim imieniu. Jeśli używasz tożsamości zarządzanej, zostanie ona utworzona automatycznie przez usługę AKS. Klastry korzystające z jednostki usługi ostatecznie osiągają stan, w którym należy odnowić jednostkę usługi, aby klaster działał. Zarządzanie jednostkami usługi zwiększa złożoność, dlatego łatwiej jest używać tożsamości zarządzanych. Te same wymagania dotyczące uprawnień dotyczą zarówno jednostki usługi, jak i tożsamości zarządzanych.

*Tożsamości zarządzane są* zasadniczo otoką wokół jednostki usługi i upraszczają zarządzanie nimi. Rotacja poświadczeń dla usługi migrowania odbywa się automatycznie co 46 dni zgodnie Azure Active Directory domyślne. Usługi AKS są używane zarówno typy tożsamości zarządzanych przypisane przez system, jak i przypisane przez użytkownika. Te tożsamości są obecnie niezmienne. Aby dowiedzieć się więcej, przeczytaj o [tożsamościach zarządzanych dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz mieć zainstalowany następujący zasób:

- Interfejs wiersza polecenia platformy Azure w wersji 2.15.1 lub nowszej

## <a name="limitations"></a>Ograniczenia

* Przenoszenie/migrowanie klastrów z włączoną tożsamością zarządzaną nie jest obsługiwane.
* Jeśli klaster został włączony, zasobniki Node-Managed Identity (NMI) modyfikują tabelę iptable węzłów w celu przechwycenia wywołań do punktu końcowego metadanych `aad-pod-identity` wystąpienia platformy Azure. Ta konfiguracja oznacza, że każde żądanie do punktu końcowego metadanych jest przechwytywane przez usługę NMI, nawet jeśli zasobnik nie używa elementu `aad-pod-identity` . Definicję CRD elementu AzurePodIdentityException można skonfigurować w taki sposób, aby wszystkie żądania do punktu końcowego metadanych pochodzące z zasobnika, który pasuje do etykiet zdefiniowanych w crd, były proxied bez przetwarzania w usłudze `aad-pod-identity` NMI. Zasobniki systemowe z etykietą w przestrzeni nazw kube-system powinny zostać wykluczone przez skonfigurowanie `kubernetes.azure.com/managedby: aks`  `aad-pod-identity` crd elementu AzurePodIdentityException. Aby uzyskać więcej informacji, [zobacz Disable aad-pod-identity for aad-pod-identity for a specific pod or application (Wyłączanie tożsamości zasobnika aad-pod-identity dla określonego zasobnika lub aplikacji).](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Aby skonfigurować wyjątek, zainstaluj [wyjątek mikrofonu YAML.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="summary-of-managed-identities"></a>Podsumowanie tożsamości zarządzanych

W przypadku wbudowanych usług i dodatków usługi AKS jest używana kilka tożsamości zarządzanych.

| Tożsamość                       | Nazwa    | Przypadek użycia | Uprawnienia domyślne | Bring your own identity (Przyniesienie własnej tożsamości)
|----------------------------|-----------|----------|
| Płaszczyzna sterowania | niewidoczne | Używane przez składniki płaszczyzny sterowania usługi AKS do zarządzania zasobami klastra, w tym usługami równoważenia obciążenia dla danych przychodzących, publicznymi ip zarządzanymi przez usługę AKS i operacjami autoskalowania klastra | Rola współautora dla grupy zasobów node | Obsługiwane
| Kubelet | Nazwa klastra usługi AKS — agentpool | Uwierzytelnianie za Azure Container Registry (ACR) | NA (dla kubernetes v1.15+) | Nie jest obecnie obsługiwana.
| Dodatek | AzureNPM | Nie jest wymagana żadna tożsamość | NA | Nie
| Dodatek | Monitorowanie sieci za pomocą interfejsu AzureCNI | Nie jest wymagana żadna tożsamość | NA | Nie
| Dodatek | azure-policy (gatekeeper) | Nie jest wymagana żadna tożsamość | NA | Nie
| Dodatek | azure-policy | Nie jest wymagana żadna tożsamość | NA | Nie
| Dodatek | Calico | Nie jest wymagana żadna tożsamość | NA | Nie
| Dodatek | Pulpit nawigacyjny | Nie jest wymagana żadna tożsamość | NA | Nie
| Dodatek | HTTPApplicationRouting | Zarządza wymaganymi zasobami sieci | Rola czytelnika dla grupy zasobów węzła, rola współautora dla strefy DNS | Nie
| Dodatek | Brama aplikacji dla danych przychodzących | Zarządza wymaganymi zasobami sieci| Rola współautora dla grupy zasobów węzła | Nie
| Dodatek | omsagent | Służy do wysyłania metryk AKS do Azure Monitor | Rola wydawcy metryk monitorowania | Nie
| Dodatek | Virtual-Node (ACIConnector) | Zarządza wymaganymi zasobami sieci dla Azure Container Instances (ACI) | Rola współautora dla grupy zasobów węzła | Nie
| Projekt OSS | aad-pod-identity | Umożliwia aplikacjom bezpieczne uzyskiwanie dostępu do zasobów w chmurze Azure Active Directory (AAD) | NA | Kroki udzielania uprawnień na stronie https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Tworzenie klastra usługi AKS przy użyciu tożsamości zarządzanych

Teraz można utworzyć klaster usługi AKS z tożsamościami zarządzanymi przy użyciu następujących poleceń interfejsu wiersza polecenia.

Najpierw utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Następnie utwórz klaster usługi AKS:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Po utworzeniu klastra można wdrożyć obciążenia aplikacji w nowym klastrze i wchodzić z nim w interakcje tak samo jak z klastrami AKS opartymi na usłudze.

Na koniec pobierz poświadczenia w celu uzyskania dostępu do klastra:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>Aktualizowanie klastra usługi AKS do tożsamości zarządzanych (wersja zapoznawcza)

Teraz możesz zaktualizować klaster usługi AKS, który aktualnie pracuje z jednostkami usługi, aby pracować z tożsamościami zarządzanymi, używając następujących poleceń interfejsu wiersza polecenia.

Najpierw zarejestruj flagę funkcji dla tożsamości przypisanej przez system:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Zaktualizuj tożsamość przypisaną przez system:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Zarejestruj flagę funkcji dla tożsamości przypisanej przez użytkownika:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Zaktualizuj tożsamość przypisaną przez użytkownika:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Po zaktualizowaniu tożsamości przypisanej przez system lub przypisanej przez użytkownika do tożsamości zarządzanej wykonaj w węzłach wykonanie operacji w celu ukończenia `az aks nodepool upgrade --node-image-only` aktualizacji tożsamości zarządzanej.

## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>Uzyskiwanie przypisanej przez system tożsamości zarządzanej dla klastra usługi AKS i korzystanie z tej tożsamości

Upewnij się, że klaster usługi AKS używa tożsamości zarządzanej za pomocą następującego polecenia interfejsu wiersza polecenia:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

Jeśli klaster używa tożsamości zarządzanych, zobaczysz `clientId` wartość "msi". Zamiast tego klaster używający jednostki usługi będzie wyświetlać identyfikator obiektu. Na przykład: 

```output
{
  "clientId": "msi"
}
```

Po sprawdzeniu, czy klaster używa tożsamości zarządzanych, można znaleźć identyfikator obiektu tożsamości przypisanej przez system płaszczyzny sterowania za pomocą następującego polecenia:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> W przypadku tworzenia i używania własnej sieci wirtualnej, statycznego adresu IP lub dołączonego dysku platformy Azure, na którym zasoby znajdują się poza grupą zasobów węzła procesu roboczego, użyj wartości PrincipalID przypisanej przez system tożsamości zarządzanej klastra, aby wykonać przypisanie roli. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Delegowanie dostępu do innych zasobów platformy Azure.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> Wypełnianie uprawnień dla tożsamości zarządzanej klastra używanej przez dostawcę usług w chmurze platformy Azure może potrwać 60 minut.


## <a name="bring-your-own-control-plane-mi"></a>Migrowanie do własnej płaszczyzny sterowania
Tożsamość niestandardowej płaszczyzny sterowania umożliwia uzyskanie dostępu do istniejącej tożsamości przed utworzeniem klastra. Ta funkcja umożliwia korzystanie ze scenariuszy, takich jak użycie niestandardowej sieci wirtualnej lub typu outboundType UDR ze wstępnie utworzoną tożsamością zarządzaną.

Musisz mieć zainstalowany interfejs wiersza polecenia platformy Azure w wersji 2.15.1 lub nowszej.

### <a name="limitations"></a>Ograniczenia
* Azure Government nie jest obecnie obsługiwana.
* Azure (Chiny) — 21Vianet nie jest obecnie obsługiwana.

Jeśli nie masz jeszcze tożsamości zarządzanej, musisz utworzyć tożsamość, na przykład przy użyciu polecenia [az identity CLI][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Wynik powinien wyglądać tak:

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

Jeśli tożsamość zarządzana jest częścią subskrypcji, możesz użyć polecenia [az identity CLI,][az-identity-list] aby ją odpytać.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Teraz możesz użyć następującego polecenia, aby utworzyć klaster z istniejącą tożsamością:

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

Pomyślne utworzenie klastra przy użyciu własnych tożsamości zarządzanych zawiera te informacje o profilu userAssignedIdentities:

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
* Tworzenie [klastrów z włączoną obsługą tożsamości zarządzanej przy ][aks-arm-template] użyciu szablonów usługi Azure Resource Manager (ARM).

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-list]: /cli/azure/identity#az-identity-list
