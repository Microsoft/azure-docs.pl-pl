---
title: Dostosowanie pojemności dla obciążeń zapytań i indeksów
titleSuffix: Azure Cognitive Search
description: Dostosuj zasoby partycji i repliki komputera w usłudze Azure Wyszukiwanie poznawcze, gdzie każdy zasób jest naliczany w jednostkach wyszukiwania do rozliczenia.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 270ff3c3e8e4cffbb1f4b1987ee497530d0c0982
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80546264"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Dostosowanie pojemności na platformie Azure Wyszukiwanie poznawcze

Przed [zainicjowaniem obsługi administracyjnej usługi wyszukiwania](search-create-service-portal.md) i zablokowaniem jej w określonej warstwie cenowej Poświęć kilka minut, aby zrozumieć rolę replik i partycji w usłudze oraz jak można dostosować usługę, aby pomieściły się w zapotrzebowaniu na zasoby.

Pojemność to funkcja [wybranej warstwy](search-sku-tier.md) (warstwy określają charakterystykę sprzętu) oraz kombinacji replik i partycji potrzebnych do prognozowania obciążeń. W zależności od warstwy i rozmiaru korekty dodanie lub zmniejszenie pojemności może zająć od 15 minut do kilku godzin. 

Podczas modyfikowania alokacji replik i partycji zalecamy używanie Azure Portal. Portal wymusza limity dla dozwolonych kombinacji, które pozostają poniżej maksymalnych limitów warstwy. Jeśli jednak wymagasz metody inicjowania obsługi opartej na skrypcie lub kodzie, [Azure PowerShell](search-manage-powershell.md) lub [interfejs API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/services) są rozwiązaniami alternatywnymi.

## <a name="terminology-replicas-and-partitions"></a>Terminologia: repliki i partycje

|||
|-|-|
|*Partycje* | Zapewnia magazyn indeksu oraz operacje we/wy dla operacji odczytu i zapisu (na przykład podczas odbudowywania lub odświeżania indeksu). Każda partycja ma udział w łącznym indeksie. W przypadku przydzielenia trzech partycji indeks jest podzielony na trzecie. |
|*Repliki* | Wystąpienia usługi wyszukiwania, używane głównie do równoważenia obciążenia operacji zapytań. Każda replika jest jedną kopią indeksu. W przypadku przydzielenia trzech replik będziesz mieć trzy kopie indeksu dostępne do obsługi żądań zapytań.|

## <a name="when-to-add-nodes"></a>Kiedy należy dodać węzły

Początkowo do usługi przydzielono minimalny poziom zasobów składający się z jednej partycji i jednej repliki. 

Pojedyncza usługa musi mieć wystarczającą ilość zasobów do obsługi wszystkich obciążeń (indeksowanie i zapytania). Żadne obciążenie nie jest uruchamiane w tle. Można zaplanować indeksowanie razy, gdy żądania zapytań są naturalnie rzadko, ale w przeciwnym razie usługa nie będzie określać priorytetów jednego zadania. Ponadto pewna ilość nadmiarowości wygładza wydajność zapytań, gdy usługi lub węzły są aktualizowane wewnętrznie.

