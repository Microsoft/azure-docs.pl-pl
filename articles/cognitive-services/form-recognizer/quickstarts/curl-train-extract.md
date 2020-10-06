---
title: 'Szybki Start: uczenie modelu i wyodrębnianie danych formularza przy użyciu narzędzia do rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem do uczenia modelu i wyodrębnienia danych z formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: b0532007ff03cd9dcf253824a1158fd0b8661120
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760465"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Szybki Start: uczenie modelu aparatu rozpoznawania formularzy i wyodrębnianie danych formularza przy użyciu interfejsu API REST z zwinięciem

W tym przewodniku szybki start użyjesz interfejsu API REST usługi Azure Forms rozpoznającego z zwinięciem, aby przeszkolić i wypróbować formularze w celu wyodrębnienia par klucz-wartość i tabel.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- zainstalowano [zwinięcie](https://curl.haxx.se/windows/) .
- Zestaw składający się z co najmniej sześciu formularzy tego samego typu. Będziesz używać pięciu z nich do uczenia modelu, a następnie przetestowania go przy użyciu szóstej formy. Formularze mogą mieć różne typy plików, ale muszą być tego samego typu dokumentu. Możesz użyć [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451) dla tego przewodnika Szybki Start. Przekaż pliki szkoleniowe do katalogu głównego kontenera magazynu obiektów BLOB na koncie usługi Azure Storage w warstwie Standardowa wydajność. Pliki testowe można umieścić w osobnym folderze.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Uczenie modelu aparatu rozpoznawania formularzy

Najpierw będziesz potrzebować zestawu danych szkoleniowych w obiekcie blob usługi Azure Storage. Należy mieć co najmniej pięć wypełnionych formularzy (dokumentów PDF i/lub obrazów) tego samego typu i struktury co główne dane wejściowe. Lub można użyć pojedynczego pustego formularza z dwoma wypełnionymi formularzami. Nazwa pliku pustego formularza musi zawierać słowo "Empty". Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../build-training-data-set.md) w celu uzyskania wskazówek i opcji tworzenia danych szkoleniowych.

> [!NOTE]
> Możesz użyć funkcji etykiety danych, aby ręcznie oznaczyć niektóre lub wszystkie dane szkoleniowe wcześniej. Jest to bardziej skomplikowany proces, ale wynikiem jest lepszy przeszkolony model. Aby dowiedzieć się więcej na temat tej funkcji, zobacz sekcję [uczenie z etykietami](../overview.md#train-with-labels) .



Aby przeprowadzić uczenie modelu aparatu rozpoznawania formularzy przy użyciu dokumentów w kontenerze obiektów blob platformy Azure, Wywołaj interfejs API **[niestandardowego modelu uczenia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** , uruchamiając następujące polecenie. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `<subscription key>` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `<SAS URL>` na adres URL sygnatury dostępu współdzielonego (SAS) kontenera magazynu obiektów blob platformy Azure. Aby pobrać adres URL SAS, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener i wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna ona mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

    # <a name="v20"></a>[Wersja 2.0](#tab/v2-0)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    # <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    
    ---



Otrzymasz `201 (Success)` odpowiedź z nagłówkiem **lokalizacji** . Wartość tego nagłówka jest IDENTYFIKATORem nowego, nauczonego modelu.

## <a name="get-training-results"></a>Pobierz wyniki szkoleń

Po rozpoczęciu operacji pouczenia należy użyć nowej operacji, **[pobrać model niestandardowy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** w celu sprawdzenia stanu szkolenia. Przekaż Identyfikator modelu do tego wywołania interfejsu API, aby sprawdzić stan szkolenia:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany za pomocą klucza subskrypcji aparatu rozpoznawania formularza.
1. Zamień `<subscription key>` na klucz subskrypcji
1. Zamień na `<model ID>` Identyfikator modelu otrzymany w poprzednim kroku



# <a name="v20"></a>[Wersja 2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```ce\": \""<SAS URL>"\"}"
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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analizowanie formularzy par klucz-wartość i tabel

Następnie będziesz używać nowo przeszkolonego modelu do analizowania dokumentu i wyodrębniania par klucz-wartość i tabel z tej usługi. Wywołaj interfejs API **[analizowania formularzy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** , uruchamiając następujące polecenie. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany z klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień na `<model ID>` Identyfikator modelu otrzymany w poprzedniej sekcji.
1. Zamień na `<SAS URL>` adres URL sygnatury dostępu współdzielonego z plikiem w usłudze Azure Storage. Postępuj zgodnie z instrukcjami w sekcji szkolenia, ale zamiast uzyskać adres URL sygnatury dostępu współdzielonego dla całego kontenera obiektów blob, uzyskaj jeden dla określonego pliku, który chcesz analizować.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

# <a name="v20"></a>[Wersja 2.0](#tab/v2-0)    
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
# <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)    
```bash
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
    
---



Otrzymasz `202 (Success)` odpowiedź z nagłówkiem **lokalizacji operacji** . Wartość tego nagłówka zawiera identyfikator wyników używany do śledzenia wyników operacji analizy. Zapisz ten identyfikator wyników dla następnego kroku.

## <a name="get-the-analyze-results"></a>Pobierz wyniki analizy

Użyj poniższego interfejsu API, aby zbadać wyniki operacji analizy.

1. Zamień na `<Endpoint>` punkt końcowy uzyskany z klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień na `<result ID>` Identyfikator otrzymany w poprzedniej sekcji.
1. Zastąp element `<subscription key>` kluczem subskrypcji.


# <a name="v20"></a>[Wersja 2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
    
---

Otrzymasz `200 (Success)` odpowiedź z treścią JSON w następującym formacie. Wynik został skrócony do uproszczenia. Zwróć uwagę na `"status"` pole w dolnej części strony. Będzie to miało wartość, `"succeeded"` gdy operacja analizy zostanie zakończona. Jeśli operacja analizy nie została ukończona, należy ponownie wykonać zapytanie dotyczące usługi przez ponowne uruchomienie polecenia. Zalecamy przedziału co najmniej jednej sekundy między wywołaniami.

Główne skojarzenia pary klucz/wartość i tabele znajdują się w `"pageResults"` węźle. Jeśli określono również zwykłe Wyodrębnianie tekstu za pomocą parametru adresu URL *includeTextDetails* , w `"readResults"` węźle będzie wyświetlana zawartość i położenie całego tekstu w dokumencie.

Te przykładowe dane wyjściowe JSON zostały skrócone dla uproszczenia.

# <a name="v20"></a>[Wersja 2.0](#tab/v2-0)
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
# <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)    
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

---


## <a name="improve-results"></a>Popraw wyniki

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem do uczenia modelu i uruchomienia go w przykładowym scenariuszu. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
