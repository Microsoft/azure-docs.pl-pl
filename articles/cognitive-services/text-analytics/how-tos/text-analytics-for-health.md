---
title: Jak używać analiza tekstu na potrzeby kondycji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wyodrębnić i oznaczyć informacje medyczne z niestrukturalnego tekstu klinicznego za pomocą analiza tekstu na potrzeby kondycji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 27af0ffdeb254dbb671a0618d0e9973336a7f529
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559018"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Instrukcje: korzystanie z analiza tekstu na potrzeby kondycji (wersja zapoznawcza)

> [!IMPORTANT] 
> Analiza tekstu dla kondycji to funkcja w wersji zapoznawczej udostępniona "AS IS" i "ze wszystkimi BŁĘDami". W związku z tym **Analiza tekstu dla kondycji (wersja zapoznawcza) nie należy implementować ani wdrażać w żadnym środowisku produkcyjnym.** Analiza tekstu dla kondycji nie są przeznaczone do użytku jako wyrób medyczny, pomoc techniczna kliniczna, narzędzie diagnostyczne ani inne technologie przeznaczone do celów diagnostycznych, pozyskiwania, łagodzenia, traktowania lub zapobiegania chorobom lub innym warunkom, a firma Microsoft nie udziela żadnych licencji ani prawa do korzystania z tej funkcji w taki sposób. Ta możliwość nie została zaprojektowana lub zaplanowana jako zamiennik do profesjonalnego doradztwa medycznego lub opinii o opieki zdrowotnej, diagnostyki, traktowania lub klinicznej oceny specjalisty ds. ochrony zdrowia i nie powinna być używana jako taka. Klient jest odpowiedzialny wyłącznie za korzystanie z analiza tekstu na potrzeby kondycji. Firma Microsoft nie gwarantuje, że analiza tekstu w zakresie kondycji lub materiały dostarczone w związku z tą możliwością będą wystarczające dla jakichkolwiek celów medycznych lub w inny sposób spełniają wymagania zdrowotne lub medyczne każdej osoby. 


Analiza tekstu dla kondycji jest funkcją usługi interfejs API analizy tekstu, która wyodrębnia i etykietuje odpowiednie informacje medyczne z tekstów bez struktury, takich jak notatki lekarskie, podsumowania zrzutów, dokumenty kliniczne i rejestry kondycji elektronicznej.  Istnieją dwa sposoby korzystania z tej usługi: 

* Internetowy interfejs API (asynchronicznie) 
* Kontener platformy Docker (synchroniczny)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Funkcje

Analiza tekstu dla kondycji wykonuje nazwane rozpoznawanie jednostek (NER), wyodrębnianie relacji, negację jednostki oraz łączenie jednostek w języku angielskim w celu uzyskania wglądu w dane kliniczne i biomedyczne bez struktury.

### <a name="named-entity-recognition"></a>[Rozpoznawanie jednostek nazwanych](#tab/ner)

Rozpoznawanie jednostek nazwanych wykrywa słowa i frazy wymienione w tekście niestrukturalnym, które mogą być skojarzone z co najmniej jednym typem semantycznym, na przykład diagnostyką, nazwą leczenia, objawem/podpisem lub wiekiem.

