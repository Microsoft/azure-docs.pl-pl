---
title: Dostępność infrastruktury platformy Azure — zabezpieczenia platformy Azure
description: Ten artykuł zawiera informacje o tym, co firma Microsoft zabezpiecza infrastruktury platformy Azure i zapewnia maksymalną dostępność danych klientów.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727229"
---
# <a name="azure-infrastructure-availability"></a>Dostępność infrastruktury platformy Azure
Ten artykuł zawiera informacje o tym, co firma Microsoft zabezpiecza infrastruktury platformy Azure i zapewnia maksymalną dostępność danych klientów. Platforma Azure zapewnia niezawodną dostępność w oparciu o rozległą nadmiarowość uzyskaną z technologią wirtualizacji.

## <a name="temporary-outages-and-natural-disaster"></a>Tymczasowa awaria i klęska żywiołowa
Zespół infrastruktury i operacji Microsoft Cloud projektuje, kompiluje, przetwarza i zwiększa bezpieczeństwo infrastruktury chmurowej. Ten zespół gwarantuje, że infrastruktura platformy Azure zapewnia wysoką dostępność i niezawodność, wysoką wydajność i skalowalność. Zespół zapewnia bardziej bezpieczną, prywatną i zaufaną chmurę.

Zasilacze awaryjne i ogromne banki baterii zapewniają, że energia elektryczna pozostanie ciągła, gdy wystąpi krótkoterminowe zakłócenia zasilania. Generatory awaryjne zapewniają moc tworzenia kopii zapasowych w celu przedłużonej awarii i planowanej konserwacji. W przypadku wystąpienia klęski żywiołowej centrum danych może używać rezerw paliwa na miejscu.

Szybkie i niezawodne sieci światłowod światłowodowe łączą centra danych z innymi głównymi centrami i użytkownikami internetowymi. Węzły obliczeniowe obsługują obciążenia bliżej użytkowników, aby zmniejszyć opóźnienia, zapewnić nadmiarowość geograficzną i zwiększyć ogólną odporność usługi. Zespół inżynierów działa wokół zegara, aby zapewnić dostępność usług.

Firma Microsoft zapewnia wysoką dostępność dzięki zaawansowanemu monitorowaniu i reagowaniu na zdarzenia, obsłudze usług i funkcji pracy awaryjnej w trybie failover. Geograficznie rozproszone centra Microsoft Operations działają 24/7/365. Sieć platformy Azure jest jednym z największych na świecie. Sieć światłowodowa i sieci dystrybucji zawartości łączy centra danych i węzły brzegowe w celu zapewnienia wysokiej wydajności i niezawodności.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Platforma Azure utrzymuje dane trwałe w dwóch lokalizacjach. Możesz wybrać lokalizację lokacji kopii zapasowej. W obu lokalizacjach platforma Azure stale utrzymuje trzy repliki danych w dobrej kondycji.

## <a name="database-availability"></a>Dostępność bazy danych
Platforma Azure zapewnia, że baza danych jest dostępna w Internecie za pomocą bramy internetowej z ciągłą dostępnością bazy danych. Monitorowanie ocenia kondycję i stan aktywnych baz danych w 5-minutowych interwałach czasowych.

## <a name="storage-availability"></a>Dostępność magazynu
Platforma Azure zapewnia magazyn za pośrednictwem wysoce skalowalnej i trwałej usługi magazynu, która zapewnia punkty końcowe łączności. Oznacza to, że aplikacja może uzyskiwać dostęp bezpośrednio do usługi magazynu. Usługa Storage przetwarza przychodzące żądania magazynu efektywnie, z integralnością transakcyjną.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co firma Microsoft pomaga w zabezpieczeniu infrastruktury platformy Azure, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)
