---
title: Funkcja zapytania moreLikeThis (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Opisuje funkcję moreLikeThis (wersja zapoznawcza), która jest dostępna w wersji zapoznawczej interfejsu API REST usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cd6b64f118460a115963ed0bf105641d80334348
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934994"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (wersja zapoznawcza) na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT] 
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2020-06-30 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.

`moreLikeThis=[key]` to parametr zapytania w [interfejsie API dokumentów wyszukiwania](/rest/api/searchservice/search-documents) , który znajduje dokumenty podobne do dokumentu określonego przez klucz dokumentu. Gdy zostanie wysłane żądanie wyszukiwania `moreLikeThis` , zapytanie jest generowane z wyszukiwanymi terminami wyciągniętymi z danego dokumentu, który opisuje ten dokument. Wygenerowane zapytanie jest następnie używane do wykonywania żądania wyszukiwania. Domyślnie zawartość wszystkich pól, które można przeszukiwać, jest uwzględniana, minus wszystkie pola z ograniczeniami, które zostały określone za pomocą `searchFields` parametru. `moreLikeThis`Nie można użyć parametru z parametrem wyszukiwania `search=[string]` .

Domyślnie są brane pod uwagę zawartość wszystkich pól z możliwością wyszukiwania najwyższego poziomu. Jeśli chcesz zamiast tego określić określone pola, możesz użyć `searchFields` parametru. 

Nie można używać `MoreLikeThis` w przypadku pól podrzędnych z możliwością wyszukiwania w [typie złożonym](search-howto-complex-data-types.md).

## <a name="examples"></a>Przykłady

W poniższych przykładach użyto przykładu hoteli z [przewodnika Szybki Start: Tworzenie indeksu wyszukiwania w Azure Portal](search-get-started-portal.md).

### <a name="simple-query"></a>Proste zapytanie

Poniższe zapytanie znajduje dokumenty, których pola opisu są najbardziej podobne do pola dokumentu źródłowego, określonego przez `moreLikeThis` parametr:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

W tym przykładzie żądanie wyszukuje Hotele podobne do tego z `HotelId` 29.
Zamiast korzystać z protokołu HTTP GET, można również wywołać `MoreLikeThis` przy użyciu protokołu HTTP Post:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Stosowanie filtrów

`MoreLikeThis` można łączyć z innymi typowymi parametrami zapytania, takimi jak `$filter` . Na przykład zapytanie może być ograniczone tylko do hoteli, którego kategoria ma wartość "budżet" i gdzie klasyfikacja jest wyższa niż 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>Wybierz pola i Ogranicz wyniki

`$top`Selektora można użyć, aby ograniczyć liczbę wyników, które powinny być zwracane w `MoreLikeThis` zapytaniu. Ponadto pola mogą być wybierane za pomocą `$select` . W tym miejscu wybiera się trzy pierwsze Hotele z IDENTYFIKATORem, nazwą i klasyfikacją: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>Następne kroki

Możesz użyć dowolnego narzędzia do testowania sieci Web, aby eksperymentować z tą funkcją.  Zalecamy używanie programu dla tego ćwiczenia.

> [!div class="nextstepaction"]
> [Poznaj interfejsy API REST platformy Azure Wyszukiwanie poznawcze przy użyciu programu Poster](search-get-started-postman.md)