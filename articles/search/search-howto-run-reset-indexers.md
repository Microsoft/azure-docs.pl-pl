---
title: Uruchom lub zresetuj indeksatory
titleSuffix: Azure Cognitive Search
description: Zresetuj indeksator, umiejętności lub pojedyncze dokumenty, aby odświeżyć wszystko lub część oraz indeks lub magazyn informacji.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: bf8a4e51e23f438265af706914a6bc73ec30f64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667667"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Jak uruchamiać lub resetować indeksatory, umiejętności lub dokumenty

Wykonywanie indeksatora może wystąpić podczas pierwszego utworzenia [indeksatora](search-indexer-overview.md), podczas uruchamiania indeksatora na żądanie lub podczas ustawiania indeksatora zgodnie z harmonogramem. Po uruchomieniu początkowym indeksator śledzi, które dokumenty wyszukiwania są indeksowane za pomocą wewnętrznego "znaku wodnego". Znacznik nigdy nie jest ujawniany w interfejsie API, ale wewnętrznie indeksator wie, że indeksowanie zostało zatrzymane, aby można było je wycofać w następnym uruchomieniu.

Możesz wyczyścić znacznik limitu, ustawiając indeksator, jeśli chcesz przetworzyć go od podstaw. Resetowanie interfejsów API jest możliwe po zmniejszeniu poziomów w hierarchii obiektów:

