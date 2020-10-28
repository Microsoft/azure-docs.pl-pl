---
title: Odzyskiwanie po awarii dla aplikacji SaaS z replikacją geograficzną
description: Dowiedz się, jak za pomocą replik geograficznych Azure SQL Database odzyskać wielodostępną aplikację SaaS w przypadku awarii
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: ffe5a1d0c9bbdbc416ecce7c36b3710339c4f059
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781026"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Odzyskiwanie po awarii dla wielodostępnej aplikacji SaaS przy użyciu replikacji geograficznej bazy danych
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku przedstawiono scenariusz pełnego odzyskiwania po awarii dla wielodostępnej aplikacji SaaS wdrożonej przy użyciu modelu bazy danych na dzierżawcę. Aby ochronić aplikację przed awarią, należy użyć [_replikacji geograficznej_](active-geo-replication-overview.md) w celu utworzenia replik dla katalogu i baz danych dzierżaw w alternatywnym regionie odzyskiwania. Jeśli wystąpi awaria, możesz szybko przejść do trybu failover z tymi replikami, aby wznowić normalne operacje biznesowe. W przypadku przejścia w tryb failover bazy danych w pierwotnym regionie stają się replikami pomocniczymi baz danych w regionie odzyskiwania. Po powrocie tych replik do trybu online automatycznie przechwytuje do stanu baz danych w regionie odzyskiwania. Po rozwiązaniu awarii nastąpiło powrót po awarii do baz danych w pierwotnym regionie produkcyjnym.

W tym samouczku przedstawiono przepływy pracy trybu failover i powrotu po awarii. Omawiane tematy:
> [!div class="checklist"]
> 
> * Synchronizuj informacje o konfiguracji bazy danych i elastycznej puli do katalogu dzierżawy
> * Konfigurowanie środowiska odzyskiwania w innym regionie zawierającym aplikacje, serwery i pule
> * Replikowanie katalogów i baz danych dzierżawy do regionu odzyskiwania przy użyciu _replikacji geograficznej_
> * Przełączenie w tryb failover aplikacji i katalogu oraz baz danych dzierżawy do regionu odzyskiwania 
> * Później w przypadku przełączenia w tryb failover aplikacji, katalogu i baz danych dzierżawy do oryginalnego regionu po rozwiązaniu awarii
> * Aktualizowanie katalogu w miarę przełączenia w tryb failover każdej bazy danych dzierżawy w celu śledzenia podstawowej lokalizacji bazy danych każdej dzierżawy
> * Upewnij się, że aplikacja i główna baza danych dzierżawy zawsze znajdują się w tym samym regionie świadczenia usługi Azure, aby zmniejszyć opóźnienie  
 

Przed rozpoczęciem pracy z tym samouczkiem upewnij się, że zostały spełnione następujące wymagania wstępne:
* Wdrożono Wingtip bilety bazy danych SaaS dla dzierżawy. Aby wdrożyć program w ciągu mniej niż pięciu minut, zobacz [wdrażanie i eksplorowanie bazy danych Wingtip biletów SaaS na aplikację dzierżawców](saas-dbpertenant-get-started-deploy.md)  
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Wprowadzenie do wzorca odzyskiwania replikacji geograficznej

