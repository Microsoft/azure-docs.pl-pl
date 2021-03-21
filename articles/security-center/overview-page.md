---
title: Główny pulpit nawigacyjny Azure Security Center lub strona "przegląd"
description: Dowiedz się więcej na temat funkcji strony Przegląd Security Center
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 768d69b626a870910d6734e1a1ddc29871f96afb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099781"
---
# <a name="azure-security-centers-overview-page"></a>Strona przeglądu Azure Security Center

Po otwarciu Azure Security Center pierwsza strona do wyświetlenia jest stroną przegląd. 

Ten interaktywny pulpit nawigacyjny zapewnia ujednolicony wgląd w zabezpieczenia stan obciążeń chmury hybrydowej. Ponadto są wyświetlane alerty zabezpieczeń, informacje o zapotrzebowaniu i inne.

Możesz wybrać dowolny element na stronie, aby uzyskać bardziej szczegółowe informacje.

:::image type="content" source="media/overview-page/overview.png" alt-text="Strona przeglądu usługi Security Center":::

## <a name="features-of-the-overview-page"></a>Funkcje strony przegląd

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Górny pasek strony przeglądu Security Center":::

**Górny pasek menu** oferuje następujące funkcje:
- **Subskrypcje** — możesz wyświetlać i filtrować listę subskrypcji, wybierając ten przycisk. Security Center dostosowuje wyświetlanie, aby odzwierciedlały stan zabezpieczeń wybranych subskrypcji.
- **Co nowego** — otwiera informacje o [wersji](release-notes.md) , dzięki czemu można na bieżąco aktualizować nowe funkcje, poprawki błędów i przestarzałe funkcje.
- **Numery wysokiego poziomu** dla połączonych kont w chmurze w celu wyświetlenia kontekstu informacji na głównych kafelkach poniżej. A także liczba ocenionych zasobów, aktywnych zaleceń i alertów zabezpieczeń. Wybierz szacowany numer zasobów, aby uzyskać dostęp do [spisu środków trwałych](asset-inventory.md). Dowiedz się więcej na temat łączenia [kont AWS](quickstart-onboard-aws.md) i [projektów GCP](quickstart-onboard-gcp.md).


Na środku strony znajdują się **cztery centralne kafelki**, z których każda łączy się z dedykowanym pulpitem nawigacyjnym, aby uzyskać więcej szczegółów:
- Dzięki **zabezpieczeniom** ocenianym Security Center nieustannie szacuje zasoby, subskrypcje i organizację pod kątem problemów z zabezpieczeniami. Następnie agreguje wszystkie wyniki w postaci pojedynczego wyniku, dzięki czemu można szybko powiedzieć, że aktualna sytuacja zabezpieczeń: wyższy wynik, tym niższy poziom ryzyka. [Dowiedz się więcej](secure-score-security-controls.md).
- **Zgodność z przepisami** — Security Center zapewnia wgląd w stan zgodności w oparciu o ciągłe oceny środowiska platformy Azure. Security Center analizuje czynniki ryzyka w środowisku chmury hybrydowej zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Te oceny są mapowane na kontrolki zgodności z obsługiwanego zestawu standardów. [Dowiedz się więcej](security-center-compliance-dashboard.md).
- **Azure Defender** — jest to platforma ochrony obciążeń w chmurze (CWPP) zintegrowana w ramach Security Center w celu uzyskania zaawansowanej, inteligentnej ochrony obciążeń platformy Azure i hybrydowych. Kafelek przedstawia pokrycie zasobów połączonych (dla aktualnie wybranych subskrypcji) i ostatnich alertów, które są kodowane kolorami według ważności. [Dowiedz się więcej](azure-defender.md).
- **Menedżer zapory** — ten kafelek przedstawia stan centrów i sieci z [Menedżera zapory platformy Azure](../firewall-manager/overview.md). 


Okienko **Insights** oferuje dostosowane elementy dla środowiska, w tym:
- Najbardziej zaatakowane zasoby
- Kontrolki zabezpieczeń, które mają najwyższy potencjał, aby zwiększyć bezpieczny wynik
- Aktywne zalecenia z największą ilością zagrożonych zasobów
- Najnowsze wpisy w blogu przez ekspertów Azure Security Center

## <a name="next-steps"></a>Następne kroki

Na tej stronie została wprowadzona Strona przeglądu Security Center. Aby uzyskać powiązane informacje, zobacz:

- [Eksplorowanie zasobów i zarządzanie nimi za pomocą spisu zasobów i narzędzi do zarządzania](asset-inventory.md)
- [Wskaźnik bezpieczeństwa w usłudze Azure Security Center](secure-score-security-controls.md)