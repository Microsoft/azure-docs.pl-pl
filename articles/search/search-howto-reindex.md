---
title: Ponowne kompilowanie indeksu wyszukiwania
titleSuffix: Azure Cognitive Search
description: Dodaj nowe elementy, zaktualizuj istniejące elementy lub dokumenty lub Usuń przestarzałe dokumenty z pełną kompilacją lub częściowym indeksem, aby odświeżyć indeks Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 47e9b80bb25b7ff14695cc67682265fe338ff76f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119105"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Jak ponownie skompilować indeks na platformie Azure Wyszukiwanie poznawcze

W tym artykule wyjaśniono, jak ponownie skompilować indeks usługi Azure Wyszukiwanie poznawcze, sytuacje, w których są wymagane ponowne kompilacje oraz Zalecenia dotyczące łagodzenia wpływu rekompilacji na bieżące żądania zapytań.

*Odbudowanie* odwołuje się do porzucania i ponownego tworzenia fizycznych struktur danych skojarzonych z indeksem, w tym wszystkich odwróconych indeksów opartych na polach. W usłudze Azure Wyszukiwanie poznawcze nie można porzucić i ponownie utworzyć poszczególnych pól. Aby ponownie skompilować indeks, należy usunąć wszystkie magazyny pól, utworzyć je ponownie na podstawie istniejącego lub zmienionego schematu indeksu, a następnie ponownie wypełnić dane wypchnięte do indeksu lub pobrać ze źródeł zewnętrznych. 

Często można skompilować ponownie indeksy podczas tworzenia w trakcie wykonywania iteracji względem projektu indeksu, ale może być również konieczne odbudowanie indeksu poziomu produkcyjnego w celu uwzględnienia zmian strukturalnych, takich jak dodawanie typów złożonych lub dodawanie pól do sugestii.

## <a name="rebuild-versus-refresh"></a>"Kompiluj ponownie" zamiast "Odśwież"

Nie należy mylić odbudowy z odświeżaniem zawartości indeksu przy użyciu nowych, zmodyfikowanych lub usuniętych dokumentów. Odświeżenie korpus wyszukiwania jest niemal nadawane w każdej aplikacji wyszukiwania, z pewnymi scenariuszami wymagającymi aktualizacji do minuty (na przykład gdy korpus wyszukiwania musi odzwierciedlać zmiany spisu w aplikacji online Sales).

Tak długo, jak struktura indeksu nie jest zmieniana, można odświeżyć indeks przy użyciu tych samych metod, które zostały wcześniej użyte do załadowania indeksu:

* W przypadku indeksowania w trybie push należy wywołać [Dodawanie, aktualizowanie lub usuwanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents) , aby wypchnąć zmiany do indeksu.

* W przypadku indeksatorów można [zaplanować wykonywanie indeksatora](search-howto-schedule-indexers.md) i użyć funkcji śledzenia zmian lub sygnatur czasowych, aby zidentyfikować różnicę. Jeśli aktualizacje muszą być odzwierciedlone szybciej niż to, w którym można zarządzać harmonogramem, możesz zamiast tego użyć indeksowania w trybie push.

## <a name="rebuild-conditions"></a>Warunki ponownego kompilowania

Porzuć i Utwórz ponownie indeks, jeśli którykolwiek z następujących warunków jest spełniony. 

