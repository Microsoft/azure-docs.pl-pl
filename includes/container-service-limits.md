---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 15e91e6f275c3a6ebe44690441404a38e8f61394
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732225"
---
| Zasób                                                                                                           | Limit                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maksymalna liczba klastrów na subskrypcję                                                                                  | 1000                                                                                                                                                                                                        |
| Maksymalna liczba węzłów na klaster przy użyciu zestawów dostępności maszyny wirtualnej i podstawowej jednostki Load Balancer SKU                       | 100                                                                                                                                                                                                         |
| Maksymalna liczba węzłów na klaster z Virtual Machine Scale Sets i [usługa Load Balancer w warstwie Standardowa SKU][standard-load-balancer] | 1000 (we wszystkich [pulach węzłów)][node-pool]                                            |
| Maksymalna liczba pul węzłów na klaster                                                                                     | 100                                                                                  |
| Maksymalna liczba zasobników w węźle: [Podstawowa sieć z][basic-networking] kubenetem                                           | Wartość maksymalna: 250 <br /> Domyślna wartość interfejsu wiersza polecenia platformy Azure: 110 <br /> Azure Resource Manager szablonu: 110 <br /> Azure Portal domyślne wdrożenie: 30          |
| Maksymalna liczba zasobników na węzeł: [Zaawansowane sieci z Azure Container Networking Interface][advanced-networking]        | Wartość maksymalna: 250 <br /> Wartość domyślna: 30                                                      |
| Wersja zapoznawcza dodatku AKS Open Service Mesh (SYSTEM)                                                                          | Wersja klastra Kubernetes: 1.19+<sup>1</sup><br />Kontrolery KLASTRÓW na klaster: 1<sup>1</sup><br />Zasobniki na kontroler JEDNOSTKI: 500<sup>1</sup><br />Konta usługi Kubernetes zarządzane przez firmę ICHT: 50<sup>1</sup> |

<sup>1</sup> Dodatek IT dla usługi AKS jest w wersji zapoznawczej i zostanie poddany dodatkowym ulepszaniom przed ogólną dostępnością. W fazie wersji zapoznawczej nie zaleca się przekraczania pokazanych limitów.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
