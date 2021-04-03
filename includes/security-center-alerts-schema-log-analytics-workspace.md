---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "79538833"
---
### <a name="the-data-model-of-the-schema"></a>Model danych schematu

|Pole|Opis|
|----|----|
|**AlertName**|Nazwa wyświetlana alertu|
|**AlertType**|unikatowy identyfikator alertu|
|**ConfidenceLevel**|Obowiązkowe Poziom pewności tego alertu (wysoka/niska)|
|**ConfidenceScore**|Obowiązkowe Wskaźnik niebezpiecznej pewności alertu zabezpieczeń|
|**Opis**|Tekst opisu alertu|
|**Nazwa wyświetlana**|Nazwa wyświetlana alertu|
|**EndTime**|Czas zakończenia alertu (godzina ostatniego zdarzenia, które uczestniczy w alercie)|
|**Jednostki**|Lista jednostek związanych z alertem. Ta lista może przechowywać różne jednostki różnych typów|
|**ExtendedLinks**|Obowiązkowe Zbiór dla wszystkich linków związanych z alertem. Ten zbiór może zawierać kombinację linków dla różnych typów|
|**ExtendedProperties**|Zbiór dodatkowych pól, które są istotne dla alertu|
|**Iszdarzenie**|Określa, czy alert jest zdarzeniem, czy zwykłym alertem. Zdarzenie to alert zabezpieczeń, który agreguje wiele alertów w jednym zdarzeniu związanym z zabezpieczeniami|
|**ProcessingEndTime**|Sygnatura czasowa UTC, w której został utworzony alert|
|**ProductComponentName**|Obowiązkowe Nazwa składnika w produkcie, który wygenerował alert.|
|**ProductName**|stała ("Azure Security Center")|
|**ProviderName**|Przestrzeń|
|**RemediationSteps**|Ręczne elementy akcji, które należy podjąć w celu skorygowania zagrożeń bezpieczeństwa|
|**ResourceId**|Pełny identyfikator zasobu, którego to dotyczy|
|**Ważność**|Ważność alertu (Wysoka/Średnia/niska/informacyjna)|
|**SourceComputerId**|unikatowy identyfikator GUID serwera, którego dotyczy ten komunikat (jeśli alert jest generowany na serwerze)|
|**SourceSystem**|Przestrzeń|
|**Rozpoczęcia**|Godzina rozpoczęcia alertu (godzina pierwszego zdarzenia, które ma wpływ na alert)|
|**SystemAlertId**|Unikatowy identyfikator tego wystąpienia alertu zabezpieczeń|
|**TenantId**|Identyfikator nadrzędnej dzierżawy usługi Azure Active Directory subskrypcji, w ramach której znajduje się zeskanowany zasób|
|**TimeGenerated**|Sygnatura czasowa UTC, w której miała miejsce Ocena (czas skanowania Security Center) (taka sama jak DiscoveredTimeUTC)|
|**Typ**|stała ("SecurityAlert")|
|**NazwaDostawcy**|Nazwa dostawcy, który dostarczył alert (np. "Microsoft")|
|**VendorOriginalId**|Przestrzeń|
|**WorkspaceResourceGroup**|w przypadku wygenerowania alertu na maszynie wirtualnej, serwerze, zestawie skalowania maszyn wirtualnych lub wystąpieniu App Service, które jest zgłaszane do obszaru roboczego, zawiera nazwę grupy zasobów obszaru roboczego|
|**WorkspaceSubscriptionId**|w przypadku wygenerowania alertu na maszynie wirtualnej, serwerze, zestawie skalowania maszyn wirtualnych lub wystąpieniu App Service, które jest zgłaszane do obszaru roboczego, zawiera identyfikator subskrypcji obszaru roboczego|
|||
