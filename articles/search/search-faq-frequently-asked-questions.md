---
title: Często zadawane pytania
titleSuffix: Azure Cognitive Search
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Microsoft Azure Wyszukiwanie poznawcze, hostowanej usługi wyszukiwania w chmurze w Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 9d6acdcf9487b2d1a5964d4ec686cd23666275b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923096"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Wyszukiwanie poznawcze platformy Azure — często zadawane pytania

 Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z usługą Azure Wyszukiwanie poznawcze.

## <a name="platform"></a>Platforma

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Jak platforma Azure Wyszukiwanie poznawcze różni się od wyszukiwania pełnotekstowego w systemie DBMS?

Usługa Azure Wyszukiwanie poznawcze obsługuje wiele źródeł danych, [analizę językową dla wielu języków](/rest/api/searchservice/language-support), [niestandardową analizę dla interesujących i nietypowych danych wejściowych](/rest/api/searchservice/custom-analyzers-in-azure-search), wyszukiwania rangi przy użyciu [profilów oceniania](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)i funkcji środowiska użytkownika, takich jak typeahead, wyróżnianie trafień i nawigacja aspektów. Zawiera również inne funkcje, takie jak synonimy i rozbudowana składnia zapytań, ale zazwyczaj nie odróżniają funkcji.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Czy mogę wstrzymać usługę Azure Wyszukiwanie poznawcze i zatrzymać rozliczanie?

Nie można wstrzymać usługi. Zasoby obliczeniowe i magazynowe są przypisywane do wyłącznego użytku podczas tworzenia usługi. Nie jest możliwe udostępnianie i odzyskiwanie tych zasobów na żądanie.

## <a name="indexing-operations"></a>Operacje indeksowania

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Przenoszenie, wykonywanie kopii zapasowych i przywracanie indeksów lub migawek indeksów?

Podczas fazy tworzenia warto przenieść indeks między usługami wyszukiwania. Na przykład możesz użyć warstwy cenowej podstawowa lub bezpłatna do opracowania indeksu, a następnie przenieść ją do warstwy Standardowa lub wyższa do użycia w środowisku produkcyjnym. 

Można również utworzyć kopię zapasową migawki indeksu w plikach, których można użyć do późniejszego przywrócenia. 

