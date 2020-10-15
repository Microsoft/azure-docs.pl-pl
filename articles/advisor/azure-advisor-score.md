---
title: Optymalizowanie obciążeń platformy Azure przy użyciu oceny klasyfikatora
description: Użyj oceny doradcy, aby maksymalnie wykorzystać platformę Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: c4c401a8b532c247806c5fe30728a1f4afd334a4
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074026"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Optymalizowanie obciążeń platformy Azure przy użyciu oceny klasyfikatora

## <a name="introduction-to-advisor-score"></a>Wprowadzenie do oceny klasyfikatora

Azure Advisor zapewnia zalecenia dotyczące najlepszych rozwiązań dla obciążeń. Te zalecenia są personalizowane i umożliwiają podejmowanie działań w celu ułatwienia:
* Zwiększ stan obciążeń i zoptymalizuj wdrożenia platformy Azure
* Zaaktywnie Zapobiegaj najważniejszym problemom, wykonując następujące najlepsze rozwiązania
* Oceń obciążenia platformy Azure względem pięciu filarów [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)

Jako podstawowa funkcja usługi Advisor **Ocena usługi Advisor** jest opracowana w celu ułatwienia skutecznego i efektywnego osiągnięcia tych celów. 

Aby maksymalnie wykorzystać możliwości platformy Azure, należy zrozumieć, w jaki sposób odbywa się Optymalizacja obciążenia, które usługi/zasoby są używane, a które nie. Aby dowiedzieć się więcej, zapoznaj się z informacjami na temat określania priorytetów akcji, na podstawie zaleceń, aby zmaksymalizować wynik. Ważne jest również śledzenie i raportowanie postępu wykonywanego w ramach tej przejazdu optymalizacji. **Ocena usługi Advisor**pozwala łatwo wykonać wszystkie te czynności za pomocą naszego nowego środowiska grywalizacji. Jako spersonalizowany konsultant chmury Azure Advisor stale ocenia dane telemetryczne użycia i konfigurację zasobów w celu sprawdzenia najlepszych praktyk branżowych. Następnie usługa Advisor agreguje swoje ustalenia do pojedynczego wyniku, dzięki czemu możesz natychmiast powiedzieć, jeśli podejmujesz kroki niezbędne do tworzenia niezawodnych, bezpiecznych i ekonomicznych rozwiązań. Ocena klasyfikatora składa się z ogólnej oceny, którą można podzielić na pięć ocen kategorii, jednej dla każdej kategorii Azure Advisor, która reprezentuje pięć filarów Well-Architected Framework. Postęp można śledzić w czasie, wyświetlając ogólny wynik oceny i kategorii za pomocą codziennych, tygodniowych i miesięcznych trendów, a następnie można ustawić testy porównawcze, aby ułatwić osiągnięcie celów. 

 ![Środowisko oceny usługi Advisor](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Jak korzystać z oceny klasyfikatora
W usłudze Advisor przedstawiono ogólny wynik doradcy i podział dla kategorii doradców w procentach. Wynik 100% w dowolnej kategorii oznacza, że wszystkie zasoby oceniane przez usługę Advisor są zgodne z najlepszymi rozwiązaniami zalecanymi przez usługę Advisor. Na drugim końcu spektrum wynik 0% oznacza, że żaden z zasobów ocenianych przez klasyfikatora nie jest zgodny z zaleceniami klasyfikatora. Korzystając z tych ziaren wynikowych, można łatwo osiągnąć Poniższy przepływ:
* **Ocena usługi Advisor** ułatwiająca Określanie planu bazowego, w jaki sposób obciążenie/subskrypcje odbywa się na podstawie oceny klasyfikatora. Możesz również zobaczyć trendy historyczne, aby zrozumieć, jaki jest trend.
* Ocena **kategorii usługi Advisor** dla każdego zalecenia informuje o tym, które zaległe rekomendacje zwiększą wynik. Te wartości odzwierciedlają zarówno wagę zalecenia, jak i naszą przewidywalną łatwość implementacji, aby mieć pewność, że można uzyskać największą wartość czasu i pomóc w uzyskaniu priorytetyzacji
* **Ocena kategorii wpływ** każdego zalecenia, które ułatwiają określanie priorytetów akcji korygowania dla każdej kategorii

Wkład każdego zalecenia do oceny kategorii jest widoczny na stronie oceny klasyfikatora w Azure Portal. Można zwiększyć każdy wynik kategorii według wartości procentowej wymienionej w kolumnie wpływ na ocenę kategorii. Ta wartość odzwierciedla zarówno wagę zalecenia w ramach kategorii, jak i przewidywaną łatwość implementacji, która pozwala na rozwiązanie potencjalnie wysuniętego owocu. Skoncentrowanie się na zaleceniach o największym wpływie na wyniki pomoże Ci w tym czasie.  

![Wpływ oceny klasyfikatora](./media/advisor-score-2.png)

Jeśli którykolwiek z zaleceń klasyfikatora nie dotyczy pojedynczego zasobu, można odroczyć lub odrzucić te zalecenia i zostaną one wykluczone z obliczenia wyniku z następnego odświeżenia. Doradca będzie również używać tych danych wejściowych jako dodatkowych informacji zwrotnych w celu ulepszenia modelu.

## <a name="how-is-advisor-score-calculated"></a>Jak obliczana jest ocena klasyfikatora?
W usłudze Advisor są wyświetlane wyniki kategorii oraz ogólny wynik doradcy jako wartości procentowe. Wynik 100% w dowolnej kategorii oznacza wszystkie zasoby, *oceniane przez usługę Advisor*, zgodnie z najlepszymi rozwiązaniami zalecanymi przez usługę Advisor. Na drugim końcu spektrum wynik 0% oznacza, że żaden z zasobów nie został oceniony przez usługę Advisor i nie jest zgodny z zaleceniami klasyfikatora. 
**Każda z pięciu kategorii ma najwyższy możliwy wynik równy 100.** Ogólny wynik doradcy jest obliczany jako suma wszystkich odpowiednich wyników kategorii, podzielona przez sumę najwyższego potencjalnego wyniku ze wszystkich odpowiednich kategorii. W przypadku większości subskrypcji oznacza to, że Doradca dodaje wynik z każdej kategorii i dzieli przez 500. Jednak **każdy wynik kategorii jest obliczany tylko wtedy, gdy używane są zasoby oceniane przez usługę Advisor.**

**Przykład obliczenia oceny klasyfikatora**
* Wynik pojedynczej subskrypcji: jest to prosta średnia dla wszystkich ocen kategorii usługi Advisor dla Twojej subskrypcji. Jeśli kategoria klasyfikatora ma wartość as-Cost = 73, Reliabilit = 85, doskonałości operacyjnej = 77, Performance = 100; **wynikiem usługi Advisor** będzie (73 + 85 + 77 + 100)/(4x100) = 0,84 lub 84%.
* Ocena wielu subskrypcji: Jeśli wybrano wiele subskrypcji, ogólna ocena usługi Advisor zostanie wygenerowane ważone. W tym przykładzie każdy wynik kategorii usługi Advisor jest agregowany na podstawie zasobów używanych przez subskrypcje. Po uzyskaniu zagregowanych ocen kategorii ważone zostanie prosta średnia dla subskrypcji. 


### <a name="scoring-methodology"></a>Metodologia oceniania: 
Obliczenia oceny klasyfikatora można obliczyć w czterech krokach:
1. Program Advisor oblicza **koszt detaliczny zasobów**, których dotyczy problem, czyli zasoby w Twoich subskrypcjach, które mają co najmniej jedno zalecenie w usłudze Advisor.
2. Program Advisor oblicza **koszt detaliczny ocenianych zasobów**, czyli zasobów monitorowanych przez usługę Advisor, niezależnie od tego, czy mają jakiekolwiek zalecenia, czy nie. 
3. Dla każdego typu rekomendacji Doradca oblicza **współczynnik zasobów w dobrej kondycji**, który jest kosztem detalicznym zasobów, których dotyczy problem, podzieloną przez koszt detaliczny szacowanych zasobów.
4. Doradca stosuje trzy dodatkowe wagi do współczynnika zasobów w dobrej kondycji w każdej kategorii:
  * Zalecenia o większym wpływie są ważone, niż te z niższym wpływem.
  * Zasoby z długoterminowymi zaleceniami będą bardziej odporne na wyniki.
  * Zasoby, które można odroczyć lub odrzucić w usłudze Advisor, zostaną całkowicie usunięte z obliczeń oceny. 
    
Doradca stosuje ten model na poziomie kategorii usługi Advisor (zabezpieczenia korzystają z modelu [oceny](../security-center/secure-score-security-controls.md#introduction-to-secure-score) zabezpieczeń), co daje ocenę oceny dla każdej kategorii, a następnie prostą średnią.


## <a name="advisor-score-faq"></a>Ocena usługi Advisor — często zadawane pytania
* **Jak często moje wyniki są odświeżane?**
Wynik jest odświeżany co najmniej raz dziennie. 
* **Dlaczego niektóre zalecenia mają pustą wartość "-" w kolumnie wpływ na ocenę kategorii?** Usługa Advisor nie zawiera natychmiast nowych zaleceń ani tych, których dotyczą ostatnie zmiany w modelu oceniania. Po krótkim okresie ewaluacyjnym zwykle jest to kilka tygodni, ale zostaną one uwzględnione w ocenie. 
* **Dlaczego wynik oceny kosztów jest większy dla niektórych zaleceń, nawet jeśli mają mniejsze potencjalne oszczędności?**
Wynik Twojego kosztu odzwierciedla potencjalne oszczędności z nieużywanych zasobów i przewidywaną łatwość wdrażania tych zaleceń. Na przykład dodatkowa waga jest stosowana do zasobów, których dotyczy problem, które są bezczynne przez dłuższy czas, nawet jeśli potencjalne oszczędności są mniejsze. 
* **Dlaczego nie mam oceny co najmniej jednej kategorii lub subskrypcji?**
Program Advisor wygeneruje ocenę tylko dla kategorii i subskrypcji, które mają zasoby oceniane przez usługę Advisor.
* **Co zrobić, jeśli zalecenie nie jest istotne?**
W przypadku odrzucenia zalecenia z usługi Advisor zostanie ono pominięte podczas obliczania wyniku. Odrzucanie zaleceń pomaga również usłudze Advisor ulepszyć jakość zaleceń.
* **Dlaczego został zmieniony wynik?** Ocenę można zmienić w przypadku korygowania zasobów, których dotyczy problem, przez przyjęcie najlepszych rozwiązań zalecanych przez usługę Advisor. Jeśli użytkownik lub każda osoba z uprawnieniami w ramach Twojej subskrypcji zmodyfikowano lub utworzyła nowe zasoby, można także zobaczyć fluktuacje wyników, ponieważ wynik jest oparty na współczynniku zasobów, na które ma wpływ koszt, względem łącznego kosztu wszystkich zasobów.
* **Jak usługa Advisor oblicza koszt detaliczny zasobów w ramach subskrypcji?**
Usługa Advisor korzysta z stawek płatność zgodnie z rzeczywistym użyciem publikowanych na stronie cennika usługi Azure.com, która nie odzwierciedla żadnych odpowiednich rabatów, pomnożonych przez ilość użycia w ostatnim dniu przydziału zasobu. Pomijanie rabatów od obliczeń kosztu zasobów powoduje, że Ocena usługi Advisor jest porównywalna z subskrypcjami, dzierżawcami i rejestracjami, w których rabaty mogą się różnić. 
* **Czy muszę wyświetlić zalecenia w usłudze Advisor, aby uzyskać punkty dla mojego wyniku?** Nie. Wynik wskazuje, czy są stosowane najlepsze rozwiązania, które zaleca usługa Advisor, nawet w przypadku, gdy te najlepsze rozwiązania zostały zastosowane z wyprzedzeniem i nigdy nie będą wyświetlane zalecenia w usłudze Advisor.
* **Czy metodologia oceniania różni się od obciążeń produkcyjnych i deweloperskich?**
Nie, ale nie na razie, ale możesz odrzucić zalecenia dotyczące poszczególnych zasobów, jeśli te zasoby są używane do tworzenia i testowania, a zalecenie nie ma zastosowania.
* **Czy mogę porównać wyniki między subskrypcją a zasobami 100 i subskrypcją z zasobami 100 000?**
Metodologia oceniania została zaprojektowana w celu kontrolowania liczby zasobów w ramach subskrypcji i usługi, dzięki czemu subskrypcje z mniejszą ilością zasobów mogą mieć wyższe lub mniejsze wyniki niż subskrypcje o większej ilości zasobów. 
* **Co oznacza, że w kolumnie wpływ na wyniki pojawia się komunikat "wkrótce"?**
Oznacza to, że jest to nowe zalecenie i nadal pracujemy nad wprowadzeniem go w naszym modelu oceny klasyfikatora. Po uwzględnieniu tego nowego zalecenia w obliczaniu wyniku zostanie wyświetlona wartość wpływu na ocenę dla danego zalecenia.  
* **Czy mój wynik zależy od tego, jak dużo spędzamy na platformie Azure?**
Nie, wynik nie musi odzwierciedlać ilości wydatków i niepotrzebnych wydatków spowoduje obniżenie kosztów.

## <a name="how-to-access-advisor"></a>Jak uzyskać dostęp do klasyfikatora
Ocena usługi Advisor jest w publicznej wersji zapoznawczej w Azure Portal. Musisz przejść do sekcji Advisor, aby znaleźć wyniki doradcy jako drugi element menu w lewym okienku nawigacji. 

![Punkt wejścia oceny klasyfikatora](./media/advisor-score-3.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia usługi Advisor dotyczące kosztów](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)