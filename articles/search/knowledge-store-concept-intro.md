---
title: Pojęcia dotyczące sklepu wiedzy
titleSuffix: Azure Cognitive Search
description: Wysyłaj wzbogacone dokumenty do usługi Azure Storage, które umożliwiają wyświetlanie, przekształcanie i używanie wzbogaconych dokumentów na platformie Azure Wyszukiwanie poznawcze i w innych aplikacjach.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3ec556c6198a00f217568f6591bd4b43c7fc743e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924303"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Magazyn wiedzy na platformie Azure Wyszukiwanie poznawcze

Sklep z bazami informacji to funkcja platformy Azure Wyszukiwanie poznawcze, która utrzymuje dane wyjściowe [potoku wzbogacania AI](cognitive-search-concept-intro.md) na potrzeby niezależnej analizy lub przetwarzania podrzędnego. *Wzbogacony dokument* to dane wyjściowe potoku, utworzone na podstawie zawartości wyodrębnionej, strukturalnej i analizowanej przy użyciu procesów AI. W standardowym potoku AI, wzbogacone dokumenty są nietrwałe, używane tylko podczas indeksowania, a następnie odrzucane. Wybranie opcji tworzenia sklepu z bazami informacji umożliwi zachowanie ulepszonych dokumentów. 

Jeśli w przeszłości korzystasz z umiejętności poznawczych, już wiesz, że *umiejętności* przenieść dokument przez sekwencję wzbogacania. Wynikiem może być indeks wyszukiwania lub projekcje w sklepie z bazami danych. Dwa dane wyjściowe, indeks wyszukiwania i magazyn wiedzy są produktami tego samego potoku; pochodzące z tych samych danych wejściowych, ale wynikające z danych wyjściowych, które są strukturalne, przechowywane i używane na różne sposoby.

