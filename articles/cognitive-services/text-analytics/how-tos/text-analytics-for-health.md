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
ms.date: 03/11/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 80a943d235783852f57832363b5af8048f010575
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599439"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Instrukcje: korzystanie z analiza tekstu na potrzeby kondycji (wersja zapoznawcza)

> [!IMPORTANT] 
> Analiza tekstu dla kondycji to funkcja w wersji zapoznawczej udostępniona "AS IS" i "ze wszystkimi BŁĘDami". W związku z tym **Analiza tekstu dla kondycji (wersja zapoznawcza) nie należy implementować ani wdrażać w żadnym środowisku produkcyjnym.** Analiza tekstu dla kondycji nie są przeznaczone do użytku jako wyrób medyczny, pomoc techniczna kliniczna, narzędzie diagnostyczne ani inne technologie przeznaczone do celów diagnostycznych, pozyskiwania, łagodzenia, traktowania lub zapobiegania chorobom lub innym warunkom, a firma Microsoft nie udziela żadnych licencji ani prawa do korzystania z tej funkcji w taki sposób. Ta możliwość nie została zaprojektowana lub zaplanowana jako zamiennik do profesjonalnego doradztwa medycznego lub opinii o opieki zdrowotnej, diagnostyki, traktowania lub klinicznej oceny specjalisty ds. ochrony zdrowia i nie powinna być używana jako taka. Klient jest odpowiedzialny wyłącznie za korzystanie z analiza tekstu na potrzeby kondycji. Firma Microsoft nie gwarantuje, że analiza tekstu w zakresie kondycji lub materiały dostarczone w związku z tą możliwością będą wystarczające dla jakichkolwiek celów medycznych lub w inny sposób spełniają wymagania zdrowotne lub medyczne każdej osoby. 


Analiza tekstu dla kondycji jest funkcją usługi interfejs API analizy tekstu, która wyodrębnia i etykietuje odpowiednie informacje medyczne z tekstów bez struktury, takich jak notatki lekarskie, podsumowania zrzutów, dokumenty kliniczne i rejestry kondycji elektronicznej.  Istnieją dwa sposoby korzystania z tej usługi: 

