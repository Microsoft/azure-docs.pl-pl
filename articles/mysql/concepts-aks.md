---
title: Nawiązywanie połączenia z usługą Azure Kubernetes — Azure Database for MySQL
description: Dowiedz się więcej na temat łączenia usługi Azure Kubernetes z usługą Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: bc40903de09ebfe04858c631eee0233561784418
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546912"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Łączenie usługi Azure Kubernetes i Azure Database for MySQL

Usługa Azure Kubernetes Service (AKS) udostępnia zarządzany klaster Kubernetes, którego można użyć na platformie Azure. Poniżej przedstawiono kilka opcji, które należy wziąć pod uwagę podczas korzystania z AKS i Azure Database for MySQL razem w celu utworzenia aplikacji.


## <a name="accelerated-networking"></a>Wydajniejsze sieci
Korzystaj z szybszych maszyn wirtualnych z obsługą sieci w klastrze AKS. Po włączeniu przyspieszonej sieci na maszynie wirtualnej istnieje małe opóźnienia, mniejsze wahania i obniżenie użycia procesora na maszynie wirtualnej. Dowiedz się więcej o tym, jak działa przyspieszone sieci, obsługiwane wersje systemów operacyjnych i obsługiwane wystąpienia maszyn wirtualnych dla systemu [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Od listopada 2018 AKS obsługuje przyspieszone sieci na tych obsługiwanych wystąpieniach maszyn wirtualnych. Przyspieszona sieć jest domyślnie włączona w nowych klastrach AKS, które korzystają z tych maszyn wirtualnych.

Możesz sprawdzić, czy klaster AKS ma przyspieszone sieci:
1. Przejdź do Azure Portal i wybierz klaster AKS.
2. Wybierz kartę Properties (Właściwości).
3. Skopiuj nazwę **grupy zasobów infrastruktury** .
4. Użyj paska wyszukiwania portalu, aby zlokalizować i otworzyć grupę zasobów infrastruktury.
5. Wybierz maszynę wirtualną w tej grupie zasobów.
6. Przejdź do karty **Sieć** maszyny wirtualnej.
7. Sprawdź, czy w **sieci** jest włączona opcja "włączone".

Lub za pośrednictwem interfejsu wiersza polecenia platformy Azure, korzystając z następujących dwóch poleceń:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Dane wyjściowe będą wygenerowanej grupy zasobów utworzonej przez AKSą zawierającą interfejs sieciowy. Wypełnij nazwę "nodeResourceGroup" i użyj jej w następnym poleceniu. **EnableAcceleratedNetworking** będzie mieć wartość true lub false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="next-steps"></a>Następne kroki
- [Tworzenie klastra usługi Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
- Dowiedz się [, jak zainstalować WordPress z wykresu Helm przy użyciu OSBA i Azure Database for MySQL](../aks/index.yml)