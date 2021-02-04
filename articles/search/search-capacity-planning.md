---
title: Oszacowanie pojemności dla obciążeń zapytań i indeksów
titleSuffix: Azure Cognitive Search
description: Dostosuj zasoby partycji i repliki komputera w usłudze Azure Wyszukiwanie poznawcze, gdzie każdy zasób jest naliczany w jednostkach wyszukiwania do rozliczenia.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: d848c1ed1ab9d4cb24dec9423d93ec62ab45633b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537225"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Oszacuj i Zarządzaj pojemnością usługi Wyszukiwanie poznawcze platformy Azure

Przed [zainicjowaniem obsługi administracyjnej usługi wyszukiwania](search-create-service-portal.md) i zablokowaniem jej w określonej warstwie cenowej Poświęć kilka minut, aby zrozumieć, jak działa wydajność oraz jak dostosować repliki i partycje, aby uwzględnić wahania obciążenia.

Pojemność jest funkcją [warstwy usług](search-sku-tier.md), ustanawiającą maksymalny rozmiar magazynu na usługę, na partycję oraz maksymalne limity liczby obiektów, które można utworzyć. Warstwa Podstawowa jest przeznaczona dla aplikacji mających niewielkie wymagania dotyczące magazynu (tylko jedną partycję), ale z możliwością uruchamiania w konfiguracji o wysokiej dostępności (3 repliki). Inne warstwy są przeznaczone do określonych obciążeń lub wzorców, takich jak Wielodostępność. Wewnętrznie usługi utworzone na tych warstwach korzystają ze sprzętu, który ułatwia te scenariusze.

Architektura skalowalności w usłudze Azure Wyszukiwanie poznawcze opiera się na elastycznych kombinacjach replik i partycji, dzięki czemu można zmieniać pojemność w zależności od tego, czy potrzebne są dodatkowe zapytania czy możliwości indeksowania. Gdy usługa zostanie utworzona, można zwiększyć lub zmniejszyć liczbę replik lub partycji niezależnie od siebie. Koszty będą przenoszone z każdym dodatkowym zasobem fizycznym, ale po zakończeniu dużych obciążeń możesz zmniejszyć skalę w celu obniżenia poziomu rachunku. W zależności od warstwy i rozmiaru korekty dodanie lub zmniejszenie pojemności może zająć od 15 minut do kilku godzin.

Podczas modyfikowania alokacji replik i partycji zalecamy używanie Azure Portal. Portal wymusza limity dla dozwolonych kombinacji, które pozostają poniżej maksymalnych limitów warstwy. Jeśli jednak wymagasz metody inicjowania obsługi opartej na skrypcie lub kodzie, [Azure PowerShell](search-manage-powershell.md) lub [interfejs API REST zarządzania](/rest/api/searchmanagement/services) są rozwiązaniami alternatywnymi.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Pojęcia: jednostki wyszukiwania, repliki, partycje, fragmentów

Pojemność jest wyrażona w *jednostkach wyszukiwania* , które mogą być przydzieleni do kombinacji *partycji* i *replik* przy użyciu podstawowego mechanizmu *fragmentowania* do obsługi elastycznych konfiguracji:

| Pojęcie  | Definicja|
|----------|-----------|
|*Jednostka wyszukiwania* | Pojedynczy przyrost całkowitej dostępnej pojemności (36 jednostek). Jest to również jednostka rozliczeniowa usługi Azure Wyszukiwanie poznawcze. Do uruchomienia usługi jest wymagana co najmniej jedna jednostka.|
|*Replika* | Wystąpienia usługi wyszukiwania, używane głównie do równoważenia obciążenia operacji zapytań. Każda replika jest hostem jednej kopii indeksu. W przypadku przydzielenia trzech replik będziesz mieć trzy kopie indeksu dostępne do obsługi żądań zapytań.|
|*Partycja* | Magazyn fizyczny i operacje we/wy na potrzeby operacji odczytu/zapisu (na przykład podczas odbudowywania lub odświeżania indeksu). Każda partycja ma wycinka całkowitego indeksu. W przypadku przydzielenia trzech partycji indeks jest podzielony na trzecie. |
|*Fragmentu* | Fragment indeksu. Usługa Azure Wyszukiwanie poznawcze dzieli każdy indeks na fragmentów, aby umożliwić szybsze Dodawanie partycji (przez przeniesienie fragmentów do nowych jednostek wyszukiwania).|

