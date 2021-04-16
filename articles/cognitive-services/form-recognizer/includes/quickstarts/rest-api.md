---
title: 'Szybki start: Rozpoznawanie formularzy API REST'
description: Użyj interfejsu API REST Rozpoznawanie formularzy aplikacji do przetwarzania formularzy, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: 8f729d3d2ebc41552919634c68557042a95649ec
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516462"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> W tym przewodniku do wykonywania wywołań interfejsu API REST jest używany program cURL.

| [Rozpoznawanie formularzy API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) | REST [Dokumentacja interfejsu API REST platformy](/rest/api/azure/) | Azure [Przykłady](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer)|

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstalowany program cURL.](https://curl.haxx.se/windows/)
* [Program PowerShell w wersji 6.0+](/powershell/scripting/install/installing-powershell-core-on-windows)lub podobnej aplikacji wiersza polecenia.
* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Obiekt blob usługi Azure Storage zawierający zestaw danych szkoleniowych. Aby uzyskać porady i opcje dotyczące budowania zestawu danych treningowych, zobacz Build a training data [set for a custom model](../../build-training-data-set.md) (Tworzenie zestawu danych treningowych dla modelu niestandardowego). W tym przewodniku Szybki start możesz użyć plików w folderze **Train** przykładowego zestawu danych [(pobierz](https://go.microsoft.com/fwlink/?linkid=2090451) i *wyodrębnij* sample_data.zip).
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Rozpoznawanie formularzy zasobów Rozpoznawanie formularzy w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
  * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z Rozpoznawanie formularzy API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
  * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.
* Adres URL obrazu paragonu. W tym przewodniku Szybki [start możesz użyć](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) przykładowego obrazu.
* Adres URL obrazu wizytówki. W tym przewodniku Szybki [start możesz użyć](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) przykładowego obrazu.
* Adres URL obrazu faktury. W tym [przewodniku](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) Szybki start możesz użyć przykładowego dokumentu.

## <a name="analyze-layout"></a>Analizowanie układu

Za pomocą Rozpoznawanie formularzy do analizowania i wyodrębniania tabel, znaczników wyboru, tekstu i struktury w dokumentach bez konieczności trenowania modelu. Aby uzyskać więcej informacji na temat wyodrębniania układu, zobacz [Przewodnik koncepcyjny układu](../../concept-layout.md). Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{subscription key}` element kluczem subskrypcji skopiowanym w poprzednim kroku.
1. `\"{your-document-url}`Zamień na jeden z przykładowych adresów URL.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

Otrzymasz odpowiedź, która zawiera nagłówek `202 (Success)` am **Operation-Location.** Wartość tego nagłówka zawiera identyfikator operacji, który umożliwia wykonywanie zapytań o stan operacji asynchronicznej i uzyskiwanie wyników. W poniższym przykładzie ciąg po `analyzeResults/` jest identyfikatorem operacji.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Uzyskiwanie wyników układu

Po wywołaniu interfejsu API analizowania **[układu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)** wywołasz interfejs **[API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult)** Pobierz wynik analizy układu, aby uzyskać stan operacji i wyodrębnione dane. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{subscription key}` element kluczem subskrypcji skopiowanym w poprzednim kroku.
1. Zastąp `{resultId}` identyfikatorem operacji z poprzedniego kroku.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>Sprawdzanie wyników

Otrzymasz odpowiedź z `200 (success)` zawartością JSON.

Zobacz poniższy obraz faktury i odpowiadające jej dane wyjściowe JSON.
* Węzeł zawiera każdy wiersz tekstu z odpowiednim rozmieszczeniem `"readResults"` pola granicznego na stronie.
* Węzeł (w wersji 2.1 w wersji zapoznawczej) wyświetla każdy znacznik wyboru (pole wyboru, znacznik radiowy) i określa, czy jego stan to `"selectionMarks"` "selected" (wybrane), czy "unselected".
* Sekcja `"pageResults"` zawiera wyodrębnione tabele. Dla każdej tabeli wyodrębniony jest indeks tekstu, wiersza i kolumny, chłonianie wierszy i kolumn, pole granicy i inne.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Dokument instrukcji projektu firmy Contoso z tabelą.":::

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

Te dane wyjściowe zostały skrócone dla uproszczenia. Zobacz pełne [przykładowe dane wyjściowe w witrynie GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

Te dane wyjściowe zostały skrócone dla uproszczenia. Zobacz pełne [przykładowe dane wyjściowe w witrynie GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

---

## <a name="analyze-receipts"></a>Analizowanie paragonów

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z paragonów w USA przy użyciu wstępnie wytrenowanych modeli paragonów. Aby uzyskać więcej informacji na temat analizy paragonów, zobacz Przewodnik koncepcyjny [dotyczący paragonów](../../concept-receipts.md). Aby rozpocząć analizowanie paragonu, wywołaj interfejs API **[analizowania paragonu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)** przy użyciu poniższego polecenia cURL. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{your receipt URL}` adresem URL obrazu potwierdzenia.
1. Zastąp `{subscription key>` element kluczem subskrypcji skopiowanym w poprzednim kroku.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

Otrzymasz odpowiedź, która zawiera nagłówek `202 (Success)` am **Operation-Location.** Wartość tego nagłówka zawiera identyfikator operacji, który umożliwia wykonywanie zapytań o stan operacji asynchronicznej i uzyskiwanie wyników. W poniższym przykładzie ciąg po `operations/` jest identyfikatorem operacji.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Uzyskiwanie wyników paragonu

Po wywołaniu interfejsu API analizowania **paragonu** wywołaj interfejs **[API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult)** Pobierz wynik paragonu analizy, aby uzyskać stan operacji i wyodrębnione dane. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` element punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji. Można go znaleźć na karcie Przegląd Rozpoznawanie formularzy **zasobów.**
1. Zastąp `{operationId}` identyfikatorem operacji z poprzedniego kroku.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz odpowiedź z danych `200 (Success)` wyjściowych JSON. Pierwsze `"status"` pole, , wskazuje stan operacji. Jeśli operacja nie zostanie ukończona, wartością będzie lub , a interfejs API należy wywołać ponownie `"status"` ręcznie lub za pomocą `"running"` `"notStarted"` skryptu. Zalecamy interwał co najmniej jednej sekundy między wywołaniami.

Węzeł `"readResults"` zawiera cały rozpoznany tekst (jeśli opcjonalny parametr *includeTextDetails* zostanie ustawiony na `true` wartość ). Tekst jest zorganizowany według strony, następnie według wiersza, a następnie według poszczególnych wyrazów. Węzeł `"documentResults"` zawiera wartości specyficzne dla paragonu odnalezione przez model. W tym miejscu znajdziesz przydatne pary klucz/wartość, takie jak podatek, suma, adres sprzedawcy itp.

Zobacz poniższy obraz paragonu i odpowiednie dane wyjściowe JSON.

![Potwierdzenie ze sklepu Contoso](../../media/contoso-allinone.jpg)

Te dane wyjściowe zostały skrócone w celu ich czytelności. Zobacz pełne [przykładowe dane wyjściowe w witrynie GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json)

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
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

## <a name="analyze-business-cards"></a>Analizowanie wizytówek

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z angielskich wizytówek przy użyciu wstępnie wytrenowany model. Aby uzyskać więcej informacji na temat analizy wizytówek, zobacz Przewodnik koncepcyjny [dotyczący wizytówek.](../../concept-business-cards.md) Aby rozpocząć analizowanie wizytówki, wywołaj interfejs API analizowania **[wizytówki](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)** przy użyciu poniższego polecenia cURL. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{your business card URL}` adresem URL obrazu potwierdzenia.
1. Zastąp `{subscription key}` element kluczem subskrypcji skopiowanym w poprzednim kroku.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

Otrzymasz odpowiedź, która zawiera nagłówek `202 (Success)` am **Operation-Location.** Wartość tego nagłówka zawiera identyfikator operacji, który umożliwia wykonywanie zapytań o stan operacji asynchronicznej i uzyskiwanie wyników.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Uzyskiwanie wyników wizytówki

Po wywołaniu interfejsu **API** analizowania wizytówki wywołaj interfejs **[API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult)** wyników analizy wizytówki, aby uzyskać stan operacji i wyodrębnione dane. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` element punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji. Można go znaleźć na karcie Przegląd Rozpoznawanie formularzy **zasobów.**
1. Zastąp `{resultId}` identyfikatorem operacji z poprzedniego kroku.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz odpowiedź z danych `200 (Success)` wyjściowych JSON.

Węzeł `"readResults"` zawiera cały rozpoznany tekst. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie według poszczególnych wyrazów. Węzeł zawiera wartości specyficzne dla `"documentResults"` wizytówki odnalezione przez model. W tym miejscu znajdziesz przydatne informacje kontaktowe, takie jak imię, imię, nazwisko, numer telefonu i tak dalej.

![Wizytówka firmy Contoso](../../media/business-card-english.jpg)

Te przykładowe dane wyjściowe JSON zostały skrócone w celu ich czytelności. Zobacz pełne [przykładowe dane wyjściowe w witrynie GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
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
                                "confidence": 0.993
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
                    }
                }
            }
        ]
    }
}
```

Skrypt będzie drukować odpowiedzi w konsoli do **momentu** ukończenia operacji Analizowanie wizytówki.

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="analyze-invoices"></a>Analizowanie faktur

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

Aby rozpocząć analizowanie faktury, użyj poniższego polecenia cURL. Aby uzyskać więcej informacji na temat analizy faktur, zobacz [Przewodnik koncepcyjny dotyczący faktur](../../concept-invoices.md). Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{your invoice URL}` element adresem URL dokumentu faktury.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

Otrzymasz odpowiedź, która zawiera nagłówek `202 (Success)` am **Operation-Location.** Wartość tego nagłówka zawiera identyfikator operacji, który umożliwia wykonywanie zapytań o stan operacji asynchronicznej i uzyskiwanie wyników.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Uzyskiwanie wyników faktury

Po wywołaniu interfejsu API analizowania **[faktur](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)** wywołasz interfejs **[API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83)** pobierz analizę wyniku faktury, aby uzyskać stan operacji i wyodrębnione dane. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` element punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji. Można go znaleźć na karcie Przegląd Rozpoznawanie formularzy **zasobów.**
1. Zastąp `{resultId}` identyfikatorem operacji z poprzedniego kroku.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz odpowiedź z danych `200 (Success)` wyjściowych JSON.

* Pole `"readResults"` zawiera każdy wiersz tekstu, który został wyodrębniony z faktury.
* Zawiera `"pageResults"` tabele i znaczniki wyboru wyodrębnione z faktury.
* Pole `"documentResults"` zawiera informacje o kluczu/wartości dla najbardziej odpowiednich części faktury.

Zobacz następujący dokument faktury i odpowiadające mu dane wyjściowe JSON.

* [Przykładowa faktura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

Ta zawartość JSON została skrócona, aby była czytelna. Zobacz pełne [przykładowe dane wyjściowe w witrynie GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...
            }
        }]
    }
}
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="analyze-id-document"></a>Analizowanie dokumentu identyfikatora

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