Wszystkie te elementy można wykonać za pomocą przykładowego kodu **index-Backup-Restore** w tym [repozytorium przykładowym platformy Azure wyszukiwanie poznawcze .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

W dowolnym momencie możesz również [uzyskać definicję indeksu](/rest/api/searchservice/get-index) przy użyciu interfejsu API REST usługi Azure wyszukiwanie poznawcze.

W Azure Portal obecnie nie ma wbudowanej funkcji wyodrębniania indeksów, migawek ani przywracania kopii zapasowych. Rozważamy jednak Dodawanie funkcji tworzenia kopii zapasowych i przywracania w przyszłej wersji. Jeśli chcesz pokazać pomoc techniczną dotyczącą tej funkcji, dokonaj głosowania na [głos użytkownika](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Czy można przywrócić indeks lub usługę po jej usunięciu?

Nie, jeśli usuniesz indeks Wyszukiwanie poznawcze lub usługę platformy Azure, nie będzie można jej odzyskać. Po usunięciu usługi Azure Wyszukiwanie poznawcze wszystkie indeksy w usłudze są trwale usuwane. Jeśli usuniesz grupę zasobów platformy Azure zawierającą co najmniej jedną usługę Wyszukiwanie poznawcze platformy Azure, wszystkie usługi zostaną trwale usunięte.  

Ponowne tworzenie zasobów, takich jak indeksy, indeksatory, źródła danych i umiejętności, wymaga ponownego utworzenia ich z kodu. 

Aby odtworzyć indeks, należy ponownie zindeksować dane ze źródeł zewnętrznych. Z tego powodu zaleca się zachowywanie kopii głównej lub kopii zapasowej oryginalnych danych w innym magazynie danych, takim jak Azure SQL Database lub Cosmos DB.

Alternatywnie możesz użyć przykładowego kodu w ramach **przywracania z użyciem indeksu kopii zapasowej** w ramach tego [przykładowego repozytorium platformy Azure wyszukiwanie poznawcze .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) , aby utworzyć kopię zapasową definicji indeksu i migawki indeksu do serii plików JSON. Później można użyć narzędzia i plików, aby przywrócić indeks, w razie potrzeby.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Czy można indeksować z replik SQL Database (dotyczy [Azure SQL Database indeksatorów](./search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md))

Podczas kompilowania indeksu od podstaw nie ma żadnych ograniczeń dotyczących używania replik podstawowych lub pomocniczych jako źródła danych. Jednak odświeżanie indeksu przy użyciu aktualizacji przyrostowych (na podstawie zmienionych rekordów) wymaga repliki podstawowej. To wymaganie pochodzi z SQL Database, co gwarantuje śledzenie zmian tylko w replikach podstawowych. Jeśli spróbujesz użyć replik pomocniczych dla obciążenia odświeżania indeksu, nie ma gwarancji, że uzyskasz wszystkie dane.

## <a name="search-operations"></a>Operacje wyszukiwania

### <a name="can-i-search-across-multiple-indexes"></a>Czy mogę przeszukiwać wiele indeksów?

Nie. Ta operacja nie jest obsługiwana. Wyszukiwanie jest zawsze ograniczone do jednego indeksu.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Czy mogę ograniczyć dostęp do indeksu wyszukiwania przez tożsamość użytkownika?

[Filtry zabezpieczeń](./search-security-trimming-for-azure-search.md) można zaimplementować z `search.in()` filtrem. Filtr jest dobrze złożony z [usług zarządzania tożsamościami, takich jak Azure Active Directory (AAD)](./search-security-trimming-for-azure-search-with-aad.md) , aby przyciąć wyniki wyszukiwania na podstawie zdefiniowanego członkostwa w grupie użytkowników.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Dlaczego nie ma żadnych dopasowań dotyczących poprawnych warunków?

Najpopularniejszym przypadkiem nie jest znajomość, że każdy typ zapytania obsługuje różne zachowania wyszukiwania i poziomy analiz językowych. Wyszukiwanie pełnotekstowe, które jest głównym obciążeniem, obejmuje fazę analizy języka, która przerywa warunki do formularzy głównych. Ten aspekt analizy zapytania rzutuje szerszy dostęp do sieci, ponieważ termin tokenów pasuje do większej liczby wariantów.

Zapytania o symbole wieloznaczne, rozmyte i wyrażenia regularnego nie są jednak analizowane jak regularne zapytania warunkowe lub zwrotne i mogą prowadzić do słabego odwołania, jeśli zapytanie nie jest zgodne z przeanalizowanym formularzem wyrazu w indeksie wyszukiwania. Aby uzyskać więcej informacji na temat analizy i analizy zapytań, zobacz temat [Architektura zapytań](./search-lucene-query-architecture.md).

### <a name="my-wildcard-searches-are-slow"></a>Wyszukiwanie przy użyciu symboli wieloznacznych jest powolne.

Większość kwerend wyszukiwania wieloznacznego, takich jak prefiks, rozmyte i wyrażenie regularne, są ponownie zapisywane wewnętrznie przy użyciu pasujących terminów w indeksie wyszukiwania. To dodatkowe przetwarzanie skanowania indeksu wyszukiwania powoduje dodanie opóźnienia. Dalsze, szerokie zapytania wyszukiwania, takie jak `a*` na przykład, które mogą być ponownie zapisywane z wieloma terminami, może być bardzo powolne. W przypadku przeszukiwanych symboli wieloznacznych należy rozważyć zdefiniowanie [analizatora niestandardowego](/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Dlaczego wyszukiwanie określa stałą lub równą ocenę 1,0 dla każdego trafienia?

Domyślnie wyniki wyszukiwania są oceniane na podstawie [Właściwości statystycznych zgodnych terminów](search-lucene-query-architecture.md#stage-4-scoring)i uporządkowane z wysokim poziomem do niskiego poziomu w zestawie wyników. Jednak niektóre typy zapytań (symbol wieloznaczny, prefiks, wyrażenie regularne) zawsze tworzą stały wynik do ogólnego wyniku dokumentu. To zachowanie jest celowe. Platforma Azure Wyszukiwanie poznawcze nakłada stałą wartość, aby umożliwić dołączenie dopasowań w wyniku rozszerzania zapytania, bez wywierania wpływu na klasyfikację.

Załóżmy na przykład, że dane wejściowe "przewodnik *" w wyszukiwaniu symboli wieloznacznych generują dopasowania w "przewodnikach", "Tourettes" i "Tourmaline". Mając na względzie charakter tych wyników, nie ma możliwości racjonalnego wyznaczenia, które warunki są bardziej cenne niż inne. Z tego powodu ignorujemy okresowe częstotliwości, gdy ocenia wyniki w kwerendach typów symboli wieloznacznych, prefiksów i wyrażeń regularnych. Wyniki wyszukiwania oparte na danych wejściowych są przydzielone stałym wskaźnikiem, aby uniknąć odliczania się do potencjalnie nieoczekiwanych dopasowań.

## <a name="skillset-operations"></a>Operacje zestawu umiejętności

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>Czy istnieją jakieś wskazówki i wskazówki pozwalające zmniejszyć opłaty za usługi poznawcze?

Można zrozumieć, że nie chcesz wykonywać wbudowanych umiejętności ani umiejętności niestandardowych więcej niż jest to absolutnie konieczne, zwłaszcza jeśli chodzi o miliony dokumentów do przetworzenia. Z tego względu dodaliśmy funkcje "wzbogacanie przyrostowe" do zestawu umiejętności wykonywania. W zasadzie można podać lokalizację pamięci podręcznej (parametry połączenia usługi BLOB Storage), która będzie używana do przechowywania danych wyjściowych z "pośredni" czynności wzbogacania.  Pozwala to na inteligentny potok wzbogacania i stosowanie tylko wzbogacania, które są niezbędne do zmodyfikowania zestawu umiejętności. W ten sposób będzie można także zaoszczędzić czas indeksowania, ponieważ potok będzie bardziej wydajny.

Dowiedz się więcej na temat [wzbogacania przyrostowego](cognitive-search-incremental-indexing-conceptual.md)

## <a name="design-patterns"></a>Wzorce projektowe

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Jaki jest najlepszy sposób implementacji zlokalizowanego wyszukiwania?

Większość klientów wybiera dedykowane pola w kolekcji, gdy ma ona obsługiwać różne ustawienia regionalne (języki) w tym samym indeksie. Pola specyficzne dla ustawień regionalnych umożliwiają przypisanie odpowiedniej analizatora. Na przykład przypisanie analizatora francuskiego firmy Microsoft do pola zawierającego ciągi francuski. Upraszcza to również filtrowanie. Jeśli wiesz, że zapytanie jest inicjowane na stronie fr-fr, możesz ograniczyć wyniki wyszukiwania do tego pola. Lub Utwórz [profil oceniania](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , aby nadać polu bardziej względną wagę. Usługa Azure Wyszukiwanie poznawcze obsługuje ponad [50 analizatorów języka](./search-language-support.md) w celu wyboru.

## <a name="next-steps"></a>Następne kroki

Czy masz pytanie dotyczące brakującej funkcji lub funkcji? Zażądaj funkcji w [witrynie sieci Web głosu użytkownika](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Zobacz też

 [StackOverflow: Azure Wyszukiwanie poznawcze](https://stackoverflow.com/questions/tagged/azure-search)   
 [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)  
 [Co to jest platforma Azure Wyszukiwanie poznawcze?](search-what-is-azure-search.md)