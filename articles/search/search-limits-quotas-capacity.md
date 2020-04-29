---
title: Limity usługi dla warstw i jednostek SKU
titleSuffix: Azure Cognitive Search
description: Limity usługi używane do planowania pojemności oraz maksymalne limity żądań i odpowiedzi dla Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549053"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limity usługi Azure Cognitive Search

Maksymalne limity dotyczące magazynu, obciążeń i ilości indeksów i innych obiektów zależą od tego, czy [usługa Azure wyszukiwanie poznawcze](search-create-service-portal.md) jest udostępniana w warstwach cenowych **bezpłatna**, **podstawowa**, **standardowa**i w przypadku **zoptymalizowanych pod kątem magazynu** .

+ **Bezpłatna** to wielodostępna Usługa udostępniona z subskrypcją platformy Azure. Żądania indeksowania i zapytań są wykonywane na replikach i partycjach, które są używane przez inne dzierżawców.

+ Funkcja **Basic** zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych w mniejszej skali, ale udostępnia infrastrukturę sieciową innym dzierżawcom.

+ **Standardowe** przebiegi na dedykowanych maszynach o większej pojemności magazynu i przetwarzania na każdym poziomie. Standard obejmuje cztery poziomy: S1, S2, S3 i S3 HD.

+ **Zoptymalizowane pod kątem magazynu** są uruchamiane na dedykowanych maszynach o większej liczbie magazynów, przepustowości magazynu i pamięci niż w **warstwie Standardowa**. Zoptymalizowane pod kątem magazynu dostępne są dwa poziomy: L1 i L2

