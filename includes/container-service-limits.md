---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: a42bba1b6524825aa571e4c18319b61b97829792
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96584482"
---
| Zasób | Limit |
| --- | :--- |
| Maksymalna liczba klastrów na subskrypcję | 1000 |
| Maksymalna liczba węzłów na klaster z zestawami dostępności maszyn wirtualnych i podstawową jednostką SKU Load Balancer  | 100 |
| Maksymalna liczba węzłów na klaster z jednostką SKU Virtual Machine Scale Sets i [Usługa Load Balancer w warstwie Standardowa][standard-load-balancer] | 1000 (100 węzłów na [pulę węzłów][node-pool]) |
| Maksymalna liczba zasobników na węzeł: [podstawowa sieć][basic-networking] z korzystającą wtyczki kubenet | 110 |
| Maksymalna liczba zasobników na węzeł: [Zaawansowane sieci][advanced-networking] przy użyciu interfejsu sieciowego kontenera platformy Azure | Wdrożenie za pomocą interfejsu wiersza polecenia platformy Azure: 30<sup>1</sup><br />Szablon Azure Resource Manager: 30<sup>1</sup><br />Wdrożenie za pomocą portalu: 30 |

<sup>1</sup> W przypadku wdrażania klastra usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Menedżer zasobów tę wartość można skonfigurować do 250 zasobników na węzeł. Nie można skonfigurować maksymalnej liczby zasobników na węzeł po wdrożeniu klastra AKS lub wdrożeniu klastra przy użyciu Azure Portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest