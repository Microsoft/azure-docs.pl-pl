---
title: Kontrola zabezpieczeń platformy Azure — Ochrona danych
description: Ochrona danych w usłudze Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d36ed6a795c5fa2241ee71751053a4cb5986aaf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576820"
---
# <a name="security-control-data-protection"></a>Kontrola zabezpieczeń: Ochrona danych

Zalecenia dotyczące ochrony danych koncentrują się na rozwiązywaniu problemów dotyczących szyfrowania, list kontroli dostępu, kontroli dostępu opartej na tożsamościach i rejestrowania inspekcji dostępu do danych.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 4.1 | 13,1 | Customer |

Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

- [Tworzenie i używanie tagów](../../azure-resource-manager/management/tag-resources.md)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 4.2 | 13,2, 2,10 | Customer |

Zaimplementuj izolację przy użyciu osobnych subskrypcji i grup zarządzania dla poszczególnych domen zabezpieczeń, takich jak typ środowiska i poziom czułości danych. Możesz ograniczyć poziom dostępu do zasobów platformy Azure, których wymagają aplikacje i środowiska korporacyjne. Dostęp do zasobów platformy Azure można kontrolować za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). 

- [Jak utworzyć dodatkowe subskrypcje platformy Azure](../../cost-management-billing/manage/create-subscription.md)

- [Jak utworzyć Grupy zarządzania](../../governance/management-groups/create-management-group-portal.md)

- [Tworzenie i używanie tagów](../../azure-resource-manager/management/tag-resources.md)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 4.3 | 13,3 | Udostępniona |

Skorzystaj z rozwiązania innej firmy w witrynie Azure Marketplace w sieci obwodowej, które monitoruje do nieautoryzowanego transferu poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i chroniącą przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../fundamentals/protection-customer-data.md)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 4.4 | 14,4 | Udostępniona |

Szyfruj wszystkie poufne informacje podczas przesyłania. Upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS 1,2 lub nowszy.

Postępuj zgodnie z zaleceniami Azure Security Center na potrzeby szyfrowania w czasie spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

- [Informacje o szyfrowaniu podczas przesyłania na platformę Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 4.5 | 14,5 | Udostępniona |

Jeśli dla konkretnej usługi na platformie Azure nie jest dostępna żadna funkcja, użyj narzędzia do odnajdywania aktywnego innej firmy, aby zidentyfikować wszystkie informacje poufne przechowywane, przetwarzane lub przekazywane przez systemy technologiczne organizacji, w tym te znajdujące się w lokacji lub u dostawcy usług zdalnych, a także aktualizować spis informacji poufnych organizacji.

Użyj Azure Information Protection, aby identyfikować poufne informacje w Microsoft 365 dokumentach.

Użyj usługi Azure SQL Information Protection, aby pomóc w klasyfikacji i etykietowania informacji przechowywanych w Azure SQL Database.

- [Jak wdrożyć usługę Azure SQL Data Discovery](../../azure-sql/database/data-discovery-and-classification-overview.md)

- [Jak zaimplementować Azure Information Protection](/azure/information-protection/deployment-roadmap)

- [Informacje na temat ochrony danych klientów na platformie Azure](../fundamentals/protection-customer-data.md)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 4,6 | 14,6 | Customer |

Aby kontrolować dostęp do danych i zasobów przy użyciu kontroli dostępu opartej na rolach (Azure RBAC), w przeciwnym razie użyj metod kontroli dostępu specyficznych dla usługi.

- [Jak skonfigurować usługę Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 4.7 | 14,7 | Udostępniona |

Jeśli jest to wymagane na potrzeby zgodności zasobów obliczeniowych, zaimplementuj narzędzie innej firmy, takie jak automatyczne rozwiązanie do ochrony przed utratą danych oparte na hoście, aby wymusić kontrolę dostępu do danych nawet wtedy, gdy dane są kopiowane poza system.

W przypadku podstawowej platformy zarządzanej przez firmę Microsoft Firma Microsoft traktuje całą zawartość klienta jako poufną i nadaje im dużą długość, aby chronić przed utratą i narażeniem danych przez klienta. Aby zapewnić bezpieczeństwo danych klienta na platformie Azure, firma Microsoft wdrożyła i utrzymuje pakiet niezawodnych kontroli i możliwości ochrony danych.

- [Informacje na temat ochrony danych klientów na platformie Azure](../fundamentals/protection-customer-data.md)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 4.8 | 14,8 | Customer |

Używaj szyfrowania w spoczynku dla wszystkich zasobów platformy Azure. Firma Microsoft zaleca, aby platforma Azure mogła zarządzać kluczami szyfrowania, jednak istnieje możliwość zarządzania własnymi kluczami w niektórych wystąpieniach. 

- [Informacje o szyfrowaniu danych magazynowanych na platformie Azure](../fundamentals/encryption-atrest.md)

- [Jak skonfigurować klucze szyfrowania zarządzane przez klienta](../../storage/common/customer-managed-keys-configure-key-vault.md)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 4,9 | 14,9 | Customer |

Użyj Azure Monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące sytuacji, w których zmiany dotyczą najważniejszych zasobów platformy Azure.

- [Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure](../../azure-monitor/alerts/alerts-activity-log.md)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [Zarządzanie](security-control-vulnerability-management.md) lukami w zabezpieczeniach