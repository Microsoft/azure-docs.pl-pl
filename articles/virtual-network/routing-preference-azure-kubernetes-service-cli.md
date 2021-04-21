---
title: Konfigurowanie preferencji routingu dla usługi Azure Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak skonfigurować klaster usługi AKS przy użyciu preferencji routingu przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 9eaad12e254150109498be0fac2f285f33a5965c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776595"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Konfigurowanie preferencji routingu dla klastra Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule pokazano, jak skonfigurować preferencję routingu za pośrednictwem sieci isp **(opcja** internetowa) dla klastra Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure. Preferencja routingu jest ustawiana przez utworzenie publicznego adresu IP typu preferencji routingu **Internet*", a następnie użycie go podczas tworzenia klastra usługi AKS.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów w regionie świadczenia usługi Azure **Wschodnie usa:**

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Utwórz publiczny adres IP z preferencją routingu **typu Internet** przy użyciu polecenia az [network public-ip create.](/cli/azure/network/public-ip#az_network_public_ip_create)

Następujące polecenie tworzy nowy publiczny adres IP z **preferencją routingu** internetowego w regionie świadczenia usługi Azure **Wschodnie usa.**

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  Obecnie preferencja routingu obsługuje tylko publiczne adresy IP IPV4.

## <a name="get-the-id-of-public-ip-address"></a>Uzyskiwanie identyfikatora publicznego adresu IP

Następujące polecenie zwraca identyfikator publicznego adresu IP utworzony w poprzedniej sekcji:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Tworzenie klastra Kubernetes z publicznym adresem IP

Następujące polecenie tworzy klaster usługi AKS z publicznym adresem IP utworzonym w poprzedniej sekcji:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Wdrożenie klastra usługi AKS może potrwać kilka minut.

Aby sprawdzić poprawność, wyszukaj publiczny adres IP utworzony we wcześniejszym kroku w Azure Portal. Zobaczysz, że adres IP jest skojarzony z usługą równoważenia obciążenia skojarzoną z klastrem Kubernetes, jak pokazano poniżej:

 ![Publiczny adres IP preferencji routingu dla usługi Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [preferencjach routingu w publicznych adresach IP.](routing-preference-overview.md) 
- [Skonfiguruj preferencję routingu dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.](configure-routing-preference-virtual-machine-cli.md)