Aby rozpocząć analizowanie dokumentu identyfikacyjnego, użyj poniższego polecenia cURL. Aby uzyskać więcej informacji na temat analizy dokumentów tożsamości, zobacz przewodnik [koncepcyjny Dotyczący dokumentów tożsamości](../../concept-identification-cards.md). Aby rozpocząć analizowanie dokumentu tożsamości, wywołaj interfejs API **[Analyze ID https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7738978e467c5fb8707) Document]** przy użyciu poniższego polecenia cURL. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{your ID document URL}` adresem URL obrazu potwierdzenia.
1. Zastąp `{subscription key}` element kluczem subskrypcji skopiowanym w poprzednim kroku.

### <a name="v21-previewtabv2-1"></a>[wersja 2.1 (wersja zapoznawcza) (#tab/v2-1

```bash
curl -i -X POST "https://{endpoint}/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your  ID document URL}'}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

Otrzymasz odpowiedź, `202 (Success)` która zawiera nagłówek **Operation-Location.** Wartość tego nagłówka zawiera identyfikator wyniku, który umożliwia wykonywanie zapytań o stan operacji asynchronicznej i uzyskiwanie wyników. W poniższym przykładzie ciąg po `analyzeResults/` jest identyfikatorem wyniku.

```console
https://westus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/0c6cb19e-538f-4b8d-98b7-e105c9995ba6
```

