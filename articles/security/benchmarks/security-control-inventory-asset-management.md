---
title: Kontrola zabezpieczeń platformy Azure — zarządzanie zapasami i zasobami
description: Zarządzanie zapasami i zasobami kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408357"
---
# <a name="security-control-inventory-and-asset-management"></a>Kontrola bezpieczeństwa: zarządzanie zapasami i zasobami

Zalecenia dotyczące zarządzania zapasami i zasobami koncentrują się na rozwiązywaniu problemów związanych z aktywnym zarządzaniem (zapasami, śledzeniem i poprawianiem) wszystkich zasobów platformy Azure, dzięki czemu tylko autoryzowane zasoby mają dostęp, a nieautoryzowane i niezarządzane zasoby są identyfikowane i usuwane.

## <a name="61-use-automated-asset-discovery-solution"></a>6.1: Korzystanie z automatycznego rozwiązania Asset Discovery

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Klient |

Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywanie wszystkich zasobów (takich jak zasoby obliczeniowe, magazyn, sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, odpowiednie (odczytu) uprawnienia w dzierżawie i wyliczyć wszystkie subskrypcje platformy Azure, a także zasoby w ramach subskrypcji.

Chociaż klasyczne zasoby platformy Azure mogą zostać wykryte za pomocą wykresu zasobów, zdecydowanie zaleca się tworzenie i używanie zasobów usługi Azure Resource Manager w przyszłości.

- [Jak tworzyć kwerendy za pomocą programu Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Jak wyświetlić subskrypcje platformy Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Poznaj usługę Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2: Obsługa metadanych zasobów

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.2 | 1.5 | Klient |

Zastosuj tagi do zasobów platformy Azure, dając metadane logicznie zorganizować je w taksonomii.

- [Jak tworzyć i używać tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Usuwanie nieautoryzowanych zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.3 | 1.6 | Klient |

W stosownych przypadkach należy używać tagowania, grup zarządzania i oddzielnych subskrypcji, aby organizować i śledzić zasoby. Regularnie uzgadniaj zapasy i upewnij się, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Jak utworzyć grupy zarządzania](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Jak tworzyć i używać tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definiowanie i obsługa spisu zatwierdzonych zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.4 | 2.1 | Klient |

Utwórz spis zatwierdzonych zasobów platformy Azure i zatwierdzonego oprogramowania dla zasobów obliczeniowych zgodnie z naszymi potrzebami organizacyjnymi.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitoruj niezatwierdzone zasoby platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.5 | 2.3, 2.4 | Klient |

Użyj zasad platformy Azure, aby wprowadzić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji.

Użyj usługi Azure Resource Graph do wykonywania zapytań/odnajdywanie zasobów w ramach ich subskrypcji.  Upewnij się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

- [Jak skonfigurować zasady platformy Azure i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak tworzyć zapytania za pomocą programu Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorowanie niezatwierdzonych aplikacji w zasobach obliczeniowych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.6 | 2.3, 2.4 | Klient |

Użyj usługi Azure virtual machine Inventory, aby zautomatyzować zbieranie informacji o całym oprogramowaniu na maszynach wirtualnych. Czas nazwy oprogramowania, wersji, wydawcy i odświeżania jest dostępny w witrynie Azure portal. Aby uzyskać dostęp do daty instalacji i innych informacji, włącz diagnostykę na poziomie gościa i przenieś dzienniki zdarzeń systemu Windows do obszaru roboczego analizy dzienników.

- [Jak włączyć inwentaryzację maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Usuwanie niezatwierdzonych zasobów platformy Azure i aplikacji

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.7 | 2.5 | Klient |

Użyj monitorowania integralności plików (śledzenia zmian) usługi Azure Security Center i spisu maszyn wirtualnych, aby zidentyfikować całe oprogramowanie zainstalowane na maszynach wirtualnych. Można zaimplementować własny proces usuwania nieautoryzowanego oprogramowania. Można również użyć rozwiązania innej firmy do identyfikacji niezatwierdzone oprogramowanie.

- [Jak korzystać z monitorowania integralności plików](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [Opis śledzenia zmian platformy Azure](https://docs.microsoft.com/azure/automation/change-tracking)

- [Jak włączyć spis maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8: Używaj wyłącznie zatwierdzonych aplikacji

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.8 | 2,6 | Klient |

Użyj kontroli aplikacji adaptacyjnych usługi Azure Security Center, aby upewnić się, że tylko autoryzowane oprogramowanie jest wykonywane, a wszystkie nieautoryzowane oprogramowanie jest blokowane w wykonywaniu na maszynach wirtualnych platformy Azure.

- [Jak korzystać z formantów aplikacji adaptacyjnych usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9: Korzystaj tylko z zatwierdzonych usług platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.9 | 2,6 | Klient |

Użyj zasad platformy Azure, aby ograniczyć usługi, które można aprowizować w swoim środowisku.

- [Jak skonfigurować zasady platformy Azure i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak odmówić określonego typu zasobu za pomocą usługi Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Prowadzenie spisu zatwierdzonych tytułów oprogramowania

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.10 | 2.7 | Klient |

Użyj formantów aplikacji adaptacyjnych usługi Azure Security Center, aby określić typy plików, do których reguła może, ale nie, zastosować.

Zaimplementuj rozwiązanie innych firm, jeśli nie spełnia to wymagań.

- [Jak korzystać z formantów aplikacji adaptacyjnych usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Ograniczanie użytkownikom możliwości interakcji z usługą Azure Resource Manager

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.11 | 2.9 | Klient |

Użyj dostępu warunkowego platformy Azure, aby ograniczyć użytkownikom możliwość interakcji z usługą Azure Resources Manager, konfigurując "Zablokuj dostęp" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy do blokowania dostępu do usługi Azure Resources Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Ograniczenie możliwości wykonywania skryptów przez użytkowników w zasobach obliczeniowych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.12 | 2.9 | Klient |

W zależności od typu skryptów można użyć konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć zdolność użytkowników do wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.  Można również korzystać z funkcji Azure Security Center Adaptive Application Controls, aby upewnić się, że tylko autoryzowane oprogramowanie jest wykonywane, a wszystkie nieautoryzowane oprogramowanie jest blokowane do wykonywania na maszynach wirtualnych platformy Azure.

- [Jak sterować wykonywaniem skryptów programu PowerShell w środowiskach systemu Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Jak korzystać z formantów aplikacji adaptacyjnych usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizycznie lub logicznie segregować aplikacje wysokiego ryzyka

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 6.13 | 2.9 | Klient |

Oprogramowanie, które jest wymagane dla operacji biznesowych, ale może nieść większe ryzyko dla organizacji, powinno być odizolowane w obrębie własnej maszyny wirtualnej i/lub sieci wirtualnej i wystarczająco zabezpieczone za pomocą zapory azure lub sieciowej grupy zabezpieczeń.

- [Jak utworzyć sieć wirtualną](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Jak utworzyć nsg z config zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [bezpieczna konfiguracja](security-control-secure-configuration.md)