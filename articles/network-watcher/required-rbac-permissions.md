---
title: Uprawnienia usługi Azure RBAC wymagane do korzystania z funkcji
titleSuffix: Azure Network Watcher
description: Dowiedz się, które uprawnienia kontroli dostępu opartej na rolach platformy Azure są wymagane do pracy z możliwościami Network Watcher.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 47418b9c5235255ff7dbf4a1a151e51e4c9aba58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019673"
---
# <a name="azure-role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Uprawnienia kontroli dostępu opartej na rolach na platformie Azure wymagane do korzystania z funkcji Network Watcher

Kontrola dostępu oparta na rolach na platformie Azure (RBAC) umożliwia przypisywanie tylko określonych czynności do członków organizacji, których potrzebują do wykonania przypisanych im obowiązków. Aby można było korzystać z funkcji Network Watcher, konto, które logujesz się do platformy Azure za [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner)pomocą programu, musi być przypisane do wbudowanych ról współautor, [współautor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)lub [sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) lub przypisane do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , do której przypisano akcje wymienione dla każdej Network Watcher możliwości w poniższych sekcjach. Aby dowiedzieć się więcej o możliwościach Network Watcher, zobacz [co to jest Network Watcher?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Network Watcher

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/odczyt                              | Pobierz obserwatora sieciowego                                          |
| Microsoft. Network/networkWatchers/Write                             | Tworzenie lub aktualizowanie obserwatora sieciowego                             |
| Microsoft. Network/networkWatchers/Delete                            | Usuwanie obserwatora sieciowego                                       |

## <a name="nsg-flow-logs"></a>Dzienniki przepływów sieciowych grup zabezpieczeń

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/configureFlowLog/akcja           | Konfigurowanie dziennika przepływu                                           |
| Microsoft. Network/networkWatchers/queryFlowLogStatus/akcja         | Stan zapytania dla dziennika przepływu                                    |

## <a name="connection-troubleshoot"></a>Rozwiązywanie problemów z połączeniami

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectivityCheck/akcja          | Zainicjuj test rozwiązywania problemów z połączeniem
| Microsoft. Network/networkWatchers/queryTroubleshootResult/akcja    | Wyniki zapytania dotyczące test rozwiązywania problemów z połączeniem                |
| Microsoft. Network/networkWatchers/Rozwiązywanie problemów/akcja               | Uruchom test rozwiązywania problemów z połączeniem                             |

## <a name="connection-monitor"></a>Monitor połączeń

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/connectionMonitors/Start/akcja   | Uruchom Monitor połączeń                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/akcja    | Zatrzymywanie monitora połączeń                                      |
| Microsoft. Network/networkWatchers/connectionMonitors/kwerenda/akcja   | Wysyłanie zapytań do monitora połączeń                                     |
| Microsoft. Network/networkWatchers/connectionMonitors/odczyt           | Pobierz monitor połączeń                                       |
| Microsoft. Network/networkWatchers/connectionMonitors/Write          | Tworzenie monitora połączeń                                    |
| Microsoft. Network/networkWatchers/connectionMonitors/Delete         | Usuwanie monitora połączeń                                    |

## <a name="packet-capture"></a>Przechwytywanie pakietów

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/packetCaptures/queryStatus/Action | Zbadaj stan przechwycenia pakietu                           |
| Microsoft. Network/networkWatchers/packetCaptures/akcja        | Zatrzymywanie przechwytywania pakietu                                          |
| Microsoft. Network/networkWatchers/packetCaptures/odczyt               | Pobierz przechwycenie pakietu                                           |
| Microsoft. Network/networkWatchers/packetCaptures/Write              | Tworzenie przechwycenia pakietu                                        |
| Microsoft. Network/networkWatchers/packetCaptures/Delete             | Usuwanie przechwytywania pakietu                                        |

## <a name="ip-flow-verify"></a>Weryfikacja przepływu adresów IP

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/ipFlowVerify/akcja               | Weryfikowanie przepływu IP                                              |

## <a name="next-hop"></a>Narzędzie Następny przeskok

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/skoku/akcja                    | Pobierz następny przeskok z maszyny wirtualnej                                     |

## <a name="network-security-group-view"></a>Widok sieciowych grup zabezpieczeń

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/securityGroupView/akcja          | Wyświetlanie grup zabezpieczeń                                           |

## <a name="topology"></a>Topologia

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/topologia/akcja                   | Uzyskiwanie topologii                                                   |
| Microsoft. Network/networkWatchers/Topology/odczyt                     | Jak wyżej                                                  |

## <a name="reachability-report"></a>Raport osiągalności

| Akcja                                                              | Opis                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft. Network/networkWatchers/azureReachabilityReport/akcja    | Pobierz raport o osiągalności platformy Azure                               |


## <a name="additional-actions"></a>Dodatkowe akcje

Funkcje Network Watcher wymagają również następujących działań:

| Akcje:                                                           | Opis                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft. Authorization/ \* /Read                                     | Służy do pobierania przypisań ról i definicji zasad platformy Azure          |
| Microsoft. resources/subscriptions/resourceGroups/Read               | Służy do wyliczania wszystkich grup zasobów w ramach subskrypcji    |
| Microsoft. Storage/storageAccounts/Read                              | Służy do pobierania właściwości dla określonego konta magazynu   |
| Microsoft. Storage/storageAccounts/listServiceSas/Action, </br> Microsoft. Storage/storageAccounts/listAccountSas/Action, <br> Microsoft. Storage/storageAccounts/listKeys/akcja| Służy do pobierania sygnatur dostępu współdzielonego (SAS) umożliwiających [bezpieczny dostęp do konta magazynu](../storage/common/storage-sas-overview.md) i zapisywanie na koncie magazynu |
| Microsoft. COMPUTE/virtualMachines/odczyt, </br> Microsoft. COMPUTE/virtualMachines/Write| Służy do logowania się do maszyny wirtualnej, przechwycenia pakietu i przekazania jej do konta magazynu|
| Microsoft. COMPUTE/virtualMachines/Extensions/Read </br> Microsoft. COMPUTE/virtualMachines/Extensions/Write| Służy do sprawdzania, czy Network Watcher rozszerzenie jest obecne, i instalowanie w razie potrzeby |
| Microsoft. COMPUTE/virtualMachineScaleSets/odczyt, </br> Microsoft. COMPUTE/virtualMachineScaleSets/Write| Służy do uzyskiwania dostępu do zestawów skalowania maszyn wirtualnych, do przechwytywania pakietów i przekazywania ich do konta magazynu|
| Microsoft. COMPUTE/virtualMachineScaleSets/rozszerzenia/odczyt, </br> Microsoft. COMPUTE/virtualMachineScaleSets/Extensions/Write| Służy do sprawdzania, czy Network Watcher rozszerzenie jest obecne, i instalowanie w razie potrzeby |
| Microsoft. Insights/alertRules/*                                     | Służy do konfigurowania alertów metryk                                     |
| Microsoft. Support/*                                                 | Służy do tworzenia i aktualizowania biletów pomocy technicznej z Network Watcher |