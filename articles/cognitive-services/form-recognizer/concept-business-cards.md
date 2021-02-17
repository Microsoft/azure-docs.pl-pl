---
title: Karty biznesowe — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje związane z analizą kart służbowych za pomocą interfejsu API rozpoznawania i limitów czasu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: c2543f74b90205a36d3f5b4481beca35c779f77e
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546027"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>Model wbudowanych kart roboczych aparatu rozpoznawania formularzy 

Aparat rozpoznawania formularzy platformy Azure umożliwia analizowanie i wyodrębnianie informacji kontaktowych z kart służbowych przy użyciu wbudowanego modelu kart służbowych. Łączy ona zaawansowane funkcje optycznego rozpoznawania znaków (OCR) z naszymi kartami biznesowymi, aby wyodrębnić informacje o kluczu z kart służbowych w języku angielskim. Wyodrębnia on osobiste informacje kontaktowe, nazwę firmy, stanowisko i nie tylko. Prebudowany interfejs API kart służbowych jest publicznie dostępny w postaci aparatu rozpoznawania w wersji zapoznawczej. 

## <a name="what-does-the-business-card-service-do"></a>Co robi usługa wizytówek?

Prebudowany interfejs API kart służbowych wyodrębnia pola kluczy z kart służbowych i zwraca je w zorganizowanej odpowiedzi JSON.