Na poniższym diagramie przedstawiono relację między replikami, partycjami, fragmentów i jednostkami wyszukiwania. Przedstawiono przykład sposobu, w jaki pojedynczy indeks jest częścią czterech jednostek wyszukiwania w usłudze z dwiema replikami i dwiema partycjami. Każda z czterech jednostek wyszukiwania przechowuje tylko połowę fragmentów indeksu. Jednostki wyszukiwania w lewej kolumnie przechowują pierwszą połowę fragmentów, składającą się z pierwszej partycji, natomiast te w prawej kolumnie przechowują drugą połowę fragmentów, składającą się z drugiej partycji. Ponieważ istnieją dwie repliki, każdy indeks fragmentu ma dwie kopie. Jednostki wyszukiwania w górnym wierszu przechowują jedną kopię, która składa się z pierwszej repliki, podczas gdy te w dolnym wierszu przechowują kolejną kopię obejmującą drugą replikę.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Indeksy wyszukiwania są podzielonej na fragmenty między partycjami.":::

Na powyższym diagramie znajduje się tylko jeden przykład. Istnieje wiele kombinacji partycji i replik, maksymalnie do 36 całkowitej liczby jednostek wyszukiwania.

W Wyszukiwanie poznawcze zarządzanie fragmentuem jest szczegółami implementacji i nie można ich konfigurować, ale wiedzą, że indeks podzielonej na fragmenty pomaga zrozumieć sporadyczne anomalie podczas klasyfikacji i zachowań autouzupełniania:

+ Anomalie klasyfikacji: wyniki wyszukiwania są obliczane najpierw na poziomie fragmentu, a następnie agregowane do jednego zestawu wyników. W zależności od właściwości zawartości fragmentu, dopasowania z jednego fragmentuu mogą być większe niż dopasowania w innym. Jeśli zauważysz, że w wynikach wyszukiwania zostanie wyszukany licznik intuicyjne klasyfikacje, najprawdopodobniej wynika to z efektów fragmentowania, zwłaszcza jeśli indeksy są małe. Te anomalie klasyfikacji można uniknąć, wybierając w [całości obliczenia oceny w całym indeksie](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), ale spowoduje to spadek wydajności.

+ Anomalie autouzupełniania: zapytania autouzupełniania, gdzie dopasowań są wykonywane na pierwszych kilku znakach częściowo wprowadzonego terminu, akceptują parametr rozmyty, który forgives małe odchylenia w pisowni. W przypadku autouzupełniania dopasowywanie rozmyte jest ograniczone do warunków w bieżącym fragmentu. Na przykład jeśli fragmentu zawiera "Microsoft" i zostanie wprowadzony częściowy termin "micor", aparat wyszukiwania będzie pasował do "Microsoft" w tym fragmentu, ale nie w innych fragmentów, który przechowuje pozostałe części indeksu.

## <a name="how-to-evaluate-capacity-requirements"></a>Jak oszacować wymagania dotyczące pojemności

Pojemność i koszty uruchomienia usługi są dostępne. Warstwy nakładają limity na dwa poziomy: Magazyn i zawartość (na przykład liczba indeksów w usłudze). Ważne jest, aby wziąć pod uwagę zarówno, ponieważ w zależności od tego, który limit osiągnął, że obowiązuje limit.

Liczby indeksów i innych obiektów są zwykle określone przez wymagania biznesowe i inżynieryjne. Na przykład może istnieć wiele wersji tego samego indeksu na potrzeby aktywnego programowania, testowania i produkcji.

Wymagania dotyczące magazynu są określane przez rozmiar indeksów, które należy skompilować. Nie ma żadnych stałych heurystycznych lub ogólnych, które pomagają oszacować. Jedynym sposobem określenia rozmiaru indeksu jest [kompilacja](search-what-is-an-index.md). Jego rozmiar będzie oparty na zaimportowanych danych, analizie tekstu i konfiguracji indeksu, takich jak włączenie sugestii, filtrowanie i sortowanie.

