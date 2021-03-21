---
title: Indeksowanie dużych zestawów danych za pomocą wbudowanych indeksatorów
titleSuffix: Azure Cognitive Search
description: Strategie dotyczące dużych indeksowania danych lub indeksowania intensywnie korzystających z obliczeń przy użyciu trybu wsadowego, odzyskania i technik dla zaplanowanych, równoległych i rozproszonych indeksowania.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: b4f54aff78526ba52e56ed9f4cf1feddf40fa69b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358396"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Jak indeksować duże zestawy danych w usłudze Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze obsługuje [dwa podstawowe podejścia](search-what-is-data-import.md) do importowania danych do indeksu wyszukiwania: *wypchnięcie* danych do indeksu programowo lub wskazanie [indeksatora wyszukiwanie poznawcze platformy Azure](search-indexer-overview.md) w obsługiwanym źródle danych w celu *ściągnięcia* danych.

W miarę wzrostu lub przetwarzania woluminów danych, może się okazać, że proste lub domyślne strategie indeksowania nie są już praktyczne. W przypadku usługi Azure Wyszukiwanie poznawcze istnieje kilka podejścia do obsługi większych zestawów danych, od sposobu tworzenia struktury żądania przekazywania danych przy użyciu indeksatora specyficznego dla zasobów dla zaplanowanych i rozproszonych obciążeń.

