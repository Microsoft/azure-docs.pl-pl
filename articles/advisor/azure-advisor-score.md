---
title: Optymalizowanie obciążeń platformy Azure przy użyciu oceny klasyfikatora
description: Użyj oceny doradcy, aby maksymalnie wykorzystać platformę Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 720a2b358e35d776a7233452eee2bd69b521654f
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056238"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Optymalizowanie obciążeń platformy Azure przy użyciu oceny klasyfikatora

## <a name="introduction-to-advisor-score"></a>Wprowadzenie do oceny klasyfikatora

Azure Advisor zapewnia zalecenia dotyczące najlepszych rozwiązań dla obciążeń. Te zalecenia są personalizowane i umożliwiają podejmowanie działań w celu ułatwienia:
* Zwiększ stan obciążeń i zoptymalizuj wdrożenia platformy Azure
* Zaaktywnie Zapobiegaj najważniejszym problemom, wykonując następujące najlepsze rozwiązania
* Oceń obciążenia platformy Azure względem pięciu filarów [Microsoft Azure architektury dobrze zaprojektowanej](https://docs.microsoft.com/azure/architecture/framework/)

Jako podstawowa funkcja usługi Advisor **Ocena usługi Advisor** jest opracowana w celu ułatwienia skutecznego i efektywnego osiągnięcia tych celów. 

Aby maksymalnie wykorzystać możliwości platformy Azure, należy zrozumieć, w jaki sposób odbywa się Optymalizacja obciążenia, które usługi/zasoby są używane, a które nie. Aby dowiedzieć się więcej, zapoznaj się z informacjami na temat określania priorytetów akcji, na podstawie zaleceń, aby zmaksymalizować wynik. Ważne jest również śledzenie i raportowanie postępu wykonywanego w ramach tej przejazdu optymalizacji. **Ocena usługi Advisor**pozwala łatwo wykonać wszystkie te czynności za pomocą naszego nowego środowiska grywalizacji. Jako spersonalizowany konsultant chmury Azure Advisor stale ocenia dane telemetryczne użycia i konfigurację zasobów w celu sprawdzenia najlepszych praktyk branżowych. Następnie usługa Advisor agreguje swoje ustalenia do pojedynczego wyniku, dzięki czemu możesz natychmiast powiedzieć, jeśli podejmujesz kroki niezbędne do tworzenia niezawodnych, bezpiecznych i ekonomicznych rozwiązań. Ocena klasyfikatora składa się z ogólnej oceny, która może być podzielona na pięć ocen kategorii, po jednej dla każdej kategorii Azure Advisor, która reprezentuje pięć filarów dobrze zaprojektowanej struktury. Postęp można śledzić w czasie, wyświetlając ogólny wynik oceny i kategorii za pomocą codziennych, tygodniowych i miesięcznych trendów, a następnie można ustawić testy porównawcze, aby ułatwić osiągnięcie celów. 

 ![Środowisko oceny usługi Advisor](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Jak korzystać z oceny klasyfikatora
W usłudze Advisor przedstawiono ogólny wynik doradcy i podział dla kategorii doradców w procentach. Wynik 100% w dowolnej kategorii oznacza, że wszystkie zasoby oceniane przez usługę Advisor są zgodne z najlepszymi rozwiązaniami zalecanymi przez usługę Advisor. Na drugim końcu spektrum wynik 0% oznacza, że żaden z zasobów ocenianych przez klasyfikatora nie jest zgodny z zaleceniami klasyfikatora. Korzystając z tych ziaren wynikowych, można łatwo osiągnąć Poniższy przepływ:
* **Ocena usługi Advisor** ułatwiająca Określanie planu bazowego, w jaki sposób obciążenie/subskrypcje odbywa się na podstawie oceny klasyfikatora. Możesz również zobaczyć trendy historyczne, aby zrozumieć, jaki jest trend.
* **Wyniki kategorii usługi Advisor** ułatwiające zrozumienie, które kategorie wymagają większej uwagi, i ułatwiają określanie priorytetów
* **Możliwy wzrost wyniku** każdego zalecenia, aby pomóc w ustaleniu priorytetów akcji korygowania dla każdej kategorii

Wkład każdego zalecenia do oceny jest widoczny na stronie Przegląd w Azure Portal. Możesz zwiększyć swój wynik, wdrażając najlepsze rozwiązania i można ustalić priorytety zaleceń, które mają największy **możliwy wzrost wyniku** , aby osiągnąć najszybszy postęp w czasie.  

![Wpływ oceny klasyfikatora](./media/advisor-score-2.png)

Ponieważ metodologia oceniania doradcy stosuje dodatkową wagę do tańszych zasobów dzięki długotrwałym zaleceń, można wykonać najwięcej postępów, korygowaniem najpierw zasoby o najwyższej cenie detalicznej. Jeśli którykolwiek z zaleceń klasyfikatora nie dotyczy pojedynczego zasobu, możesz odrzucić te zalecenia, aby wykluczyć je z obliczeń oceny i przesłać opinię do usługi Advisor, aby ulepszyć jej zalecenia. 

## <a name="how-is-advisor-score-calculated"></a>Jak obliczana jest ocena klasyfikatora?
W usłudze Advisor są wyświetlane wyniki kategorii oraz ogólny wynik doradcy jako wartości procentowe. Wynik 100% w dowolnej kategorii oznacza wszystkie zasoby, *oceniane przez usługę Advisor*, zgodnie z najlepszymi rozwiązaniami zalecanymi przez usługę Advisor. Na drugim końcu spektrum wynik 0% oznacza, że żaden z zasobów nie został oceniony przez usługę Advisor i nie jest zgodny z zaleceniami klasyfikatora. 
**Każda z pięciu kategorii ma najwyższy możliwy wynik równy 100.** Ogólny wynik doradcy jest obliczany jako suma wszystkich odpowiednich wyników kategorii, podzielona przez sumę najwyższego potencjalnego wyniku ze wszystkich odpowiednich kategorii. W przypadku większości subskrypcji oznacza to, że Doradca dodaje wynik z każdej kategorii i dzieli przez 500. Jednak **każdy wynik kategorii jest obliczany tylko wtedy, gdy używane są zasoby oceniane przez usługę Advisor.**

### <a name="scoring-methodology"></a>Metodologia oceniania: 
Obliczenia oceny klasyfikatora można obliczyć w czterech krokach:
1. Program Advisor oblicza **dzienny koszt detaliczny zasobów**, których dotyczy problem, czyli zasoby w Twoich subskrypcjach, które mają co najmniej jedno zalecenie w usłudze Advisor.
2. Program Advisor oblicza **dzienny koszt detaliczny szacowanych zasobów**, które są zasobami monitorowanymi przez usługę Advisor, niezależnie od tego, czy mają jakiekolwiek zalecenia, czy nie. 
3. Dla każdego typu zalecenia Advisor oblicza **współczynnik zasobów w dobrej kondycji**, który jest kosztem zasobów, których dotyczy problem, podzieloną przez koszt ocenianych zasobów.
4. Doradca stosuje trzy dodatkowe wagi do współczynnika zasobów w dobrej kondycji w każdej kategorii:
* Zalecenia o większym wpływie są ważone, niż te z niższym wpływem.
* Zasoby z długoterminowymi zaleceniami będą bardziej odporne na wyniki.
* Zasoby, które zostały odrzucone w usłudze Advisor, zostaną całkowicie usunięte z obliczeń oceny. 
    
Doradca stosuje ten model na poziomie kategorii usługi Advisor (zabezpieczenia korzystają z modelu [oceny](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) zabezpieczeń), co daje ocenę oceny dla każdej kategorii, a następnie prostą średnią.


## <a name="advisor-score-faq"></a>Ocena usługi Advisor — często zadawane pytania
* **Jak często moje wyniki są odświeżane?**
Wynik jest odświeżany co najmniej raz dziennie. 
* **Czy muszę wyświetlić zalecenia w usłudze Advisor, aby uzyskać punkt dla mojego wyniku?**
Nie. Wynik pokazuje, czy są stosowane najlepsze rozwiązania, które zaleca usługa Advisor, nawet jeśli te zalecenia nie zostaną nigdy wyświetlone w usłudze Advisor i nie zapoznają się z najlepszymi rozwiązaniami.  
* **Jak Doradca oblicza dzienny koszt detaliczny zasobów w ramach subskrypcji?**
Usługa Advisor korzysta z stawek *płatność zgodnie z rzeczywistym* użyciem publikowanych na stronie cennika usługi Azure.com, która nie odzwierciedla żadnych odpowiednich rabatów, pomnożonych przez ilość użycia w ostatnim dniu przydziału zasobu. Pomijanie rabatów od obliczeń kosztu zasobów powoduje, że Ocena usługi Advisor jest porównywalna z subskrypcjami, dzierżawcami i rejestracjami, w których rabaty mogą się różnić. 
* **Co zrobić, jeśli zalecenie nie jest istotne?**
W przypadku odrzucenia zalecenia z usługi Advisor zostanie ono pominięte podczas obliczania wyniku. Odrzucanie zaleceń pomaga również usłudze Advisor ulepszyć jakość zaleceń.
* **Dlaczego został zmieniony wynik?** Ocenę można zmienić w przypadku korygowania zasobów, których dotyczy problem, przez przyjęcie najlepszych rozwiązań zalecanych przez usługę Advisor. Jeśli użytkownik lub każda osoba z uprawnieniami w ramach Twojej subskrypcji zmodyfikowano lub utworzyła nowe zasoby, można także zobaczyć fluktuacje wyników, ponieważ wynik jest oparty na współczynniku zasobów, na które ma wpływ koszt, względem łącznego kosztu wszystkich zasobów.
* **Czy mój wynik zależy od tego, jak dużo spędzamy na platformie Azure?**
Nie. Wynik jest przeznaczony do kontrolowania rozmiaru subskrypcji i usługi mieszanej. 
* **Czy metodologia oceniania różni się od obciążeń produkcyjnych i deweloperskich?**
Nie, ale nie na razie, ale możesz odrzucić zalecenia dotyczące poszczególnych zasobów, jeśli te zasoby są używane do tworzenia i testowania, a zalecenie nie ma zastosowania.
* **Czy mogę porównać wyniki między subskrypcją a zasobami 100 i subskrypcją z zasobami 100 000?**
Metodologia oceniania została zaprojektowana w celu kontrolowania liczby zasobów w ramach subskrypcji i usługi, dzięki czemu subskrypcje z mniejszą ilością zasobów mogą mieć wyższe lub mniejsze wyniki niż subskrypcje o większej ilości zasobów. 

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
