---
title: plik dołączany
description: plik dołączany
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334629"
---
| Zasób | Limit |
| --- | :--- |
| Maksymalna liczba klastrów na subskrypcję | 100 |
| Maksymalna liczba węzłów na klaster z zestawami dostępności maszyn wirtualnych i podstawową jednostką SKU Load Balancer  | 100 |
| Maksymalna liczba węzłów na klaster z jednostką SKU Virtual Machine Scale Sets i [Usługa Load Balancer w warstwie Standardowa][standard-load-balancer] | 1000 (100 węzłów na [pulę węzłów][node-pool]) |
| Maksymalna liczba zasobników na węzeł: [podstawowa sieć][basic-networking] z korzystającą wtyczki kubenet | 110 |
| Maksymalna liczba zasobników na węzeł: [Zaawansowane sieci][advanced-networking] przy użyciu interfejsu sieciowego kontenera platformy Azure | Wdrożenie za pomocą interfejsu wiersza polecenia platformy Azure: 30<sup>1</sup><br />Szablon Azure Resource Manager: 30<sup>1</sup><br />Wdrożenie za pomocą portalu: 30 |

<sup>1</sup> W przypadku wdrażania klastra usługi Azure Kubernetes Service (AKS) przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Menedżer zasobów tę wartość można skonfigurować do 250 zasobników na węzeł. Nie można skonfigurować maksymalnej liczby zasobników na węzeł po wdrożeniu klastra AKS lub wdrożeniu klastra przy użyciu Azure Portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
