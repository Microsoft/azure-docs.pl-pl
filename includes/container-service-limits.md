---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 32edd880e5e455393e60c87f4f963d71a3e59a20
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555624"
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
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest