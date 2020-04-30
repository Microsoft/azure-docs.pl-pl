---
title: 'Szybki Start: uczenie się z etykietami przy użyciu interfejsu API REST i aparatu rozpoznawania języka Python'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą funkcji rozpoznawania danych z etykietami z interfejsem API REST i Python utworzyć niestandardową model przy użyciu narzędzia z możliwością uzyskiwania informacji.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 36ded3bd85cd7acdffbfe46b9e931a811994fa30
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81531104"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Uczenie modelu aparatu rozpoznawania formularzy z etykietami przy użyciu interfejsu API REST i języka Python

W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy w języku Python, aby nauczyć model niestandardowy z ręcznie oznaczonymi danymi. Aby dowiedzieć się więcej na temat tej funkcji, zobacz sekcję [uczenie z etykietami](../overview.md#train-with-labels) .

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- Zainstalowana w języku [Python](https://www.python.org/downloads/) (Jeśli chcesz uruchomić przykład lokalnie).
- Zestaw składający się z co najmniej sześciu formularzy tego samego typu. Te dane będą używane do uczenia modelu i testowania formularza. Możesz użyć [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451) dla tego przewodnika Szybki Start. Przekaż pliki szkoleniowe do katalogu głównego kontenera magazynu obiektów BLOB na koncie usługi Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Skonfiguruj dane szkoleniowe

Następnie musisz skonfigurować wymagane dane wejściowe. Funkcja dane z etykietą ma specjalne wymagania wejściowe poza tymi, które są wymagane do uczenia modelu niestandardowego. 

Upewnij się, że wszystkie dokumenty szkoleniowe mają ten sam format. Jeśli masz formularze w wielu formatach, zorganizuj je w podfolderach w oparciu o wspólny format. Podczas uczenia należy skierować interfejs API do podfolderu.

Aby zapewnić uczenie modelu przy użyciu danych z etykietami, potrzebne są następujące pliki jako dane wejściowe w podfolderze. Dowiesz się, jak utworzyć poniższe pliki.

* **Formularze źródłowe** — formularze, z których mają zostać wyodrębnione dane. Obsługiwane typy to JPEG, PNG, PDF lub TIFF.
* **Pliki układów OCR** — pliki JSON opisujące rozmiary i położenia wszystkich czytelnych tekstu w każdym formularzu źródłowym. Użyjesz interfejsu API układu aparatu rozpoznawania formularzy do wygenerowania tych danych. 
* **Pliki etykiet** — pliki JSON opisujące etykiety danych wprowadzane ręcznie przez użytkownika.

Wszystkie te pliki powinny zajmować ten sam podfolder i mieć następujący format:

* input_file1. PDF 
* input_file1. PDF. OCR. JSON
* input_file1. PDF. labels. JSON 
* input_file2. PDF 
* input_file2. PDF. OCR. JSON
* input_file2. PDF. labels. JSON
* ...

> [!TIP]
> Podczas etykietowania formularzy przy użyciu narzędzia do [etykietowania przykładowego](./label-tool.md)aparatu rozpoznawania formularzy narzędzie tworzy te pliki etykiet i układów OCR automatycznie.

### <a name="create-the-ocr-output-files"></a>Utwórz pliki wyjściowe OCR

Potrzebujesz plików wyników OCR, aby usługa mogła rozważyć odpowiednie pliki wejściowe dla szkolenia z etykietą. Aby uzyskać wyniki OCR dla danego formularza źródłowego, wykonaj poniższe kroki:

1. Wywołaj interfejs API **[analizowania układu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** w kontenerze układu odczytu z plikiem wejściowym w ramach treści żądania. Zapisz identyfikator znaleziony w nagłówku **operacji** odpowiedzi.
1. Wywołaj interfejs API **[wyników Get analizowanie układu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** przy użyciu identyfikatora operacji z poprzedniego kroku.
1. Pobierz odpowiedź i Zapisz zawartość do pliku. Dla każdego formularza źródłowego odpowiedni plik OCR powinien zawierać oryginalną nazwę pliku `.ocr.json`. Dane wyjściowe OCR w formacie JSON powinny mieć następujący format. Zobacz [przykładowy plik OCR](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) , aby zapoznać się z pełnym przykładem. 

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

### <a name="create-the-label-files"></a>Utwórz pliki etykiet

Pliki etykiet zawierają skojarzenia klucz-wartość wprowadzone ręcznie przez użytkownika. Są one potrzebne do szkolenia danych z etykietami, ale nie każdy plik źródłowy musi mieć odpowiedni plik etykiet. Pliki źródłowe bez etykiet będą traktowane jako zwykłe dokumenty szkoleniowe. Zalecamy używanie co najmniej pięciu plików z etykietą w celu zapewnienia niezawodnego szkolenia.

Podczas tworzenia pliku etykiet można opcjonalnie określić regiony&mdash;dokładne położenia wartości w dokumencie. Zapewni to uczenie nawet wyższą dokładność. Regiony są sformatowane jako zbiór ośmiu wartości odpowiadających czterem X, współrzędnej Y: w lewym górnym rogu, w prawym górnym rogu i w lewym dolnym rogu. Wartości współrzędnych należą do zakresu od zera do jednego, skalowane do wymiarów strony.

Dla każdego formularza źródłowego plik etykiety powinien zawierać oryginalną nazwę pliku `.labels.json`. Plik etykiety powinien mieć następujący format. Zapoznaj się z przykładowym [plikiem etykiet](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) .

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
> Do każdego elementu tekstowego można zastosować tylko jedną etykietę, a każda etykieta może zostać zastosowana tylko raz na stronie. Nie można obecnie zastosować etykiety na wielu stronach.


## <a name="train-a-model-using-labeled-data"></a>Uczenie modelu przy użyciu danych z etykietami

Aby przeprowadzić uczenie modelu z danymi z etykietami, Wywołaj interfejs API **[niestandardowego modelu uczenia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** , uruchamiając następujący kod w języku Python. Przed uruchomieniem kodu wprowadź następujące zmiany:

1. Zamień `<Endpoint>` na adres URL punktu końcowego dla zasobu aparatu rozpoznawania formularza.
1. Zamień `<SAS URL>` na adres URL sygnatury dostępu współdzielonego (SAS) kontenera magazynu obiektów blob platformy Azure. Aby pobrać adres URL SAS, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener i wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Zamień `<Blob folder name>` na nazwę folderu w kontenerze obiektów blob, w którym znajdują się dane wejściowe. Lub, jeśli Twoje dane są w katalogu głównym, pozostaw to pole puste i Usuń `"prefix"` je z treści żądania HTTP.

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

## <a name="get-training-results"></a>Pobierz wyniki szkoleń

Po rozpoczęciu operacji pouczenia należy użyć zwróconego identyfikatora, aby pobrać stan operacji. Dodaj następujący kod w dolnej części skryptu języka Python. Spowoduje to użycie wartości identyfikatora z rozmowy szkoleniowej w nowym wywołaniu interfejsu API. Operacja szkoleniowa jest asynchroniczna, dlatego skrypt wywołuje interfejs API w regularnych odstępach czasu, aż do momentu ukończenia stanu szkolenia. Zalecamy interwał co najmniej jednej sekundy.

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

Po zakończeniu procesu szkolenia otrzymasz `201 (Success)` odpowiedź z zawartością JSON, taką jak poniższy. Odpowiedź została skrócona dla uproszczenia.

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

Po zakończeniu procesu otrzymasz `202 (Success)` odpowiedź z zawartością JSON w następującym formacie. Odpowiedź została skrócona dla uproszczenia. Główne skojarzenia klucz/wartość znajdują się w `"documentResults"` węźle. Wyniki interfejsu API układu (zawartość i położenie całego tekstu w dokumencie) znajdują się w `"readResults"` węźle.

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

## <a name="improve-results"></a>Popraw wyniki

Przeanalizuj `"confidence"` wartości dla każdego wyniku klucza/wartości pod `"documentResults"` węzłem. Należy również przyjrzeć się ocenom zaufania w `"readResults"` węźle, który odpowiada operacji układu. Wiarygodność wyników układu nie wpływa na wiarygodność wyników wyodrębniania klucza/wartości, dlatego należy zaznaczyć oba te elementy.
* Jeśli oceny pewności dla operacji układu są niskie, spróbuj poprawić jakość dokumentów wejściowych (zobacz [wymagania wejściowe](../overview.md#input-requirements)).
* Jeśli oceny zaufania dla operacji wyodrębniania klucza/wartości są niskie, upewnij się, że analizowane dokumenty są tego samego typu co dokumenty używane w zestawie szkoleniowym. Jeśli dokumenty w zestawie szkoleniowym mają różne wahania, należy podzielić je na różne foldery i przeanalizować jeden model dla każdej odmiany.

### <a name="avoid-cluttered-labels"></a>Unikaj nieczytelnych etykiet

Czasami w przypadku zastosowania różnych etykiet w tym samym wierszu tekstu usługa może scalić te etykiety w jedno pole. Na przykład w adresie można oznaczyć miasto, Województwo i kod pocztowy jako różne pola, ale podczas przewidywania te pola nie są rozpoznawane osobno.

Rozumiemy, że ten scenariusz jest istotny dla naszych klientów i pracujemy nad ulepszeniem tego scenariusza w przyszłości. Obecnie zaleca się, aby nasi użytkownicy mogli etykietować wiele pól, które są w jednym polu, a następnie oddzielić te warunki w trakcie przetwarzania końcowego wyników wyodrębniania.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób korzystania z interfejsu API REST aparatu rozpoznawania formularzy w języku Python w celu uczenia modelu z ręcznymi etykietami danych. Następnie zapoznaj się z [dokumentacją interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) w celu EKSPLOROWANIA interfejsu API rozpoznawania formularzy o większej głębokości.
