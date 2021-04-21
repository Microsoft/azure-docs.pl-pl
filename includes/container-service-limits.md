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
ms.openlocfilehash: da22991b9a1c4b69d3a3d6eb6f76b0925a6ad3d4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799809"
---
| Zasób                                                                                                           | Limit                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maksymalna liczba klastrów na subskrypcję                                                                                  | 5000                                                                                                                                                                                                        |
| Maksymalna liczba węzłów na klaster z zestawami dostępności maszyny wirtualnej i jednostką SKU Load Balancer Podstawowa                       | 100                                                                                                                                                                                                         |
| Maksymalna liczba węzłów na klaster z Virtual Machine Scale Sets i [usługa Load Balancer w warstwie Standardowa SKU][standard-load-balancer] | 1000 (we wszystkich [pulach węzłów)][node-pool]                                            |
| Maksymalna liczba pul węzłów na klaster                                                                                     | 100                                                                                  |
| Maksymalna liczba zasobników na węzeł: [podstawowa sieć z][basic-networking] kubenetem                                           | Wartość maksymalna: 250 <br /> Domyślna wartość interfejsu wiersza polecenia platformy Azure: 110 <br /> Azure Resource Manager szablonu domyślnego: 110 <br /> Azure Portal domyślne wdrożenie: 30          |
| Maksymalna liczba zasobników na węzeł: [Zaawansowane sieci z][advanced-networking] Azure Container Networking Interface        | Wartość maksymalna: 250 <br /> Ustawienie domyślne: 30                                                      |
| Otwieranie podglądu dodatku usługi AKS usługi Service Mesh (SYSTEM)                                                                          | Wersja klastra Kubernetes: 1.19+<sup>1</sup><br />Kontrolery WOLUMINU na klaster: 1<sup>1</sup><br />Zasobniki na kontroler JEDNOSTKI: 500<sup>1</sup><br />Konta usługi Kubernetes zarządzane przez użytkownika ZEA: 50<sup>1</sup> |

<sup>1</sup> Dodatek IT dla usługi AKS jest w wersji zapoznawczej i zostanie poddany dodatkowym ulepszaniom przed ogólną dostępnością. W fazie wersji zapoznawczej nie zaleca się przekraczania pokazanych limitów.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
