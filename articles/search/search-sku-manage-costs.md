---
title: Oszacowanie pojemności i kosztów
titleSuffix: Azure Cognitive Search
description: Zapoznaj się ze wskazówkami dotyczącymi oszacowania pojemności i zarządzania kosztami usług wyszukiwania, w tym infrastrukturą i narzędziami na platformie Azure, a także najlepszymi rozwiązaniami dotyczącymi użycia zasobów.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.openlocfilehash: d48ae71a979a2d0f1457b0cefa8a98a02710dd96
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577690"
---
# <a name="how-to-estimate-capacity-and-costs-of-an-azure-cognitive-search-service"></a>Jak oszacować pojemność i koszty usługi Wyszukiwanie poznawcze platformy Azure

W ramach planowania pojemności na platformie Azure Wyszukiwanie poznawcze następujące wskazówki mogą pomóc obniżyć koszty lub skuteczniej zarządzać kosztami:

+ Utwórz wszystkie zasoby w tym samym regionie lub w możliwie najmniejszej liczbie regionów, aby zminimalizować lub wyeliminować opłaty za przepustowość.

+ Konsoliduj wszystkie usługi do jednej grupy zasobów, takiej jak Azure Wyszukiwanie poznawcze, Cognitive Services i inne usługi platformy Azure używane w rozwiązaniu. W Azure Portal Znajdź grupę zasobów i Użyj poleceń **Cost Management** , aby uzyskać wgląd w rzeczywiste i przewidywane wydatki.

+ Rozważ użycie aplikacji sieci Web platformy Azure dla aplikacji frontonu, aby żądania i odpowiedzi były przechowywane w ramach granicy centrum danych.

+ Skalowanie w górę dla operacji intensywnie korzystających z zasobów, takich jak indeksowanie, a następnie korygowanie w dół na potrzeby zwykłych obciążeń zapytań. Rozpocznij od minimalnej konfiguracji Wyszukiwanie poznawcze platformy Azure (jeden element SU składający się z jednej partycji i jednej repliki), a następnie Monitoruj aktywność użytkowników, aby identyfikować wzorce użycia, które wskazują potrzebę większej pojemności. Jeśli istnieje przewidywalny wzorzec, można synchronizować skalę z aktywnością (należy napisać kod w celu zautomatyzowania tego).

W obszarze [Wybierz warstwę cenową](search-sku-tier.md)objaśniono zdarzenia do rozliczenia, formułę rozliczeń i stawkę rozliczaną. Ponadto możesz odwiedzić usługi [rozliczeń i zarządzania kosztami](../cost-management-billing/cost-management-billing-overview.md) dla wbudowanych narzędzi i funkcji związanych z wydatkami.

Tymczasowe wyłączenie usługi wyszukiwania nie jest możliwe. Dedykowane zasoby są zawsze operacyjne, przydzielane do wyłącznego użytku przez okres istnienia usługi. Usuwanie usługi jest trwałe, a także usuwa skojarzone z nią dane.

W odniesieniu do samej usługi jedynym sposobem obniżenia poziomu rachunku jest zredukowanie replik i partycji na poziom, który nadal zapewnia akceptowalną wydajność i zgodność z umową [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), lub tworzenie usługi w niższej warstwie (S1 stawki godzinowe są niższe niż w przypadku stawek S2 i S3). Przy założeniu, że usługa zostanie zainicjowana na niższym końcu projekcji obciążenia, w przypadku skalowalność usługi można utworzyć drugą usługę o większej warstwie, ponownie skompilować indeksy w drugiej usłudze, a następnie usunąć pierwszy z nich.

## <a name="how-to-evaluate-capacity-requirements"></a>Jak oszacować wymagania dotyczące pojemności

Na platformie Azure Wyszukiwanie poznawcze pojemność jest uporządkowana jako *repliki* i *partycje*.

+ Repliki to wystąpienia usługi wyszukiwania. Każda replika obsługuje jedną kopię indeksu o zrównoważonym obciążeniu. Na przykład usługa mająca sześć replik ma sześć kopii każdego indeksu załadowanego w usłudze.

+ Partycje przechowują indeksy i automatycznie dzielą dane z możliwością wyszukiwania. Dwie partycje dzielą indeks na pół, trzy partycje dzielą go na trzecie i tak dalej. W odniesieniu do pojemności *rozmiar partycji* to podstawowa funkcja rozróżniania między warstwami.

