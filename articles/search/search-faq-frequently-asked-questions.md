---
title: Często zadawane pytania
titleSuffix: Azure Cognitive Search
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące usługi Microsoft Azure Cognitive Search, usługi wyszukiwania hostowanego w chmurze na platformie Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 520699b81024de9491f34263f16872428ddbd487
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618041"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Usługa Azure Cognitive Search — często zadawane pytania (często zadawane pytania)

 Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z usługą Azure Cognitive Search.

## <a name="platform"></a>Platforma

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Czym różni się usługa Azure Cognitive Search od wyszukiwania pełnotekstowym w moim DBMS?

Usługa Azure Cognitive Search obsługuje wiele źródeł danych, [analizę językową wielu języków,](https://docs.microsoft.com/rest/api/searchservice/language-support) [niestandardową analizę interesujących i nietypowych danych,](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)formanty rankingu wyszukiwania za pomocą [profilów oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)oraz funkcje środowiska użytkownika, takie jak typo-czoło, wyróżnianie trafień i nawigacja fasetowana. Zawiera również inne funkcje, takie jak synonimy i składnia kwerendy rozszerzonej, ale zazwyczaj nie rozróżniają funkcji.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Czy mogę wstrzymać usługę Azure Cognitive Search i zatrzymać rozliczenia?

Nie można wstrzymać usługi. Zasoby obliczeniowe i magazynowe są przydzielane do wyłącznego użytku podczas tworzenia usługi. Nie jest możliwe, aby zwolnić i odzyskać te zasoby na żądanie.

## <a name="indexing-operations"></a>Operacje indeksowania

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Przenoszenie, tworzenie kopii zapasowych i przywracanie indeksów lub migawek indeksu?

W fazie rozwoju można przenieść indeks między usługami wyszukiwania. Na przykład można użyć podstawowej lub bezpłatnej warstwy cenowej do opracowania indeksu, a następnie chcesz przenieść go do warstwy standardowej lub wyższej do użytku w produkcji. 

Lub można wykonać kopię zapasową migawki indeksu do plików, które mogą być używane do przywrócenia go później. 

Wszystkie te czynności można wykonać za pomocą przykładowego kodu **przywracania kopii zapasowej indeksu** w tym [przykładowym repo usługi Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

Definicję [indeksu](https://docs.microsoft.com/rest/api/searchservice/get-index) można również uzyskać w dowolnym momencie przy użyciu interfejsu API REST usługi Azure Cognitive Search.

Obecnie nie ma wbudowanej funkcji wyodrębniania indeksu, migawki lub przywracania kopii zapasowej w witrynie Azure portal. Rozważamy jednak dodanie funkcji tworzenia kopii zapasowych i przywracania w przyszłej wersji. Jeśli chcesz okazać swoje poparcie dla tej funkcji, oddaj głos na [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Czy mogę przywrócić indeks lub usługę po jej usunięciu?

Nie, jeśli usuniesz indeks lub usługę Azure Cognitive Search, nie można go odzyskać. Po usunięciu usługi Azure Cognitive Search wszystkie indeksy w usłudze są trwale usuwane. Jeśli usuniesz grupę zasobów platformy Azure, która zawiera jedną lub więcej usług Azure Cognitive Search, wszystkie usługi zostaną trwale usunięte.  

Ponownetworzenie zasobów, takich jak indeksy, indeksatory, źródła danych i skillsets wymaga, aby odtworzyć je z kodu. 

Aby ponownie utworzyć indeks, należy ponownie indeksować dane ze źródeł zewnętrznych. Z tego powodu zaleca się zachowanie kopii wzorcowej lub kopii zapasowej oryginalnych danych w innym magazynie danych, takich jak usługa Azure SQL Database lub Cosmos DB.

Alternatywnie można użyć przykładowego kodu **przywracania kopii zapasowej indeksu** w tym [przykładowym repozytorium usługi Azure Cognitive Search .NET,](https://github.com/Azure-Samples/azure-search-dotnet-samples) aby wykonać kopię zapasową definicji indeksu i migawki indeksu w serii plików JSON. Później można użyć narzędzia i plików, aby przywrócić indeks, jeśli to konieczne.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Czy mogę indeksować z replik bazy danych SQL (dotyczy [indeksatorów usługi Azure SQL Database)](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)

Nie ma żadnych ograniczeń dotyczących używania replik podstawowych lub pomocniczych jako źródła danych podczas tworzenia indeksu od podstaw. Jednak odświeżanie indeksu z aktualizacjami przyrostowymi (na podstawie zmienionych rekordów) wymaga repliki podstawowej. To wymaganie pochodzi z bazy danych SQL, która gwarantuje śledzenie zmian tylko w replikach podstawowych. Jeśli spróbujesz użyć replik pomocniczych dla obciążenia odświeżania indeksu, nie ma żadnej gwarancji, że otrzymasz wszystkie dane.

## <a name="search-operations"></a>Operacje wyszukiwania

### <a name="can-i-search-across-multiple-indexes"></a>Czy mogę wyszukiwać w wielu indeksach?

Nie, ta operacja nie jest obsługiwana. Wyszukiwanie jest zawsze ograniczone do jednego indeksu.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Czy mogę ograniczyć dostęp do indeksu wyszukiwania według tożsamości użytkownika?

[Filtry zabezpieczeń](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) można `search.in()` zaimplementować za pomocą filtru. Filtr dobrze komponuje się z [usługami zarządzania tożsamościami, takimi jak usługa Azure Active Directory(AAD),](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) aby przyciąć wyniki wyszukiwania na podstawie zdefiniowanego członkostwa w grupie użytkowników.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Dlaczego mecze zerowe są zgodne z warunkami, o których wiem, że są ważne?

Najczęstszym przypadkiem jest nie wiedząc, że każdy typ zapytania obsługuje różne zachowania wyszukiwania i poziomy analiz językowych. Wyszukiwanie pełnotekstowe, które jest głównym obciążeniem, obejmuje fazę analizy języka, która rozkłada terminy do formularzy głównych. Ten aspekt analizowania zapytań rzuca szerszą sieć nad możliwymi dopasowaniami, ponieważ termin tokenized pasuje do większej liczby wariantów.

Wildcard, rozmyte i wyrażenia regularne, jednak nie są analizowane jak zwykłe zapytania terminów lub fraz i może prowadzić do słabego odwołania, jeśli kwerenda nie pasuje do analizowanego formularza wyrazu w indeksie wyszukiwania. Aby uzyskać więcej informacji na temat analizowania i analizy zapytań, zobacz [architektura kwerend](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Moje wyszukiwania symboli wieloznacznych są powolne.

Większość zapytań z symbolami wieloznacznymi, takich jak prefiks, rozmyty i wyrażenia regularne, są przepisywane wewnętrznie z pasującymi terminami w indeksie wyszukiwania. To dodatkowe przetwarzanie skanowania indeksu wyszukiwania dodaje do opóźnienia. Ponadto szerokie zapytania wyszukiwania, `a*` takie jak na przykład, które mogą być przepisane z wieloma terminami, mogą być bardzo powolne. W przypadku wykonywania wyszukiwania symboli wieloznacznych należy rozważyć zdefiniowanie [niestandardowego analizatora](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Dlaczego ranking wyszukiwania jest stały lub równy wynik 1.0 za każde trafienie?

Domyślnie wyniki wyszukiwania są oceniane na podstawie [właściwości statystycznych pasujących terminów](search-lucene-query-architecture.md#stage-4-scoring)i uporządkowane od najwyższego do najniższego w zestawie wyników. Jednak niektóre typy zapytań (symbol wieloznaczny, prefiks, wyrażenie regularne) zawsze przyczyniają się do stałego wyniku do ogólnego wyniku dokumentu. To zachowanie jest celowe. Usługa Azure Cognitive Search nakłada stały wynik, aby umożliwić dopasowania znalezione za pośrednictwem rozszerzenia zapytania, które mają zostać uwzględnione w wynikach, bez wpływu na ranking.

Załóżmy na przykład, że wejście "tour*" w wyszukiwaniu z symbolami wieloznacznymi powoduje, że mecze są "wycieczki", "tourettes" i "tourmaline". Biorąc pod uwagę charakter tych wyników, nie ma sposobu, aby racjonalnie wywnioskować, które terminy są cenniejsze niż inne. Z tego powodu ignorujemy częstotliwości terminów podczas oceniania wyników w kwerendach typów symboli wieloznacznych, prefiksów i wyrażenia regularnego. Wyniki wyszukiwania na podstawie częściowego wprowadzania danych są podane stały wynik, aby uniknąć stronniczości w kierunku potencjalnie nieoczekiwanych dopasowań.

## <a name="skillset-operations"></a>Operacje zestawów umiejętności

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>Czy są jakieś wskazówki lub wskazówki, aby zmniejszyć opłaty za usługi poznawcze po spożyciu?

Zrozumiałe jest, że nie chcesz wykonywać wbudowanych umiejętności lub umiejętności niestandardowych więcej niż jest to absolutnie konieczne, zwłaszcza jeśli masz do czynienia z milionami dokumentów do przetworzenia. Mając to na uwadze, dodaliśmy możliwości "przyrostowe wzbogacanie" do wykonywania skillset. W istocie można podać lokalizację pamięci podręcznej (ciąg połączenia magazynu obiektów blob), który będzie używany do przechowywania danych wyjściowych "pośrednich" kroków wzbogacania.  Dzięki temu potok wzbogacania być inteligentne i stosować tylko wzbogacenia, które są niezbędne podczas modyfikowania zestaw umiejętności. Pozwoli to oczywiście również zaoszczędzić czas indeksowania, ponieważ potok będzie bardziej wydajny.

Dowiedz się więcej o [wzbogaceniu przyrostowym](cognitive-search-incremental-indexing-conceptual.md)

## <a name="design-patterns"></a>Wzorce projektowe

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Jakie jest najlepsze podejście do wdrażania zlokalizowanych wyszukiwań?

Większość klientów wybiera dedykowane pola za pomocą kolekcji, jeśli chodzi o obsługę różnych ustawień regionalnych (języków) w tym samym indeksie. Pola specyficzne dla ustawień regionalnych umożliwiają przypisanie odpowiedniego analizatora. Na przykład przypisanie microsoft francuski analizator do pola zawierającego ciągi francuskie. Upraszcza również filtrowanie. Jeśli wiesz, że zapytanie jest inicjowane na stronie fr-fr, możesz ograniczyć wyniki wyszukiwania do tego pola. Możesz też utworzyć [profil punktacji,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) aby nadać pole większej wadze względnej. Usługa Azure Cognitive Search obsługuje ponad [50 analizatorów języka](https://docs.microsoft.com/azure/search/search-language-support) do wyboru.

## <a name="next-steps"></a>Następne kroki

Czy masz pytanie dotyczące brakującej funkcji lub funkcji? Poproś o tę funkcję na [stronie internetowej User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Zobacz też

 [StackOverflow: Wyszukiwarka poznawcza platformy Azure](https://stackoverflow.com/questions/tagged/azure-search)   
 [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)  
 [Co to jest wyszukiwanie poznawcze na platformie Azure?](search-what-is-azure-search.md)
