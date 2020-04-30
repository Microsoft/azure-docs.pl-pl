---
title: Korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service
description: Dowiedz się, jak używać tożsamości zarządzanych w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112970"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service

Obecnie klaster usługi Azure Kubernetes Service (AKS) (w odróżnieniu od dostawcy usług w chmurze Kubernetes) wymaga tożsamości do tworzenia dodatkowych zasobów, takich jak moduły równoważenia obciążenia i dyski zarządzane na platformie Azure, Ta tożsamość może być *tożsamością zarządzaną* lub jednostką *usługi*. W przypadku korzystania z jednostki [usługi](kubernetes-service-principal.md)należy podać jedną lub AKS w Twoim imieniu. Jeśli używasz tożsamości zarządzanej, zostanie ona utworzona automatycznie przez AKS. Klastry korzystające z jednostek usługi ostatecznie docierają do stanu, w którym należy przeprowadzić odnowienie jednostki usługi w celu zachowania działania klastra. Zarządzanie jednostkami usługi zwiększa złożoność, dlatego łatwiej jest używać zarządzanych tożsamości. Te same wymagania dotyczące uprawnień dotyczą zarówno jednostek głównych usługi, jak i zarządzanych tożsamości.

*Tożsamości zarządzane* są zasadniczo otoką dla podmiotów usługi i upraszczają zarządzanie nimi. Aby dowiedzieć się więcej, Przeczytaj o [zarządzanych tożsamościach dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS tworzy dwie zarządzane tożsamości:

- **Tożsamość zarządzana przypisana przez system**: tożsamość wykorzystywana przez dostawcę chmury Kubernetes do tworzenia zasobów platformy Azure w imieniu użytkownika. Cykl życia tożsamości przypisanej do systemu jest powiązany z tym klastrem. Tożsamość jest usuwana po usunięciu klastra.
- **Tożsamość zarządzana przypisana przez użytkownika**: tożsamość, która jest używana na potrzeby autoryzacji w klastrze. Na przykład tożsamość przypisana przez użytkownika służy do autoryzacji AKS do używania rejestrów kontenerów platformy Azure (rekordami ACR) lub do autoryzacji kubelet do uzyskiwania metadanych z platformy Azure.

Dodatki również są uwierzytelniane przy użyciu tożsamości zarządzanej. Dla każdego dodatku zarządzana tożsamość jest tworzona przez AKS i obowiązuje w okresie istnienia dodatku. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Musisz mieć zainstalowany następujący zasób:

- Interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej

## <a name="create-an-aks-cluster-with-managed-identities"></a>Tworzenie klastra AKS z tożsamościami zarządzanymi

Można teraz utworzyć klaster AKS z tożsamościami zarządzanymi przy użyciu następujących poleceń interfejsu wiersza polecenia.

Najpierw utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Następnie utwórz klaster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Pomyślne utworzenie klastra przy użyciu tożsamości zarządzanych zawiera informacje o profilu głównej usługi:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> Aby utworzyć i korzystać z własnej sieci wirtualnej, statycznego adresu IP lub dołączonego dysku platformy Azure, na którym znajdują się zasoby spoza grupy zasobów MC_ *, należy użyć PrincipalIDa przypisanej tożsamości zarządzanej, aby wykonać przypisanie roli. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [delegowanie dostępu do innych zasobów platformy Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Wypełnianie uprawnień do tożsamości zarządzanej przez dostawcę w chmurze platformy Azure może potrwać do 60 minut.

Na koniec Uzyskaj poświadczenia, aby uzyskać dostęp do klastra:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Klaster zostanie utworzony w ciągu kilku minut. Następnie można wdrożyć obciążenia aplikacji w nowym klastrze i korzystać z nich w taki sam sposób, jak w przypadku klastrów AKS opartych na głównej usłudze.

> [!IMPORTANT]
>
> - Klastry AKS z tożsamościami zarządzanymi można włączać tylko podczas tworzenia klastra.
> - Istniejących klastrów AKS nie można zaktualizować ani uaktualnić, aby umożliwić zarządzanie tożsamościami.
