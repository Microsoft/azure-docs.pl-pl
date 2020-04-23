---
title: Błędy i ostrzeżenia indeksatora
titleSuffix: Azure Cognitive Search
description: Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ed10e998ea05b6687190b1f87095f8bc28265905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086619"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Rozwiązywanie typowych błędów i ostrzeżeń indeksatora w usłudze Azure Cognitive Search

Ten artykuł zawiera informacje i rozwiązania typowych błędów i ostrzeżeń, które mogą wystąpić podczas indeksowania i wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.

Indeksowanie zatrzymuje się, gdy liczba błędów przekracza ['maxFailedItems'](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Jeśli chcesz, aby indeksatory ignorowały te błędy (i pomijały `maxFailedItems` "nieudane dokumenty"), rozważ zaktualizowanie i `maxFailedItemsPerBatch` zgodnie z opisem [w tym miejscu](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Każdy dokument nie powiódł się wraz z jego kluczem dokumentu (jeśli jest dostępny) będzie pokazywał się jako błąd w stanie wykonywania indeksatora. Można wykorzystać [interfejsu API indeksu](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ręcznie przekazać dokumenty w późniejszym momencie, jeśli ustawiono indeksator tolerować błędy.

Informacje o błędzie w tym artykule może pomóc w rozwiązaniu błędów, umożliwiając indeksowanie, aby kontynuować.

Ostrzeżenia nie przestają indeksowania, ale wskazują warunki, które mogą spowodować nieoczekiwane wyniki. Czy podejmiesz działanie, czy nie, zależy od danych i scenariusza.

Począwszy od `2019-05-06`wersji interfejsu API, błędy i ostrzeżenia indeksatora na poziomie elementu są skonstruowane w celu zapewnienia większej przejrzystości wokół przyczyn i kolejnych kroków. Zawierają one następujące właściwości:

| Właściwość | Opis | Przykład |
| --- | --- | --- |
| key | Identyfikator dokumentu, na który ma wpływ błąd lub ostrzeżenie. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | Nazwa operacji opisująca miejsce wystąpienia błędu lub ostrzeżenia. Jest to generowane przez następującą strukturę: [kategoria]. [podkategoria]. [resourceType]. [nazwa zasobów] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | Opis wysokiego poziomu błędu lub ostrzeżenia. | Nie można wykonać umiejętności, ponieważ żądanie interfejsu api sieci Web nie powiodło się. |
| Szczegóły | Wszelkie dodatkowe szczegóły, które mogą być pomocne w zdiagnozowaniu problemu, takie jak odpowiedź WebApi, jeśli wykonanie umiejętności niestandardowej nie powiodło się. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 źródło,`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` Func ... reszta śladu stosu... |
| documentationLink | Łącze do odpowiedniej dokumentacji ze szczegółowymi informacjami do debugowania i rozwiązania problemu. Ten link często wskazuje jedną z poniższych sekcji na tej stronie. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Błąd: nie można odczytać dokumentu

Indeksator nie może odczytać dokumentu ze źródła danych. Może się to zdarzyć ze względu na:

| Przyczyna | Szczegóły/Przykład | Rozwiązanie |
| --- | --- | --- |
| Niespójne typy pól w różnych dokumentach | "Typ wartości ma niezgodność z typem kolumny. Nie można `'{47.6,-122.1}'` przechowywać w kolumnie autorzy.  Oczekiwanym typem jest JArray."  "Błąd konwersji nvarchar typu danych do float."  "Konwersja nie powiodła się podczas konwersji wartości nvarchar '12 miesięcy' na typ danych int."  "Arytmetyczny błąd przepełnienia konwersji wyrażenia na typ danych int." | Upewnij się, że typ każdego pola jest taki sam w różnych dokumentach. Na przykład jeśli pierwsze `'startTime'` pole dokumentu jest DateTime, a w drugim dokumencie jest ciągiem, ten błąd zostanie trafiony. |
| błędy z podstawowej usługi źródła danych | (z Cosmos DB)`{"Errors":["Request rate is large"]}` | Sprawdź wystąpienie magazynu, aby upewnić się, że jest w dobrej kondycji. Może być konieczne dostosowanie skalowania/partycjonowania. |
| przejściowe problemy | Wystąpił błąd na poziomie transportu podczas odbierania wyników z serwera. (dostawca: Dostawca TCP, błąd: 0 - Istniejące połączenie zostało przymusowo zamknięte przez hosta zdalnego | Czasami występują nieoczekiwane problemy z łącznością. Spróbuj uruchomić dokument za pośrednictwem indeksatora później. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Błąd: nie można wyodrębnić zawartości lub metadanych z dokumentu
Indeksator ze źródłem danych obiektów Blob nie może wyodrębnić zawartości lub metadanych z dokumentu (na przykład pliku PDF). Może się to zdarzyć ze względu na:

| Przyczyna | Szczegóły/Przykład | Rozwiązanie |
| --- | --- | --- |
| blob przekracza limit rozmiaru | Dokument `'150441598'` to bajty, który przekracza `'134217728'` maksymalny rozmiar bajtów dla wyodrębniania dokumentów dla bieżącej warstwy usług. | [błędy indeksowania obiektów blob](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| obiekt blob ma nieobsługicony typ zawartości | Dokument ma nieobsługicony typ zawartości`'image/png'` | [błędy indeksowania obiektów blob](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| obiekt blob jest szyfrowany | Nie można przetworzyć dokumentu — może być zaszyfrowany lub chroniony hasłem. | Obiekt blob można pominąć z [ustawieniami obiektu blob](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| przejściowe problemy | "Błąd przetwarzania obiektu blob: żądanie zostało przerwane: żądanie zostało anulowane." "Limit czasu dokumentu podczas przetwarzania." | Czasami występują nieoczekiwane problemy z łącznością. Spróbuj uruchomić dokument za pośrednictwem indeksatora później. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Błąd: nie można przeanalizować dokumentu
Indeksator odczytuje dokument ze źródła danych, ale wystąpił problem z konwersją zawartości dokumentu na określony schemat mapowania pól. Może się to zdarzyć ze względu na:

| Przyczyna | Szczegóły/Przykład | Rozwiązanie |
| --- | --- | --- |
| Brakuje klucza dokumentu | Nie można zabraknąć klucza dokumentu lub nie jest on pusty | Upewnij się, że wszystkie dokumenty mają prawidłowe klucze dokumentów |
| Klucz dokumentu jest nieprawidłowy | Klucz dokumentu nie może być dłuższy niż 1024 znaki | Zmodyfikuj klucz dokumentu, aby spełnić wymagania dotyczące sprawdzania poprawności. |
| Nie można zastosować mapowania pól do pola | Nie można zastosować `'functionName'` funkcji `'fieldName'`mapowania do pola . Tablica nie może mieć wartości null. Nazwa parametru: bajty | Sprawdź [mapowania pól zdefiniowane](search-indexer-field-mappings.md) w indeksatorze i porównaj je z danymi określonego pola dokumentu, który uległ awarii. Może być konieczna modyfikacja mapowań pól lub danych dokumentu. |
| Nie można odczytać wartości pola | Nie można odczytać wartości `'fieldName'` kolumny `'fieldIndex'`w indeksie . Wystąpił błąd na poziomie transportu podczas odbierania wyników z serwera. (dostawca: dostawca TCP, błąd: 0 - Istniejące połączenie zostało przymusowo zamknięte przez hosta zdalnego).) | Te błędy są zazwyczaj spowodowane nieoczekiwanymi problemami z łącznością z podstawową usługą źródła danych. Spróbuj uruchomić dokument za pośrednictwem indeksatora później. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Błąd: nie można wykonać umiejętności
Indeksator nie był w stanie uruchomić umiejętności w skillset.

| Przyczyna | Szczegóły/Przykład | Rozwiązanie |
| --- | --- | --- |
| Przejściowe problemy z łącznością | Wystąpił błąd przejściowy. Spróbuj ponownie później. | Czasami występują nieoczekiwane problemy z łącznością. Spróbuj uruchomić dokument za pośrednictwem indeksatora później. |
| Potencjalny błąd produktu | Wystąpił nieoczekiwany błąd. | Wskazuje to na nieznaną klasę awarii i może oznaczać, że występuje błąd produktu. Aby uzyskać pomoc, złóż [zgłoszenie do pomocy](https://ms.portal.azure.com/#create/Microsoft.Support) technicznej. |
| Umiejętność napotkała błąd podczas wykonywania | (Z umiejętności scalania) Co najmniej jedna wartość przesunięcia była nieprawidłowa i nie można jej przeanalizować. Elementy zostały wstawione na końcu tekstu | Użyj informacji w komunikacie o błędzie, aby rozwiązać problem. Ten rodzaj awarii będzie wymagać działania, aby rozwiązać. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Błąd: nie można wykonać umiejętności, ponieważ żądanie interfejsu API sieci Web nie powiodło się
Wykonanie umiejętności nie powiodło się, ponieważ wywołanie do interfejsu API sieci Web nie powiodło się. Zazwyczaj ta klasa awarii występuje, gdy umiejętności niestandardowe są używane, w którym to przypadku należy debugować kod niestandardowy, aby rozwiązać problem. Jeśli zamiast tego błąd jest z wbudowanej umiejętności, zapoznaj się z komunikatem o błędzie, aby uzyskać pomoc w rozwiązaniu problemu.

Podczas debugowania tego problemu należy zwrócić uwagę na [ostrzeżenia dotyczące wprowadzania umiejętności](#warning-skill-input-was-invalid) dla tej umiejętności. Punkt końcowy interfejsu API sieci Web może się nie powiódł, ponieważ indeksator przekazuje go nieoczekiwane dane wejściowe.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Błąd: nie można wykonać umiejętności, ponieważ odpowiedź umiejętności interfejsu API sieci Web jest nieprawidłowa
Wykonanie umiejętności nie powiodło się, ponieważ wywołanie do interfejsu API sieci Web zwróciło nieprawidłową odpowiedź. Zazwyczaj ta klasa awarii występuje, gdy umiejętności niestandardowe są używane, w którym to przypadku należy debugować kod niestandardowy, aby rozwiązać problem. Jeśli zamiast tego błąd jest z wbudowanej umiejętności, należy złożyć [bilet pomocy technicznej,](https://ms.portal.azure.com/#create/Microsoft.Support) aby uzyskać pomoc.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Błąd: Umiejętność nie została wykonana w terminie
Istnieją dwa przypadki, w których można napotkać ten komunikat o błędzie, z których każdy powinien być traktowany inaczej. Postępuj zgodnie z poniższymi instrukcjami w zależności od umiejętności, która zwróciła ci ten błąd.

### <a name="built-in-cognitive-service-skills"></a>Wbudowane umiejętności usługi poznawczej
Wiele wbudowanych umiejętności poznawczych, takich jak wykrywanie języka, rozpoznawanie jednostek lub OCR, są obsługiwane przez punkt końcowy interfejsu API usługi Cognitive Service. Czasami występują przejściowe problemy z tymi punktami końcowymi i limit czasu żądania. W przypadku problemów przejściowych nie ma lekarstwa, z wyjątkiem oczekiwania i próby ponownie. Jako środki zaradcze należy rozważyć ustawienie indeksatora do [uruchomienia zgodnie z harmonogramem](search-howto-schedule-indexers.md). Zaplanowane indeksowanie podnosi się tam, gdzie zostało przerwane. Zakładając, że przejściowe problemy zostały rozwiązane, indeksowania i przetwarzania umiejętności poznawczych powinny być w stanie kontynuować w następnym zaplanowanym uruchomieniu.

Jeśli ten błąd jest nadal widoczny w tym samym dokumencie dla wbudowanej umiejętności poznawczych, złóż [bilet pomocy technicznej,](https://ms.portal.azure.com/#create/Microsoft.Support) aby uzyskać pomoc, ponieważ nie jest to oczekiwane.

### <a name="custom-skills"></a>Umiejętności niestandardowe
Jeśli napotkasz błąd limitu czasu z niestandardową umiejętnością, którą utworzyłeś, możesz wypróbować kilka rzeczy. Najpierw przejrzyj swoje umiejętności niestandardowe i upewnij się, że nie utknie w nieskończonej pętli i że konsekwentnie zwraca wynik. Po potwierdzeniu, że tak jest, określ, jaki jest czas wykonania twoich umiejętności. Jeśli nie ustawiono jawnie `timeout` wartości w definicji umiejętności `timeout` niestandardowych, wartość domyślna to 30 sekund. Jeśli 30 sekund nie jest wystarczająco długie, aby twoje `timeout` umiejętności mogły zostać wykonane, możesz określić wyższą wartość w definicji umiejętności niestandardowych. Oto przykład niestandardowej definicji umiejętności, w której limit czasu jest ustawiony na 90 sekund:

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

Maksymalna wartość, którą można `timeout` ustawić dla parametru wynosi 230 sekund.  Jeśli twoja umiejętność niestandardowa nie jest w stanie wykonać konsekwentnie w `batchSize` ciągu 230 sekund, możesz rozważyć zmniejszenie umiejętności niestandardowych, tak aby miał mniej dokumentów do przetworzenia w ramach jednego wykonania.  Jeśli masz już `batchSize` ustawioną na 1, musisz przepisać umiejętność, aby móc wykonać w mniej niż 230 sekund lub w inny sposób podzielić ją na wiele umiejętności niestandardowych, tak aby czas wykonania dla dowolnej pojedynczej umiejętności niestandardowej wynosi maksymalnie 230 sekund. Zapoznaj się z [niestandardową dokumentacją umiejętności,](cognitive-search-custom-skill-web-api.md) aby uzyskać więcej informacji.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Błąd: nie`MergeOrUpload`można ' ' | '`Delete`' do indeksu wyszukiwania

Dokument został odczytany i przetworzony, ale indeksator nie mógł dodać go do indeksu wyszukiwania. Może się to zdarzyć ze względu na:

| Przyczyna | Szczegóły/Przykład | Rozwiązanie |
| --- | --- | --- |
| Pole zawiera zbyt duży termin | Termin w dokumencie jest większy niż [limit 32 KB](search-limits-quotas-capacity.md#api-request-limits) | Można uniknąć tego ograniczenia, upewniając się, że pole nie jest skonfigurowany jako filtrowania, facetable, lub sortowania.
| Dokument jest zbyt duży, aby można go było zindeksować | Dokument jest większy niż [maksymalny rozmiar żądania interfejsu API](search-limits-quotas-capacity.md#api-request-limits) | [Jak indeksować duże zestawy danych](search-howto-large-index.md)
| Dokument zawiera zbyt wiele obiektów w kolekcji | Kolekcja w dokumencie przekracza [maksymalną liczbę elementów we wszystkich limitach kolekcji złożonych](search-limits-quotas-capacity.md#index-limits) "Dokument z kluczem `'1000052'` zawiera `'4303'` obiekty w kolekcjach (tablice JSON). W `'3000'` większości obiektów mogą być w kolekcjach w całym dokumencie. Usuń obiekty z kolekcji i spróbuj ponownie zindeksować dokument." | Zaleca się zmniejszenie rozmiaru złożonej kolekcji w dokumencie do poniżej limitu i uniknięcia wysokiego wykorzystania magazynu.
| Problemy z połączeniem z indeksem docelowym (który powtarza się po ponownych próbach), ponieważ usługa jest pod innym obciążeniem, takim jak wykonywanie zapytań lub indeksowanie. | Nie można ustanowić połączenia do aktualizacji indeksu. Usługa wyszukiwania jest pod dużym obciążeniem. | [Skalowanie usługi wyszukiwania w górę](search-capacity-planning.md)
| Usługa wyszukiwania jest załatana do aktualizacji usługi lub znajduje się w środku ponownej konfiguracji topologii. | Nie można ustanowić połączenia do aktualizacji indeksu. Usługa wyszukiwania jest obecnie wyłączna/usługa wyszukiwania przechodzi przejście. | Konfigurowanie usługi z co najmniej 3 replikami dla 99,9% dostępności na [dokumentację umowy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Błąd w podstawowym zasobie obliczeniowym/sieciowym (rzadko) | Nie można ustanowić połączenia do aktualizacji indeksu. Wystąpił nieznany błąd. | Skonfiguruj indeksatory do [uruchamiania zgodnie z harmonogramem,](search-howto-schedule-indexers.md) aby odebrać ze stanu awarii.
| Żądanie indeksowania do indeksu docelowego nie zostało potwierdzone w okresie limitu czasu z powodu problemów z siecią. | Nie można ustanowić połączenia z indeksem wyszukiwania w odpowiednim czasie. | Skonfiguruj indeksatory do [uruchamiania zgodnie z harmonogramem,](search-howto-schedule-indexers.md) aby odebrać ze stanu awarii. Ponadto spróbuj obniżyć rozmiar [partii](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) indeksatora, jeśli ten warunek błędu będzie się powtarzał.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Błąd: nie można zindeksować dokumentu, ponieważ niektóre dane dokumentu były nieprawidłowe

Dokument został odczytany i przetworzony przez indeksatora, ale ze względu na niezgodność w konfiguracji pól indeksu i danych wyodrębnionych i przetworzonych przez indeksatora, nie można dodać go do indeksu wyszukiwania. Może się to zdarzyć ze względu na:

| Przyczyna | Szczegóły/Przykład
| --- | ---
| Typ danych pól wyodrębnionych przez indeksator jest niezgodny z modelem danych odpowiedniego pola indeksu docelowego. | Pole danych '_data_' w dokumencie z kluczem '888' ma nieprawidłową wartość 'typu 'Edm.String''. Oczekiwanym typem była "Kolekcja(Edm.String)". |
| Nie można wyodrębnić żadnej jednostki JSON z wartości ciągu. | Nie można przeanalizować wartości 'typu 'Edm.String''_danych_pola' jako obiektu JSON. Błąd:'Po przeanalizowaniu wartości napotkano nieoczekiwany znak: ''. Ścieżka '_ścieżka_', linia 1, pozycja 3162.' |
| Nie można wyodrębnić kolekcji jednostek JSON z wartości ciągu.  | Nie można przeanalizować wartości 'typu 'Edm.String''_danych_pola' jako tablicy JSON. Błąd:'Po przeanalizowaniu wartości napotkano nieoczekiwany znak: ''. Ścieżka '[0]', linia 1, pozycja 27.' |
| Nieznany typ został odkryty w dokumencie źródłowym. | Nie można zindeksować nieznanego typu '_unknown_' |
| W dokumencie źródłowym użyto niezgodnej notacji dla punktów geograficznych. | Literały ciągu WKT POINT nie są obsługiwane. Zamiast tego użyj literałów punktowych GeoJson |

We wszystkich tych przypadkach należy zapoznać się z [obsługiwanymi typami danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) i [mapą typu danych dla indeksatorów,](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) aby upewnić się, że schemat indeksu został poprawnie utworzony i skonfigurowano odpowiednie [mapowania pól indeksatora](search-indexer-field-mappings.md). Komunikat o błędzie będzie zawierać szczegóły, które mogą pomóc wyśledzić źródło niezgodności.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Błąd: nie można użyć zintegrowanych zasad śledzenia zmian, ponieważ tabela ma złożony klucz podstawowy

Dotyczy to tabel SQL i zwykle dzieje się, gdy klucz jest zdefiniowany jako klucz złożony lub, gdy tabela zdefiniowała unikatowy indeks klastrowany (jak w indeksie SQL, a nie indeks azure search). Głównym powodem jest to, że atrybut klucza jest modyfikowany jako złożony klucz podstawowy w przypadku [unikatowego indeksu klastrowanego](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15). W takim przypadku upewnij się, że tabela SQL nie ma unikatowego indeksu klastrowanego lub że pole klucza jest mapowane na pole, które gwarantuje, że nie ma zduplikowanych wartości.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Błąd: nie można przetworzyć dokumentu w maksymalnym czasie wykonywania indeksatora

Ten błąd występuje, gdy indeksator nie może zakończyć przetwarzania pojedynczego dokumentu ze źródła danych w dozwolonym czasie wykonywania. [Maksymalny czas pracy](search-limits-quotas-capacity.md#indexer-limits) jest krótszy, gdy używane są zestawy umiejętności. Gdy wystąpi ten błąd, jeśli masz maxFailedItems ustawiona na wartość inną niż 0, indeksator pomija dokument w przyszłych uruchamia, dzięki czemu indeksowanie może przejść. Jeśli nie możesz sobie pozwolić na pominięcie dowolnego dokumentu lub jeśli widzisz ten błąd konsekwentnie, należy rozważyć podział dokumentów na mniejsze dokumenty, dzięki czemu można dokonać częściowego postępu w ramach wykonywania jednego indeksatora.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Błąd: nie można udokumentować projektu

Ten błąd występuje, gdy indeksator próbuje [projekt danych do magazynu wiedzy](knowledge-store-projection-overview.md) i wystąpił błąd w naszej próbie.  Ten błąd może być spójne i naprawić lub może to być przejściowy błąd z ujścia wyjściowego projekcji, które mogą być konieczne czekać i ponowić próbę w celu rozwiązania.  Oto zestaw znanych stanów awarii i możliwych rozwiązań.

| Przyczyna | Szczegóły/Przykład | Rozwiązanie |
| --- | --- | --- |
| Nie można zaktualizować `'blobUri'` obiektu blob rzutowania w kontenerze`'containerName'` |Określony kontener nie istnieje. | Indeksator sprawdzi, czy określony kontener został wcześniej utworzony i utworzy go w razie potrzeby, ale wykonuje tę kontrolę tylko raz na uruchomienie indeksatora. Ten błąd oznacza, że coś usunęło kontener po tym kroku.  Aby rozwiązać ten problem, spróbuj tego: pozostaw informacje o koncie magazynu w spokoju, poczekaj na zakończenie indeksatora, a następnie uruchom ponownie indeksatora. |
| Nie można zaktualizować `'blobUri'` obiektu blob rzutowania w kontenerze`'containerName'` |Nie można zapisać danych w połączeniu transportowym: Istniejące połączenie zostało przymusowo zamknięte przez hosta zdalnego. | Oczekuje się, że będzie to przejściowy błąd z usługą Azure Storage i w związku z tym powinny zostać rozwiązane przez ponowne uruchomienie indeksatora. Jeśli ten błąd jest stale napotkany, złóż [zgłoszenie do pomocy technicznej,](https://ms.portal.azure.com/#create/Microsoft.Support) aby można było go zbadać dalej.  |
| Nie można `'projectionRow'` zaktualizować wiersza w tabeli`'tableName'` | Serwer jest zajęty. | Oczekuje się, że będzie to przejściowy błąd z usługą Azure Storage i w związku z tym powinny zostać rozwiązane przez ponowne uruchomienie indeksatora. Jeśli ten błąd jest stale napotkany, złóż [zgłoszenie do pomocy technicznej,](https://ms.portal.azure.com/#create/Microsoft.Support) aby można było go zbadać dalej.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Ostrzeżenie: Wprowadzanie umiejętności było nieprawidłowe
Brak danych wejściowych do umiejętności, niewłaściwy typ lub w inny sposób nieprawidłowy. Komunikat ostrzegawczy wskaże wpływ:
1) Nie można wykonać umiejętności
2) Umiejętności wykonywane, ale mogą mieć nieoczekiwane wyniki

Umiejętności poznawcze mają wymagane dane wejściowe i opcjonalne dane wejściowe. Na przykład [key frazy wyodrębniania](cognitive-search-skill-keyphrases.md) umiejętności `text` `languageCode`ma dwa wymagane dane wejściowe , i nie ma danych wejściowych opcjonalnych. Niestandardowe dane wejściowe umiejętności są uważane za dane wejściowe opcjonalne.

Jeśli brakuje wymaganych danych wejściowych lub jeśli dane wejściowe nie są odpowiedniego typu, umiejętność zostanie pominięta i generuje ostrzeżenie. Pominięte umiejętności nie generują żadnych wyników, więc jeśli inne umiejętności używają wyników pominiętej umiejętności, mogą generować dodatkowe ostrzeżenia.

Jeśli brakuje opcjonalnego wejścia, umiejętność będzie nadal działać, ale może spowodować nieoczekiwane dane wyjściowe z powodu brakującego wejścia.

W obu przypadkach można oczekiwać tego ostrzeżenia ze względu na kształt danych. Na przykład, jeśli masz dokument zawierający informacje `firstName`o `middleName`osobach z polami , i `lastName`, `middleName`możesz mieć niektóre dokumenty, które nie mają wpisu dla . Jeśli przekażesz `middleName` jako dane wejściowe do umiejętności w potoku, oczekuje się, że ten wkład umiejętności może brakować przez część czasu. Należy ocenić dane i scenariusz, aby ustalić, czy w wyniku tego ostrzeżenia jest wymagana żadna akcja.

Jeśli chcesz podać wartość domyślną w przypadku braku danych wejściowych, możesz użyć [umiejętności Warunkowe,](cognitive-search-skill-conditional.md) aby wygenerować wartość domyślną, a następnie użyć danych wyjściowych [umiejętności warunkowej](cognitive-search-skill-conditional.md) jako danych wejściowych umiejętności.


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

| Przyczyna | Szczegóły/Przykład | Rozwiązanie |
| --- | --- | --- |
| Wprowadzanie umiejętności jest niewłaściwym typem | "Wymagane dane wejściowe umiejętności nie `String`były typu oczekiwanego. Nazwa: `text`, `/document/merged_content`Źródło: ."  "Wymagane dane wejściowe umiejętności nie miały oczekiwanego formatu. Nazwa: `text`, `/document/merged_content`Źródło: ."  "Nie można iterować `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`zaostrą zaostrą poza tablicą."  "Nie można `0` wybrać w `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`innych tablic " | Niektóre umiejętności oczekują danych wejściowych określonych typów, `text` na przykład umiejętności [sentyment](cognitive-search-skill-sentiment.md) oczekuje się ciągu. Jeśli dane wejściowe określa wartość bez ciągu, a następnie umiejętności nie wykonuje i generuje żadnych wyjść. Upewnij się, że zestaw danych ma wartości wejściowe jednolite w typie lub użyj [niestandardowego interfejsu API sieci Web,](cognitive-search-custom-skill-web-api.md) aby wstępnie przetworzyć dane wejściowe. Jeśli iteracji umiejętności nad tablicy, sprawdź kontekst umiejętności `*` i dane wejściowe mają w odpowiednich pozycjach. Zazwyczaj kontekst i źródło wejściowe `*` powinny kończyć się dla tablic. |
| Brak danych wejściowych umiejętności | "Brakuje wymaganego wkładu umiejętności. Nazwa: `text`, `/document/merged_content`Źródło: " `/document/normalized_images/0/imageTags`"Brak wartości."  "Nie można `0` wybrać `/document/pages` tablicy długości." `0` | Jeśli wszystkie dokumenty otrzymują to ostrzeżenie, najprawdopodobniej istnieje literówka w ścieżkach wejściowych i `*` należy dokładnie sprawdzić nazwę właściwości, dodatkowe lub brakuje w ścieżce i upewnij się, że dokumenty ze źródła danych zawierają wymagane dane wejściowe. |
| Wprowadzanie kodu języka umiejętności jest nieprawidłowe | Wprowadzanie `languageCode` umiejętności ma `X,Y,Z`następujące kody języków, z których co najmniej jeden jest nieprawidłowy. | Zobacz więcej szczegółów [poniżej](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Ostrzeżenie: Wprowadzenie umiejętności "languageCode" ma następujące kody języka "X, Y,Z", z których co najmniej jeden jest nieprawidłowy.
Jedna lub więcej wartości przekazanych `languageCode` do opcjonalnego wprowadzania umiejętności niższego rzędu nie jest obsługiwana. Może to nastąpić, jeśli przekazujesz dane wyjściowe [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) do kolejnych umiejętności, a dane wyjściowe składa się z większej liczby języków niż są obsługiwane w tych umiejętności niższego rzędu.

Jeśli wiesz, że twój zestaw danych jest w jednym języku, należy usunąć `languageCode` [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) i wprowadzania umiejętności i użyć parametru `defaultLanguageCode` umiejętności dla tej umiejętności zamiast, zakładając, że język jest obsługiwany dla tej umiejętności.

Jeśli wiesz, że zestaw danych zawiera wiele języków, a zatem potrzebujesz `languageCode` [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) i wprowadzania danych, należy rozważyć dodanie [ConditionalSkill](cognitive-search-skill-conditional.md) odfiltrować tekst z językami, które nie są obsługiwane przed przekazaniem w tekście do niższego rzędu umiejętności.  Oto przykład tego, jak to może wyglądać dla EntityRecognitionSkill:

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

Oto kilka odwołań do aktualnie obsługiwanych języków dla każdej z umiejętności, które mogą powodować ten komunikat o błędzie:
* [Obsługiwane języki analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (dla [keyphraseextractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md)i [PIIDetectionSkill)](cognitive-search-skill-pii-detection.md)
* [Tłumacz obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (dla [Tłumaczenia tekstuSkill](cognitive-search-skill-text-translation.md))
* [Dzielenie tekstu](cognitive-search-skill-textsplit.md) Obsługiwane języki:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Ostrzeżenie: Dane wejściowe umiejętności zostały obcięty
Umiejętności poznawcze mają ograniczenia do długości tekstu, które mogą być analizowane na raz. Jeśli wprowadzanie tekstu tych umiejętności są powyżej tego limitu, będziemy obcinać tekst, aby spełnić limit, a następnie wykonać wzbogacenie na tym obcięty tekst. Oznacza to, że umiejętność jest wykonywana, ale nie nad wszystkimi danymi.

W przykładzie LanguageDetectionSkill `'text'` poniżej pole wejściowe może wyzwolić to ostrzeżenie, jeśli przekracza limit znaków. Limity wprowadzania umiejętności można znaleźć w [dokumentacji umiejętności.](cognitive-search-predefined-skills.md)

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

Jeśli chcesz mieć pewność, że cały tekst jest analizowany, rozważ użycie [umiejętności Podziel](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Ostrzeżenie: Odpowiedź umiejętności web API zawiera ostrzeżenia
Indeksator był w stanie uruchomić umiejętności w skillset, ale odpowiedź z żądania interfejsu API sieci Web wskazane były ostrzeżenia podczas wykonywania. Przejrzyj ostrzeżenia, aby dowiedzieć się, jak wpływa na dane i czy wymagane jest działanie.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Ostrzeżenie: Bieżąca konfiguracja indeksatora nie obsługuje postępu przyrostowego

To ostrzeżenie występuje tylko dla źródeł danych usługi Cosmos DB.

Przyrostowy postęp podczas indeksowania zapewnia, że jeśli wykonanie indeksatora zostanie przerwane przez przejściowe błędy lub limit czasu wykonywania, indeksator może podnieść, gdzie zostało przerwane następnym uruchomieniu, zamiast ponownego indeksowania całej kolekcji od podstaw. Jest to szczególnie ważne podczas indeksowania dużych kolekcji.

Możliwość wznowienia niedokończonego zadania indeksowania opiera się na konieczności `_ts` dokumentów uporządkowanych przez kolumnę. Indeksator używa sygnatury czasowej, aby określić, który dokument ma być pobrany dalej. Jeśli `_ts` brakuje kolumny lub jeśli indeksator nie może określić, czy kwerenda niestandardowa jest uporządkowana przez nią, indeksator rozpoczyna się od początku i zobaczysz to ostrzeżenie.

Istnieje możliwość zastąpienia tego zachowania, włączając postęp przyrostowy i pomijając to `assumeOrderByHighWatermarkColumn` ostrzeżenie przy użyciu właściwości konfiguracji.

Aby uzyskać więcej informacji, zobacz [Postęp przyrostowy i kwerendy niestandardowe](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Ostrzeżenie: Niektóre dane zostały utracone podczas projekcji. Wiersz "X" w tabeli "Y" ma właściwość ciągu "Z", która była zbyt długa.

[Usługa Magazyn tabel](https://azure.microsoft.com/services/storage/tables) ma limity dotyczące właściwości dużych [jednostek.](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) Ciągi mogą mieć 32 000 znaków lub mniej. Jeśli rzutowany jest wiersz z właściwością ciągu dłuższą niż 32 000 znaków, zachowane są tylko pierwsze 32 000 znaków. Aby obejść ten problem, należy unikać rzutowania wierszy z właściwościami ciągu dłuższymi niż 32 000 znaków.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Ostrzeżenie: Obcięty wyodrębniony tekst do znaków X
Indeksatory ograniczają ilość tekstu można wyodrębnić z jednego dokumentu. Ten limit zależy od warstwy cenowej: 32 000 znaków dla warstwy bezpłatnej, 64 000 dla podstawowej, 4 miliony dla standardu, 8 milionów dla standardu S2 i 16 milionów dla standardu S3. Tekst, który został obcięty, nie zostanie zindeksowany. Aby uniknąć tego ostrzeżenia, spróbuj podzielić dokumenty z dużą ilością tekstu na wiele mniejszych dokumentów. 

Aby uzyskać więcej informacji, zobacz [Limity indeksatora](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Ostrzeżenie: Nie można mapować pola wyjściowego "X" do indeksu wyszukiwania
Mapowania pól wyjściowych, które odwołują się do nieistniejących/zerowych danych, będą powodować ostrzeżenia dla każdego dokumentu i powodować powstanie pustego pola indeksu. Aby rozwiązać ten problem, należy dokładnie sprawdzić ścieżki źródłowe mapowania pól wyjściowych pod kątem możliwych literówek lub ustawić wartość domyślną przy użyciu [umiejętności Warunkowe](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Ostrzeżenie: Zasada wykrywania zmian danych jest skonfigurowana do używania kolumny klucza "X"
[Zasady wykrywania zmian danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) mają określone wymagania dotyczące kolumn używanych do wykrywania zmian. Jednym z tych wymagań jest, że ta kolumna jest aktualizowana za każdym razem, gdy element źródłowy jest zmieniany. Innym wymaganiem jest, że nowa wartość dla tej kolumny jest większa niż poprzednia wartość. Kolumny kluczy nie spełniają tego wymogu, ponieważ nie zmieniają się przy każdej aktualizacji. Aby obejść ten problem, wybierz inną kolumnę dla zasad wykrywania zmian.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Ostrzeżenie: Tekst dokumentu wydaje się być zakodowany w trybie UTF-16, ale brakuje znaku zamówienia bajtów

[Tryby analizowania indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) muszą wiedzieć, jak tekst jest zakodowany przed analizowaniem go. Dwa najczęstsze sposoby kodowania tekstu to UTF-16 i UTF-8. UTF-8 jest kodowaniem o zmiennej długości, gdzie każdy znak ma długość od 1 bajtu do 4 bajtów. UTF-16 jest kodowaniem o stałej długości, w którym każdy znak ma długość 2 bajtów. UTF-16 ma dwa różne warianty, "big endian" i "little endian". Kodowanie tekstu jest określane przez "znacznik kolejności bajtów", serię bajtów przed tekstem.

| Kodowanie | Znacznik zamówienia bajtów |
| --- | --- |
| UTF-16 Duży Endian | 0xFE 0xFF |
| UTF-16 Mały Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Jeśli nie ma znaku kolejności bajtów, przyjmuje się, że tekst jest zakodowany jako UTF-8.

Aby obejść to ostrzeżenie, należy określić, co jest kodowanie tekstu dla tego obiektu blob i dodać odpowiedni znacznik kolejności bajtów.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Ostrzeżenie: Kolekcja usługi Cosmos DB "X" ma zasad indeksowania lazy. Niektóre dane mogą zostać utracone

Kolekcje z zasad indeksowania [z opóźnieniem](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) nie można wyszukiwać konsekwentnie, co powoduje, że indeksator brakuje danych. Aby obejść to ostrzeżenie, zmień zasady indeksowania na Spójne.
