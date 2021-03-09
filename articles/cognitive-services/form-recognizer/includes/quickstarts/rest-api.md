---
title: 'Szybki Start: interfejs API REST aparatu rozpoznawania formularzy'
description: Użyj interfejsu API REST aparatu rozpoznawania formularzy, aby utworzyć aplikację przetwarzającej formularze, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/15/2020
ms.author: lajanuar
ms.openlocfilehash: 2cff960e2dfe6a85b7e16395a167b77f66690c56
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511079"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> W tym przewodniku zastosowano zwinięcie, aby wykonać wywołania interfejsu API REST. Istnieje również [przykładowy kod w usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest) , który ilustruje sposób wywoływania interfejsów API REST w języku Python.

## <a name="prerequisites"></a>Wymagania wstępne

* zainstalowano [zwinięcie](https://curl.haxx.se/windows/) .
* Program [PowerShell w wersji 6.0](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)lub podobnej aplikacji w wierszu polecenia.
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Obiekt BLOB usługi Azure Storage zawierający zestaw danych szkoleniowych. Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../../build-training-data-set.md) w celu uzyskania wskazówek i opcji związanych z zestawem danych szkoleniowych. W tym przewodniku szybki start można użyć plików w folderze **uczenie** [zestawu danych przykładowych](https://go.microsoft.com/fwlink/?linkid=2090451) (pobierz i Wyodrębnij *sample_data.zip*).
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Utwórz zasób aparatu rozpoznawania formularzy "  target="_blank"> Utwórz zasób aparatu rozpoznawania formularza </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
  * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API rozpoznawania formularzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
  * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Adres URL obrazu potwierdzenia. Możesz użyć [przykładowego obrazu](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) dla tego przewodnika Szybki Start.
* Adres URL obrazu karty biznesowej. Możesz użyć [przykładowego obrazu](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) dla tego przewodnika Szybki Start.
* Adres URL obrazu faktury. Możesz użyć [przykładowego dokumentu](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) dla tego przewodnika Szybki Start.

## <a name="analyze-layout"></a>Analizowanie układu

Aparat rozpoznawania formularzy służy do analizowania i wyodrębniania tabel, znaczników wyboru, tekstu i struktury w dokumentach, bez konieczności uczenia modelu. Więcej informacji o wyodrębnianiu układu znajduje się w [przewodniku koncepcyjnym układu](../../concept-layout.md). Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{subscription key}` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `\"{your-document-url}` na jeden z przykładowych adresów URL.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

Otrzymasz odpowiedź obejmującą `202 (Success)` nagłówek **operacji** am. Wartość tego nagłówka zawiera identyfikator operacji, którego można użyć do zbadania stanu operacji asynchronicznej i uzyskania wyników. W poniższym przykładzie ciąg po `analyzeResults/` to identyfikator operacji.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Pobierz wyniki układu

Po wywołaniu interfejsu API **[Analizowanie układu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)** należy wywołać interfejs API **[wyników Get Analizuj](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult)** w celu uzyskania stanu operacji i wyodrębnionych danych. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{subscription key}` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień na `{resultId}` Identyfikator operacji z poprzedniego kroku.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>Sprawdzanie wyników

Otrzymasz `200 (success)` odpowiedź z zawartością JSON.

Zapoznaj się z poniższym obrazem faktury i odpowiednimi danymi wyjściowymi JSON.
* `"readResults"`Węzeł zawiera każdy wiersz tekstu z odpowiednim umieszczaniem pola ograniczenia na stronie. 
* `"selectionMarks"`Węzeł (w wersji zapoznawczej programu v 2.1) pokazuje każdy znacznik wyboru (CheckBox, znacznik radiowy) i czy jego stan to "selected" lub "Unselected". 
* `"pageResults"`Sekcja zawiera wyodrębnione tabele. Dla każdej tabeli jest wyodrębniany tekst, wiersz i indeks kolumny, łączenie wierszy i kolumn.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Dokument programu contoso Project Statement z tabelą.":::

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

To wyjście zostało skrócone dla uproszczenia. Zobacz [pełne przykładowe dane wyjściowe w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

To wyjście zostało skrócone dla uproszczenia. Zobacz [pełne przykładowe dane wyjściowe w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

## <a name="analyze-invoices"></a>Analizuj faktury

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

Aby rozpocząć analizowanie faktury, użyj poniższego polecenia. Aby uzyskać więcej informacji na temat analizy faktur, zobacz [Przewodnik dotyczący pojęć dotyczących faktur](../../concept-invoices.md). Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{your invoice URL}` na adres URL dokumentu faktury.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

Otrzymasz odpowiedź obejmującą `202 (Success)` nagłówek **operacji** am. Wartość tego nagłówka zawiera identyfikator operacji, którego można użyć do zbadania stanu operacji asynchronicznej i uzyskania wyników.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Pobierz wyniki faktury

Po wywołaniu interfejsu API **[Analizowanie faktury](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** można wywołać interfejs API **[wyników uzyskiwania analizy faktury](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** , aby uzyskać stan operacji i wyodrębnionych danych. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany za pomocą klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień na `{resultId}` Identyfikator operacji z poprzedniego kroku.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz `200 (Success)` odpowiedź z danymi wyjściowymi JSON. 
* `"readResults"`Pole zawiera wszystkie wiersze tekstu wyodrębnione z faktury.
* `"pageResults"`Obejmuje znaczniki tabel i wyborów wyodrębnione z faktury.
* `"documentResults"`Pole zawiera informacje o kluczu/wartości dla najbardziej odpowiednich części faktury.

Zapoznaj się z następującym dokumentem faktury i odpowiednimi danymi wyjściowymi JSON. 

* [Przykładowa faktura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

Ta zawartość JSON została skrócona pod kątem czytelności. Zobacz [pełne przykładowe dane wyjściowe w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

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
> Ta funkcja jest niedostępna w wybranej wersji interfejsu API.

---

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

Aby szkolić model niestandardowy, musisz mieć zestaw danych szkoleniowych w obiekcie blob usługi Azure Storage. Wymagana jest co najmniej pięć formularzy wypełnionych (dokumentów PDF i/lub obrazów) tego samego typu lub struktury. Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../../build-training-data-set.md) w celu uzyskania wskazówek i opcji tworzenia danych szkoleniowych.

Szkolenie bez etykiet danych jest operacją domyślną i jest prostsze. Alternatywnie możesz wcześniej ręcznie oznaczyć niektóre lub wszystkie dane szkoleniowe. Jest to bardziej skomplikowany proces, ale wynikiem jest lepszy przeszkolony model.

> [!NOTE]
> Możesz również nauczyć modele przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do próbkowania przykładowego aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md).


### <a name="train-a-model-without-labels"></a>Uczenie modelu bez etykiet

Aby przeprowadzić uczenie modelu aparatu rozpoznawania formularzy przy użyciu dokumentów w kontenerze obiektów blob platformy Azure, Wywołaj interfejs API **[niestandardowego modelu uczenia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** , uruchamiając następujące polecenie. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{subscription key}` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `{SAS URL}` na adres URL sygnatury dostępu współdzielonego (SAS) kontenera magazynu obiektów blob platformy Azure. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Pobieranie adresu URL SAS":::

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

Otrzymasz `201 (Success)` odpowiedź z nagłówkiem **lokalizacji** . Wartość tego nagłówka jest IDENTYFIKATORem nowego, nauczonego modelu.

### <a name="train-a-model-with-labels"></a>Uczenie modelu z etykietami

Aby szkolić z etykietami, musisz mieć specjalne pliki informacji o etykietach ( `\<filename\>.pdf.labels.json` ) w kontenerze magazynu obiektów BLOB obok dokumentów szkoleniowych. [Narzędzie do etykietowania próbek aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający Tworzenie tych plików etykiet. Po ich utworzeniu można wywołać interfejs API **[niestandardowego modelu uczenia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** z `"useLabelFile"` parametrem ustawionym na wartość `true` w treści JSON.

Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{subscription key}` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `{SAS URL}` na adres URL sygnatury dostępu współdzielonego (SAS) kontenera magazynu obiektów blob platformy Azure. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Pobieranie adresu URL SAS":::

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

Otrzymasz `201 (Success)` odpowiedź z nagłówkiem **lokalizacji** . Wartość tego nagłówka jest IDENTYFIKATORem nowego, nauczonego modelu.

### <a name="get-training-results"></a>Pobierz wyniki szkoleń

Po rozpoczęciu operacji pouczenia należy użyć nowej operacji, **[pobrać model niestandardowy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** w celu sprawdzenia stanu szkolenia. Przekaż Identyfikator modelu do tego wywołania interfejsu API, aby sprawdzić stan szkolenia:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany za pomocą klucza subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{subscription key}` na klucz subskrypcji
1. Zamień na `{model ID}` Identyfikator modelu otrzymany w poprzednim kroku

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz `200 (Success)` odpowiedź z treścią JSON w następującym formacie. Zwróć uwagę na `"status"` pole. Będzie to miało wartość `"ready"` po zakończeniu szkolenia. Jeśli model nie zakończył szkolenia, należy ponownie wykonać zapytanie dotyczące usługi przez ponowne uruchomienie polecenia. Zalecamy przedziału co najmniej jednej sekundy między wywołaniami.

`"modelId"`Pole zawiera identyfikator modelu, który jest szkoleniowy. Będzie to potrzebne do następnego kroku.

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

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy przy użyciu modelu niestandardowego

Następnie będziesz używać nowo przeszkolonego modelu do analizowania dokumentu i wyodrębniania par klucz-wartość i tabel z tej usługi. Wywołaj interfejs API **[analizowania formularzy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** , uruchamiając następujące polecenie. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany z klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień na `{model ID}` Identyfikator modelu otrzymany w poprzedniej sekcji.
1. Zamień na `{SAS URL}` adres URL sygnatury dostępu współdzielonego z plikiem w usłudze Azure Storage. Postępuj zgodnie z instrukcjami w sekcji szkolenia, ale zamiast uzyskać adres URL sygnatury dostępu współdzielonego dla całego kontenera obiektów blob, uzyskaj jeden dla określonego pliku, który chcesz analizować.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

Otrzymasz `202 (Success)` odpowiedź z nagłówkiem **lokalizacji operacji** . Wartość tego nagłówka zawiera identyfikator wyników używany do śledzenia wyników operacji analizy. Zapisz ten identyfikator wyników dla następnego kroku.

### <a name="get-the-analyze-results"></a>Pobierz wyniki analizy

Wywołaj interfejs API uzyskiwania **[wyników formularza analizy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeFormResult)** , aby zbadać wyniki operacji analizy.

1. Zamień na `{Endpoint}` punkt końcowy uzyskany z klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień na `{result ID}` Identyfikator otrzymany w poprzedniej sekcji.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz `200 (Success)` odpowiedź z treścią JSON w następującym formacie. Wynik został skrócony do uproszczenia. Zwróć uwagę na `"status"` pole w dolnej części strony. Będzie to miało wartość, `"succeeded"` gdy operacja analizy zostanie zakończona. Jeśli operacja analizy nie została ukończona, należy ponownie wykonać zapytanie dotyczące usługi przez ponowne uruchomienie polecenia. Zalecamy przedziału co najmniej jednej sekundy między wywołaniami.

W modelach niestandardowych przeszkolonych bez etykiet, skojarzenia pary klucz/wartość i tabele znajdują się w `"pageResults"` węźle danych wyjściowych JSON. W modelach niestandardowych przeszkolonych za pomocą etykiet, skojarzenia pary klucz/wartość znajdują się w `"documentResults"` węźle. Jeśli określono również zwykłe Wyodrębnianie tekstu za pomocą parametru adresu URL *includeTextDetails* , w `"readResults"` węźle będzie wyświetlana zawartość i położenie całego tekstu w dokumencie.

Te przykładowe dane wyjściowe JSON zostały skrócone dla uproszczenia. Zobacz [pełne przykładowe dane wyjściowe w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json).

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

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

### <a name="improve-results"></a>Popraw wyniki

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="analyze-receipts"></a>Analizuj potwierdzenia

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z paragonów w Stanach Zjednoczonych przy użyciu wstępnie przeszkolonego modelu paragonów. Aby uzyskać więcej informacji na temat analizy paragonów, zobacz [Przewodnik po pojęciach dotyczących przyjęć](../../concept-receipts.md). Aby rozpocząć analizowanie potwierdzenia, Wywołaj interfejs API **[analizy paragonów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync)** przy użyciu poniższego polecenia. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{your receipt URL}` na adres URL obrazu paragonu.
1. Zamień `{subscription key>` na klucz subskrypcji skopiowany z poprzedniego kroku.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

Otrzymasz odpowiedź obejmującą `202 (Success)` nagłówek **operacji** am. Wartość tego nagłówka zawiera identyfikator operacji, którego można użyć do zbadania stanu operacji asynchronicznej i uzyskania wyników. W poniższym przykładzie ciąg po `operations/` to identyfikator operacji.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Pobierz wyniki odbioru

Po wywołaniu interfejsu API **Analizowanie paragonów** należy wywołać interfejs API **[wyników uzyskiwania analizy przychodu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult)** , aby uzyskać stan operacji i wyodrębnionych danych. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany za pomocą klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień na `{operationId}` Identyfikator operacji z poprzedniego kroku.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz `200 (Success)` odpowiedź z danymi wyjściowymi JSON. Pierwsze pole, `"status"` , wskazuje stan operacji. Jeśli operacja nie zostanie ukończona, wartość `"status"` będzie `"running"` lub `"notStarted"` i należy ponownie wywołać interfejs API, ręcznie lub za pomocą skryptu. Zalecamy przedziału co najmniej jednej sekundy między wywołaniami.

`"readResults"`Węzeł zawiera cały rozpoznany tekst (w przypadku ustawienia opcjonalnego parametru *includeTextDetails* na `true` ). Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. `"documentResults"`Węzeł zawiera wartości specyficzne dla paragonu wykryte przez model. Tutaj znajdziesz przydatne pary klucz/wartość, takie jak podatek, łączny, adres handlowca itd.

Zapoznaj się z poniższym obrazem paragonu i odpowiednimi danymi wyjściowymi JSON.

![Potwierdzenie ze sklepu contoso](../../media/contoso-allinone.jpg)

Ten wynik został skrócony do czytelności. Zobacz [pełne przykładowe dane wyjściowe w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

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

## <a name="analyze-business-cards"></a>Analizowanie kart służbowych

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)  

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z angielskiej karty biznesowej przy użyciu wstępnie nauczonego modelu. Aby uzyskać więcej informacji na temat analizy karty biznesowej, zobacz [Przewodnik po pojęciach dotyczących wizytówek](../../concept-business-cards.md). Aby rozpocząć analizowanie karty biznesowej, należy wywołać interfejs API **[analizy biznesowej](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** przy użyciu poniższego polecenia. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{your business card URL}` na adres URL obrazu paragonu.
1. Zamień `{subscription key}` na klucz subskrypcji skopiowany z poprzedniego kroku.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your business card URL}'}"
```

Otrzymasz odpowiedź obejmującą `202 (Success)` nagłówek **operacji** am. Wartość tego nagłówka zawiera identyfikator operacji, którego można użyć do zbadania stanu operacji asynchronicznej i uzyskania wyników.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Pobierz wyniki karty biznesowej

Po wywołaniu interfejsu API **analizy biznesowej** należy wywołać interfejs API **[wyników uzyskiwania analizy biznesowej](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** , aby uzyskać stan operacji i wyodrębnionych danych. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `{Endpoint}` punkt końcowy uzyskany za pomocą klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień na `{resultId}` Identyfikator operacji z poprzedniego kroku.
1. Zastąp element `{subscription key}` kluczem subskrypcji.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Otrzymasz `200 (Success)` odpowiedź z danymi wyjściowymi JSON. 

`"readResults"`Węzeł zawiera cały rozpoznany tekst. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. `"documentResults"`Węzeł zawiera wartości specyficzne dla karty biznesowej, które zostały odnalezione przez model. W tym miejscu znajdziesz przydatne informacje kontaktowe, takie jak nazwa firmy, imię, nazwisko, numer telefonu itd.

![Wizytówka firmy Contoso](../../media/business-card-english.jpg)

Te przykładowe dane wyjściowe JSON zostały skrócone w celu zapewnienia czytelności. Zobacz [pełne przykładowe dane wyjściowe w serwisie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

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

Skrypt będzie drukował odpowiedzi do konsoli do momentu zakończenia operacji **analizowania karty biznesowej** .

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)  

> [!IMPORTANT]
> Ta funkcja jest niedostępna w wybranej wersji interfejsu API.

---

## <a name="manage-custom-models"></a>Zarządzanie modelami niestandardowymi

### <a name="get-a-list-of-custom-models"></a>Pobieranie listy modeli niestandardowych

Użyj interfejsu API **[niestandardowych modeli](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetCustomModels)** w poniższym poleceniu, aby zwrócić listę wszystkich niestandardowych modeli należących do subskrypcji.

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{subscription key}` na klucz subskrypcji skopiowany z poprzedniego kroku.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz `200` odpowiedź sukcesów z danymi JSON, takimi jak poniższe. `"modelList"`Element zawiera wszystkie utworzone modele i ich informacje.

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

Aby pobrać szczegółowe informacje o określonym modelu niestandardowym, należy użyć interfejsu API **[pobierania niestandardowego modelu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetCustomModel)** w poniższym poleceniu.

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{subscription key}` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `{modelId}` na identyfikator niestandardowego modelu, który ma zostać wyszukany.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz `200` odpowiedź sukcesów z danymi JSON, takimi jak poniższe.

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

Możesz również usunąć model z konta, odwołując się do jego identyfikatora. To polecenie wywołuje interfejs API **[usuwania niestandardowych modeli](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/DeleteCustomModel)** , aby usunąć model używany w poprzedniej sekcji.

1. Zamień na `{Endpoint}` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `{subscription key}` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `{modelId}` na identyfikator niestandardowego modelu, który ma zostać wyszukany.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Otrzymasz odpowiedź o `204` powodzeniu, wskazując, że model jest oznaczony do usunięcia. Artefakty modelu zostaną usunięte w ciągu 48 godzin.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy do uczenia modeli i analizowania formularzy na różne sposoby. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
