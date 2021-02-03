---
title: Limity usługi dla warstw i jednostek SKU
titleSuffix: Azure Cognitive Search
description: Limity usługi używane do planowania pojemności oraz maksymalne limity żądań i odpowiedzi dla Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 994ed74750d159dfdb83259e9fe921f870ec2241
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509371"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Limity usługi Azure Cognitive Search

Maksymalne limity dotyczące magazynu, obciążeń i ilości indeksów i innych obiektów zależą od tego, czy [usługa Azure wyszukiwanie poznawcze](search-create-service-portal.md) jest udostępniana w warstwach cenowych **bezpłatna**, **podstawowa**, **standardowa** i w przypadku **zoptymalizowanych pod kątem magazynu** .

+ **Bezpłatna** to wielodostępna Usługa udostępniona z subskrypcją platformy Azure. 

+ Funkcja **Basic** zapewnia dedykowane zasoby obliczeniowe dla obciążeń produkcyjnych w mniejszej skali, ale udostępnia infrastrukturę sieciową innym dzierżawcom.

+ **Standardowe** przebiegi na dedykowanych maszynach o większej pojemności magazynu i przetwarzania na każdym poziomie. Standard obejmuje cztery poziomy: S1, S2, S3 i S3 HD. Wysoka gęstość S3 (S3 HD) jest zaprojektowana dla [wielu dzierżawców](search-modeling-multitenant-saas-applications.md) i dużych ilości małych indeksów (3000 indeksów na usługę). Funkcja S3 HD nie zapewnia [funkcji indeksatora](search-indexer-overview.md) i pozyskiwania danych musi korzystać z interfejsów API, które wypychanie danych ze źródła do indeksu. 

+ **Zoptymalizowane pod kątem magazynu** są uruchamiane na dedykowanych maszynach o większej liczbie magazynów, przepustowości magazynu i pamięci niż w **warstwie Standardowa**. Ta warstwa wskazuje duże, wolne od siebie indeksy. Optymalizacja pod kątem magazynu jest dostępna na dwóch poziomach: L1 i L2.

## <a name="subscription-limits"></a>Limity subskrypcji
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limity magazynu
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limity indeksu

| Zasób | Bezpłatna | Podstawowa &nbsp; <sup>1</sup>  | S1 | S2 | S3 | &nbsp;Wyjście S3 HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maksymalna liczba indeksów |3 |5 lub 15 |50 |200 |200 |1000 na partycję lub 3000 na usługę |10 |10 |
| Maksymalna liczba prostych pól na indeks |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maksymalna liczba złożonych pól kolekcji na indeks |40 |40 |40 |40 |40 |40 |40 |40 |
| Maksymalna liczba elementów we wszystkich złożonych kolekcjach na dokument &nbsp; <sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maksymalna głębokość pól złożonych |10 |10 |10 |10 |10 |10 |10 |10 |
| Maksymalna liczba [sugestii](/rest/api/searchservice/suggesters) na indeks |1 |1 |1 |1 |1 |1 |1 |1 |
| Maksymalna liczba [profilów oceniania](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) na indeks |100 |100 |100 |100 |100 |100 |100 |100 |
| Maksymalna liczba funkcji na profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> podstawowe usługi utworzone przed grudnia 2017 mają niższe limity (5 zamiast 15) w indeksach. Warstwa Podstawowa jest jedyną jednostką SKU z niższym limitem 100 pól na indeks.

