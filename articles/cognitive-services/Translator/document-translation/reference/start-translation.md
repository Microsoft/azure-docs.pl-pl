---
title: Rozpoczynanie tłumaczenia
titleSuffix: Azure Cognitive Services
description: Uruchom żądanie tłumaczenia dokumentu za pomocą usługi tłumaczenia dokumentów.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 1d167962e22953a4a9ca69ece347f8427b9218c9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836314"
---
# <a name="start-translation"></a>Rozpoczynanie tłumaczenia

Użyj tego interfejsu API, aby rozpocząć zbiorcze (wsadowe) żądanie tłumaczenia za pomocą usługi tłumaczenia dokumentów. Każde żądanie może zawierać wiele dokumentów i musi zawierać kontener źródłowy i docelowy dla każdego dokumentu.

Filtr prefiksu i sufiksu (jeśli został podany) służy do filtrowania folderów. Prefiks jest stosowany do ścieżki podrzędnej po nazwie kontenera.

Słowniki/pamięć tłumaczenia mogą zostać uwzględnione w żądaniu i są stosowane przez usługę podczas tłumaczenia dokumentu.

Jeśli słownik jest nieprawidłowy lub nieosiągalny podczas tłumaczenia, w stanie dokumentu jest wyświetlany błąd. Jeśli plik o tej samej nazwie już istnieje w miejscu docelowym, zostanie zastąpiony. Wartość targetUrl dla każdego języka docelowego musi być unikatowa.

## <a name="request-url"></a>Adres URL żądania

