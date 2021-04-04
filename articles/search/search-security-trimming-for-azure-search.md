---
title: Filtry zabezpieczeń do przycinania wyników
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak zaimplementować uprawnienia zabezpieczeń na poziomie dokumentu dla wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze przy użyciu filtrów zabezpieczeń i tożsamości użytkowników.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 8bd162fcf2011d2ccce716564763e7f54f19ff69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97631807"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Filtry zabezpieczeń do przycinania wyników na platformie Azure Wyszukiwanie poznawcze

Filtry zabezpieczeń można stosować do przycinania wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze w oparciu o tożsamość użytkownika. To środowisko wyszukiwania zwykle wymaga porównania tożsamości osoby, która chce przeszukać w odniesieniu do pola zawierającego zasady, które mają uprawnienia do dokumentu. Po znalezieniu dopasowania użytkownik lub podmiot zabezpieczeń (na przykład grupa lub rola) ma dostęp do tego dokumentu.

Jednym ze sposobów osiągnięcia filtrowania zabezpieczeń jest skomplikowane rozłączenie wyrażeń równości: na przykład, `Id eq 'id1' or Id eq 'id2'` i tak dalej. Takie podejście jest podatne na błędy, trudne do utrzymania i w przypadkach, gdy lista zawiera setki lub tysiące wartości, spowalnia czas odpowiedzi kwerendy o wiele sekund. 

Łatwiejszym i szybszym podejściem jest użycie `search.in` funkcji. Jeśli używasz `search.in(Id, 'id1, id2, ...')` zamiast wyrażenia równości, możesz oczekiwać, że czasy odpowiedzi są podrzędne.

W tym artykule pokazano, jak wykonać filtrowanie zabezpieczeń, wykonując następujące czynności:
> [!div class="checklist"]
> * Utwórz pole, które zawiera identyfikatory podmiotu zabezpieczeń 
> * Wypchnij lub zaktualizuj istniejące dokumenty z odpowiednimi identyfikatorami podmiotu zabezpieczeń
> * Wydaj żądanie `search.in` Search `filter`

>[!NOTE]
> Proces pobierania identyfikatorów podmiotu zabezpieczeń nie został uwzględniony w tym dokumencie. Należy uzyskać od dostawcy usługi tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz [subskrypcję platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F),[usługę wyszukiwanie poznawcze platformy Azure](search-create-service-portal.md)i [indeks](search-what-is-an-index.md).  

## <a name="create-security-field"></a>Utwórz pole zabezpieczeń

Dokumenty muszą zawierać pole określające, które grupy mają dostęp. Te informacje staną się kryteriami filtrowania, względem których dokumenty są wybierane lub odrzucane z zestawu wyników zwróconego do wystawcy.
Załóżmy, że mamy indeks zabezpieczonych plików, a każdy plik jest dostępny dla innego zestawu użytkowników.

1. Dodaj pole `group_ids` (w tym miejscu możesz wybrać dowolną nazwę) `Collection(Edm.String)` . Upewnij się, że pole ma `filterable` atrybut ustawiony na `true` tak, aby wyniki wyszukiwania zostały odfiltrowane na podstawie dostępu użytkownika. Na przykład jeśli ustawisz `group_ids` pole na `["group_id1, group_id2"]` dla dokumentu z `file_name` "secured_file_b", tylko użytkownicy należący do identyfikatorów grup "group_id1" lub "group_id2" mają dostęp do odczytu do tego pliku.
   
   Upewnij się, że `retrievable` atrybut pola jest ustawiony na `false` tak, aby nie był zwracany jako część żądania wyszukiwania.

2. Dodaj także `file_id` `file_name` pola i dla przykładu.  

    ```JSON
    {
        "name": "securedfiles",  
        "fields": [
            {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
            {"name": "file_name", "type": "Edm.String"},
            {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
        ]
    }
    ```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>Wypychanie danych do indeksu przy użyciu interfejsu API REST
  
Wydaj żądanie HTTP POST do punktu końcowego adresu URL Twojego indeksu. Treść żądania HTTP jest obiektem JSON zawierającym dokumenty, które mają zostać dodane:

```http
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2020-06-30  
Content-Type: application/json
api-key: [admin key]
```

W treści żądania Określ zawartość dokumentów:

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

Jeśli musisz zaktualizować istniejący dokument z listą grup, możesz użyć `merge` `mergeOrUpload` akcji lub:

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

Aby uzyskać szczegółowe informacje na temat dodawania lub aktualizowania dokumentów, można odczytać [Edytowanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="apply-the-security-filter"></a>Zastosuj filtr zabezpieczeń

Aby można było przyciąć dokumenty na podstawie `group_ids` dostępu, należy wydać zapytanie wyszukiwania z `group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` filtrem, gdzie "group_id1, group_id2,..." są grupami, do których należy wystawca żądania wyszukiwania.

Ten filtr dopasowuje wszystkie dokumenty, dla których `group_ids` pole zawiera jeden z podanego identyfikatorów.
Aby uzyskać szczegółowe informacje na temat wyszukiwania dokumentów przy użyciu usługi Azure Wyszukiwanie poznawcze, można odczytywać [dokumenty wyszukiwania](/rest/api/searchservice/search-documents).
Należy zauważyć, że w tym przykładzie pokazano, jak przeszukiwać dokumenty przy użyciu żądania POST.

Wydaj żądanie HTTP POST:

```http
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2020-06-30
Content-Type: application/json  
api-key: [admin or query key]
```

Określ filtr w treści żądania:

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

Należy pobrać dokumenty z powrotem, gdzie `group_ids` zawiera "group_id1" lub "group_id2". Innymi słowy, uzyskasz dokumenty, do których wystawcy żądania ma dostęp do odczytu.

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano wzorzec filtrowania wyników w oparciu o tożsamość użytkownika i `search.in()` funkcję. Za pomocą tej funkcji można przekazać identyfikatory zasad dla użytkownika żądającego, aby dopasować je do identyfikatorów podmiotu zabezpieczeń skojarzonych z każdym dokumentem docelowym. Gdy żądanie wyszukiwania jest obsługiwane, `search.in` Funkcja filtruje wyniki wyszukiwania, dla których żaden z podmiotów zabezpieczeń użytkownika nie ma dostępu do odczytu. Identyfikatory podmiotów mogą reprezentować elementy, takie jak grupy zabezpieczeń, role, a nawet własna tożsamość użytkownika.

Aby uzyskać alternatywny wzorzec oparty na Active Directory lub aby ponownie odwiedzić inne funkcje zabezpieczeń, zobacz następujące linki.

* [Filtry zabezpieczeń do przycinania wyników przy użyciu tożsamości Active Directory](search-security-trimming-for-azure-search-with-aad.md)
* [Zabezpieczenia w usłudze Azure Wyszukiwanie poznawcze](search-security-overview.md)