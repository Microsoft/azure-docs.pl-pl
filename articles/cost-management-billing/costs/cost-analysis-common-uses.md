---
title: Najczęstsze zastosowania analizy kosztów w usłudze Azure Cost Management
description: W tym artykule wyjaśniono, jak uzyskać wyniki dla typowych zadań analizy kosztów w usłudze Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 72b371fd1184c9c9e69593c5ad7e57310abf1de2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600029"
---
# <a name="common-cost-analysis-uses"></a>Najczęstsze zastosowania analizy kosztów

Użytkownicy usługi Azure Cost Management często poszukują odpowiedzi na pytania zadawane przez innych. Ten artykuł zawiera instrukcje, jak uzyskać wyniki dla typowych zadań analizy kosztów w usłudze Cost Management.

## <a name="view-forecasted-costs"></a>Wyświetl prognozowane koszty

Prognozowane koszty są wyświetlane w obszarach analizy kosztów widoku obszaru i widoku skumulowanego kolumnowego. Prognoza jest oparta na historii użycia zasobów. Zmiany dotyczące użycia zasobów wpływają na przewidywane koszty.

W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.

W widoku domyślnym górny wykres zawiera sekcje kosztu rzeczywistego/zamortyzowanego i prognozowanego. Jednolity kolor wykresu przedstawia koszt rzeczywisty/zamortyzowany. Kolor zacieniowany przedstawia koszt prognozowany.

