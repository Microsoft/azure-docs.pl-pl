---
title: Dołącz Cognitive Services do zestawu umiejętności
titleSuffix: Azure Cognitive Search
description: Instrukcje dotyczące dołączania Cognitive Services całościowej subskrypcji do potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: c9f6a5ebc4f3242181196bd40b62f7522d025b84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924981"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Dołącz zasób Cognitive Services do zestawu umiejętności na platformie Azure Wyszukiwanie poznawcze 

Podczas konfigurowania potoku wzbogacania w usłudze Azure Wyszukiwanie poznawcze można wzbogacić ograniczoną liczbę dokumentów bezpłatnie. W przypadku większych i bardziej częstych obciążeń należy dołączyć zasób Cognitive Services rozliczany.

W tym artykule dowiesz się, jak dołączyć zasób, przypisując klucz do zestawu umiejętności, który definiuje potok wzbogacania.

## <a name="resources-used-during-enrichment"></a>Zasoby używane podczas wzbogacania

Usługa Azure Wyszukiwanie poznawcze ma zależność od Cognitive Services, w tym [Przetwarzanie obrazów](https://azure.microsoft.com/services/cognitive-services/computer-vision/) do analizy obrazów i optycznego rozpoznawania znaków (OCR), [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) do przetwarzania w języku naturalnym oraz inne wzbogacania, takie jak [tłumaczenie tekstu](https://azure.microsoft.com/services/cognitive-services/translator-text-api/). W kontekście wzbogacania na platformie Azure Wyszukiwanie poznawcze te algorytmy AI są opakowane w ramach *umiejętności*, umieszczane w *zestawu umiejętności*i odwołujące się do *indeksatora* podczas indeksowania.

## <a name="how-billing-works"></a>Sposób działania rozliczeń

+ Usługa Azure Wyszukiwanie poznawcze korzysta z klucza zasobu Cognitive Services podanym w zestawu umiejętności do rozliczania obrazów i wzbogacania tekstu. Do realizacji umiejętności rozliczanych jest [Cognitive Services cena płatności zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem.

+ Wyodrębnianie obrazów to operacja Wyszukiwanie poznawcze platformy Azure, która występuje, gdy dokumenty są pęknięte przed wzbogacaniem. Wyodrębnianie obrazów jest rozliczane. Aby zapoznać się z cennikiem wyodrębniania obrazów, zobacz [stronę z cennikiem usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).

+ Wyodrębnianie tekstu występuje również podczas frazy do łamania dokumentu. Nie jest rozliczany.

+ Nie są naliczane opłaty za umiejętności, które nie wywołują Cognitive Services, w tym warunkowego, kształtowania, scalania tekstu ani umiejętności dzielenia tekstu.

## <a name="same-region-requirement"></a>Wymóg tego samego regionu

Firma Microsoft wymaga, aby usługa Azure Wyszukiwanie poznawcze i usługa Azure Cognitive Services istniały w tym samym regionie. W przeciwnym razie zostanie wyświetlony komunikat w czasie wykonywania: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Nie ma możliwości przenoszenia usługi między regionami. Jeśli zostanie wyświetlony ten błąd, należy utworzyć nowy zasób Cognitive Services w tym samym regionie co usługa Azure Wyszukiwanie poznawcze.

> [!NOTE]
> Niektóre wbudowane umiejętności są oparte na Cognitive Services nieregionalnych (na przykład [umiejętność tłumaczenia tekstu](cognitive-search-skill-text-translation.md)). Użycie umiejętności nieregionalnej oznacza, że Twoje żądanie może być serwisowane w regionie innym niż region Wyszukiwanie poznawcze platformy Azure. Aby uzyskać więcej informacji na temat usług nieregionalnych, zobacz stronę [Cognitive Services produkt według regionów](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Korzystanie z bezpłatnych zasobów

Możesz użyć ograniczonej, bezpłatnej opcji przetwarzania, aby ukończyć samouczek wzbogacania AI i ćwiczenia szybkiego startu.

Bezpłatny (ograniczone wzbogacanie) zasoby są ograniczone do 20 dokumentów dziennie, na indeksator. Aby zresetować licznik, można usunąć i utworzyć ponownie indeksator.

1. Otwórz Kreatora importowania danych:

   ![Otwórz Kreatora importowania danych](media/search-get-started-portal/import-data-cmd.png "Otwórz Kreatora importowania danych")

1. Wybierz źródło danych i Kontynuuj **Dodawanie wzbogacenia AI (opcjonalne)**. Aby zapoznać się z przewodnikiem krok po kroku tego kreatora, zobacz [Tworzenie indeksu w Azure Portal](search-get-started-portal.md).

1. Rozwiń węzeł **dołącz Cognitive Services** a następnie wybierz pozycję **wolne (ograniczone wzbogacania)**:

   ![Rozwinięta sekcja Cognitive Services Attach](./media/cognitive-search-attach-cognitive-services/attach1.png "Rozwinięta sekcja Cognitive Services Attach")

1. Teraz możesz przejść do następnych kroków, takich jak **Dodawanie umiejętności poznawczych**.

## <a name="use-billable-resources"></a>Korzystanie z zasobów rozliczanych

W przypadku obciążeń, które tworzą więcej niż 20 wzbogacań dziennie, pamiętaj, aby dołączyć do rozliczanego zasobu Cognitive Services. Zalecamy, aby zawsze dołączać Cognitive Services do rozliczeń, nawet jeśli nie ma potrzeby wywoływania interfejsy API usług Cognitive Services. Dołączanie zasobu przesłania dzienny limit.

Opłata jest naliczana tylko za umiejętności, które wywołują interfejsy API usług Cognitive Services. Nie są naliczane opłaty za [niestandardowe umiejętności](cognitive-search-create-custom-skill-example.md)ani umiejętności takie jak [łączenie tekstu](cognitive-search-skill-textmerger.md), [rozdzielacz tekstu](cognitive-search-skill-textsplit.md)i [kształtowanie](cognitive-search-skill-shaper.md), które nie są oparte na interfejsie API.

1. Otwórz Kreatora importowania danych, wybierz źródło danych i Kontynuuj **Dodawanie wzbogacenia AI (opcjonalnie)**.

1. Rozwiń węzeł **dołącz Cognitive Services** a następnie wybierz pozycję **utwórz nowy zasób Cognitive Services**. Zostanie otwarta nowa karta, aby można było utworzyć zasób:

   ![Tworzenie zasobu usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Tworzenie zasobu usług Cognitive Services")

1. Z listy **Lokalizacja** wybierz region, w którym znajduje się usługa Azure wyszukiwanie poznawcze. Upewnij się, że ten region jest używany ze względu na wydajność. Użycie tego regionu również unieważnia opłaty za przepustowość wychodzącą w różnych regionach.

1. Na liście **warstwa cenowa** wybierz pozycję **S0** , aby uzyskać kolekcję "All-in-in-one" funkcji Cognitive Services, w tym funkcje wizji i języka, które z powrotem zapewniały wbudowane umiejętności udostępniane przez usługę Azure wyszukiwanie poznawcze.

   W przypadku warstwy S0 można znaleźć stawki dla konkretnych obciążeń na [stronie cennika Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Na liście **Wybierz ofertę** upewnij się, że **Cognitive Services** jest zaznaczone.
   + W obszarze Funkcje **językowe** stawki dla **Analiza tekstu Standard** dotyczą indeksowania AI.
   + W obszarze Funkcje **wizji** obowiązują stawki za **Przetwarzanie obrazów S1** .

1. Wybierz pozycję **Utwórz** , aby udostępnić nowy zasób Cognitive Services.

1. Wróć do poprzedniej karty, która zawiera Kreatora importu danych. Wybierz pozycję **Odśwież** , aby wyświetlić zasób Cognitive Services, a następnie wybierz zasób:

   ![Wybierz zasób Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Wybierz zasób Cognitive Services")

1. Rozwiń sekcję **Dodawanie umiejętności poznawczych** , aby wybrać konkretne umiejętności poznawcze, które mają być uruchamiane na danych. Ukończ resztę kreatora.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Dołącz istniejący zestawu umiejętności do zasobu Cognitive Services

Jeśli masz istniejący zestawu umiejętności, możesz dołączyć go do nowego lub innego zasobu Cognitive Services.

1. Na stronie **Przegląd usługi** wybierz pozycję **umiejętności**:

   ![Karta umiejętności](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Karta umiejętności")

1. Wybierz nazwę zestawu umiejętności, a następnie wybierz istniejący zasób lub Utwórz nowy. Wybierz **przycisk OK** , aby potwierdzić zmiany.

   ![Lista zasobów zestawu umiejętności](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Lista zasobów zestawu umiejętności")

   Należy pamiętać, że opcja **bezpłatne (ograniczone wzbogacanie)** pozwala na codzienne 20 dokumentów i umożliwia **utworzenie Cognitive Services nowego** zasobu do rozliczania. W przypadku utworzenia nowego zasobu wybierz pozycję **Odśwież** , aby odświeżyć listę zasobów Cognitive Services, a następnie wybierz zasób.

## <a name="attach-cognitive-services-programmatically"></a>Dołącz Cognitive Services programowo

Gdy tworzysz zestawu umiejętności programowo, Dodaj `cognitiveServices` sekcję do zestawu umiejętności. W tej sekcji należy uwzględnić klucz zasobu Cognitive Services, który ma zostać skojarzony z zestawu umiejętności. Należy pamiętać, że zasób musi znajdować się w tym samym regionie co zasób Wyszukiwanie poznawcze platformy Azure. Należy również uwzględnić `@odata.type` i ustawić wartość `#Microsoft.Azure.Search.CognitiveServicesByKey` .

Poniższy przykład pokazuje ten wzorzec. Zwróć uwagę na `cognitiveServices` sekcję na końcu definicji.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Przykład: oszacowanie kosztów

Aby oszacować koszty związane z indeksowaniem wyszukiwania poznawczego, Zacznij od pomysłu dotyczącego wyglądu średniej dokumentu, aby można było uruchamiać pewne liczby. Na przykład może to być przybliżone:

+ 1 000 plików PDF.
+ Każda z sześciu stron.
+ Jeden obraz na stronę (obrazy 6 000).
+ 3 000 znaków na stronę.

Założenie potoku, który składa się z łamania dokumentu dla każdego pliku PDF, wyodrębniania obrazów i tekstu, optycznego rozpoznawania znaków (OCR) obrazów oraz rozpoznawania jednostek w organizacji.

Ceny przedstawione w tym artykule są hipotetyczne. Są one używane do zilustrowania procesu szacowania. Twoje koszty mogą być niższe. W przypadku rzeczywistych cen transakcji zobacz temat [Cognitive Services Cennik](https://azure.microsoft.com/pricing/details/cognitive-services).

1. W przypadku dokumentu z zawartością tekstową i obrazem Wyodrębnianie tekstu jest obecnie bezpłatne. W przypadku obrazów 6 000 przyjmuje się, że $1 dla 1 000 każdego obrazu, który został wyodrębniony. Jest to koszt $6,00 dla tego kroku.

2. W przypadku OCR obrazów 6 000 w języku angielskim, umiejętność optycznego rozpoznawania znaków używa najlepszego algorytmu (DescribeText). Przy założeniu, że koszt $2,50 na 1 000 obrazów do przeanalizowania, należy uregulować $15,00 w tym kroku.

3. W przypadku wyodrębniania jednostek masz łączną liczbę trzech rekordów tekstowych na stronę. Każdy rekord ma 1 000 znaków. Trzy rekordy tekstowe na stronę pomnożone przez 6 000 stron równą 18 000 rekordów tekstowych. Przy założeniu, że $2,00 na 1 000 rekordów tekstowych, ten krok będzie kosztować $36,00.

Wszystkie te informacje są umieszczane w artykule o $57,00 do pobierania 1 000 dokumentów PDF tego typu przy użyciu opisanego zestawu umiejętności.

## <a name="next-steps"></a>Następne kroki
+ [Strona cennika usługi Azure Wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/)
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Utwórz zestawu umiejętności (REST)](/rest/api/searchservice/create-skillset)
+ [Jak zmapować wzbogacone pola](cognitive-search-output-field-mapping.md)