> [!div class="mx-imgBorder"]
> ![NER kondycji](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Wyodrębnianie relacji](#tab/relation-extraction)

Wyodrębnienie relacji identyfikuje znaczące połączenia między pojęciami wymienionymi w tekście. Na przykład relacja "czas warunku" można znaleźć, kojarząc nazwę warunku z godziną. 

> [!div class="mx-imgBorder"]
> ![Kondycja](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Łączenie jednostek](#tab/entity-linking)

Łączenie jednostek odróżnia odrębne jednostki, kojarząc nazwane jednostki wymienione w tekście z pojęciami znajdującymi się w wstępnie zdefiniowanej bazie danych koncepcji. Na przykład ujednolicony system językowy (UMLS).

> [!div class="mx-imgBorder"]
> ![Stan EL](../media/ta-for-health/health-entity-linking.png)

Analiza tekstu dla kondycji obsługuje łączenie z słownikami kondycji i biomedycznej, które znajdują się w źródle wiedzy o systemie Unified[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html).

### <a name="negation-detection"></a>[Wykrywanie negacji](#tab/negation-detection) 

Znaczenie dla zawartości medycznej ma duże wpływ na modyfikatory, takie jak Negacja, co może mieć krytyczne znaczenie w przypadku niezdiagnozowania. Analiza tekstu dla kondycji obsługuje wykrywanie negacji dla różnych jednostek wymienionych w tekście. 

> [!div class="mx-imgBorder"]
> ![Kondycja — minus](../media/ta-for-health/health-negation.png)

---

Aby uzyskać pełną listę obsługiwanych jednostek, zobacz [Kategorie jednostek](../named-entity-types.md?tabs=health) zwrócone przez analiza tekstu dla kondycji.

### <a name="supported-languages-and-regions"></a>Obsługiwane języki i regiony

Analiza tekstu dla kondycji obsługuje tylko dokumenty w języku angielskim. 

Analiza tekstu dla hostowanego interfejsu API sieci Web usługi kondycji jest obecnie dostępny tylko w następujących regionach: zachodnie stany USA 2, Wschodnie stany USA 2, środkowe stany USA, Europa Północna i Europa Zachodnia.

## <a name="request-access-to-the-public-preview"></a>Zażądaj dostępu do publicznej wersji zapoznawczej

Wypełnij i prześlij [formularz żądania Cognitive Services](https://aka.ms/csgate) , aby zażądać dostępu do analiza tekstu dla publicznej wersji zapoznawczej kondycji. Za użycie kondycji nie będą naliczane opłaty za analiza tekstu. 

Formularz żąda informacji o użytkowniku, firmie i scenariuszu użytkownika, dla którego będziesz używać kontenera. Po przesłaniu formularza zespół usługi Azure Cognitive Services sprawdzi go i wyśle wiadomość e-mail z decyzją.

> [!IMPORTANT]
> * W formularzu musisz użyć adresu e-mail skojarzonego z IDENTYFIKATORem subskrypcji platformy Azure.
> * Zasób platformy Azure, którego używasz, musi zostać utworzony przy użyciu zatwierdzonego identyfikatora subskrypcji platformy Azure. 
> * Sprawdź pocztę e-mail (Skrzynka odbiorcza i foldery śmieci), aby uzyskać aktualizacje stanu aplikacji firmy Microsoft.

## <a name="using-the-docker-container"></a>Używanie kontenera Docker 

Aby uruchomić analiza tekstu dla kontenera kondycji w swoim środowisku, postępuj zgodnie [z tymi instrukcjami, aby pobrać i zainstalować kontener](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Używanie biblioteki klienta

Najnowsza wersja wstępna biblioteki klienta analiza tekstu umożliwia wywoływanie analiza tekstu na potrzeby kondycji przy użyciu obiektu klienckiego. Zapoznaj się z dokumentacją referencyjną i Zobacz przykłady w witrynie GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Wysyłanie żądania interfejsu API REST 

### <a name="preparation"></a>Przygotowanie

Analiza tekstu w przypadku kondycji daje wynik wyższej jakości, gdy zostanie nadana mniejsza ilość tekstu do pracy. Jest to przeciwieństwo do niektórych innych funkcji analiza tekstu, takich jak wyodrębnianie kluczowych fraz, które wykonują lepsze działania w większych blokach tekstu. Aby uzyskać najlepsze wyniki tych operacji, rozważ odpowiednio restrukturyzację danych wejściowych.

Musisz mieć dokumenty JSON w tym formacie: ID, text i Language. 

Rozmiar dokumentu musi zawierać 5 120 znaków na dokument. Aby uzyskać maksymalną dozwoloną liczbę dokumentów w kolekcji, zobacz artykuł dotyczący [limitów danych](../concepts/data-limits.md?tabs=version-3) w obszarze pojęcia. Kolekcja jest przesyłana w treści żądania.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Tworzenie struktury żądania interfejsu API dla hostowanego asynchronicznego interfejsu API sieci Web

W przypadku kontenera i hostowanego interfejsu API sieci Web należy utworzyć żądanie POST. Możesz [użyć](text-analytics-how-to-call-api.md)programu do wypełniania, polecenia lub **konsoli testowania interfejsu API** w analiza tekstu, aby [uzyskać informacje o kondycji hostowanego interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) , aby szybko utworzyć i wysłać żądanie post do hostowanego interfejsu API sieci Web w żądanym regionie. 

Poniżej znajduje się przykład pliku JSON dołączonego do analiza tekstu dla treści wpisu żądania interfejsu API kondycji:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Hostowana asynchroniczna odpowiedź interfejsu API sieci Web 

Ponieważ to żądanie POST służy do przesyłania zadania dla operacji asynchronicznej, w obiekcie Response nie ma tekstu.  Wymagana jest jednak wartość klucza operacji w nagłówkach odpowiedzi w celu uzyskania żądania GET w celu sprawdzenia stanu zadania i danych wyjściowych.  Poniżej znajduje się przykład wartości klucza lokalizacji operacji w nagłówku odpowiedzi żądania POST:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

Aby sprawdzić stan zadania, wprowadź żądanie GET do adresu URL w polu Nagłówek klucza operacji w odpowiedzi na wpis.  Następujące stany są używane w celu odzwierciedlenia stanu zadania:,,,,, `NotStarted` `running` `succeeded` `failed` `rejected` `cancelling` i `cancelled` .  

Zadanie można anulować przy użyciu `NotStarted` `running` stanu lub z WYWOŁANIEM Delete http na ten sam adres URL co żądanie Get.  Więcej informacji na temat wywołania usuwania jest dostępnych w [Analiza tekstu dla dokumentacji interfejsu API hostowanej kondycji](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Poniżej przedstawiono przykład odpowiedzi na żądanie GET.  Należy pamiętać, że dane wyjściowe są dostępne do pobrania, dopóki nie przeprowadzono operacji `expirationDateTime` (24 godziny od momentu utworzenia zadania), po upływie którego dane wyjściowe zostaną usunięte.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Tworzenie struktury żądania interfejsu API dla kontenera

Aby przesłać zapytanie do wdrożonego kontenera, można [użyć programu Poster](text-analytics-how-to-call-api.md) lub przykładowego żądania zwinięcie, zastępując `serverURL` zmienną odpowiednią wartością.  Zwróć uwagę, że wersja interfejsu API w adresie URL kontenera jest inna niż hostowany interfejs API.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Poniższy kod JSON jest przykładem pliku JSON dołączonego do analiza tekstu dla treści wpisu żądania interfejsu API kondycji:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Treść odpowiedzi kontenera

Poniższy kod JSON jest analiza tekstu przykładem treści odpowiedzi interfejsu API kondycji z kontenera synchronicznego wywołania:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Dane wyjściowe wykrywania negacji

W przypadku korzystania z wykrywania negacji w niektórych przypadkach pojedynczy termin negacji może zająć kilka warunków jednocześnie. Negacja rozpoznanej jednostki jest reprezentowana w danych wyjściowych JSON przez wartość logiczną `isNegated` flagi, na przykład:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Dane wyjściowe wyodrębniania relacji

Dane wyjściowe wyodrębniania relacji zawierają odwołania identyfikatora URI do *źródła* relacji i jego *celu*. Jednostki z rolą relacji `ENTITY` są przypisane do `target` pola. Jednostki z rolą relacji `ATTRIBUTE` są przypisane do `source` pola. Relacje skrótu zawierają dwukierunkowe `source` i `target` pola i `bidirectional` zostaną ustawione na `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Zobacz także

* [Przegląd analizy tekstu](../overview.md)
* [Kategorie nazwanych jednostek](../named-entity-types.md)
* [Co nowego](../whats-new.md)
