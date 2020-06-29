---
title: Korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service
description: Dowiedz się, jak używać tożsamości zarządzanych w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/04/2020
ms.author: mlearned
ms.openlocfilehash: 5854f512eb5a85430fbf95499274187a6d66016c
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445274"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service

Obecnie klaster usługi Azure Kubernetes Service (AKS) (w odróżnieniu od dostawcy usług w chmurze Kubernetes) wymaga tożsamości do tworzenia dodatkowych zasobów, takich jak moduły równoważenia obciążenia i dyski zarządzane na platformie Azure. Ta tożsamość może być *tożsamością zarządzaną* lub jednostką *usługi*. W przypadku korzystania z jednostki [usługi](kubernetes-service-principal.md)należy podać jedną lub AKS w Twoim imieniu. Jeśli używasz tożsamości zarządzanej, zostanie ona utworzona automatycznie przez AKS. Klastry korzystające z jednostek usługi ostatecznie docierają do stanu, w którym należy przeprowadzić odnowienie jednostki usługi w celu zachowania działania klastra. Zarządzanie jednostkami usługi zwiększa złożoność, dlatego łatwiej jest używać zarządzanych tożsamości. Te same wymagania dotyczące uprawnień dotyczą zarówno jednostek głównych usługi, jak i zarządzanych tożsamości.

*Tożsamości zarządzane* są zasadniczo otoką dla podmiotów usługi i upraszczają zarządzanie nimi. Rotacja poświadczeń dla mnie odbywa się automatycznie co 46 dni zgodnie z ustawieniami domyślnymi Azure Active Directory. AKS używa typów zarządzanych tożsamości przypisanych do systemu i przypisanych przez użytkownika. Te tożsamości są obecnie niezmienne. Aby dowiedzieć się więcej, Przeczytaj o [zarządzanych tożsamościach dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Musisz mieć zainstalowany następujący zasób:

- Interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej

## <a name="limitations"></a>Ograniczenia

* Korzystanie z własnych tożsamości zarządzanych nie jest obecnie obsługiwane.
* Klastry AKS z tożsamościami zarządzanymi można włączać tylko podczas tworzenia klastra.
* Istniejących klastrów AKS nie można zaktualizować ani uaktualnić, aby umożliwić zarządzanie tożsamościami.
* W trakcie operacji **uaktualniania** klastra zarządzana tożsamość jest tymczasowo niedostępna.

## <a name="summary-of-managed-identities"></a>Podsumowanie tożsamości zarządzanych

AKS używa kilku zarządzanych tożsamości dla wbudowanych usług i dodatków.

| Tożsamość                       | Nazwa    | Przypadek użycia | Uprawnienia domyślne | Korzystanie z własnej tożsamości
|----------------------------|-----------|----------|
| Płaszczyzna kontroli | niewidoczne | Używane przez AKS do zarządzania zasobami sieciowymi, np. tworzenia modułu równoważenia obciążenia dla ruchu przychodzącego, publicznego adresu IP itp.| Rola współautora dla grupy zasobów węzła | Nie jest obecnie obsługiwana.
| Kubelet | Nazwa klastra AKS — nieznanej obiektu agentpool | Uwierzytelnianie za pomocą Azure Container Registry (ACR) | Rola czytnika dla grupy zasobów węzła | Nie jest obecnie obsługiwana.
| Dodatek | AzureNPM | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | Monitorowanie sieci AzureCNI | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | azurepolicy (strażnik) | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | azurepolicy | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | Calico | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | Pulpit nawigacyjny | Żadna tożsamość nie jest wymagana | Nie dotyczy | Nie
| Dodatek | HTTPApplicationRouting | Zarządza wymaganymi zasobami sieciowymi | Rola czytnika dla grupy zasobów węzła, rola współautora dla strefy DNS | Nie
| Dodatek | Brama aplikacji przychodzącej | Zarządza wymaganymi zasobami sieciowymi| Rola współautora dla grupy zasobów węzła | Nie
| Dodatek | omsagent | Służy do wysyłania metryk AKS do Azure Monitor | Rola wydawcy metryk monitorowania | Nie
| Dodatek | Węzeł wirtualny (ACIConnector) | Zarządza wymaganymi zasobami sieci dla Azure Container Instances (ACI) | Rola współautora dla grupy zasobów węzła | Nie


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

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Użyj następującego polecenia, aby zbadać identyfikator objectid tożsamości zarządzanej płaszczyzny kontroli:

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

Wynik powinien wyglądać następująco:

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> Aby utworzyć i korzystać z własnej sieci wirtualnej, statycznego adresu IP lub dołączonego dysku platformy Azure, na którym znajdują się zasoby poza grupą zasobów węzła roboczego, należy użyć PrincipalIDa przypisanej tożsamości zarządzanej przez system klastra w celu wykonania przypisania roli. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [delegowanie dostępu do innych zasobów platformy Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Wypełnianie uprawnień do tożsamości zarządzanej przez dostawcę w chmurze platformy Azure może potrwać do 60 minut.

Na koniec Uzyskaj poświadczenia, aby uzyskać dostęp do klastra:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Klaster zostanie utworzony w ciągu kilku minut. Następnie można wdrożyć obciążenia aplikacji w nowym klastrze i korzystać z nich w taki sam sposób, jak w przypadku klastrów AKS opartych na głównej usłudze.