[![Prognozowany koszt](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>Wyświetlanie prognozowanych kosztów pogrupowanych według usługi

Widok domyślny nie przedstawia grupy prognozowanych kosztów według usługi, dlatego należy dodać odpowiednią grupę.

W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.

Wybierz pozycję **Grupuj według**  >  **Nazwa usługi**.

Widok przedstawia koszty pogrupowane dla każdej usługi. Prognozowany koszt nie jest obliczany dla każdej usługi. Jest on przewidywany dla **sumy** wszystkich usług.

[![Pogrupowany koszt prognozowany](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>Wyświetlanie prognozowanych kosztów usługi

Prognozowane koszty można zawęzić do jednej usługi. Na przykład można zobaczyć przewidywane koszty tylko dla maszyn wirtualnych.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz pozycję **Dodaj filtr**, a następnie pozycję **Nazwa usługi**.
1. Z listy **wyboru** wybierz usługę. Na przykład wybierz pozycję **maszyny wirtualne**.

Zapoznaj się z rzeczywistym kosztem wybranej usługi i prognozowanym kosztem.

Do widoku można dodać więcej dostosowań.

1. Dodaj drugi filtr dla **miernika** i wybierz wartość do odfiltrowania dla pojedynczego typu miernika w ramach wybranej nazwy usługi.
1. Grupuj według pozycji **Zasób**, aby wyświetlić określone zasoby, które generują koszt. Prognozowany koszt nie jest obliczany dla każdej usługi. Jest on przewidywany dla **sumy** wszystkich zasobów.

[![Prognozowany koszt usługi](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Wyświetlanie jednocześnie kosztów platform Azure i AWS  

Aby wyświetlić koszty platform Azure i AWS, użyj zakresów grup zarządzania na platformie Azure.

1. Utwórz grupę zarządzania lub wybierz istniejącą grupę.
1. Przypisz istniejące subskrypcje platformy Azure do tej grupy zarządzania.
1. Przypisz *tę samą* grupę zarządzania do połączonego konta dla łącznika.
1. Przejdź do analizy kosztów i wybierz pozycję **Koszty skumulowane**.
1. Wybierz pozycję **Grupuj według**  -  **Dostawca**.

## <a name="view-cost-breakdown-by-azure-service"></a>Wyświetlanie podziału kosztów według usługi platformy Azure

Wyświetlanie kosztów według usługi platformy Azure może pomóc w lepszym zrozumieniu, które części infrastruktury generują największe koszty. Na przykład koszty obliczeniowe maszyn wirtualnych mogą być małe. Jednak ze względu na ilość informacji emitowanych przez maszyny wirtualne mogą zostać naliczone znaczne koszty sieci. Zrozumienie podstawowych czynników wpływających na koszty usług platformy Azure jest niezbędne, aby móc dostosowywać użycie usługi w zależności od potrzeb.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz pozycję **Koszt według usługi**, a następnie grupuj według **warstwy usług**.
1. Zmień widok na **Tabela**.

[![Podział kosztów według usługi platformy Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>Przeglądanie zafakturowanych kosztów w analizie kosztów

Aby wyświetlić szczegóły faktury w witrynie Azure Portal, przejdź do analizy kosztów dla zakresu skojarzonego z analizowaną fakturą. Wybierz widok **Szczegóły faktury**. Szczegóły faktury przedstawiają opłaty, które są widoczne na fakturze.

[![Przykład pokazujący szczegóły faktury](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

Przeglądając szczegóły faktury, możesz zidentyfikować usługę z nieoczekiwanymi kosztami i określić, które zasoby są bezpośrednio skojarzone z zasobem w analizie kosztów. Jeśli na przykład chcesz przeanalizować opłaty za usługę Virtual Machines, przejdź do widoku **Skumulowany koszt**. Następnie ustaw stopień szczegółowości na **Codziennie**, ustaw filtr opłat **Nazwa usługi: Virtual Machines** i pogrupuj opłaty według wartości **Zasób**.

[![Przykład przedstawiający skumulowane koszty maszyn wirtualnych](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Wyświetlanie podziału kosztów według zasobu platformy Azure

Usługi są tworzone za pomocą zasobów platformy Azure. Przeglądanie kosztów w oparciu o zasoby może ułatwić szybką identyfikację głównych składników kosztów. Jeśli usługa ma zasoby, które są zbyt kosztowne, rozważ wprowadzenie zmian w celu ograniczenia kosztów.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz pozycję **Koszt według zasobu**.
1. Zmień widok na **Tabela**.

[![Wyświetlanie podziału kosztów według zasobu platformy Azure](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Wyświetlanie podziału kosztów według wybranych wymiarów

Wymiary umożliwiają organizowanie kosztów w oparciu o różne wartości metadanych pokazane w opłatach. Można na przykład grupować swoje koszty według lokalizacji.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz filtr **Grupuj według**.  
    [![Wybierz pozycję Grupuj według elementu](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. Opcjonalnie zapisz widok do późniejszego użycia.
1. Kliknij wykres kołowy pod wykresem, aby wyświetlić bardziej szczegółowe dane.  
    [![Wyświetlanie podziału kosztów według wybranych wymiarów](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Wyświetlanie kosztów w rozbiciu dziennym i miesięcznym

Analiza dziennych i miesięcznych kosztów może ułatwić określenie, czy istnieją części tygodnia lub roku, kiedy koszty są wyższe. Jeśli notujesz większy ruch klientów w okresie świątecznym, czy przekłada się to na wzrost kosztów platformy Azure? Czy piątek jest bardziej kosztowny od poniedziałku?

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Ustaw **Poziom szczegółowości** na **Miesięczny** lub **Dzienny**.

[![Wyświetlanie kosztów w rozbiciu dziennym](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>Wyświetlanie opłat za maszynę wirtualną typu spot

Maszyny wirtualne typu spot mogą zapewnić duże oszczędności w przypadku obciążeń, które obsługują przerwy. Obciążenia są uruchamiane w ramach nieużywanej pojemności platformy Azure. Ze względu na to, że maszyny wirtualne typu spot mogą zostać wykluczone w dowolnym momencie, są one dostępne ze znacznym rabatem. Użyj następujących kroków, aby wyświetlić swoje opłaty za maszyny wirtualne typu spot.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Na przykład **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
2. Dodaj filtr **Model cen: Spot**.

![Przykład przedstawiający filtr maszyn wirtualnych typu spot](./media/cost-analysis-common-uses/spot-vm-filter.png)

Wymiar Model cen jest także używany do wyświetlania opłat za zasoby na żądanie i rezerwacje.

## <a name="view-your-reservation-charges"></a>Wyświetlanie opłat związanych z rezerwacją

Wystąpienia zarezerwowane umożliwiają oszczędności finansowe na platformie Azure. Rezerwacja oznacza wydanie pieniędzy z góry za określoną ilość zasobów używanych w określonym czasie. Analiza kosztów pokazuje opłaty tak jak są one wyświetlane na rachunku. Opłaty są pokazywane jako koszty rzeczywiste lub amortyzowane w okresie rezerwacji.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Na przykład **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Dodaj filtr **Model cen: Rezerwacja**.
1. W obszarze **Zakres** i obok wyświetlonego kosztu kliknij symbol strzałki w dół i wybierz metrykę **Koszt rzeczywisty** lub **Koszt amortyzowany**.

![Wybieranie metryki kosztu](./media/cost-analysis-common-uses/metric-cost.png)

Każda metryka wpływa na sposób wyświetlania danych dla opłat za rezerwację.

**Koszt rzeczywisty** — pokazuje zakup tak jak jest on umieszczony na rachunku. Jeśli na przykład zakupiono jednoroczną rezerwację za 1200 USD w styczniu, analiza kosztów wykazuje dla rezerwacji koszt 1200 USD w stycznia. Nie pokazuje kosztu rezerwacji dla innych miesięcy roku. Jeśli pogrupujesz rzeczywiste koszty według maszyny wirtualnej, to maszyna wirtualna, która otrzymała korzyść rezerwacji w danym miesiącu, będzie mieć zerowy koszt dla danego miesiąca.

**Koszt amortyzowany** — pokazuje zakup rezerwacji podzielony jako koszt amortyzowany na okres rezerwacji. Korzystając z tego samego przykładu, analiza kosztów przedstawia koszt 100 USD koszt dla każdego miesiąca w ciągu roku, jeśli zakupiono jednoroczną rezerwację za 1200 USD w styczniu. Jeśli pogrupujesz koszty według maszyny wirtualnej dla tego przykładu, zobaczysz koszt przypisany do każdej maszyny wirtualnej, która otrzymała korzyść rezerwacji.

## <a name="view-your-reservation-utilization"></a>Wyświetlanie wykorzystania rezerwacji

Po zakupieniu rezerwacji ważne jest, aby śledzić jej wykorzystanie, aby uzyskać to, za co się zapłaciło. Na przykład jeśli zakupiono 10 maszyn wirtualnych na rok i tylko pięć z nich będzie używanych, to połowa zakupu zostanie zmarnowana. Istnieją dwa różne sposoby oceny wykorzystania:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Wyświetlanie kosztów nieużywanych wystąpień zarezerwowanych w analizie kosztów

Aby określić, jaka kwota jest obecnie marnowana każdego miesiąca dla zakupu rezerwacji, wykonaj poniższe kroki.

1. W witrynie Azure Portal przejdź do analizy kosztów dla zakresu, w którym zastosowano rezerwację. Na przykład **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Dodaj filtr **Model cen: Rezerwacja**.
1. Wybierz widok **Koszt amortyzowany**.
1. Ustaw poziom szczegółowości na wartość **Miesięczny**.
1. Ustaw okres na bieżący rok lub termin rezerwacji.
1. Ustaw typ wykresu na **Skumulowany kolumnowy**.
1. Zgrupuj opłaty według pola **Typ opłaty**.
1. Przejrzyj wyniki dla wartości `unusedreservation`.

[![Obraz przedstawiający użycie rezerwacji](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Wyświetlanie wykorzystania w obszarze Rezerwacje

Aby uzyskać szczegółowe instrukcje, zobacz [Optymalizacja użycia rezerwacji](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use).

## <a name="view-costs-for-a-specific-tag"></a>Wyświetlanie kosztów dla określonego tagu

Wielu użytkowników platformy Azure stosuje tagi do swoich zasobów, takich jak centrum kosztów lub środowisko programistyczne (produkcyjne i testowe), aby lepiej klasyfikować opłaty. Tagi są wyświetlane jako wymiar w analizie kosztów. Można użyć wymiaru, aby uzyskać wgląd w niestandardowe kategoryzacje tagów.

Obsługa tagów dotyczy użycia zgłaszanego *po tym*, gdy tag został zastosowany do zasobu. Tagi nie są stosowane wstecznie dla celów zestawień kosztów.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz pozycję **Grupuj według** dla tagu.

[![Wyświetlanie kosztów dla określonego tagu](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Pobieranie szczegółowego zestawienia użycia

Plik raportu szczegółów użycia w formacie CSV zawiera podział wszystkich opłat naliczanych w ramach faktury. Możesz użyć raportu, aby porównać go z fakturą i lepiej ją zrozumieć. Każda opłata rozliczona na fakturze ma swój odpowiednik w rozbitych opłatach w raporcie użycia.

1. W witrynie Azure Portal przejdź do karty **Użycie i opłaty** dla konta rozliczeniowego lub subskrypcji. Przykład: **Zarządzanie kosztami i rozliczenia** > **Rozliczenia** > **Użycie i opłaty**.
1. Wybierz element wiersza do pobrania, a następnie kliknij symbol pobierania.  
    [![Pobieranie użycia i opłat](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Wybierz plik użycia, który chcesz pobrać.  
    ![Wybieranie pliku użycia do pobrania](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Wyświetlanie miesięcznego podziału kosztów umowy EA

Rejestracja umowy EA powoduje naliczanie kosztów dla całej organizacji. Zrozumienie sposobu, w jaki koszty są naliczane i fakturowane w czasie, ułatwia zaangażowanie właściwych uczestników projektu w celu zapewnienia, że koszty będą zarządzane w sposób odpowiedzialny.

Koszty są wyświetlane tylko dla aktywnej rejestracji. W przypadku przeniesienia rejestracji (nieaktywnej) do nowej (aktywnej) koszty dla poprzedniej rejestracji nie są wyświetlane w usłudze Cost Management.


1. W witrynie Azure Portal przejdź do obszaru **Zarządzanie kosztami i rozliczenia** > **Omówienie**.
1. Kliknij pozycję **Podział** dla bieżącego miesiąca i wyświetl podział opłaty z góry za platformę Azure (wcześniej nazywanej zobowiązaniem pieniężnym).  
    [![Przegląd kosztów umowy EA — podsumowanie z podziałem](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Kliknij kartę **Użycie i opłaty** i wyświetl podział dla poprzedniego miesiąca w wybranym przedziale czasu.  
    [![Karta Użycie i opłaty](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Wyświetlanie miesięcznego kosztu rejestracji według okresu

Skorzystaj z graficznego widoku miesięcznych kosztów rejestracji, aby zrozumieć trendy kosztów i kwoty faktur w danym okresie.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz rejestrację i ustaw okres rejestracji.
1. Ustaw stopień szczegółowości na miesięczny, a następnie ustaw widok na **Kolumna     (skumulowany)** .

Można grupować i filtrować dane pod kątem ich bardziej szczegółowej analizy.

[![Miesięczny koszt rejestracji według okresu](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Wyświetlanie skumulowanych kosztów rejestracji w ramach umowy EA

Wyświetl łączne opłaty netto na przestrzeni czasu, aby poznać ogólne wydatki dla organizacji w danym okresie.

1. W witrynie Azure Portal przejdź do analizy kosztów dla swojego zakresu. Przykład: **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**.
1. Wybierz rejestrację, a następnie wyświetl bieżące skumulowane koszty.

[![Skumulowane koszty rejestracji](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Następne kroki
- Jeśli pierwszy przewodnik Szybki start dla usługi Cost Management nie został jeszcze przez Ciebie ukończony, przeczytaj go w temacie [Rozpoczęcie analizowania kosztów](quick-acm-cost-analysis.md).
- Przeczytaj [dokumentację dotyczącą usługi Cost Management](../index.yml).
