---
title: Optymalizowanie obciążeń platformy Azure za pomocą oceny klasyfikatora
description: Skorzystaj z Azure Advisor oceny, aby maksymalnie wykorzystać platformę Azure.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630126"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Optymalizowanie obciążeń platformy Azure za pomocą oceny klasyfikatora

## <a name="introduction-to-advisor-score"></a>Wprowadzenie do oceny klasyfikatora

Azure Advisor zapewnia zalecenia dotyczące najlepszych rozwiązań dla obciążeń. Te zalecenia są personalizowane i umożliwiają podejmowanie działań w celu ułatwienia:

* Zwiększ stan obciążeń i zoptymalizuj wdrożenia platformy Azure.
* Zaaktywnie Zapobiegaj najważniejszym problemom, postępując zgodnie z najlepszymi rozwiązaniami.
* Oceń swoje obciążenia platformy Azure na pięć filarów [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/).

Jako podstawowa funkcja usługi Advisor Ocena usługi Advisor może pomóc w skutecznym i wydajnym osiąganiu tych celów.

Aby maksymalnie wykorzystać możliwości platformy Azure, należy zrozumieć, w jaki sposób odbywa się Optymalizacja obciążenia. Musisz wiedzieć, które usługi lub zasoby są używane prawidłowo, a które nie. Aby dowiedzieć się więcej, zapoznaj się z informacjami na temat określania priorytetów akcji, na podstawie zaleceń, aby zmaksymalizować wynik.

Ważne jest również śledzenie i raportowanie postępu wykonywanego w ramach tej przejazdu optymalizacji. Ocena usługi Advisor pozwala łatwo wykonać wszystkie te czynności przy użyciu nowego środowiska grywalizacji.

Jako spersonalizowany konsultant chmury Azure Advisor stale ocenia dane telemetryczne użycia i konfigurację zasobów w celu sprawdzenia najlepszych praktyk branżowych. Następnie usługa Advisor agreguje swoje wyniki do pojedynczego wyniku. Korzystając z tego wyniku, można szybko powiedzieć, jeśli podejmujesz niezbędne kroki w celu utworzenia niezawodnych, bezpiecznych i ekonomicznych rozwiązań.

Ocena klasyfikatora składa się z ogólnej oceny, którą można podzielić na pięć ocen kategorii. Jeden wynik dla każdej kategorii klasyfikatora reprezentuje pięć filarów Well-Architected Framework.