Te same techniki mają zastosowanie również do długotrwałych procesów. W szczególności kroki opisane w temacie [Parallel Indexing](#parallel-indexing) są przydatne w przypadku obliczeń intensywnie wykorzystujących indeksowanie, takie jak analiza obrazu lub przetwarzanie języka naturalnego w [potoku wzbogacenia AI](cognitive-search-concept-intro.md).

W poniższych sekcjach opisano techniki indeksowania dużych ilości danych przy użyciu interfejsu API wypychania i indeksatorów.

## <a name="use-the-push-api"></a>Korzystanie z interfejsu API wypychania

Podczas wypychania danych do indeksu przy użyciu [interfejsu API REST dodawania dokumentów](/rest/api/searchservice/addupdate-or-delete-documents) lub [metody IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments)istnieje kilka kluczowych zagadnień, które wpływają na szybkość indeksowania. Te czynniki opisano w poniższej sekcji, a zakres od ustawiania pojemności usługi na optymalizacje kodu.

Aby uzyskać więcej informacji i przykładów kodu, które ilustrują indeksowanie modelu wypychania, zobacz [Samouczek: Optymalizowanie szybkości indeksowania](tutorial-optimize-indexing-push-api.md).

### <a name="capacity-of-your-service"></a>Pojemność usługi

Pierwszym krokiem jest zapoznanie się z charakterystyką i [limitami](search-limits-quotas-capacity.md) warstwy, w której zainicjowano obsługę usługi. Jednym z kluczowych różnic między warstwami cenowymi jest rozmiar i szybkość partycji, która ma bezpośredni wpływ na szybkość indeksowania. Jeśli usługa wyszukiwania została zainicjowana w warstwie, która jest niewystarczająca do obciążania, uaktualnienie do nowej warstwy może być najłatwiejszym i najbardziej efektywnym rozwiązaniem w celu zwiększenia przepływności indeksowania.

Po spełnieniu warstwy, następnym krokiem może być zwiększenie liczby partycji. Alokację partycji można zmienić w dół po początkowym indeksie, aby zmniejszyć całkowity koszt działania usługi.

> [!NOTE]
> Dodanie dodatkowych replik może również zwiększyć szybkość indeksowania, ale nie jest to gwarantowane. Z drugiej strony, dodatkowe repliki spowodują zwiększenie woluminu zapytania, który może obsłużyć usługa wyszukiwania. Repliki są również kluczowym składnikiem do uzyskiwania [umowy SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
>
> Przed dodaniem partycji/replik lub uaktualnieniem do wyższego poziomu należy wziąć pod uwagę koszt pieniężny i czas alokacji. Dodawanie partycji może znacząco zwiększyć szybkość indeksowania, ale Dodawanie/usuwanie ich może zająć od 15 minut do kilku godzin. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [dostosowywania pojemności](search-capacity-planning.md).
>

### <a name="review-index-schema"></a>Przegląd schematu indeksu

Schemat indeksu odgrywa ważną rolę w indeksowaniu danych. Im więcej pól i ustawionych przez siebie właściwości (takich jak *Wyszukiwanie*, tworzenie i *filtrowanie* *),* przyczyniają się do zwiększenia czasu indeksowania. Ogólnie rzecz biorąc należy tworzyć i określać pola, które są potrzebne w indeksie wyszukiwania.

> [!NOTE]
> Aby zachować rozmiar dokumentu w dół, Unikaj dodawania danych innych niż Queryable do indeksu. Obrazy i inne dane binarne nie są bezpośrednio przeszukiwane i nie powinny być przechowywane w indeksie. Aby zintegrować dane niequeryablene z wynikami wyszukiwania, należy zdefiniować pole, które nie jest możliwe do przeszukania, które przechowuje odwołanie do tego zasobu.

### <a name="check-the-batch-size"></a>Sprawdzanie rozmiaru partii

Jednym z najprostszych mechanizmów indeksowania większego zestawu danych jest przesyłanie wielu dokumentów lub rekordów w jednym żądaniu. Tak długo, jak cały ładunek przekracza 16 MB, żądanie może obsłużyć do 1000 dokumentów w operacji ładowania zbiorczego. Te limity mają zastosowanie w przypadku korzystania z [interfejsu API REST dodawania dokumentów](/rest/api/searchservice/addupdate-or-delete-documents) lub [metody IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) w zestawie .NET SDK. Dla obu interfejsów API w treści każdego żądania należy spakować 1000 dokumentów.

Użycie partii do indeksowania dokumentów znacznie poprawi wydajność indeksowania. Określenie optymalnego rozmiaru partii danych jest kluczowym elementem optymalizacji szybkości indeksowania. Dwa podstawowe czynniki wpływające na optymalny rozmiar partii to:

+ Schemat indeksu
+ Rozmiar danych

Ze względu na to, że optymalny rozmiar wsadu zależy od indeksu i danych, najlepszym rozwiązaniem jest przetestowanie różnych rozmiarów partii, aby określić, jakie wyniki mają największą szybkość indeksowania w danym scenariuszu. Ten [samouczek](tutorial-optimize-indexing-push-api.md) zawiera przykładowy kod służący do testowania rozmiarów partii przy użyciu zestawu .NET SDK. 

### <a name="number-of-threadsworkers"></a>Liczba wątków/procesów roboczych

Aby w pełni wykorzystać szybkość indeksowania Wyszukiwanie poznawcze platformy Azure, prawdopodobnie trzeba będzie użyć wielu wątków, aby wysyłać żądania indeksowania wsadowego współbieżnie do usługi.  

Optymalna liczba wątków jest określana na podstawie:

+ Warstwa usługi wyszukiwania
+ Liczba partycji
+ Rozmiar partii
+ Schemat indeksu

Możesz zmodyfikować ten przykład i przetestować z różnymi liczbami wątków, aby określić optymalną liczbę wątków dla danego scenariusza. Jednak o ile wiele wątków jest uruchomionych współbieżnie, powinno być możliwe korzystanie z większości korzyści z wydajności. 

> [!NOTE]
> W miarę zwiększania warstwy usługi wyszukiwania lub zwiększania partycji należy również zwiększyć liczbę współbieżnych wątków.

Podczas narastania żądań, które powodują przeszukanie usługi wyszukiwania, mogą wystąpić [kody stanu HTTP](/rest/api/searchservice/http-status-codes) wskazujące, że żądanie nie zostało w pełni zakończone. Podczas indeksowania dwa typowe kody stanu HTTP to:

+ **503 Usługa niedostępna** — ten błąd oznacza, że system jest mocno obciążony i nie można w tej chwili przetworzyć Twojego żądania.
+ **207 o wielu stanach** — ten błąd oznacza, że niektóre dokumenty zostały wykonane pomyślnie, ale co najmniej jeden z nich nie powiódł się.

### <a name="retry-strategy"></a>Strategia ponawiania prób

Jeśli wystąpi awaria, żądania powinny być ponawiane przy użyciu [strategii wycofywaniaego ponawiania prób](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Zestaw .NET SDK platformy Azure Wyszukiwanie poznawcze automatycznie ponawia próbę 503s i inne Nieudane żądania, ale musisz zaimplementować własną logikę, aby ponowić próbę 207s. Narzędzia typu open source, takie jak [Polly](https://github.com/App-vNext/Polly) , mogą również służyć do implementowania strategii ponawiania prób.

### <a name="network-data-transfer-speeds"></a>Szybkość transferu danych w sieci

Szybkość transferu danych w sieci może być czynnikiem ograniczającym podczas indeksowania danych. Indeksowanie danych z poziomu środowiska platformy Azure to prosty sposób przyspieszenia indeksowania.

## <a name="use-indexers-pull-api"></a>Używanie indeksatorów (interfejs API ściągania)

[Indeksatory](search-indexer-overview.md) służą do przeszukiwania obsługiwanych źródeł danych platformy Azure w celu wyszukania zawartości. Chociaż nie jest to przeznaczone do indeksowania na dużą skalę, kilka funkcji indeksatora jest szczególnie przydatne do obsługi większych zestawów danych:

+ Harmonogramy umożliwiają dedystrybuowanie indeksowania w regularnych odstępach czasu, aby można było je rozłożyć w miarę upływu czasu.
+ Zaplanowane indeksowanie można wznowić w ostatnim znanym punkcie zatrzymywania. Jeśli źródło danych nie jest w pełni przeszukiwane w oknie 24-godzinnym, indeksator wznowi indeksowanie dziennie dwa w miejscu, w którym jest pozostawione.
+ Partycjonowanie danych w mniejszych pojedynczych źródłach danych umożliwia przetwarzanie równoległe. Możesz podzielić dane źródłowe na mniejsze składniki, na przykład na wiele kontenerów w usłudze Azure Blob Storage, a następnie utworzyć odpowiadające im wiele [obiektów źródła danych](/rest/api/searchservice/create-data-source) na platformie Azure wyszukiwanie poznawcze, które mogą być indeksowane równolegle.

> [!NOTE]
> Indeksatory są specyficzne dla źródła danych, więc użycie metody indeksatora jest możliwe tylko dla wybranych źródeł danych na platformie Azure: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [BLOB Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="check-the-batchsize-argument-on-create-indexer"></a>Sprawdź argument batchSize przy tworzeniu indeksatora

Podobnie jak w przypadku interfejsu API wypychania, Indeksatory umożliwiają skonfigurowanie liczby elementów na partię. W przypadku indeksatorów opartych na [interfejsie API Rest tworzenia indeksatora](/rest/api/searchservice/Create-Indexer)można ustawić `batchSize` argument, aby dostosować to ustawienie, aby lepiej odpowiadało charakterystyce danych. 

Domyślne rozmiary partii to specyficzne dla źródła danych. Azure SQL Database i Azure Cosmos DB mają domyślny rozmiar wsadu 1000. W przeciwieństwie do indeksowania obiektów blob platformy Azure ustawia rozmiar wsadu w 10 dokumentach w celu rozpoznania większego średniego rozmiaru dokumentu. 

### <a name="scheduled-indexing"></a>Zaplanowane indeksowanie

Planowanie indeksatora to ważny mechanizm przetwarzania dużych zestawów danych, a także długotrwałe procesy, takie jak analiza obrazu w potoku wyszukiwania poznawczego. Przetwarzanie indeksatora działa w oknie 24-godzinnym. Jeśli przetwarzanie zakończy się niepowodzeniem w ciągu 24 godzin, zachowania funkcji indeksatora dotyczące planowania mogą współdziałać z zaletą. 

Zaplanowana funkcja indeksowania jest uruchamiana w określonych odstępach czasu, a zadanie zwykle kończy się przed wznowieniem przy następnym zaplanowanym interwale. Jeśli jednak przetwarzanie nie zakończy się w przedziale czasu, indeksator zostanie zatrzymany (ponieważ został uruchomiony poza czasem). Podczas następnego interwału przetwarzanie zostanie wznowione w miejscu, w którym zostało przerwane, a system śledzi miejsce wystąpienia. 

W praktyce w przypadku obciążeń indeksu obejmujących kilka dni można umieścić indeksator w harmonogramie 24-godzinnym. Gdy indeksowanie zostanie wznowione dla następnego 24-godzinnego cyklu, zostanie ono ponownie uruchomione od ostatniego znanego dobrego dokumentu. Dzięki temu indeksator może współdziałać w sposób za pośrednictwem zaległości dokumentu w ciągu kilku dni do czasu przetworzenia wszystkich nieprzetworzonych dokumentów. Aby uzyskać więcej informacji na temat ogólnych ustawień harmonogramów, zobacz [Tworzenie interfejsu API REST indeksatora](/rest/api/searchservice/Create-Indexer) lub zapoznaj [się z tematem planowanie indeksatorów dla usługi Azure wyszukiwanie poznawcze](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indeksowanie równoległe

Strategia indeksowań równoległych opiera się na indeksowaniu wielu źródeł danych w artykułem, gdzie każda definicja źródła danych określa podzestaw danych. 

W przypadku nierutynowych obliczeniowych wymagań dotyczących indeksowania — takich jak OCR na skanowanych dokumentach w potoku wyszukiwania poznawczego, analiza obrazu lub przetwarzanie języka naturalnego — równoległa strategia indeksowania jest często właściwym podejściem do ukończenia długotrwałego procesu w najkrótszym czasie. Jeśli możesz wyeliminować lub zmniejszyć żądania zapytań, indeksowanie równoległe w usłudze, która nie jest równocześnie obsługiwana, jest najlepszą strategią do pracy w dużej części zawartości z niską szybkością przetwarzania. 

Przetwarzanie równoległe obejmuje następujące elementy:

+ Poddzielenie danych źródłowych między wieloma kontenerami lub wieloma folderami wirtualnymi w tym samym kontenerze. 
+ Zamapuj każdy zestaw danych minipasek na jego własne [Źródło danych](/rest/api/searchservice/create-data-source), sparowany z jego własnym [indeksatorem](/rest/api/searchservice/create-indexer).
+ W przypadku wyszukiwania poznawczego odwołują się do tego samego [zestawu umiejętności](/rest/api/searchservice/create-skillset) w każdej definicji indeksatora.
+ Zapisz w tym samym docelowym indeksie wyszukiwania. 
+ Zaplanuj, aby wszystkie indeksatory były uruchamiane w tym samym czasie.

> [!NOTE]
> W usłudze Azure Wyszukiwanie poznawcze nie można przypisywać pojedynczych replik ani partycji do przetwarzania indeksowania lub zapytań. System określa sposób użycia zasobów. Aby zrozumieć wpływ na wydajność zapytań, możesz spróbować wykonać indeksowanie równoległe w środowisku testowym przed przeprowadzeniem go do produkcji.  

### <a name="how-to-configure-parallel-indexing"></a>Jak skonfigurować indeksowanie równoległe

W przypadku indeksatorów pojemność przetwarzania jest luźno oparta na jednym podsystemie indeksatora dla każdej jednostki usługi (SU) używanej przez usługę wyszukiwania. Wiele współbieżnych indeksatorów jest dostępnych w ramach usług Azure Wyszukiwanie poznawcze Services, które są obsługiwane w warstwach Podstawowa lub standardowa mających co najmniej dwie repliki. 

1. Na [Azure Portal](https://portal.azure.com)na stronie **Przegląd** pulpitu nawigacyjnego usługi wyszukiwania Sprawdź **warstwę cenową** , aby potwierdzić, że może ona obsługiwać równoległe indeksowanie. Warstwy Podstawowa i Standardowa oferują wiele replik.

2. Można uruchamiać dowolną liczbę indeksatorów równolegle jako liczbę jednostek wyszukiwania w usłudze. W obszarze **Ustawienia**  >  **skalowanie** [Zwiększ liczbę replik](search-capacity-planning.md) lub partycji do przetwarzania równoległego: jedną dodatkową replikę lub partycję dla każdego obciążenia indeksatora. Pozostaw wystarczającą liczbę dla istniejącego woluminu zapytania. Obniżanie obciążenia zapytań dla indeksowania nie jest dobrym kompromisem.

3. Dystrybuuj dane do wielu kontenerów na poziomie, do którego mogą uzyskać dostęp indeksatory platformy Azure Wyszukiwanie poznawcze. Może to być wiele tabel w Azure SQL Database, wiele kontenerów w usłudze Azure Blob Storage lub wiele kolekcji. Zdefiniuj jeden obiekt źródła danych dla każdej tabeli lub kontenera.

4. Utwórz i Zaplanuj uruchamianie wielu indeksatorów równolegle:

   + Załóżmy, że usługa ma sześć replik. Skonfiguruj sześć indeksatorów, z których każdy jest mapowany do źródła danych zawierającego jeden szósty zestaw danych dla 6-kierunkowego podziału całego zestawu danych. 

   + Wskaż każdy indeksator do tego samego indeksu. W przypadku obciążeń związanych z wyszukiwaniem poznawczym należy wskazać każdy indeksator do tego samego zestawu umiejętności.

   + W ramach każdej definicji indeksatora Zaplanuj ten sam wzorzec wykonywania w czasie wykonywania. Na przykład program `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` tworzy harmonogram na 2018-05-15 dla wszystkich indeksatorów uruchomionych w ciągu ośmiu godzin.

W zaplanowanym czasie wszystkie indeksatory rozpoczynają wykonywanie, ładowania danych, stosowania wzbogacania (Jeśli skonfigurowano potok wyszukiwania poznawczego) i zapisu w indeksie. Usługa Azure Wyszukiwanie poznawcze nie blokuje indeksów pod kątem aktualizacji. Współbieżne zapisy są zarządzane, a następnie ponów próbę, jeśli określony zapis nie powiedzie się przy pierwszej próbie.

> [!Note]
> Podczas zwiększania replik należy rozważyć zwiększenie liczby partycji, jeśli rozmiar indeksu jest rzutowany, aby znacząco zwiększyć. Partycje przechowują wycinki indeksowanej zawartości; im więcej partycji, tym mniejsza jest możliwość przechowywania wycinka każdego z nich.

## <a name="see-also"></a>Zobacz też

+ [Omówienie indeksatora](search-indexer-overview.md)
+ [Indeksowanie w portalu](search-import-data-portal.md)
+ [Azure SQL Database indeksator](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indeksator usługi Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indeksator usługi Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indeksator usługi Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpieczenia w usłudze Azure Wyszukiwanie poznawcze](search-security-overview.md)