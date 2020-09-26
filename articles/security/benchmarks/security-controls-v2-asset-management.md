---
title: Azure Security test — wersja 2 — Zarządzanie zasobami
description: Azure Security test — zarządzanie zasobami
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 845c4f3194f35c68306e74734e5ecc6109d9c103
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318028"
---
# <a name="security-control-v2-asset-management"></a>Kontrola zabezpieczeń v2: zarządzanie zasobami

Zarządzanie zasobami obejmuje kontrolki zapewniające widoczność i nadzór nad bezpieczeństwem zasobów platformy Azure. Obejmuje to zalecenia dotyczące uprawnień dla personelu zabezpieczeń, bezpieczeństwa dostępu do spisu zasobów oraz zarządzania zatwierdzeniami usług i zasobów (spis, śledzenie i poprawne).

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Upewnij się, że zespół ds. zabezpieczeń ma wgląd w ryzyko związane z zasobami

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| AM-1 | 1,1, 1,2 | CM-8, PM-5 |

Upewnij się, że zespoły zabezpieczeń mają przyznane uprawnienia czytelnika zabezpieczeń w dzierżawie i subskrypcjach platformy Azure, aby umożliwić im monitorowanie zagrożeń bezpieczeństwa przy użyciu Azure Security Center. 

W zależności od tego, jak są strukturalne obowiązki zespołu zabezpieczeń, monitorowanie zagrożeń bezpieczeństwa może być obowiązkiem centralnego zespołu zabezpieczeń lub zespołu lokalnego. Wspomniane powyżej informacje o zabezpieczeniach i zagrożeniach muszą zawsze zostać zagregowane w organizacji. 

Uprawnienia czytelnika zabezpieczeń mogą być stosowane szeroko do całej dzierżawy (głównej grupy zarządzania) lub zakresu do grup zarządzania lub określonych subskrypcji. 

Uwaga: w celu uzyskania wglądu w obciążenia i usługi mogą być wymagane dodatkowe uprawnienia. 

- [Omówienie roli czytelnik zabezpieczeń](../../role-based-access-control/built-in-roles.md#security-reader)

- [Omówienie Grupy zarządzania platformy Azure](../../governance/management-groups/overview.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Upewnij się, że zespół ds. zabezpieczeń ma dostęp do spisu zasobów i metadanych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| AM-2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Upewnij się, że zespoły zabezpieczeń mają dostęp do stale aktualizowanego spisu zasobów na platformie Azure. Zespoły ds. zabezpieczeń często potrzebują tego spisu, aby oszacować potencjalne zagrożenie w organizacji do pojawiających się zagrożeń i jako dane wejściowe w celu ciągłego ulepszania zabezpieczeń. 

Funkcja spisu Azure Security Center i wykres zasobów platformy Azure mogą wykonywać zapytania dotyczące i odnajdywania wszystkich zasobów w subskrypcjach, w tym usług platformy Azure, aplikacji i zasobów sieciowych.  

Logicznie Organizuj zasoby zgodnie z taksonomią organizacji przy użyciu tagów, a także innych metadanych na platformie Azure (nazwa, opis i Kategoria).  

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Azure Security Center zarządzanie spisem zasobów](../../security-center/asset-inventory.md)

- [Aby uzyskać więcej informacji na temat tagowania elementów zawartości, zobacz Przewodnik po nazwie i znakowaniu zasobów](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Używaj tylko zatwierdzonych usług platformy Azure

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| AM-3 | 2,3, 2,4 | CM – 7, CM-8 |

Użyj Azure Policy, aby przeprowadzić inspekcję i ograniczyć liczbę usług, które użytkownicy mogą inicjować w danym środowisku. Użyj grafu zasobów platformy Azure do wykonywania zapytań dotyczących zasobów i odnajdywania ich w ramach subskrypcji.  Za pomocą Azure Monitor można tworzyć reguły wyzwalające alerty w przypadku wykrycia niezatwierdzonej usługi.

- [Konfigurowanie Azure Policy i zarządzanie nimi](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odmówić określonego typu zasobu za pomocą Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

- [Jak tworzyć zapytania za pomocą Eksploratora Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zapewnij bezpieczeństwo zarządzania cyklem życia zasobów

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| AM-4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Ustanawianie lub aktualizowanie zasad zabezpieczeń, które dotyczą procesów zarządzania cyklem życia zasobów dla modyfikacji o dużym wpływie. Modyfikacje obejmują następujące zmiany: dostawcy tożsamości i dostępu, czułość danych, konfiguracja sieci i przypisanie uprawnień administracyjnych.

Usuń zasoby platformy Azure, gdy nie są już potrzebne.

- [Usuń grupę zasobów i zasób platformy Azure](../../azure-resource-manager/management/delete-resource-group.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: ograniczanie możliwości korzystania przez użytkowników z Azure Resource Manager

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

Korzystanie z dostępu warunkowego usługi Azure AD w celu ograniczenia możliwości korzystania przez użytkowników z Azure Resource Manager przez skonfigurowanie "blokowania dostępu" dla aplikacji "Microsoft Azure Management".

- [Jak skonfigurować dostęp warunkowy, aby blokować dostęp do usługi Azure Resources](../../role-based-access-control/conditional-access-azure-management.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Używaj tylko zatwierdzonych aplikacji w zasobach obliczeniowych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| AM-6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Upewnij się, że wykonywane jest tylko autoryzowane oprogramowanie i że wszystkie nieautoryzowane oprogramowanie zostało zablokowane na platformie Azure Virtual Machines.

Użyj Azure Security Center (ASC) adaptacyjnych kontrolek aplikacji, aby odnajdywać i generować listę dozwolonych aplikacji. Można również użyć kontroli aplikacji adaptacyjnych ASC, aby upewnić się, że wykonywane jest tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie zostanie zablokowane na platformie Azure Virtual Machines.

Za pomocą Azure Automation Change Tracking i spisu można zautomatyzować zbieranie informacji o spisie z maszyn wirtualnych z systemami Windows i Linux. W Azure Portal są dostępne nazwy, wersje, Wydawca i czas odświeżania oprogramowania. Aby uzyskać datę instalacji oprogramowania i inne informacje, Włącz diagnostykę na poziomie gościa i Przekieruj dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

W zależności od typu skryptów można użyć konfiguracji specyficznych dla systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w zasobach obliczeniowych platformy Azure. 

Możesz również użyć rozwiązania innej firmy w celu odnalezienia i zidentyfikowania niezatwierdzonego oprogramowania.

- [Jak używać Azure Security Center adaptacyjnych kontroli aplikacji](../../security-center/security-center-adaptive-application.md)

- [Informacje o Azure Automation Change Tracking i spisie](../../automation/change-tracking.md)

- [Jak kontrolować wykonywanie skryptów programu PowerShell w środowiskach systemu Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpieczenia infrastruktury i punktu końcowego](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Zarządzanie stanem bezpieczeństwa](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Zarządzanie zgodnością zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

