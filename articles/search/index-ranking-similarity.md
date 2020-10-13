---
title: Algorytm podobieństwa do klasyfikacji
titleSuffix: Azure Cognitive Search
description: Jak ustawić algorytm podobieństwa, aby wypróbować nowy algorytm podobieństwa na potrzeby klasyfikowania
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: 651e0635f0b556cd47adfccdbac59ef587570128
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535733"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algorytm klasyfikacji w usłudze Azure Wyszukiwanie poznawcze

> [!IMPORTANT]
> Od 15 lipca 2020, nowo utworzone usługi wyszukiwania będą korzystać z funkcji klasyfikacji BM25 automatycznie, która została sprawdzona w większości przypadków, aby zapewnić klasyfikacje wyszukiwania, które są lepiej dopasowane do oczekiwań użytkowników niż bieżąca Klasyfikacja domyślna. Poza wyższą klasyfikacją BM25 włącza również opcje konfiguracji dla wyników dostrajania na podstawie takich czynników, jak rozmiar dokumentu.  
>
> W przypadku tej zmiany najprawdopodobniej zobaczysz nieznaczne zmiany w kolejności wyników wyszukiwania. Dla osób, które chcą przetestować wpływ tej zmiany, algorytm BM25 jest dostępny w interfejsie API-Version 2019-05-06-Preview i w 2020-06-30.  

W tym artykule opisano, jak można użyć nowego BM25ego algorytmu klasyfikacji w istniejących usługach wyszukiwania dla nowych indeksów utworzonych i zapytań przy użyciu interfejsu API podglądu.

Usługa Azure Wyszukiwanie poznawcze jest w trakcie wdrażania oficjalnej implementacji programu z Okapi BM25, *BM25Similarity*, która zastąpi poprzednio używaną implementację *ClassicSimilarity* . Podobnie jak w przypadku starszego algorytmu ClassicSimilarity, BM25Similarity to funkcja pobierania "TF-IDF", która używa częstotliwości okresów (TF) i odwrotnej częstotliwości dokumentu (IDF) jako zmiennych do obliczania ocen przydatności dla każdej pary dokumentów-zapytań, która jest następnie używana do klasyfikowania. 

Chociaż koncepcyjnie przypomina starszy klasyczny algorytm podobieństwa, BM25 pobiera jego rdzeń w probabilistyczne pobierania informacji w celu ich usprawnienia. BM25 oferuje także zaawansowane opcje dostosowywania, takie jak umożliwienie użytkownikowi decydowania, jak Ocena istotności skaluje się z terminem częstotliwości pasujących warunków.

## <a name="how-to-test-bm25-today"></a>Jak przetestować BM25 dzisiaj

Podczas tworzenia nowego indeksu można ustawić właściwość **podobieństwo** , aby określić algorytm. Możesz użyć `api-version=2019-05-06-Preview` , jak pokazano poniżej, lub `api-version=2020-06-30` .

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
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

Właściwość **podobieństwa** jest przydatna w tym okresie przejściowym, gdy oba algorytmy są dostępne tylko w istniejących usługach. 

| Właściwość | Opis |
|----------|-------------|
| podobieństwa | Opcjonalny. Prawidłowe wartości to *"#Microsoft. Azure. Search. ClassicSimilarity"* lub *"#Microsoft. Azure. Search. BM25Similarity"*. <br/> Wymaga `api-version=2019-05-06-Preview` lub nie dotyczy usługi wyszukiwania utworzonej przed 15 lipca 2020. |

W przypadku nowych usług utworzonych po 15 lipca 2020 BM25 jest używany automatycznie i jest to jedyny algorytm podobieństwa. Jeśli użytkownik spróbuje określić **podobieństwo** do `ClassicSimilarity` nowej usługi, zostanie zwrócony błąd 400, ponieważ ten algorytm nie jest obsługiwany w nowej usłudze.

