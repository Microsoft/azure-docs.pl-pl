---
title: Definicja interfejsu dla umiejętności niestandardowych
titleSuffix: Azure Cognitive Search
description: Niestandardowy interfejs wyodrębniania danych dla niestandardowej umiejętności interfejsu API sieci Web w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e78f0d1e8d6d637dfebe1ff475ab8416ba49a263
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935419"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Jak dodać niestandardową umiejętność do potoku wzbogacenia Wyszukiwanie poznawcze platformy Azure

> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=172&end=221]

[Potok wzbogacania](cognitive-search-concept-intro.md) w usłudze Azure wyszukiwanie poznawcze może być montowany z [wbudowanych umiejętności poznawczych](cognitive-search-predefined-skills.md) , a także [niestandardowych umiejętności](cognitive-search-custom-skill-web-api.md) , które tworzysz i dodajesz do potoku. W tym artykule dowiesz się, jak utworzyć niestandardową umiejętność, która uwidacznia interfejs umożliwiający dołączenie go do potoku wzbogacania AI. 

Tworzenie niestandardowych umiejętności umożliwia wstawianie przekształceń unikatowych dla zawartości. Niestandardowa umiejętnie wykonuje się niezależnie od zastosowania dowolnych wymaganych czynności wzbogacania. Można na przykład zdefiniować jednostki niestandardowe specyficzne dla pola, utworzyć niestandardowe modele klasyfikacji, aby rozróżnić kontrakty biznesowe i finansowe oraz dokumenty, lub dodać umiejętność rozpoznawania mowy, aby lepiej uzyskać dostęp do plików audio w celu uzyskania odpowiedniej zawartości. Aby zapoznać się z przykładem krok po kroku, zobacz [przykład: Tworzenie niestandardowej umiejętności dla wzbogacania AI](cognitive-search-create-custom-skill-example.md).

 Niezależnie od tego, jakie możliwości niestandardowe są wymagane, istnieje prosty i przejrzysty interfejs do łączenia niestandardowych umiejętności z resztą potoku wzbogacania. Jedynym wymaganiem do włączenia w [zestawu umiejętności](cognitive-search-defining-skillset.md) jest możliwość przyjmowania danych wejściowych i emisji w sposób, który jest zużywany w ramach zestawu umiejętności jako całości. Fokus tego artykułu znajduje się w formatach wejściowych i wyjściowych wymaganych przez potok wzbogacania.

## <a name="web-api-custom-skill-interface"></a>Niestandardowy interfejs niestandardowych interfejsów API sieci Web

Niestandardowe punkty końcowe umiejętności WebAPI według domyślnego limitu czasu, jeśli nie zwracają odpowiedzi w ramach 30-sekundowego okna. Potok indeksowania jest synchroniczny, a indeksowanie spowoduje wygenerowanie błędu limitu czasu, jeśli w tym oknie nie zostanie odebrana odpowiedź.  Limit czasu można skonfigurować do 230 sekund, ustawiając parametr timeout:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Upewnij się, że identyfikator URI jest bezpieczny (HTTPS).

Obecnie jedynym mechanizmem do współdziałania z niestandardowymi umiejętnościami jest interfejs API sieci Web. Potrzeby interfejsu API sieci Web muszą spełniać wymagania opisane w tej sekcji.

### <a name="1--web-api-input-format"></a>1. format danych wejściowych interfejsu API sieci Web


> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=294&end=340]


Internetowy interfejs API musi akceptować tablicę rekordów do przetworzenia. Każdy rekord musi zawierać "zbiór właściwości", który jest danymi wejściowymi do internetowego interfejsu API. 

Załóżmy, że chcesz utworzyć prosty wzbogacający, który identyfikuje pierwszy dzień wymieniony w tekście kontraktu. W tym przykładzie umiejętność akceptuje pojedyncze dane wejściowe *contractText* jako tekst kontraktu. Kwalifikacja ma także pojedyncze dane wyjściowe, czyli datę kontraktu. Aby ułatwić wzbogacanie, zwróć ten *contractDate* do kształtu złożonego typu wieloczęściowego.

Internetowy interfejs API powinien być gotowy do otrzymywania partii rekordów wejściowych. Każdy element członkowski tablicy *wartości* reprezentuje dane wejściowe dla określonego rekordu. Każdy rekord musi mieć następujące elementy:

+ Element członkowski *recordId* , który jest unikatowym identyfikatorem dla określonego rekordu. Gdy Program wzbogacający zwraca wyniki, musi podać ten identyfikator *recordId* , aby umożliwić wywołującemu dopasowanie wyników rekordu do ich danych wejściowych.

+ Element członkowski *danych* , który zasadniczo jest zbiorem pól wejściowych dla każdego rekordu.

Aby być bardziej konkretny, na powyższym przykładzie internetowy interfejs API powinien oczekiwać żądań, które wyglądają następująco:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
W rzeczywistości usługa może zostać wywołana z setkami lub tysiącami rekordów zamiast tylko trzech przedstawionych tutaj.

### <a name="2-web-api-output-format"></a>2. format danych wyjściowych interfejsu API sieci Web

Format danych wyjściowych to zestaw rekordów zawierających identyfikator *recordId*i zbiór właściwości 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

W tym konkretnym przykładzie istnieje tylko jedno wyjście, ale można wyprowadzić więcej niż jedną właściwość. 

### <a name="errors-and-warning"></a>Błędy i ostrzeżenia

Jak pokazano w poprzednim przykładzie, można zwrócić komunikaty o błędach i ostrzeżeniach dla każdego rekordu.

## <a name="consuming-custom-skills-from-skillset"></a>Zużywanie umiejętności niestandardowych z zestawu umiejętności

Podczas tworzenia programu wzbogacania interfejsu API sieci Web można opisać nagłówki i parametry HTTP w ramach żądania. Poniższy fragment kodu przedstawia sposób, w jaki parametry żądania i *opcjonalne* nagłówki HTTP mogą być opisane jako część definicji zestawu umiejętności. Nagłówki HTTP nie są wymagane, ale umożliwiają dodawanie dodatkowych możliwości konfiguracji do umiejętności i Ustawianie ich z definicji zestawu umiejętności.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono wymagania dotyczące interfejsu niezbędne do integracji niestandardowej umiejętności z zestawu umiejętności. Kliknij poniższe linki, aby dowiedzieć się więcej na temat umiejętności niestandardowych i kompozycji zestawu umiejętności.

+ [Obejrzyj nasze wideo dotyczące umiejętności niestandardowych](https://youtu.be/fHLCE-NZeb4)
+ [Umiejętności dotyczące oszczędzania mocy: repozytorium umiejętności niestandardowych](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Przykład: Tworzenie niestandardowej umiejętności dla wzbogacania AI](cognitive-search-create-custom-skill-example.md)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Utwórz zestawu umiejętności (REST)](/rest/api/searchservice/create-skillset)
+ [Jak zmapować wzbogacone pola](cognitive-search-output-field-mapping.md)