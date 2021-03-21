---
title: Potwierdzenia — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje związane z analizą odbioru przy użyciu interfejsu API rozpoznawania i limitów.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 81e8cd5cf4af8da76ae4eb09bed5a4ee0368da4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467361"
---
# <a name="form-recognizer-prebuilt-receipt-model"></a>Model odbioru prekompilowanego aparatu rozpoznawania formularzy

Aparat rozpoznawania formularzy platformy Azure umożliwia analizowanie i wyodrębnianie informacji z przyjęć sprzedaży przy użyciu wbudowanego modelu paragonów. Łączy nasze zaawansowane funkcje [rozpoznawania znaków optycznych (OCR)](../computer-vision/concept-recognizing-text.md) z modelami uczenia głębokiego, aby wyodrębnić najważniejsze informacje z przyjęć pisanych w języku angielskim.

## <a name="understanding-receipts"></a>Informacje o potwierdzeniach

Wiele firm i użytkowników nadal polega na ręcznym wyodrębnieniu danych z przyjęć sprzedaży. Automatyczne wyodrębnianie danych z tych przyjęć może być skomplikowane. Paragony mogą być Crumpled, trudne do odczytania, mieć elementy napisane ręcznie i zawierać obrazy smartphone o niskiej jakości. Ponadto szablony i pola paragonów mogą się różnić w zależności od rynku, regionu i handlowca. Te wyzwania związane z wyodrębnianiem i wykrywaniem pól sprawiają, że przetwarzanie potwierdzenia jest unikatowy.  

Interfejs API paragonów używa optycznego rozpoznawania znaków (OCR) i naszego prekompilowanego modelu w celu włączenia dużych scenariuszy przetwarzania paragonów. W przypadku interfejsu API odbierania nie ma potrzeby uczenia modelu. Wyślij obraz paragonu do interfejsu API usługi Analiza przychodów, a dane są wyodrębniane.

![Przykładowe potwierdzenie](./media/receipts-example.jpg)


## <a name="what-does-the-receipt-service-do"></a>Co robi usługa Paragon? 

Wstępnie utworzona usługa paragonów wyodrębnia zawartość otrzymanych danych sprzedaży, czyli &mdash; Typ paragonu, który często uzyskuje się w restauracjach, detalicznych lub w sklepie spożywczym.

### <a name="fields-extracted"></a>Wyodrębnione pola

|Nazwa| Typ | Opis | Tekst | Wartość (standardowe dane wyjściowe) |
|:-----|:----|:----|:----| :----|
| Paragon | ciąg | Typ paragonu sprzedaży | Podzielonych |  |
| Sprzedawcy | ciąg | Nazwa sprzedawcy, który wystawił potwierdzenie | Contoso |  |
| MerchantPhoneNumber | phoneNumber | Numer telefonu handlowego na liście | 987-654-3210 | + 19876543210 |
| MerchantAddress | ciąg | Na liście adres handlowca | 123, główny St Redmond WA 98052 |  |
| TransactionDate | data | Data wystawienia potwierdzenia | 06, 2019 | 2019-06-26  |
| TransactionTime | time | Godzina wystawienia potwierdzenia | 4:49 PM | 16:49:00  |
| Łącznie | liczba | Całkowita suma przychodów | $14,34 | 14,34 |
| Suma częściowa | liczba | Suma częściowa przychodu, często przed opodatkowaniem, jest stosowana | $12,34 | 12.34 |
| Podatek | liczba | Podatek od odbioru, często podatek od sprzedaży lub odpowiednik | 2,00 USD | 2,00 |
| Porada | liczba | Porada dołączona przez kupującego | $1,00 | 1,00 |
| Elementy | Tablica obiektów | Wyodrębnione elementy wiersza, z nazwami, ilością, ceną jednostkową i łączną ceną wyekstrahowaną | |
| Nazwa | ciąg | Nazwa elementu | Surface Pro 6 | |
| Liczba | liczba | Ilość każdego elementu | 1 | |
| Cena | liczba | Indywidualna cena każdej jednostki elementu | $999,00 | 999,00 |
| Cena łączna | liczba | Łączna cena elementu wiersza | $999,00 | 999,00 |