W przypadku istniejących usług utworzonych przed 15 lipca 2020, klasyczny podobieństwo pozostaje algorytmem domyślnym. Jeśli właściwość **podobieństwa** zostanie pominięta lub ma wartość null, indeks używa algorytmu klasycznego. Jeśli chcesz użyć nowego algorytmu, musisz ustawić **podobieństwo** zgodnie z powyższym opisem.

## <a name="bm25-similarity-parameters"></a>Parametry podobieństwa BM25

BM25 podobieństwa dodaje dwa dostosowywalne parametry, aby kontrolować obliczony wynik istotności.

### <a name="k1"></a>K1

Parametr *K1* kontroluje funkcję skalowania między okresem obowiązywania poszczególnych warunków dopasowywania do ostatecznego wyniku dopasowania w parze Document-Query.

Wartość zero reprezentuje "model binarny", gdzie udział jednego pasującego terminu jest taki sam dla wszystkich zgodnych dokumentów, bez względu na to, ile razy ten termin pojawia się w tekście, podczas gdy większa wartość K1 umożliwia dalszą podwyżkę, gdy w dokumencie zostanie znalezionych więcej wystąpień tego samego terminu. Domyślnie usługa Azure Wyszukiwanie poznawcze używa wartości 1,2 dla parametru K1. Użycie wyższej wartości K1 może być ważne w przypadkach, gdy oczekujemy, że wiele warunków będzie częścią zapytania wyszukiwania. W takich przypadkach możemy chcieć preferować dokumenty, które pasują do wielu różnych warunków zapytania, które są przeszukiwane w dokumentach, które pasują tylko do jednego, wielu razy. Na przykład podczas wykonywania zapytania dotyczącego indeksu dla dokumentów zawierających warunki "Apollo Spaceflight" możemy chcieć obniżyć wynik artykułu dotyczący języka greckiego Mythology, który zawiera termin "Apollo" kilka razy, bez wzmianki o wyrazie "Spaceflight", w porównaniu do innego artykułu, w którym jawnie wspomina zarówno "Apollo", jak i "Spaceflight". 
 
### <a name="b"></a>b

Parametr *b* kontroluje, jak długość dokumentu ma wpływ na ocenę istotności.

Wartość 0,0 oznacza, że długość dokumentu nie będzie miała wpływu na ocenę, natomiast wartość 1,0 oznacza, że wpływ częstotliwości okresów dla oceny istotności będzie znormalizowany przez długość dokumentu. Wartością domyślną użytą w Wyszukiwanie poznawcze platformy Azure dla parametru b jest 0,75. Ujednolicenie częstotliwości według długości dokumentu jest przydatne w przypadkach, w których chcemy karać dłuższe dokumenty. W niektórych przypadkach dłuższe dokumenty (takie jak Pełna Nowa) mogą zawierać wiele nieistotnych warunków w porównaniu do znacznie krótszych dokumentów.

### <a name="setting-k1-and-b-parameters"></a>Ustawianie parametrów K1 i b

Aby dostosować wartości b lub K1, po prostu dodaj je jako właściwości do obiektu podobieństwa przy użyciu BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

Algorytm podobieństwa można ustawić tylko w czasie tworzenia indeksu. Oznacza to, że używany algorytm podobieństwa nie może zostać zmieniony dla istniejących indeksów. Parametry *"b"* i *"K1"* można modyfikować podczas aktualizowania istniejącej definicji indeksu korzystającej z BM25. Zmiana tych wartości w istniejącym indeksie spowoduje, że indeks zostanie przełączony w tryb offline przez co najmniej kilka sekund, co spowoduje niepowodzenie żądania indeksowania i zapytania. Z tego powodu należy ustawić parametr "allowIndexDowntime = true" w ciągu zapytania żądania aktualizacji:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>Zobacz też  

+ [Dokumentacja interfejsu API REST](/rest/api/searchservice/)
+ [Dodawanie profilów oceniania do indeksu](index-add-scoring-profiles.md)
+ [Tworzenie interfejsu API indeksu](/rest/api/searchservice/create-index)
+ [Azure Wyszukiwanie poznawcze .NET SDK](/dotnet/api/overview/azure/search)