### <a name="get-the-analyze-id-document-result"></a>Uzyskiwanie wyniku analizy dokumentu identyfikatora

Po wywołaniu interfejsu **API** analizowania dokumentu identyfikatora wywołaj interfejs **[API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822)** pobierz identyfikator analizy wyników dokumentu, aby uzyskać stan operacji i wyodrębnione dane.  Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{endpoint}` element punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji. Można go znaleźć na karcie Przegląd Rozpoznawanie formularzy **zasobów.**
1. Zastąp `{resultId}` identyfikatorem operacji z poprzedniego kroku.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -X GET "https://{endpoint}/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz odpowiedź z danych `200 (Success)` wyjściowych JSON. Pierwsze `"status"` pole, , wskazuje stan operacji. Jeśli operacja nie zostanie ukończona, wartością będzie lub , a interfejs API należy wywołać ponownie ręcznie lub za pośrednictwem skryptu do momentu `"status"` `"running"` otrzymania `"notStarted"` `succeeded` wartości. Zalecamy interwał co najmniej jednej sekundy między wywołaniami.

* Pole `"readResults"` zawiera każdy wiersz tekstu, który został wyodrębniony z dokumentu tożsamości.
* Pole `"documentResults"` zawiera tablicę obiektów, z których każdy reprezentuje dokument id wykryty w dokumencie wejściowym.

Poniżej znajduje się przykładowy dokument tożsamości i odpowiadające mu dane wyjściowe JSON

* :::image type="content" source="https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-license.jpg" alt-text="przykładowe prawo jazdy":::

```json
{
    "status": "succeeded",
    "createdDateTime": "2021-04-13T17:24:52Z",
    "lastUpdatedDateTime": "2021-04-13T17:24:55Z",
    "analyzeResult": {
      "version": "2.1.0",
      "readResults": [
        {
          "page": 1,
          "angle": -0.2823,
          "width": 450,
          "height": 294,
          "unit": "pixel"
        }
      ],
      "documentResults": [
        {
          "docType": "prebuilt:idDocument:driverLicense",
          "docTypeConfidence": 0.995,
          "pageRange": [
            1,
            1
          ],
          "fields": {
            "Address": {
              "type": "string",
              "valueString": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "text": "123 STREET ADDRESS YOUR CITY WA 99999-1234",
              "boundingBox": [
                158,
                151,
                326,
                151,
                326,
                177,
                158,
                177
              ],
              "page": 1,
              "confidence": 0.965
            },
            "Country": {
              "type": "country",
              "valueCountry": "USA",
              "confidence": 0.99
            },
            "DateOfBirth": {
              "type": "date",
              "valueDate": "1958-01-06",
              "text": "01/06/1958",
              "boundingBox": [
                187,
                133,
                272,
                132,
                272,
                148,
                187,
                149
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DateOfExpiration": {
              "type": "date",
              "valueDate": "2020-08-12",
              "text": "08/12/2020",
              "boundingBox": [
                332,
                230,
                414,
                228,
                414,
                244,
                332,
                245
              ],
              "page": 1,
              "confidence": 0.99
            },
            "DocumentNumber": {
              "type": "string",
              "valueString": "LICWDLACD5DG",
              "text": "LIC#WDLABCD456DG",
              "boundingBox": [
                162,
                70,
                307,
                68,
                307,
                84,
                163,
                85
              ],
              "page": 1,
              "confidence": 0.99
            },
            "FirstName": {
              "type": "string",
              "valueString": "LIAM R.",
              "text": "LIAM R.",
              "boundingBox": [
                158,
                102,
                216,
                102,
                216,
                116,
                158,
                116
              ],
              "page": 1,
              "confidence": 0.985
            },
            "LastName": {
              "type": "string",
              "valueString": "TALBOT",
              "text": "TALBOT",
              "boundingBox": [
                160,
                86,
                213,
                85,
                213,
                99,
                160,
                100
              ],
              "page": 1,
              "confidence": 0.987
            },
            "Region": {
              "type": "string",
              "valueString": "Washington",
              "confidence": 0.99
            },
            "Sex": {
              "type": "gender",
              "valueGender": "M",
              "text": "M",
              "boundingBox": [
                226,
                190,
                232,
                190,
                233,
                201,
                226,
                201
              ],
              "page": 1,
              "confidence": 0.99
            }
          }
        }
      ]
    }
  }
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

Do trenowania modelu niestandardowego potrzebny jest zestaw danych szkoleniowych w obiekcie blob usługi Azure Storage. Potrzebujesz co najmniej pięciu wypełnionych formularzy (dokumentów PDF i/lub obrazów) tego samego typu/struktury. Zobacz [Build a training data set for a custom model](../../build-training-data-set.md) (Tworzenie zestawu danych treningowych dla modelu niestandardowego), aby uzyskać porady i opcje dotyczące kompilowania danych treningowych.

Trennie bez danych oznaczonych etykietami jest operacją domyślną i jest prostsze. Alternatywnie możesz ręcznie oznaczać etykietami niektóre lub wszystkie dane treningowe z wyprzedzeniem. Jest to bardziej złożony proces, ale powoduje, że model jest lepiej wytrenowany.

> [!NOTE]
> Modele można również szkolić za pomocą graficznego interfejsu użytkownika, takiego [jak Rozpoznawanie formularzy przykładowe narzędzie do etykietowania](../../quickstarts/label-tool.md).


### <a name="train-a-model-without-labels"></a>Trenowanie modelu bez etykiet

Aby wyt Rozpoznawanie formularzy za pomocą dokumentów w kontenerze obiektów blob platformy Azure, wywołaj interfejs **[API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** trenowania modelu niestandardowego, uruchamiając następujące polecenie cURL. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{subscription key}` element kluczem subskrypcji skopiowanym w poprzednim kroku.
1. Zastąp wartości adresem URL sygnatury dostępu `{SAS URL}` współdzielonego (SAS) kontenera usługi Azure Blob Storage. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Pobieranie adresu URL sygnatury dostępu współdzielonego":::

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

Otrzymasz odpowiedź z `201 (Success)` nagłówkiem **Location.** Wartość tego nagłówka jest identyfikatorem trenowany nowy model.

### <a name="train-a-model-with-labels"></a>Trenowanie modelu przy użyciu etykiet

Aby trenować za pomocą etykiet, musisz mieć specjalne pliki z informacjami o etykietach ( ) w kontenerze `\<filename\>.pdf.labels.json` magazynu obiektów blob obok dokumentów szkoleniowych. Przykładowe [Rozpoznawanie formularzy etykietowania](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający tworzenie tych plików etykiet. Gdy już je masz, możesz wywołać interfejs **[API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** trenowania modelu niestandardowego z parametrem ustawionym na w `"useLabelFile"` treści `true` JSON.

Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{subscription key}` element kluczem subskrypcji skopiowanym w poprzednim kroku.
1. Zastąp wartości adresem URL sygnatury dostępu współdzielonego `{SAS URL}` (SAS) kontenera usługi Azure Blob Storage. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Pobieranie adresu URL sygnatury dostępu współdzielonego":::

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

Otrzymasz odpowiedź z `201 (Success)` nagłówkiem **Lokalizacja.** Wartość tego nagłówka to identyfikator nowego modelu, który jest trenowany.

### <a name="get-training-results"></a>Uzyskiwanie wyników szkolenia

Po zakończeniu operacji trenowania użyj nowej operacji **[Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** model niestandardowy, aby sprawdzić stan trenowania. Przekaż identyfikator modelu do tego wywołania interfejsu API, aby sprawdzić stan trenowania:

1. Zastąp `{Endpoint}` element punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{subscription key}` element kluczem subskrypcji
1. Zastąp `{model ID}` identyfikatorem modelu otrzymanym w poprzednim kroku

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz odpowiedź z `200 (Success)` treścią JSON w następującym formacie. Zwróć uwagę na `"status"` pole . Ta wartość będzie mieć wartość `"ready"` po zakończeniu trenowania. Jeśli trenowanie modelu nie zostało ukończone, konieczne będzie ponowne odpytanie usługi przez ponowne uruchomić polecenie. Zalecamy interwał co najmniej jednej sekundy między wywołaniami.

Pole `"modelId"` zawiera identyfikator trenowania modelu. Będzie potrzebny w następnym kroku.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy za pomocą modelu niestandardowego

Następnie użyjesz nowo wytrenego modelu, aby przeanalizować dokument i wyodrębnić z niego pary klucz-wartość oraz tabele. Wywołaj interfejs API **[formularza analizy,](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)** uruchamiając następujące polecenie cURL. Przed uruchomieniem polecenia należy wprowadzić następujące zmiany:

1. Zastąp `{Endpoint}` element punktem końcowym uzyskanymi z klucza Rozpoznawanie formularzy subskrypcji. Można go znaleźć na karcie Przegląd Rozpoznawanie formularzy **zasobów.**
1. Zastąp `{model ID}` identyfikatorem modelu otrzymanym w poprzedniej sekcji.
1. Zastąp `{SAS URL}` element adresem URL sygnatury dostępu współdzielonego pliku w usłudze Azure Storage. Wykonaj kroki opisane w sekcji Szkolenie, ale zamiast uzyskać adres URL sygnatury dostępu współdzielonego dla całego kontenera obiektów blob, pobierz go dla określonego pliku, który chcesz przeanalizować.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

Otrzymasz odpowiedź z `202 (Success)` nagłówkiem **Operation-Location.** Wartość tego nagłówka zawiera identyfikator wyników, który umożliwia śledzenie wyników operacji Analizowanie. Zapisz ten identyfikator wyników do następnego kroku.

### <a name="get-the-analyze-results"></a>Uzyskiwanie wyników analizy

Wywołaj interfejs API **[pobierz wynik formularza analizy,](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult)** aby odpytować wyniki operacji Analyze.

1. Zastąp `{Endpoint}` element punktem końcowym uzyskanymi z klucza Rozpoznawanie formularzy subskrypcji. Można go znaleźć na karcie Przegląd Rozpoznawanie formularzy **zasobów.**
1. Zastąp `{result ID}` identyfikatorem otrzymanym w poprzedniej sekcji.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz odpowiedź z `200 (Success)` treścią JSON w następującym formacie. Dane wyjściowe zostały skrócone dla uproszczenia. Zwróć uwagę `"status"` na pole w dolnej części. Ta wartość będzie mieć wartość `"succeeded"` po zakończeniu operacji analizowania. Jeśli operacja Analizowanie nie została ukończona, konieczne będzie ponowne odpytanie usługi przez ponowne uruchomić polecenie. Zalecamy interwał co najmniej jednej sekundy między wywołaniami.

W modelach niestandardowych wytrenowanych bez etykiet skojarzenia par klucz/wartość i tabele znajdują się w węźle `"pageResults"` danych wyjściowych JSON. W modelach niestandardowych wytrenowanych za pomocą etykiet skojarzenia pary klucz/wartość znajdują się w `"documentResults"` węźle. Jeśli określono również wyodrębnianie zwykłego tekstu za pomocą parametru adresu URL *includeTextDetails,* węzeł będzie wyświetlać zawartość i pozycje całego tekstu `"readResults"` w dokumencie.

Te przykładowe dane wyjściowe JSON zostały skrócone dla uproszczenia. Zobacz pełne [przykładowe dane wyjściowe w witrynie GitHub.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json)

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          },
          ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ],
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          },
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ],
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```

---

### <a name="improve-results"></a>Poprawianie wyników

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Zarządzanie modelami niestandardowymi

### <a name="get-a-list-of-custom-models"></a>Uzyskiwanie listy modeli niestandardowych

Użyj **[interfejsu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModels)** API List Custom Models (Lista modeli niestandardowych) w poniższym poleceniu, aby zwrócić listę wszystkich modeli niestandardowych należących do subskrypcji.

1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{subscription key}` element kluczem subskrypcji skopiowanym w poprzednim kroku.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz odpowiedź z komunikatem `200` o sukcesie z danymi JSON podobnymi do poniższych. Element `"modelList"` zawiera wszystkie utworzone modele i ich informacje.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Uzyskiwanie określonego modelu

Aby pobrać szczegółowe informacje o określonym modelu niestandardowym, użyj interfejsu API **[pobierania modelu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** niestandardowego w poniższym poleceniu.

1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{subscription key}` element kluczem subskrypcji skopiowanym w poprzednim kroku.
1. Zastąp `{modelId}` identyfikatorem modelu niestandardowego, który chcesz sprawdzić.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz odpowiedź z komunikatem `200` o sukcesie z danymi JSON podobnymi do poniższych.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Usuwanie modelu z konta zasobu

Możesz również usunąć model z konta, odwołując się do jego identyfikatora. To polecenie wywołuje interfejs API **[usuwania modelu niestandardowego,](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/DeleteCustomModel)** aby usunąć model używany w poprzedniej sekcji.
kod
1. Zastąp `{Endpoint}` parametr punktem końcowym uzyskanymi przy użyciu Rozpoznawanie formularzy subskrypcji.
1. Zastąp `{subscription key}` element kluczem subskrypcji skopiowanym w poprzednim kroku.
1. Zastąp `{modelId}` identyfikatorem modelu niestandardowego, który chcesz sprawdzić.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz odpowiedź z `204` informacją o sukcesie wskazującą, że model został oznaczony do usunięcia. Artefakty modelu zostaną usunięte w ciągu 48 godzin.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start za pomocą interfejsu API REST Rozpoznawanie formularzy trenować modele i analizować formularze na różne sposoby. Następnie zapoznaj się z dokumentacją referencyjną, aby bardziej szczegółowo poznać Rozpoznawanie formularzy API.

> [!div class="nextstepaction"]
> [Dokumentacja referencyjna interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
