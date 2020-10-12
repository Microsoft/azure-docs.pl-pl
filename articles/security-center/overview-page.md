---
title: Główny pulpit nawigacyjny Azure Security Center lub strona "przegląd"
description: Dowiedz się więcej na temat funkcji strony Przegląd Security Center
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 3f7298ec7bba82eaddc4a002723f44bc5da68433
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945349"
---
# <a name="azure-security-centers-overview-page"></a>Strona przeglądu Azure Security Center

Po otwarciu Azure Security Center pierwsza strona do wyświetlenia jest stroną przegląd. 

:::image type="content" source="media/overview-page/overview.png" alt-text="Strona przeglądu usługi Security Center":::

Wykrywaj i oceniaj bezpieczeństwo obciążeń oraz zidentyfikuj i ograniczaj ryzyko, korzystając ze strony Przegląd Security Center.

Przegląd zapewnia ujednolicony wgląd w zabezpieczenia stan obciążeń chmury hybrydowej. Ponadto są wyświetlane alerty zabezpieczeń, informacje o zapotrzebowaniu i inne.


## <a name="features-of-the-overview-page"></a>Funkcje strony przegląd

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Strona przeglądu usługi Security Center":::

**Górny pasek menu** oferuje następujące funkcje:
- **Subskrypcje** — możesz wyświetlać i filtrować listę subskrypcji, wybierając ten przycisk. Security Center dostosowuje wyświetlanie, aby odzwierciedlały stan zabezpieczeń wybranych subskrypcji.
- **Co nowego** — otwiera informacje o [wersji](release-notes.md) , dzięki czemu można na bieżąco aktualizować nowe funkcje, poprawki błędów i przestarzałe funkcje.
- **Numery wysokiego poziomu** dla połączonych kont w chmurze w celu wyświetlenia kontekstu informacji na głównych kafelkach poniżej. Jak również liczba aktywnych zaleceń i alertów.
    Dowiedz się więcej na temat łączenia [kont AWS](quickstart-onboard-aws.md) i [projektów GCP](quickstart-onboard-gcp.md).


Na środku strony znajdują się **cztery centralne kafelki**, z których każda łączy się z dedykowanym pulpitem nawigacyjnym, aby uzyskać więcej szczegółów:
- Dzięki **zabezpieczeniom** ocenianym Security Center nieustannie szacuje zasoby, subskrypcje i organizację pod kątem problemów z zabezpieczeniami. Następnie agreguje wszystkie wyniki w postaci pojedynczego wyniku, dzięki czemu można szybko powiedzieć, że aktualna sytuacja zabezpieczeń: wyższy wynik, tym niższy poziom ryzyka. [Dowiedz się więcej](secure-score-security-controls.md).
- **Zgodność** — Security Center zapewnia wgląd w stan zgodności w oparciu o ciągłe oceny środowiska platformy Azure. Security Center analizuje czynniki ryzyka w środowisku chmury hybrydowej zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Te oceny są mapowane na kontrolki zgodności z obsługiwanego zestawu standardów. [Dowiedz się więcej](security-center-compliance-dashboard.md).
- **Usługa Azure Defender** — jest to platforma ochrony obciążeń w chmurze (CWPP) zintegrowana w ramach Security Center w celu zaawansowanej, inteligentnej ochrony obciążeń platformy Azure i hybrydowych. Kafelek przedstawia pokrycie zasobów połączonych (dla aktualnie wybranych subskrypcji) i ostatnich alertów, które są kodowane kolorami według ważności. [Dowiedz się więcej](azure-defender.md).
- **Spis** — kafelek przedstawia liczbę niemonitorowanych maszyn wirtualnych oraz prostą BAROMETER zasobów monitorowanych przez Security Center. [Dowiedz się więcej](asset-inventory.md).


Okienko **Insights** oferuje dostosowane elementy dla środowiska, w tym:
- Najbardziej zaatakowane zasoby
- Kontrolki zabezpieczeń, które mają najwyższy potencjał, aby zwiększyć bezpieczny wynik
- Aktywne zalecenia z największą ilością zagrożonych zasobów
- Najnowsze wpisy w blogu przez ekspertów Azure Security Center

## <a name="next-steps"></a>Następne kroki

Na tej stronie została wprowadzona Strona przeglądu Security Center. Aby uzyskać powiązane informacje, zobacz:

- [Eksplorowanie zasobów i zarządzanie nimi za pomocą spisu zasobów i narzędzi do zarządzania](asset-inventory.md)
- [Zabezpiecz ocenę w Azure Security Center](secure-score-security-controls.md)