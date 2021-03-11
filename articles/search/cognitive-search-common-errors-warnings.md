---
title: Błędy i ostrzeżenia indeksatora
titleSuffix: Azure Cognitive Search
description: Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas wzbogacania danych AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3ba0abe8510291351c10ba085ba7e42b8197d886
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553242"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Rozwiązywanie problemów z typowymi błędami indeksatora i ostrzeżeniami w usłudze Azure Wyszukiwanie poznawcze

Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas indeksowania i wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.

Indeksowanie jest przerywane, gdy licznik błędów przekracza wartość ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Jeśli chcesz, aby indeksatory ignorował te błędy (i pominąć "dokumenty nieudane"), rozważ zaktualizowanie `maxFailedItems` i `maxFailedItemsPerBatch` zgodnie z opisem w [tym miejscu](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Każdy uszkodzony dokument wraz z jego kluczem dokumentu (jeśli jest dostępny) będzie wyświetlany jako błąd w stanie wykonywania indeksatora. Możesz użyć [interfejsu API indeksu](/rest/api/searchservice/addupdate-or-delete-documents) , aby ręcznie przekazać dokumenty w późniejszym momencie, jeśli ustawiono indeksator, aby tolerował błędy.

Informacje o błędzie w tym artykule mogą pomóc w rozwiązaniu błędów, co umożliwia kontynuowanie indeksowania.

Ostrzeżenia nie zatrzymują indeksowania, ale wskazują warunki, które mogą spowodować nieoczekiwane wyniki. Niezależnie od tego, czy podejmujesz akcję, czy nie zależą od danych i Twojego scenariusza.

Począwszy od wersji interfejsu API `2019-05-06` , błędy indeksatora na poziomie elementu i ostrzeżenia są uporządkowane w celu zapewnienia większej przejrzystości wokół przyczyn i następnych kroków. Zawierają one następujące właściwości:

| Właściwość | Opis | Przykład |
| --- | --- | --- |
| key | Identyfikator dokumentu dokumentu, którego dotyczy błąd lub ostrzeżenie. | https: \/ /coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | Nazwa operacji opisująca miejsce wystąpienia błędu lub ostrzeżenia. Ta wartość jest generowana przez następującą strukturę: [Kategoria]. [Podkategoria]. [ResourceType]. Source | DocumentExtraction. azureblob. myBlobContainerName wzbogacanie. WebApiSkill. Moja umiejętność projekcji. SearchIndex. OutputFieldMapping. myOutputFieldName projekcji. SearchIndex. MergeOrUpload. Indeksname. KnowledgeStore. Table. webtablename |
| message | Ogólny opis błędu lub ostrzeżenia. | Nie można wykonać umiejętności, ponieważ żądanie interfejsu API sieci Web nie powiodło się. |
| uzyskać | Wszelkie dodatkowe szczegóły, które mogą być pomocne w diagnozowaniu problemu, takie jak odpowiedź WebApi w przypadku niepowodzenia wykonywania niestandardowej umiejętności. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 Źródło, Func `2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` ... Pozostałe ślady stosu... |
| documentationLink | Link do odpowiedniej dokumentacji ze szczegółowymi informacjami na temat debugowania i rozwiązywania problemu. Ten link będzie często wskazywał jedną z poniższych sekcji na tej stronie. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>Błąd: nie można odczytać dokumentu

Indeksator nie mógł odczytać dokumentu ze źródła danych. Przyczyną może być:

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Niespójne typy pól w różnych dokumentach | "Typ wartości jest niezgodny z typem kolumny. Nie można przechowywać `'{47.6,-122.1}'` w kolumnie autorów.  Oczekiwany typ to JArray ".  "Błąd podczas konwertowania typu danych nvarchar na float".  "Konwersja nie powiodła się podczas konwersji wartości nvarchar" 12 miesięcy "na typ danych int."  "Błąd przepełnienia arytmetycznego konwersji wyrażenia na typ danych int." | Upewnij się, że typ każdego pola jest taki sam w różnych dokumentach. Na przykład jeśli pierwsze `'startTime'` pole dokumentu ma wartość DateTime, a w drugim dokumencie jest ciągiem, ten błąd zostanie trafiony. |
| błędy usługi źródłowej źródła danych | (z Cosmos DB) `{"Errors":["Request rate is large"]}` | Sprawdź wystąpienie magazynu, aby upewnić się, że jest w dobrej kondycji. Może być konieczne dostosowanie skalowania/partycjonowania. |
| problemy przejściowe | Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. (Dostawca: Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Błąd: nie można wyodrębnić zawartości lub metadanych z dokumentu
Indeksator ze źródłem danych obiektu BLOB nie mógł wyodrębnić zawartości lub metadanych z dokumentu (na przykład pliku PDF). Przyczyną może być:

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| rozmiar obiektu BLOB przekracza limit. | Dokument jest `'150441598'` bajtów, który przekracza maksymalny rozmiar `'134217728'` bajtów na potrzeby wyodrębniania dokumentów dla bieżącej warstwy usług. | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| Obiekt BLOB ma nieobsługiwany typ zawartości | Dokument zawiera nieobsługiwany typ zawartości `'image/png'` | [Błędy indeksowania obiektów BLOB](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| Obiekt BLOB jest zaszyfrowany | Nie można przetworzyć dokumentu — może on być zaszyfrowany lub chroniony hasłem. | Możesz pominąć obiekt BLOB za pomocą [ustawień obiektu BLOB](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex). |
| problemy przejściowe | "Błąd przetwarzania obiektu BLOB: żądanie zostało przerwane: żądanie zostało anulowane." "Dokument przekroczył limit czasu podczas przetwarzania". | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>Błąd: nie można przeanalizować dokumentu
Indeksator odczytuje dokument ze źródła danych, ale wystąpił problem podczas konwertowania zawartości dokumentu do określonego schematu mapowania pól. Przyczyną może być:

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Brak klucza dokumentu | Brak klucza dokumentu lub jest on pusty | Upewnij się, że wszystkie dokumenty mają prawidłowe klucze dokumentu. Klucz dokumentu jest określany przez ustawienie właściwości "Key" jako części [definicji indeksu](/rest/api/searchservice/create-index#request-body). Indeksatory będą emitować ten błąd, jeśli właściwość oflagowana jako "Key" nie zostanie znaleziona w określonym dokumencie. |
| Klucz dokumentu jest nieprawidłowy | Klucz dokumentu nie może mieć więcej niż 1024 znaków | Zmodyfikuj klucz dokumentu, aby spełniał wymagania dotyczące weryfikacji. |
| Nie można zastosować mapowania pola do pola | Nie można zastosować funkcji mapowania `'functionName'` do pola `'fieldName'` . Tablica nie może mieć wartości null. Nazwa parametru: bajty | Dokładnie sprawdź [mapowania pól](search-indexer-field-mappings.md) zdefiniowane w indeksatorze i porównaj z danymi określonego pola dokumentu, który się nie powiódł. Może być konieczne zmodyfikowanie mapowań pól lub danych dokumentu. |
| Nie można odczytać wartości pola | Nie można odczytać wartości kolumny `'fieldName'` w indeksie `'fieldIndex'` . Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera. (Dostawca: Dostawca TCP, błąd: 0 — istniejące połączenie zostało wymuszone przez hosta zdalnego). | Te błędy są zwykle spowodowane nieoczekiwanymi problemami z łącznością z usługą źródłową źródła danych. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>Błąd: nie można zmapować pola wyjściowego " `xyz` " na indeks wyszukiwania z powodu problemu deserializacji podczas stosowania funkcji mapowania " `abc` "
Mapowanie danych wyjściowych mogło się nie powieść, ponieważ dane wyjściowe są w niewłaściwym formacie dla używanej funkcji mapowania. Na przykład zastosowanie funkcji mapowania Base64Encode na danych binarnych spowoduje wygenerowanie tego błędu. Aby rozwiązać ten problem, należy ponownie uruchomić indeksator bez określania funkcji mapowania lub upewnić się, że funkcja mapowania jest zgodna z typem danych wyjściowych. Szczegóły można znaleźć w temacie [Mapowanie pola danych wyjściowych](cognitive-search-output-field-mapping.md) .

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>Błąd: nie można wykonać umiejętności
Indeksator nie mógł uruchomić umiejętności w zestawu umiejętności.

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Przejściowe problemy z łącznością | Wystąpił błąd przejściowy. Spróbuj ponownie później. | Sporadycznie występują nieoczekiwane problemy z łącznością. Spróbuj ponownie uruchomić dokument za pomocą indeksatora później. |
| Potencjalna usterka produktu | Wystąpił nieoczekiwany błąd. | Oznacza to nieznaną klasę błędu i może oznaczać, że występuje usterka produktu. Zapoznaj się z [biletem pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc. |
| Umiejętność napotkała błąd podczas wykonywania | (Z poziomu umiejętności scalania) Co najmniej jedna wartość przesunięcia była nieprawidłowa i nie można jej przeanalizować. Wstawiono elementy na końcu tekstu | Aby rozwiązać ten problem, użyj informacji w komunikacie o błędzie. Ten rodzaj błędu będzie wymagał działania do rozwiązania. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Błąd: nie można wykonać umiejętności, ponieważ żądanie internetowego interfejsu API nie powiodło się
Wykonanie kwalifikacji nie powiodło się, ponieważ wywołanie interfejsu API sieci Web nie powiodło się. Zazwyczaj ta klasa awarii występuje, gdy są używane niestandardowe umiejętności, w takim przypadku konieczne będzie debugowanie niestandardowego kodu w celu rozwiązania problemu. Jeśli zamiast tego błąd pochodzi z wbudowanej umiejętności, zapoznaj się z komunikatem o błędzie, aby uzyskać pomoc w rozwiązywaniu problemu.

Podczas debugowania tego problemu należy zwrócić uwagę na wszelkie [ostrzeżenia dotyczące danych wejściowych](#warning-skill-input-was-invalid) dotyczących umiejętności. Punkt końcowy interfejsu API sieci Web może się nie powieść, ponieważ indeksator przekazuje nieoczekiwane dane wejściowe.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Błąd: nie można wykonać umiejętności, ponieważ odpowiedź dotycząca umiejętności interfejsu API sieci Web jest nieprawidłowa
Wykonanie kwalifikacji nie powiodło się, ponieważ wywołanie interfejsu API sieci Web zwróciło nieprawidłową odpowiedź. Zazwyczaj ta klasa awarii występuje, gdy są używane niestandardowe umiejętności, w takim przypadku konieczne będzie debugowanie niestandardowego kodu w celu rozwiązania problemu. Jeśli zamiast tego niepowodzenie pochodzi z wbudowanej umiejętności, Utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc.

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Błąd: umiejętność nie została wykonana w ramach limitu czasu
Istnieją dwa przypadki, w których może wystąpić ten komunikat o błędzie, z których każdy powinien być traktowany inaczej. Postępuj zgodnie z poniższymi instrukcjami, w zależności od tego, jaką umiejętność zwróciła ten błąd.

### <a name="built-in-cognitive-service-skills"></a>Wbudowane umiejętności usługi poznawczej
Wiele wbudowanych umiejętności poznawczych, takich jak wykrywanie języka, rozpoznawanie jednostek lub OCR, są obsługiwane przez punkt końcowy interfejsu API usługi poznawczej. Czasami występują przejściowe problemy z tymi punktami końcowymi i upłynął limit czasu żądania. W przypadku problemów przejściowych nie ma żadnych naprawienia, z wyjątkiem oczekiwania i spróbuj ponownie. Jako środek zaradczy należy rozważyć skonfigurowanie indeksatora do [uruchamiania zgodnie z harmonogramem](search-howto-schedule-indexers.md). Zaplanowane indeksowanie jest odbierane w miejscu, w którym zostało pozostawione. Przy założeniu, że przejściowe problemy są rozwiązywane, indeksowanie i przetwarzanie umiejętności poznawcze powinny mieć możliwość kontynuowania przy następnym zaplanowanym uruchomieniu.

Jeśli ten błąd będzie nadal występować w tym samym dokumencie dla wbudowanej umiejętności poznawczej, Utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby uzyskać pomoc, ponieważ nie jest to oczekiwane.

### <a name="custom-skills"></a>Umiejętności niestandardowe
Jeśli wystąpi błąd przekroczenia limitu czasu z utworzoną niestandardową umiejętnością, istnieje kilka rzeczy, które można wypróbować. Najpierw przejrzyj swoją niestandardową umiejętność i upewnij się, że nie jest ona zablokowana w pętli nieskończonej i że zwraca wynik konsekwentnie. Po potwierdzeniu, że jest to przypadek, ustal, jaki jest czas wykonywania danej umiejętności. Jeśli nie ustawiono jawnie `timeout` wartości w niestandardowej definicji umiejętności, wartość domyślna `timeout` to 30 sekund. Jeśli 30 sekund nie jest wystarczająco długi, aby można było wykonać swoją umiejętność, możesz określić wyższą `timeout` wartość w niestandardowej definicji umiejętności. Oto przykład niestandardowej definicji umiejętności, w której limit czasu jest ustawiony na 90 sekund:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

Maksymalna wartość, którą można ustawić dla `timeout` parametru to 230 sekund.  Jeśli niestandardowa umiejętność nie może zostać wykonana spójnie w ciągu 230 sekund, możesz rozważyć zmniejszenie umiejętności niestandardowych, aby było możliwe `batchSize` Przetwarzanie mniejszej liczby dokumentów w ramach jednego wykonania.  Jeśli ustawiono już wartość `batchSize` 1, należy ponownie napisać umiejętność, aby można było wykonać ją w mniej niż 230 sekund lub w inny sposób podzielić ją na wiele umiejętności niestandardowych, tak aby czas wykonywania dla każdej pojedynczej umiejętności niestandardowej wynosił maksymalnie 230 sekund. Zapoznaj się z dokumentacją dotyczącą [niestandardowych umiejętności](cognitive-search-custom-skill-web-api.md) , aby uzyskać więcej informacji.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Błąd: nie można " `MergeOrUpload` " | `Delete`dokument "" w indeksie wyszukiwania

Dokument został odczytany i przetworzony, ale indeksator nie mógł go dodać do indeksu wyszukiwania. Przyczyną może być:

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Pole zawiera termin, który jest zbyt duży | Termin w dokumencie jest większy niż [limit 32 KB](search-limits-quotas-capacity.md#api-request-limits) | Można uniknąć tego ograniczenia, upewniając się, że pole nie jest skonfigurowane jako możliwe do filtrowania, tworzenia i sortowania.
| Dokument jest zbyt duży, aby można go było zindeksować | Dokument jest większy niż [Maksymalny rozmiar żądania interfejsu API](search-limits-quotas-capacity.md#api-request-limits) | [Jak indeksować duże zestawy danych](search-howto-large-index.md)
| Dokument zawiera zbyt wiele obiektów w kolekcji | Kolekcja w dokumencie przekracza [maksymalną liczbę elementów we wszystkich złożonych kolekcjach limitów](search-limits-quotas-capacity.md#index-limits) "dokument z kluczem `'1000052'` ma `'4303'` obiekty w kolekcjach (tablice JSON). W większości `'3000'` obiektów mogą znajdować się kolekcje w całym dokumencie. Usuń obiekty z kolekcji i spróbuj ponownie wykonać indeksowanie dokumentu ". | Zalecamy zmniejszenie rozmiaru kolekcji złożonej w dokumencie do wartości poniżej limitu i uniknięcie dużego użycia magazynu.
| Problem z nawiązaniem połączenia z docelowym indeksem (który utrzymuje się po ponownych próbach), ponieważ usługa jest w innym załadowaniu, na przykład w przypadku wykonywania zapytań lub indeksowania. | Nie można ustanowić połączenia w celu zaktualizowania indeksu. Usługa wyszukiwania jest w dużym obciążeniu. | [Skalowanie w górę usługi wyszukiwania](search-capacity-planning.md)
| Trwa poprawianie usługi wyszukiwania w ramach aktualizacji usługi lub jest ona w trakcie ponownej konfiguracji topologii. | Nie można ustanowić połączenia w celu zaktualizowania indeksu. Usługa wyszukiwania jest obecnie wyłączona/usługa wyszukiwania przechodzi do przejścia. | Skonfiguruj usługę z co najmniej 3 replikami na 99,9% dostępności na potrzeby [dokumentacji umowy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Niepowodzenie w źródłowym zasobów obliczeniowych/sieciowych (rzadkich) | Nie można ustanowić połączenia w celu zaktualizowania indeksu. Wystąpił nieznany błąd. | Skonfiguruj indeksatory do [uruchomienia zgodnie z harmonogramem](search-howto-schedule-indexers.md) , aby przeprowadzić pobieranie z niepowodzenia.
| Żądanie indeksowania wprowadzone do indeksu docelowego nie zostało potwierdzone w przedziale czasu z powodu problemów z siecią. | Nie można nawiązać połączenia z indeksem wyszukiwania w odpowiednim czasie. | Skonfiguruj indeksatory do [uruchomienia zgodnie z harmonogramem](search-howto-schedule-indexers.md) , aby przeprowadzić pobieranie z niepowodzenia. Ponadto spróbuj zmniejszyć [rozmiar wsadu](/rest/api/searchservice/create-indexer#parameters) indeksatora, jeśli ten błąd będzie się utrzymywał.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Błąd: nie można indeksować dokumentu, ponieważ niektóre dane dokumentu były nieprawidłowe

Dokument został odczytany i przetworzony przez indeksator, ale z powodu niezgodności konfiguracji pól indeksu i danych wyodrębnionych i przetworzonych przez indeksator, nie można dodać go do indeksu wyszukiwania. Przyczyną może być:

| Przyczyna | Szczegóły/przykład
| --- | ---
| Typ danych pól wyodrębnionych przez indeksator jest niezgodny z modelem danych odpowiedniego docelowego pola indeksu. | Pole danych "_Data_" w dokumencie z kluczem "888" ma nieprawidłową wartość "typu EDM. String" ". Oczekiwany typ to "Kolekcja (EDM. String)". |
| Nie można wyodrębnić żadnej jednostki JSON z wartości ciągu. | Nie można przeanalizować wartości "typu" EDM. String "" dla pola "_Data_" jako obiektu JSON. Błąd: "po przeanalizowaniu wartości napotkano nieoczekiwany znak:" ". Ścieżka "_Path_", wiersz 1, pozycja 3162. " |
| Nie można wyodrębnić kolekcji jednostek JSON z wartości ciągu.  | Nie można przeanalizować wartości "typu" EDM. String "" dla pola "_Data_" jako tablicy JSON. Błąd: "po przeanalizowaniu wartości napotkano nieoczekiwany znak:" ". Ścieżka ' [0] ', wiersz 1, pozycja 27. ' |
| W dokumencie źródłowym odnaleziono nieznany typ. | Nieznany typ "_nieznany_" nie może być indeksowany |
| W dokumencie źródłowym użyto niezgodnej notacji punktów geograficznych. | Literały ciągu punktu WKT nie są obsługiwane. Zamiast tego użyj literałów punktu GEOJSON |

We wszystkich tych przypadkach należy zapoznać się z [obsługiwanymi typami danych](/rest/api/searchservice/supported-data-types) i [mapą typów danych dla indeksatorów](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) , aby upewnić się, że schemat indeksu jest prawidłowo skonstruowany i skonfigurowano odpowiednie [mapowania pól indeksatora](search-indexer-field-mappings.md). Komunikat o błędzie będzie zawierać szczegóły, które mogą pomóc w śledzeniu źródła niezgodności.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Błąd: nie można użyć zintegrowanych zasad śledzenia zmian, ponieważ tabela zawiera złożony klucz podstawowy

Dotyczy to tabel SQL i zwykle ma miejsce, gdy klucz jest zdefiniowany jako klucz złożony lub, gdy tabela ma zdefiniowany unikatowy indeks klastrowany (jak w indeksie SQL, a nie indeks Azure Search). Głównym powodem jest to, że atrybut klucza jest modyfikowany jako złożony klucz podstawowy w przypadku [unikatowego indeksu klastrowanego](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described). W takim przypadku upewnij się, że tabela SQL nie ma unikatowego indeksu klastrowanego lub że pole klucza zostało zamapowane na pole, które nie ma zduplikowanych wartości.

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Błąd: nie można przetworzyć dokumentu w maksymalnym czasie wykonywania indeksatora

Ten błąd występuje, gdy indeksator nie może zakończyć przetwarzania pojedynczego dokumentu ze źródła danych w dozwolonym czasie wykonywania. [Maksymalny czas działania](search-limits-quotas-capacity.md#indexer-limits) jest krótszy, gdy są używane umiejętności. W przypadku wystąpienia tego błędu, jeśli maxFailedItems ustawiono wartość inną niż 0, Indeksator pomija dokument w przyszłych uruchomieniach, tak aby indeksowanie mogło postępować. Jeśli nie możesz mieć możliwości pominięcia jakiegokolwiek dokumentu lub jeśli ten błąd występuje w sposób ciągły, rozważ przerwanie dokumentów w mniejszych dokumentach, aby można było wykonać częściowe postępy w ramach jednego wykonywania indeksatora.

<nazwa = "nie może być dokumentem projektu></a>

## <a name="error-could-not-project-document"></a>Błąd: nie można projektować dokumentu

Ten błąd występuje, gdy indeksator próbuje [projektować dane w sklepie z wiedzą](knowledge-store-projection-overview.md) i wystąpił błąd w naszej próbie.  Ten błąd może być spójny i fixable. może to być błąd przejściowy, który może wymagać poczekania i ponowienia próby w celu rozwiązania problemu.  Poniżej przedstawiono zestaw znanych Stanów niepowodzeń i możliwych rozwiązań.

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Nie można zaktualizować obiektu BLOB projekcji `'blobUri'` w kontenerze `'containerName'` |Określony kontener nie istnieje. | Indeksator sprawdzi, czy określony kontener został wcześniej utworzony i utworzy go w razie potrzeby, ale wykonuje to sprawdzenie tylko raz dla indeksatora. Ten błąd oznacza, że element usunięty z kontenera jest usuwany po tym kroku.  Aby rozwiązać ten problem, wypróbuj: pozostaw same informacje o koncie magazynu, poczekaj na zakończenie indeksatora, a następnie ponownie uruchom indeksator. |
| Nie można zaktualizować obiektu BLOB projekcji `'blobUri'` w kontenerze `'containerName'` |Nie można zapisać danych do połączenia transportowego: wykryto, że istniejące połączenie zostało wymuszone przez hosta zdalnego. | Jest to oczekiwany błąd przejściowy usługi Azure Storage, dlatego należy rozwiązać ten problem przez ponowne uruchomienie indeksatora. Jeśli ten błąd wystąpi konsekwentnie, Utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby można było go dokładniej zbadać.  |
| Nie można zaktualizować wiersza `'projectionRow'` w tabeli `'tableName'` | Serwer jest zajęty. | Jest to oczekiwany błąd przejściowy usługi Azure Storage, dlatego należy rozwiązać ten problem przez ponowne uruchomienie indeksatora. Jeśli ten błąd wystąpi konsekwentnie, Utwórz [bilet pomocy technicznej](https://ms.portal.azure.com/#create/Microsoft.Support) , aby można było go dokładniej zbadać.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>Ostrzeżenie: dane wejściowe dotyczące umiejętności były nieprawidłowe
Brak danych wejściowych dla umiejętności, nieprawidłowy typ lub w inny sposób nieprawidłowy. Komunikat ostrzegawczy będzie wskazywał wpływ:
1) Nie można wykonać umiejętności
2) Umiejętność została wykonana, ale może mieć nieoczekiwane wyniki

Umiejętności poznawcze mają wymagane dane wejściowe i opcjonalne dane wejściowe. Na przykład [umiejętność wyodrębniania frazy klucza](cognitive-search-skill-keyphrases.md) ma dwa wymagane dane wejściowe `text` , `languageCode` i bez dodatkowych danych wejściowych. Niestandardowe dane wejściowe umiejętności są uznawane za opcjonalne dane wejściowe.

Jeśli brakuje wymaganych danych wejściowych lub jeśli jakiekolwiek dane wejściowe nie są odpowiednim typem, umiejętność zostanie pominięta i wygeneruje ostrzeżenie. Pominięte umiejętności nie generują żadnych danych wyjściowych, więc jeśli inne umiejętności korzystają z wyników pominiętych umiejętności, mogą generować dodatkowe ostrzeżenia.

Jeśli brakuje opcjonalnego danych wejściowych, umiejętność nadal będzie działać, ale może generować nieoczekiwane dane wyjściowe ze względu na Brak danych wejściowych.

W obu przypadkach to ostrzeżenie może być oczekiwane ze względu na kształt danych. Na przykład jeśli masz dokument zawierający informacje o osobach z polami `firstName` , `middleName` i `lastName` , możesz mieć pewne dokumenty, dla których nie ma wpisu `middleName` . Jeśli chcesz przekazać `middleName` jako dane wejściowe do umiejętności w potoku, oczekujesz, że dane wejściowe dotyczące umiejętności mogą nie mieć pewnego czasu. Należy oszacować dane i scenariusz, aby określić, czy w wyniku tego ostrzeżenia wymagane jest wykonanie jakiejkolwiek akcji.

Jeśli chcesz podać wartość domyślną w przypadku braku danych wejściowych, możesz użyć [umiejętności warunkowej](cognitive-search-skill-conditional.md) do wygenerowania wartości domyślnej, a następnie użyć danych wyjściowych z [umiejętności warunkowej](cognitive-search-skill-conditional.md) jako danych wejściowych umiejętności.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Dane wejściowe umiejętności są niewłaściwego typu | "Wymagane dane wejściowe kwalifikacji nie mają oczekiwanego typu `String` . Name: `text` , source: `/document/merged_content` . "  "Wymagane dane wejściowe kwalifikacji mają nieoczekiwany format. Name: `text` , source: `/document/merged_content` . "  "Nie można wykonać iteracji w przypadku braku tablicy `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ".  "Nie można wybrać `0` w niearray `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` " | Pewne umiejętności oczekują danych wejściowych określonych typów, na przykład [umiejętność tonacji](cognitive-search-skill-sentiment.md) powinna `text` być ciągiem. Jeśli dane wejściowe określają wartość różną od ciągu, wówczas umiejętność nie zostanie wykonana i nie wygeneruje żadnych danych wyjściowych. Upewnij się, że zestaw danych zawiera wartości wejściowe, które są jednorodne w typie, lub Użyj [niestandardowej umiejętności interfejsu API sieci Web](cognitive-search-custom-skill-web-api.md) , aby wstępnie przetworzyć dane wejściowe. Jeśli Iteracja jest przeprowadzana przez tablicę, sprawdź kontekst umiejętności i wprowadź `*` odpowiednie pozycje. Zwykle zarówno kontekst, jak i źródło danych wejściowych powinny kończyć się `*` na potrzeby tablic. |
| Brak danych wejściowych kwalifikacji | Brak "wymaganych danych wejściowych umiejętności. Name: `text` , source: `/document/merged_content` "" brak wartości " `/document/normalized_images/0/imageTags` .  "Nie można wybrać `0` w tablicy `/document/pages` o długości `0` ". | Jeśli wszystkie dokumenty otrzymają to ostrzeżenie, prawdopodobnie występuje literówka w ścieżkach wejściowych i należy dokładnie sprawdzić wielkość liter nazwy właściwości, dodatkowe lub brakujące `*` w ścieżce, i upewnić się, że dokumenty ze źródła danych zawierają wymagane dane wejściowe. |
| Dane wejściowe kodu języka umiejętności są nieprawidłowe | Dane wejściowe kwalifikacji `languageCode` mają następujące kody języka: co `X,Y,Z` najmniej jeden z nich jest nieprawidłowy. | Zobacz więcej szczegółów [poniżej](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Ostrzeżenie: dane wejściowe umiejętności "languageCode" zawierają następujące kody języka: "X, Y, Z", co najmniej jeden z nich jest nieprawidłowy.
Co najmniej jedna wartość przeniesiona do opcjonalnego `languageCode` danych wejściowych z poziomu umiejętności podrzędnej nie jest obsługiwana. Taka sytuacja może wystąpić, jeśli przekazujesz dane wyjściowe [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) do kolejnych umiejętności, a dane wyjściowe składają się z większej liczby języków niż jest to obsługiwane w tych umiejętnościach podrzędnych.

Należy zauważyć, że można również otrzymać ostrzeżenie podobne do tego, jeśli nieprawidłowe `countryHint` dane wejściowe zostaną przesłane do LanguageDetectionSkill. Jeśli tak się stanie, sprawdź, czy pole, którego używasz ze źródła danych dla tego danych wejściowych zawiera prawidłowe kody krajów ISO 3166-1 Alpha-2 2. Jeśli niektóre z nich są prawidłowe i niektóre z nich są nieprawidłowe, przejdź do poniższych wskazówek, ale Zastąp ciąg `languageCode` `countryHint` i `defaultLanguageCode` with, `defaultCountryHint` Aby dopasować się do Twojego przypadku użycia.

Jeśli wiesz, że zestaw danych znajduje się w jednym języku, należy usunąć [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) i `languageCode` dane wejściowe dotyczące umiejętności, a `defaultLanguageCode` zamiast tego użyć parametru umiejętności dla tej umiejętności, przy założeniu, że język jest obsługiwany dla tej umiejętności.

Jeśli wiesz, że zestaw danych zawiera wiele języków i dlatego potrzebujesz [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) i `languageCode` danych wejściowych, rozważ dodanie [ConditionalSkill](cognitive-search-skill-conditional.md) w celu odfiltrowania tekstu w językach, które nie są obsługiwane przed przekazaniem tekstu do poziomu umiejętności podrzędnej.  Oto przykład tego, co może wyglądać jak w przypadku EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Poniżej przedstawiono niektóre odwołania do obecnie obsługiwanych języków dla każdej z umiejętności, które mogą generować ten komunikat o błędzie:
* [Analiza tekstu obsługiwane języki](../cognitive-services/text-analytics/language-support.md) (dla [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md)i [PIIDetectionSkill](cognitive-search-skill-pii-detection.md))
* [Języki obsługiwane przez translatora](../cognitive-services/translator/language-support.md) (dla [tekstu TranslationSkill](cognitive-search-skill-text-translation.md))
* [SplitSkill tekstu](cognitive-search-skill-textsplit.md) Obsługiwane języki: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>Ostrzeżenie: dane wejściowe dotyczące umiejętności zostały obcięte
Umiejętności poznawcze mają ograniczone długość tekstu, który można analizować jednocześnie. Jeśli wprowadzanie tekstu tych umiejętności przekracza ten limit, spowoduje to obcinanie tekstu w celu spełnienia limitu, a następnie przeprowadzenie wzbogacania tego tekstu. Oznacza to, że umiejętność jest wykonywana, ale nie na wszystkich Twoich danych.

W poniższym przykładzie LanguageDetectionSkill `'text'` pole wejściowe może wyzwolić to ostrzeżenie, jeśli przekracza limit znaków. Limity danych wejściowych kwalifikacji można znaleźć w dokumentacji dotyczącej [umiejętności](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Jeśli chcesz upewnić się, że cały tekst jest analizowany, rozważ użycie opcji [Podziel umiejętność](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Ostrzeżenie: odpowiedź na umiejętność interfejsu API sieci Web zawiera ostrzeżenia
Indeksator mógł uruchomić umiejętność w zestawu umiejętności, ale odpowiedź z żądania internetowego interfejsu API wskazywała, że wystąpiły ostrzeżenia podczas wykonywania. Zapoznaj się z ostrzeżeniami, aby zrozumieć, w jaki sposób wpływają dane oraz czy nie są wymagane akcje.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Ostrzeżenie: Bieżąca konfiguracja indeksatora nie obsługuje przyrostowego postępu

To ostrzeżenie występuje tylko w przypadku Cosmos DB źródeł danych.

Przyrostowy postęp podczas indeksowania zapewnia, że jeśli wykonywanie indeksatora zostanie przerwane przez przejściowe błędy lub limit czasu wykonywania, indeksator może zostać pobrany w miejscu, w którym zostanie pozostawiony po następnym uruchomieniu, zamiast konieczności ponownego indeksowania całej kolekcji od podstaw. Jest to szczególnie ważne podczas indeksowania dużych kolekcji.

Możliwość wznowienia nieukończonego zadania indeksowania jest predykatem według dokumentów uporządkowanych według `_ts` kolumny. Indeksator używa sygnatury czasowej, aby określić, który dokument powinien zostać pobrany dalej. Jeśli `_ts` brakuje kolumny lub jeśli indeksator nie może określić, czy kwerenda niestandardowa jest uporządkowana, indeksator rozpoczyna się od początku i zobaczysz to ostrzeżenie.

Istnieje możliwość zastąpienia tego zachowania, co pozwala na przyrostowy postęp i pomijanie tego ostrzeżenia przy użyciu `assumeOrderByHighWatermarkColumn` właściwości konfiguracja.

Aby uzyskać więcej informacji, zobacz [przyrostowy postęp i zapytania niestandardowe](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Ostrzeżenie: niektóre dane zostały utracone podczas projekcji. Wiersz "X" w tabeli "Y" ma właściwość ciągu "Z", która jest zbyt długa.

[Usługa Table Storage](https://azure.microsoft.com/services/storage/tables) ma limity dotyczące sposobu, w jaki mogą być [Właściwości dużych jednostek](/rest/api/storageservices/understanding-the-table-service-data-model#property-types) . Ciągi mogą zawierać co najwyżej 32 000 znaków. Jeśli wiersz z właściwością ciągu dłuższą niż 32 000 znaków jest rzutowany, zachowywane są tylko pierwsze znaki 32 000. Aby obejść ten problem, należy unikać tworzenia projektów wierszy z właściwościami ciągu dłuższymi niż 32 000 znaków.

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Ostrzeżenie: obcięto wyodrębniony tekst do X znaków
Indeksatory ograniczają ilość tekstu, który można wyodrębnić z dowolnego dokumentu. Ten limit zależy od warstwy cenowej: 32 000 znaków dla warstwy Bezpłatna, 64 000 dla wersji podstawowa, 4 000 000 dla Standard, 8 000 000 dla standardu S2 i 16 000 000 dla standardowego S3. Tekst, który został obcięty, nie będzie indeksowany. Aby uniknąć tego ostrzeżenia, spróbuj rozdzielić dokumenty z dużymi ilościami tekstu na kilka mniejszych dokumentów. 

Aby uzyskać więcej informacji, zobacz [limity indeksatora](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Ostrzeżenie: nie można zmapować pola wyjściowego "X" na indeks wyszukiwania
Mapowania pól wyjściowych, które odwołują się do nieistniejących/niepustych danych, będą generować ostrzeżenia dla każdego dokumentu i powodować puste pole indeksu. Aby obejść ten problem, należy dokładnie sprawdzić ścieżki źródłowe mapowania pól wyjściowych dla możliwych błędów lub ustawić wartość domyślną przy użyciu [umiejętności warunkowej](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist). Szczegóły można znaleźć w temacie [Mapowanie pola danych wyjściowych](cognitive-search-output-field-mapping.md) .

| Przyczyna | Szczegóły/przykład | Rozwiązanie |
| --- | --- | --- |
| Nie można wykonać iteracji w przypadku niearray | "Nie można wykonać iteracji w przypadku braku tablicy `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ". | Ten błąd występuje, gdy wyjście nie jest tablicą. Jeśli uważasz, że dane wyjściowe powinny być tablicą, sprawdź wskazane wyjściowe ścieżki pola źródłowego pod kątem błędów. Na przykład `*` w nazwie pola źródłowego mogą znajdować się brakujące lub dodatkowe. Istnieje również możliwość, że dane wejściowe tej umiejętności mają wartość null, co oznacza pustą tablicę. Znajdź podobne szczegóły w polu [dane wejściowe dotyczące umiejętności były nieprawidłowe](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) .    |
| Nie można wybrać `0` w niearray | "Nie można wybrać `0` w nie Array `/document/pages` ". | Może się tak zdarzyć, jeśli dane wyjściowe umiejętności nie generują tablicy, a wynikowa nazwa pola źródłowego ma indeks tablicy lub `*` ścieżkę. Sprawdź dokładnie ścieżki podane w nazwach pól źródłowych danych wyjściowych i wartość pola dla wskazanej nazwy pola. Znajdź podobne szczegóły w polu [dane wejściowe dotyczące umiejętności były nieprawidłowe](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) .  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Ostrzeżenie: zasady wykrywania zmian danych są skonfigurowane do korzystania z kolumny klucza "X"
[Zasady wykrywania zmian danych](/rest/api/searchservice/create-data-source#data-change-detection-policies) mają określone wymagania dotyczące kolumn, których używają do wykrywania zmian. Jedno z tych wymagań polega na tym, że ta kolumna jest aktualizowana za każdym razem, gdy element źródłowy zostanie zmieniony. Innym wymaganiem jest to, że nowa wartość dla tej kolumny jest większa niż Poprzednia wartość. Kolumny kluczy nie spełniają tego wymagania, ponieważ nie zmieniają w każdej aktualizacji. Aby obejść ten problem, wybierz inną kolumnę dla zasad wykrywania zmian.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Ostrzeżenie: tekst dokumentu jest zakodowany w formacie UTF-16, ale brakuje znacznika kolejności bajtów

[Tryby analizowania indeksatora](/rest/api/searchservice/create-indexer#blob-configuration-parameters) muszą wiedzieć, jak kodowanie tekstu przed jego przeanalizą. Dwa najczęstsze sposoby kodowania tekstu to UTF-16 i UTF-8. UTF-8 to kodowanie o zmiennej długości, gdzie każdy znak ma długość od 1 do 4 bajtów. UTF-16 to kodowanie o stałej długości, gdzie każdy znak ma długość 2 bajtów. UTF-16 ma dwa różne warianty, "big endian" i "little endian". Kodowanie tekstu jest określane na podstawie "znacznika kolejności bajtów", serii bajtów przed tekstem.

| Encoding | Znacznik kolejności bajtów |
| --- | --- |
| Big endian UTF-16 | 0xFE 0xFF |
| Little endian UTF-16 | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Jeśli nie ma znacznika kolejności bajtów, przyjmuje się, że tekst zostanie zakodowany jako UTF-8.

Aby obejść to ostrzeżenie, należy określić, co ma być kodowanie tekstu dla tego obiektu BLOB, i dodać odpowiedni znacznik kolejności bajtów.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Ostrzeżenie: Kolekcja Cosmos DB "X" ma opóźnione zasady indeksowania. Niektóre dane mogą zostać utracone

Kolekcje z [opóźnionymi](../cosmos-db/index-policy.md#indexing-mode) zasadami indeksowania nie mogą być wykonywane w sposób ciągły, co spowodowało brak danych indeksatora. Aby obejść to ostrzeżenie, Zmień zasady indeksowania tak, aby były spójne.

## <a name="warning-the-document-contains-very-long-words-longer-than-64-characters-these-words-may-result-in-truncated-andor-unreliable-model-predictions"></a>Ostrzeżenie: dokument zawiera bardzo długie słowa (więcej niż 64 znaków). Te słowa mogą powodować obcinanie i/lub zawodne przewidywania modeli.

To ostrzeżenie jest przesyłane z usługi analiza tekstu.  W niektórych przypadkach jest bezpieczne ignorowanie tego ostrzeżenia, na przykład wtedy, gdy dokument zawiera długi adres URL (prawdopodobnie nie jest to kluczowa fraza lub tonacji, itp.).  Należy pamiętać, że gdy wyraz jest dłuższy niż 64 znaków, zostanie obcięty do 64 znaków, które mogą wpływać na przewidywania modeli.