Fizycznie magazyn wiedzy to [Azure Storage](../storage/common/storage-account-overview.md), Azure Table Storage, Azure Blob Storage lub oba te elementy. Wszystkie narzędzia i procesy, które mogą łączyć się z usługą Azure Storage, mogą zużywać zawartość sklepu z bazami wiedzy.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![Magazyn wiedzy w diagramie potoku](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Magazyn wiedzy w diagramie potoku")

## <a name="benefits-of-knowledge-store"></a>Zalety sklepu z bazami danych

Magazyn wiedzy oferuje strukturę, kontekst i rzeczywistą zawartość — wydobyć z plików danych bez struktury i z częściową strukturą, takich jak obiekty blob, pliki obrazów, które zostały poddane analizie, a nawet dane strukturalne, Przekształć w nowe formularze. W [instruktażu krok po kroku](knowledge-store-create-rest.md)można zobaczyć, jak w pierwszej kolejności jest podzielony dokument o gęstym formacie JSON na podstruktury, który został przekształcony w nowe struktury i w inny sposób udostępniony dla procesów podrzędnych, takich jak obciążenia maszynowe i analizy danych.

Mimo że warto zobaczyć, co można utworzyć za pomocą potoku wzbogacenia AI, realny potencjał magazynu wiedzy jest możliwość zmiany kształtu danych. Możesz zacząć od podstawowej zestawu umiejętności, a następnie wykonać iterację w celu dodania rosnących poziomów struktury, którą można następnie połączyć do nowych struktur, przydatnych w innych aplikacjach poza platformą Azure Wyszukiwanie poznawcze.

Wyliczane są korzyści z używania sklepu z bazami danych:

+ Korzystaj z ulepszonych dokumentów w [narzędziach do analizy i raportowania](#tools-and-apps) innych niż wyszukiwanie. Power BI z Power Query jest atrakcyjnym wyborem, ale dowolne narzędzie lub aplikacja, która może łączyć się z usługą Azure Storage, może pobierać z utworzonego przez siebie magazynu wiedzy.

+ Uściślij potok indeksowania AI podczas debugowania kroków i definicji zestawu umiejętności. Magazyn wiedzy przedstawia produkt definicji zestawu umiejętności w potoku indeksowania systemu AI. Możesz użyć tych wyników, aby zaprojektować lepszy zestawu umiejętności, ponieważ można zobaczyć dokładnie, jak wyglądają wzbogacenia. Możesz użyć [Eksplorator usługi Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) w usłudze Azure Storage, aby wyświetlić zawartość sklepu z bazami wiedzy.

+ Kształtowanie danych w nowych formularzach. Zmiana kształtu jest skodyfikowana w umiejętności, ale punkt jest, że zestawu umiejętności może teraz zapewnić tę możliwość. [Umiejętność kształtowania](cognitive-search-skill-shaper.md) w usłudze Azure wyszukiwanie poznawcze została rozszerzona w celu uwzględnienia tego zadania. Zmiana kształtu pozwala zdefiniować projekcję, która jest wyrównana do zamierzonego użycia danych przy zachowaniu relacji.

> [!Note]
> Jesteś nowym sposobem wzbogacania i umiejętności poznawczych? Platforma Azure Wyszukiwanie poznawcze integruje się z funkcjami obsługi Cognitive Services i języka, aby wyodrębnić i wzbogacić dane źródłowe za pomocą optycznego rozpoznawania znaków (OCR) przez pliki obrazów, rozpoznawanie jednostek i wyodrębnianie kluczowych fraz z plików tekstowych. Aby uzyskać więcej informacji, zobacz [wzbogacanie AI na platformie Azure wyszukiwanie poznawcze](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Magazyn fizyczny


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


Wyrażenie fizyczne magazynu wiedzy jest przegubem za pomocą `projections` elementu `knowledgeStore` definicji w zestawu umiejętności. Projekcja definiuje strukturę danych wyjściowych, tak aby była zgodna z zamierzonym użyciem.

Projekcje mogą być łączone jako tabele, obiekty lub pliki.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Typ projekcji określony w tej strukturze określa typ magazynu używanego przez magazyn wiedzy.

+ Magazyn tabel jest używany podczas definiowania `tables` . Zdefiniuj projekcję tabeli, gdy potrzebujesz tabelarycznych struktur raportowania dla danych wejściowych w narzędziach analitycznych lub wyeksportować jako ramki danych do innych magazynów danych. Możesz określić wielokrotność, `tables` Aby uzyskać podzbiór lub przecięcie dokumentów wzbogaconych. W ramach tej samej grupy projekcji relacje tabeli są zachowywane, aby można było korzystać ze wszystkich z nich.

+ Magazyn obiektów BLOB jest używany podczas definiowania `objects` lub `files` . Fizyczna reprezentacja elementu `object` to hierarchiczna struktura JSON, która reprezentuje wzbogacony dokument. A `file` jest obrazem wyodrębnionym z dokumentu, przeniesionym niezmienionym do magazynu obiektów BLOB.

Pojedynczy obiekt projekcji zawiera jeden zestaw `tables` , `objects` , `files` , i dla wielu scenariuszy, co może być wystarczające. 

Istnieje jednak możliwość utworzenia wielu zestawów `table` - `object` - `file` projekcji i można to zrobić, jeśli chcesz mieć różne relacje danych. W zestawie dane są powiązane, przy założeniu, że te relacje istnieją i mogą być wykrywane. W przypadku tworzenia dodatkowych zestawów dokumenty w każdej grupie nigdy nie są powiązane. Przykładem użycia wielu grup projekcji może być, jeśli chcesz, aby te same dane były przeznaczone do użytku z systemem online i muszą być reprezentowane w określony sposób. te same dane są również przeznaczone do użycia w potoku analizy danych, który jest prezentowany inaczej.

## <a name="requirements"></a>Wymagania 

[Usługa Azure Storage](../storage/index.yml) jest wymagana. Zapewnia magazyn fizyczny. Można użyć usługi BLOB Storage, magazynu tabel lub obu. Magazyn obiektów BLOB jest używany w nienaruszonych dokumentach, zazwyczaj gdy dane wyjściowe przechodzą do procesów podrzędnych. Magazyn tabel jest przeznaczony dla wycinków ulepszonych dokumentów, często używanych na potrzeby analizy i raportowania.

[Zestawu umiejętności](cognitive-search-working-with-skillsets.md) jest wymagany. Zawiera `knowledgeStore` definicję i określa strukturę i kompozycję wzbogaconego dokumentu. Nie można utworzyć sklepu z wiedzą przy użyciu pustej zestawu umiejętności. Musisz mieć co najmniej jedną umiejętność w zestawu umiejętności.

Wymagany jest [indeksator](search-indexer-overview.md) . Zestawu umiejętności jest wywoływany przez indeksator, który jest dyskiem wykonania. Indeksatory mają swój własny zestaw wymagań i atrybutów. Niektóre z tych atrybutów mają bezpośredni wpływ na magazyn wiedzy:

+ Indeksatory wymagają [obsługiwanego źródła danych platformy Azure](search-indexer-overview.md#supported-data-sources) (potok, który ostatecznie tworzy magazyn wiedzy, jest uruchamiany przez ściąganie danych z obsługiwanego źródła na platformie Azure). 

+ Indeksatory wymagają indeksu wyszukiwania. Indeksator wymaga podania schematu indeksu, nawet jeśli nie planujesz go używać. Minimalny indeks ma jedno pole ciągu oznaczone jako klucz.

+ Indeksatory zapewniają opcjonalne mapowania pól używane do aliasowania pola źródłowego w polu docelowym. Jeśli domyślne mapowanie pola wymaga modyfikacji (aby użyć innej nazwy lub typu), można utworzyć [Mapowanie pola](search-indexer-field-mappings.md) w indeksatorze. W przypadku danych wyjściowych magazynu wiedzy miejsce docelowe może być polem w obiekcie obiektu BLOB lub tabeli.

+ Indeksatory mają harmonogramy i inne właściwości, takie jak mechanizmy wykrywania zmian zapewniane przez różne źródła danych, mogą być również stosowane do magazynu wiedzy. Na przykład można [zaplanować](search-howto-schedule-indexers.md) wzbogacanie w regularnych odstępach czasu, aby odświeżyć zawartość. 

## <a name="how-to-create-a-knowledge-store"></a>Jak utworzyć sklep merytoryczny

Aby utworzyć magazyn wiedzy, użyj portalu lub interfejsu API REST ( `api-version=2020-06-30` ).

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Kreator **importu danych** zawiera opcje tworzenia sklepu z bazami informacji. W przypadku początkowej eksploracji [Utwórz swój pierwszy magazyn wiedzy w czterech krokach](knowledge-store-connect-power-bi.md).

1. Wybierz obsługiwane źródło danych.

1. Określ wzbogacanie: dołączanie zasobu, wybieranie umiejętności i określanie sklepu merytorycznego. 

1. Utwórz schemat indeksu. Kreator wymaga go i może wywnioskować go dla Ciebie.

1. Uruchom kreatora. W tym ostatnim kroku występuje Ekstrakcja, wzbogacanie i magazyn.

### <a name="use-create-skillset-rest-api"></a>Użyj Create zestawu umiejętności (interfejs API REST)]

A `knowledgeStore` jest zdefiniowany w [zestawu umiejętności](cognitive-search-working-with-skillsets.md), który z kolei jest wywoływany przez [indeksator](search-indexer-overview.md). Podczas wzbogacania usługa Azure Wyszukiwanie poznawcze tworzy miejsce na koncie usługi Azure Storage i projektuje wzbogacone dokumenty jako obiekty blob lub tabele, w zależności od konfiguracji.

Interfejs API REST to jeden mechanizm, za pomocą którego można programowo utworzyć magazyn wiedzy. Prostą metodą eksplorowania jest [utworzenie pierwszego sklepu z bazami danych przy użyciu programu Poster i interfejsu API REST](knowledge-store-create-rest.md).

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Jak nawiązać połączenie z narzędziami i aplikacjami

Po wprowadzeniu wzbogaceń do magazynu dowolne narzędzie lub technologia, która łączy się z obiektem blob platformy Azure lub magazynem tabel, może służyć do eksplorowania, analizowania lub korzystania z zawartości. Na poniższej liście przedstawiono początek:

+ [Eksplorator usługi Storage](knowledge-store-view-storage-explorer.md) , aby wyświetlić wzbogaconą strukturę dokumentu i zawartość. Rozważmy to jako narzędzie podstawowe do wyświetlania zawartości w sklepie z bazami danych.

+ [Power BI](knowledge-store-connect-power-bi.md) na potrzeby raportowania i analizy. 

+ [Azure Data Factory](../data-factory/index.yml) dalsze manipulowanie.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Dokumentacja interfejsu API

Wersja interfejsu API REST `2020-06-30` zapewnia magazyn wiedzy przez dodatkowe definicje w witrynie umiejętności. Oprócz odwołania, aby uzyskać szczegółowe informacje na temat wywoływania interfejsów API, zobacz artykuł  [Tworzenie sklepu merytorycznego korzystającego z programu Poster](knowledge-store-create-rest.md) .

+ [Create zestawu umiejętności (API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Update zestawu umiejętności (API-Version = 2020-06-30)](/rest/api/searchservice/update-skillset)


## <a name="next-steps"></a>Następne kroki

Magazyn wiedzy oferuje trwałość ulepszonych dokumentów, przydatnych podczas projektowania zestawu umiejętności lub tworzenia nowych struktur i zawartości do użycia przez dowolne aplikacje klienckie, które mogą uzyskać dostęp do konta usługi Azure Storage.

Najprostszym podejściem do tworzenia wzbogaconych dokumentów jest [Przechodzenie przez portal](knowledge-store-create-portal.md), ale można również użyć programu Poster i interfejsu API REST, który jest bardziej przydatny, jeśli chcesz uzyskać wgląd w sposób tworzenia obiektów i odwoływania się do nich.

> [!div class="nextstepaction"]
> [Tworzenie sklepu z wiedzą przy użyciu programu Poster i REST](knowledge-store-create-rest.md)

Aby dowiedzieć się więcej o projekcjach, możliwościach i sposobach [ich definiowania w zestawu umiejętności](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projekcje w sklepie merytorycznym](knowledge-store-projection-overview.md)

Samouczek obejmujący zaawansowane koncepcje dotyczące projekcji, takich jak dzielenie, kształtowanie i relacje w tekście, rozpoczyna się od [definiowania projekcji w sklepie z wiedzą](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definiowanie prognoz w sklepie z bazami danych](knowledge-store-projections-examples.md)