---
title: Porady dotyczące wydajności
titleSuffix: Azure Cognitive Search
description: Poznaj wskazówki i najlepsze rozwiązania dotyczące maksymalizowania wydajności usługi wyszukiwania.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 28325a1bbda1b2d4a4bb060ae3e79057275ee42a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582349"
---
# <a name="tips-for-better-performance-in-azure-cognitive-search"></a>Wskazówki dotyczące lepszej wydajności na platformie Azure Wyszukiwanie poznawcze

Ten artykuł zawiera zbiór porad i najlepszych rozwiązań, które często są zalecane w celu zwiększenia wydajności. Wiedząc, które czynniki najprawdopodobniej mają wpływ na wydajność wyszukiwania, mogą pomóc w uniknięciu niewydajności i największej możliwości usługi wyszukiwania. Niektóre kluczowe czynniki obejmują:

+ Kompozycja indeksu (schemat i rozmiar)
+ Typy zapytań
+ Pojemność usługi (warstwa i liczba replik i partycji)

## <a name="index-size-and-schema"></a>Rozmiar indeksu i schemat

Zapytania działają szybciej na mniejszych indeksach. Jest to częściowo funkcja, która ma mniej pól do skanowania, ale również z powodu tego, jak system buforuje zawartość dla przyszłych zapytań. Po wykonaniu pierwszej kwerendy część zawartości pozostanie w pamięci, w której jest przeszukiwana bardziej wydajnie. Ze względu na to, że rozmiar indeksu rośnie wraz z upływem czasu, najlepszym rozwiązaniem jest okresowe odwiedzenie kompozycji indeksu, schematu i dokumentów, aby wyszukać możliwości zmniejszenia zawartości. Jeśli jednak indeks ma odpowiedni rozmiar, jedyną inną kalibracją, którą można wykonać, jest zwiększenie pojemności: przez [dodanie replik](search-capacity-planning.md#adjust-capacity) lub uaktualnienie warstwy usług. Sekcja ["Porada: uaktualnianie do warstwy Standardowa S2"]] (#tip-Upgrade-to-a-standard-S2-warstwowa) pokazuje, jak oszacować skalę w górę i w poziomie.

Złożoność schematu może również mieć negatywny wpływ na indeksowanie i wydajność zapytań. Nadmierne przypisywanie pól w ograniczeniach i wymaganiach dotyczących przetwarzania. [Typy złożone](search-howto-complex-data-types.md) pobierają więcej indeksów i zapytań. W następnych sekcjach opisano każdy warunek.

### <a name="tip-be-selective-in-field-attribution"></a>Porada: można wybiórczo w przypisywaniu pól

Typowy błąd, który Administratorzy i deweloper podczas tworzenia indeksu wyszukiwania zaznacza wszystkie dostępne właściwości dla pól, w przeciwieństwie do wyboru tylko właściwości, które są potrzebne. Na przykład, jeśli pole nie musi być wyszukiwaniem pełnotekstowym, pomiń to pole podczas ustawiania atrybutu z możliwością wyszukiwania.

:::image type="content" source="media/search-performance/perf-selective-field-attributes.png" alt-text="Przyznawanie wybiórcze" border="true":::

Obsługa filtrów, aspektów i sortowania może mieć czterokrotne wymagania dotyczące magazynu. Jeśli dodasz sugestie, wymagania dotyczące magazynu trafią jeszcze więcej. Ilustracja przedstawiająca wpływ atrybutów na magazyn można znaleźć w temacie [atrybuty i rozmiar indeksu](search-what-is-an-index.md#index-size).

Podsumowujące, konsekwencje nadmiernego przypisywania obejmują:

+ Obniżenie wydajności indeksowania z powodu dodatkowej pracy wymaganej do przetworzenia zawartości w polu, a następnie zapisania jej w odwróconym indeksie wyszukiwania (ustaw atrybut "z możliwością wyszukiwania" tylko dla pól zawierających zawartość do przeszukiwania).

+ Tworzy większą powierzchnię, którą każda kwerenda musi uwzględnić. Wszystkie pola oznaczone jako przeszukiwane są skanowane w ramach wyszukiwania pełnotekstowego.

+ Zwiększa koszty operacyjne z powodu dodatkowego magazynu. Filtrowanie i sortowanie wymaga dodatkowego miejsca do przechowywania oryginalnych (nieanalizowanych) ciągów. Należy unikać ustawiania filtrowania lub sortowania dla pól, które ich nie potrzebują.

+ W wielu przypadkach za pośrednictwem przypisywania ogranicza możliwości pola. Na przykład, jeśli pole ma charakter, z możliwością filtrowania i wyszukiwania, można przechowywać 16 KB tekstu w obrębie pola, natomiast pole z możliwością wyszukiwania może zawierać do 16 MB tekstu.

> [!NOTE]
> Należy unikać tylko niepotrzebnego przypisywania. Filtry i zestawy reguł często mają kluczowe znaczenie dla środowiska wyszukiwania, a w przypadkach, gdy filtry są używane, często potrzebne jest sortowanie, aby można było zamówić wyniki (filtry przez siebie zwracają w zestawie nieuporządkowanym).

### <a name="tip-consider-alternatives-to-complex-types"></a>Porada: Uwzględnij alternatywy dla typów złożonych

Złożone typy danych są przydatne, gdy dane mają skomplikowaną strukturę zagnieżdżoną, taką jak elementy nadrzędny-podrzędny, które znajdują się w dokumentach JSON. Minusem typy złożone są dodatkowymi wymaganiami dotyczącymi magazynu i dodatkowymi zasobami wymaganymi do indeksowania zawartości w porównaniu z niezłożonymi typami danych. 

W niektórych przypadkach można uniknąć tych kompromisów, mapując złożoną strukturę danych do prostszego typu pola, takiego jak kolekcja. Alternatywnie można wybrać opcję spłaszczenia hierarchii pól do poszczególnych pól poziomu głównego.

:::image type="content" source="media/search-performance/perf-flattened-field-hierarchy.png" alt-text="spłaszczona struktura pól" border="true":::

## <a name="types-of-queries"></a>Typy zapytań

Typy wysyłanych kwerend są jednym z najważniejszych czynników wydajności, a Optymalizacja zapytań może znacząco poprawić wydajność. Podczas projektowania zapytań należy wziąć pod uwagę następujące kwestie:

+ **Liczba pól do przeszukiwania.** Każde dodatkowe pole z możliwością wyszukiwania wymaga dodatkowej pracy przez usługę wyszukiwania. Można ograniczyć przeszukiwane pola w czasie zapytania przy użyciu parametru "searchFields". Najlepszym rozwiązaniem jest określenie tylko pól, które należy podjąć, aby zwiększyć wydajność.

+ **Ilość zwracanych danych.** Pobieranie dużej ilości zawartości może spowodować wolniejsze wykonywanie zapytań. Podczas tworzenia struktury zapytania należy zwrócić tylko pola, które są potrzebne do renderowania strony wyników, a następnie pobrać pozostałe pola przy użyciu [interfejsu API wyszukiwania](/rest/api/searchservice/lookup-document) , gdy użytkownik wybierze dopasowanie.

+ **Użycie wyszukiwania częściowego warunku.** [Częściowe wyszukiwania warunkowe](search-query-partial-matching.md), takie jak wyszukiwanie prefiksów, Wyszukiwanie rozmyte i wyszukiwanie wyrażeń regularnych, są bardziej kosztowne niż typowe wyszukiwania słów kluczowych, ponieważ wymagają skanowania pełnego indeksu w celu uzyskania wyników.

+ **Liczba aspektów.** Dodawanie aspektów do zapytań wymaga agregacji dla każdego zapytania. Ogólnie rzecz biorąc Dodaj tylko te aspekty, które planujesz renderować w aplikacji.

+ **Ograniczanie pól o dużej kardynalności.**  *Pole wysoka Kardynalność* odwołuje się do pola z elementem lub z możliwością filtrowania, które ma znaczną liczbę unikatowych wartości, a w efekcie zużywa znaczące zasoby podczas obliczania wyników. Na przykład ustawienie identyfikatora produktu lub pola opisu jako "kroju" i "filtrującego" będzie traktowane jako wysoka Kardynalność, ponieważ większość wartości z dokumentu do dokumentu jest unikatowa.

### <a name="tip-use-search-functions-instead-overloading-filter-criteria"></a>Porada: Użyj funkcji wyszukiwania zamiast kryteriów filtrowania przeciążania

Ponieważ zapytanie używa coraz bardziej [złożonych kryteriów filtrowania](search-query-odata-filter.md#filter-size-limitations), wydajność zapytania wyszukiwania zostanie obniżona. Rozważmy poniższy przykład, który ilustruje użycie filtrów do przycinania wyników w oparciu o tożsamość użytkownika:

```json
$filter= userid eq 123 or userid eq 234 or userid eq 345 or userid eq 456 or userid eq 567
```

W takim przypadku wyrażenia filtru służą do sprawdzania, czy pojedyncze pole w każdym dokumencie jest równe jednej z wielu możliwych wartości tożsamości użytkownika. Najprawdopodobniej znajdziesz ten wzorzec w aplikacjach, które implementują [przycinanie zabezpieczeń](search-security-trimming-for-azure-search.md) (sprawdzając pole zawierające co najmniej jeden identyfikator podmiotu) na liście identyfikatorów głównych reprezentujących użytkownika wystawiającego zapytanie).

Bardziej wydajnym sposobem wykonywania filtrów zawierających dużą liczbę wartości jest użycie [ `search.in` funkcji](search-query-odata-search-in-function.md), jak pokazano w poniższym przykładzie:

```json
search.in(userid, '123,234,345,456,567', ',')
```

### <a name="tip-add-partitions-for-slow-individual-queries"></a>Porada: Dodawanie partycji dla wolnych pojedynczych zapytań

Gdy wydajność zapytań jest ogólnie niska, dodanie większej liczby replik często rozwiązuje problem. Ale co zrobić, jeśli problem dotyczy pojedynczego zapytania, które trwa zbyt długo? W tym scenariuszu dodanie replik nie powiedzie się, ale mogą wystąpić dodatkowe partycje. Partycja dzieli dane na dodatkowe zasoby obliczeniowe. Dwie partycje dzielą dane w połowie, trzecia partycja dzieli je na trzecie i tak dalej. 

Jeden dodatni efekt dodawania partycji polega na tym, że wolniejsze zapytania czasami przyspieszają działanie z powodu równoległego przetwarzania danych. Zanotowano przetwarzanie równoległe na temat niskich zapytań selektywnych, takich jak zapytania, które pasują do wielu dokumentów, lub aspektów dostarczających liczniki w dużej liczbie dokumentów. Ponieważ do oceny dokładność dokumentów lub obliczenia liczby dokumentów wymagane jest znaczące obliczenie, dodawanie dodatkowych partycji ułatwia wykonywanie zapytań.  

Aby dodać partycje, użyj [Azure Portal](search-create-service-portal.md), [PowerShell](search-manage-powershell.md), [interfejsu wiersza polecenia platformy Azure](search-manage-azure-cli.md)lub zestawu SDK zarządzania.

## <a name="service-capacity"></a>Pojemność usługi

Usługa jest nakładana, gdy zapytania trwają zbyt długo lub gdy usługa zaczyna przerywać żądania. W takim przypadku można rozwiązać problem, uaktualniając usługę lub dodając pojemność.

Warstwa usługi wyszukiwania oraz liczba replik/partycji również mają duży wpływ na wydajność. Każda wyższa warstwa zapewnia szybszy procesor CPU i więcej pamięci, z których oba mają pozytywny wpływ na wydajność.

### <a name="tip-upgrade-to-a-standard-s2-tier"></a>Porada: uaktualnianie do warstwy Standardowa S2

W przypadku standardowej warstwy wyszukiwania S1 często klienci są uruchamiani. Typowym wzorcem usług S1 jest to, że indeksy rośnie wraz z upływem czasu, co wymaga większej liczby partycji. Więcej partycji prowadzi do wolniejszych czasów odpowiedzi, więc do obsługi obciążenia zapytania dodawane są więcej replik. Jak można wyobrazić, koszt uruchomienia usługi S1 już się zakończył na poziomach wykraczających poza konfigurację początkową.

W tym celu należy zwrócić uwagę na to, czy korzystne jest przejście do wyższej warstwy, w przeciwieństwie do stopniowego zwiększania liczby partycji lub replik bieżącej usługi. 

Rozważmy poniższą topologię jako przykład usługi, która została podjęta w przypadku zwiększenia poziomu wydajności:

+ Warstwa standardowa S1
+ Rozmiar indeksu: 190 GB
+ Liczba partycji: 8 (na S1, rozmiar partycji wynosi 25 GB na partycję)
+ Liczba replik: 2
+ Łączna liczba jednostek wyszukiwania: 16 (8 partycji x 2)
+ Hipotetyczna cena detaliczna: ~ $4 000 USD/miesiąc (przyjmuje $250 USD x 16 jednostek wyszukiwania)

Załóżmy, że administrator usługi nadal widzi wyższe stawki opóźnienia i rozważa dodanie innej repliki. Spowoduje to zmianę liczby replik z 2 na 3, a w rezultacie zmiana liczby jednostek wyszukiwania na 24 oraz wynikowa Cena $6 000 USD/miesiąc.

Jeśli jednak administrator wybrał opcję przejścia do warstwy Standardowa S2, topologia będzie wyglądać następująco:

+ Warstwa standardowa S2
+ Rozmiar indeksu: 190 GB
+ Liczba partycji: 2 (w przypadku S2, rozmiar partycji wynosi 100 GB na partycję)
+ Liczba replik: 2
+ Łączna liczba jednostek wyszukiwania: 4 (2 partycje x 2 repliki)
+ Hipotetyczna cena detaliczna: ~ $4 000 USD/miesiąc ($1000 USD x 4 jednostek wyszukiwania)

Ponieważ ten hipotetyczny scenariusz ilustruje, konfiguracje w niższych warstwach mogą powodować podobne koszty, tak jakby była wybrana wyższa warstwa w pierwszym miejscu. Jednak wyższe warstwy są dostarczane z usługą Premium Storage, co sprawia, że indeksowanie jest szybsze. Wyższe warstwy również mają znacznie większą moc obliczeniową, a także dodatkową pamięć. W przypadku tych samych kosztów można utworzyć bardziej wydajną infrastrukturę z tym samym indeksem.

Ważną zaletą dodanej pamięci jest to, że większy indeks może być buforowany, co zmniejsza opóźnienie wyszukiwania i większą liczbę zapytań na sekundę. Dzięki tej dodatkowej mocy administrator może nie potrzebować nawet zwiększenia liczby replik i może zapłatę mniej niż przez przekroczenie usługi S1.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tymi dodatkowymi artykułami dotyczącymi wydajności usług.

+ [Analizowanie wydajności](search-performance-analysis.md)
+ [Wybierz warstwę usług](search-sku-tier.md)
+ [Dodawanie pojemności (repliki i partycje)](search-capacity-planning.md#adjust-capacity)
