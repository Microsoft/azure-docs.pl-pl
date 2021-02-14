---
title: Skalowanie pod kątem wydajności
titleSuffix: Azure Cognitive Search
description: Poznaj techniki i najlepsze rozwiązania dotyczące dostrajania wydajności Wyszukiwanie poznawcze platformy Azure i konfigurowania optymalnej skali.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: b7c71524dc40f7eabd5ff86ee21c8197acfae1a3
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102295"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Skalowanie pod kątem wydajności na platformie Azure Wyszukiwanie poznawcze

W tym artykule opisano najlepsze rozwiązania dla zaawansowanych scenariuszy z zaawansowanymi wymaganiami dotyczącymi skalowalności i dostępności.

## <a name="start-with-baseline-numbers"></a>Rozpocznij od numerów linii bazowej

Przed przystąpieniem do większego nakładu prac związanych z wdrażaniem upewnij się, że wiesz, jak wygląda typowe obciążenie zapytania. Poniższe wskazówki mogą pomóc dotrzeć do numerów bazowych zapytań.

1. Wybierz oczekiwany czas oczekiwania (lub maksymalną ilość czasu), przez który należy wykonać typowe żądanie wyszukiwania.

1. Twórz i testuj rzeczywiste obciążenie usługi wyszukiwania za pomocą realistycznego zestawu danych, aby zmierzyć te stawki opóźnienia.

1. Zacznij od niskiej liczby zapytań na sekundę (zapytań), a następnie stopniowo Zwiększaj liczbę wykonywanych testów do momentu spadku opóźnienia zapytania poniżej wstępnie zdefiniowanego elementu docelowego. Jest to ważny test, który ułatwia planowanie skalowania w miarę zwiększania użycia aplikacji.

1. Gdy to możliwe, należy ponownie użyć połączeń HTTP. Jeśli używasz zestawu SDK platformy Azure Wyszukiwanie poznawcze .NET, oznacza to, że należy ponownie użyć wystąpienia lub wystąpienia [SearchClient](/dotnet/api/azure.search.documents.searchclient) , a jeśli korzystasz z interfejsu API REST, należy ponownie użyć pojedynczej HttpClient.

1. Różnej treści żądań zapytań, dzięki czemu wyszukiwanie odbywa się na różnych częściach indeksu. Odmiana jest ważna, ponieważ w przypadku ciągłego wykonywania tych samych żądań wyszukiwania buforowanie danych rozpocznie się, aby wydajność była lepsza niż w przypadku bardziej nieznaczącego zestawu zapytań.

1. Różnicowanie struktury żądań zapytań w celu uzyskania różnych typów zapytań. Nie każde zapytanie wyszukiwania wykonuje się na tym samym poziomie. Na przykład wyszukiwanie dokumentów lub sugestia wyszukiwania jest zwykle szybsze niż zapytanie o znaczną liczbę aspektów i filtrów. Kompozycja testowa powinna zawierać różne zapytania, w przybliżeniu tak samo jak w środowisku produkcyjnym.  

Podczas tworzenia tych obciążeń testowych należy wziąć pod uwagę pewne cechy Wyszukiwanie poznawcze platformy Azure:

+ Istnieje możliwość przeciążenia usługi przez wypychanie zbyt wielu zapytań wyszukiwania jednocześnie. W takim przypadku zobaczysz kody odpowiedzi HTTP 503. Aby uniknąć 503 podczas testowania, Zacznij od różnych zakresów żądań wyszukiwania, aby zobaczyć różnice w współczynniku opóźnień podczas dodawania kolejnych żądań wyszukiwania.

+ Usługa Azure Wyszukiwanie poznawcze nie uruchamia zadań indeksowania w tle. Jeśli usługa obsługuje współbieżne obciążenia zapytań i indeksowania, należy to uwzględnić, wprowadzając zadania indeksowania do testów zapytań lub przez Eksplorowanie opcji uruchamiania zadań indeksowania w godzinach szczytu.

