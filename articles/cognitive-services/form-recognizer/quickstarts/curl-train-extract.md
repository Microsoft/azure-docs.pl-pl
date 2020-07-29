---
title: 'Szybki Start: uczenie modelu i wyodrębnianie danych formularza przy użyciu narzędzia do rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem do uczenia modelu i wyodrębnienia danych z formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: f89ab93820770eb8b5485bb7911c60fe2438454a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026823"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Szybki Start: uczenie modelu aparatu rozpoznawania formularzy i wyodrębnianie danych formularza przy użyciu interfejsu API REST z zwinięciem

W tym przewodniku szybki start użyjesz interfejsu API REST usługi Azure Forms rozpoznającego z zwinięciem, aby przeszkolić i wypróbować formularze w celu wyodrębnienia par klucz-wartość i tabel.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- zainstalowano [zwinięcie](https://curl.haxx.se/windows/) .
- Zestaw składający się z co najmniej sześciu formularzy tego samego typu. Będziesz używać pięciu z nich do uczenia modelu, a następnie przetestowania go przy użyciu szóstej formy. Formularze mogą mieć różne typy plików, ale muszą być tego samego typu dokumentu. Możesz użyć [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451) dla tego przewodnika Szybki Start. Przekaż pliki szkoleniowe do katalogu głównego kontenera magazynu obiektów BLOB na koncie usługi Azure Storage. Pliki testowe można umieścić w osobnym folderze.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Uczenie modelu aparatu rozpoznawania formularzy

Najpierw będziesz potrzebować zestawu danych szkoleniowych w obiekcie blob usługi Azure Storage. Należy mieć co najmniej pięć wypełnionych formularzy (dokumentów PDF i/lub obrazów) tego samego typu i struktury co główne dane wejściowe. Lub można użyć pojedynczego pustego formularza z dwoma wypełnionymi formularzami. Nazwa pliku pustego formularza musi zawierać słowo "Empty". Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../build-training-data-set.md) w celu uzyskania wskazówek i opcji tworzenia danych szkoleniowych.

> [!NOTE]
> Możesz użyć funkcji etykiety danych, aby ręcznie oznaczyć niektóre lub wszystkie dane szkoleniowe wcześniej. Jest to bardziej skomplikowany proces, ale wynikiem jest lepszy przeszkolony model. Aby dowiedzieć się więcej na temat tej funkcji, zobacz sekcję [uczenie z etykietami](../overview.md#train-with-labels) .

Aby przeprowadzić uczenie modelu aparatu rozpoznawania formularzy przy użyciu dokumentów w kontenerze obiektów blob platformy Azure, Wywołaj interfejs API **[niestandardowego modelu uczenia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** , uruchamiając następujące polecenie. Przed uruchomieniem polecenia wprowadź następujące zmiany:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `<subscription key>` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień `<SAS URL>` na adres URL sygnatury dostępu współdzielonego (SAS) kontenera magazynu obiektów blob platformy Azure. Aby pobrać adres URL SAS, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener i wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Otrzymasz `201 (Success)` odpowiedź z nagłówkiem **lokalizacji** . Wartość tego nagłówka jest IDENTYFIKATORem nowego, nauczonego modelu. 

## <a name="get-training-results"></a>Pobierz wyniki szkoleń

Po rozpoczęciu operacji pouczenia należy użyć nowej operacji, **[pobrać model niestandardowy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** w celu sprawdzenia stanu szkolenia. Przekaż Identyfikator modelu do tego wywołania interfejsu API, aby sprawdzić stan szkolenia:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany za pomocą klucza subskrypcji aparatu rozpoznawania formularza.
1. Zamień `<subscription key>` na klucz subskrypcji
1. Zamień na `<model ID>` Identyfikator modelu otrzymany w poprzednim kroku

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

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

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Otrzymasz `202 (Success)` odpowiedź z nagłówkiem **lokalizacji operacji** . Wartość tego nagłówka zawiera identyfikator wyników używany do śledzenia wyników operacji analizy. Zapisz ten identyfikator wyników dla następnego kroku.

## <a name="get-the-analyze-results"></a>Pobierz wyniki analizy

Użyj poniższego interfejsu API, aby zbadać wyniki operacji analizy.

1. Zamień na `<Endpoint>` punkt końcowy uzyskany z klucza subskrypcji aparatu rozpoznawania formularza. Można go znaleźć na karcie **Przegląd** zasobów aparatu rozpoznawania formularza.
1. Zamień na `<result ID>` Identyfikator otrzymany w poprzedniej sekcji.
1. Zastąp element `<subscription key>` kluczem subskrypcji.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Otrzymasz `200 (Success)` odpowiedź z treścią JSON w następującym formacie. Wynik został skrócony do uproszczenia. Zwróć uwagę na `"status"` pole w dolnej części strony. Będzie to miało wartość, `"succeeded"` gdy operacja analizy zostanie zakończona. Jeśli operacja analizy nie została ukończona, należy ponownie wykonać zapytanie dotyczące usługi przez ponowne uruchomienie polecenia. Zalecamy przedziału co najmniej jednej sekundy między wywołaniami.

Główne skojarzenia pary klucz/wartość i tabele znajdują się w `"pageResults"` węźle. Jeśli określono również zwykłe Wyodrębnianie tekstu za pomocą parametru adresu URL *includeTextDetails* , w `"readResults"` węźle będzie wyświetlana zawartość i położenie całego tekstu w dokumencie.

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Popraw wyniki

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy z zwinięciem do uczenia modelu i uruchomienia go w przykładowym scenariuszu. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
