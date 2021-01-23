---
title: Zarządzanie wieloma bazami danych za pomocą pul elastycznych
description: Zarządzanie i skalowanie wielu baz danych w Azure SQL Database-setek i tysiącach przy użyciu pul elastycznych. Jedna cena za zasoby, które można dystrybuować w razie potrzeby.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, sstein
ms.date: 12/9/2020
ms.openlocfilehash: f50042caf21630c5054ead76825e49b820405c5b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732698"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Pule elastyczne ułatwiają zarządzanie wieloma bazami danych w Azure SQL Database i skalowanie ich.
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database pule elastyczne to proste i ekonomiczne rozwiązanie służące do zarządzania i skalowania wielu baz danych, które mają różne i nieprzewidywalne wymagania dotyczące użycia. Bazy danych w puli elastycznej znajdują się na jednym serwerze i udostępniają określoną liczbę zasobów z ustawioną ceną. Pule elastyczne pule w usłudze Azure SQL Database umożliwiają deweloperom SaaS optymalizację stosunku ceny do wydajności dla grupy baz danych w ramach określonego budżetu, zapewniając jednocześnie elastyczność wydajności dla każdej bazy danych.

## <a name="what-are-sql-elastic-pools"></a>Co to są pule elastyczne SQL

Deweloperzy SaaS tworzą aplikacje w oparciu o warstwy danych w dużej skali składające się z wielu baz danych. Typowym wzorcem aplikacji jest udostępnianie jednej bazy danych dla każdego klienta. Jednak różni klienci często mają różne i nieprzewidywalne wzorce użycia i trudno jest przewidzieć wymagania dotyczące zasobów poszczególnych użytkowników poszczególnych baz danych. Tradycyjnie masz dwie opcje:

- Zasoby nadmiernej aprowizacji w oparciu o szczytowe użycie i za pośrednictwem płacenia
- W ramach zastrzegania kosztów, kosztem wydajności i zadowoleniem klientów podczas szczytu.

