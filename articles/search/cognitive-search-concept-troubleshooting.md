---
title: Wskazówki dotyczące projektowania wzbogacenia AI
titleSuffix: Azure Cognitive Search
description: Porady i rozwiązywanie problemów związanych z konfigurowaniem potoków wzbogacania AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 25f0e0f15a299ef8b946b3d5fa0eb3eddc2272c2
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84508624"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Wskazówki dotyczące wzbogacania AI na platformie Azure Wyszukiwanie poznawcze

Ten artykuł zawiera listę porad i wskazówek ułatwiających przechodzenie po rozpoczęciu pracy z funkcjami wzbogacania AI na platformie Azure Wyszukiwanie poznawcze. 

Jeśli jeszcze tego nie zrobiono, [zapoznaj się z samouczkiem: informacje o sposobie wywoływania interfejsów API wzbogacania AI](cognitive-search-quickstart-blob.md) w celu zastosowania wzbogacania rozszerzeń AI do źródła danych obiektów BLOB.

## <a name="tip-1-start-with-a-small-dataset"></a>Porada 1: Rozpoczynanie pracy z małym zestawem danych
Najlepszym sposobem, aby szybko znaleźć problemy, jest zwiększenie szybkości, w której można rozwiązać problemy. Najlepszym sposobem zredukowania czasu indeksowania jest zmniejszenie liczby dokumentów do indeksowania. 

Zacznij od utworzenia źródła danych kilku dokumenty/rekordy. Przykładowy dokument powinien być dobrym reprezentacją różnorodnych dokumentów, które będą indeksowane. 

Uruchom próbkę dokumentu za pomocą kompleksowego potoku i sprawdź, czy wyniki spełniają Twoje potrzeby. Po spełnieniu wyników możesz dodać więcej plików do źródła danych.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Porada 2: Upewnij się, że poświadczenia źródła danych są poprawne
Połączenie ze źródłem danych nie jest sprawdzane do momentu zdefiniowania indeksatora, który go używa. Jeśli zobaczysz jakiekolwiek błędy z informacją, że indeksator nie może uzyskać do danych, upewnij się, że:
- Parametry połączenia są poprawne. Szczególnie podczas tworzenia tokenów SAS upewnij się, że używasz formatu oczekiwanego przez usługę Azure Wyszukiwanie poznawcze. Zobacz [sekcję jak określić poświadczenia](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) , aby dowiedzieć się więcej na temat obsługiwanych formatów.
- Nazwa kontenera w indeksatorze jest poprawna.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Porada 3: Zobacz, co działa nawet w przypadku wystąpienia niektórych błędów
Czasami niewielka awaria powoduje zatrzymanie indeksatora w jego ścieżkach. Jest to konieczne, jeśli planujesz rozwiązać problemy po jednym z nich. Jednak może być konieczne ignorowanie określonego typu błędu, dzięki czemu indeksator może kontynuować, aby zobaczyć, jakie przepływy faktycznie działają.

W takim przypadku możesz chcieć poinstruować indeksator, aby ignorował błędy. W tym celu należy ustawić *maxFailedItems* i *maxFailedItemsPerBatch* jako-1 jako część definicji indeksatora.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Porada 4: spojrzenie na wzbogacone dokumenty pod okapem 
Wzbogacone dokumenty są tymczasowymi strukturami utworzonymi podczas wzbogacania, a następnie usuwane po zakończeniu przetwarzania.

Aby przechwycić migawkę wzbogaconego dokumentu utworzoną podczas indeksowania, dodaj pole o nazwie ```enriched``` do indeksu. Indeksator automatycznie zrzuca do tego pola ciąg będący reprezentacją wszystkich wzbogaceń dokumentu.

Pole ```enriched``` będzie zawierać ciąg, który jest logiczną reprezentacją wzbogaconego dokumentu w pamięci w formacie JSON.  Wartość pola jest jednak prawidłowym dokumentem w formacie JSON. Cudzysłowy są umieszczane w sekwencji ucieczki, więc musisz zastąpić ciąg `\"` znakiem `"`, aby wyświetlić dokument w postaci sformatowanego kodu JSON. 

Wzbogacone pole jest przeznaczone tylko do celów debugowania, aby ułatwić zrozumienie kształtu logicznego zawartości, względem której są oceniane wyrażenia. Nie należy zależeć od tego pola do indeksowania.

Dodaj ```enriched``` pole jako część definicji indeksu na potrzeby debugowania:

#### <a name="request-body-syntax"></a>Składnia treści żądania
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

### <a name="debug-sessions"></a>Sesje debugowania

