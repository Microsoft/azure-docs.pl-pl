---
title: Używanie tożsamości zarządzanych w usłudze Azure Kubernetes
description: Dowiedz się, jak używać tożsamości zarządzanych w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 8a150e2f197f24db5da331195290d11ad925c47e
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392651"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Używanie tożsamości zarządzanych w usłudze Azure Kubernetes

Obecnie klaster usługi Azure Kubernetes Service (AKS) (w szczególności dostawca chmury Kubernetes) wymaga i tożsamości do tworzenia dodatkowych zasobów, takich jak moduły równoważenia obciążenia i dyski zarządzane na platformie Azure, ta tożsamość może być *tożsamością zarządzaną* lub *jednostką usługi.* Jeśli używasz [jednostki usługi,](kubernetes-service-principal.md)należy podać jeden lub AKS tworzy jeden w Twoim imieniu. Jeśli używasz tożsamości zarządzanej, zostanie ona utworzona przez AKS automatycznie. Klastry przy użyciu jednostki usługi po pewnym czasie osiągnąć stan, w którym jednostki usługi muszą być odnawiane, aby utrzymać klastra pracy. Zarządzanie jednostkami usługi zwiększa złożoność, dlatego jest łatwiejsze do korzystania z zarządzanych tożsamości zamiast. Te same wymagania dotyczące uprawnień dotyczą zarówno podmiotów usługi, jak i tożsamości zarządzanych.

*Tożsamości zarządzane* są zasadniczo otoki wokół podmiotów usługi i uczynić ich zarządzanie prostsze. Aby dowiedzieć się więcej, przeczytaj o [tożsamościach zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Usługa AKS tworzy dwie tożsamości zarządzane:

- **Tożsamość zarządzana przypisana do systemu:** tożsamość używana przez dostawcę chmury Kubernetes do tworzenia zasobów platformy Azure w imieniu użytkownika. Cykl życia tożsamości przypisanej do systemu jest powiązany z cyklem życia klastra. Tożsamość jest usuwana po usunięciu klastra.
- **Tożsamość zarządzana przypisana przez użytkownika:** tożsamość używana do autoryzacji w klastrze. Na przykład tożsamość przypisana przez użytkownika jest używana do autoryzowania usługi AKS do używania rejestratorów kontenerów platformy Azure (ARS) lub do autoryzowania kubelet w celu uzyskania metadanych z platformy Azure.

Dodatki są również uwierzytelniane przy użyciu tożsamości zarządzanej. Dla każdego dodatku zarządzana tożsamość jest tworzona przez usługi AKS i trwa przez cały okres ważności dodatku. Aby utworzyć własną sieć wirtualną, statyczny adres IP lub dołączony dysk platformy Azure, na którym zasoby znajdują się poza grupą zasobów MC_*, użyj identyfikatora głównego klastra, aby wykonać przypisanie roli. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Delegowanie dostępu do innych zasobów platformy Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Musi być zainstalowany następujący zasób:

- Interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej

## <a name="create-an-aks-cluster-with-managed-identities"></a>Tworzenie klastra AKS z tożsamościami zarządzanymi

Teraz można utworzyć klaster AKS z zarządzanymi tożsamościami przy użyciu następujących poleceń interfejsu wiersza polecenia.

Najpierw utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Następnie utwórz klaster usługi AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Pomyślne utworzenie klastra przy użyciu tożsamości zarządzanych zawiera informacje o profilu jednostki usługi:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

Na koniec uzyskaj poświadczenia dostępu do klastra:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Klaster zostanie utworzony w ciągu kilku minut. Następnie można wdrożyć obciążenia aplikacji do nowego klastra i współdziałać z nim tak samo, jak to zrobić z klastrów AKS opartych na jednostkach usług.

> [!IMPORTANT]
>
> - Klastry AKS z tożsamościami zarządzanymi można włączyć tylko podczas tworzenia klastra.
> - Istniejących klastrów AKS nie można zaktualizować ani uaktualnić, aby włączyć tożsamości zarządzane.