* [Internetowy interfejs API (asynchronicznie)](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Kontener platformy Docker (synchroniczny)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Funkcje

Analiza tekstu dla kondycji wykonuje nazwane rozpoznawanie jednostek (NER), wyodrębnianie relacji, negację jednostki oraz łączenie jednostek w języku angielskim w celu uzyskania wglądu w dane kliniczne i biomedyczne bez struktury.

### <a name="named-entity-recognition"></a>[Rozpoznawanie jednostek nazwanych](#tab/ner)

Rozpoznawanie jednostek nazwanych wykrywa słowa i frazy wymienione w tekście niestrukturalnym, które mogą być skojarzone z co najmniej jednym typem semantycznym, na przykład diagnostyką, nazwą leczenia, objawem/podpisem lub wiekiem.

> [!div class="mx-imgBorder"]
> ![NER kondycji](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Wyodrębnianie relacji](#tab/relation-extraction)

Wyodrębnienie relacji identyfikuje znaczące połączenia między pojęciami wymienionymi w tekście. Na przykład relacja "czas warunku" można znaleźć, kojarząc nazwę warunku z czasem lub między skrótem a pełnym opisem.  

> [!div class="mx-imgBorder"]
> ![Kondycja](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Łączenie jednostek](#tab/entity-linking)

Łączenie jednostek odróżnia odrębne jednostki przez skojarzenie nazwanych jednostek wymienionych w tekście z pojęciami znalezionymi we wstępnie zdefiniowanej bazie danych koncepcji, w tym systemu ujednoliconego języka medycznego (UMLS). Koncepcje medyczne są również przypisywane preferowanym nazewnictwem jako dodatkowa forma normalizacji.

> [!div class="mx-imgBorder"]
> ![Stan EL](../media/ta-for-health/health-entity-linking.png)

Analiza tekstu dla kondycji obsługuje łączenie z słownikami kondycji i biomedycznej, które znajdują się w źródle wiedzy o systemie Unified[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html).

### <a name="assertion-detection"></a>[Wykrywanie potwierdzenia](#tab/assertion-detection) 

Znaczenie dla zawartości medycznej ma duże wpływ na modyfikatory, takie jak negatywne lub warunkowe potwierdzenia, które mogą mieć krytyczne konsekwencje, jeśli nie zostały reprezentowane. Analiza tekstu dla kondycji obsługuje trzy kategorie wykrywania potwierdzeń dla jednostek w tekście: 

* pewności
* warunk
* skojarzenie

> [!div class="mx-imgBorder"]
> ![Kondycja — minus](../media/ta-for-health/assertions.png)

---

Aby uzyskać pełną listę obsługiwanych jednostek, zobacz [Kategorie jednostek](../named-entity-types.md?tabs=health) zwrócone przez analiza tekstu dla kondycji. Aby uzyskać informacje na temat wyników pewności, zobacz [Analiza tekstu przezroczystość](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

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

> [!NOTE]
> Zarówno asynchroniczny `/analyze` , jak i `/health` punkt końcowy są dostępne tylko w następujących regionach: zachodnie stany USA 2, Wschodnie stany USA 2, środkowe stany USA, Europa Północna i Europa Zachodnia.  Aby wykonać pomyślne żądania do tych punktów końcowych, upewnij się, że zasób został utworzony w jednym z tych regionów.

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

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/health/jobs/<jobID>`

Aby sprawdzić stan zadania, wprowadź żądanie GET do adresu URL w polu Nagłówek klucza operacji w odpowiedzi na wpis.  Następujące stany są używane w celu odzwierciedlenia stanu zadania:,,,,, `NotStarted` `running` `succeeded` `failed` `rejected` `cancelling` i `cancelled` .  

Zadanie można anulować przy użyciu `NotStarted` `running` stanu lub z WYWOŁANIEM Delete http na ten sam adres URL co żądanie Get.  Więcej informacji na temat wywołania usuwania jest dostępnych w [Analiza tekstu dla dokumentacji interfejsu API hostowanej kondycji](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Poniżej przedstawiono przykład odpowiedzi na żądanie GET.  Dane wyjściowe są dostępne do pobrania, dopóki nie przeprowadzono operacji `expirationDateTime` (24 godziny od momentu utworzenia zadania), po upływie którego dane wyjściowe zostaną usunięte.

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-03-11T05:43:37Z",
    "createdDateTime": "2021-03-11T05:42:32Z",
    "expirationDateTime": "2021-03-12T05:42:32Z",
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
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.94
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-03-01"
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
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>Dane wyjściowe potwierdzenia

Analiza tekstu dla kondycji zwraca Modyfikatory potwierdzeń, które są atrybutami, które są przypisane do koncepcji medycznych, które zapewniają dokładniejsze zrozumienie kontekstu pojęć w tekście. Te Modyfikatory są podzielone na trzy kategorie, z których każdy koncentruje się na różnych aspektach i zawierający zestaw wzajemnie wykluczających się wartości. Do każdej jednostki jest przypisana tylko jedna wartość na kategorię. Najbardziej typową wartością dla każdej kategorii jest wartość domyślna. Odpowiedź wyjściowa usługi zawiera tylko Modyfikatory potwierdzenia, które różnią się od wartości domyślnej.

**Pewność**  — zawiera informacje dotyczące obecności (obecne w porównaniu z nieobecnym) koncepcji i sposobu, w jaki tekst ma wpływ na swoją obecność (określony i możliwy).
*   **Pozytywna** [domyślnie]: koncepcja istnieje lub wystąpił.
* **Wartość ujemna**: koncepcja nie istnieje teraz lub nigdy nie wystąpił.
* **Positive_Possible**: koncepcja prawdopodobnie istnieje, ale istnieje pewna niepewność.
* **Negative_Possible**: istnienie koncepcji jest mało prawdopodobne, ale istnieje pewna niepewność.
* **Neutral_Possible**: koncepcja może lub nie istnieje bez żadnej tendencji po obu stronach.

**Warunkowa** — zawiera informacje dotyczące tego, czy istnienie koncepcji zależy od określonych warunków. 
*   **Brak** [domyślnie]: koncepcja jest faktem, a nie jest hipotetyczna i nie zależy od określonych warunków.
*   **Hipotetyczny**: koncepcja może opracowywać lub występować w przyszłości.
*   **Warunkowo**: koncepcja istnieje lub występuje tylko w określonych warunkach.

**Association** — określa, czy koncepcja jest skojarzona z podmiotem tekstu lub kogoś innego.
*   **Podmiot** [domyślnie]: koncepcja jest skojarzona z podmiotem tekstu, zazwyczaj pacjentem.
*   **Someone_Else**: koncepcja jest skojarzona z osobą, która nie jest podmiotem tekstu.


Wykrywanie potwierdzenia reprezentuje jednostki negacji jako wartość ujemną dla kategorii pewności, na przykład:

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty&quot;: &quot;negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id&quot;: &quot;C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id&quot;: &quot;0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>Dane wyjściowe wyodrębniania relacji

Analiza tekstu dla kondycji rozpoznaje relacje między różnymi koncepcjami, w tym relacjami między atrybutami i jednostkami (na przykład kierunekem struktury treści, dozowaniem leków) i między jednostkami (na przykład wykrywanie skrótów).

**JEDNOSTEK**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

> [!NOTE]
> * Relacje odwołujące się do WARUNKu mogą odwoływać się do typu jednostki diagnostyki lub typu jednostki SYMPTOM_OR_SIGN.
> * Relacje odwołujące się do leków mogą odwoływać się do typu jednostki MEDICATION_NAME lub MEDICATION_CLASS typu jednostki.
> * Relacje odwołujące się do czasu mogą odwoływać się do typu jednostki czasu lub typu jednostki daty.

Dane wyjściowe wyodrębniania relacji zawierają odwołania do identyfikatorów URI i przypisane role jednostek o typie relacji. Na przykład:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>Zobacz też

* [Przegląd analizy tekstu](../overview.md)
* [Kategorie nazwanych jednostek](../named-entity-types.md)
* [Co nowego](../whats-new.md)
