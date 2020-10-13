---
title: 'Szybki Start: wyodrębnianie informacji o tekście i układzie przy użyciu języka Python — formularz rozpoznawania'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz interfejsu API REST układu aparatu rozpoznawania w języku Python w celu odczytywania danych tekstowych i tabel z formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 72420019ead1ae47054ae62197d8cc310063a6b9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969776"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Szybki Start: wyodrębnianie informacji o tekście i układzie przy użyciu interfejsu API REST aparatu rozpoznawania formularzy w języku Python

W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy platformy Azure w języku Python, aby wyodrębnić informacje o układzie tekstu oraz dane tabeli z dokumentów formularzy.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- Zainstalowana w języku [Python](https://www.python.org/downloads/) (Jeśli chcesz uruchomić przykład lokalnie).
- Dokument formularza. Możesz pobrać obraz z [zestawu przykładowych danych](https://go.microsoft.com/fwlink/?linkid=2090451) (Pobierz i wyodrębnij *sample_data.zip*) dla tego przewodnika Szybki Start.

> [!NOTE]
> Ten przewodnik Szybki Start używa lokalnie przechowywanego dokumentu. Aby dowiedzieć się, jak używać plików zdalnych, do których uzyskuje się dostęp za pomocą adresu URL, zobacz [dokumentację referencyjną](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync).


## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analizowanie układu formularza

Aby rozpocząć analizowanie układu, należy wywołać interfejs API **[Analizowanie układu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** przy użyciu poniższego skryptu języka Python. Przed uruchomieniem skryptu wprowadź następujące zmiany:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień na `<path to your form>` ścieżkę do dokumentu formularza lokalnego.
1. Zamień `<subscription key>` na klucz subskrypcji skopiowany z poprzedniego kroku.

    # <a name="v20"></a>[Wersja 2.0](#tab/v2-0) 
    ```python
    ########### Python Form Recognizer Async Layout #############
    
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```   
    # <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)  
    ```python
    ########### Python Form Recognizer Async Layout #############
    
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```  
    
    
      ---


1. Zapisz kod w pliku z rozszerzeniem. pr. Na przykład *form-Recognizer-Layout.py*.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-layout.py`.

Otrzymasz `202 (Success)` odpowiedź, która zawiera nagłówek **operacji-Location** , który skrypt będzie drukowany w konsoli programu. Ten nagłówek zawiera identyfikator operacji, którego można użyć do zbadania stanu operacji asynchronicznej i uzyskania wyników. W poniższym przykładzie wartość ciąg po `operations/` to identyfikator operacji.

# <a name="v20"></a>[Wersja 2.0](#tab/v2-0)   
```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
``` 
# <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)  
```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```  

---
    



## <a name="get-the-layout-results"></a>Pobierz wyniki układu

Po wywołaniu interfejsu API **Analizowanie układu** należy wywołać interfejs API **[wyników Get Analizuj](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** w celu uzyskania stanu operacji i wyodrębnionych danych. Dodaj następujący kod w dolnej części skryptu języka Python. Ten kod używa wartości identyfikatora operacji w nowym wywołaniu interfejsu API. Ten skrypt wywołuje interfejs API w regularnych odstępach czasu, dopóki wyniki nie będą dostępne. Zalecamy interwał co najmniej jednej sekundy.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
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
1. Ponownie Użyj `python` polecenia, aby uruchomić przykład. Na przykład `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Skrypt będzie drukował odpowiedzi do konsoli do momentu zakończenia operacji **analizy układu** . Następnie drukuje wyodrębnione dane w formacie JSON. `"readResults"`Węzeł zawiera każdy wiersz tekstu z odpowiednim umieszczaniem pola ograniczenia na stronie. `"selectionMarks"`Węzeł (w wersji zapoznawczej programu v 2.1) pokazuje każdy znacznik wyboru (CheckBox, znacznik radiowy) i czy jego stan to "selected" lub "Unselected". W `"pageResults"` polu są wyświetlane wszystkie fragmenty tekstu w tabelach, z których każda jest Współrzędna kolumny wiersza.

Zapoznaj się z poniższym obrazem faktury i odpowiednimi danymi wyjściowymi JSON. Wynik został skrócony do uproszczenia.

:::image type="content" source="../media/contoso-invoice.png" alt-text="Dokument programu contoso Project Statement z tabelą.":::

# <a name="v20"></a>[Wersja 2.0](#tab/v2-0)    
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

# <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)   
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

---



## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy w języku Python w celu wyodrębnienia układu tekstu faktury. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)