| Warunek | Opis |
|-----------|-------------|
| Zmiana definicji pola | Zmiana nazwy pola, typu danych lub określonych [atrybutów indeksu](/rest/api/searchservice/create-index) (wyszukiwanie, filtrowanie, sortowanie, tworzenie i tworzenie) wymaga pełnej kompilacji. |
| Przypisz Analizator do pola | [Analizatory](search-analyzers.md) są zdefiniowane w indeksie, a następnie przypisywane do pól. W dowolnym momencie możesz dodać nową definicję analizatora do indeksu, ale można *przypisać* Analizator tylko po utworzeniu pola. Dotyczy to zarówno **analizatora** , jak i właściwości **indexAnalyzer** . Właściwość **searchAnalyzer** jest wyjątkiem (można przypisać tę właściwość do istniejącego pola). |
| Aktualizowanie lub usuwanie definicji analizatora w indeksie | Nie można usunąć ani zmienić istniejącej konfiguracji analizatora (analizator, tokenizatora, filtr tokenu lub filtr znaków) w indeksie, chyba że cały indeks zostanie odbudowany. |
| Dodawanie pola do sugestii | Jeśli pole już istnieje i chcesz dodać je do konstrukcji [sugerującej](index-add-suggesters.md) , należy ponownie skompilować indeks. |
| Usuń pole | Aby fizycznie usunąć wszystkie ślady pola, należy ponownie skompilować indeks. Gdy bezpośrednie ponowne kompilowanie nie jest praktyczne, można zmodyfikować kod aplikacji, aby wyłączyć dostęp do pola "usunięte" lub użyć [$SELECT parametru zapytania](search-query-odata-select.md) , aby wybrać, które pola są reprezentowane w zestawie wyników. Fizycznie definicja pola i zawartość pozostają w indeksie do momentu kolejnej ponownej kompilacji, gdy zastosuje schemat, który pomija pole w danym momencie. |
| Przełącz warstwy | Jeśli potrzebujesz większej pojemności, w Azure Portal nie ma żadnego uaktualnienia w miejscu. Należy utworzyć nową usługę, a indeksy muszą być kompilowane od podstaw w nowej usłudze. Aby ułatwić automatyzację tego procesu, możesz użyć przykładowego kodu **index-Backup-Restore** w ramach tego [przykładowego repozytorium platformy Azure wyszukiwanie poznawcze .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Ta aplikacja utworzy kopię zapasową indeksu w serii plików JSON, a następnie ponownie utworzy indeks w określonej usłudze wyszukiwania.|

## <a name="update-conditions"></a>Warunki aktualizacji

Wiele innych modyfikacji można wprowadzać bez wpływu na istniejące struktury fizyczne. W szczególnych przypadkach następujące zmiany *nie* wymagają odbudowania indeksu. W przypadku tych zmian można [zaktualizować definicję indeksu](/rest/api/searchservice/update-index) zmianami.

+ Dodawanie nowego pola
+ Ustaw atrybut możliwy do **pobierania** dla istniejącego pola
+ Ustaw **searchAnalyzer** dla istniejącego pola
+ Dodaj nową definicję analizatora w indeksie
+ Dodawanie, aktualizowanie lub usuwanie profilów oceniania
+ Dodawanie, aktualizowanie lub usuwanie ustawień mechanizmu CORS
+ Dodawanie, aktualizowanie lub usuwanie synonymMaps

Po dodaniu nowego pola, istniejące indeksowane dokumenty otrzymują wartość null dla nowego pola. W przypadku późniejszego odświeżenia danych wartości z zewnętrznych danych źródłowych zastąpią wartości null dodane przez Wyszukiwanie poznawcze platformy Azure. Aby uzyskać więcej informacji na temat aktualizowania zawartości indeksu, zobacz [Dodawanie, aktualizowanie lub usuwanie dokumentów](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Jak ponownie skompilować indeks

Podczas tworzenia schematu indeksu często zmienia się. Można to zaplanować przez utworzenie indeksów, które można szybko usunąć, odtworzyć i ponownie załadować przy użyciu małego reprezentatywnego zestawu danych.

W przypadku aplikacji znajdujących się już w środowisku produkcyjnym zalecamy utworzenie nowego indeksu, który jest uruchamiany obok istniejącego indeksu, aby uniknąć przestojów zapytań. Kod aplikacji zapewnia przekierowanie do nowego indeksu.

Indeksowanie nie działa w tle, a usługa będzie zrównoważyć dodatkowe indeksowanie dla bieżących zapytań. Podczas indeksowania można [monitorować żądania zapytań](search-monitor-queries.md) w portalu, aby upewnić się, że zapytania kończą się w odpowiednim czasie.

1. Ustal, czy ponowna kompilacja jest wymagana. W przypadku dodawania pól lub zmieniania części indeksu, który nie jest powiązany z polami, można po prostu [zaktualizować definicję](/rest/api/searchservice/update-index) bez usuwania, ponownego tworzenia i całkowitego ładowania.

1. [Pobierz definicję indeksu](/rest/api/searchservice/get-index) , jeśli będzie potrzebna do użycia w przyszłości.

1. [Porzuć istniejący indeks](/rest/api/searchservice/delete-index), zakładając, że nie są uruchomione nowe i stare indeksy obok siebie. 

   Wszystkie zapytania ukierunkowane na ten indeks zostaną natychmiast usunięte. Należy pamiętać, że usunięcie indeksu jest nieodwracalne, co niszczy magazyn fizyczny dla kolekcji pól i innych konstrukcji. Wstrzymaj, aby myśleć o implikacjach przed jego usunięciem. 

1. [Utwórz poprawiony indeks](/rest/api/searchservice/create-index), gdzie treść żądania zawiera zmienione lub zmodyfikowane definicje pól.

1. [Załaduj indeks z dokumentami](/rest/api/searchservice/addupdate-or-delete-documents) ze źródła zewnętrznego.

Podczas tworzenia indeksu magazyn fizyczny jest przypisywany dla każdego pola w schemacie indeksu z odwróconym indeksem utworzonym dla każdego pola z możliwością wyszukiwania. Pola, które nie są możliwe do wyszukiwania, mogą być używane w filtrach lub wyrażeniach, ale nie mają odwróconych indeksów i nie są pełnotekstowe ani rozmyte. W przypadku ponownego kompilowania indeksu te odwrócone indeksy są usuwane i ponownie tworzone na podstawie pożądanego schematu indeksu.

Po załadowaniu indeksu, odwrócony indeks każdego pola jest wypełniany wszystkimi unikatowymi wyrazami tokenów z każdego dokumentu, z mapą odpowiadającymi identyfikatorami dokumentów. Na przykład podczas indeksowania zestawu danych hoteli, odwrócony Indeks utworzony dla pola miasto może zawierać terminy dla Seattle, Portland i tak dalej. Dokumenty, które zawierają Seattle lub Portland w polu miasto, będą miały swój identyfikator dokumentu na liście wraz z terminem. Na każdej operacji [dodawania, aktualizowania lub usuwania](/rest/api/searchservice/addupdate-or-delete-documents) zostanie odpowiednio zaktualizowana lista warunki i identyfikator dokumentu.

> [!NOTE]
> Jeśli masz rygorystyczne wymagania umowy SLA, możesz rozważyć zainicjowanie obsługi nowej usługi przeznaczonej dla tej pracy, z uwzględnieniem tworzenia i indeksowania w pełnej izolacji z poziomu indeksu produkcyjnego. Oddzielna usługa jest uruchamiana na własnym sprzęcie, eliminując wszelką możliwość rywalizacji o zasoby. Po zakończeniu opracowywania należy pozostawić nowy indeks, przekierować zapytania do nowego punktu końcowego i indeksu lub uruchomić gotowy kod, aby opublikować poprawiony indeks w oryginalnej usłudze Azure Wyszukiwanie poznawcze. Obecnie nie ma mechanizmu przechodzenia gotowego do użycia indeksu do innej usługi.

## <a name="check-for-updates"></a>Sprawdź aktualizacje

Możesz rozpocząć wykonywanie zapytań względem indeksu zaraz po załadowaniu pierwszego dokumentu. Jeśli znasz identyfikator dokumentu, [interfejs API REST dokumentu wyszukiwania](/rest/api/searchservice/lookup-document) zwraca określony dokument. W celu szerszego testowania należy zaczekać, aż indeks zostanie całkowicie załadowany, a następnie użyć zapytań w celu sprawdzenia kontekstu, który powinien być widoczny.

Możesz użyć [Eksploratora wyszukiwania](search-explorer.md) lub narzędzia do testowania sieci Web, takiego jak [poster](search-get-started-rest.md) lub [Visual Studio Code](search-get-started-vs-code.md) , aby sprawdzić dostępność zaktualizowanej zawartości.

Jeśli dodano lub zmieniono nazwę pola, użyj [$SELECT](search-query-odata-select.md) , aby zwrócić to pole: `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Zobacz też

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeksowanie dużych zestawów danych na dużą skalę](search-howto-large-index.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Azure SQL Database indeksator](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczenia w usłudze Azure Wyszukiwanie poznawcze](search-security-overview.md)