<sup>2</sup> dla elementów istnieje górny limit, ponieważ jego duża liczba znacząco zwiększa magazyn wymagany dla Twojego indeksu. Element kolekcji złożonej jest zdefiniowany jako element członkowski tej kolekcji. Załóżmy na przykład, że [dokument hotelu ma złożoną kolekcję pokojów](search-howto-complex-data-types.md#indexing-complex-types), każdy pokój w kolekcji pokojów jest traktowany jako element. Podczas indeksowania aparat indeksowania może bezpiecznie przetwarzać maksymalnie 3000 elementów w całym dokumencie. [Ten limit](search-api-migration.md#upgrade-to-2019-05-06) został wprowadzony w `api-version=2019-05-06` i ma zastosowanie tylko do złożonych kolekcji, a nie do kolekcji ciągów lub do pól złożonych.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limity dokumentów 

Od października 2018 nie ma już żadnych limitów żadnego dokumentu dla każdej nowej usługi utworzonej w dowolnej warstwie rozliczeniowej (podstawowa, S1, S2, S3, S3 HD) w dowolnym regionie. Starsze usługi utworzone przed październikiem 2018 mogą nadal podlegać limitom liczby dokumentów.

Aby ustalić, czy usługa ma limity dokumentów, należy użyć [interfejsu API REST usługi Get Service Statistics](/rest/api/searchservice/get-service-statistics). Limity dokumentów są uwzględniane w odpowiedzi z oznaczeniem `null` braku ograniczeń.

> [!NOTE]
> Mimo że nie ma żadnych ograniczeń dokumentu narzuconych przez usługę, istnieje limit fragmentu o około 24 000 000 000 dokumentów na indeks w usługach wyszukiwania w warstwach Podstawowa, S1, S2 i S3. W przypadku platformy S3 HD limit fragmentu to 2 000 000 000 dokumentów na indeks. Każdy element kolekcji złożonej jest traktowany jako odrębny dokument pod kątem limitów fragmentu.

### <a name="document-size-limits-per-api-call"></a>Limity rozmiaru dokumentu dla wywołania interfejsu API

Maksymalny rozmiar dokumentu podczas wywoływania interfejsu API indeksu wynosi około 16 megabajtów.

Rozmiar dokumentu jest w rzeczywistości limitem rozmiaru treści żądania interfejsu API indeksu. Ponieważ jednocześnie można przekazać partię wielu dokumentów do interfejsu API indeksu, ograniczenie rozmiaru jest realistyczne od liczby dokumentów w partii. W przypadku partii z jednym dokumentem maksymalny rozmiar dokumentu wynosi 16 MB w formacie JSON.

Podczas szacowania rozmiaru dokumentu Pamiętaj, aby uwzględnić tylko te pola, które mogą być używane przez usługę wyszukiwania. Wszystkie dane binarne lub obrazy w dokumentach źródłowych należy pominąć w obliczeniach.  

## <a name="indexer-limits"></a>Limity indeksatora

Maksymalne czasy działania są dostępne w celu zapewnienia równowagi i stabilności usługi jako całości, ale większe zestawy danych mogą wymagać więcej czasu indeksowania niż maksymalna dozwolona wartość. Jeśli zadanie indeksowania nie może zakończyć się w maksymalnym dozwolonym czasie, spróbuj uruchomić je zgodnie z harmonogramem. Harmonogram śledzi Stan indeksowania. Jeśli zaplanowane zadanie indeksowania zostanie przerwane z dowolnego powodu, indeksator może zostać pobrany w miejscu, w którym został on ostatnio pozostawiony podczas następnego zaplanowanego uruchomienia.


| Zasób | Bezpłatnie &nbsp; <sup>1</sup> | Podstawowa &nbsp; <sup>2</sup>| S1 | S2 | S3 | S3 &nbsp; HD &nbsp; <sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maksymalna liczba indeksatorów |3 |5 lub 15|50 |200 |200 |Nie dotyczy |10 |10 |
| Maksymalna liczba źródeł danych |3 |5 lub 15 |50 |200 |200 |Nie dotyczy |10 |10 |
| Maksymalna umiejętności <sup>4</sup> |3 |5 lub 15 |50 |200 |200 |Nie dotyczy |10 |10 |
| Maksymalne ładowanie indeksowania na wywołanie |10 000 dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Ograniczone tylko przez maksymalną liczbę dokumentów |Nie dotyczy |Bez ograniczeń |Bez ograniczeń |
| Minimalny harmonogram | 5 min |5 min |5 min |5 min |5 min |5 min |5 min | 5 min |
| Maksymalny czas działania| 1-3 minut |24 godziny |24 godziny |24 godziny |24 godziny |Nie dotyczy  |24 godziny |24 godziny |
| Maksymalny czas działania indeksatorów z zestawu umiejętności <sup>5</sup> | 3-10 minut |2 godziny |2 godziny |2 godziny |2 godziny |Nie dotyczy  |2 godziny |2 godziny |
| Indeksator obiektów blob: maksymalny rozmiar obiektu BLOB, MB |16 |16 |128 |256 |256 |Nie dotyczy  |256 |256 |
| Indeksator obiektów blob: Maksymalna liczba znaków zawartości wyodrębnionych z obiektu BLOB |32 000 |64 000 |4 &nbsp; mln |8 &nbsp; mln |16 &nbsp; mln |Nie dotyczy |4 &nbsp; mln |4 &nbsp; mln |

<sup>1</sup> bezpłatne usługi mają maksymalny czas wykonywania indeksatora wynoszący 3 minuty dla źródeł obiektów blob i 1 minuty dla wszystkich innych źródeł danych. W przypadku indeksowania AI, które wywołuje Cognitive Services, bezpłatne usługi są ograniczone do 20 bezpłatnych transakcji dziennie, gdzie transakcja jest definiowana jako dokument, który został pomyślnie przeszedł przez potok wzbogacania.

<sup>2</sup> podstawowe usługi utworzone przed grudnia 2017 mają niższe limity (5 zamiast 15) dla indeksatorów, źródeł danych i umiejętności.

<sup>3</sup> usługi HD S3 nie obejmują obsługi indeksatora.

<sup>4</sup> maksymalnie 30 umiejętności na zestawu umiejętności.

<sup>5</sup> AI wzbogacanie i analiza obrazów są znacznie pracochłonne i zużywają nieproporcjonalne ilości dostępnej mocy obliczeniowej. Czas działania tych obciążeń został skrócony, aby umożliwić uruchamianie innych zadań w kolejce.

> [!NOTE]
> Jak określono w [limitach indeksu](#index-limits), indeksatory będą również wymuszać górny limit 3000 elementów we wszystkich złożonych kolekcjach dla każdego dokumentu, rozpoczynając od najnowszej wersji interfejsu API GA, która obsługuje typy złożone ( `2019-05-06` ). Oznacza to, że jeśli utworzono indeksator z poprzednią wersją interfejsu API, nie będzie to możliwe. Aby zachować maksymalną zgodność, indeksator, który został utworzony przy użyciu wcześniejszej wersji interfejsu API, a następnie zaktualizowany przy użyciu wersji interfejsu API `2019-05-06` lub nowszej, będzie nadal **wykluczony** z limitów. Klienci powinni mieć świadomość niekorzystnego wpływu na bardzo duże złożone kolekcje (jak wspomniano wcześniej) i zdecydowanie zaleca się utworzenie nowych indeksatorów z najnowszą wersją interfejsu API w wersji GA.

## <a name="shared-private-link-resource-limits"></a>Limity zasobów udostępnionego linku prywatnego

Indeksatory mogą uzyskiwać dostęp do innych zasobów platformy Azure [za pośrednictwem prywatnych punktów końcowych](search-indexer-howto-access-private.md) zarządzanych za pośrednictwem [interfejsu API zasobów udostępnionego linku prywatnego](/rest/api/searchmanagement/sharedprivatelinkresources). W tej sekcji opisano limity związane z tą funkcją.

| Zasób | Bezpłatna | Podstawowy | S1 | S2 | S3 | S3 (wysoka gęstość) | L1 | L2
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Wsparcie dla prywatnego indeksatora punktu końcowego | Nie | Tak | Tak | Tak | Tak | Nie | Tak | Tak |
| Obsługa prywatnego punktu końcowego dla indeksatorów z zestawu umiejętności<sup>1</sup> | Nie | Nie | Nie | Tak | Tak | Nie | Tak | Tak |
| Maksymalna liczba prywatnych punktów końcowych | Nie dotyczy | 10 lub 30 | 100 | 400 | 400 | Nie dotyczy | 20 | 20 |
| Maksymalna liczba różnych typów zasobów<sup>2</sup> | Nie dotyczy | 4 | 7 | 15 | 15 | Nie dotyczy | 4 | 4 |

<sup>1</sup> wzbogacanie programu AI i analiza obrazów są intensywnie czasochłonne i zużywają nieproporcjonalne ilości dostępnej mocy obliczeniowej. Z tego powodu połączenia prywatne są wyłączone w niższych warstwach, aby uniknąć niekorzystnego wpływu na wydajność i stabilność usługi wyszukiwania.

<sup>2</sup> liczba różnych typów zasobów jest obliczana jako liczba unikatowych `groupId` wartości używanych w ramach wszystkich udostępnionych zasobów linków prywatnych dla danej usługi wyszukiwania, niezależnie od stanu zasobu.

## <a name="synonym-limits"></a>Limity synonimów

Maksymalna liczba map synonimów jest zależna od warstwy. Każda reguła może mieć do 20 rozszerzeń, w których rozwinięcie jest równoważne. Na przykład podaną "Cat", skojarzenie z "Kitty", "Feline" i "Felis" (rodzaj dla kotów) będzie liczona jako 3 rozszerzenia.

| Zasób | Bezpłatna | Podstawowy | S1 | S2 | S3 | S3 — HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maksymalne mapy synonimów |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maksymalna liczba reguł na mapę |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="data-limits-ai-enrichment"></a>Limity danych (wzbogacanie AI)

[Potok wzbogacenia AI](cognitive-search-concept-intro.md) , który tworzy wywołania do zasobu analiza tekstu na potrzeby [rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md), [wyodrębniania fraz kluczy](cognitive-search-skill-keyphrases.md), [analizy tonacji](cognitive-search-skill-sentiment.md), [wykrywania języka](cognitive-search-skill-language-detection.md)i [wykrywania informacji osobistych](cognitive-search-skill-pii-detection.md) podlega limitom danych. Maksymalny rozmiar rekordu powinien składać się z 50 000 znaków mierzonych przez [`String.Length`](/dotnet/api/system.string.length) . Jeśli musisz podzielić dane przed wysłaniem ich do analizatora tonacji, użyj [umiejętności podziału tekstu](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limity ograniczania

Żądania interfejsu API są ograniczone, ponieważ system zbliża się do pojemności szczytowej. Ograniczanie przepływności działa inaczej dla różnych interfejsów API. Interfejsy API zapytań (wyszukiwanie/sugerowanie/Autouzupełnianie) i indeksowanie interfejsów API indeksowania dynamicznie na podstawie obciążenia usługi. Interfejsy API indeksowania i interfejs API operacji usługi mają limity szybkości żądań statycznych. 

Limity liczby żądań statycznych dla operacji związanych z indeksem:

+ Indeksy list (GET/Indexes): 3 na sekundę na jednostkę wyszukiwania
+ Pobierz indeks (GET/Indexes/MyIndex): 10 na sekundę na jednostkę wyszukiwania
+ Utwórz indeks (POST/Indexes): 12 na minutę na jednostkę wyszukiwania
+ Utwórz lub zaktualizuj indeks (PUT/Indexes/MyIndex): 6 na sekundę na jednostkę wyszukiwania
+ Usuń indeks (DELETE/Indexes/MyIndex): 12 na minutę na jednostkę wyszukiwania 

Limity liczby żądań statycznych dla operacji związanych z usługą:

+ Statystyki usługi (GET/servicestats): 4 na sekundę na jednostkę wyszukiwania

## <a name="api-request-limits"></a>Limity żądań interfejsu API
* Maksymalnie 16 MB na żądanie <sup>1</sup>
* Maksymalna długość adresu URL 8 KB
* Maksymalna 1000 dokumentów na partię operacji przekazywania, scalania lub usuwania indeksu
* Maksymalna 32 pól w klauzuli $orderby
* Maksymalny rozmiar terminu wyszukiwania to 32 766 bajtów (32 KB minus 2 bajty) tekstu zakodowanego w formacie UTF-8

<sup>1</sup> na platformie Azure wyszukiwanie poznawcze treść żądania podlega górnemu limitowi 16 MB, co nakłada praktyczny limit zawartości poszczególnych pól lub kolekcji, które nie są w inny sposób ograniczone przez limity teoretyczne (zobacz [obsługiwane typy danych](/rest/api/searchservice/supported-data-types) , aby uzyskać więcej informacji na temat kompozycji i ograniczeń pól).

## <a name="api-response-limits"></a>Limity odpowiedzi interfejsu API
* Maksymalna 1000 dokumentów zwróconych na stronę wyników wyszukiwania
* Maksymalna 100 sugestii zwróconych na żądanie interfejsu API sugestii

## <a name="api-key-limits"></a>Limity kluczy interfejsu API
Klucze interfejsu API są używane do uwierzytelniania usługi. Są dwa typy analizatorów. Klucze administratora są określone w nagłówku żądania i zapewniają pełny dostęp do odczytu i zapisu do usługi. Klucze zapytań są tylko do odczytu, określone w adresie URL i zazwyczaj są dystrybuowane do aplikacji klienckich.

* Maksymalnie 2 klucze administracyjne na usługę
* Maksymalnie 50 kluczy zapytań na usługę