+ Całe korpus wyszukiwania (Użyj [indeksatorów resetowania](#reset-indexers))
+ Określony dokument lub lista dokumentów (Użyj [resetowania dokumentów — wersja zapoznawcza](#reset-docs))
+ Konkretna umiejętność lub wzbogacanie w dokumencie (Użyj [resetowania umiejętności — wersja zapoznawcza](#reset-skills))

Resetowanie interfejsów API służy do odświeżania zawartości w pamięci podręcznej (stosowanej w scenariuszach [wzbogacania AI](cognitive-search-concept-intro.md) ) lub czyszczenia górnego znacznika i ponownego kompilowania indeksu.

Resetowanie, po którym następuje uruchomienie, może przetwarzać istniejące dokumenty i nowe dokumenty, ale nie usuwa oddzielonych dokumentów wyszukiwania w indeksie wyszukiwania, który został utworzony w poprzednich uruchomieniach. Aby uzyskać więcej informacji na temat usuwania, zobacz [Dodawanie, aktualizowanie lub usuwanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="run-indexers"></a>Uruchom indeksatory

[Utwórz indeksator](/rest/api/searchservice/create-indexer) tworzy i uruchamia indeksator, chyba że zostanie utworzony w stanie wyłączone ("wyłączone": true). Pierwsze uruchomienie trwa dłużej, ponieważ jego obiekt obejmują również jego tworzenie.

[Uruchom indeksator](/rest/api/searchservice/run-indexer) wykryje i przetworzy tylko te informacje, które są niezbędne do synchronizowania indeksu wyszukiwania ze źródłem danych. Magazyn obiektów BLOB ma wbudowaną funkcję wykrywania zmian. Inne źródła danych, takie jak Azure SQL lub Cosmos DB, muszą zostać skonfigurowane pod kątem wykrywania zmian, zanim indeksator będzie mógł odczytać tylko nowe i zaktualizowane wiersze.

Indeksator można uruchomić przy użyciu dowolnego z następujących metod:

+ Azure Portal przy użyciu polecenia **Run** na stronie indeksatora
+ [Uruchom indeksator (REST)](/rest/api/searchservice/run-indexer)
+ [Metoda RunIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) w zestawie Azure .NET SDK (lub użycie równoważnej metody RunIndexer w innym zestawie SDK)

Wykonanie indeksatora podlega następującym ograniczeniom:

+ Maksymalna liczba zadań indeksatora to 1 na replikę, brak współbieżnych zadań.

  Jeśli wykonywanie indeksatora jest już w wydajności, otrzymasz następujące powiadomienie: "nie można uruchomić indeksatora" <indeksatora-Name> ", błąd:" inne wywołanie indeksatora jest obecnie w toku; współbieżne wywołania są niedozwolone. "

+ Maksymalny czas działania wynosi 2 godziny, jeśli jest używany zestawu umiejętności i 24 godziny bez. 

  Przetwarzanie można rozciągnąć przez umieszczenie indeksatora zgodnie z harmonogramem. Warstwa Bezpłatna ma niższe limity czasu wykonywania. Aby uzyskać pełną listę, zobacz [limity indeksatora](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Zresetuj indeksator

Resetowanie indeksatora jest obejmujące wszystkie. W indeksie wyszukiwania każdy dokument wyszukiwania, który został pierwotnie wypełniony przez indeksator, jest oznaczony do pełnego przetwarzania. Wszystkie nowe dokumenty znalazły bazowe Źródło zostaną dodane do indeksu jako dokumenty wyszukiwania. Jeśli indeksator jest skonfigurowany do korzystania z zestawu umiejętności i [buforowania](search-howto-incremental-index.md), zestawu umiejętności jest ponownie uruchamiany, a pamięć podręczna jest odświeżana.

Indeksator można zresetować przy użyciu dowolnego z tych metod, a następnie indeksatora uruchamianego przy użyciu jednej z metod omówionych powyżej.

+ Azure Portal, za pomocą polecenia **Reset** na stronie indeksatora
+ [Zresetuj indeksator (REST)](/rest/api/searchservice/reset-indexer)
+ [Metoda ResetIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) w zestawie Azure .NET SDK (lub użycie równoważnej metody RunIndexer w innym zestawie SDK)

Flaga resetowania jest czyszczona po zakończeniu przebiegu. Każda logika automatycznego wykrywania zmian, która jest działająca dla źródła danych, zostanie wznowiona podczas następnego uruchomienia, a wszystkie inne nowe lub zaktualizowane wartości są pozostały w pozostałej części zestawu danych.

> [!NOTE]
> Żądanie resetowania określa, co jest przetwarzane ponownie (indeksator, umiejętność lub dokument), ale nie wpływa na zachowanie w czasie wykonywania indeksatora. Jeśli indeksator ma parametry czasu wykonywania, mapowania pól, buforowanie, opcje wsadowe i tak dalej, te ustawienia są stosowane po uruchomieniu indeksatora po jego zresetowaniu.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Resetuj umiejętności (wersja zapoznawcza)

> [!IMPORTANT] 
> [Resetowanie umiejętności](/rest/api/searchservice/preview-api/reset-skills) jest w publicznej wersji zapoznawczej, dostępne tylko za pomocą interfejsu API REST w wersji zapoznawczej. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W przypadku indeksatorów, które mają umiejętności, można resetować konkretne umiejętności, aby wymusić przetwarzanie tej umiejętności i wszelkich umiejętności podrzędnych, które są zależne od danych wyjściowych. W [pamięci podręcznej wzbogacania](search-howto-incremental-index.md) są również odświeżane. Resetowanie umiejętności unieważnia wyniki w pamięci podręcznej, co jest przydatne, gdy zostanie wdrożona nowa wersja umiejętności, a indeksator ma ponownie uruchamiać tę umiejętność dla wszystkich dokumentów. 

[Resetowanie umiejętności](/rest/api/searchservice/preview-api/reset-skills) jest możliwe za POOREDNICTWEM protokołu REST **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Możesz określić indywidualne umiejętności, jak wskazano w powyższym przykładzie, ale jeśli którekolwiek z tych umiejętności wymagają danych wyjściowych z nieznajdujących się na liście umiejętności (#2 przez #4), będą uruchamiane nieznajdujące się na liście umiejętności, chyba że pamięć podręczna będzie zawierać niezbędne informacje. Aby to było prawdziwe, buforowanie w pamięci podręcznej dla umiejętności #2 przez #4 nie może być zależne od #1 (wymienione na potrzeby resetowania).

Jeśli nie określono żadnych umiejętności, cały zestawu umiejętności jest wykonywany i Jeśli buforowanie jest włączone, pamięć podręczna jest również odświeżana.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Resetuj dokumenty (wersja zapoznawcza)

> [!IMPORTANT] 
> [Resetowanie dokumentów](/rest/api/searchservice/preview-api/reset-documents) jest w publicznej wersji zapoznawczej, która jest dostępna tylko w wersji zapoznawczej interfejsu API REST. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Interfejs API resetowania dokumentów](/rest/api/searchservice/preview-api/reset-documents) akceptuje listę kluczy dokumentów, dzięki czemu można odświeżać określone dokumenty. Jeśli ta wartość jest określona, parametry resetowania staną się jedynym wyznacznikami przetworzonych, niezależnie od innych zmian w danych źródłowych. Na przykład w przypadku dodania lub zaktualizowania 20 obiektów BLOB od momentu ostatniego uruchomienia indeksatora, ale tylko jeden dokument zostanie zresetowany tylko wtedy, gdy tylko jeden dokument zostanie przetworzony.

Dla poszczególnych dokumentów wszystkie pola w tym dokumencie wyszukiwania są odświeżane przy użyciu wartości ze źródła danych. Nie można wybierać i wybierać pól do odświeżenia. 

Jeśli dokument jest wzbogacony przez zestawu umiejętności i zawiera dane w pamięci podręcznej, zestawu umiejętności jest wywoływana dla tylko określonych dokumentów, a w pamięci podręcznej zostanie zaktualizowane dla przetworzonych dokumentów.

Podczas testowania tego interfejsu API po raz pierwszy następujące interfejsy API ułatwią sprawdzenie i przetestowanie zachowań:

+ [Uzyskaj stan indeksatora](/rest/api/searchservice/get-indexer-status) z wersją interfejsu API `2020-06-30-Preview` , aby sprawdzić stan resetowania i stan wykonania. Informacje na temat żądania resetowania można znaleźć na końcu odpowiedzi o stanie.
+ [Zresetuj dokumenty](/rest/api/searchservice/preview-api/reset-documents) z wersją interfejsu API `2020-06-30-Preview` , aby określić, które dokumenty należy przetworzyć.
+ [Uruchom indeksator](/rest/api/searchservice/run-indexer) , aby uruchomić indeksator (dowolna wersja interfejsu API).
+ [Wyszukaj w dokumentach](/rest/api/searchservice/search-documents) sprawdzanie zaktualizowanych wartości, a także Zwróć klucze dokumentów, jeśli nie masz pewności, jaka jest wartość. Użyj, `"select": "<field names>"` Jeśli chcesz ograniczyć, które pola są wyświetlane w odpowiedzi.

### <a name="formulate-and-send-the-reset-request"></a>Formułowanie i wysyłanie żądania resetowania

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

Klucze dokumentów podane w żądaniu są wartościami z indeksu wyszukiwania, które mogą się różnić od odpowiednich pól w źródle danych. Jeśli nie masz pewności co do wartości klucza, [Wyślij zapytanie](search-query-create.md) w celu zwrócenia wartości. Możesz użyć, `select` Aby zwrócić tylko pole klucza dokumentu.

W przypadku obiektów blob, które są analizowane w wielu dokumentach wyszukiwania (na przykład jeśli użyto [jsonLines lub jsonArrays](search-howto-index-json-blobs.md), lub [delimitedText](search-howto-index-csv-blobs.md)) jako tryb analizy, klucz dokumentu jest generowany przez indeksator i może być nieznany dla użytkownika. W takiej sytuacji zapytanie dla klucza dokumentu będzie w podającej poprawnej wartości.

Wielokrotne wywoływanie interfejsu API przy użyciu różnych kluczy dołącza nowe klucze do listy kluczy dokumentów. Wywołanie interfejsu API z **`overwrite`** parametrem ustawionym na wartość true spowoduje zastąpienie bieżącej listy kluczy dokumentu do zresetowania przy użyciu ładunku żądania:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Sprawdź stan resetowania

Aby sprawdzić stan resetowania i zobaczyć, które klucze dokumentów są umieszczane w kolejce do przetworzenia, użyj opcji [Pobierz indeksator stanu](/rest/api/searchservice/get-indexer-status) z **`api-version=06-30-2020-Preview`** . Interfejs API w wersji zapoznawczej zwróci **`currentState`** sekcję, którą można znaleźć na końcu odpowiedzi dotyczącej stanu indeksatora.

Tryb "Mode" będzie **`indexingAllDocs`** przeznaczony do resetowania umiejętności (ponieważ mogą to być na przykład wszystkie dokumenty, które są wypełniane za pomocą wzbogacania AI).

W przypadku resetowania dokumentów tryb jest ustawiany na **`indexingResetDocs`** . Indeksator zachowuje ten stan do momentu, gdy wszystkie klucze dokumentu podane w wywołaniu resetowania dokumentów są przetwarzane i żadne inne zadania indeksatora nie będą wykonywane, gdy operacja jest w toku. Znalezienie wszystkich dokumentów na liście kluczy dokumentu wymaga złamania każdego dokumentu w celu zlokalizowania i dopasowania klucza. może to potrwać, jeśli zestaw danych jest duży. Jeśli kontener obiektów BLOB zawiera setki obiektów blob, a dokumenty, które chcesz zresetować, znajdują się na końcu, indeksator nie znajdzie pasujących obiektów blob, dopóki wszystkie inne osoby nie zostaną wcześniej zaznaczone.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Po ponownym przetworzonym dokumencie indeksator powróci do **`indexingAllDocs`** trybu i przetworzy wszystkie inne nowe lub zaktualizowane dokumenty przy następnym uruchomieniu.

## <a name="next-steps"></a>Następne kroki

Zresetuj interfejsy API służą do informowania zakresu następnego uruchomienia indeksatora. W przypadku rzeczywistego przetwarzania należy wywołać indeksator na żądanie lub zezwolić na zaplanowane zadanie, aby zakończyć pracę. Po zakończeniu przebiegu indeksator powraca do normalnego przetwarzania, niezależnie od tego, czy jest on oparty na harmonogramie czy w przetwarzaniu na żądanie.

Po zresetowaniu i ponownym uruchomieniu zadań indeksatora można monitorować stan usługi wyszukiwania lub uzyskać szczegółowe informacje za pośrednictwem rejestrowania diagnostycznego.

+ [Operacje indeksatora (REST)](/rest/api/searchservice/indexer-operations)
+ [Monitorowanie stanu indeksatora wyszukiwania](search-howto-monitor-indexers.md)
+ [Zbieranie i analizowanie danych dziennika](search-monitor-logs.md)
+ [Zaplanuj indeksator](search-howto-schedule-indexers.md)