Zgodnie z ogólną regułą wyszukiwanie aplikacji może wymagać większej liczby replik niż partycje, szczególnie w przypadku, gdy operacje usługi są wliczane do obciążeń zapytań. W sekcji o [wysokiej dostępności](#HA) wyjaśniono, dlaczego.

> [!NOTE]
> Dodanie większej liczby replik lub partycji zwiększa koszt działania usługi i może wprowadzać niewielkie różnice w sposobie uporządkowania wyników. Należy sprawdzić [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/) , aby zrozumieć implikacje rozliczeń związanych z dodawaniem kolejnych węzłów. [Wykres poniżej](#chart) może pomóc w odniesieniu do liczby jednostek wyszukiwania wymaganych dla określonej konfiguracji. Aby uzyskać więcej informacji o tym, jak dodatkowe repliki wpływają na przetwarzanie zapytań, zobacz [porządkowanie wyników](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Jak przydzielić repliki i partycje

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i wybierz usługę wyszukiwania.

1. W obszarze **Ustawienia**Otwórz stronę **skalowanie** , aby zmodyfikować repliki i partycje. 

   Poniższy zrzut ekranu przedstawia usługę standardową, która została zainicjowana z jedną repliką i partycją. Formuła u dołu wskazuje liczbę używanych jednostek wyszukiwania (1). Jeśli cena jednostkowa była $100 (nie cena rzeczywista), miesięczny koszt uruchamiania tej usługi będzie wynosić $100.

   ![Strona skalowania przedstawiająca bieżące wartości](media/search-capacity-planning/1-initial-values.png "Strona skalowania przedstawiająca bieżące wartości")

1. Użyj suwaka, aby zwiększyć lub zmniejszyć liczbę partycji. Formuła u dołu wskazuje liczbę używanych jednostek wyszukiwania.

   Ten przykład podwaja pojemność, z dwiema replikami i partycjami każdy. Zwróć uwagę na liczbę jednostek wyszukiwania; jest teraz cztery, ponieważ formuła rozliczania jest repliką pomnożoną przez partycje (2 x 2). Podwojenie pojemności więcej niż podwaja koszt działania usługi. Jeśli koszt jednostki wyszukiwania to $100, nowe miesięczne rozliczenie będzie teraz $400.

   W przypadku bieżących kosztów na jednostkę dla każdej warstwy odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/search/).

   ![Dodawanie replik i partycji](media/search-capacity-planning/2-add-2-each.png "Dodawanie replik i partycji")

1. Kliknij przycisk **Zapisz** , aby potwierdzić zmiany.

   ![Potwierdź zmiany w zakresie skalowania i rozliczeń](media/search-capacity-planning/3-save-confirm.png "Potwierdź zmiany w zakresie skalowania i rozliczeń")

   Zmiany pojemności potrwają kilka godzin. Nie można anulować po rozpoczęciu procesu i nie ma monitorowania w czasie rzeczywistym dla zmian replik i partycji. Jednak następujący komunikat pozostanie widoczny w czasie trwania zmian.

   ![Komunikat o stanie w portalu](media/search-capacity-planning/4-updating.png "Komunikat o stanie w portalu")

> [!NOTE]
> Po aprowizacji usługi nie można jej uaktualnić do wyższej warstwy. Należy utworzyć usługę wyszukiwania w nowej warstwie i ponownie załadować indeksy. Zobacz [Tworzenie usługi Azure wyszukiwanie poznawcze w portalu,](search-create-service-portal.md) Aby uzyskać pomoc dotyczącą aprowizacji usług.
>
> Ponadto partycje i repliki są zarządzane wyłącznie i wewnętrznie przez usługę. Nie ma koncepcji koligacji procesora ani przypisywania obciążenia do określonego węzła.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinacje partycji i repliki

Usługa podstawowa może mieć dokładnie jedną partycję i maksymalnie trzy repliki, co pozwala na maksymalne ograniczenie trzech usług SUs. Jedyny przystawka zasobów to repliki. Aby zapewnić wysoką dostępność zapytań, potrzebne są co najmniej dwie repliki.

Wszystkie usługi wyszukiwania zoptymalizowane pod kątem standardowej i magazynu mogą przyjąć następujące kombinacje replik i partycji, z zastrzeżeniem limitu 36-SU. 

|   | **1 partycja** | **2 partycje** | **3 partycje** | **4 partycje** | **6 partycji** | **12 partycji** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliki** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliki** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliki** |4 SU |8 SU |12 SU |16 SU |24 SU |Nie dotyczy |
| **5 replik** |5 SU |10 SU |15 SU |20 SU |30 SU |Nie dotyczy |
| **6 replik** |6 SU |12 SU |18 SU |24 SU |36 SU |Nie dotyczy |
| **12 replik** |12 SU |24 SU |36 SU |Nie dotyczy |Nie dotyczy |Nie dotyczy |

W witrynie sieci Web systemu Azure szczegółowo objaśniono usługi SUs, cennik i pojemność. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Liczba replik i partycji jest dzielona równomiernie na 12 (w odnoszącym się do 1, 2, 3, 4, 6, 12). Jest to spowodowane tym, że platforma Azure Wyszukiwanie poznawcze wstępnie dzieli każdy indeks na 12 fragmentów, tak aby można go było rozłożyć w równe fragmenty we wszystkich partycjach. Na przykład jeśli usługa ma trzy partycje i tworzysz indeks, każda partycja będzie zawierać cztery fragmentów indeksu. Sposób, w jaki usługa Azure Wyszukiwanie poznawcze fragmentów indeks, to szczegóły implementacji, które mogą ulec zmianie w przyszłych wersjach. Mimo że liczba to 12 dzisiaj, nie należy oczekiwać, że ta liczba będzie zawsze 12 w przyszłości.
>

<a id="HA"></a>

## <a name="high-availability"></a>Wysoka dostępność

Ponieważ jest to łatwe i stosunkowo szybkie skalowanie, zazwyczaj zalecamy rozpoczęcie od jednej partycji i jednej lub dwóch replik, a następnie skalowanie w górę jako kompilacja woluminów zapytań. Obciążenia zapytań są uruchamiane głównie w replikach. Jeśli potrzebujesz większej przepływności lub wysokiej dostępności, prawdopodobnie będziesz potrzebować dodatkowych replik.

Ogólne zalecenia dotyczące wysokiej dostępności są następujące:

* Dwie repliki w celu zapewnienia wysokiej dostępności obciążeń tylko do odczytu (zapytania)

* Trzy lub więcej replik w celu zapewnienia wysokiej dostępności obciążeń odczytu/zapisu (zapytania i indeksowania jako pojedyncze dokumenty są dodawane, aktualizowane lub usuwane)

Umowy dotyczące poziomu usług (SLA) dla usługi Azure Wyszukiwanie poznawcze są przeznaczone dla operacji zapytań i aktualizacji indeksu, które obejmują dodawanie, aktualizowanie lub usuwanie dokumentów.

Warstwa Podstawowa jest przeznaczona dla jednej partycji i trzech replik. Jeśli chcesz, aby elastyczność natychmiast reagować na wahania popytu dla indeksowania i przepływności zapytań, weź pod uwagę jedną z warstw standardowych.  Jeśli okaże się, że wymagania dotyczące magazynu rośnie znacznie szybciej niż przepływność zapytań, należy rozważyć jedną z warstw zoptymalizowanych pod kątem magazynu.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Obecnie nie ma wbudowanego mechanizmu odzyskiwania po awarii. Dodawanie partycji lub replik będzie niewłaściwym strategią dla celów odzyskiwania po awarii. Najbardziej typowym podejściem jest dodanie nadmiarowości na poziomie usługi przez skonfigurowanie drugiej usługi wyszukiwania w innym regionie. Podobnie jak w przypadku dostępności podczas odbudowywania indeksu, przekierowania lub logiki trybu failover muszą pochodzić z kodu.

## <a name="estimate-replicas"></a>Szacuj repliki

W przypadku usługi produkcyjnej należy przydzielić trzy repliki na potrzeby umów SLA. W przypadku powolnej wydajności zapytań można dodać repliki, aby dodatkowe kopie indeksu były przełączane w tryb online w celu obsługi większych obciążeń zapytań i równoważenia obciążenia żądań przez wiele replik.

Nie zawieramy wytycznych dotyczących liczby replik potrzebnych do obsługi obciążeń zapytań. Wydajność zapytań zależy od złożoności zapytania i konkurencyjnych obciążeń. Mimo że dodanie replik jasno skutkuje lepszą wydajnością, wynik nie jest ściśle liniowy: dodanie trzech replik nie gwarantuje trzykrotnej przepływności.

Aby uzyskać wskazówki dotyczące szacowania zapytań dla rozwiązania, zobacz [skalowanie na potrzeby zapytań dotyczących wydajności](search-performance-optimization.md)i [monitorowania](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Szacuj partycje

[Wybrana warstwa](search-sku-tier.md) określa rozmiar partycji i szybkość, a każda warstwa jest zoptymalizowana wokół zestawu właściwości, które pasują do różnych scenariuszy. W przypadku wybrania warstwy wyższej można potrzebować mniejszej liczby partycji niż w przypadku przechodzenia z S1. Jednym z pytań, które należy odpowiedzieć przez testowanie samodzielne, jest to, czy większa i bardziej kosztowna partycja zapewnia lepszą wydajność niż dwie tańsze partycje w ramach usługi, która została zainicjowana w niższej warstwie.

Wyszukiwanie aplikacji, które wymagają odświeżenia danych w czasie rzeczywistym, będzie wymagało jeszcze większej liczby partycji niż repliki. Dodawanie partycji powoduje rozłożenie operacji odczytu/zapisu na większą liczbę zasobów obliczeniowych. Zapewnia również więcej miejsca na dysku do przechowywania dodatkowych indeksów i dokumentów.

Więcej indeksów trwa dłużej. W związku z tym może się okazać, że każdy wzrost przyrostowy partycji wymaga mniejszego, ale proporcjonalnego wzrostu w replikach. Złożoność zapytań i woluminu zapytania polega na tym, jak szybko wykonywanie zapytań jest wyłączone.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybierz warstwę cenową dla usługi Azure Wyszukiwanie poznawcze](search-sku-tier.md)