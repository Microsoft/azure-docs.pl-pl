---
title: Konfigurowanie algorytmu podobieństwa klasyfikacji
titleSuffix: Azure Cognitive Search
description: Jak ustawić algorytm podobieństwa, aby wypróbować nowy algorytm podobieństwa na potrzeby klasyfikowania
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9f806b512ae8e118fca8f32115c8be3b493fd681
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677787"
---
# <a name="configure-ranking-algorithms-in-azure-cognitive-search"></a>Konfigurowanie algorytmów klasyfikacji w usłudze Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze obsługuje dwa algorytmy klasyfikacji podobieństwa:

+ *Klasyczny algorytm podobieństwa* używany przez wszystkie usługi wyszukiwania do 15 lipca 2020.
+ Implementacja algorytmu *Okapi BM25* używana we wszystkich usługach wyszukiwania utworzonych po 15 lipca.

Klasyfikacja BM25 jest nowym ustawieniem domyślnym, ponieważ ma on generować klasyfikacje wyszukiwania, które są lepiej dostosowane do oczekiwań użytkownika. Włącza również opcje konfiguracji dla wyników dostrajania na podstawie czynników, takich jak rozmiar dokumentu. W przypadku nowych usług utworzonych po 15 lipca 2020 BM25 jest używany automatycznie i jest to jedyny algorytm podobieństwa. Jeśli spróbujesz ustawić podobieństwo do ClassicSimilarity w nowej usłudze, zostanie zwrócony błąd HTTP 400, ponieważ ten algorytm nie jest obsługiwany przez usługę.

W przypadku starszych usług utworzonych przed 15 lipca 2020, klasyczny podobieństwo pozostaje algorytmem domyślnym. Starsze usługi mogą ustawiać właściwości indeksu wyszukiwania, aby wywoływać BM25, jak wyjaśniono poniżej. Jeśli przełączasz się z klasycznego do BM25, możesz oczekiwać, że zobaczysz pewne różnice w sposobie uporządkowania wyników wyszukiwania.

> [!NOTE]
> Wyszukiwanie semantyczne to dodatkowy algorytm ponownej klasyfikacji semantycznej, który zawęża przerwy między oczekiwaniami a wynikami jeszcze więcej. W przeciwieństwie do innych algorytmów jest to funkcja dodatku, która iteruje w istniejącym zestawie wyników. Aby użyć algorytmu wyszukiwania semantycznego w wersji zapoznawczej, należy utworzyć nową usługę i należy określić [Typ kwerendy semantycznej](semantic-how-to-query-request.md). Aby uzyskać więcej informacji, zobacz [Omówienie wyszukiwania semantycznego](semantic-search-overview.md).

## <a name="create-a-search-index-for-bm25-scoring"></a>Utwórz indeks wyszukiwania dla oceny BM25

Jeśli używasz usługi wyszukiwania, która została utworzona przed 15 lipca 2020, możesz ustawić właściwość podobieństwa na BM25Similarity lub ClassicSimilarity w definicji indeksu. Jeśli właściwość podobieństwa zostanie pominięta lub ma wartość null, indeks używa algorytmu klasycznego.

Algorytm podobieństwa można ustawić tylko w czasie tworzenia indeksu. Jednak po utworzeniu indeksu za pomocą BM25 można zaktualizować istniejący indeks, aby ustawić lub zmodyfikować parametry BM25.

| Biblioteka kliencka | Właściwość podobieństwa |
|----------------|---------------------|
| .NET  | [SearchIndex. podobieństwo](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex. setpodobieństwo](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex. podobieństwo](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [Właściwość podobieństwa w SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>Przykład REST

Można również użyć [interfejsu API REST](/rest/api/searchservice/create-index), jak ilustruje poniższy przykład:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="bm25-similarity-parameters"></a>Parametry podobieństwa BM25

BM25 podobieństwa dodaje dwa dostosowywalne parametry, aby kontrolować obliczony wynik istotności. Można ustawić parametry BM25 podczas tworzenia indeksu lub jako aktualizację indeksu, jeśli algorytm BM25 został określony podczas tworzenia indeksu.

| Właściwość | Typ | Opis |
|----------|------|-------------|
| K1 | liczba | Kontroluje funkcję skalowania między okresem obowiązywania poszczególnych warunków dopasowywania do ostatecznej oceny istotności pary Document-Query. Wartości są zwykle 0,0 do 3,0, z 1,2 jako domyślne. </br></br>Wartość 0,0 reprezentuje "model binarny", gdzie udział jednego pasującego terminu jest taki sam dla wszystkich zgodnych dokumentów, bez względu na to, ile razy ten termin pojawia się w tekście, podczas gdy większa wartość K1 umożliwia dalszą podwyżkę, gdy w dokumencie zostanie znalezionych więcej wystąpień tego samego terminu. </br></br>Użycie wyższej wartości K1 może być ważne w przypadkach, gdy oczekujemy, że wiele warunków będzie częścią zapytania wyszukiwania. W takich przypadkach możemy chcieć preferować dokumenty, które pasują do wielu różnych warunków zapytania, które są przeszukiwane w dokumentach, które pasują tylko do jednego, wielu razy. Na przykład podczas wykonywania zapytania dotyczącego indeksu dla dokumentów zawierających warunki "Apollo Spaceflight" możemy chcieć obniżyć wynik artykułu dotyczący języka greckiego Mythology, który zawiera termin "Apollo" kilka razy, bez wzmianki o "Spaceflight", w porównaniu do innego artykułu, w którym jawnie wspomina zarówno "Apollo", jak i "Spaceflight". |
| b | liczba | Kontroluje, jak długość dokumentu ma wpływ na ocenę istotności. Wartości są z zakresu od 0 do 1, z wartością domyślną 0,75. </br></br>Wartość 0,0 oznacza, że długość dokumentu nie będzie miała wpływu na ocenę, natomiast wartość 1,0 oznacza, że wpływ częstotliwości okresów dla oceny istotności będzie znormalizowany przez długość dokumentu. </br></br>Ujednolicenie częstotliwości według długości dokumentu jest przydatne w przypadkach, w których chcemy karać dłuższe dokumenty. W niektórych przypadkach dłuższe dokumenty (takie jak Pełna Nowa) mogą zawierać wiele nieistotnych warunków w porównaniu do znacznie krótszych dokumentów. |

### <a name="setting-k1-and-b-parameters"></a>Ustawianie parametrów K1 i b

Aby ustawić lub zmodyfikować wartości b lub K1, Dodaj je do obiektu podobieństwa BM25. Ustawienie lub zmiana tych wartości w istniejącym indeksie przejdzie indeks do trybu offline przez co najmniej kilka sekund, powodując niepowodzenie aktywnego indeksowania i żądań zapytań. W związku z tym należy ustawić parametr "allowIndexDowntime = true" żądania aktualizacji:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Zobacz też  

+ [Dokumentacja interfejsu API REST](/rest/api/searchservice/)
+ [Dodawanie profilów oceniania do indeksu](index-add-scoring-profiles.md)
+ [Tworzenie interfejsu API indeksu](/rest/api/searchservice/create-index)
+ [Azure Wyszukiwanie poznawcze .NET SDK](/dotnet/api/overview/azure/search)