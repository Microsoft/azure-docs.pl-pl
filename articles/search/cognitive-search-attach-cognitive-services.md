---
title: Dołącz Cognitive Services do zestawu umiejętności
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak dołączać Cognitive Services całą subskrypcję do potoku wzbogacenia AI na platformie Azure Wyszukiwanie poznawcze.
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 77735166fafe9d39dff483baa89a4b31db31275d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577926"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Dołącz zasób Cognitive Services do zestawu umiejętności na platformie Azure Wyszukiwanie poznawcze

Podczas konfigurowania [potoku wzbogacania AI](cognitive-search-concept-intro.md) na platformie Azure wyszukiwanie poznawcze można wzbogacić ograniczoną liczbę dokumentów bezpłatnie. W przypadku większych i bardziej częstych obciążeń należy dołączyć rozliczenie "wszystko w jednym" Cognitive Services zasobu. Subskrypcja "All-in-one" odwołuje się do "Cognitive Services" jako oferty, a nie poszczególnych usług, z dostępem udzielonym za pomocą jednego klucza interfejsu API.

Zasób Cognitive Services "wszystko w jednym" obejmuje [wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md) , które można uwzględnić w zestawu umiejętności:

+ [Przetwarzanie obrazów](https://azure.microsoft.com/services/cognitive-services/computer-vision/) do analizy obrazów i optycznego rozpoznawania znaków (OCR)
+ [Analiza tekstu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) wykrywania języka, rozpoznawania jednostek, analizy tonacji i wyodrębniania kluczowych fraz
+ [Tłumaczenie tekstu](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

Klucz Cognitive Services "All-in-one" jest opcjonalny w definicji zestawu umiejętności. Gdy liczba codziennych transakcji jest mniejsza niż 20 dziennie, koszt jest pochłaniany. Jeśli jednak transakcje przekroczą tę liczbę, wymagany jest prawidłowy klucz zasobu, aby można było kontynuować przetwarzanie.

Wszystkie klucze zasobów "wszystko w jednym" są prawidłowe. Wewnętrznie usługa wyszukiwania będzie używać zasobu, który znajduje się w tym samym regionie fizycznym, nawet jeśli klucz "wszystko w jednym" dotyczy zasobu w innym regionie. Na stronie [Dostępność produktu](https://azure.microsoft.com/global-infrastructure/services/?products=search) obok siebie widoczna jest regionalna.

> [!NOTE]
> W przypadku pominięcia wstępnie zdefiniowanych umiejętności w zestawu umiejętności, Cognitive Services nie jest dostępny i nie będzie naliczana opłata, nawet jeśli zestawu umiejętności określa klucz.

## <a name="how-billing-works"></a>Sposób działania rozliczeń

+ Usługa Azure Wyszukiwanie poznawcze korzysta z klucza zasobu Cognitive Services podanym w zestawu umiejętności do rozliczania obrazów i wzbogacania tekstu. Do realizacji umiejętności rozliczanych jest [Cognitive Services cena płatności zgodnie z rzeczywistym](https://azure.microsoft.com/pricing/details/cognitive-services/)użyciem.

+ Wyodrębnianie obrazów to operacja Wyszukiwanie poznawcze platformy Azure, która występuje, gdy dokumenty są pęknięte przed wzbogacaniem. Wyodrębnianie obrazów jest rozliczane. Aby zapoznać się z cennikiem wyodrębniania obrazów, zobacz [stronę z cennikiem usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/).

+ Wyodrębnianie tekstu występuje również podczas frazy do łamania dokumentu. Nie jest rozliczany.

+ Nie są naliczane opłaty za umiejętności, które nie wywołują Cognitive Services, w tym warunkowego, kształtowania, scalania tekstu ani umiejętności dzielenia tekstu.

## <a name="same-region-requirement"></a>Wymóg tego samego regionu

Oba Wyszukiwanie poznawcze i Cognitive Services muszą istnieć w tym samym regionie fizycznym, co zostało wskazane na stronie [Dostępność produktu](https://azure.microsoft.com/global-infrastructure/services/?products=search) . Większość regionów oferujących Wyszukiwanie poznawcze również Cognitive Services.

W przypadku próby wzbogacenia systemu AI w regionie, w którym nie ma obu tych usług, zobaczysz następujący komunikat: "podany klucz nie jest prawidłowym kluczem typu CognitiveServices dla regionu usługi wyszukiwania".

> [!NOTE]
> Niektóre wbudowane umiejętności są oparte na Cognitive Services nieregionalnych (na przykład [umiejętność tłumaczenia tekstu](cognitive-search-skill-text-translation.md)). Użycie umiejętności nieregionalnej oznacza, że Twoje żądanie może być serwisowane w regionie innym niż region Wyszukiwanie poznawcze platformy Azure. Aby uzyskać więcej informacji na temat usług nieregionalnych, zobacz stronę [Cognitive Services produkt według regionów](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Korzystanie z bezpłatnych zasobów

Możesz użyć ograniczonej, bezpłatnej opcji przetwarzania, aby ukończyć samouczek wzbogacania AI i ćwiczenia szybkiego startu.

Bezpłatny (ograniczone wzbogacanie) zasoby są ograniczone do 20 dokumentów dziennie, na indeksator. Możesz [zresetować indeksator,](search-howto-run-reset-indexers.md) aby zresetować licznik.

W przypadku używania Kreatora **importu danych** do wzbogacania AI można znaleźć opcje "Dołącz Cognitive Services" na stronie **Dodaj wzbogacenie AI (opcjonalnie)** .

![Rozwinięta sekcja Cognitive Services Attach](./media/cognitive-search-attach-cognitive-services/attach1.png "Rozwinięta sekcja Cognitive Services Attach")

## <a name="use-billable-resources"></a>Korzystanie z zasobów rozliczanych

W przypadku obciążeń, które tworzą więcej niż 20 wzbogacań dziennie, pamiętaj, aby dołączyć do rozliczanego zasobu Cognitive Services. Zalecamy, aby zawsze dołączać Cognitive Services do rozliczeń, nawet jeśli nie ma potrzeby wywoływania interfejsy API usług Cognitive Services. Dołączanie zasobu przesłania dzienny limit.

Opłata jest naliczana tylko za umiejętności, które wywołują interfejsy API usług Cognitive Services. Nie są naliczane opłaty za [niestandardowe umiejętności](cognitive-search-create-custom-skill-example.md)ani umiejętności takie jak [łączenie tekstu](cognitive-search-skill-textmerger.md), [rozdzielacz tekstu](cognitive-search-skill-textsplit.md)i [kształtowanie](cognitive-search-skill-shaper.md), które nie są oparte na interfejsie API.

W przypadku korzystania z kreatora **importu danych** można skonfigurować zasób rozliczany na stronie **Dodaj wzbogacanie AI (opcjonalnie)** .

1. Rozwiń węzeł **dołącz Cognitive Services** a następnie wybierz pozycję **utwórz nowy zasób Cognitive Services**. Zostanie otwarta nowa karta, aby można było utworzyć zasób:

   ![Tworzenie zasobu usług Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Tworzenie zasobu usług Cognitive Services")

1. Na liście **Lokalizacja** wybierz ten sam region, w którym znajduje się usługa wyszukiwania.

1. Na liście **warstwa cenowa** wybierz pozycję **S0** , aby uzyskać kolekcję "All-in-in-one" funkcji Cognitive Services, w tym funkcje wizji i języka, które z powrotem zapewniały wbudowane umiejętności udostępniane przez usługę Azure wyszukiwanie poznawcze.

   W przypadku warstwy S0 można znaleźć stawki dla konkretnych obciążeń na [stronie cennika Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Na liście **Wybierz ofertę** upewnij się, że **Cognitive Services** jest zaznaczone.
   + W obszarze Funkcje **językowe** stawki dla **Analiza tekstu Standard** dotyczą indeksowania AI.
   + W obszarze Funkcje **wizji** obowiązują stawki za **Przetwarzanie obrazów S1** .

1. Wybierz pozycję **Utwórz** , aby udostępnić nowy zasób Cognitive Services.

1. Wróć do poprzedniej karty. Wybierz pozycję **Odśwież** , aby wyświetlić zasób Cognitive Services, a następnie wybierz zasób:

   ![Wybierz zasób Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Wybierz zasób Cognitive Services")

1. Rozwiń sekcję **Dodawanie umiejętności poznawczych** , aby wybrać konkretne umiejętności poznawcze, które mają być uruchamiane na danych. Ukończ resztę kreatora.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Dołącz istniejący zestawu umiejętności do zasobu Cognitive Services

Jeśli masz istniejący zestawu umiejętności, możesz dołączyć go do nowego lub innego zasobu Cognitive Services.

1. Na stronie Przegląd usługi wyszukiwania wybierz pozycję **umiejętności**:

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