![Architektura odzyskiwania](./media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Odzyskiwanie po awarii (DR) jest ważnym zagadnieniem dotyczącym wielu aplikacji, zarówno ze względu na zgodność, jak i ciągłości biznesowej. W przypadku długotrwałej przerwy w działaniu usługi, dobrze przygotowany plan odzyskiwania po awarii może zminimalizować zakłócenia działania firmy. Korzystanie z funkcji replikacji geograficznej zapewnia najniższy cel punktu odzyskiwania i RTO przez utrzymywanie replik bazy danych w regionie odzyskiwania, które można przełączać w tryb failover.

Plan odzyskiwania po awarii oparty na replikacji geograficznej obejmuje trzy różne części:
* Konfigurowanie i konserwowanie środowiska odzyskiwania
* Odzyskiwanie awaryjne aplikacji i baz danych do środowiska odzyskiwania w przypadku wystąpienia awarii,
* Wycofywanie — tryb failover aplikacji i baz danych z powrotem do oryginalnego regionu po rozwiązaniu aplikacji 

Wszystkie części muszą być traktowane ostrożnie, szczególnie w przypadku działania na dużą skalę. Ogólnie, plan musi mieć kilka celów:

* Konfigurowanie
    * Ustanów i obsługuj środowisko obrazu dublowanego w regionie odzyskiwania. Tworzenie pul elastycznych i replikowanie wszystkich baz danych w tym środowisku odzyskiwania rezerwuje pojemność w regionie odzyskiwania. Utrzymywanie tego środowiska obejmuje replikowanie nowych baz danych dzierżawy w miarę ich udostępniania.  
* Odzyskiwania
    * W przypadku, gdy środowisko odzyskiwania skalowane w dół służy do minimalizowania codziennych kosztów, pule i bazy danych muszą być skalowane w celu uzyskania pełnej pojemności operacyjnej w regionie odzyskiwania
    * Włącz obsługę nowych dzierżawców w regionie odzyskiwania tak szybko, jak to możliwe  
    * Być zoptymalizowane pod kątem przywracania dzierżawców w kolejności priorytetów
    * Być zoptymalizowane pod kątem możliwie najszybszego uzyskiwania dzierżawców w trybie online przez wykonywanie kroków równolegle, tam gdzie jest to praktyczne
    * Być odporne na awarie, ponowne uruchomienie i idempotentne
    * Można anulować proces w średnim locie, jeśli oryginalny region powróci do linii.
* Repatriacja 
    * Przełączenie w tryb failover baz danych z regionu odzyskiwania do replik w pierwotnym regionie z minimalnym wpływem na dzierżawcę: brak utraty danych i minimalny okres poza wierszem dla dzierżawy.   

W tym samouczku te wyzwania są rozwiązywane przy użyciu funkcji Azure SQL Database i platformy Azure:

* [Azure Resource Manager szablonów](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), aby zastrzec wszystkie potrzebne pojemności tak szybko, jak to możliwe. Szablony Azure Resource Manager są używane do aprowizacji lustrzanego obrazu serwerów produkcyjnych i pul elastycznych w regionie odzyskiwania.
* [Replikacja geograficzna](active-geo-replication-overview.md), która umożliwia tworzenie asynchronicznie zreplikowanych serwerów pomocniczych tylko do odczytu dla wszystkich baz danych. Podczas awarii przechodzą do trybu failover w replikach w regionie odzyskiwania.  Po rozwiązaniu awarii nastąpi powrót po awarii do baz danych w oryginalnym regionie bez utraty danych.
* [Asynchroniczne](../../azure-resource-manager/management/async-operations.md) operacje trybu failover wysyłane w kolejności priorytetu dzierżawy w celu zminimalizowania czasu pracy w trybie failover dla dużej liczby baz danych.
* [Funkcje odzyskiwania fragmentu Management](elastic-database-recovery-manager.md)do zmiany wpisów bazy danych w katalogu podczas odzyskiwania i wycofywania. Te funkcje umożliwiają aplikacji łączenie się z bazami danych dzierżaw, niezależnie od lokalizacji bez ponownego konfigurowania aplikacji.
* [Aliasy DNS programu SQL Server](./dns-alias-overview.md), aby zapewnić bezproblemową obsługę nowych dzierżawców niezależnie od regionu, w którym działa aplikacja. Aliasy DNS są również używane do zezwalania procesowi synchronizacji katalogu na łączenie się z usługą Active Catalog, niezależnie od jego lokalizacji.

## <a name="get-the-disaster-recovery-scripts"></a>Pobierz skrypty odzyskiwania po awarii 

> [!IMPORTANT]
> Podobnie jak w przypadku wszystkich skryptów zarządzania biletów Wingtip, skrypty DR są przykładową jakością i nie są używane w środowisku produkcyjnym. 

Skrypty odzyskiwania używane w tym samouczku i kodzie źródłowym aplikacji Wingtip są dostępne w [repozytorium GitHub Wingtip SaaS Database for dzierżawca](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania skryptów zarządzania biletami Wingtip.

## <a name="tutorial-overview"></a>Omówienie samouczka
W tym samouczku należy najpierw użyć replikacji geograficznej w celu utworzenia replik aplikacji biletów Wingtip i jej baz danych w innym regionie. Następnie przejdź do trybu failover w tym regionie, aby symulować odzyskiwanie po awarii. Po zakończeniu aplikacja jest w pełni funkcjonalna w regionie odzyskiwania.

Później w oddzielnym kroku repatriacji katalog i bazy danych dzierżawy są przełączane w tryb failover w regionie odzyskiwania do oryginalnego regionu. Aplikacja i bazy danych pozostają dostępne w trakcie wycofywania. Po zakończeniu aplikacja jest w pełni funkcjonalna w oryginalnym regionie.

> [!Note]
> Aplikacja zostanie odzyskana w _sparowanym regionie_ regionu, w którym aplikacja jest wdrażana. Aby uzyskać więcej informacji, zobacz [regiony sparowane na platformie Azure](../../best-practices-availability-paired-regions.md).

## <a name="review-the-healthy-state-of-the-application"></a>Przejrzyj stan kondycji aplikacji

Przed rozpoczęciem procesu odzyskiwania zapoznaj się z normalnym dobrym stanem aplikacji.
1. W przeglądarce sieci Web otwórz centrum zdarzeń biletów Wingtip ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET — Zamień użytkownika na &lt; &gt; wartość użytkownika wdrożenia).
    * Przewiń w dół strony i zwróć uwagę na nazwę serwera wykazu i lokalizację w stopce. Lokalizacja to region, w którym została wdrożona aplikacja.
    *Porada: Umieść wskaźnik myszy nad lokalizacją, aby powiększyć ekran.* 
     ![ Stan kondycji centrum zdarzeń w oryginalnym regionie](./media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Kliknij dzierżawę korytarza w firmie Contoso i otwórz jej stronę zdarzeń.
    * W stopce Zwróć uwagę na nazwę serwera dzierżawy. Lokalizacja będzie taka sama jak lokalizacja serwera wykazu.

3. W [Azure Portal](https://portal.azure.com)Otwórz grupę zasobów, w której wdrożono aplikację
    * Zwróć uwagę na region, w którym są wdrażane Serwery. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synchronizuj konfigurację dzierżawy w wykazie

W tym zadaniu zostanie rozpoczęty proces, który synchronizuje konfigurację serwerów, pul elastycznych i baz danych w katalogu dzierżawców. Ten proces zachowuje aktualność informacji w wykazie.  Proces działa z usługą Active Catalog, niezależnie od tego, czy znajduje się w oryginalnym regionie, czy w regionie odzyskiwania. Informacje o konfiguracji są używane w ramach procesu odzyskiwania, aby upewnić się, że środowisko odzyskiwania jest zgodne z oryginalnym środowiskiem, a później podczas wycofywania, aby upewnić się, że oryginalny region jest spójny ze wszystkimi zmianami wprowadzonymi w środowisku odzyskiwania. Wykaz jest również używany do śledzenia stanu odzyskiwania zasobów dzierżawy

> [!IMPORTANT]
> Dla uproszczenia proces synchronizacji i inne długotrwałe procesy odzyskiwania i wycofywania są implementowane w tych samouczkach jako lokalne zadania programu PowerShell lub sesje, które są uruchamiane w ramach logowania użytkownika klienta. Tokeny uwierzytelniania wydawane podczas logowania wygaśnie po kilku godzinach, a zadania zakończą się niepowodzeniem. W scenariuszu produkcyjnym długotrwałe procesy powinny być implementowane jako niezawodne usługi platformy Azure, które są uruchamiane w ramach jednostki usługi. Zobacz [używanie Azure PowerShell, aby utworzyć jednostkę usługi przy użyciu certyfikatu](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

1. W _ISE programu PowerShell_ Otwórz plik. ..\Learning Modules\UserConfig.PSM1. Zastąp `<resourcegroup>` `<user>` wartości i w wierszach 10 i 11 wartością używaną podczas wdrażania aplikacji.  Zapisz plik!

2. W *ISE programu PowerShell* Otwórz skrypt ..\Learning Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 awaryjny i ustaw:
    * **$DemoScenario = 1** , uruchom zadanie w tle, które synchronizuje serwer dzierżawy i informacje o konfiguracji puli do wykazu

3. Naciśnij klawisz **F5** , aby uruchomić skrypt synchronizacji. Zostanie otwarta nowa sesja programu PowerShell w celu zsynchronizowania konfiguracji zasobów dzierżawy.
![Zrzut ekranu pokazujący nową sesję programu PowerShell otwartą w celu zsynchronizowania konfiguracji zasobów dzierżawy.](./media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Pozostaw okno programu PowerShell działające w tle i Kontynuuj pracę z pozostałą częścią samouczka. 

> [!Note]
> Proces synchronizacji nawiązuje połączenie z wykazem za pośrednictwem aliasu DNS. Ten alias jest modyfikowany podczas przywracania i wycofywania w celu wskazywania usługi Active Catalog. Proces synchronizacji zachowuje aktualność katalogu przy użyciu wszelkich zmian konfiguracji bazy danych lub puli wprowadzonych w regionie odzyskiwania.  Podczas wycofywania te zmiany są stosowane do zasobów równoważnych w oryginalnym regionie.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Tworzenie pomocniczych replik bazy danych w regionie odzyskiwania

W tym zadaniu zostanie rozpoczęty proces służący do wdrażania zduplikowanego wystąpienia aplikacji i replikowania wykazu oraz wszystkich baz danych dzierżaw do regionu odzyskiwania.

> [!Note]
> W tym samouczku Dodano ochronę replikacji geograficznej do przykładowej aplikacji biletów Wingtip. W scenariuszu produkcyjnym dla aplikacji korzystającej z replikacji geograficznej każda dzierżawa zostanie zainicjowana z bazą danych z replikacją geograficzną od samego początku. Zobacz [Projektowanie usług o wysokiej dostępności przy użyciu Azure SQL Database](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. W *ISE programu PowerShell* Otwórz skrypt ..\Learning Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 awaryjny i ustaw następujące wartości:
    * **$DemoScenario = 2** , Tworzenie dublowanego środowiska odzyskiwania obrazu i replikowanie wykazu oraz baz danych dzierżaw

2. Naciśnij klawisz **F5** , aby uruchomić skrypt. Zostanie otwarta nowa sesja programu PowerShell w celu utworzenia replik.
![Proces synchronizacji](./media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Przegląd normalnego stanu aplikacji

W tym momencie aplikacja działa normalnie w oryginalnym regionie i jest teraz chroniona przez replikację geograficzną.  Repliki pomocnicze tylko do odczytu istnieją w regionie odzyskiwania dla wszystkich baz danych. 

1. W Azure Portal Sprawdź grupy zasobów i zwróć uwagę, że grupa zasobów została utworzona z sufiksem odzyskiwania w regionie odzyskiwania. 

2. Eksplorowanie zasobów w grupie zasobów odzyskiwania.  

3. Kliknij bazę danych korytarze contoso wspólnie na serwerze _tenants1-DPT- &lt; User &gt; -Recovery_ .  Kliknij pozycję Geo-Replication po lewej stronie. 

    ![Link do replikacji geograficznej firmy Contoso](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Na mapie regionów platformy Azure Zwróć uwagę na link replikacji geograficznej między podstawową w regionie pierwotnym i pomocniczym w regionie odzyskiwania.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Przełączenie w tryb failover aplikacji do regionu odzyskiwania

### <a name="geo-replication-recovery-process-overview"></a>Omówienie procesu odzyskiwania replikacji geograficznej

Skrypt odzyskiwania wykonuje następujące zadania:

1. Wyłącza punkt końcowy Traffic Manager aplikacji sieci Web w oryginalnym regionie. Wyłączenie punktu końcowego uniemożliwia użytkownikom nawiązywanie połączenia z aplikacją w nieprawidłowym stanie, jeśli oryginalny region przejdzie w tryb online podczas odzyskiwania.

1. Program używa trybu failover bazy danych wykazu w regionie odzyskiwania, aby uczynić ją podstawową bazą danych, i aktualizuje alias _activecatalog_ , aby wskazywał serwer wykazu odzyskiwania.

1. Aktualizuje alias _newtenant_ w taki sposób, aby wskazywał serwer dzierżawy w regionie odzyskiwania. Zmiana tego aliasu gwarantuje, że bazy danych dla nowych dzierżawców są inicjowane w regionie odzyskiwania. 

1. Oznacza wszystkie istniejące dzierżawy w wykazie odzyskiwania jako w trybie offline, aby uniemożliwić dostęp do baz danych dzierżawy przed przełączeniem w tryb failover.

1. Aktualizuje konfigurację wszystkich pul elastycznych i replikuje pojedyncze bazy danych w regionie odzyskiwania w celu dublowania ich konfiguracji w oryginalnym regionie. (To zadanie jest konieczne tylko wtedy, gdy pule lub zreplikowane bazy danych w środowisku odzyskiwania są skalowane w dół podczas normalnych operacji w celu obniżenia kosztów).

1. Włącza punkt końcowy Traffic Manager aplikacji sieci Web w regionie odzyskiwania. Włączenie tego punktu końcowego umożliwia aplikacji Inicjowanie obsługi administracyjnej nowych dzierżawców. Na tym etapie istniejące dzierżawy są nadal w trybie offline.

1. Przesyła partie żądań w celu wymuszenia przełączenia w tryb failover baz danych w kolejności priorytetów.
    * Partie są zorganizowane w taki sposób, że bazy danych są w trybie failover równolegle między wszystkimi pulami.
    * Żądania trybu failover są przesyłane przy użyciu operacji asynchronicznych, dzięki czemu są one przesyłane szybko i wiele żądań można przetwarzać współbieżnie.

   > [!Note]
   > W scenariuszu przestoju podstawowe bazy danych w oryginalnym regionie są w trybie offline.  Wymuś tryb failover na pomocniczym zerwanie połączenia z serwerem podstawowym bez próby zastosowania wszelkich pozostałych transakcji w kolejce. W scenariuszu testowania odzyskiwania po awarii, jak w tym samouczku, jeśli w momencie przełączenia w tryb failover istnieje jakiekolwiek działanie aktualizacji, może to spowodować utratę danych. Później podczas procesu wycofywania w przypadku przejścia w tryb failover bazy danych w regionie odzyskiwania do oryginalnego regionu zostanie użyta normalna praca awaryjna, aby upewnić się, że nie ma utraty danych.

1. Monitoruje usługę, aby określić, kiedy bazy danych zostały przełączone w tryb failover. Gdy baza danych dzierżawy zostanie przełączona w tryb failover, aktualizuje wykaz, aby zarejestrować stan odzyskiwania bazy danych dzierżawy i oznaczyć dzierżawcę jako online.
    * Aplikacja może uzyskiwać dostęp do baz danych dzierżawy, gdy tylko są one oznaczone jako online w wykazie.
    * Suma wartości rowversion w bazie danych dzierżawy jest przechowywana w wykazie. Ta wartość pełni funkcję odcisku palca, która umożliwia procesowi wycofywania w celu ustalenia, czy baza danych została zaktualizowana w regionie odzyskiwania.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Uruchamianie skryptu w celu przełączenia w tryb failover w regionie odzyskiwania

Teraz Załóżmy, że wystąpi awaria w regionie, w którym aplikacja jest wdrażana, i uruchomienie skryptu odzyskiwania:

1. W *ISE programu PowerShell* Otwórz skrypt ..\Learning Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 awaryjny i ustaw następujące wartości:
    * **$DemoScenario = 3** , Odzyskaj aplikację do regionu odzyskiwania, przełączając w tryb failover do replik

2. Naciśnij klawisz **F5** , aby uruchomić skrypt.  
    * Skrypt zostanie otwarty w nowym oknie programu PowerShell, a następnie rozpocznie się seria zadań programu PowerShell uruchamianych równolegle. Te zadania przechodzą w tryb failover baz danych dzierżawy do regionu odzyskiwania.
    * Region odzyskiwania to _sparowany region_ skojarzony z regionem świadczenia usługi Azure, w którym została wdrożona aplikacja. Aby uzyskać więcej informacji, zobacz [regiony sparowane na platformie Azure](../../best-practices-availability-paired-regions.md). 

3. Monitoruj stan procesu odzyskiwania w oknie programu PowerShell.
    ![proces trybu failover](./media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Aby poznać kod zadań odzyskiwania, przejrzyj skrypty programu PowerShell w folderze. ..\Learning Modules\Business ciągłości i Recovery\DR-FailoverToReplica\RecoveryJobs awarii.

### <a name="review-the-application-state-during-recovery"></a>Sprawdzanie stanu aplikacji podczas odzyskiwania

Gdy punkt końcowy aplikacji jest wyłączony w Traffic Manager, aplikacja jest niedostępna. Po przełączeniu katalogu w tryb failover do regionu odzyskiwania i wszystkich dzierżawców oznaczonych w trybie offline aplikacja zostanie przywrócona do trybu online. Mimo że aplikacja jest dostępna, każda dzierżawa pojawia się w trybie offline w centrum zdarzeń do momentu przełączenia w tryb failover. Ważne jest, aby zaprojektować aplikację do obsługi baz danych dzierżaw w trybie offline.

1. Natychmiast po odzyskaniu bazy danych wykazu Odśwież centrum zdarzeń biletów Wingtip w przeglądarce internetowej.
   * Zwróć uwagę, że nazwa serwera wykazu ma teraz sufiks _odzyskiwania_ i znajduje się w regionie odzyskiwania.
   * Zwróć uwagę, że dzierżawy, które nie zostały jeszcze przywrócone, są oznaczone jako w trybie offline i nie są wybierane.  

     > [!Note]
     > Aby odzyskać tylko kilka baz danych, nie można odświeżyć przeglądarki przed ukończeniem odzyskiwania, co może spowodować, że dzierżawy nie będą widoczne w trybie offline. 
 
     ![Centrum zdarzeń w trybie offline](./media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Jeśli otworzysz bezpośrednio stronę zdarzeń dzierżawcy w trybie offline, zostanie wyświetlone powiadomienie "dzierżawa w trybie offline". Jeśli na przykład firma Contoso jest w trybie offline, spróbuj otworzyć http://events.wingtip-dpt.&lt stronę "User &gt; . Trafficmanager.NET/contosoconcerthall ![ contoso w trybie offline](./media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Inicjowanie obsługi administracyjnej nowej dzierżawy w regionie odzyskiwania
Nawet przed przełączeniem wszystkich istniejących baz danych dzierżaw do trybu failover można udostępnić nowe dzierżawy w regionie odzyskiwania.  

1. W *ISE programu PowerShell* Otwórz skrypt ..\Learning Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 awarii i Ustaw następującą właściwość:
    * **$DemoScenario = 4** , Inicjowanie obsługi administracyjnej nowej dzierżawy w regionie odzyskiwania

2. Naciśnij klawisz **F5** , aby uruchomić skrypt i udostępnić nową dzierżawę. 

3. Po zakończeniu zostanie otwarta strona zdarzenia korytarzy Hawthorn w przeglądarce. Zwróć uwagę na stopkę, w której zainicjowano Inicjowanie obsługi bazy danych Hawthorn w regionie odzyskiwania.
    ![Strona zdarzeń korytarza Hawthorn](./media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. W przeglądarce Odśwież stronę centrum zdarzeń biletów Wingtip, aby zobaczyć dołączoną część Hawthorn. 
    * Jeśli zainicjowano Inicjowanie obsługi Hawthorn bez oczekiwania na przywrócenie innych dzierżawców, inne dzierżawy mogą być nadal w trybie offline.


## <a name="review-the-recovered-state-of-the-application"></a>Przejrzyj stan odzyskany aplikacji

Po zakończeniu procesu odzyskiwania aplikacja i wszystkie dzierżawy są w pełni funkcjonalne w regionie odzyskiwania. 

1. Gdy widok w oknie konsoli programu PowerShell wskazuje, że wszystkie dzierżawy są odzyskiwane, Odśwież centrum zdarzeń.  Wszystkie dzierżawy będą widoczne w trybie online, łącznie z nową dzierżawą, Hawthorn.

    ![odzyskane i nowe dzierżawy w centrum zdarzeń](./media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. W [Azure Portal](https://portal.azure.com)Otwórz listę grup zasobów.  
    * Zwróć uwagę na wdrożoną grupę zasobów oraz grupę zasobów odzyskiwania z sufiksem _odzyskiwania_ .  Grupa zasobów odzyskiwania zawiera wszystkie zasoby utworzone podczas procesu odzyskiwania oraz nowe zasoby utworzone podczas przestoju.  

3. Otwórz grupę zasobów odzyskiwania i zwróć uwagę na następujące elementy:
   * Wersje odzyskiwania katalogu i serwerów tenants1 z sufiksem _odzyskiwania_ .  Przywrócone wykazy i bazy danych dzierżawy na tych serwerach mają nazwy używane w oryginalnym regionie.

   * Program SQL Server _tenants2-DPT- &lt; User &gt; -Recovery_ .  Ten serwer jest używany do aprowizacji nowych dzierżawców podczas przestoju.
   * App Service o nazwie _Events-Wingtip-DPT- &lt; recoveryregion &gt; - &lt; User&gt_ ;, czyli wystąpienie odzyskiwania aplikacji Events. 

     ![Zasoby odzyskiwania platformy Azure](./media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Otwórz program SQL Server _tenants2-DPT- &lt; User &gt; -Recovery_ .  Zwróć uwagę, że zawiera _hawthornhall_ bazy danych i pulę elastyczną, _Pool1_ .  Baza danych _hawthornhall_ jest konfigurowana jako Elastyczna baza danych w elastycznej puli _Pool1_ .

5. Przejdź z powrotem do grupy zasobów i kliknij bazę danych korytarze contoso wspólnie na serwerze _tenants1-DPT- &lt; User &gt; -Recovery_ . Kliknij pozycję Geo-Replication po lewej stronie.
    
    ![Baza danych firmy Contoso po zakończeniu pracy w trybie failover](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Zmień dane dzierżawy 
W tym zadaniu należy zaktualizować jedną z baz danych dzierżawy. 

1. W przeglądarce Znajdź listę zdarzeń dla korytarza wspólnie Contoso i zanotuj nazwę ostatniego zdarzenia.
2. W *ISE programu PowerShell* w skrypcie. ..\Learning Modules\Business (ciągłość i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 awarii) Ustaw następującą wartość:
    * **$DemoScenario = 5** Usuwanie zdarzenia z dzierżawy w regionie odzyskiwania
3. Naciśnij klawisz **F5** , aby wykonać skrypt
4. Odśwież stronę ze zdarzeniami korytarzy w http://events.wingtip-dpt.&lt firmie Contoso (; User &gt; . trafficmanager.NET/contosoconcerthall — podstawianie &lt; użytkownika &gt; z wartością użytkownika wdrożenia) i Zauważ, że ostatnie zdarzenie zostało usunięte.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Wycofywanie aplikacji do oryginalnego regionu produkcji

To zadanie służy do wycofywania aplikacji do oryginalnego regionu. W rzeczywistym scenariuszu można zainicjować proces wycofywania po rozwiązaniu awarii.

### <a name="repatriation-process-overview"></a>Przegląd procesu wycofywania

![Architektura repatriacji](./media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Proces repatriacji:
1. Anuluje żądania przywracania bazy danych zaległe lub w locie.
2. Aktualizuje alias _newtenant_ w taki sposób, aby wskazywał serwer dzierżawców w regionie pochodzenia. Zmiana tego aliasu gwarantuje, że bazy danych dla nowych dzierżawców będą teraz obsługiwane w regionie pochodzenia.
3. Nasiona wszelkich zmienionych danych dzierżawy w oryginalnym regionie
4. Przełączenie w tryb failover baz danych dzierżawy w kolejności priorytetów.

Praca w trybie failover efektywnie przenosi bazę danych do regionu oryginalnego. Gdy baza danych zostanie przełączona w tryb failover, wszystkie otwarte połączenia są porzucane, a baza danych jest niedostępna przez kilka sekund. Aplikacje powinny być zapisywane z użyciem logiki ponawiania, aby upewnić się, że nastąpi połączenie ponownie.  Chociaż to krótkie rozłączenie często nie jest zauważalne, możesz zdecydować się na wycofywanie baz danych poza godzinami pracy. 


### <a name="run-the-repatriation-script"></a>Uruchom skrypt repatriacji
Teraz wyobraź sobie, że awaria zostanie rozwiązany i uruchomisz skrypt repatriacji.

1. W programie *POWERSHELL ISE* w skrypcie. ..\Learning Modules\Business i Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 awarii.

2. Sprawdź, czy proces synchronizacji katalogu jest nadal uruchomiony w jego wystąpieniu programu PowerShell.  W razie potrzeby uruchom ją ponownie przez ustawienie:
    * **$DemoScenario = 1** , uruchom synchronizację informacji o konfiguracji serwera dzierżawy, puli i bazy danych do wykazu
    * Naciśnij klawisz **F5** , aby uruchomić skrypt.

3.  Następnie, aby rozpocząć proces wycofywania, ustaw:
    * **$DemoScenario = 6** , wycofywanie aplikacji do jej oryginalnego regionu
    * Naciśnij klawisz **F5** , aby uruchomić skrypt odzyskiwania w nowym oknie programu PowerShell.  Repatriacja zajmie kilka minut i będzie można monitorować ją w oknie programu PowerShell.
    ![Proces repatriacji](./media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Gdy skrypt jest uruchomiony, Odśwież stronę centrum zdarzeń ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET)
    * Zwróć uwagę, że wszyscy dzierżawcy są w trybie online i są dostępni w całym procesie.

5. Po zakończeniu procesu wycofywania Odśwież centrum zdarzeń i Otwórz stronę zdarzeń dla korytarza Hawthorn. Zwróć uwagę, że ta baza danych została przywrócona do oryginalnego regionu.
    ![Wycofywane centrum zdarzeń](./media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Projektowanie aplikacji w celu zapewnienia współdziałania aplikacji i bazy danych 
Aplikacja została zaprojektowana tak, aby zawsze łączyła się z wystąpienia w tym samym regionie, w którym znajduje się baza danych dzierżawcy. Ten projekt zmniejsza opóźnienia między aplikacją a bazą danych. Ta optymalizacja zakłada, że interakcja między bazami danych jest chattier od interakcji z użytkownikiem do aplikacji.  

Bazy danych dzierżawy mogą być rozłożone między odzyskiwaniem i oryginalnymi regionami przez pewien czas podczas procesu wycofywania. Dla każdej bazy danych aplikacja szuka regionu, w którym znajduje się baza danych, przez wykonanie wyszukiwania DNS na nazwie serwera dzierżawy. W SQL Database nazwa serwera jest aliasem. Nazwa serwera aliasu zawiera nazwę regionu. Jeśli aplikacja nie znajduje się w tym samym regionie, co baza danych, przekierowuje do wystąpienia w tym samym regionie, w którym znajduje się serwer. Przekierowanie do wystąpienia w tym samym regionie, w którym znajduje się baza danych, minimalizuje opóźnienia między aplikacją a bazą danych. 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> 
> * Synchronizuj informacje o konfiguracji bazy danych i elastycznej puli do katalogu dzierżawy
> * Konfigurowanie środowiska odzyskiwania w innym regionie zawierającym aplikacje, serwery i pule
> * Replikowanie katalogów i baz danych dzierżawy do regionu odzyskiwania przy użyciu _replikacji geograficznej_
> * Przełączenie w tryb failover aplikacji i katalogu oraz baz danych dzierżawy do regionu odzyskiwania 
> * Po rozwiązaniu awarii Przewróć do oryginalnego regionu aplikacje, katalog i bazy danych dzierżawy

Więcej informacji na temat technologii Azure SQL Database zapewnia możliwość zapewnienia ciągłości działania w dokumentacji usługi [Przegląd ciągłości biznesowej](business-continuity-high-availability-disaster-recover-hadr-overview.md) .

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dodatkowe samouczki, które kompilują się po aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)