> [!Tip]
> Możesz symulować realistyczne obciążenie zapytań za pomocą narzędzi do testowania obciążenia. Wypróbuj [testowanie obciążenia za pomocą usługi Azure DevOps](/azure/devops/test/load-test/get-started-simple-cloud-load-test) lub użyj jednego z tych [wariantów](/azure/devops/test/load-test/overview#alternatives).

## <a name="scale-for-high-query-volume"></a>Skalowanie dla dużej ilości zapytań

Usługa jest nakładana, gdy zapytania trwają zbyt długo lub gdy usługa zaczyna przerywać żądania. W takim przypadku można rozwiązać problem na jeden z dwóch sposobów:

+ **Dodaj repliki**  

  Każda replika to kopia danych, dzięki czemu usługa może równoważyć obciążenie żądaniami do wielu kopii.  Wszystkie Równoważenie obciążenia i replikacja danych są zarządzane przez usługę Azure Wyszukiwanie poznawcze i można w dowolnym momencie zmienić liczbę replik przyznanych dla usługi. Możesz przydzielić maksymalnie 12 replik w standardowej usłudze wyszukiwania i 3 replikach w podstawowej usłudze wyszukiwania. Repliki można dostosować za pomocą [Azure Portal](search-create-service-portal.md) lub [programu PowerShell](search-manage-powershell.md).

+ **Tworzenie nowej usługi w wyższej warstwie**  

  Usługa Azure Wyszukiwanie poznawcze obejmuje [wiele warstw](https://azure.microsoft.com/pricing/details/search/) , a każda z nich oferuje różne poziomy wydajności. W niektórych przypadkach może istnieć wiele zapytań, na które nie można zapewnić wystarczającej ilości szybkością oferowaną, nawet jeśli repliki są maxed. W takim przypadku Rozważ przeniesienie do warstwy o wyższej wydajności, takiej jak standardowa warstwa S3, przeznaczona dla scenariuszy mających dużą liczbę dokumentów i wysoce wysokich obciążeń związanych z zapytaniami.

## <a name="scale-for-slow-individual-queries"></a>Skalowanie dla wolnych pojedynczych zapytań

Kolejną przyczyną częstych opóźnień jest wykonanie pojedynczej kwerendy trwa zbyt długo. W takim przypadku Dodawanie replik nie będzie pomocne. Dostępne są dwie opcje, które mogą pomóc w następujących kwestiach:

+ **Zwiększ partycje**

  Partycja dzieli dane na dodatkowe zasoby obliczeniowe. Dwie partycje dzielą dane w połowie, trzecia partycja dzieli je na trzecie i tak dalej. Jeden dodatni efekt uboczny polega na tym, że wolniejsze zapytania czasami przyspieszają działanie z powodu równoległego przetwarzania danych. Zanotowano przetwarzanie równoległe na temat niskich zapytań selektywnych, takich jak zapytania, które pasują do wielu dokumentów, lub aspektów dostarczających liczniki w dużej liczbie dokumentów. Ponieważ do oceny dokładność dokumentów lub obliczenia liczby dokumentów wymagane jest znaczące obliczenie, dodawanie dodatkowych partycji ułatwia wykonywanie zapytań.  
   
  W usłudze wyszukiwania w warstwie Standardowa może znajdować się maksymalnie 12 partycji i 1 partycja. Partycje można dostosować za pomocą [Azure Portal](search-create-service-portal.md) lub [programu PowerShell](search-manage-powershell.md).

+ **Ograniczanie pól o dużej kardynalności**

  Pole wysoka Kardynalność składa się z pola z możliwością tworzenia lub filtrowania, które ma znaczną liczbę unikatowych wartości, a w efekcie zużywa znaczące zasoby podczas obliczania wyników. Na przykład ustawienie identyfikatora produktu lub pola opisu jako wartości do przefiltrowania/filtrowania będzie traktowane jako wysoka Kardynalność, ponieważ większość wartości z dokumentu do dokumentu jest unikatowa. Wszędzie tam, gdzie to możliwe, należy ograniczyć liczbę pól o dużej kardynalności.

+ **Zwiększ poziom wyszukiwania**  

  Przechodzenie do nowszej warstwy usługi Azure Wyszukiwanie poznawcze może być innym sposobem poprawy wydajności wolnych zapytań. Każda wyższa warstwa zapewnia szybszy procesor CPU i więcej pamięci, z których oba mają pozytywny wpływ na wydajność zapytań.

## <a name="scale-for-availability"></a>Skalowanie pod kątem dostępności

Repliki nie tylko skracają opóźnienia zapytań, ale mogą również zapewnić wysoką dostępność. W przypadku pojedynczej repliki należy oczekiwać okresowego przestoju spowodowanego ponownym uruchomieniem serwera po aktualizacji oprogramowania lub w przypadku innych zdarzeń konserwacji, które nastąpią. W związku z tym ważne jest, aby wziąć pod uwagę, czy aplikacja wymaga wysokiej dostępności wyszukiwania (zapytania), a także zapisów (zdarzenia indeksowania). Usługa Azure Wyszukiwanie poznawcze oferuje opcje umowy SLA dla wszystkich płatnych ofert wyszukiwania o następujących atrybutach:

+ Dwie repliki w celu zapewnienia wysokiej dostępności obciążeń tylko do odczytu (zapytania)

+ Trzy lub więcej replik w celu zapewnienia wysokiej dostępności obciążeń odczytu i zapisu (zapytania i indeksowanie)

Aby uzyskać więcej informacji na ten temat, odwiedź [Umowa dotycząca poziomu usług wyszukiwanie poznawcze platformy Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Ponieważ repliki są kopiami danych, dzięki czemu wiele replik umożliwia platformie Azure Wyszukiwanie poznawcze wykonywanie ponownych uruchomień maszyn i ich konserwacji w odniesieniu do jednej repliki, podczas gdy wykonanie zapytania jest kontynuowane w przypadku innych replik. Z drugiej strony, Jeśli przełączysz repliki, nastąpi pogorszenie wydajności zapytania, przy założeniu, że te repliki były zasobami wykorzystywanymi.

<a name="availability-zones"></a>

### <a name="availability-zones"></a>Strefy dostępności

[Strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview) podzielić centra danych regionu na odrębne grupy lokalizacji fizycznych w celu zapewnienia wysokiej dostępności w tym samym regionie. W przypadku Wyszukiwanie poznawcze poszczególne repliki są jednostkami przypisywania stref. Usługa wyszukiwania jest uruchamiana w jednym regionie; jego repliki działają w różnych strefach.

Możesz użyć Strefy dostępności z Wyszukiwanie poznawcze platformy Azure, dodając co najmniej dwie repliki do usługi wyszukiwania. Każda replika zostanie umieszczona w innej strefie dostępności w regionie. Jeśli masz więcej replik niż Strefy dostępności, repliki będą dystrybuowane między Strefy dostępności tak jak to możliwe.

Usługa Azure Wyszukiwanie poznawcze obecnie obsługuje Strefy dostępności dla warstwy Standardowa lub wyższych usług wyszukiwania, które zostały utworzone w jednym z następujących regionów:

+ Australia Wschodnia (Data utworzenia: 30 stycznia 2021 lub nowszej)
+ Kanada Środkowa (Data i 30 stycznia 2021 lub nowsza)
+ Środkowe stany USA (utworzono 4 grudnia 2020 lub nowsze)
+ Wschodnie stany USA 2 (utworzono 30 stycznia 2021 lub nowsze)
+ Francja Środkowa (Data 23 października 2020 lub nowsza)
+ Japonia Wschodnia (Data i 30 stycznia 2021 lub nowsza)
+ Europa Północna (Data i 28 stycznia 2021 lub nowsza)
+ Azja Wschodnia Południowy (wersja 31 stycznia 2021 lub nowsza)
+ Południowe Zjednoczone Królestwo (utworzono 30 stycznia 2021 lub nowsze)
+ Europa Zachodnia (Data 29 stycznia 2021 lub nowsza)
+ Zachodnie stany USA 2 (utworzono 30 stycznia 2021 lub nowsze)

Strefy dostępności nie wpływają na [Umowa dotycząca poziomu usług wyszukiwanie poznawcze platformy Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Nadal potrzebna jest co najmniej 3 repliki na potrzeby wysokiej dostępności zapytań.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Skalowanie obciążeń rozproszonych geograficznie i nadmiarowości geograficznej

W przypadku obciążeń rozproszonych geograficznie użytkownicy, którzy znajdują się daleko od centrum danych hosta, będą mieli wyższe stawki opóźnienia. Jednym środkiem ograniczającym jest zainicjowanie obsługi wielu usług wyszukiwania w regionach z bliższą bliskością tych użytkowników.

Usługa Azure Wyszukiwanie poznawcze nie zapewnia obecnie zautomatyzowanej metody replikowania indeksów Wyszukiwanie poznawcze platformy Azure między regionami, ale istnieje kilka technik, których można użyć, aby ten proces był prosty do wdrożenia i zarządzania. Są one opisane w następnych sekcjach.

Celem rozproszonego geograficznie zestawu usług wyszukiwania jest posiadanie co najmniej dwóch indeksów dostępnych w co najmniej dwóch regionach, w których użytkownik jest kierowany do usługi Azure Wyszukiwanie poznawcze, która zapewnia najniższe opóźnienie, jak pokazano w tym przykładzie:

   ![Wiele kart usług według regionów][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Utrzymywanie synchronizacji danych w wielu usługach

Istnieją dwie opcje utrzymywania synchronizacji usług wyszukiwania rozproszonego, które składają się z usługi [azure wyszukiwanie poznawcze Indexer](search-indexer-overview.md) lub interfejsu API wypychania (nazywanego również [interfejsem API REST usługi Azure wyszukiwanie poznawcze](/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Używanie indeksatorów do aktualizowania zawartości w wielu usługach

Jeśli używasz już indeksatora w jednej usłudze, możesz skonfigurować drugi indeksator dla drugiej usługi tak, aby korzystał z tego samego obiektu źródła danych, pobierając dane z tej samej lokalizacji. Każda usługa w każdym regionie ma swój własny indeksator i docelowy indeks (indeks wyszukiwania nie jest udostępniony, co oznacza, że dane są duplikowane), ale każdy indeksator odwołuje się do tego samego źródła danych.

Poniżej znajduje się wysoce wysoki poziom wizualizacji, która będzie wyglądać na architekturę.

   ![Pojedyncze źródło danych z rozproszoną usługą indeksatora i kombinacjami usług][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Używanie interfejsów API REST do wypychania aktualizacji zawartości w wielu usługach

Jeśli korzystasz z interfejsu API REST usługi Azure Wyszukiwanie poznawcze do [wypychania zawartości w indeksie wyszukiwanie poznawcze platformy Azure](/rest/api/searchservice/update-index), możesz zapewnić synchronizację różnych usług wyszukiwania przez wypychanie zmian do wszystkich usług wyszukiwania, gdy wymagana jest aktualizacja. W kodzie, pamiętaj, aby obsługiwać przypadki, w których aktualizacja jednej usługi wyszukiwania kończy się niepowodzeniem, ale powiodła się dla innych usług wyszukiwania.

## <a name="leverage-azure-traffic-manager"></a>Korzystanie z usługi Azure Traffic Manager

[Usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umożliwia kierowanie żądań do wielu witryn sieci Web zlokalizowanych w lokalizacji geograficznej, które są następnie obsługiwane przez wiele usług wyszukiwania. Jedną z zalet Traffic Manager jest to, że może ona sondować platformę Azure Wyszukiwanie poznawcze, aby upewnić się, że jest ona dostępna i będzie kierować użytkowników do alternatywnych usług wyszukiwania w przypadku przestoju. Ponadto w przypadku routingu żądań wyszukiwania za pomocą usługi Azure Web Sites usługa Azure Traffic Manager umożliwia Równoważenie obciążenia, w przypadku których witryna internetowa jest niedostępna, ale nie Wyszukiwanie poznawcze platformy Azure. Oto przykład architektury, która wykorzystuje Traffic Manager.

   ![Wiele kart usług według regionów z centralnym Traffic Manager][3]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o limitach warstw cenowych i usług dla każdej z nich, zobacz [limity usługi](search-limits-quotas-capacity.md). Zapoznaj się z tematem [Planowanie pojemności](search-capacity-planning.md) , aby dowiedzieć się więcej na temat kombinacji partycji i repliki.

Aby zapoznać się z omówieniem dotyczącym wydajności i demonstracji technik opisanych w tym artykule, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png