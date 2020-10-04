---
title: Nawiązywanie połączenia z Azure Database for PostgreSQL usługą Azure Kubernetes — pojedynczy serwer
description: Informacje na temat nawiązywania połączenia z usługą Azure Kubernetes Service (AKS) przy użyciu jednego serwera Azure Database for PostgreSQL
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.date: 07/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b7da2fcc1310f03f894e048089658f25be3a149
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708853"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Łączenie usługi Azure Kubernetes i Azure Database for PostgreSQL — pojedynczy serwer

Usługa Azure Kubernetes Service (AKS) udostępnia zarządzany klaster Kubernetes, którego można użyć na platformie Azure. Poniżej przedstawiono kilka opcji, które należy wziąć pod uwagę podczas korzystania z AKS i Azure Database for PostgreSQL razem w celu utworzenia aplikacji.


## <a name="accelerated-networking"></a>Wydajniejsze sieci
Korzystaj z szybszych maszyn wirtualnych z obsługą sieci w klastrze AKS. Po włączeniu przyspieszonej sieci na maszynie wirtualnej istnieje małe opóźnienia, mniejsze wahania i obniżenie użycia procesora na maszynie wirtualnej. Dowiedz się więcej o tym, jak działa przyspieszone sieci, obsługiwane wersje systemów operacyjnych i obsługiwane wystąpienia maszyn wirtualnych dla systemu [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Od listopada 2018 AKS obsługuje przyspieszone sieci na tych obsługiwanych wystąpieniach maszyn wirtualnych. Przyspieszona sieć jest domyślnie włączona w nowych klastrach AKS, które korzystają z tych maszyn wirtualnych.

Możesz sprawdzić, czy klaster AKS ma przyspieszone sieci:
1. Przejdź do Azure Portal i wybierz klaster AKS.
2. Wybierz kartę Properties (Właściwości).
3. Skopiuj nazwę **grupy zasobów infrastruktury**.
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


## <a name="connection-pooling"></a>Buforowanie połączeń
Połączenie pulę minimalizuje koszt i czas związany z tworzeniem i zamykaniem nowych połączeń z bazą danych. Pula jest kolekcją połączeń, które mogą być ponownie używane. 

Istnieje wiele pul połączeń, których można używać z PostgreSQL. Jednym z nich jest [PgBouncer](https://pgbouncer.github.io/). W Container Registry firmy Microsoft udostępniamy lekkie PgBouncer kontenerów, które mogą być używane w przyczepce do puli połączeń z AKS do Azure Database for PostgreSQL. Odwiedź [stronę centrum platformy Docker](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) , aby dowiedzieć się, jak uzyskać dostęp do tego obrazu i korzystać z niego. 


## <a name="next-steps"></a>Następne kroki
-  [Tworzenie klastra usługi Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