> [!NOTE]
> Od 1 lipca wszystkie warstwy są ogólnie dostępne, w tym warstwy zoptymalizowane pod kątem magazynu. Wszystkie ceny można znaleźć na stronie [szczegółów cennika](https://azure.microsoft.com/pricing/details/search/) .

  Wysoka gęstość S3 (S3 HD) jest zaprojektowana dla określonych obciążeń: [Wielodostępność](search-modeling-multitenant-saas-applications.md) i duże ilości małych indeksów (3000 indeksów na usługę). Ta warstwa nie udostępnia [funkcji indeksatora](search-indexer-overview.md). W systemie S3 HD dane muszą wykorzystać podejście wypychane przy użyciu wywołań interfejsu API w celu wypychania danych ze źródła do indeksu. 

> [!NOTE]
> Usługa została zainicjowana z określoną warstwą. Warstwy przeskoków mające na celu uzyskanie pojemności wymagają aprowizacji nowej usługi (uaktualnienie w miejscu nie istnieje). Aby uzyskać więcej informacji, zobacz [Wybieranie jednostki SKU lub warstwy](search-sku-tier.md). Aby dowiedzieć się więcej o dostosowywaniu pojemności w ramach już zainicjowanej usługi, zobacz [poziomy zasobów skalowania dla obciążeń zapytań i indeksowania](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limity subskrypcji
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limity magazynu
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limity indeksu

| Zasób | Bezpłatna | Podstawowa&nbsp;<sup>1</sup>  | S1 | S2 | S3 | Wyjście&nbsp;S3 HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maksymalna liczba indeksów |3 |5 lub 15 |50 |200 |200 |1000 na partycję lub 3000 na usługę |10 |10 |
| Maksymalna liczba prostych pól na indeks |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maksymalna liczba złożonych pól kolekcji na indeks |40 |40 |40 |40 |40 |40 |40 |40 |
| Maksymalna liczba elementów we wszystkich złożonych kolekcjach&nbsp;na dokument<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maksymalna głębokość pól złożonych |10 |10 |10 |10 |10 |10 |10 |10 |
| Maksymalna liczba [sugestii](https://docs.microsoft.com/rest/api/searchservice/suggesters) na indeks |1 |1 |1 |1 |1 |1 |1 |1 |
| Maksymalna liczba [profilów oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) na indeks |100 |100 |100 |100 |100 |100 |100 |100 |
| Maksymalna liczba funkcji na profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> podstawowe usługi utworzone przed grudnia 2017 mają niższe limity (5 zamiast 15) w indeksach. Warstwa Podstawowa jest jedyną jednostką SKU z niższym limitem 100 pól na indeks.

<sup>2</sup> posiadanie bardzo dużej liczby elementów w złożonych kolekcjach dla dokumentu powoduje obecnie duże wykorzystanie magazynu. Jest to znany problem. W międzyczasie limit 3000 jest bezpieczny górną granicą dla wszystkich warstw usług. Ten limit jest wymuszany tylko w przypadku operacji indeksowania, które wykorzystują najwcześniejszą ogólnie dostępną wersję interfejsu API, która obsługuje`2019-05-06`pola typu złożonego (). Aby nie przerywać klientów, którzy mogą korzystać z wcześniejszych wersji programu Preview API (które obsługują pola typu złożonego), nie będzie wymuszany ten limit dla operacji indeksowania korzystających z tych wersji programu Preview API. Należy pamiętać, że wersji zapoznawczej interfejsu API nie można używać w scenariuszach produkcyjnych, a firma Microsoft zaleca klientom przejście do najnowszej wersji interfejsu API o wysokiej dostępności.

> [!NOTE]
> Chociaż maksymalna pojemność pojedynczego indeksu jest zwykle ograniczona przez dostępny magazyn, istnieją maksymalne górne granice całkowitej liczby dokumentów, które mogą być przechowywane w pojedynczym indeksie. Ten limit wynosi około 24 000 000 000 dokumentów na indeks dla usług wyszukiwania Basic, S1, S2 i S3 oraz 2 000 000 000 dokumentów na indeks dla usług wyszukiwania S3HD. Każdy element złożonych kolekcji jest traktowany jako osobne dokumenty na potrzeby tych limitów.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limity dokumentów 

Od października 2018 nie ma już żadnych limitów żadnego dokumentu dla każdej nowej usługi utworzonej w dowolnej warstwie rozliczeniowej (podstawowa, S1, S2, S3, S3 HD) w dowolnym regionie. Starsze usługi utworzone przed październikiem 2018 mogą nadal podlegać limitom liczby dokumentów.

Aby ustalić, czy usługa ma limity dokumentów, należy użyć [interfejsu API REST usługi Get Service Statistics](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Limity dokumentów są uwzględniane w odpowiedzi z `null` oznaczeniem braku ograniczeń.

### <a name="document-size-limits-per-api-call"></a>Limity rozmiaru dokumentu dla wywołania interfejsu API

Maksymalny rozmiar dokumentu podczas wywoływania interfejsu API indeksu wynosi około 16 megabajtów.

Rozmiar dokumentu jest w rzeczywistości limitem rozmiaru treści żądania interfejsu API indeksu. Ponieważ jednocześnie można przekazać partię wielu dokumentów do interfejsu API indeksu, ograniczenie rozmiaru jest realistyczne od liczby dokumentów w partii. W przypadku partii z jednym dokumentem maksymalny rozmiar dokumentu wynosi 16 MB w formacie JSON.

Aby zachować rozmiar dokumentu w dół, pamiętaj, aby wykluczyć z żądania dane inne niż queryable. Obrazy i inne dane binarne nie są bezpośrednio Queryable i nie powinny być przechowywane w indeksie. Aby zintegrować dane niequeryablene z wynikami wyszukiwania, Zdefiniuj pole niedrukowalne, które przechowuje odwołanie do tego zasobu.

## <a name="indexer-limits"></a>Limity indeksatora

Maksymalne czasy działania są dostępne w celu zapewnienia równowagi i stabilności usługi jako całości, ale większe zestawy danych mogą wymagać więcej czasu indeksowania niż maksymalna dozwolona wartość. Jeśli zadanie indeksowania nie może zakończyć się w maksymalnym dozwolonym czasie, spróbuj uruchomić je zgodnie z harmonogramem. Harmonogram śledzi Stan indeksowania. Jeśli zaplanowane zadanie indeksowania zostanie przerwane z dowolnego powodu, indeksator może zostać pobrany w miejscu, w którym został on ostatnio pozostawiony podczas następnego zaplanowanego uruchomienia.


| Zasób | Bezpłatnie&nbsp;<sup>1</sup> | Podstawowa&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maksymalna liczba indeksatorów |3 |5 lub 15|50 |200 |200 |Nie dotyczy |10 |10 |
| Maksymalna liczba źródeł danych |3 |5 lub 15 |50 |200 |200 |Nie dotyczy |10 |10 |
| Maksymalna umiejętności <sup>4</sup> |3 |5 lub 15 |50 |200 |200 |Nie dotyczy |10 |10 |
| Maksymalne ładowanie indeksowania na wywołanie |10 000 dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Nie dotyczy |Bez ograniczeń |Bez ograniczeń |
| Minimalny harmonogram | 5 minut |5 minut |5 minut |5 minut |5 minut |5 minut |5 minut | 5 minut |
| Maksymalny czas działania <sup>5</sup> | 1-3 minut |24 godziny |24 godziny |24 godziny |24 godziny |Nie dotyczy  |24 godziny |24 godziny |
| Maksymalny czas działania dla umiejętności wyszukiwania poznawczego lub indeksowania obiektów BLOB za pomocą analizy obrazów <sup>5</sup> | 3-10 minut |2 godziny |2 godziny |2 godziny |2 godziny |Nie dotyczy  |2 godziny |2 godziny |
| Indeksator obiektów blob: maksymalny rozmiar obiektu BLOB, MB |16 |16 |128 |256 |256 |Nie dotyczy  |256 |256 |
| Indeksator obiektów blob: Maksymalna liczba znaków zawartości wyodrębnionych z obiektu BLOB |32 000 |64 000 |4&nbsp;mln |8&nbsp;mln |16&nbsp;mln |Nie dotyczy |4&nbsp;mln |4&nbsp;mln |

<sup>1</sup> bezpłatne usługi mają maksymalny czas wykonywania indeksatora wynoszący 3 minuty dla źródeł obiektów blob i 1 minuty dla wszystkich innych źródeł danych. W przypadku indeksowania AI, które wywołuje Cognitive Services, bezpłatne usługi są ograniczone do 20 bezpłatnych transakcji dziennie, gdzie transakcja jest definiowana jako dokument, który został pomyślnie przeszedł przez potok wzbogacania.

<sup>2</sup> podstawowe usługi utworzone przed grudnia 2017 mają niższe limity (5 zamiast 15) dla indeksatorów, źródeł danych i umiejętności.

<sup>3</sup> usługi HD S3 nie obejmują obsługi indeksatora.

<sup>4</sup> maksymalnie 30 umiejętności na zestawu umiejętności.

<sup>5</sup> obciążeń wyszukiwania poznawczego i analizy obrazów w indeksie obiektów blob platformy Azure mają krótsze czasy działania niż zwykłe indeksowanie tekstu. Analiza obrazów i przetwarzanie języka naturalnego są intensywnie pracochłonne i zużywają nieproporcjonalne ilości dostępnej mocy obliczeniowej. Czas działania został zmniejszony w celu nadania innym zadania w kolejce możliwości uruchomienia.  

> [!NOTE]
> Jak określono w [limitach indeksu](#index-limits), indeksatory będą również wymuszać górny limit 3000 elementów we wszystkich złożonych kolekcjach dla każdego dokumentu, rozpoczynając od najnowszej wersji interfejsu API GA,`2019-05-06`która obsługuje typy złożone (). Oznacza to, że jeśli utworzono indeksator z poprzednią wersją interfejsu API, nie będzie to możliwe. Aby zachować maksymalną zgodność, indeksator, który został utworzony przy użyciu wcześniejszej wersji interfejsu API, a następnie zaktualizowany przy użyciu `2019-05-06` wersji interfejsu API lub nowszej, będzie nadal **wykluczony** z limitów. Klienci powinni mieć świadomość niekorzystnego wpływu na bardzo duże złożone kolekcje (jak wspomniano wcześniej) i zdecydowanie zaleca się utworzenie nowych indeksatorów z najnowszą wersją interfejsu API w wersji GA.

## <a name="synonym-limits"></a>Limity synonimów

Maksymalna liczba dozwolonych map synonimów jest różna w zależności od warstwy cenowej. Każda reguła może mieć do 20 rozszerzeń, w których rozwinięcie jest równoważne. Na przykład podaną "Cat", skojarzenie z "Kitty", "Feline" i "Felis" (rodzaj dla kotów) będzie liczona jako 3 rozszerzenia.

| Zasób | Bezpłatna | Podstawowy | S1 | S2 | S3 | S3 — HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maksymalne mapy synonimów |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maksymalna liczba reguł na mapę |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Zapytania na sekundę (zapytań)

Oszacowania zapytań muszą być opracowane niezależnie od każdego klienta. Rozmiar indeksu i złożoność, rozmiar zapytania i złożoność oraz wielkość ruchu to podstawowe znaczniki zapytań. Nie ma możliwości zaoferowania znaczących szacunków, gdy takie czynniki są nieznane.

Oszacowania są bardziej przewidywalne, gdy są obliczane w usługach uruchomionych na dedykowanych zasobach (warstwach Podstawowa i standardowa). Możesz oszacować zapytań bardziej blisko, ponieważ masz kontrolę nad większymi parametrami. Aby uzyskać wskazówki dotyczące sposobu podejścia do oceny, zobacz temat [wydajność i optymalizacja na platformie wyszukiwanie poznawcze Azure](search-performance-optimization.md).

W przypadku warstw zoptymalizowanych pod kątem magazynu należy oczekiwać mniejszej przepływności zapytań i wyższych opóźnień niż w przypadku warstw standardowych.  Metodologia oszacowania wydajności zapytań, która będzie w trakcie pracy, jest taka sama jak w przypadku warstw standardowych.

## <a name="data-limits-ai-enrichment"></a>Limity danych (wzbogacanie AI)

[Potok wzbogacenia AI](cognitive-search-concept-intro.md) , który tworzy wywołania do zasobu analiza tekstu na potrzeby [rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md), [wyodrębniania fraz kluczy](cognitive-search-skill-keyphrases.md), [analizy tonacji](cognitive-search-skill-sentiment.md), [wykrywania języka](cognitive-search-skill-language-detection.md)i [wykrywania danych osobowych](cognitive-search-skill-pii-detection.md) podlega limitom. Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Jeśli musisz podzielić dane przed wysłaniem ich do analizatora tonacji, użyj [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limity ograniczania

Zapytania wyszukiwania i żądania indeksowania są ograniczone, ponieważ system zbliża się do pojemności szczytowej. Ograniczanie przepływności działa inaczej dla różnych interfejsów API. Interfejsy API zapytań (wyszukiwanie/sugerowanie/Autouzupełnianie) i indeksowanie interfejsów API indeksowania dynamicznie na podstawie obciążenia usługi. Interfejsy API indeksu mają limity szybkości żądań statycznych. 

Limity liczby żądań statycznych dla operacji związanych z indeksem:

+ Indeksy list (GET/Indexes): 5 na sekundę na jednostkę wyszukiwania
+ Pobierz indeks (GET/Indexes/MyIndex): 10 na sekundę na jednostkę wyszukiwania
+ Utwórz indeks (POST/Indexes): 12 na minutę na jednostkę wyszukiwania
+ Utwórz lub zaktualizuj indeks (PUT/Indexes/MyIndex): 6 na sekundę na jednostkę wyszukiwania
+ Usuń indeks (DELETE/Indexes/MyIndex): 12 na minutę na jednostkę wyszukiwania 

## <a name="api-request-limits"></a>Limity żądań interfejsu API
* Maksymalnie 16 MB na żądanie <sup>1</sup>
* Maksymalna długość adresu URL 8 KB
* Maksymalna 1000 dokumentów na partię operacji przekazywania, scalania lub usuwania indeksu
* Maksymalna 32 pól w klauzuli $orderby
* Maksymalny rozmiar terminu wyszukiwania to 32 766 bajtów (32 KB minus 2 bajty) tekstu zakodowanego w formacie UTF-8

<sup>1</sup> na platformie Azure wyszukiwanie poznawcze treść żądania podlega górnemu limitowi 16 MB, co nakłada praktyczny limit zawartości poszczególnych pól lub kolekcji, które nie są w inny sposób ograniczone przez limity teoretyczne (zobacz [obsługiwane typy danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , aby uzyskać więcej informacji na temat kompozycji i ograniczeń pól).

## <a name="api-response-limits"></a>Limity odpowiedzi interfejsu API
* Maksymalna 1000 dokumentów zwróconych na stronę wyników wyszukiwania
* Maksymalna 100 sugestii zwróconych na żądanie interfejsu API sugestii

## <a name="api-key-limits"></a>Limity kluczy interfejsu API
Klucze interfejsu API są używane do uwierzytelniania usługi. Są dwa typy analizatorów. Klucze administratora są określone w nagłówku żądania i zapewniają pełny dostęp do odczytu i zapisu do usługi. Klucze zapytań są tylko do odczytu, określone w adresie URL i zazwyczaj są dystrybuowane do aplikacji klienckich.

* Maksymalnie 2 klucze administracyjne na usługę
* Maksymalnie 50 kluczy zapytań na usługę