### <a name="additional-features"></a>Dodatkowe funkcje

Interfejs API paragonu zwraca również następujące informacje:

* Poziom ufności pól (każde pole zwraca skojarzoną wartość zaufania)
* Tekst nieprzetworzony OCR (wyodrębniany tekst wyjściowy tekstu dla całego potwierdzenia)
* Pole ograniczenia dla każdej wartości, wiersza i słowa

## <a name="try-it-out"></a>Czas to wypróbować

Aby wypróbować usługę potwierdzenia rozpoznawania formularzy, przejdź do narzędzia przykładowego interfejsu użytkownika w trybie online:

> [!div class="nextstepaction"]
> [Wypróbuj wbudowane modele](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Wymagania wejściowe

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Obsługiwane ustawienia regionalne 

* **Wstępnie skompilowane przyjęcie w wersji 2.0** (ga) obsługuje przyjęcia sprzedaży w ustawieniach regionalnych en-us
* **Wstępnie utworzone przyjęcie — wersja zapoznawcza. 3** (publiczna wersja zapoznawcza) dodaje dodatkową pomoc techniczną dla następujących ustawień regionalnych usługi: 
  * EN-AU 
  * EN-CA 
  * PL GB 
  * DODATEK EN-IN 

  > [!NOTE]
  > Dane wejściowe języka 
  >
  > Wstępnie utworzone przyjęcie v 2.1 — wersja zapoznawcza. 3 zawiera opcjonalny parametr żądania, aby określić ustawienia regionalne odbioru z dodatkowych rynków w języku angielskim. W przypadku przyjęć sprzedaży w języku angielskim z Australii (EN-AU), Kanada (EN-CA), Wielka Brytania (en-GB) i Indie (EN-IN) można określić ustawienia regionalne, aby uzyskać ulepszone wyniki. Jeśli nie określono ustawień regionalnych w wersji 2.1-Preview. 3, model będzie domyślnie modelem EN-US.


## <a name="the-analyze-receipt-operation"></a>Operacja analizy przychodu

[Przeanalizuj potwierdzenie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync) pobiera obraz lub plik PDF z paragonu jako dane wejściowe i wyodrębnia wartości zainteresowań i tekstu. Wywołanie zwraca pole nagłówka odpowiedzi o nazwie `Operation-Location` . `Operation-Location`Wartość jest adresem URL, który zawiera identyfikator wynik do użycia w następnym kroku.

|Nagłówek odpowiedzi| Adres URL wyniku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>Operacja pobrania wyniku analizy przychodu

Drugim krokiem jest wywołanie operacji [Get Analizuj wynik przyjęcia](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult) . Ta operacja przyjmuje jako dane wejściowe Identyfikator wyniku, który został utworzony przez operację Analizuj potwierdzenie. Zwraca odpowiedź JSON, która zawiera pole **stanu** z następującymi możliwymi wartościami. Tę operację można wywołać iteracyjnie, dopóki nie zwróci wartości z wartością **sukces** . Użyj interwału od 3 do 5 sekund, aby uniknąć przekroczenia liczby żądań na sekundę (RPS pliku).

|Pole| Typ | Możliwe wartości |
|:-----|:----:|:----|
|status | ciąg | notStarted: operacja nie została rozpoczęta. |
| |  | Uruchamianie: operacja analizy jest w toku. |
| |  | Niepowodzenie: operacja analizy zakończyła się niepowodzeniem. |
| |  | powodzenie: operacja analizy zakończyła się pomyślnie. |

Gdy wartość w polu **stan** zostanie **zakończona pomyślnie** , odpowiedź JSON będzie zawierać opis potwierdzenia i wyniki rozpoznawania tekstu. Wynik interpretacji paragonu jest zorganizowany jako słownik nazwanych wartości pól. Każda wartość zawiera wyodrębniony tekst, znormalizowana wartość, pole ograniczenia, pewność i odpowiadające im elementy programu Word. Wynik rozpoznawania tekstu jest zorganizowany jako hierarchia wierszy i słów, z tekstem, obwiednią i informacjami o ufności.

![Przykładowe wyniki odbioru](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Przykładowe dane wyjściowe JSON


Odpowiedź na wynik operacji Pobierz analizę wyniku przyjęcia będzie strukturalną reprezentacją przyjęcia ze wszystkimi wyodrębnionymi informacjami.  Zobacz tutaj, aby znaleźć [przykładowy plik paragonu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-allinone.jpg) i jego strukturalne [dane wyjściowe otrzymanego przykładu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

Zobacz następujący przykład pomyślnej odpowiedzi JSON:
* `"readResults"`Węzeł zawiera cały rozpoznany tekst. Tekst jest zorganizowany według strony, następnie według wiersza, a następnie poszczególnych słów. 
* `"documentResults"`Węzeł zawiera wartości specyficzne dla karty biznesowej, które zostały odnalezione przez model. W tym miejscu znajdziesz przydatne pary klucz/wartość, takie jak imię, nazwisko, nazwa firmy i inne.

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

## <a name="customer-scenarios"></a>Scenariusze klientów  

Dane wyodrębnione za pomocą interfejsu API paragonów mogą służyć do wykonywania różnych zadań. Poniżej przedstawiono kilka przykładów klientów, którzy korzystali z interfejsu API odbierania.

### <a name="business-expense-reporting"></a>Raportowanie wydatków w firmie  

Często wydatki biznesowe obejmują czas poświęcany na ręczne wprowadzanie danych z obrazów przyjęć. Korzystając z interfejsu API paragonów, można użyć wyodrębnionych pól, aby częściowo zautomatyzować ten proces i szybko analizować swoje wpłaty.  

Interfejs API paragonów jest prostym wyjściem JSON umożliwiającym używanie wyodrębnionych wartości pól na wiele sposobów. Integruj z wewnętrznymi aplikacjami do wydatków, aby wstępnie wypełnić raporty dotyczące wydatków. Aby uzyskać więcej informacji na ten temat, Przeczytaj o tym, jak Acumatica korzysta z interfejsu API odbioru w celu [raportowania wydatków na mniej bolesnym proces](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Inspekcja i ewidencjonowanie aktywności

Dane wyjściowe interfejsu API paragonów mogą być również używane do analizowania dużej liczby wydatków w różnych punktach w ramach raportowania wydatków i procesu zwrotnego. Możesz przetwarzać potwierdzenia, aby klasyfikacja je do ręcznego przeprowadzania inspekcji lub szybkich zatwierdzeń.  

Dane wyjściowe paragonu są również przydatne do ogólnego utrzymywania się w ramach firmy lub użytku osobistego. Korzystając z interfejsu API paragonów, można przekształcić dowolne nieprzetworzone dane obrazu/pliku PDF na cyfrowe dane wyjściowe, które są funkcjonalne.

### <a name="consumer-behavior"></a>Zachowanie konsumentów 

Paragony zawierają przydatne dane, których można użyć do analizowania zachowań klientów i trendów zakupów.

Interfejs API paragonu zapewnia również [funkcję przetwarzania paragonów programu AI Builder](/ai-builder/prebuilt-receipt-processing).

## <a name="next-steps"></a>Następne kroki

 . Zacznij pisać aplikację przetwarzania paragonów z aparatem rozpoznawania formularzy w wybranym języku programistycznym.

> [!div class="nextstepaction"]
> [Kończenie pracy z aparatem szybkiego startu formularza](quickstarts/client-library.md)

## <a name="see-also"></a>Zobacz też

* [Co to jest rozpoznawanie formularzy?](overview.md)
* [Dokumentacja interfejsu API rozpoznawania formularzy](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)
>
