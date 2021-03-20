---
title: Tworzenie zestawu umiejętności
titleSuffix: Azure Cognitive Search
description: Zdefiniuj wyodrębnianie danych, przetwarzanie języka naturalnego lub procedurę analizy obrazów, aby wzbogacać i wyodrębnić informacje o strukturze z danych do użycia w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 39a7c92ca6c83684658cf767722698806ed994ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935453"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Jak utworzyć zestawu umiejętności w potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze 

![etapy indeksatora](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "etapy indeksatora")

Zestawu umiejętności definiuje operacje wyodrębniania i wzbogacania danych w celu przeszukiwania. Zestawu umiejętności wykonuje się po wyodrębnieniu zawartości tekstu i obrazu z dokumentów źródłowych, a po dowolnych polach z dokumentu źródłowego są one (opcjonalnie) zmapowane do pól docelowych w indeksie lub magazynie wiedzy.

Zestawu umiejętności zawiera jedną lub więcej *umiejętności poznawczych* , które reprezentują konkretne operacje wzbogacania, takie jak tłumaczenie tekstu, wyodrębnianie kluczowych fraz lub wykonywanie optycznego rozpoznawania znaków z pliku obrazu. Aby utworzyć zestawu umiejętności, możesz skorzystać z [wbudowanych umiejętności](cognitive-search-predefined-skills.md) firmy Microsoft lub niestandardowych umiejętności, które zawierają modele lub logikę przetwarzania, które zapewniasz (zobacz [przykład: Tworzenie niestandardowej umiejętności w potoku wzbogacania AI](cognitive-search-create-custom-skill-example.md) , aby uzyskać więcej informacji).

W tym artykule dowiesz się, jak utworzyć potok wzbogacania dla umiejętności, których chcesz użyć. Zestawu umiejętności jest dołączany do [indeksatora](search-indexer-overview.md)wyszukiwanie poznawcze platformy Azure. Jedną z części projektu potoku, omówionego w tym artykule, jest konstrukcja zestawu umiejętności. 