W przypadku wyszukiwania pełnotekstowego podstawowa struktura danych jest [odwrotną](https://en.wikipedia.org/wiki/Inverted_index) strukturą indeksu, która ma inne cechy niż dane źródłowe. W przypadku odwróconego indeksu rozmiar i złożoność są określane przez zawartość, a nie niekoniecznie ilość danych, które są do niego strumieniowo. Duże źródło danych o wysokiej nadmiarowości może spowodować zmniejszenie indeksu niż mniejszy zestaw danych, który zawiera wysoce zmienną zawartość. Jest to rzadko możliwe do wywnioskowania rozmiaru indeksu na podstawie rozmiaru oryginalnego zestawu danych.

> [!NOTE] 
> Chociaż oszacowanie przyszłych potrzeb dotyczących indeksów i magazynu może wyglądać podobnie jak wątpliwości, warto wykonać te czynności. Jeśli pojemność warstwy wyzostanie zbyt niska, należy udostępnić nową usługę w wyższej warstwie, a następnie [ponownie załadować indeksy](search-howto-reindex.md). Nie istnieje uaktualnienie w miejscu usługi z jednej warstwy do innej.
>

### <a name="estimate-with-the-free-tier"></a>Szacowanie przy użyciu warstwy Bezpłatna

Jednym z metod oszacowania wydajności jest rozpoczęcie od warstwy Bezpłatna. Należy pamiętać, że bezpłatna usługa oferuje maksymalnie trzy indeksy, 50 MB miejsca do magazynowania i 2 minuty czasu indeksowania. Oszacowanie przewidywanego rozmiaru indeksu z tymi ograniczeniami może być trudne, ale te czynności są następujące:

+ [Utwórz bezpłatną usługę](search-create-service-portal.md).
+ Przygotuj mały reprezentatywny zestaw danych.
+ Utwórz indeks i Załaduj dane. Jeśli zestaw danych może być hostowany w źródle danych platformy Azure obsługiwanym przez indeksatory, można użyć [Kreatora importu danych w portalu](search-get-started-portal.md) do utworzenia i załadowania indeksu. W przeciwnym razie należy użyć REST i [Ogłoś](search-get-started-rest.md) lub [Visual Studio Code](search-get-started-vs-code.md) , aby utworzyć indeks i wypchnąć dane. Model wypychania wymaga, aby dane były w postaci dokumentów JSON, gdzie pola w dokumencie odpowiadają polom w indeksie.
+ Zbierz informacje o indeksie, takie jak rozmiar. Funkcje i atrybuty mają wpływ na magazyn. Na przykład dodanie sugestii (zapytania wyszukiwania jako typ) spowoduje zwiększenie wymagań dotyczących magazynu. 

  Korzystając z tego samego zestawu danych, można spróbować utworzyć wiele wersji indeksu z różnymi atrybutami każdego pola, aby zobaczyć, jak różnią się wymagania dotyczące magazynu. Aby uzyskać więcej informacji, zobacz ["konsekwencje dotyczące magazynu" w temacie Tworzenie podstawowego indeksu](search-what-is-an-index.md#index-size).

Dzięki przybliżonemu szacunkowi możesz dwukrotnie określić wartość budżetu dla dwóch indeksów (rozwój i produkcja), a następnie wybrać odpowiednią warstwę.

### <a name="estimate-with-a-billable-tier"></a>Szacowanie przy użyciu warstwy rozliczeniowej

Dedykowane zasoby mogą obsługiwać większe próbkowanie i czasy przetwarzania dla bardziej realistycznych szacunków liczby indeksów, rozmiaru i woluminów zapytań podczas opracowywania. Niektórzy klienci przechodźą w prawo przy użyciu warstwy rozliczeniowej, a następnie ponownie ocenianej jako dojrzały projekt programistyczny.

1. [Przejrzyj limity usługi w każdej warstwie](./search-limits-quotas-capacity.md#index-limits) , aby określić, czy niższe warstwy mogą obsługiwać wymaganą liczbę indeksów. W warstwach Podstawowa, S1 i S2 limity indeksu są odpowiednio 15, 50 i 200. Warstwa zoptymalizowana pod kątem magazynu ma limit 10 indeksów, ponieważ jest ona zaprojektowana do obsługi niskiej liczby bardzo dużych indeksów.

1. [Utwórz usługę w warstwie rozliczanej](search-create-service-portal.md):

    + Jeśli nie masz pewności co do planowanego obciążenia, Zacznij od warstwy Podstawowa lub S1.
    + Zacznij od warstwy S2 lub nawet S3, jeśli testowanie obejmuje indeksowanie dużej skali i ładowanie zapytań.
    + Zacznij od magazynu zoptymalizowanego pod kątem technologii L1 lub L2, jeśli indeksowanie dużej ilości danych jest stosunkowo niskie, podobnie jak w przypadku wewnętrznej aplikacji biznesowej.

1. [Utwórz początkowy indeks](search-what-is-an-index.md) , aby określić sposób, w jaki dane źródłowe są tłumaczone na indeks. Jest to jedyny sposób oszacowania rozmiaru indeksu.

1. [Monitoruj magazyn, limity usług, woluminy zapytań i opóźnienia](search-monitor-usage.md) w portalu. W portalu są wyświetlane zapytania na sekundę, ograniczone zapytania i opóźnienie wyszukiwania. Wszystkie te wartości mogą pomóc zdecydować, czy wybrano odpowiednią warstwę.

1. Dodaj repliki, jeśli potrzebujesz wysokiej dostępności lub w przypadku powolnej wydajności zapytań.

   Nie ma żadnych wytycznych dotyczących liczby replik potrzebnych do obsługi obciążeń zapytań. Wydajność zapytań zależy od złożoności zapytania i konkurencyjnych obciążeń. Mimo że dodanie replik jasno skutkuje lepszą wydajnością, wynik nie jest ściśle liniowy: dodanie trzech replik nie gwarantuje trzykrotnej przepływności. Aby uzyskać wskazówki dotyczące szacowania zapytań dla rozwiązania, zobacz [skalowanie na potrzeby zapytań dotyczących wydajności](search-performance-optimization.md)i [monitorowania](search-monitor-queries.md).

> [!NOTE]
> Wymagania dotyczące magazynu mogą być naliczane w przypadku dołączenia danych, które nigdy nie będą przeszukiwane. W idealnym przypadku dokumenty zawierają tylko te dane, które są potrzebne do obsługi wyszukiwania. Dane binarne nie mogą być wyszukiwane i powinny być przechowywane osobno (mogą znajdować się w tabeli lub w magazynie obiektów blob platformy Azure). W indeksie należy dodać pole do przechowywania odwołania do danych zewnętrznych. Maksymalny rozmiar pojedynczego dokumentu wyszukiwania wynosi 16 MB (lub mniej w przypadku zbiorczego przekazywania wielu dokumentów w jednym żądaniu). Aby uzyskać więcej informacji, zobacz [limity usług w usłudze Azure wyszukiwanie poznawcze](search-limits-quotas-capacity.md).
>

**Zagadnienia dotyczące woluminów zapytań**

Zapytania na sekundę (zapytań) to istotna Metryka w czasie dostrajania wydajności, ale ogólnie można wziąć pod uwagę tylko warstwę, jeśli na początku oczekuje się dużej ilości zapytań.

Warstwy standardowe mogą zapewnić równowagę między replikami i partycjami. Można zwiększyć szybkością oferowaną zapytania, dodając repliki do równoważenia obciążenia lub dodając partycje do przetwarzania równoległego. Następnie można dostroić wydajność po aprowizacji usługi.

Jeśli od samego początku oczekujesz dużej liczby trwałych woluminów zapytań, należy rozważyć wyższe warstwy standardowe. Następnie można przełączyć partycje i repliki w tryb offline, a nawet przełączać się do usługi niższej warstwy, jeśli te woluminy zapytań nie wystąpią. Aby uzyskać więcej informacji na temat obliczania przepływności zapytań, zobacz temat [wydajność i optymalizacja w usłudze Azure wyszukiwanie poznawcze](search-performance-optimization.md).

Warstwy zoptymalizowane pod kątem magazynu są przydatne w przypadku obciążeń dużych ilości danych, dzięki czemu można obsługiwać więcej ogólnego magazynu indeksów, gdy wymagania dotyczące opóźnień zapytań są mniej ważne. Nadal należy używać dodatkowych replik do równoważenia obciążenia i dodatkowych partycji do przetwarzania równoległego. Następnie można dostroić wydajność po aprowizacji usługi.

**Umowy dotyczące poziomu usług**

Funkcje warstwy Bezpłatna i wersja zapoznawcza nie zapewniają [umów dotyczących poziomu usług (umowy SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). W przypadku wszystkich warstw rozliczanych umowy SLA zacznie obowiązywać po wprowadzeniu wystarczającej nadmiarowości dla usługi. Musisz mieć co najmniej dwie repliki dla zapytania (Read) umowy SLA. Musisz mieć trzy lub więcej replik na potrzeby zapytań i indeksowania (do odczytu i zapisu) umowy SLA. Liczba partycji nie ma wpływu na umowy SLA.

## <a name="tips-for-capacity-planning"></a>Wskazówki dotyczące planowania pojemności

+ Zezwalaj na metryki do kompilowania zapytań i Zbieraj dane dotyczące wzorców użycia (zapytania w godzinach pracy, indeksowanie w godzinach poza szczytem). Te dane służą do informowania o decyzjach o aprowizacji usług. Chociaż nie jest to praktyczne ani codzienne erze, można dynamicznie dostosować partycje i zasoby, aby uwzględnić planowane zmiany w woluminach zapytań. Możesz również uwzględnić niezaplanowane, ale nieplanowane zmiany, jeśli poziomy są wystarczająco długie, aby zagwarantować wykonanie akcji.

+ Należy pamiętać, że jedyną minusem w ramach aprowizacji jest to, że może zajść potrzeba odrywania usługi, jeśli rzeczywiste wymagania są większe niż Twoje prognozy. Aby uniknąć przerw w działaniu usługi, należy utworzyć nową usługę w wyższej warstwie i uruchamiać ją równolegle do momentu, gdy wszystkie aplikacje i żądania będą kierowane do nowego punktu końcowego.

## <a name="when-to-add-partitions-and-replicas"></a>Kiedy należy dodać partycje i repliki

Początkowo do usługi przydzielono minimalny poziom zasobów składający się z jednej partycji i jednej repliki.

Pojedyncza usługa musi mieć wystarczającą ilość zasobów do obsługi wszystkich obciążeń (indeksowanie i zapytania). Żadne obciążenie nie jest uruchamiane w tle. Można zaplanować indeksowanie razy, gdy żądania zapytań są naturalnie rzadko, ale w przeciwnym razie usługa nie będzie określać priorytetów jednego zadania. Ponadto pewna ilość nadmiarowości wygładza wydajność zapytań, gdy usługi lub węzły są aktualizowane wewnętrznie.

Zgodnie z ogólną regułą wyszukiwanie aplikacji może wymagać większej liczby replik niż partycje, szczególnie w przypadku, gdy operacje usługi są wliczane do obciążeń zapytań. W sekcji o [wysokiej dostępności](#HA) wyjaśniono, dlaczego.

[Wybrana warstwa](search-sku-tier.md) określa rozmiar partycji i szybkość, a każda warstwa jest zoptymalizowana wokół zestawu właściwości, które pasują do różnych scenariuszy. W przypadku wybrania warstwy wyższej można potrzebować mniejszej liczby partycji niż w przypadku przechodzenia z S1. Jednym z pytań, które należy odpowiedzieć przez testowanie samodzielne, jest to, czy większa i bardziej kosztowna partycja zapewnia lepszą wydajność niż dwie tańsze partycje w ramach usługi, która została zainicjowana w niższej warstwie.

Wyszukiwanie aplikacji, które wymagają odświeżenia danych w czasie rzeczywistym, będzie wymagało jeszcze większej liczby partycji niż repliki. Dodawanie partycji powoduje rozłożenie operacji odczytu/zapisu na większą liczbę zasobów obliczeniowych. Zapewnia również więcej miejsca na dysku do przechowywania dodatkowych indeksów i dokumentów.

Więcej indeksów trwa dłużej. W związku z tym może się okazać, że każdy wzrost przyrostowy partycji wymaga mniejszego, ale proporcjonalnego wzrostu w replikach. Złożoność zapytań i woluminu zapytania polega na tym, jak szybko wykonywanie zapytań jest wyłączone.

> [!NOTE]
> Dodanie większej liczby replik lub partycji zwiększa koszt działania usługi i może wprowadzać niewielkie różnice w sposobie uporządkowania wyników. Należy sprawdzić [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) , aby zrozumieć implikacje rozliczeń związanych z dodawaniem kolejnych węzłów. [Wykres poniżej](#chart) może pomóc w odniesieniu do liczby jednostek wyszukiwania wymaganych dla określonej konfiguracji. Aby uzyskać więcej informacji o tym, jak dodatkowe repliki wpływają na przetwarzanie zapytań, zobacz [porządkowanie wyników](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Jak przydzielić repliki i partycje

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i wybierz usługę wyszukiwania.

1. W obszarze **Ustawienia** Otwórz stronę **skalowanie** , aby zmodyfikować repliki i partycje. 

   Poniższy zrzut ekranu przedstawia podstawowy standard obsługiwany z jedną repliką repliki i partycją. Formuła u dołu wskazuje liczbę używanych jednostek wyszukiwania (1). Jeśli cena jednostkowa była $100 (nie cena rzeczywista), miesięczny koszt uruchamiania tej usługi będzie wynosić $100.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Strona skalowania przedstawiająca bieżące wartości" border="true":::

1. Użyj suwaka, aby zwiększyć lub zmniejszyć liczbę partycji. Formuła u dołu wskazuje liczbę używanych jednostek wyszukiwania. Wybierz pozycję **Zapisz**.

   Ten przykład dodaje drugą replikę i partycję. Zwróć uwagę na liczbę jednostek wyszukiwania; jest teraz cztery, ponieważ formuła rozliczania jest repliką pomnożoną przez partycje (2 x 2). Podwojenie pojemności więcej niż podwaja koszt działania usługi. Jeśli koszt jednostki wyszukiwania to $100, nowe miesięczne rozliczenie będzie teraz $400.

   W przypadku bieżących kosztów na jednostkę dla każdej warstwy odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Dodawanie replik i partycji" border="true":::

1. Po zapisaniu można sprawdzić powiadomienia, aby upewnić się, że akcja zakończyła się pomyślnie.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Zapisz zmiany" border="true":::

   Zmiany pojemności mogą trwać do kilku godzin. Nie można anulować po rozpoczęciu procesu i nie ma monitorowania w czasie rzeczywistym dla zmian replik i partycji. Jednak następujący komunikat pozostanie widoczny w czasie trwania zmian.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Komunikat o stanie w portalu" border="true":::

> [!NOTE]
> Po aprowizacji usługi nie można jej uaktualnić do wyższej warstwy. Należy utworzyć usługę wyszukiwania w nowej warstwie i ponownie załadować indeksy. Zobacz [Tworzenie usługi Azure wyszukiwanie poznawcze w portalu,](search-create-service-portal.md) Aby uzyskać pomoc dotyczącą aprowizacji usług.
>
> Ponadto partycje i repliki są zarządzane wyłącznie i wewnętrznie przez usługę. Nie ma koncepcji koligacji procesora ani przypisywania obciążenia do określonego węzła.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinacje partycji i repliki

Usługa podstawowa może mieć dokładnie jedną partycję i maksymalnie trzy repliki, co pozwala na maksymalne ograniczenie trzech usług SUs. Jedyny przystawka zasobów to repliki. Aby zapewnić wysoką dostępność zapytań, potrzebne są co najmniej dwie repliki.

Wszystkie usługi wyszukiwania zoptymalizowane pod kątem standardowej i magazynu mogą przyjąć następujące kombinacje replik i partycji, z zastrzeżeniem limitu 36-SU dozwolonego dla tych warstw. 

|   | **1 partycja** | **2 partycje** | **3 partycje** | **4 partycje** | **6 partycji** | **12 partycji** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliki** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliki** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliki** |4 SU |8 SU |12 SU |16 SU |24 SU |Nie dotyczy |
| **5 replik** |5 SU |10 SU |15 SU |20 SU |30 SU |Nie dotyczy |
| **6 replik** |6 SU |12 SU |18 SU |24 SU |36 SU |Nie dotyczy |
| **12 replik** |12 SU |24 SU |36 SU |NIE DOTYCZY |NIE DOTYCZY |NIE DOTYCZY |

W witrynie sieci Web systemu Azure szczegółowo objaśniono usługi SUs, cennik i pojemność. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Liczba replik i partycji jest dzielona równomiernie na 12 (w odnoszącym się do 1, 2, 3, 4, 6, 12). Jest to spowodowane tym, że platforma Azure Wyszukiwanie poznawcze wstępnie dzieli każdy indeks na 12 fragmentów, tak aby można go było rozłożyć w równe fragmenty we wszystkich partycjach. Na przykład jeśli usługa ma trzy partycje i tworzysz indeks, każda partycja będzie zawierać cztery fragmentów indeksu. Sposób, w jaki usługa Azure Wyszukiwanie poznawcze fragmentów indeks, to szczegóły implementacji, które mogą ulec zmianie w przyszłych wersjach. Mimo że liczba to 12 dzisiaj, nie należy oczekiwać, że ta liczba będzie zawsze 12 w przyszłości.
>

<a id="HA"></a>

## <a name="high-availability"></a>Wysoka dostępność

Ponieważ jest to łatwe i stosunkowo szybkie skalowanie, zazwyczaj zalecamy rozpoczęcie od jednej partycji i jednej lub dwóch replik, a następnie skalowanie w górę jako kompilacja woluminów zapytań. Obciążenia zapytań są uruchamiane głównie w replikach. Jeśli potrzebujesz większej przepływności lub wysokiej dostępności, prawdopodobnie będziesz potrzebować dodatkowych replik.

Ogólne zalecenia dotyczące wysokiej dostępności są następujące:

+ Dwie repliki w celu zapewnienia wysokiej dostępności obciążeń tylko do odczytu (zapytania)

+ Trzy lub więcej replik w celu zapewnienia wysokiej dostępności obciążeń odczytu/zapisu (zapytania i indeksowania jako pojedyncze dokumenty są dodawane, aktualizowane lub usuwane)

Umowy dotyczące poziomu usług (SLA) dla usługi Azure Wyszukiwanie poznawcze są przeznaczone dla operacji zapytań i aktualizacji indeksu, które obejmują dodawanie, aktualizowanie lub usuwanie dokumentów.

Warstwa Podstawowa jest przeznaczona dla jednej partycji i trzech replik. Jeśli chcesz, aby elastyczność natychmiast reagować na wahania popytu dla indeksowania i przepływności zapytań, weź pod uwagę jedną z warstw standardowych.  Jeśli okaże się, że wymagania dotyczące magazynu rośnie znacznie szybciej niż przepływność zapytań, należy rozważyć jedną z warstw zoptymalizowanych pod kątem magazynu.

## <a name="about-queries-per-second-qps"></a>Zapytania na sekundę (zapytań) — informacje

Ze względu na dużą liczbę czynników, które przechodzą na wydajność zapytań, firma Microsoft nie publikuje oczekiwanych liczb zapytań. Oszacowania zapytań muszą być opracowywane niezależnie przez każdego klienta przy użyciu warstwy usług, konfiguracji, indeksu i konstrukcji zapytań, które są prawidłowe dla danej aplikacji. Rozmiar indeksu i złożoność, rozmiar zapytania i złożoność oraz wielkość ruchu to podstawowe znaczniki zapytań. Nie ma możliwości zaoferowania znaczących szacunków, gdy takie czynniki są nieznane.

Oszacowania są bardziej przewidywalne, gdy są obliczane w usługach uruchomionych na dedykowanych zasobach (warstwach Podstawowa i standardowa). Możesz oszacować zapytań bardziej blisko, ponieważ masz kontrolę nad większymi parametrami. Aby uzyskać wskazówki dotyczące sposobu podejścia do oceny, zobacz temat [wydajność i optymalizacja na platformie wyszukiwanie poznawcze Azure](search-performance-optimization.md).

W przypadku warstw zoptymalizowanych pod kątem magazynu (L1 i L2) należy oczekiwać mniejszej przepływności zapytań i wyższych opóźnień niż w przypadku warstw standardowych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak oszacować koszty i zarządzać nimi](search-sku-manage-costs.md)