Sesje debugowania to edytor wizualny, który współpracuje z istniejącym zestawu umiejętności w Azure Portal. W ramach sesji debugowania można identyfikować i rozwiązywać błędy, sprawdzać poprawność zmian i wypchnąć zmiany do zestawu umiejętności produkcyjnego w potoku wzbogacenia AI. Jest to funkcja w wersji zapoznawczej, a dostęp jest udzielany na zasadzie wielkości liter. [Zapoznaj się z dokumentacją](https://docs.microsoft.com/azure/search/cognitive-search-debug-session) i Dowiedz się, jak to zrobić, aby uzyskać dostęp.

## <a name="tip-5-expected-content-fails-to-appear"></a>Porada 5: nie można wyświetlić oczekiwanej zawartości

Brakująca zawartość może być wynikiem porzucenia dokumentów podczas indeksowania. Warstwy Bezpłatna i podstawowa mają niskie limity rozmiaru dokumentu. Każdy plik przekraczający limit jest usuwany podczas indeksowania. Możesz sprawdzić porzucone dokumenty w Azure Portal. Na pulpicie nawigacyjnym usługi wyszukiwania kliknij dwukrotnie kafelek indeksatory. Sprawdź stosunek pomyślnych dokumentów indeksowanych. Jeśli wartość nie jest równa 100%, możesz kliknąć współczynnik, aby uzyskać więcej szczegółów. 

Jeśli problem dotyczy rozmiaru pliku, może zostać wyświetlony następujący błąd: "obiekt BLOB \<file-name> " ma rozmiar \<file-size> bajtów, który przekracza maksymalny rozmiar wyodrębniania dokumentu dla bieżącej warstwy usług ". Aby uzyskać więcej informacji na temat limitów indeksatora, zobacz [limity usługi](search-limits-quotas-capacity.md).

Druga przyczyna niepowodzenia wyświetlania zawartości może być związana z błędami mapowania operacji wejścia/wyjścia. Na przykład nazwa elementu docelowego danych wyjściowych to "ludzie", ale nazwa pola indeksu jest małymi literami "ludzie". System może zwrócić komunikaty o powodzeniu 201 dla całego potoku, aby podejrzewać, że indeksowanie powiedzie się, gdy w rzeczywistości pole jest puste. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Porada 6: rozszerzona przetwarzanie poza maksymalnym czasem wykonywania (okno 24-godzinne)

Analiza obrazów jest intensywnie czasochłonna dla nawet prostych przypadków, dlatego w przypadku, gdy obrazy są szczególnie duże lub złożone, czasy przetwarzania mogą przekroczyć maksymalny dozwolony czas. 

Maksymalny czas działania zależy od warstwy: kilka minut w warstwie Bezpłatna, indeksowanie 24-godzinne w warstwach płatnych. Jeśli przetwarzanie zakończy się niepowodzeniem w ciągu 24 godzin dla przetwarzania na żądanie, przejdź do harmonogramu, aby indeksator mógł pobrać, gdzie został pozostawiony. 

W przypadku zaplanowanych indeksatorów indeksowanie zostaje wznowione zgodnie z harmonogramem w ostatnim znanym dobrym dokumencie. Korzystając z harmonogramu cyklicznego, indeksator może wykonać swój sposób przez zaległości obrazu w ciągu kilku godzin lub dni, do momentu przetworzenia wszystkich nieprzetwarzanych obrazów. Aby uzyskać więcej informacji na temat składni harmonogramu, zobacz [krok 3. Tworzenie — indeksator](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) lub zapoznaj [się z tematem planowanie indeksatorów dla usługi Azure wyszukiwanie poznawcze](search-howto-schedule-indexers.md).

> [!NOTE]
> Jeśli indeksator jest ustawiony na określony harmonogram, ale wielokrotnie powtarza się w tym samym dokumencie za każdym razem, gdy zostanie on uruchomiony, indeksator zacznie działać w krótszym interwale (maksymalnie co 24 godziny) do momentu pomyślnego przekroczenia postępu.  Jeśli uważasz, że Rozwiązano problem, który spowodował zablokowanie indeksatora w określonym punkcie, można wykonać uruchomienie na żądanie indeksatora, a jeśli ten proces pomyślnie przejdzie, indeks ponownie powróci do jego ustawionego interwału harmonogramu.

W przypadku indeksowania opartego na portalu (zgodnie z opisem w przewodniku Szybki Start) wybranie opcji indeksatora "Uruchom raz" ogranicza przetwarzanie do 1 godziny ( `"maxRunTime": "PT1H"` ). Możesz chcieć wydłużyć przedział czasu przetwarzania.

## <a name="tip-7-increase-indexing-throughput"></a>Porada 7: zwiększenie przepływności indeksowania

W przypadku [indeksowania równoległego](search-howto-large-index.md)należy umieścić dane w wielu kontenerach lub w wielu folderach wirtualnych w tym samym kontenerze. Następnie Utwórz wiele par DataSource i indeksatora. Wszystkie indeksatory mogą używać tego samego zestawu umiejętności i zapisywać w tym samym docelowym indeksie wyszukiwania, dzięki czemu aplikacja wyszukiwania nie musi znać tego partycjonowania.
Aby uzyskać więcej informacji, zobacz [indeksowanie dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Zobacz także
+ [Szybki Start: Tworzenie potoku wzbogacenia AI w portalu](cognitive-search-quickstart-blob.md)
+ [Samouczek: informacje o interfejsach API REST wzbogacania AI](cognitive-search-tutorial-blob.md)
+ [Określanie poświadczeń źródła danych](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Indeksowanie dużych zestawów danych](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak zmapować wzbogacone pola na indeks](cognitive-search-output-field-mapping.md)
