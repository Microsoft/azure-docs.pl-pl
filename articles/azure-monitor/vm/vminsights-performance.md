---
title: Jak uzyskać Wykres wydajności za pomocą usługi VM Insights
description: Wydajność to funkcja szczegółowych informacji o maszynie wirtualnej, która automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje na temat korzystania z niego w różnych scenariuszach.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/31/2020
ms.openlocfilehash: 02cb6a0c69fef1a33dd9327ddbd86e02b376a60e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046469"
---
# <a name="how-to-chart-performance-with-vm-insights"></a>Jak uzyskać Wykres wydajności za pomocą usługi VM Insights

Szczegółowe informacje o maszynie wirtualnej zawierają zestaw wykresów wydajności przeznaczonych dla kilku kluczowych wskaźników wydajności (KPI), aby pomóc w ustaleniu, jak dobrze działa maszyna wirtualna. Wykresy przedstawiają wykorzystanie zasobów w danym okresie, aby można było identyfikować wąskie gardła, anomalie lub przełączać się do perspektywy zawierającej poszczególne maszyny, aby wyświetlić wykorzystanie zasobów na podstawie wybranej metryki. Chociaż istnieje wiele elementów, które należy wziąć pod uwagę podczas pracy z wydajnością, usługa VM Insights monitoruje kluczowe wskaźniki wydajności systemu operacyjnego związane z procesorem, pamięcią, kartą sieciową i wykorzystaniem dysku. Wydajność uzupełnia funkcję monitorowania kondycji i pomaga w ujawnianiu problemów, które wskazują na awarię składnika systemu, zapewnia wsparcie i optymalizację w celu osiągnięcia wydajności lub planowania pojemności.  

## <a name="limitations"></a>Ograniczenia
Poniżej przedstawiono ograniczenia dotyczące zbierania danych o wydajności za pomocą usługi VM Insights.