> [!NOTE]
> Inna część projektu potoku określa indeksator, pokryty w [następnym kroku](#next-step). Definicja indeksatora zawiera odwołanie do zestawu umiejętności oraz mapowania pól, które są używane do łączenia danych wejściowych z docelowym indeksem.

Najważniejsze kwestie do zapamiętania:

+ Można mieć tylko jeden zestawu umiejętności na indeksator.
+ Zestawu umiejętności musi mieć co najmniej jedną umiejętność.
+ Można utworzyć wiele umiejętności tego samego typu (na przykład wariantów umiejętności analizy obrazów).

## <a name="begin-with-the-end-in-mind"></a>Zacznij od zakończenia

Zalecany początkowy krok polega na tym, które dane mają zostać wyodrębnione z danych pierwotnych, oraz w jaki sposób używać tych danych w rozwiązaniu wyszukiwania. Utworzenie ilustracji całego potoku wzbogacania może pomóc w zidentyfikowaniu niezbędnych kroków.

Załóżmy, że interesuje Cię przetwarzanie zestawu komentarzy analityków finansowych. Dla każdego pliku, chcesz wyodrębnić nazwy firmowe i ogólne tonacji komentarzy. Warto również napisać niestandardowy wzbogacający, który używa usługi wyszukiwanie jednostek Bing, aby znaleźć dodatkowe informacje o firmie, takie jak rodzaj firmy, w której jest zaangażowana firma. Zasadniczo, chcesz wyodrębnić informacje takie jak następujące, które są indeksowane dla każdego dokumentu:

| rekord — tekst | towarzystw | tonacji | opisy firmy |
|--------|-----|-----|-----|
|Przykładowy rekord| ["Microsoft", "LinkedIn"] | 0,99 | ["Microsoft Corporation to amerykańska firma wielonarodowych technologii...", "LinkedIn to sieć społecznościowa zorientowana na działalność biznesową i"... "]

Poniższy diagram ilustruje hipotetyczny potok wzbogacania:

![Hipotetyczny potok wzbogacania](media/cognitive-search-defining-skillset/sample-skillset.png "Hipotetyczny potok wzbogacania")


Po uzyskaniu odpowiedniego pomysłu w potoku można wyrazić zestawu umiejętności, który zawiera te kroki. Funkcja zestawu umiejętności jest wyrażana w przypadku przekazywania definicji indeksatora do usługi Azure Wyszukiwanie poznawcze. Aby dowiedzieć się więcej na temat przekazywania indeksatora, zapoznaj się z [dokumentacją indeksatora](/rest/api/searchservice/create-indexer).


Na diagramie krok *łamania dokumentu* odbywa się automatycznie. Zasadniczo usługa Azure Wyszukiwanie poznawcze wie, jak otworzyć dobrze znane pliki i utworzyć pole *zawartości* zawierające tekst wyodrębniony z każdego dokumentu. Białe pola są wbudowanymi wzbogacami, a kropkowane pole "wyszukiwanie jednostek Bing" reprezentuje obiekt wzbogacany, który tworzysz. Tak jak pokazano, zestawu umiejętności zawiera trzy umiejętności.

## <a name="skillset-definition-in-rest"></a>Definicja zestawu umiejętności w REST

Zestawu umiejętności jest definiowana jako tablica umiejętności. Każda umiejętność definiuje źródło danych wejściowych i nazwę wygenerowanego wyjścia. Za pomocą [interfejsu API REST Create zestawu umiejętności](/rest/api/searchservice/create-skillset)można zdefiniować zestawu umiejętności, który odpowiada poprzedniemu diagramowi: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Tworzenie zestawu umiejętności

Podczas tworzenia zestawu umiejętności można podać opis, aby zestawu umiejętności samodzielny dokument. Opis jest opcjonalny, ale przydatny do śledzenia zawartości zestawu umiejętności. Ponieważ zestawu umiejętności jest dokumentem JSON, który nie zezwala na komentarze, musisz użyć `description` elementu.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

Następny element w zestawu umiejętności jest tablicą umiejętności. Każdą umiejętność można traktować jako pierwotną wersję wzbogacania. Każda umiejętność wykonuje niewielkie zadanie w tym potoku wzbogacania. Każdy z nich przyjmuje dane wejściowe (lub zestaw danych wejściowych) i zwraca niektóre wyniki. W następnych sekcjach zawarto informacje na temat sposobu określania umiejętności wbudowanych i niestandardowych, a także do łączenia się z danymi wejściowymi i wyjściowymi. Dane wejściowe mogą pochodzić z danych źródłowych lub z innej umiejętności. Wyniki można mapować do pola w indeksie wyszukiwania lub użyć jako danych wejściowych w celu uzyskania kwalifikacji podrzędnych.

## <a name="add-built-in-skills"></a>Dodawanie wbudowanych umiejętności

Przyjrzyjmy się pierwszej umiejętności, która stanowi wbudowaną [umiejętność rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Każda wbudowana umiejętność ma `odata.type` `input` właściwości, i `output` . Właściwości specyficzne dla umiejętności zawierają dodatkowe informacje dotyczące tej umiejętności. W przypadku rozpoznawania jednostek `categories` jest jedną jednostką z ustalonego zestawu typów jednostek, które może rozpoznać przedmieszczony model.

* Każda umiejętność powinna mieć ```"context"``` . Kontekst reprezentuje poziom, na którym operacje mają miejsce. W powyższej umiejętności kontekst jest całym dokumentem, co oznacza, że umiejętność rozpoznawania jednostki jest wywoływana raz dla dokumentu. Dane wyjściowe są również tworzone na tym poziomie. Dokładniej, ```"organizations"``` są generowane jako element członkowski ```"/document"``` . W obszarze umiejętności podrzędne można odwołać się do nowo utworzonych informacji jako ```"/document/organizations"``` .  Jeśli ```"context"``` pole nie jest jawnie ustawione, domyślnym kontekstem jest dokument.

* Umiejętność ma jedno wejście o nazwie "text" ze źródłowym zestawem wejściowym na ```"/document/content"``` . Umiejętność (rozpoznawanie jednostek) działa w polu *zawartość* każdego dokumentu, który jest standardowym polem utworzonym przez indeksator usługi Azure Blob. 

* Umiejętność ma jedno wyjście ```"organizations"``` . Dane wyjściowe istnieją tylko podczas przetwarzania. Aby połączyć dane wyjściowe z danymi wejściowymi w celu uzyskania kwalifikacji podrzędnych, należy odwołać się do danych wyjściowych jako ```"/document/organizations"``` .

* W przypadku określonego dokumentu wartość ```"/document/organizations"``` jest tablicą organizacji wyodrębnionych z tekstu. Na przykład:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Niektóre sytuacje odwołują się do każdego elementu tablicy osobno. Załóżmy na przykład, że chcesz przekazać każdy element ```"/document/organizations"``` oddzielnie do innej umiejętności (na przykład niestandardowego programu do wyszukiwania jednostek Bing). Można odwołać się do każdego elementu tablicy, dodając gwiazdkę do ścieżki: ```"/document/organizations/*"``` 

Druga umiejętność wyodrębniania tonacji jest zgodna z tym samym wzorcem, co pierwszy wzbogacający. Przyjmuje ```"/document/content"``` jako dane wejściowe i zwraca ocenę tonacji dla każdego wystąpienia zawartości. Ponieważ nie ustawiono ```"context"``` jawnie pola, dane wyjściowe (mySentiment) są teraz elementem podrzędnym ```"/document"``` .

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Dodaj niestandardową umiejętność

Odwołaj strukturę niestandardowego elementu wzbogacania wyszukiwania jednostek Bing:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Ta definicja to [niestandardowa umiejętność](cognitive-search-custom-skill-web-api.md) wywołująca internetowy interfejs API w ramach procesu wzbogacania. W przypadku każdej organizacji identyfikowanej przez funkcję rozpoznawania jednostek ta umiejętność wywołuje internetowy interfejs API, aby znaleźć opis tej organizacji. Aranżacja, kiedy należy wywołać interfejs API sieci Web i jak przepływać otrzymane informacje, jest obsługiwana wewnętrznie przez aparat wzbogacania. Jednak Inicjalizacja niezbędna do wywołania tego niestandardowego interfejsu API musi być podana w formacie JSON (na przykład identyfikator URI, httpHeaders i oczekiwane dane wejściowe). Aby uzyskać wskazówki dotyczące tworzenia niestandardowego interfejsu API sieci Web dla potoku wzbogacania, zobacz [How to define a Custom Interface](cognitive-search-custom-skill-interface.md).

Zwróć uwagę, że pole "context" jest ustawione na wartość ```"/document/organizations/*"``` przy użyciu gwiazdki, co oznacza, że krok wzbogacania jest wywoływany *dla każdej* organizacji ```"/document/organizations"``` . 

Dane wyjściowe — w tym przypadku opis firmy jest generowany dla każdej identyfikowanej organizacji. W przypadku odwoływania się do opisu w kroku podrzędnym (na przykład w przypadku wyodrębniania kluczowych fraz) należy użyć ścieżki ```"/document/organizations/*/description"``` do tego celu. 

## <a name="add-structure"></a>Dodaj strukturę

Zestawu umiejętności generuje strukturalne informacje z danych bez struktury. Rozpatrzmy następujący przykład:

*"W czwartym kwartale firma Microsoft zarejestrował $1 100 000 000 w przychodach z serwisu LinkedIn, firma sieci społecznościowej ją zakupiła w ubiegłym roku. Pozyskiwanie umożliwia firmie Microsoft łączenie możliwości serwisu LinkedIn z funkcjami CRM i Office. Akcjonariusze są przyjemnością z postępem do tej pory ".*

Prawdopodobnie wynik będzie wygenerował strukturę podobną do poniższej ilustracji:

![Przykładowa struktura wyjściowa](media/cognitive-search-defining-skillset/enriched-doc.png "Przykładowa struktura wyjściowa")

Do tej pory Ta struktura była tylko wewnętrzna, tylko pamięć i używana tylko w indeksach Wyszukiwanie poznawcze platformy Azure. Dodanie sklepu z bazami danych umożliwia zapisanie wzbogacania w celu użycia poza wyszukiwaniem.

## <a name="add-a-knowledge-store"></a>Dodawanie sklepu merytorycznego

[Magazyn wiedzy](knowledge-store-concept-intro.md) to funkcja platformy Azure wyszukiwanie poznawcze do zapisywania wzbogaconego dokumentu. Magazyn wiedzy tworzony przez użytkownika w ramach konta usługi Azure Storage jest repozytorium, w którym są używane wzbogacone dane. 

Definicja sklepu merytorycznego jest dodawana do zestawu umiejętności. Przewodnik po całym procesie można znaleźć [w temacie Tworzenie sklepu z bazami danych w usłudze REST](knowledge-store-create-rest.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Można zapisać wzbogacone dokumenty jako tabele z niehierarchicznymi relacjami zachowywane lub jako dokumenty JSON w usłudze BLOB Storage. Dane wyjściowe z dowolnego umiejętności w zestawu umiejętności mogą być źródłem jako dane wejściowe dla projekcji. Jeśli szukasz danych w określonym kształcie, zaktualizowana [umiejętność kształtu](cognitive-search-skill-shaper.md) może teraz modelować typy złożone do użycia. 

<a name="next-step"></a>

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już potok wzbogacania i umiejętności, Kontynuuj, [jak odwoływać się do adnotacji w zestawu umiejętności](cognitive-search-concept-annotations-syntax.md) lub [Jak mapować dane wyjściowe do pól w indeksie](cognitive-search-output-field-mapping.md).