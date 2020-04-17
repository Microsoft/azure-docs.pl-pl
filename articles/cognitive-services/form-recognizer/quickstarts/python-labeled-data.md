---
title: 'Szybki start: trenuj z etykietami przy użyciu interfejsu API REST i Pythona — aparat rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą funkcji danych z etykietą rozpoznawania formularzy z interfejsem API REST i pythonem do uczenia modelu niestandardowego.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 36ded3bd85cd7acdffbfe46b9e931a811994fa30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531104"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Trenuj model aparatu rozpoznawania formularzy z etykietami przy użyciu interfejsu API REST i języka Python

W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania formularzy w języku Python, aby wyszkolić model niestandardowy z danymi oznaczonymi ręcznie. Zobacz [Train with labels](../overview.md#train-with-labels) sekcji przeglądu, aby dowiedzieć się więcej o tej funkcji.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, musisz mieć:
- [Python](https://www.python.org/downloads/) zainstalowany (jeśli chcesz uruchomić przykład lokalny).
- Zestaw co najmniej sześciu form tego samego typu. Użyjesz tych danych do szkolenia modelu i przetestowania formularza. Do tego szybkiego startu można użyć [przykładowego zestawu danych.](https://go.microsoft.com/fwlink/?linkid=2090451) Przekaż pliki szkoleniowe do katalogu głównego kontenera magazynu obiektów blob na koncie usługi Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularzy

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Konfigurowanie danych szkoleniowych

Następnie musisz skonfigurować wymagane dane wejściowe. Funkcja danych oznaczonych etykietą ma specjalne wymagania wejściowe wykraczające poza wymagania potrzebne do szkolenia modelu niestandardowego. 

Upewnij się, że wszystkie dokumenty szkoleniowe są w tym samym formacie. Jeśli masz formularze w wielu formatach, zorganizuj je w podfoldery na podstawie wspólnego formatu. Podczas szkolenia należy skierować interfejs API do podfolderu.

Aby wyszkolić model przy użyciu danych oznaczonych etykietą, potrzebne są następujące pliki jako dane wejściowe w podfolderze. Dowiesz się, jak utworzyć ten plik poniżej.

* **Formularze źródłowe** — formularze do wyodrębniania danych. Obsługiwane typy to JPEG, PNG, PDF lub TIFF.
* **Pliki układu OCR** - pliki JSON, które opisują rozmiary i pozycje wszystkich czytelnych tekstu w każdym formularzu źródłowym. Do wygenerowania tych danych użyjesz interfejsu API układu aparatu rozpoznawania formularzy. 
* **Pliki etykiet** — pliki JSON opisujące etykiety danych wprowadzone ręcznie przez użytkownika.

Wszystkie te pliki powinny zajmować ten sam podfolder i być w następującym formacie:

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> Podczas etykietowania formularzy za pomocą narzędzia do [etykietowania przykładowego](./label-tool.md)aparatu rozpoznawania formularzy narzędzie automatycznie tworzy te pliki układowe etykiet i ocr.

### <a name="create-the-ocr-output-files"></a>Tworzenie plików wyjściowych OCR

Potrzebne są pliki wyników OCR, aby usługa rozważyła odpowiednie pliki wejściowe dla szkolenia z etykietami. Aby uzyskać wyniki OCR dla danego formularza źródłowego, wykonaj poniższe czynności:

1. Wywołanie interfejsu API **[układu analizy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** w kontenerze układu odczytu z plikiem wejściowym jako częścią treści żądania. Zapisz identyfikator znaleziony w nagłówku **lokalizacja operacji i lokalizacji** odpowiedzi.
1. Wywołanie interfejsu API **[wyniku analizy analizy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** przy użyciu identyfikatora operacji z poprzedniego kroku.
1. Pobierz odpowiedź i zapisz zawartość do pliku. Dla każdego formularza źródłowego odpowiedni plik OCR powinien mieć `.ocr.json`oryginalną nazwę pliku dołączona do pliku . Dane wyjściowe JSON OCR powinny mieć następujący format. Zobacz [przykładowy plik OCR,](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) aby uzyskać pełny przykład. 

    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
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
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```

### <a name="create-the-label-files"></a>Tworzenie plików etykiet

Pliki etykiet zawierają skojarzenia klucz-wartość wprowadzone ręcznie przez użytkownika. Są one potrzebne do szkolenia danych etykietą, ale nie każdy plik źródłowy musi mieć odpowiedni plik etykiety. Pliki źródłowe bez etykiet będą traktowane jako zwykłe dokumenty szkoleniowe. Firma Microsoft zaleca pięć lub więcej oznaczonych plików dla niezawodnego szkolenia.

Podczas tworzenia pliku etykiety można opcjonalnie określić&mdash;dokładne położenie wartości w dokumencie. To da szkolenia jeszcze większą dokładność. Regiony są formatowane jako zestaw ośmiu wartości odpowiadających czterem współrzędnym X,Y: lewy górny, prawy górny, prawy dolny i lewy dolny. Wartości współrzędnych są od zera do jednego, skalowane do wymiarów strony.

Dla każdego formularza źródłowego odpowiedni plik etykiety powinien mieć `.labels.json`oryginalną nazwę pliku dołączona do pliku . Plik etykiety powinien mieć następujący format. Zobacz [przykładowy plik etykiety,](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) aby uzyskać pełny przykład.

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!NOTE]
> Do każdego elementu tekstowego można zastosować tylko jedną etykietę, a każdą etykietę można zastosować tylko raz na stronę. Obecnie nie można zastosować etykiety na wielu stronach.


## <a name="train-a-model-using-labeled-data"></a>Szkolenie modelu przy użyciu danych oznaczonych etykietą

Aby uszkoliiwać model z oznaczonymi danymi, należy wywołać interfejs API **[modelu niestandardowego pociągu,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** uruchamiając następujący kod języka Python. Przed uruchomieniem kodu należy wprowadzić następujące zmiany:

1. Zamień `<Endpoint>` na adres URL punktu końcowego zasobu aparatu rozpoznawania formularzy.
1. Zamień `<SAS URL>` adres URL sygnatury dostępu współdzielonego (SAS) kontenera magazynu obiektów Blob platformy Azure. Aby pobrać adres URL sygnatury dostępu Współdzielonego, otwórz Eksploratora magazynu Platformy Microsoft Azure, kliknij prawym przyciskiem myszy kontener i wybierz pozycję **Pobierz podpis dostępu współdzielonego**. Upewnij się, że uprawnienia **Do odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość w sekcji **ADRES URL.** Powinien mieć formularz: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Zamień `<Blob folder name>` na nazwę folderu w kontenerze obiektów blob, w którym znajdują się dane wejściowe. Lub, jeśli dane są w katalogu głównym, `"prefix"` pozostaw to puste i usuń pole z treści żądania HTTP.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```

## <a name="get-training-results"></a>Uzyskaj wyniki treningów

Po rozpoczęciu operacji pociągu, należy użyć zwróconego identyfikatora, aby uzyskać stan operacji. Dodaj następujący kod na dole skryptu Pythona. Używa wartości identyfikatora z wywołania szkoleniowego w nowym wywołaniu interfejsu API. Operacja szkoleniowa jest asynchronizawo, więc ten skrypt wywołuje interfejs API w regularnych odstępach czasu, aż do ukończenia stanu szkolenia. Zalecamy interwał jednej sekundy lub więcej.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Po zakończeniu procesu szkolenia otrzymasz odpowiedź `201 (Success)` z zawartością JSON, taką jak następująca. Odpowiedź została skrócona dla uproszczenia.

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

Skopiuj `"modelId"` wartość do użycia w poniższych krokach.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Po zakończeniu procesu otrzymasz odpowiedź `202 (Success)` z zawartością JSON w następującym formacie. Odpowiedź została skrócona dla uproszczenia. Główne skojarzenia klucza/wartości `"documentResults"` znajdują się w węźle. Wyniki interfejsu API układu (zawartość i pozycje całego tekstu w `"readResults"` dokumencie) znajdują się w węźle.

```json
{ 
    "analyzeResult":{ 
      "version":"2.0.0",
      "readResults":[ 
        { 
          "page":1,
          "language":"en",
          "angle":0,
          "width":8.5,
          "height":11,
          "unit":"inch",
          "lines":[ 
            { 
              "language":"en",
              "boundingBox":[ 
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
          ]
        }
      ],
      "pageResults":[ 
        { 
          "page":1,
          "tables":[ 
            { 
              "rows":2,
              "columns":6,
              "cells":[ 
                { 
                  "rowIndex":0,
                  "columnIndex":0,
                  "text":"Invoice Number",
                  "boundingBox":[ 
                    0.5075,
                    2.8088,
                    1.9061,
                    2.8088,
                    1.9061,
                    3.3219,
                    0.5075,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/8/words/0",
                    "#/readResults/0/lines/8/words/1"
                  ]
                },
                ...        
              ]
            }
          ]
        }
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/15/words/0"
                }
              ],
              "fieldName":"total"
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/12/words/0"
                }
              ],
              "fieldName":"invoice #"
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/16/words/0"
                }
              ],
              "fieldName":"vat"
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Poprawa wyników

Sprawdź `"confidence"` wartości dla każdego wyniku klucz/wartość w węźle. `"documentResults"` Należy również spojrzeć na wyniki `"readResults"` zaufania w węźle, które odpowiadają operacji Układu. Zaufanie wyników układu nie wpływa na zaufanie wyników wyodrębniania klucz/wartość, dlatego należy sprawdzić oba.
* Jeśli wyniki zaufania dla operacji Układ są niskie, spróbuj poprawić jakość dokumentów wejściowych (zobacz [Wymagania wejściowe](../overview.md#input-requirements)).
* Jeśli wyniki zaufania dla operacji wyodrębniania klucz/wartość są niskie, upewnij się, że analizowane dokumenty są tego samego typu, co dokumenty używane w zestawie szkoleniowym. Jeśli dokumenty w zestawie szkoleniowym mają różnice w wyglądzie, należy rozważyć podzielenie ich na różne foldery i szkolenie jednego modelu dla każdej odmiany.

### <a name="avoid-cluttered-labels"></a>Unikaj zaśmieconych etykiet

Czasami po zastosowaniu różnych etykiet w tym samym wierszu tekstu usługa może scalić te etykiety w jedno pole. Na przykład w adresie można oznaczyć miasto, stan i kod pocztowy jako różne pola, ale podczas przewidywania te pola nie są rozpoznawane oddzielnie.

Rozumiemy, że ten scenariusz jest niezbędny dla naszych klientów i pracujemy nad poprawą tego w przyszłości. Obecnie zalecamy naszym użytkownikom, aby oznaczyć wiele zagraconych pól jako jedno pole, a następnie oddzielić terminy w post-processing wyników ekstrakcji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start nauczysz się, jak używać interfejsu API REST rozpoznawania formularzy w języku Python do szkolenia modelu z danymi oznaczonymi ręcznie. Następnie zobacz [dokumentację odwołania interfejsu API,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) aby zbadać interfejs API rozpoznawania formularzy bardziej szczegółowo.
