---
title: 'Szybki Start: wyodrębnianie danych przyjęcia przy użyciu narzędzia do rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem, aby wyodrębnić dane z obrazów przyjęć sprzedaży w USA.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 4fa31a5be41e89c8fdc821ae77ff151b184316df
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88518020"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Szybki Start: wyodrębnianie danych o odbiorze przy użyciu interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem

W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy platformy Azure z zwinięciem, aby wyodrębnić i zidentyfikować odpowiednie informacje z przyjęć sprzedaży w USA.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- zainstalowano [zwinięcie](https://curl.haxx.se/windows/) .
- Adres URL obrazu potwierdzenia. Możesz użyć [przykładowego obrazu](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) dla tego przewodnika Szybki Start.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analizowanie paragonu

Aby rozpocząć analizowanie paragonu, należy wywołać interfejs API **[analizy paragonów](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** przy użyciu poniższego polecenia. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `<your receipt URL>` na adres URL obrazu paragonu.
1. Zamień `<subscription key>` na klucz subskrypcji skopiowany z poprzedniego kroku.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \"<your receipt URL>\"}"
```

Otrzymasz odpowiedź obejmującą `202 (Success)` nagłówek **operacji** am. Wartość tego nagłówka zawiera identyfikator operacji, którego można użyć do zbadania stanu operacji asynchronicznej i uzyskania wyników. W poniższym przykładzie ciąg po `operations/` to identyfikator operacji.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Pobierz wyniki odbioru

Po wywołaniu interfejsu API **Analizowanie paragonów** należy wywołać interfejs API **[wyników uzyskiwania analizy przychodu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** , aby uzyskać stan operacji i wyodrębnionych danych. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany za pomocą klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień na `<operationId>` Identyfikator operacji z poprzedniego kroku.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz `200 (Success)` odpowiedź z danymi wyjściowymi JSON. Pierwsze pole, `"status"` , wskazuje stan operacji. Jeśli operacja zostanie ukończona, `"recognitionResults"` pole zawiera wszystkie wiersze tekstu wyodrębnione z paragonu, a `"understandingResults"` pole zawiera informacje o kluczu/wartości dla najbardziej odpowiednich części paragonu. Jeśli operacja nie zostanie ukończona, wartość `"status"` będzie `"running"` lub `"notStarted"` i należy ponownie wywołać interfejs API, ręcznie lub za pomocą skryptu. Zalecamy przedziału co najmniej jednej sekundy między wywołaniami.

Zapoznaj się z poniższym obrazem paragonu i odpowiednimi danymi wyjściowymi JSON. Dane wyjściowe zostały skrócone w celu zapewnienia czytelności.

![Potwierdzenie ze sklepu contoso](../media/contoso-allinone.jpg)

`"recognitionResults"`Węzeł zawiera cały rozpoznany tekst. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. `"understandingResults"`Węzeł zawiera wartości specyficzne dla paragonu wykryte przez model. Tutaj znajdziesz przydatne pary klucz/wartość, takie jak podatek, łączny, adres handlowca itd.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.0.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem, aby wyodrębnić zawartość paragonu sprzedaży. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
