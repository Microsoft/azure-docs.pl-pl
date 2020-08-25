---
title: 'Szybki Start: wyodrębnianie danych z kart służbowych przy użyciu języka Python — formularz rozpoznawania'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy w języku Python, aby wyodrębnić dane z obrazów kart firmowych w angielskiej wersji językowej.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: aa16952d2b2dff6f69abfc37090a9e00b7d48a27
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751134"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Szybki Start: wyodrębnianie danych z kart służbowych przy użyciu interfejsu API REST usługi rozpoznawania formularzy w języku Python

W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy platformy Azure w języku Python, aby wyodrębnić i zidentyfikować odpowiednie informacje na temat kart służbowych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- Zainstalowana w języku [Python](https://www.python.org/downloads/) (Jeśli chcesz uruchomić przykład lokalnie).
- Adres URL obrazu karty biznesowej. Możesz użyć [przykładowego obrazu](../media/business-card-english.jpg) dla tego przewodnika Szybki Start.

> [!NOTE]
> Ten przewodnik Szybki Start korzysta ze zdalnego obrazu karty biznesowej, do którego uzyskuje dostęp za pośrednictwem adresu URL Aby zamiast tego użyć plików lokalnych, zapoznaj się z [dokumentacją referencyjną](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync).

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Analizowanie karty biznesowej

Aby rozpocząć analizowanie karty biznesowej, należy wywołać interfejs API **[analizy biznesowej](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** przy użyciu poniższego skryptu języka Python. Przed uruchomieniem skryptu wprowadź następujące zmiany:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `<your business card URL>` na adres URL obrazu karty biznesowej.
1. Zamień `<subscription key>` na klucz subskrypcji skopiowany z poprzedniego kroku.

    ```python
    ########### Python Form Recognizer Async Business cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults"
    source = r"<path to your business card>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Zapisz kod w pliku z rozszerzeniem. pr. Na przykład *form-Recognizer-BusinessCards.py*.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-businesscards.py`.

Otrzymasz `202 (Success)` odpowiedź, która zawiera nagłówek **operacji-Location** , który skrypt będzie drukowany w konsoli programu. Ten nagłówek zawiera identyfikator operacji, którego można użyć do zbadania stanu operacji asynchronicznej i uzyskania wyników. W poniższym przykładzie wartość ciąg po `operations/` to identyfikator operacji.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>Pobierz wyniki z karty biznesowej

Po wywołaniu interfejsu API **analizy biznesowej** należy wywołać interfejs API **[wyników uzyskiwania analizy biznesowej](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** , aby uzyskać stan operacji i wyodrębnionych danych. Dodaj następujący kod w dolnej części skryptu języka Python. Spowoduje to użycie wartości identyfikatora operacji w nowym wywołaniu interfejsu API. Ten skrypt wywołuje interfejs API w regularnych odstępach czasu, dopóki wyniki nie będą dostępne. Zalecamy interwał co najmniej jednej sekundy.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Zapisz skrypt.
1. Ponownie Użyj `python` polecenia, aby uruchomić przykład. Na przykład `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi
![Wizytówka firmy Contoso](../media/business-card-english.jpg)

Ten przykład ilustruje dane wyjściowe JSON zwrócone przez aparat rozpoznawania formularza. Te próbki zostały obcięte na potrzeby odczytywania przykładu.

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

Skrypt będzie drukował odpowiedzi do konsoli do momentu zakończenia operacji **analizowania karty biznesowej** . `"readResults"`Węzeł zawiera cały rozpoznany tekst. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. `"documentResults"`Węzeł zawiera wartości specyficzne dla karty biznesowej, które zostały odnalezione przez model. Tutaj znajdziesz przydatne pary klucz/wartość, takie jak nazwa firmy, imię, nazwisko, telefon i tak dalej.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy w języku Python w celu wyodrębnienia zawartości karty biznesowej. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