![Obraz przedstawiający elementy contoso z FOTT + dane wyjściowe JSON](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>Wyodrębnione pola:

|Nazwa| Typ | Opis | Tekst | 
|:-----|:----|:----|:----|
| Contacters | Tablica obiektów | Nazwa kontaktu wyodrębniona z karty biznesowej | [{"FirstName": "Jan", "LastName": "Nowak"}] |
| FirstName (Imię) | ciąg | Imię i nazwisko osoby kontaktowej | Zawierają | 
| LastName (Nazwisko) | ciąg | Nazwisko osoby kontaktowej (rodzina) |     Kowalski | 
| CompanyNames | tablica ciągów | Nazwa firmy wyodrębniona z karty biznesowej | ["Contoso"] | 
| Działy | tablica ciągów | Dział lub organizacja kontaktu | ["R&D"] | 
| JobTitles | tablica ciągów | Lista tytułów zadań kontaktu | ["Inżynieria oprogramowania"] | 
| Wiadomości e-mail | tablica ciągów | Kontaktowy adres e-mail wyodrębniony z karty biznesowej | ["johndoe@contoso.com"] | 
| Witryny internetowe | tablica ciągów | Witryna internetowa wyodrębniona z karty biznesowej | ["https://www.contoso.com"] | 
| Adresy | tablica ciągów | Adres wyodrębniony z karty biznesowej | ["123 główna ulica, Redmond, WA 98052"] | 
| MobilePhones | Tablica numerów telefonów | Numer telefonu komórkowego wyodrębniony z karty biznesowej | ["+ 19876543210"] |
| Faksów | Tablica numerów telefonów | Numer telefonu faksu wyodrębniony z karty biznesowej | ["+ 19876543211"] |
| WorkPhones | Tablica numerów telefonów | Numer telefonu służbowego wyodrębniony z karty biznesowej | ["+ 19876543231"] |
| OtherPhones     | Tablica numerów telefonów | Inny numer telefonu wyodrębniony z karty biznesowej | ["+ 19876543233"] |


Interfejs API wizytówek może również zwrócić cały rozpoznany tekst z karty biznesowej. To dane wyjściowe OCR są zawarte w odpowiedzi JSON.  

### <a name="input-requirements"></a>Wymagania wejściowe 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>Operacja analizowania karty biznesowej

[Karta analizy biznesowej](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) pobiera obraz lub plik PDF karty biznesowej jako dane wejściowe i wyodrębnia interesujące wartości. Wywołanie zwraca pole nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator wynik do użycia w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>Operacja uzyskiwania wyniku analizy karty biznesowej

Drugim krokiem jest wywołanie operacji [Pobierz wyniki analizy karty biznesowej](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult) . Ta operacja przyjmuje jako dane wejściowe Identyfikator wyniku, który został utworzony przez operację Analizuj kartę biznesową. Zwraca odpowiedź JSON, która zawiera pole **stanu** z następującymi możliwymi wartościami. Tę operację można wywołać iteracyjnie, dopóki nie zwróci wartości z wartością **sukces** . Użyj interwału od 3 do 5 sekund, aby uniknąć przekroczenia liczby żądań na sekundę (RPS pliku).

|Pole| Typ | Możliwe wartości |
|:-----|:----:|:----|
|status | ciąg | notStarted: operacja analizy nie została rozpoczęta.<br /><br />Uruchamianie: operacja analizy jest w toku.<br /><br />Niepowodzenie: operacja analizy zakończyła się niepowodzeniem.<br /><br />powodzenie: operacja analizy zakończyła się pomyślnie.|

W przypadku wartości pola **stan** **zakończyła się pomyślnie** , odpowiedź JSON będzie zawierać informacje o zrozumieniu karty biznesowej i opcjonalnych wynikach rozpoznawania tekstu, jeśli jest to wymagane. Wynik zrozumienie karty biznesowej jest zorganizowany jako słownik nazwanych wartości pól, gdzie każda wartość zawiera wyodrębniony tekst, znormalizowana wartość, pole ograniczenia, pewność i odpowiadające im elementy programu Word. Wynik rozpoznawania tekstu jest zorganizowany jako hierarchia wierszy i słów, z tekstem, obwiednią i informacjami o ufności.

![Przykładowe dane wyjściowe karty biznesowej](./media/business-card-results.png)

### <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON

Odpowiedź na operację uzyskiwania wyników na karcie analiza biznesowa jest strukturalną reprezentacją wizytówki zawierającej wszystkie wyodrębnione informacje.  Zobacz tutaj, aby zapoznać się z [przykładowym plikiem karty biznesowej](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-english.jpg) i danymi wyjściowymi przykładowe dane wyjściowe [karty biznesowej](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

Zobacz następujący przykład pomyślnej odpowiedzi JSON:
* `"readResults"`Węzeł zawiera cały rozpoznany tekst. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. 
* `"documentResults"`Węzeł zawiera wartości specyficzne dla karty biznesowej, które zostały odnalezione przez model. W tym miejscu znajdziesz przydatne informacje kontaktowe, takie jak imię i nazwisko, nazwisko, nazwa firmy itd.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Postępuj zgodnie z przewodnikiem [Szybki Start, aby](./QuickStarts/client-library.md) zaimplementować wyodrębnianie danych z kart służbowych przy użyciu języka Python i interfejsu API REST.

## <a name="customer-scenarios"></a>Scenariusze klientów  

Dane wyodrębnione za pomocą interfejsu API kart służbowych mogą służyć do wykonywania różnych zadań. Wyodrębnienie tych informacji kontaktowych powoduje automatyczne zaoszczędzenie czasu dla tych w rolach związanych z klientem. Poniżej przedstawiono kilka przykładów naszych klientów korzystających z interfejsu API wizytówek:

* Wyodrębnij informacje kontaktowe z kart służbowych i szybko Twórz kontakty telefoniczne. 
* Integruj z programem CRM, aby automatycznie tworzyć kontakty przy użyciu obrazów kart służbowych. 
* Śledź potencjalne wyniki sprzedaży.  
* Pobieraj zbiorczo informacje kontaktowe z istniejących obrazów kart służbowych. 

Interfejs API kart służbowych zapewnia również [funkcję przetwarzania karty biznesowej programu AI Builder](/ai-builder/prebuilt-business-card).

## <a name="next-steps"></a>Następne kroki

- Postępuj zgodnie z [przewodnikiem Szybki Start](./quickstarts/client-library.md) , aby rozpocząć rozpoznawanie kart służbowych.

## <a name="see-also"></a>Zobacz też

* [Co to jest rozpoznawanie formularzy?](./overview.md)
* [Dokumentacja interfejsu API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