Postęp można śledzić w czasie, wyświetlając ogólną ocenę i ocenę kategorii za pomocą codziennych, cotygodniowych i miesięcznych trendów. Możesz również ustawić testy porównawcze, aby ułatwić osiągnięcie celów.

 ![Zrzut ekranu przedstawiający stronę oceny klasyfikatora.](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Interpretowanie oceny klasyfikatora

Doradca przedstawia ogólny wynik doradcy oraz podział dla kategorii klasyfikatora w procentach. Wynik 100% w dowolnej kategorii oznacza, że wszystkie zasoby oceniane przez usługę Advisor są zgodne z najlepszymi rozwiązaniami zalecanymi przez usługę Advisor. Na drugim końcu spektrum wynik 0% oznacza, że żaden z zasobów ocenianych przez klasyfikatora nie jest zgodny z zaleceniami klasyfikatora. Korzystając z tych ziaren wynikowych, można łatwo osiągnąć następujący przepływ:

* **Ocena usługi Advisor** pomaga określić, w jaki sposób obciążenie lub subskrypcje są wykonywane na podstawie oceny klasyfikatora. Możesz również zobaczyć trendy historyczne, aby zrozumieć, jaki jest trend.
* **Ocena według kategorii** dla każdego zalecenia informuje o tym, które zaległe rekomendacje zwiększą wynik. Te wartości odzwierciedlają zarówno wagę zalecenia, jak i przewidywaną łatwość implementacji. Te czynniki pomagają zapewnić największą wartość w czasie. Ułatwiają one również określanie priorytetów.
* **Wpływ oceny kategorii** dla każdego zalecenia pomaga określić priorytety akcji korygowania dla każdej kategorii.

Wkład każdego zalecenia do oceny kategorii jest widoczny na stronie **oceny klasyfikatora** w Azure Portal. Można zwiększyć każdy wynik kategorii według wartości procentowej wymienionej w kolumnie **podwyżki wyniku** . Ta wartość odzwierciedla zarówno wagę zalecenia w kategorii, jak i przewidywaną łatwość implementacji, która pozwala na obsługę najłatwiejszych zadań. Skoncentrowanie się na zaleceniach o największym wpływie na wyniki pomoże Ci w tym czasie.

![Zrzut ekranu pokazujący wpływ oceny klasyfikatora.](./media/advisor-score-2.png)

Jeśli jakiekolwiek zalecenia klasyfikatora nie mają zastosowania do poszczególnych zasobów, można odłożyć lub odrzucić te zalecenia. Zostaną one wykluczone z obliczenia wyniku przy następnym odświeżeniu. Doradca będzie również używać tych danych wejściowych jako dodatkowych informacji zwrotnych w celu ulepszenia modelu.

## <a name="how-is-an-advisor-score-calculated"></a>Jak jest obliczana Ocena klasyfikatora?

W usłudze Advisor są wyświetlane wyniki kategorii oraz ogólny wynik doradcy jako wartości procentowe. Wynik 100% w dowolnej kategorii oznacza wszystkie zasoby, *oceniane przez usługę Advisor*, zgodnie z najlepszymi rozwiązaniami zalecanymi przez usługę Advisor. Na drugim końcu spektrum wynik 0% oznacza, że żaden z zasobów nie został oceniony przez usługę Advisor zgodnie z zaleceniami klasyfikatora.

**Każda z pięciu kategorii ma najwyższy możliwy wynik równy 100.** Ogólny wynik doradcy jest obliczany jako suma wszystkich odpowiednich wyników kategorii, podzielona przez sumę najwyższego potencjalnego wyniku ze wszystkich odpowiednich kategorii. W przypadku większości subskrypcji oznacza to, że Doradca dodaje wynik z każdej kategorii i dzieli o 500. Ale *każdy wynik kategorii jest obliczany tylko wtedy, gdy używane są zasoby oceniane przez usługę Advisor*.

### <a name="advisor-score-calculation-example"></a>Przykład obliczenia oceny klasyfikatora

* **Wynik pojedynczej subskrypcji:** Ten przykład to prosta średnia dla wszystkich ocen kategorii usługi Advisor dla Twojej subskrypcji. Jeśli kategoria klasyfikatora otrzymuje wartość- **Cost** = 73, **niezawodność** = 85, **Operational doskonałości** = 77 i **Performance** = 100, wynik doradcy to (73 + 85 + 77 + 100)/(4x100) = 0,84% lub 84%.
* **Ocena wielu subskrypcji:** W przypadku wybrania wielu subskrypcji wszystkie wygenerowane wyniki klasyfikatora są ważone jako oceny kategorii zagregowanej. W tym przykładzie każdy wynik kategorii usługi Advisor jest agregowany na podstawie zasobów używanych przez subskrypcje. Gdy usługa Advisor ma ważone wartości kategorii zagregowane, usługa Advisor wykonuje proste Obliczanie średniej, aby zapewnić ogólną ocenę subskrypcji.

### <a name="scoring-methodology"></a>Metodologia oceniania

Obliczenia oceny klasyfikatora można obliczyć w czterech krokach:

1. Program Advisor oblicza *koszt detaliczny zasobów, których* to dotyczy. Te zasoby znajdują się w Twoich subskrypcjach, które mają co najmniej jedną rekomendację w usłudze Advisor.
1. Program Advisor oblicza *koszt detaliczny szacowanych zasobów*. Te zasoby są monitorowane przez usługę Advisor, niezależnie od tego, czy mają jakiekolwiek zalecenia, czy nie.
1. Dla każdego typu zalecenia Advisor oblicza *współczynnik zasobów w dobrej kondycji*. Ten stosunek to koszt detaliczny zasobów, których to dotyczy, podzielona przez koszt detaliczny szacowanych zasobów.
1. Doradca stosuje trzy dodatkowe wagi do współczynnika zasobów w dobrej kondycji w każdej kategorii:

   * Zalecenia o większym wpływie są ważone niż zalecenia z niższym wpływem.
   * Zasoby z długoterminowymi zaleceniami będą bardziej odporne na wyniki.
   * Zasoby, które można odroczyć lub odrzucić w usłudze Advisor, zostaną całkowicie usunięte z obliczeń oceny.

Doradca stosuje ten model na poziomie kategorii usługi Advisor, aby uzyskać ocenę klasyfikatora dla każdej kategorii. **Zabezpieczenia** wykorzystują [bezpieczny model oceny](../security-center/secure-score-security-controls.md#introduction-to-secure-score) . Prosta średnia daje wynik oceny klasyfikatora końcowego.

## <a name="advisor-score-faqs"></a>Ocena usługi Advisor — często zadawane pytania

### <a name="how-often-is-my-score-refreshed"></a>Jak często moje wyniki są odświeżane?

Wynik jest odświeżany co najmniej raz dziennie.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Dlaczego niektóre zalecenia mają pustą wartość "-" w kolumnie wpływ na ocenę kategorii?

Doradca nie zawiera natychmiast nowych zaleceń ani zaleceń z najnowszymi zmianami w modelu oceniania. Po krótkim okresie ewaluacyjnym zwykle jest to kilka tygodni, są one uwzględniane w ocenie.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Dlaczego wynik oceny kosztów jest większy dla niektórych zaleceń, nawet jeśli mają mniejsze potencjalne oszczędności?

Wynik Twojego **kosztu** odzwierciedla potencjalne oszczędności z nieużywanych zasobów i przewidywaną łatwość wdrażania tych zaleceń. Na przykład dodatkowa waga jest stosowana do zasobów, których dotyczy problem, które są bezczynne przez dłuższy czas, nawet jeśli potencjalne oszczędności są mniejsze.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Dlaczego nie mam oceny co najmniej jednej kategorii lub subskrypcji?

Doradca generuje ocenę tylko dla kategorii i subskrypcji, które mają zasoby, które są oceniane przez usługę Advisor.

### <a name="what-if-a-recommendation-isnt-relevant"></a>Co zrobić, jeśli zalecenie nie jest istotne?

W przypadku odrzucenia zalecenia z usługi Advisor zostanie ono pominięte podczas obliczania wyniku. Niebrakujące zalecenia pomagają również ulepszyć jakość zaleceń.

### <a name="why-did-my-score-change"></a>Dlaczego został zmieniony wynik?

Ocenę można zmienić w przypadku korygowania zasobów, których dotyczy problem, przez przyjęcie najlepszych rozwiązań zalecanych przez usługę Advisor. Jeśli użytkownik lub każda osoba z uprawnieniami w ramach subskrypcji ma zmodyfikowane lub utworzone nowe zasoby, można także zobaczyć fluktuacje wyników. Wynik jest oparty na współczynniku zasobów związanych z kosztami względem łącznego kosztu wszystkich zasobów.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Jak usługa Advisor oblicza koszt detaliczny zasobów w ramach subskrypcji?

Usługa Advisor korzysta z stawek płatność zgodnie z rzeczywistym użyciem publikowanych w [cenniku platformy Azure](https://azure.microsoft.com/pricing/). Stawki te nie uwzględniają żadnych odpowiednich rabatów. Stawki są następnie mnożone przez ilość użycia w ostatnim dniu przydzielonego zasobu. Pomijanie rabatów od obliczeń kosztu zasobów sprawia, że wyniki usługi Advisor są porównywalne z subskrypcjami, dzierżawcami i rejestracjami, w których rabaty mogą się różnić.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>Czy muszę wyświetlić zalecenia w usłudze Advisor, aby uzyskać punkty dla mojego wyniku?

Nie. Wynik wskazuje, czy są stosowane najlepsze rozwiązania, które zaleca usługa Advisor, nawet w przypadku, gdy te najlepsze rozwiązania zostały zastosowane z wyprzedzeniem i nigdy nie będą wyświetlane zalecenia w usłudze Advisor.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>Czy metodologia oceniania różni się od obciążeń produkcyjnych i deweloperskich?

Nie, nie teraz. Można jednak odrzucić zalecenia dotyczące poszczególnych zasobów, jeśli te zasoby są używane na potrzeby programowania i testowania, a zalecenia nie mają zastosowania.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>Czy mogę porównać wyniki między subskrypcją a zasobami 100 i subskrypcją z zasobami 100 000?

Metodologia oceniania została zaprojektowana w celu kontrolowania liczby zasobów w ramach subskrypcji i usługi. Subskrypcje o mniejszej ilości zasobów mogą mieć wyższe lub mniejsze wyniki niż subskrypcje o większej ilości zasobów.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>Co oznacza, że w kolumnie wpływ na wyniki pojawia się komunikat "wkrótce"?

Ten komunikat oznacza, że zalecenie jest nowe i pracujemy nad wprowadzeniem go do modelu oceny klasyfikatora. Po uwzględnieniu tego nowego zalecenia w obliczaniu wyniku zostanie wyświetlona wartość wpływu na ocenę dla danego zalecenia.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>Czy mój wynik zależy od tego, jak dużo spędzamy na platformie Azure?

Nie. Wynikowe wyniki nie muszą odzwierciedlać poświęconego nakładu. Niepotrzebne wydatki będą powodować obniżenie **kosztów** .

## <a name="access-advisor-score"></a>Ocena programu Access Advisor

Ocena usługi Advisor jest w publicznej wersji zapoznawczej w Azure Portal. W okienku po lewej stronie w sekcji **Advisor** Zobacz ocenę usługi **Advisor**.

![Zrzut ekranu pokazujący punkt wejścia oceny klasyfikatora.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń klasyfikatora, zobacz:

* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia usługi Advisor dotyczące kosztów](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