> [!NOTE]
> Wszystkie warstwy zoptymalizowane pod kątem standardowej i magazynu obsługują [elastyczne kombinacje replik i partycji](search-capacity-planning.md#chart) , dzięki czemu można [zoptymalizować system pod kątem szybkości lub magazynu](search-performance-optimization.md) , zmieniając saldo. Warstwa Podstawowa oferuje maksymalnie trzy repliki w celu zapewnienia wysokiej dostępności, ale ma tylko jedną partycję. Warstwy bezpłatne nie zapewniają dedykowanych zasobów: zasoby obliczeniowe są współużytkowane przez wielu subskrybentów.

### <a name="evaluating-capacity"></a>Ocena wydajności

Pojemność i koszty uruchomienia usługi są dostępne. Warstwy nakładają limity na dwa poziomy: Magazyn i zawartość (na przykład liczba indeksów). Należy zastanowić się, że w zależności od tego, który limit dociera do pierwszego osiągnięcia, obowiązuje limit.

Wymagania biznesowe zwykle określają liczbę indeksów, które będą potrzebne. Na przykład może być potrzebny indeks globalny dla dużego repozytorium dokumentów. Lub może być potrzebne wiele indeksów opartych na regionie, aplikacji lub w trakcie pracy z firmą.

Aby określić rozmiar indeksu, należy go [skompilować](search-what-is-an-index.md). Jego rozmiar będzie oparty na zaimportowanych danych i konfiguracji indeksu, takich jak włączenie sugestii, filtrowanie i sortowanie.

W przypadku wyszukiwania pełnotekstowego podstawowa struktura danych jest [odwrotną](https://en.wikipedia.org/wiki/Inverted_index) strukturą indeksu, która ma inne cechy niż dane źródłowe. W przypadku odwróconego indeksu rozmiar i złożoność są określane przez zawartość, a nie niekoniecznie ilość danych, które są do niego strumieniowo. Duże źródło danych o wysokiej nadmiarowości może spowodować zmniejszenie indeksu niż mniejszy zestaw danych, który zawiera wysoce zmienną zawartość. Jest to rzadko możliwe do wywnioskowania rozmiaru indeksu na podstawie rozmiaru oryginalnego zestawu danych.

> [!NOTE] 
> Chociaż oszacowanie przyszłych potrzeb dotyczących indeksów i magazynu może wyglądać podobnie jak wątpliwości, warto wykonać te czynności. Jeśli pojemność warstwy wyzostanie zbyt niska, należy udostępnić nową usługę w wyższej warstwie, a następnie [ponownie załadować indeksy](search-howto-reindex.md). Nie istnieje uaktualnienie w miejscu usługi z jednej warstwy do innej.
>

## <a name="estimate-with-the-free-tier"></a>Szacowanie przy użyciu warstwy Bezpłatna

Jednym z metod oszacowania wydajności jest rozpoczęcie od warstwy Bezpłatna. Należy pamiętać, że bezpłatna usługa oferuje maksymalnie trzy indeksy, 50 MB miejsca do magazynowania i 2 minuty czasu indeksowania. Oszacowanie przewidywanego rozmiaru indeksu z tymi ograniczeniami może być trudne, ale te czynności są następujące:

+ [Utwórz bezpłatną usługę](search-create-service-portal.md).
+ Przygotuj mały reprezentatywny zestaw danych.
+ [Utwórz początkowy indeks w portalu](search-get-started-portal.md) i zanotuj jego rozmiar. Funkcje i atrybuty mają wpływ na magazyn. Na przykład dodanie sugestii (zapytania wyszukiwania jako typ) spowoduje zwiększenie wymagań dotyczących magazynu. Korzystając z tego samego zestawu danych, można spróbować utworzyć wiele wersji indeksu z różnymi atrybutami każdego pola, aby zobaczyć, jak różnią się wymagania dotyczące magazynu. Aby uzyskać więcej informacji, zobacz ["konsekwencje dotyczące magazynu" w temacie Tworzenie podstawowego indeksu](search-what-is-an-index.md#index-size).

Dzięki przybliżonemu szacunkowi możesz dwukrotnie określić wartość budżetu dla dwóch indeksów (rozwój i produkcja), a następnie wybrać odpowiednią warstwę.

## <a name="estimate-with-a-billable-tier"></a>Szacowanie przy użyciu warstwy rozliczeniowej

Dedykowane zasoby mogą obsługiwać większe próbkowanie i czasy przetwarzania dla bardziej realistycznych szacunków liczby indeksów, rozmiaru i woluminów zapytań podczas opracowywania. Niektórzy klienci przechodźą w prawo przy użyciu warstwy rozliczeniowej, a następnie ponownie ocenianej jako dojrzały projekt programistyczny.

1. [Przejrzyj limity usługi w każdej warstwie](./search-limits-quotas-capacity.md#index-limits) , aby określić, czy niższe warstwy mogą obsługiwać wymaganą liczbę indeksów. W warstwach Podstawowa, S1 i S2 limity indeksu są odpowiednio 15, 50 i 200. Warstwa zoptymalizowana pod kątem magazynu ma limit 10 indeksów, ponieważ jest ona zaprojektowana do obsługi niskiej liczby bardzo dużych indeksów.

1. [Utwórz usługę w warstwie rozliczanej](search-create-service-portal.md):

    + Jeśli nie masz pewności co do planowanego obciążenia, Zacznij od warstwy Podstawowa lub S1.
    + Zacznij od o godzinie S2 lub nawet S3, Jeśli wiesz, że chcesz mieć indeksowanie dużej skali i ładowanie zapytań.
    + Zacznij od magazynu zoptymalizowanego pod kątem technologii L1 lub L2, jeśli indeksowanie dużej ilości danych jest stosunkowo niskie, podobnie jak w przypadku wewnętrznej aplikacji biznesowej.

1. [Utwórz początkowy indeks](search-what-is-an-index.md) , aby określić sposób, w jaki dane źródłowe są tłumaczone na indeks. Jest to jedyny sposób oszacowania rozmiaru indeksu.

1. [Monitoruj magazyn, limity usług, woluminy zapytań i opóźnienia](search-monitor-usage.md) w portalu. W portalu są wyświetlane zapytania na sekundę, ograniczone zapytania i opóźnienie wyszukiwania. Wszystkie te wartości mogą pomóc zdecydować, czy wybrano odpowiednią warstwę. 

Liczba i rozmiar indeksu są równie ważne dla analizy. Wynika to z faktu, że maksymalne limity są osiągane za pomocą pełnego wykorzystania magazynu (partycji) lub maksymalnych limitów zasobów (indeksów, indeksatorów itd.), w zależności od tego, co nastąpi wcześniej. Portal pomaga śledzić oba elementy, pokazując bieżące użycie i maksymalne limity, obok strony przegląd.

> [!NOTE]
> Wymagania dotyczące magazynu mogą być naliczane, jeśli dokumenty zawierają dane nadmiarowe. W idealnym przypadku dokumenty zawierają tylko te dane, które są potrzebne do obsługi wyszukiwania. Dane binarne nie mogą być wyszukiwane i powinny być przechowywane osobno (mogą znajdować się w tabeli lub w magazynie obiektów blob platformy Azure). W indeksie należy dodać pole do przechowywania odwołania do danych zewnętrznych. Maksymalny rozmiar pojedynczego dokumentu wynosi 16 MB (lub mniej, jeśli są przekazywane zbiorczo wiele dokumentów w jednym żądaniu). Aby uzyskać więcej informacji, zobacz [limity usług w usłudze Azure wyszukiwanie poznawcze](search-limits-quotas-capacity.md).
>

**Zagadnienia dotyczące woluminów zapytań**

Zapytania na sekundę (zapytań) to istotna Metryka w czasie dostrajania wydajności, ale ogólnie można wziąć pod uwagę tylko warstwę, jeśli na początku oczekuje się dużej ilości zapytań.

Warstwy standardowe mogą zapewnić równowagę między replikami i partycjami. Można zwiększyć szybkością oferowaną zapytania, dodając repliki do równoważenia obciążenia lub dodając partycje do przetwarzania równoległego. Następnie można dostroić wydajność po aprowizacji usługi.

Jeśli od samego początku oczekujesz dużej liczby trwałych woluminów zapytań, należy rozważyć wyższe warstwy standardowe. Następnie można przełączyć partycje i repliki w tryb offline, a nawet przełączać się do usługi niższej warstwy, jeśli te woluminy zapytań nie wystąpią. Aby uzyskać więcej informacji na temat obliczania przepływności zapytań, zobacz temat [wydajność i optymalizacja w usłudze Azure wyszukiwanie poznawcze](search-performance-optimization.md).

Warstwy zoptymalizowane pod kątem magazynu są przydatne w przypadku obciążeń dużych ilości danych, dzięki czemu można obsługiwać więcej ogólnego magazynu indeksów, gdy wymagania dotyczące opóźnień zapytań są mniej ważne. Nadal należy używać dodatkowych replik do równoważenia obciążenia i dodatkowych partycji do przetwarzania równoległego. Następnie można dostroić wydajność po aprowizacji usługi.

**Umowy dotyczące poziomu usług**

Funkcje warstwy Bezpłatna i wersja zapoznawcza nie zapewniają [umów dotyczących poziomu usług (umowy SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). W przypadku wszystkich warstw rozliczanych umowy SLA zacznie obowiązywać po wprowadzeniu wystarczającej nadmiarowości dla usługi. Musisz mieć co najmniej dwie repliki dla zapytania (Read) umowy SLA. Musisz mieć trzy lub więcej replik na potrzeby zapytań i indeksowania (do odczytu i zapisu) umowy SLA. Liczba partycji nie ma wpływu na umowy SLA.

## <a name="tips-for-tier-evaluation"></a>Wskazówki dotyczące oceny warstwy

+ Zezwalaj na metryki do kompilowania zapytań i Zbieraj dane dotyczące wzorców użycia (zapytania w godzinach pracy, indeksowanie w godzinach poza szczytem). Te dane służą do informowania o decyzjach o aprowizacji usług. Chociaż nie jest to praktyczne ani codzienne erze, można dynamicznie dostosować partycje i zasoby, aby uwzględnić planowane zmiany w woluminach zapytań. Możesz również uwzględnić niezaplanowane, ale nieplanowane zmiany, jeśli poziomy są wystarczająco długie, aby zagwarantować wykonanie akcji.

+ Należy pamiętać, że jedyną minusem w ramach aprowizacji jest to, że może zajść potrzeba odrywania usługi, jeśli rzeczywiste wymagania są większe niż Twoje prognozy. Aby uniknąć przerw w działaniu usługi, należy utworzyć nową usługę w wyższej warstwie i uruchamiać ją równolegle do momentu, gdy wszystkie aplikacje i żądania będą kierowane do nowego punktu końcowego.

## <a name="next-steps"></a>Następne kroki

Zacznij od warstwy Bezpłatna i skompiluj początkowy indeks przy użyciu podzestawu danych, aby zrozumieć jego cechy. Struktura danych na platformie Azure Wyszukiwanie poznawcze jest odwrotną strukturą indeksu. Rozmiar i złożoność odwróconego indeksu jest określana przez zawartość. Należy pamiętać, że wysoce nadmiarowa zawartość jest wynikiem mniejszych indeksów niż wysoce nieregularna zawartość. Dlatego charakterystyki zawartości, a nie rozmiar zestawu danych określają wymagania dotyczące magazynu indeksu.

Po wstępnym oszacowaniu rozmiaru indeksu [zainicjuj usługę rozliczaną](search-create-service-portal.md) w jednej z warstw omówionych w tym artykule: wersja podstawowa, standardowa i zoptymalizowana pod kątem magazynu. Osłabij wszystkie sztuczne ograniczenia dotyczące ustalania rozmiarów danych i [ponownie skompiluj indeks](search-howto-reindex.md) , aby uwzględnić wszystkie dane, które mają być przeszukiwane.

[Przydziel partycje i repliki](search-capacity-planning.md) w miarę potrzeb, aby uzyskać wymaganą wydajność i skalowanie.

Jeśli wydajność i pojemność są dobrane, wszystko jest gotowe. W przeciwnym razie Utwórz ponownie usługę wyszukiwania w innej warstwie, która jest bardziej dokładnie wyrównana do Twoich potrzeb.

> [!NOTE]
> Jeśli masz pytania, Opublikuj je w usłudze [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) lub [skontaktuj się z pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).