- **Dostępna pamięć** nie jest dostępna dla maszyn wirtualnych z systemem Red Hat Linux (RHEL) 6. Ta Metryka jest obliczana na podstawie **MemAvailable** , która została wprowadzona w [jądrze w wersji 3,14](http://www.man7.org/linux/man-pages/man1/free.1.html).
- Metryki są dostępne tylko w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux przy użyciu systemu plików XFS lub rodziny plików EXT (EXT2, EXT3, EXT4).

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspektywa z obsługą wiele maszyn wirtualnych od Azure Monitor

W Azure Monitor funkcja wydajność zapewnia widok wszystkich monitorowanych maszyn wirtualnych wdrożonych w grupach roboczych w ramach subskrypcji lub w środowisku użytkownika. Aby uzyskać dostęp z Azure Monitor, wykonaj następujące czynności. 

1. W Azure Portal wybierz pozycję **Monitoruj**. 
2. Wybierz **Virtual Machines** w sekcji **rozwiązania** .
3. Wybierz kartę **Wydajność**.

![Widok listy górnych wyników wydajności usługi VM Insights](media/vminsights-performance/vminsights-performance-aggview-01.png)

Na karcie **pierwsze N wykresów** , jeśli masz więcej niż jeden obszar roboczy log Analytics, wybierz obszar roboczy z włączonym rozwiązaniem w selektorze **obszaru roboczego** w górnej części strony. Selektor **grupy** zwróci subskrypcje, grupy zasobów, [grupy komputerów](../logs/computer-groups.md)i zestawy skalowania maszyn wirtualnych na komputerach związanych z wybranym obszarem roboczym, za pomocą których można filtrować wyniki wyświetlane na wykresach na tej stronie oraz na innych stronach. Wybór dotyczy tylko funkcji wydajności i nie jest przenoszone do poziomu kondycji ani mapy.  

Domyślnie wykresy przedstawiają ostatnie 24 godziny. Za pomocą selektora **TimeRange** można wykonać zapytanie o historyczne zakresy czasu o wartości do 30 dni, aby pokazać, jak wydajność jest wyszukiwana w przeszłości.

Na stronie zostaną wyświetlone pięć wykresów wykorzystania pojemności:

* Użycie procesora CPU%-pokazuje pięć najważniejszych maszyn z najwyższym średnim wykorzystaniem procesora 
* Dostępna pamięć — pokazuje pięć najważniejszych maszyn z najniższą średnią ilością dostępnej pamięci 
* Używane miejsce na dysku logicznym%-pokazuje pięć najważniejszych maszyn z najwyższym średnim wykorzystanym miejscem na dysku (%) na wszystkich woluminach dysków 
* Szybkość wysłania bajtów — pokazuje pięć najważniejszych maszyn z największą średnią ilością wysłanych bajtów 
* Szybkość odbierania bajtów — pokazuje pięć najważniejszych maszyn z największą ilością odebranych bajtów 

Kliknięcie ikony pinezki w prawym górnym rogu dowolnego z pięciu wykresów spowoduje Przypinanie wybranego wykresu do ostatnio oglądanego pulpitu nawigacyjnego platformy Azure.  Z poziomu pulpitu nawigacyjnego można zmieniać rozmiar wykresu i zmienić jego położenie. Wybranie wykresu z pulpitu nawigacyjnego spowoduje przekierowanie do usługi VM Insights i załadowanie poprawnego zakresu i widoku.  

Kliknięcie ikony znajdującej się po lewej stronie ikony pinezki na jednym z pięciu wykresów spowoduje otwarcie widoku **listy pierwszych N** .  Tutaj zobaczysz wykorzystanie zasobów dla tej metryki wydajności przez poszczególne maszyny wirtualne w widoku listy i który komputer ma największe tendencje.  

![Widok listy pierwszych N dla wybranej metryki wydajności](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Po kliknięciu maszyny wirtualnej okienko **Właściwości** zostanie rozwinięte po prawej stronie, aby wyświetlić właściwości wybranego elementu, takie jak informacje o systemie zgłoszone przez system operacyjny, właściwości maszyny wirtualnej platformy Azure itd. Kliknięcie jednej z opcji w sekcji **szybkie linki** spowoduje przekierowanie do tej funkcji bezpośrednio z wybranej maszyny wirtualnej.  

![Okienko właściwości maszyny wirtualnej](./media/vminsights-performance/vminsights-properties-pane-01.png)

Przejdź do karty **zagregowane wykresy** , aby wyświetlić metryki wydajności odfiltrowane według średnich lub percentylych miar.  

![Widok zagregowany wydajności usługi VM Insights](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Dostępne są następujące wykresy wykorzystania pojemności:

* Użycie procesora CPU%-wartości domyślne pokazujące średnią i górną używany 95. percentyl 
* Dostępna pamięć — domyślne wartości pokazujące średnią, 5 pierwszych i dziesiątą percentyl 
* Używane miejsce na dysku logicznym%-Defaults pokazujące średnią i używany 95. percentyl 
* Szybkość wysyłania bajtów — wartości domyślne pokazujące średnią liczbę wysłanych bajtów 
* Szybkość odbierania bajtów — wartości domyślne pokazujące średnią liczbę odebranych bajtów

Można również zmienić stopień szczegółowości wykresów w zakresie czasu, zaznaczając w selektorze percentylu **średnią**, **minimalną**, **maksymalną**, **pięćdziesiąt**, **90** i **używany 95.** .

Aby wyświetlić wykorzystanie zasobów przez pojedynczą maszynę wirtualną w widoku listy i zobaczyć, który komputer jest trendem o największym wykorzystaniu, wybierz kartę **Lista pierwszych N** .  Na stronie **Lista pierwszych N** przedstawiono 20 najważniejszych maszyn posortowanych przez najbardziej wykorzystywane przez używany 95. percentyl w przypadku użycia *procesora CPU (%)*.  Więcej maszyn można zobaczyć, wybierając pozycję **Załaduj więcej**, a wyniki rozszerzają, aby pokazać Najpopularniejsze maszyny 500. 

>[!NOTE]
>Lista nie może jednocześnie wyświetlać więcej niż 500 maszyn.  
>

![Przykład N najważniejszych stron list](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Aby filtrować wyniki na określonej maszynie wirtualnej na liście, wprowadź jej nazwę w polu tekstowym **Wyszukaj według nazwy** .  

Jeśli wolisz przeglądać użycie z innej metryki wydajności, z listy rozwijanej **Metryka** wybierz **dostępną pamięć**, wartość **zajętego miejsca na dysku (%)**, **bajty odebrane przez sieć** lub bajty **wysłane przez sieć** oraz listę aktualizacji, aby pokazać zakres użycia tej metryki.  

Wybranie maszyny wirtualnej z listy spowoduje otwarcie panelu **Właściwości** po prawej stronie strony i w tym miejscu można wybrać **szczegóły wydajności**.  Zostanie otwarta strona **szczegółów maszyny wirtualnej** i ma ona zakres dla tej maszyny wirtualnej, podobnie jak w przypadku uzyskiwania dostępu do usługi VM Insights bezpośrednio z maszyny wirtualnej platformy Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Wyświetlanie wydajności bezpośrednio z maszyny wirtualnej platformy Azure

Aby uzyskać dostęp bezpośrednio z maszyny wirtualnej, wykonaj następujące czynności.

1. W Azure Portal wybierz pozycję **Virtual Machines**. 
2. Z listy wybierz maszynę wirtualną, a następnie w sekcji **monitorowanie** wybierz pozycję **szczegółowe informacje**.  
3. Wybierz kartę **Wydajność**. 

Ta strona zawiera nie tylko wykresy wykorzystania wydajności, ale również tabelę zawierającą dla każdego wykrytego dysku logicznego, jego pojemność, wykorzystanie i łączną średnią wartość na podstawie każdej miary.  

Dostępne są następujące wykresy wykorzystania pojemności:

* Użycie procesora CPU%-wartości domyślne pokazujące średnią i górną używany 95. percentyl 
* Dostępna pamięć — domyślne wartości pokazujące średnią, 5 pierwszych i dziesiątą percentyl 
* Używane miejsce na dysku logicznym%-Defaults pokazujące średnią i używany 95. percentyl 
* Liczba operacji we/wy dysku logicznego — wartości domyślne pokazujące średnią i używany 95. percentyl
* Dysk logiczny MB/s — wartości domyślne pokazujące średnią i używany 95. percentyl
* Maksymalny użyty dysk logiczny%-Defaults pokazujący średnią i używany 95. percentyl
* Szybkość wysyłania bajtów — wartości domyślne pokazujące średnią liczbę wysłanych bajtów 
* Szybkość odbierania bajtów — wartości domyślne pokazujące średnią liczbę odebranych bajtów

Kliknięcie ikony pinezki w prawym górnym rogu dowolnego z wykresów powoduje Przypinanie zaznaczonego wykresu do ostatnio oglądanego pulpitu nawigacyjnego platformy Azure. Z poziomu pulpitu nawigacyjnego można zmieniać rozmiar wykresu i zmienić jego położenie. Wybranie wykresu z pulpitu nawigacyjnego przekierowuje do usługi VM Insights i ładuje widok szczegółów wydajności dla maszyny wirtualnej.  

![Wydajność usługi VM Insights bezpośrednio z widoku maszyny wirtualnej](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Wyświetlanie wydajności bezpośrednio z zestawu skalowania maszyn wirtualnych platformy Azure

Aby uzyskać dostęp bezpośrednio z zestawu skalowania maszyn wirtualnych platformy Azure, wykonaj następujące czynności.

1. W Azure Portal wybierz pozycję **zestawy skalowania maszyn wirtualnych**.
2. Z listy wybierz maszynę wirtualną, a następnie w sekcji **monitorowanie** wybierz pozycję **szczegółowe** dane, aby wyświetlić kartę **wydajność** .

Ta strona ładuje widok wydajności Azure Monitor w zakresie do wybranego zestawu skalowania. Dzięki temu można zobaczyć pierwsze N wystąpień w zestawie skalowania w zestawie monitorowanych metryk, wyświetlić zagregowaną wydajność w zestawie skalowania i zobaczyć trendy wybranych metryk w poszczególnych wystąpieniach N zestawu skalowania. Wybranie wystąpienia z widoku listy umożliwia załadowanie jego mapy lub przechodzenie do szczegółowego widoku wydajności dla tego wystąpienia.

Kliknięcie ikony pinezki w prawym górnym rogu dowolnego z wykresów powoduje Przypinanie zaznaczonego wykresu do ostatnio oglądanego pulpitu nawigacyjnego platformy Azure. Z poziomu pulpitu nawigacyjnego można zmieniać rozmiar wykresu i zmienić jego położenie. Wybranie wykresu z pulpitu nawigacyjnego przekierowuje do usługi VM Insights i ładuje widok szczegółów wydajności dla maszyny wirtualnej.  

![Wydajność usługi VM Insights bezpośrednio z widoku zestawu skalowania maszyn wirtualnych](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Możesz również uzyskać dostęp do szczegółowego widoku wydajności dla określonego wystąpienia z widoku wystąpienia zestawu skalowania. Przejdź do pozycji **wystąpienia** w sekcji **Ustawienia** , a następnie wybierz pozycję **szczegółowe** dane.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [skoroszytów](vminsights-workbooks.md) zawartych w usłudze VM Insights w celu dalszej analizy metryk wydajności i sieci.  

- Aby dowiedzieć się więcej o wykrytych zależnościach aplikacji, zobacz [Wyświetlanie mapy usługi VM Insights](vminsights-maps.md).
