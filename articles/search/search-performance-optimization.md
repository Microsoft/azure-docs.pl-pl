---
title: Dostępność i ciągłość
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak zapewnić wysoką dostępność i odporność usługi wyszukiwania na zakłócenia, a nawet katastrofalne błędy.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581527"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Dostępność i ciągłość działania na platformie Azure Wyszukiwanie poznawcze

W Wyszukiwanie poznawcze dostępność jest dostępna za poorednictwem wielu replik, a ciągłość biznesowa (i odzyskiwanie po awarii) jest realizowana za poorednictwem wielu usług wyszukiwania. Ten artykuł zawiera wskazówki, których można użyć jako punktu wyjścia do tworzenia strategii, która spełnia wymagania biznesowe w zakresie dostępności i ciągłych operacji.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Wysoka dostępność

W Wyszukiwanie poznawcze repliki są kopiami indeksu. Posiadanie wielu replik pozwala platformie Azure Wyszukiwanie poznawcze wykonywać ponownych uruchomień maszyn i przeprowadzać konserwację w odniesieniu do jednej repliki, podczas gdy wykonanie zapytania jest kontynuowane na innych replikach. Aby uzyskać więcej informacji na temat dodawania replik, zobacz [Dodawanie lub zmniejszanie replik i partycji](search-capacity-planning.md#adjust-capacity).

W przypadku każdej indywidualnej usługi wyszukiwania firma Microsoft gwarantuje dostępność konfiguracji przez co najmniej 99,9% czasu, w których są spełnione następujące kryteria: 

+ Dwie repliki w celu zapewnienia wysokiej dostępności obciążeń tylko do odczytu (zapytania)

+ Trzy lub więcej replik w celu zapewnienia wysokiej dostępności obciążeń odczytu i zapisu (zapytania i indeksowanie) 

Żadna umowa SLA nie jest dostępna dla warstwy Bezpłatna. Aby uzyskać więcej informacji, zobacz [Umowa SLA dla usługi Azure wyszukiwanie poznawcze](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Strefy dostępności

[Strefy dostępności](../availability-zones/az-overview.md) to funkcja platformy Azure, która dzieli centra danych regionu na odrębne grupy lokalizacji fizycznych w celu zapewnienia wysokiej dostępności w tym samym regionie. Jeśli używasz Strefy dostępności dla Wyszukiwanie poznawcze, poszczególne repliki są jednostkami przypisywania strefy. Usługa wyszukiwania jest uruchamiana w jednym regionie; jego repliki działają w różnych strefach.

Możesz użyć Strefy dostępności z Wyszukiwanie poznawcze platformy Azure, dodając co najmniej dwie repliki do usługi wyszukiwania. Każda replika zostanie umieszczona w innej strefie dostępności w regionie. Jeśli masz więcej replik niż Strefy dostępności, repliki będą dystrybuowane między Strefy dostępności tak jak to możliwe. W Twojej części nie ma określonych działań, z wyjątkiem [tworzenia usługi wyszukiwania](search-create-service-portal.md) w regionie, który zapewnia strefy dostępności, a następnie skonfigurować usługę do [korzystania z wielu replik](search-capacity-planning.md#adjust-capacity).

Usługa Azure Wyszukiwanie poznawcze obecnie obsługuje Strefy dostępności dla warstwy Standardowa lub wyższych usług wyszukiwania, które zostały utworzone w jednym z następujących regionów:

+ Australia Wschodnia (Data utworzenia: 30 stycznia 2021 lub nowszej)
+ Kanada Środkowa (Data i 30 stycznia 2021 lub nowsza)
+ Środkowe stany USA (utworzono 4 grudnia 2020 lub nowsze)
+ Wschodnie stany USA (utworzono 27 stycznia 2021 lub nowszej)
+ Wschodnie stany USA 2 (utworzono 30 stycznia 2021 lub nowsze)
+ Francja Środkowa (Data 23 października 2020 lub nowsza)
+ Japonia Wschodnia (Data i 30 stycznia 2021 lub nowsza)
+ Europa Północna (Data i 28 stycznia 2021 lub nowsza)
+ Azja Wschodnia Południowy (wersja 31 stycznia 2021 lub nowsza)
+ Południowe Zjednoczone Królestwo (utworzono 30 stycznia 2021 lub nowsze)
+ Europa Zachodnia (Data 29 stycznia 2021 lub nowsza)
+ Zachodnie stany USA 2 (utworzono 30 stycznia 2021 lub nowsze)

Strefy dostępności nie wpływają na [Umowa dotycząca poziomu usług wyszukiwanie poznawcze platformy Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Nadal potrzebna jest co najmniej 3 repliki na potrzeby wysokiej dostępności zapytań.

## <a name="multiple-services-in-separate-geographic-regions"></a>Wiele usług w oddzielnych regionach geograficznych

Chociaż większość klientów używa tylko jednej usługi, nadmiarowość usług może być konieczna, jeśli wymagania operacyjne są następujące:

+ [Ciągłość działania i odzyskiwanie po awarii (BCDR)](../best-practices-availability-paired-regions.md) (wyszukiwanie poznawcze nie zapewnia natychmiastowej pracy w trybie failover w przypadku przestoju).
+ Aplikacje wdrożone globalnie. Jeśli żądania zapytań i indeksowania pochodzą ze wszystkich danych na całym świecie, użytkownicy, którzy znajdują się najbliżej centrum danych hosta, będą mieć szybszą wydajność. Tworzenie dodatkowych usług w regionach o bliskim sąsiedztwie tych użytkowników pozwala na wyrównywanie wydajności dla wszystkich użytkowników.
+ [Architektury z wieloma dzierżawcami](search-modeling-multitenant-saas-applications.md) czasami są wywoływane w przypadku co najmniej dwóch usług.

Jeśli potrzebujesz dwóch dodatkowych usług wyszukiwania, tworzenie ich w różnych regionach może spełnić wymagania aplikacji dotyczące ciągłości i odzyskiwania, a także krótszych czasów odpowiedzi dla globalnej bazy użytkowników.

Usługa Azure Wyszukiwanie poznawcze obecnie nie zapewnia zautomatyzowanej metody replikowania indeksów geograficznych w różnych regionach, ale istnieje kilka technik, które mogą być używane, aby ten proces był prosty do wdrożenia i zarządzania. Są one opisane w następnych sekcjach.

Celem rozproszonego geograficznie zestawu usług wyszukiwania jest posiadanie co najmniej dwóch indeksów dostępnych w co najmniej dwóch regionach, w których użytkownik jest kierowany do usługi Azure Wyszukiwanie poznawcze, która zapewnia najniższe opóźnienie:

   ![Wiele kart usług według regionów][1]

Możesz zaimplementować tę architekturę, tworząc wiele usług i Projektując strategię synchronizacji danych. Opcjonalnie możesz dołączyć zasób, taki jak Traffic Manager platformy Azure, dla żądań routingu. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi wyszukiwania](search-create-service-portal.md).

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Utrzymywanie synchronizacji danych w wielu usługach

Istnieją dwie opcje utrzymywania synchronizacji co najmniej dwóch usług wyszukiwania rozproszonego, które składają się z użycia usługi [azure wyszukiwanie poznawcze Indexer](search-indexer-overview.md) lub interfejsu API wypychania (nazywanego również [interfejsem API REST platformy Azure wyszukiwanie poznawcze](/rest/api/searchservice/)). 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>Opcja 1. Używanie indeksatorów do aktualizowania zawartości w wielu usługach

Jeśli używasz już indeksatora w jednej usłudze, możesz skonfigurować drugi indeksator dla drugiej usługi tak, aby korzystał z tego samego obiektu źródła danych, pobierając dane z tej samej lokalizacji. Każda usługa w każdym regionie ma swój własny indeksator i docelowy indeks (indeks wyszukiwania nie jest udostępniony, co oznacza, że dane są duplikowane), ale każdy indeksator odwołuje się do tego samego źródła danych.

Poniżej znajduje się wysoce wysoki poziom wizualizacji, która będzie wyglądać na architekturę.

   ![Pojedyncze źródło danych z rozproszoną usługą indeksatora i kombinacjami usług][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Opcja 2. Korzystanie z interfejsów API REST do wypychania aktualizacji zawartości w wielu usługach

Jeśli korzystasz z interfejsu API REST usługi Azure Wyszukiwanie poznawcze, aby [wypchnąć zawartość do indeksu wyszukiwania](tutorial-optimize-indexing-push-api.md), możesz zachować synchronizację różnych usług wyszukiwania przez wypychanie zmian do wszystkich usług wyszukiwania, gdy wymagana jest aktualizacja. W kodzie, pamiętaj, aby obsługiwać przypadki, w których aktualizacja jednej usługi wyszukiwania kończy się niepowodzeniem, ale powiodła się dla innych usług wyszukiwania.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Dopasowywanie żądań za pomocą usługi Azure Traffic Manager

[Usługa Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umożliwia kierowanie żądań do wielu witryn sieci Web zlokalizowanych w lokalizacji geograficznej, które są następnie obsługiwane przez wiele usług wyszukiwania. Jedną z zalet Traffic Manager jest to, że może ona sondować platformę Azure Wyszukiwanie poznawcze, aby upewnić się, że jest ona dostępna i będzie kierować użytkowników do alternatywnych usług wyszukiwania w przypadku przestoju. Ponadto w przypadku routingu żądań wyszukiwania za pomocą usługi Azure Web Sites usługa Azure Traffic Manager umożliwia Równoważenie obciążenia, w przypadku których witryna internetowa jest niedostępna, ale nie Wyszukiwanie poznawcze platformy Azure. Oto przykład architektury, która wykorzystuje Traffic Manager.

   ![Wiele kart usług według regionów z centralnym Traffic Manager][3]

## <a name="disaster-recovery-and-service-outages"></a>Odzyskiwanie po awarii i awaria usługi

Mimo że możemy odzyskać Twoje dane, usługa Azure Wyszukiwanie poznawcze nie zapewnia natychmiastowej pracy w trybie failover w przypadku wystąpienia awarii w klastrze lub na poziomie centrum danych. Jeśli klaster ulegnie awarii w centrum danych, zespół operacyjny wykryje i zacznie działać do przywracania usługi. Podczas przywracania usługi możesz zażądać kredytów na korzystanie z usługi, aby wyrównać niedostępność usługi na [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Jeśli ciągła usługa jest wymagana w przypadku wystąpienia awarii poza kontrolą firmy Microsoft, można [zainicjować obsługę administracyjną dodatkowej usługi](search-create-service-portal.md) w innym regionie i wdrożyć strategię replikacji geograficznej w celu zapewnienia, że indeksy są w pełni nadmiarowe dla wszystkich usług.

Klienci używający [indeksatorów](search-indexer-overview.md) do wypełniania i odświeżania indeksów mogą obsługiwać odzyskiwanie po awarii za pomocą indeksatorów specyficznych dla lokalizacji geograficznych wykorzystujących to samo źródło danych. Dwie usługi w różnych regionach, z których każdy uruchamia indeksator, może indeksować to samo źródło danych w celu zapewnienia nadmiarowości geograficznej. W przypadku indeksowania ze źródeł danych, które są również geograficznie nadmiarowe, należy pamiętać, że indeksatory usługi Azure Wyszukiwanie poznawcze mogą wykonywać tylko indeksowanie przyrostowe (Scalanie aktualizacji z nowych, zmodyfikowanych lub usuniętych dokumentów) z replik podstawowych. W przypadku zdarzenia trybu failover Pamiętaj, aby ponownie wskazać indeksator do nowej repliki podstawowej. 

Jeśli nie używasz indeksatorów, użyj kodu aplikacji do wypychania obiektów i danych do różnych usług wyszukiwania równolegle. Aby uzyskać więcej informacji, zobacz temat [Utrzymywanie synchronizacji danych w wielu usługach](#data-sync).

## <a name="back-up-and-restore-alternatives"></a>Wykonaj kopię zapasową i Przywróć alternatywy

Ponieważ platforma Azure Wyszukiwanie poznawcze nie jest podstawowym rozwiązaniem do przechowywania danych, firma Microsoft nie zapewnia formalnego mechanizmu tworzenia kopii zapasowych i przywracania samoobsługowego. Można jednak użyć przykładowego kodu do **przywracania kopii zapasowej** w ramach tego [przykładowego repozytorium platformy Azure wyszukiwanie poznawcze .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) , aby utworzyć kopię zapasową definicji indeksu i migawki do serii plików JSON, a następnie użyć tych plików do przywrócenia indeksu, w razie potrzeby. To narzędzie umożliwia również przenoszenie indeksów między warstwami usług.

W przeciwnym razie kod aplikacji używany do tworzenia i wypełniania indeksu jest opcją przywracania de facto w przypadku usunięcia indeksu przez pomyłkę. Aby ponownie skompilować indeks, należy go usunąć (przy założeniu, że istnieje), ponownie utworzyć indeks w usłudze i załadować ponownie przez pobranie danych z podstawowego magazynu danych.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o limitach warstw cenowych i usług dla każdej z nich, zobacz [limity usługi](search-limits-quotas-capacity.md). Zapoznaj się z tematem [Planowanie pojemności](search-capacity-planning.md) , aby dowiedzieć się więcej na temat kombinacji partycji i repliki.

Aby zapoznać się z omówieniem dotyczącym wydajności i demonstracji technik opisanych w tym artykule, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png