Pule elastyczne rozwiązują ten problem, upewniając się, że bazy danych uzyskują potrzebne im zasoby wydajności, gdy ich potrzebują. Udostępniają one prosty mechanizm alokacji zasobów w ramach przewidywalnego budżetu. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](saas-tenancy-app-design-patterns.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
>
> [!IMPORTANT]
> Nie jest naliczana opłata za bazę danych dla pul elastycznych. Opłaty są naliczane za każdą godzinę, gdy pula istnieje z największą liczbą jednostek eDTU lub rdzeni wirtualnych, niezależnie od użycia lub od tego, czy pula była aktywna przez czas krótszy niż godzina.

Pule elastyczne umożliwiają deweloperom kupowanie zasobów dla puli współdzielonej przez wiele baz danych w celu uwzględnienia nieprzewidywalnych okresów użycia poszczególnych baz danych. Zasoby dla puli można skonfigurować na podstawie [modelu zakupu opartego](service-tiers-dtu.md) na jednostkach DTU lub [modelu zakupu opartego na rdzeń wirtualny](service-tiers-vcore.md). Wymagania dotyczące zasobów dla puli są określane przez zagregowane wykorzystanie jego baz danych. Ilość zasobów dostępnych dla puli jest kontrolowana przez budżet dewelopera. Deweloper po prostu dodaje bazy danych do puli, opcjonalnie ustawia minimalną i maksymalną ilość zasobów dla baz danych (minimalną i maksymalną DTU lub minimalną lub maksymalną rdzeni wirtualnych w zależności od wybranego modelu odzyskania), a następnie ustawia zasoby puli na podstawie ich budżetu. Korzystając z pul, deweloper może bezproblemowo rozwijać swoją usługę od niewielkiego startupu do dojrzałego biznesu w coraz większej skali.

Poszczególne bazy danych w ramach puli mają możliwość elastycznego skalowania automatycznego w określonym zakresie parametrów. Pod dużym obciążeniem baza danych może zużywać więcej zasobów w celu spełnienia wymagań. Bazy danych w mniejszych obciążeniach zużywają mniej, a bazy danych nie zużywają żadnych zasobów. Aprowizacja zasobów dla całej puli zamiast pojedynczych baz danych upraszcza zadania związane z zarządzaniem. Dodatkowo istnieje przewidywalny budżet dla puli. Dodatkowe zasoby można dodać do istniejącej puli o minimalnym przestoju. Podobnie, jeśli dodatkowe zasoby nie są już potrzebne, można je usunąć z istniejącej puli w dowolnym momencie. I można dodawać i usuwać bazy danych z puli. Jeśli baza danych przewidywalnie niewystarczająco wykorzystuje zasoby, należy ją przenieść.

> [!NOTE]
> Podczas przechodzenia baz danych do lub z puli elastycznej nie ma przestojów z wyjątkiem krótkiego czasu (w kolejności sekund) na końcu operacji, gdy połączenia bazy danych są porzucane.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Kiedy należy uwzględnić pulę elastyczną SQL Database

Pule są odpowiednio dopasowane do wielu baz danych o określonych wzorcach użycia. Dla danej bazy danych ten wzorzec charakteryzuje się niskim średnim wykorzystaniem oraz stosunkowo rzadkimi okresami zwiększonego użycia. Z kolei wiele baz danych z trwałym średnim wykorzystaniem nie należy umieszczać w tej samej puli elastycznej.

Im więcej baz danych możesz dodać do puli, tym większe uzyskujesz oszczędności. W zależności od wzorca wykorzystania aplikacji możliwe jest wyświetlanie oszczędności z użyciem co najmniej dwóch baz danych S3.

Poniższe sekcje ułatwiają zrozumienie, jak ocenić, czy dodanie konkretnej kolekcji baz danych do puli może być korzystne. W przykładach użyto pul warstwy Standardowa, ale te same zasady mają zastosowanie także do pul w warstwach Podstawowa i Premium.

### <a name="assessing-database-utilization-patterns"></a>Ocena wzorców użycia bazy danych

Na poniższej ilustracji pokazano przykład bazy danych, która przez większość czasu pozostaje bezczynna, ale okresowo doświadcza dużego wzrostu aktywności. Jest to wzorzec użycia, który jest odpowiedni dla puli:

   ![pojedyncza baza danych odpowiednia dla puli](./media/elastic-pool-overview/one-database.png)

Wykres ilustruje użycie jednostek DTU w ciągu 1 godziny od 12:00 do 1:00, gdzie każdy punkt danych ma 1 minutę szczegółowości. Na 12:10 DB1 pik do 90 DTU, ale ogólne średnie użycie jest mniejsze niż pięć DTU. Do uruchomienia tego obciążenia w pojedynczej bazie danych jest wymagany rozmiar obliczeń S3, ale nie są one używane w okresach niskiego działania.

Pula umożliwia udostępnienie tych nieużywanych jednostek DTU wielu bazom danych, a tym samym zmniejsza liczbę potrzebnych jednostek DTU i obniża koszty ogólne.

Rozwijając poprzedni przykład, załóżmy, że istnieją dodatkowe bazy danych o podobnych wzorcach użycia jak baza danych DB1. W następnych dwóch poniższych ilustracjach użycie czterech baz danych i 20 baz danych odbywa się na tym samym wykresie, aby zilustrować nienakładający się charakter ich użycia w czasie przy użyciu modelu zakupu opartego na jednostkach DTU:

   ![cztery bazy danych z wzorcem użycia odpowiednim dla puli](./media/elastic-pool-overview/four-databases.png)

   ![dwadzieścia baz danych z wzorcem użycia odpowiednim dla puli](./media/elastic-pool-overview/twenty-databases.png)

Zagregowane użycie jednostek DTU we wszystkich 20 bazach danych przedstawia czarna linia na poprzednim rysunku. Pokazuje ona, że zagregowane użycie jednostek DTU nigdy nie przekracza 100 DTU i wskazuje, że te 20 baz danych może współużytkować 100 jednostek eDTU w tym okresie. Wynikiem tego jest spadek 20x w DTU i obniżka ceny trzynastokrotnie niższą w porównaniu do umieszczania każdej bazy danych w rozmiarach S3 dla pojedynczych baz danych.

Ten przykład jest idealny z następujących przyczyn:

- Istnieją duże różnice między użyciem szczytowym a średnim użyciem na bazę danych.
- Użycie szczytowe dla poszczególnych baz danych występuje w różnych punktach w czasie.
- Jednostki eDTU są współdzielone przez wiele baz danych.

W modelu zakupu jednostek DTU cena puli jest funkcją puli jednostek eDTU. Chociaż cena jednostki eDTU dla puli jest 1,5 raza większa niż cena jednostki DTU dla pojedynczej bazy danych, **jednostki eDTU puli mogą być współdzielone przez wiele baz danych, a tym samym potrzebna jest mniejsza całkowita liczba jednostek eDTU**. Te różnice w cenie i współużytkowanie jednostek eDTU są podstawą potencjalnych oszczędności, które mogą zapewnić pule.

W modelu zakupów rdzeń wirtualny cena jednostkowa rdzeń wirtualny dla pul elastycznych jest taka sama jak cena jednostkowa rdzeń wirtualny dla pojedynczych baz danych.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Jak mogę wybrać poprawnego rozmiaru puli

Najlepszy rozmiar puli zależy od zagregowanych zasobów potrzebnych dla wszystkich baz danych w puli. Obejmuje to określenie następujących czynności:

- Maksymalne zasoby obliczeniowe wykorzystane przez wszystkie bazy danych w puli.  Zasoby obliczeniowe są indeksowane przez jednostek eDTU lub rdzeni wirtualnych w zależności od wybranego modelu zakupu.
- Maksymalna liczba bajtów magazynu wykorzystana przez wszystkie bazy danych w puli.

W przypadku warstw usług i limitów zasobów w każdym modelu zakupu zapoznaj się z [modelem zakupu opartym na](service-tiers-dtu.md) jednostkach DTU lub [modelem zakupów opartym na rdzeń wirtualny](service-tiers-vcore.md).

Poniższe kroki ułatwiają oszacowanie, czy pula jest bardziej opłacalna niż pojedyncze bazy danych:

1. Oszacuj jednostek eDTU lub rdzeni wirtualnych wymagany dla puli w następujący sposób:

W przypadku modelu zakupu opartego na jednostkach DTU:

MAKS. (<*całkowita liczba baz danych* x *średnia użycie jednostek DTU na bazę danych*>, <*liczbę WSPÓŁBIEŻNIE szczytowego* *użycia jednostek DTU baz danych X na bazę danych*>)

W przypadku modelu zakupu opartego na rdzeń wirtualny:

MAX (<*całkowita liczba baz danych* x *średnia rdzeń wirtualny na bazę danych*>, <*liczbę współbieżnie szczytowego* *użycia rdzeń wirtualny baz danych x na bazę danych*>)

2. Oszacowanie całkowitego miejsca do magazynowania wymaganego dla puli przez dodanie rozmiaru danych wymaganego dla wszystkich baz danych w puli. W przypadku modelu zakupu jednostek DTU Określ rozmiar puli jednostek eDTU, który zapewnia tę ilość miejsca w magazynie.
3. W przypadku modelu zakupu opartego na jednostkach DTU należy uzyskać więcej wartości szacunkowych jednostek eDTU z kroku 1 i 2. W przypadku modelu zakupu opartego na rdzeń wirtualny należy uzyskać oszacowanie rdzeń wirtualny z kroku 1.
4. Zapoznaj się z [cennikiem SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) i Znajdź najmniejszy rozmiar puli większy niż oszacowanie od kroku 3.
5. Porównaj cenę puli z kroku 4 z ceną za korzystanie z odpowiednich rozmiarów obliczeń dla pojedynczych baz danych.

> [!IMPORTANT]
> Jeśli liczba baz danych w puli zbliża się do maksymalnej obsługiwanej wartości, należy wziąć pod uwagę [Zarządzanie zasobami w ramach gęstych pul elastycznych](elastic-pool-resource-management.md).

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Korzystanie z innych funkcji SQL Database z elastycznymi pulami

### <a name="elastic-jobs-and-elastic-pools"></a>Zadania elastyczne i elastyczne pule

W przypadku puli zadania zarządzania są uproszczone dzięki uruchamianiu skryptów w **[zadaniach elastycznych](elastic-jobs-overview.md)**. Zadanie elastyczne eliminuje większość monotonnych czynności związanych z dużą liczbą baz danych.

Aby uzyskać więcej informacji na temat innych narzędzi do pracy z wieloma bazami danych, zobacz artykuł dotyczący [skalowania w poziomie za pomocą usługi Azure SQL Database](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opcje ciągłości biznesowej dla baz danych w puli elastycznej

Bazy danych w puli obsługują te same [funkcje ciągłości działalności biznesowej](business-continuity-high-availability-disaster-recover-hadr-overview.md), które są dostępne dla pojedynczych baz danych.

- **Przywracanie do określonego momentu**

  Przywracanie do określonego momentu używa automatycznych kopii zapasowych bazy danych w celu odzyskania bazy danych w puli do konkretnego punktu w czasie. Zobacz [Przywracanie do punktu w czasie](recovery-using-backups.md#point-in-time-restore).

- **Przywracanie geograficzne**

  Przywracanie geograficzne zapewnia domyślną opcję odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie, w którym jest hostowana baza danych. Zobacz [przywracanie Azure SQL Database lub przełączanie do trybu failover do pomocniczego](disaster-recovery-guidance.md)

- **Aktywna replikacja geograficzna**

  W przypadku aplikacji, które mają bardziej agresywne wymagania dotyczące odzyskiwania niż w przypadku przywracania geograficznego, można skonfigurować [aktywną replikację geograficzną](active-geo-replication-overview.md) lub [grupę autotrybu failover](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Tworzenie nowej SQL Database elastycznej puli przy użyciu Azure Portal

Istnieją dwa sposoby tworzenia elastycznej puli w Azure Portal.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby utworzyć pulę elastyczną. Wyszukaj i wybierz pozycję **Azure SQL**.
2. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Wybieranie opcji wdrożenia SQL** . Dodatkowe informacje na temat pul elastycznych można wyświetlić, wybierając pozycję **Pokaż szczegóły** na kafelku **bazy danych** .
3. Na kafelku **bazy danych** wybierz pozycję **Pula elastyczna** na liście rozwijanej **Typ zasobu** , a następnie wybierz pozycję **Utwórz**:

   ![Tworzenie elastycznej puli](./media/elastic-pool-overview/create-elastic-pool.png)

4. Lub można utworzyć pulę elastyczną, przechodząc do istniejącego serwera i klikając pozycję **+ Nowa pula** , aby utworzyć pulę bezpośrednio na tym serwerze.

> [!NOTE]
> Można utworzyć wiele pul na serwerze, ale nie można dodawać baz danych z różnych serwerów do tej samej puli.

Warstwa usługi puli określa funkcje dostępne dla elastycznych pul i maksymalną ilość zasobów dostępnych dla każdej bazy danych. Aby uzyskać szczegółowe informacje, zobacz limity zasobów dla pul elastycznych w [modelu DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). W przypadku limitów zasobów opartych na rdzeń wirtualny dla pul elastycznych zobacz [limity zasobów opartych na rdzeń wirtualny — pule elastyczne](resource-limits-vcore-elastic-pools.md).

Aby skonfigurować zasoby i Cennik puli, kliknij pozycję **Konfiguruj pulę**. Następnie wybierz warstwę usług, Dodaj bazy danych do puli i skonfiguruj limity zasobów dla puli i jej baz danych.

Po zakończeniu konfigurowania puli można kliknąć pozycję "Zastosuj", nazwę puli, a następnie kliknąć przycisk "OK", aby utworzyć pulę.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorowanie puli elastycznej i jej baz danych

W Azure Portal można monitorować wykorzystanie puli elastycznej i baz danych w tej puli. Możesz również wprowadzić zestaw zmian w puli elastycznej i przesłać wszystkie zmiany w tym samym czasie. Te zmiany obejmują dodawanie lub usuwanie baz danych, zmienianie ustawień puli elastycznej lub zmienianie ustawień bazy danych.

Korzystając z wbudowanych narzędzi do [monitorowania wydajności](./performance-guidance.md) i [alertów](./alerts-insights-configure-portal.md), można łączyć się z ocenami wydajności.  Ponadto SQL Database mogą [emitować metryki i dzienniki zasobów](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal) w celu łatwiejszego monitorowania.

## <a name="customer-case-studies"></a>Analizy przypadków klientów

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart używane pule elastyczne z Azure SQL Database, aby szybko rozwijać swoje usługi biznesowe według stawki 1 000 nowej bazy danych Azure SQL na miesiąc.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco używa pul elastycznych z Azure SQL Database, aby szybko inicjować i skalować usługi dla tysięcy dzierżawców w chmurze.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI korzysta z elastycznych pul z Azure SQL Database, aby przyspieszyć cykl programowania i zwiększyć wydajność usług i klientów.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o cenach, zobacz [Cennik puli elastycznej](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Aby skalować pule elastyczne, zobacz [skalowanie pul elastycznych](elastic-pool-scale.md) i [skalowanie elastycznej puli — przykładowego kodu](scripts/monitor-and-scale-pool-powershell.md)
- Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](saas-tenancy-app-design-patterns.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
- Aby zapoznać się z samouczkiem SaaS przy użyciu pul elastycznych, zobacz [wprowadzenie do aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md).
- Aby dowiedzieć się więcej o zarządzaniu zasobami w pulach elastycznych z wieloma bazami danych, zobacz [Zarządzanie zasobami w ramach gęstych pul elastycznych](elastic-pool-resource-management.md).