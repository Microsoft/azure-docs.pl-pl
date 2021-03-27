---
title: Żądanie przetwarzania wsadowego przesyłania tłumaczenia dokumentów
titleSuffix: Azure Cognitive Services
description: Prześlij żądanie tłumaczenia dokumentu do usługi tłumaczenia dokumentów.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 803a0b9f4496a3d785aa6f22833fee4ae6eca6e0
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613094"
---
# <a name="document-translation-submit-batch-request"></a>Tłumaczenie dokumentu: Prześlij żądanie wsadowe

Ten interfejs API służy do przesyłania zbiorczego żądania tłumaczenia do usługi tłumaczenia dokumentów. Każde żądanie może zawierać wiele dokumentów i musi zawierać kontener źródłowy i docelowy dla każdego dokumentu.

Prefiks i filtr sufiksu (jeśli jest podany) są używane do filtrowania folderów. Prefiks jest stosowany do ścieżki podrzędnej po nazwie kontenera.

Pamięć Glossaries/translation może być uwzględniona w żądaniu i są stosowane przez usługę podczas tłumaczenia dokumentu.

Jeśli słownik jest nieprawidłowy lub nieosiągalny podczas tłumaczenia, w stanie dokumentu jest wskazywany błąd. Jeśli plik o tej samej nazwie już istnieje w miejscu docelowym, zostanie zastąpiony. Docelowy adres URL dla każdego języka docelowego muszą być unikatowe.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie do:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Dowiedz się, jak znaleźć swoją [niestandardową nazwę domeny](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na stronie klucze zasobów Azure Portal _i punktu_ końcowego ani globalnego punktu końcowego usługi Translator — `api.cognitive.microsofttranslator.com` w celu żądania protokołu HTTP do tłumaczenia dokumentów.

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="request-body-batch-submission-request"></a>Treść żądania: żądanie przesłania wsadowego

|Nazwa|Typ|Opis|
|--- |--- |--- |
|danych wejściowych|BatchRequest[]|BatchRequest wymienione poniżej. Lista wejściowa dokumentów lub folderów zawierających dokumenty. Typy nośników: "Application/JSON", "text/JSON", "Application/* + JSON".|

### <a name="inputs"></a>Dane wejściowe

Definicja żądania tłumaczenia wsadowego.

|Nazwa|Typ|Wymagane|Opis|
|--- |--- |--- |--- |
|source|SourceInput[]|Prawda|dane wejściowe. Źródło wymienione poniżej. Źródło dokumentów wejściowych.|
|storageType|StorageInputType[]|Prawda|dane wejściowe. StorageType wymienione poniżej. Typ magazynu ciągu źródłowego dokumentów wejściowych.|
|celach|TargetInput[]|Prawda|dane wejściowe. Target wymienione poniżej. Lokalizacja miejsca docelowego dla danych wyjściowych.|

**dane wejściowe. Źródło**

Źródło dokumentów wejściowych.

|Nazwa|Typ|Wymagane|Opis|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Fałsz|DocumentFilter [] wymienione poniżej.|
|Filter. prefix|ciąg|Fałsz|Ciąg prefiksu z rozróżnianą wielkością liter, który umożliwia filtrowanie dokumentów w ścieżce źródłowej na potrzeby tłumaczenia. Na przykład w przypadku korzystania z identyfikatora URI obiektu BLOB usługi Azure Storage Użyj prefiksu, aby ograniczyć podfoldery do translacji.|
|Filter. sufiks|ciąg|Fałsz|Ciąg sufiksu z rozróżnianą wielkością liter, który umożliwia filtrowanie dokumentów w ścieżce źródłowej na potrzeby tłumaczenia. Jest to najczęściej używane w przypadku rozszerzeń plików.|
|language|ciąg|Fałsz|Kod języka jeśli nie zostanie określony, zostanie przeprowadzone automatyczne wykrywanie dokumentu.|
|sourceUrl|ciąg|True|Lokalizacja folderu/kontenera lub pojedynczego pliku z dokumentami.|
|storageSource|StorageSource|Fałsz|StorageSource wymienione poniżej.|
|storageSource. AzureBlob|ciąg|Fałsz||

**Inputs. StorageType**

Typ magazynu ciągu źródłowego dokumentów wejściowych.

|Nazwa|Typ|
|--- |--- |
| — plik|ciąg|
|folder|ciąg|

**Inputs. Target**

Miejsce docelowe dla zakończonych przetłumaczonych dokumentów.

|Nazwa|Typ|Wymagane|Opis|
|--- |--- |--- |--- |
|category|ciąg|Fałsz|Kategoria/System niestandardowy dla żądania tłumaczenia.|
|glossaries|Słownik []|Fałsz|Słownik wymieniony poniżej. Lista słowników.|
|Glossaries. format|ciąg|Fałsz|Formatowanie.|
|glossaries.glossaryUrl|ciąg|True (jeśli jest używany Glossaries)|Lokalizacja słownika. Jeśli parametr format nie zostanie podany, użyjemy rozszerzenia pliku. Jeśli w słowniku nie ma pary Language translation, nie zostanie ona zastosowana.|
|glossaries.storageSource|StorageSource|Fałsz|StorageSource wymienione powyżej.|
|Docelowy adres URL|ciąg|True|Lokalizacja folderu/kontenera z dokumentami.|
|language|ciąg|True|Dwuliterowy kod języka docelowego. Zobacz [listę kodów języka](../../language-support.md).|
|storageSource|StorageSource []|Fałsz|StorageSource [] wymienione powyżej.|
|Wersja|ciąg|Fałsz|Wersja.|

## <a name="example-request"></a>Przykładowe żądanie

Poniżej przedstawiono przykłady żądań wsadowych.

**Tłumaczenie wszystkich dokumentów w kontenerze**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Tłumaczenie wszystkich dokumentów w kontenerze stosując Glossaries**

Upewnij się, że utworzono adres URL słownika & token sygnatury dostępu współdzielonego dla określonego obiektu blob/dokumentu (nie dla kontenera)

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Tłumaczenie określonego folderu w kontenerze**

Upewnij się, że określono nazwę folderu (z uwzględnieniem wielkości liter) jako prefiks w filtrze, chociaż token sygnatury dostępu współdzielonego nadal znajduje się w kontenerze.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Tłumaczenie określonego dokumentu w kontenerze**

* Upewnij się, że określono "StorageType": "File"
* Upewnij się, że został utworzony źródłowy adres URL & token SAS dla określonego obiektu blob/dokumentu (nie dla kontenera)
* Upewnij się, że określono nazwę pliku docelowego jako część docelowego adresu URL — mimo że token sygnatury dostępu współdzielonego jest nadal dla kontenera.
* Przykładowe żądanie poniżej pokazuje pojedynczy dokument możliwy do przetłumaczenia na dwa języki docelowe

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Kody stanu odpowiedzi

Oto możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|202|Przyjmować. Pomyślne żądanie i żądanie usługi Batch są tworzone przez usługę. Nagłówek Operation-Location będzie wskazywać adres URL stanu z IDENTYFIKATORem operacji. HeadersOperation-Location: String|
|400|Nieprawidłowe żądanie. Nieprawidłowe żądanie. Sprawdź parametry wejściowe.|
|401|Próby. Sprawdź swoje poświadczenia.|
|429|Częstotliwość żądań jest zbyt duża.|
|500|Wewnętrzny błąd serwera.|
|503|Usługa jest obecnie niedostępna.  Spróbuj ponownie później.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer tymczasowy niedostępny</li></ul>|

## <a name="error-response"></a>Odpowiedź na błąd

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wyliczenia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny ze wskazówkami dotyczącymi interfejsu API Cognitive Services. Zawiera wymagane właściwości ErrorCode, komunikat i opcjonalne właściwości target, szczegóły (para klucz wartość), błąd wewnętrzny (może to być zagnieżdżone).|
|fabryk. Kodzie|ciąg|Pobiera ciąg błędu kodu.|
|innerError. Message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi

Poniższe informacje są zwracane w odpowiedzi na pomyślne.

Identyfikator zadania można znaleźć w nagłówku odpowiedzi metody POST Operation-Location wartość adresu URL. Ostatni parametr adresu URL to identyfikator zadania operacji (ciąg następujący: "/Operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Przykładowa odpowiedź na błąd

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

Skorzystaj z naszego przewodnika Szybki Start, aby dowiedzieć się więcej o korzystaniu z tłumaczenia dokumentów i biblioteki klienckiej.

> [!div class="nextstepaction"]
> [Wprowadzenie do tłumaczenia dokumentu](../get-started-with-document-translation.md)
