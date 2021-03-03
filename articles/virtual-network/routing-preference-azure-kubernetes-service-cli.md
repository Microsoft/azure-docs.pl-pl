---
title: Konfigurowanie preferencji routingu dla usługi Azure Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak skonfigurować klaster AKS z preferencją routingu za pomocą interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680343"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Konfigurowanie preferencji routingu dla klastra Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób konfigurowania preferencji routingu za pośrednictwem sieci usługodawcy internetowego (opcja **internetowa** ) dla klastra Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure. Preferencja routingu jest ustawiana przez utworzenie publicznego adresu IP typu preferencji Routing * * Internet * * * *, a następnie użycie go podczas tworzenia klastra AKS.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.49 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów w regionie platformy Azure **Wschodnie stany USA** :

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Utwórz publiczny adres IP z preferencją routingu typu **internetowego** za pomocą polecenia [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create).

Następujące polecenie tworzy nowy publiczny adres IP z preferencją routingu **internetowego** w regionie platformy Azure **Wschodnie stany USA** .

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
>  Obecnie preferencje routingu obsługują tylko publiczne adresy IP w protokole IPV4.

## <a name="get-the-id-of-public-ip-address"></a>Pobieranie identyfikatora publicznego adresu IP

Następujące polecenie zwraca publiczny adres IP utworzony w poprzedniej sekcji:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Utwórz klaster Kubernetes z publicznym adresem IP

Następujące polecenie tworzy klaster AKS z publicznym adresem IP utworzonym w poprzedniej sekcji:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Wdrożenie klastra AKS może zająć kilka minut.

Aby sprawdzić poprawność, Wyszukaj publiczny adres IP utworzony we wcześniejszej części Azure Portal, zostanie wyświetlony adres IP skojarzony z modułem równoważenia obciążenia skojarzonym z klastrem Kubernetes, jak pokazano poniżej:

 ![Publiczny adres IP preferencji routingu dla Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [preferencjach routingu w publicznych adresach IP](routing-preference-overview.md). 
- [Skonfiguruj preferencję routingu dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](configure-routing-preference-virtual-machine-cli.md).

