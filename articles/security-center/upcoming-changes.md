---
title: Ważne zmiany dotyczące Azure Security Center
description: Nadchodzące zmiany w Azure Security Center, które mogą być potrzebne, i dla których może być konieczne zaplanowanie
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380169"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Ważne zmiany w Azure Security Center

> [!IMPORTANT]
> Informacje na tej stronie odnoszą się do produktów lub funkcji w wersji wstępnej, które mogą być znacząco modyfikowane przed ich udostępnieniem komercyjnemu. Firma Microsoft nie udziela żadnych zobowiązań ani gwarancji, wyraźnych ani dorozumianych, w odniesieniu do informacji podanych w tym miejscu.

Na tej stronie znajdziesz informacje o zmianach, które są planowane dla Security Center. Opisano w nim planowane modyfikacje produktu, które mogą mieć wpływ na takie rzeczy jak bezpieczny wynik lub przepływy pracy.

Jeśli szukasz najnowszych informacji o wersji, znajdziesz je w temacie [co nowego w Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planowane zmiany

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>"Aktualizacje systemu powinny być zainstalowane na swoich maszynach" zalecenia dotyczące uzyskiwania podrzędnych zaleceń

#### <a name="summary"></a>Podsumowanie

| Aspekt | Szczegóły |
|---------|---------|
|Data anonsu | 9 listopada 2020  |
|Data zmiany  |  Średnio-koniec listopada 2020 |
|Wpływ     | W trakcie przejścia z bieżącej wersji tego zalecenia do jego zastąpienia bezpieczny wynik może ulec zmianie. |
|  |  |

Firma Microsoft udostępnia rozszerzoną wersję **aktualizacji systemu, które należy zainstalować na zaleceń dotyczących maszyn** . Nowa wersja *zastąpi* bieżącą wersję kontroli zabezpieczeń Zastosuj system updates i doprowadzi do następujących ulepszeń:

- Zalecenia podrzędne dla każdej brakującej aktualizacji
- Przeprojektowane środowisko na Azure Security Center stronach Azure Portal
- Wzbogacone dane dla zalecenia z grafu zasobów platformy Azure

#### <a name="transition-period"></a>Okres przejściowy

Będzie dostępny okres przejściowy wynoszący 36 godz. (w przybliżeniu). Aby zminimalizować potencjalne zakłócenia, zaplanowano aktualizację, która została przeprowadzona w weekendy. W trakcie przejścia może to wpłynąć na Twoje bezpieczne oceny.

#### <a name="redesigned-portal-experience"></a>Przeprojektowana obsługa portalu

**Na swoich maszynach należy zainstalować na** stronie Szczegóły rekomendacji zawierającej listę wyników, jak pokazano poniżej. Po wybraniu pojedynczego wyszukiwania zostanie otwarte okienko Szczegóły z linkiem do informacji o korygowaniu oraz z listą zasobów, których to dotyczy.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Otwieranie jednego z zaleceń w portalu w celu uzyskania zaktualizowanego zalecenia":::


#### <a name="richer-data-from-azure-resource-graph"></a>Bogatsze dane z grafu zasobów platformy Azure

Azure Resource Graph to usługa platformy Azure, która została zaprojektowana w celu zapewnienia wydajnej eksploracji zasobów. Za pomocą argumentu ARG można wykonywać zapytania na dużą skalę w ramach danego zestawu subskrypcji, dzięki czemu można efektywnie zarządzać środowiskiem. 

Aby uzyskać Azure Security Center, można użyć ARG i [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) do wykonywania zapytań o szeroki zakres danych stan zabezpieczeń.

W przypadku wysyłania zapytania do bieżącej wersji **aktualizacji systemu należy zainstalować na swoich maszynach** jedyne dostępne informacje z tego argumentu to zalecenie, które należy skorygować na komputerze. Po wydaniu zaktualizowanej wersji następujące zapytanie zwróci wszystkie brakujące aktualizacje systemu pogrupowane według maszyn.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z najnowszymi zmianami w produkcie, zobacz [co nowego w programie Azure Security Center?](release-notes.md).