Wyślij `POST` żądanie do:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Dowiedz się, jak znaleźć [niestandardową nazwę domeny.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Wszystkie żądania interfejsu API do usługi tłumaczenia dokumentów wymagają niestandardowego punktu końcowego domeny**.
> * Nie można użyć punktu końcowego znalezionego na  stronie kluczy Azure Portal punktu końcowego ani globalnego punktu końcowego translatora — do przesyłania żądań HTTP do `api.cognitive.microsofttranslator.com` tłumaczenia dokumentów.

## <a name="request-headers"></a>Nagłówki żądań

Nagłówki żądań to:

|Nagłówki|Opis|
|--- |--- |
|Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania|

## <a name="request-body-batch-submission-request"></a>Treść żądania: żądanie przesłania wsadowego

|Nazwa|Typ|Opis|
|--- |--- |--- |
|Wejścia|BatchRequest[]|BatchRequest wymienione poniżej. Lista wejściowa dokumentów lub folderów zawierających dokumenty. Typy multimediów: "application/json", "text/json", "application/*+json".|

### <a name="inputs"></a>Dane wejściowe

Definicja wejściowego żądania tłumaczenia wsadowego.

|Nazwa|Typ|Wymagane|Opis|
|--- |--- |--- |--- |
|source|SourceInput[]|Prawda|inputs.source wymienione poniżej. Źródło dokumentów wejściowych.|
|typ magazynu|StorageInputType[]|Prawda|Inputs.storageType wymieniony poniżej. Typ magazynu ciągu źródłowego dokumentów wejściowych.|
|Cele|TargetInput[]|Prawda|inputs.target wymienione poniżej. Lokalizacja miejsca docelowego dla danych wyjściowych.|

**inputs.source**

Źródło dokumentów wejściowych.

|Nazwa|Typ|Wymagane|Opis|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Fałsz|DocumentFilter[] wymieniony poniżej.|
|filter.prefix|ciąg|Fałsz|Ciąg prefiksu z wielkością liter do filtrowania dokumentów w ścieżce źródłowej w celu tłumaczenia. Na przykład w przypadku korzystania z identyfikatora URI obiektu blob usługi Azure Storage użyj prefiksu , aby ograniczyć podfolderów do tłumaczenia.|
|filter.suffix|ciąg|Fałsz|Ciąg sufiksu z wielkością liter do filtrowania dokumentów w ścieżce źródłowej w celu tłumaczenia. Jest to najczęściej używane w przypadku rozszerzeń plików.|
|language|ciąg|Fałsz|Kod języka Jeśli nie zostanie określony, wykonamy automatyczne wykrywanie dokumentu.|
|sourceUrl|ciąg|True|Lokalizacja folderu/kontenera lub pojedynczego pliku z dokumentami.|
|storageSource|Źródło usługi Storage|Fałsz|Źródło magazynu wymienione poniżej.|
|storageSource.AzureBlob|ciąg|Fałsz||

**inputs.storageType**

Typ magazynu ciągu źródłowego dokumentów wejściowych.

|Nazwa|Typ|
|--- |--- |
| — plik|ciąg|
|folder|ciąg|

**inputs.target**

Miejsce docelowe dla ukończonych przetłumaczonych dokumentów.

|Nazwa|Typ|Wymagane|Opis|
|--- |--- |--- |--- |
|category|ciąg|Fałsz|Kategoria/system niestandardowy dla żądania tłumaczenia.|
|Glosariusze|Słownik[]|Fałsz|Słownik wymieniony poniżej. Lista słownika.|
|glossaries.format|ciąg|Fałsz|Formacie.|
|glossaries.glossaryUrl|ciąg|True (w przypadku używania słowników)|Lokalizacja słownika. Użyjemy rozszerzenia pliku do wyodrębnienia formatowania, jeśli parametr formatu nie zostanie podany. Jeśli para języków tłumaczenia nie jest obecna w słowniku, nie zostanie zastosowana.|
|glossaries.storageSource|Źródło magazynu|Fałsz|MagazynŹródło wymienione powyżej.|
|wartość targetUrl|ciąg|True|Lokalizacja folderu/kontenera z dokumentami.|
|language|ciąg|True|Dwulicieowy kod języka docelowego. Zobacz [listę kodów języków.](../../language-support.md)|
|magazynŹródło|StorageSource []|Fałsz|Źródło Magazynu [] wymienione powyżej.|
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

**Tłumaczenie wszystkich dokumentów w kontenerze z zastosowaniem słowników**

Upewnij się, że utworzono adres URL słownika & sas tokenu dla określonego obiektu blob/dokumentu (nie dla kontenera)

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

Upewnij się, że w filtrze określono nazwę folderu (z wielkością liter) jako prefiks — mimo że token SAS nadal jest dla kontenera.

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

* Upewnij się, że określono wartość "storageType": "File"
* Upewnij się, że utworzono źródłowy adres URL & sas tokenu dla określonego obiektu blob/dokumentu (nie dla kontenera)
* Upewnij się, że określono docelową nazwę pliku jako część docelowego adresu URL, chociaż token SAS nadal jest dla kontenera.
* Przykładowe żądanie poniżej pokazuje, że pojedynczy dokument jest tłumaczony na dwa języki docelowe

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

Poniżej przedstawiono możliwe kody stanu HTTP zwracane przez żądanie.

|Kod stanu|Opis|
|--- |--- |
|202|Akceptowane. Żądanie pomyślne i żądanie wsadowe są tworzone przez usługę. Nagłówek Operation-Location adres URL stanu z ciągiem operation ID.HeadersOperation-Location:|
|400|Złe żądanie. Nieprawidłowe żądanie. Sprawdź parametry wejściowe.|
|401|Nieautoryzowanych. Sprawdź swoje poświadczenia.|
|429|Szybkość żądań jest zbyt duża.|
|500|Wewnętrzny błąd serwera.|
|503|Usługa jest obecnie niedostępna.  Spróbuj ponownie później.|
|Inne kody stanu|<ul><li>Zbyt wiele żądań</li><li>Serwer jest tymczasowo niedostępny</li></ul>|

## <a name="error-response"></a>Odpowiedź z błędem

|Nazwa|Typ|Opis|
|--- |--- |--- |
|kod|ciąg|Wylinia zawierające kody błędów wysokiego poziomu. Możliwe wartości:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Brak autoryzacji</li></ul>|
|message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|
|innerError|InnerErrorV2|Nowy format błędu wewnętrznego, który jest zgodny z Cognitive Services API. Zawiera wymagane właściwości ErrorCode, komunikat i opcjonalny element docelowy właściwości, szczegóły (para wartości klucza), błąd wewnętrzny (może być zagnieżdżony).|
|Wewnętrzny. Errorcode|ciąg|Pobiera ciąg błędu kodu.|
|innerError.message|ciąg|Pobiera komunikat o błędzie wysokiego poziomu.|

## <a name="examples"></a>Przykłady

### <a name="example-successful-response"></a>Przykład pomyślnej odpowiedzi

Po pomyślnym zwróceniu odpowiedzi są zwracane następujące informacje.

Identyfikator zadania można znaleźć w nagłówku odpowiedzi metody POST Operation-Location URL. Ostatnim parametrem adresu URL jest identyfikator zadania operacji (ciąg "/operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Przykładowa odpowiedź o błędzie

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

Postępuj zgodnie z naszymi przewodnikami Szybki start, aby dowiedzieć się więcej na temat korzystania z tłumaczenia dokumentów i biblioteki klienta.

> [!div class="nextstepaction"]
> [Wprowadzenie do tłumaczenia dokumentów](../get-started-with-document-translation.md)
