---
title: 'SaaS Apps: geograficznie nadmiarowe kopie zapasowe na potrzeby odzyskiwania po awarii'
description: Dowiedz się, jak używać Azure SQL Database geograficznie nadmiarowych kopii zapasowych w celu odzyskania wielodostępnej aplikacji SaaS w przypadku awarii
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 3fe6095595f5270b18536e6ef46afe4a0a5b3268
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607715"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Używanie przywracania geograficznego do odzyskiwania wielodostępnej aplikacji SaaS z kopii zapasowych bazy danych
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku przedstawiono scenariusz pełnego odzyskiwania po awarii dla wielodostępnej aplikacji SaaS wdrożonej z użyciem bazy danych na model dzierżawy. Funkcja [przywracania geograficznego](recovery-using-backups.md) umożliwia odzyskanie danych wykazu i dzierżawców z automatycznie obsługiwanych geograficznie nadmiarowych kopii zapasowych w innym regionie odzyskiwania. Po rozwiązaniu awarii należy używać [replikacji geograficznej](active-geo-replication-overview.md) do wycofywania zmienionych baz danych do ich oryginalnego regionu.

![Na diagramie przedstawiono oryginalne i pierwotne regiony, w których są dostępne aplikacje, wykazy, oryginalne lub lustrzane obrazy serwerów i pul, automatyczne kopie zapasowe w magazynie, z regionem odzyskiwania akceptującym replikację geograficzną kopii zapasowej oraz serwer i pulę dla nowych dzierżawców.](./media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Przywracanie geograficzne to najtańsze rozwiązanie do odzyskiwania po awarii dla Azure SQL Database. Jednak przywracanie z kopii zapasowych nadmiarowych może skutkować utratą danych nawet o godzinę. Może to potrwać pewien czas, w zależności od rozmiaru każdej bazy danych. 

> [!NOTE]
> Odzyskaj aplikacje z najniższym możliwym elementem RPO i RTO przy użyciu replikacji geograficznej zamiast przywracania geograficznego.

W tym samouczku przedstawiono przepływy pracy przywracania i wycofywania. Omawiane kwestie:
> [!div class="checklist"]
> 
> * Zsynchronizuj informacje o konfiguracji bazy danych i elastycznej puli z katalogiem dzierżawy.
> * Skonfiguruj lustrzane środowisko obrazu w regionie odzyskiwania obejmującym aplikacje, serwery i pule.   
> * Odzyskiwanie wykazu i dzierżawców baz danych przy użyciu funkcji przywracania geograficznego.
> * Użyj replikacji geograficznej do wycofywania katalogu dzierżawy i zmian baz danych dzierżawy po rozwiązaniu awarii.
> * Aktualizowanie katalogu w miarę przywracania każdej bazy danych (lub wycofywania) w celu śledzenia bieżącej lokalizacji aktywnej kopii bazy danych każdej dzierżawy.
> * Upewnij się, że aplikacja i baza danych dzierżawy zawsze znajdują się w tym samym regionie świadczenia usługi Azure, aby zmniejszyć opóźnienie. 
 

Przed rozpoczęciem tego samouczka należy spełnić następujące wymagania wstępne:
* Wdróż bazę danych SaaS biletów Wingtip na aplikację dzierżawców. Aby wdrożyć program w ciągu mniej niż pięciu minut, zobacz [wdrażanie i eksplorowanie bazy danych Wingtip biletów SaaS dla aplikacji dzierżawców](saas-dbpertenant-get-started-deploy.md). 
* Zainstaluj program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Wprowadzenie do wzorca przywracania geograficznego

Odzyskiwanie po awarii (DR) jest ważnym zagadnieniem dotyczącym wielu aplikacji, zarówno ze względu na zgodność, jak i ciągłości biznesowej. Jeśli wystąpi długotrwała awaria usługi, dobrze przygotowany plan odzyskiwania po awarii może zminimalizować zakłócenia działania firmy. Plan odzyskiwania po awarii na podstawie przywracania geograficznego musi mieć kilka celów:
 * Zarezerwuj wszystkie potrzebne pojemności w wybranym regionie odzyskiwania tak szybko, jak to możliwe, aby upewnić się, że jest ona dostępna do przywracania baz danych dzierżawy.
 * Ustanów dublowane środowisko odzyskiwania obrazu, które odzwierciedla oryginalną pulę i konfigurację bazy danych. 
 * Zezwalaj na anulowanie procesu przywracania w ramach usługi średniego, jeśli oryginalny region powróci do trybu online.
 * Zapewnij szybkie Inicjowanie obsługi dzierżawy, tak jak najszybciej nowe dołączenie do dzierżawy może zostać uruchomione ponownie.
 * Być zoptymalizowane pod kątem przywracania dzierżawców w kolejności priorytetów.
 * Zoptymalizowane w celu uzyskania dzierżawy w trybie online najszybciej, jak to możliwe, przez wykonywanie kroków równolegle, w przypadku praktycznych.
 * Być odporne na awarie, ponowne uruchomienie i idempotentne.
 * Wycofywanie baz danych do ich oryginalnego regionu z minimalnym wpływem na dzierżawy po rozwiązaniu awarii.  

> [!NOTE]
> Aplikacja zostanie odzyskana w sparowanym regionie regionu, w którym aplikacja jest wdrażana. Aby uzyskać więcej informacji, zobacz [regiony sparowane na platformie Azure](../../best-practices-availability-paired-regions.md).   

Ten samouczek używa funkcji Azure SQL Database i platformy Azure w celu rozwiązania tych wyzwań:

* [Azure Resource Manager szablonów](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), aby zastrzec wszystkie potrzebne pojemności tak szybko, jak to możliwe. Szablony Azure Resource Manager są używane do aprowizacji lustrzanego obrazu oryginalnych serwerów i pul elastycznych w regionie odzyskiwania. Zostanie również utworzony oddzielny serwer i Pula do aprowizacji nowych dzierżawców.
* [Elastic Database Biblioteka edcl (Client Library](elastic-database-client-library.md) ), aby utworzyć i zachować katalog bazy danych dzierżawcy. Wykaz rozszerzony obejmuje okresowe odświeżanie informacji o konfiguracji puli i bazy danych.
* [Funkcje odzyskiwania fragmentu Management](elastic-database-recovery-manager.md) Biblioteka edcl, aby zachować wpisy lokalizacji bazy danych w katalogu podczas odzyskiwania i wycofywania.  
* [Przywracanie geograficzne](../../key-vault/general/disaster-recovery-guidance.md), aby odzyskać wykaz i bazy danych dzierżawy z automatycznie obsługiwanych geograficznie nadmiarowych kopii zapasowych. 
* [Asynchroniczne operacje przywracania](../../azure-resource-manager/management/async-operations.md)wysyłane w kolejności z priorytetem dzierżawy są umieszczane w kolejce dla każdej puli przez system i przetwarzane w partiach, więc pula nie jest przeciążona. Te operacje można anulować przed lub w razie potrzeby, jeśli jest to konieczne.   
* [Replikacja geograficzna](active-geo-replication-overview.md), która pozwala na wycofywanie baz danych do oryginalnego regionu po awarii. Korzystanie z replikacji geograficznej nie powoduje utraty danych i minimalnego wpływu na dzierżawę.
* [Aliasy DNS programu SQL Server](./dns-alias-overview.md), aby umożliwić procesowi synchronizacji katalogu łączenie się z usługą Active Catalog niezależnie od jej lokalizacji.  

## <a name="get-the-disaster-recovery-scripts"></a>Pobierz skrypty odzyskiwania po awarii

Skrypty DR używane w tym samouczku są dostępne w [repozytorium usługi Wingtip w bazie danych SaaS na dzierżawcę](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania skryptów zarządzania biletami Wingtip.

> [!IMPORTANT]
> Podobnie jak w przypadku wszystkich skryptów zarządzania biletów Wingtip, skrypty DR są przykładową jakością i nie są używane w środowisku produkcyjnym.

## <a name="review-the-healthy-state-of-the-application"></a>Przejrzyj stan kondycji aplikacji
Przed rozpoczęciem procesu odzyskiwania zapoznaj się z normalnym dobrym stanem aplikacji.

1. W przeglądarce sieci Web otwórz centrum zdarzeń biletów Wingtip ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET, Zastąp &lt; użytkownika &gt; swoją wartością użytkownika wdrożenia).
    
   Przewiń w dół strony i zwróć uwagę na nazwę serwera wykazu i lokalizację w stopce. Lokalizacja to region, w którym została wdrożona aplikacja.    

   > [!TIP]
   > Przesuń wskaźnik myszy nad lokalizację, aby powiększyć ekran.

   ![Stan kondycji centrum zdarzeń w oryginalnym regionie](./media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Wybierz dzierżawę z firmy Contoso i otwórz jej stronę zdarzeń.

   W stopce Zwróć uwagę na nazwę serwera dzierżawy. Lokalizacja jest taka sama jak lokalizacja serwera wykazu.

   ![Korytarz uzgadniania firmy Contoso — oryginalny region](./media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)   

3. W [Azure Portal](https://portal.azure.com)Przejrzyj i otwórz grupę zasobów, w której została wdrożona aplikacja.

   Zwróć uwagę na zasoby i region, w którym wdrożone są składniki usługi App Service i SQL Database.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synchronizuj konfigurację dzierżawy w wykazie

W tym zadaniu zostanie rozpoczęty proces synchronizacji konfiguracji serwerów, pul elastycznych i baz danych w katalogu dzierżawcy. Te informacje są używane później do konfigurowania dublowanego środowiska obrazu w regionie odzyskiwania.

> [!IMPORTANT]
> Dla uproszczenia proces synchronizacji i inne długotrwałe procesy odzyskiwania i wycofywania są implementowane w tych przykładach jako lokalne zadania programu PowerShell lub sesje, które są uruchamiane w ramach logowania użytkownika klienta. Tokeny uwierzytelniania wydawane podczas logowania wygasają po kilku godzinach, a zadania zakończą się niepowodzeniem. W scenariuszu produkcyjnym długotrwałe procesy powinny być implementowane jako niezawodne usługi platformy Azure, które są uruchamiane w ramach jednostki usługi. Zobacz [używanie Azure PowerShell, aby utworzyć jednostkę usługi przy użyciu certyfikatu](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). 

1. W ISE programu PowerShell Otwórz plik. ..\Learning Modules\UserConfig.psm1. Zastąp `<resourcegroup>` `<user>` wartości i w wierszach 10 i 11 wartością używaną podczas wdrażania aplikacji. Zapisz plik.

2. W ISE programu PowerShell Otwórz skrypt. ..\Learning Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 awarii.

    W tym samouczku uruchomiono poszczególne scenariusze w tym skrypcie programu PowerShell, co spowoduje, że ten plik zostanie otwarty.

3. Ustaw następujące ustawienia:

    $DemoScenario = 1: uruchomienie zadania w tle, które synchronizuje informacje o konfiguracji serwera dzierżawy i puli do wykazu.

4. Aby uruchomić skrypt synchronizacji, wybierz F5. 

    Te informacje są używane później w celu zapewnienia, że odzyskiwanie spowoduje utworzenie lustrzanego obrazu serwerów, pul i baz danych w regionie odzyskiwania.  

    ![Proces synchronizacji](./media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Pozostaw okno programu PowerShell działające w tle i przejdź do pozostałej części tego samouczka.

> [!NOTE]
> Proces synchronizacji nawiązuje połączenie z wykazem za pośrednictwem aliasu DNS. Alias jest modyfikowany podczas przywracania i wycofywania w celu wskazywania usługi Active Catalog. Proces synchronizacji zachowuje aktualność katalogu z uwzględnieniem wszystkich zmian konfiguracji bazy danych lub puli wprowadzonych w regionie odzyskiwania. Podczas wycofywania te zmiany są stosowane do zasobów równoważnych w oryginalnym regionie.

## <a name="geo-restore-recovery-process-overview"></a>Przegląd procesu odzyskiwania geograficznego

Proces odzyskiwania w ramach przywracania geograficznego służy do wdrażania aplikacji i przywracania baz danych z kopii zapasowych w regionie odzyskiwania.

Proces odzyskiwania wykonuje następujące czynności:

1. Wyłącza punkt końcowy Traffic Manager platformy Azure dla aplikacji sieci Web w oryginalnym regionie. Wyłączenie punktu końcowego uniemożliwia użytkownikom nawiązywanie połączenia z aplikacją w nieprawidłowym stanie, jeśli oryginalny region przejdzie w tryb online podczas odzyskiwania.

2. Program udostępnia serwer wykazu odzyskiwania w regionie odzyskiwania, geograficznie przywraca bazę danych wykazu i aktualizuje alias activecatalog w taki sposób, aby wskazywał przywrócony serwer wykazu. Zmiana aliasu wykazu gwarantuje, że proces synchronizacji katalogu będzie zawsze synchronizowany z usługą Active Catalog.

3. Oznacza wszystkie istniejące dzierżawy w wykazie odzyskiwania jako w trybie offline, aby uniemożliwić dostęp do baz danych dzierżaw, zanim zostaną przywrócone.

4. Inicjuje obsługę wystąpienia aplikacji w regionie odzyskiwania i konfiguruje ją do korzystania z przywróconego wykazu w tym regionie. Aby zachować opóźnienie do minimum, przykładowa aplikacja została zaprojektowana tak, aby zawsze łączyła się z bazą danych dzierżawcy w tym samym regionie.

5. Inicjuje obsługę puli serwerów i elastycznych pul, w których są inicjowane nowe dzierżawy. Tworzenie tych zasobów zapewnia, że inicjowanie obsługi nowych dzierżawców nie zakłóca odzyskiwania istniejących dzierżawców.

6. Aktualizuje nowy alias dzierżawy w taki sposób, aby wskazywał serwerowi nowe bazy danych dzierżaw w regionie odzyskiwania. Zmiana tego aliasu gwarantuje, że bazy danych dla nowych dzierżawców są inicjowane w regionie odzyskiwania.
        
7. Inicjuje obsługę administracyjną serwerów i pul elastycznych w regionie odzyskiwania. Te serwery i pule są lustrzanym obrazem konfiguracji w oryginalnym regionie. Pule aprowizacji z góry zastrzegają wydajność potrzebną do przywrócenia wszystkich baz danych.

    Awaria w regionie może spowodować duże wykorzystanie zasobów dostępnych w sparowanym regionie. Jeśli korzystasz z przywracania geograficznego w przypadku odzyskiwania po awarii, zaleca się szybkie zarezerwowanie zasobów. Należy rozważyć replikację geograficzną, jeśli ma ona krytyczne znaczenie dla aplikacji w określonym regionie. 

8. Włącza punkt końcowy Traffic Manager aplikacji sieci Web w regionie odzyskiwania. Włączenie tego punktu końcowego umożliwia aplikacji Inicjowanie obsługi administracyjnej nowych dzierżawców. Na tym etapie istniejące dzierżawy są nadal w trybie offline.

9. Przesyła partie żądań w celu przywrócenia baz danych w kolejności priorytetów. 

    * Partie są zorganizowane w taki sposób, że bazy danych są przywracane równolegle między wszystkimi pulami.  

    * Żądania przywracania są przesyłane asynchronicznie, więc są one przesyłane szybko i w kolejce do wykonania w każdej puli.

    * Ponieważ żądania przywracania są przetwarzane równolegle między wszystkimi pulami, lepiej jest rozpowszechnić ważne dzierżawy w wielu pulach. 

10. Monitoruje usługę, aby określić, kiedy bazy danych są przywracane. Po przywróceniu bazy danych dzierżawy jest ona oznaczona jako online w wykazie i zostanie zarejestrowana rowversion sum dla bazy danych dzierżawy. 

    * Aplikacja może uzyskiwać dostęp do baz danych dzierżawy, gdy tylko są one oznaczone jako online w wykazie.

    * Suma wartości rowversion w bazie danych dzierżawy jest przechowywana w wykazie. Ta suma pełni funkcję odcisku palca, która umożliwia procesowi wycofywania w celu ustalenia, czy baza danych została zaktualizowana w regionie odzyskiwania.

## <a name="run-the-recovery-script"></a>Uruchamianie skryptu odzyskiwania

> [!IMPORTANT]
> Ten samouczek umożliwia przywrócenie baz danych z geograficznie nadmiarowych kopii zapasowych. Chociaż te kopie zapasowe są zazwyczaj dostępne w ciągu 10 minut, może to potrwać do godziny. Skrypt jest wstrzymywany do momentu ich udostępnienia.

Załóżmy, że wystąpi awaria w regionie, w którym aplikacja jest wdrożona, i uruchomienie skryptu odzyskiwania:

1. W ISE programu PowerShell w skrypcie. ..\Learning Modules\Business (ciągłość i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 awarii) Ustaw następującą wartość:

    $DemoScenario = 2: Odzyskaj aplikację do regionu odzyskiwania przez przywrócenie z geograficznie nadmiarowych kopii zapasowych.

2. Aby uruchomić skrypt, wybierz F5.  

    * Skrypt zostanie otwarty w nowym oknie programu PowerShell, a następnie zostanie uruchomiony zestaw zadań programu PowerShell, które są uruchamiane równolegle. Te zadania umożliwiają przywracanie serwerów, pul i baz danych do regionu odzyskiwania.

    * Region odzyskiwania to sparowany region skojarzony z regionem świadczenia usługi Azure, w którym została wdrożona aplikacja. Aby uzyskać więcej informacji, zobacz [regiony sparowane na platformie Azure](../../best-practices-availability-paired-regions.md). 

3. Monitoruj stan procesu odzyskiwania w oknie programu PowerShell.

    ![Zrzut ekranu przedstawiający okno programu PowerShell, w którym można monitorować stan procesu odzyskiwania.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Aby poznać kod zadań odzyskiwania, przejrzyj skrypty programu PowerShell w folderze. ..\Learning Modules\Business ciągłości i Recovery\DR-RestoreFromBackup\RecoveryJobs awarii.

## <a name="review-the-application-state-during-recovery"></a>Sprawdzanie stanu aplikacji podczas odzyskiwania
Gdy punkt końcowy aplikacji jest wyłączony w Traffic Manager, aplikacja jest niedostępna. Wykaz zostanie przywrócony, a wszystkie dzierżawy są oznaczone jako w trybie offline. Punkt końcowy aplikacji w regionie odzyskiwania jest następnie włączony, a aplikacja jest z powrotem w tryb online. Mimo że aplikacja jest dostępna, dzierżawy pojawiają się w trybie offline w centrum zdarzeń do momentu przywrócenia ich baz danych. Ważne jest, aby zaprojektować aplikację do obsługi baz danych dzierżaw w trybie offline.

* Po odzyskaniu bazy danych wykazu, ale zanim dzierżawcy będą z powrotem w tryb online, Odśwież centrum zdarzeń biletów Wingtip w przeglądarce internetowej.

  * Zwróć uwagę, że nazwa serwera wykazu ma teraz sufiks odzyskiwania i znajduje się w regionie odzyskiwania.

  * Zwróć uwagę, że dzierżawy, które nie zostały jeszcze przywrócone, są oznaczone jako offline i nie są wybierane.   
 
    ![Proces odzyskiwania](./media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)  

  * Jeśli otworzysz stronę zdarzeń dzierżawcy bezpośrednio, gdy dzierżawa jest w trybie offline, na stronie zostanie wyświetlone powiadomienie w trybie offline dzierżawy. Jeśli na przykład firma Contoso jest w trybie offline, spróbuj otworzyć http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET/contosoconcerthall.

    ![Zrzut ekranu przedstawiający stronę zdarzeń trybu offline.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Inicjowanie obsługi administracyjnej nowej dzierżawy w regionie odzyskiwania
Nawet przed przywróceniem baz danych dzierżaw można udostępnić nowe dzierżawy w regionie odzyskiwania. Nowe bazy danych dzierżawy inicjowane w regionie odzyskiwania są wycofywane z odzyskanymi bazami danych w późniejszym czasie.   

1. W ISE programu PowerShell w skrypcie. ..\Learning Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 awarii Ustaw następującą właściwość:

    $DemoScenario = 3: Inicjowanie obsługi nowej dzierżawy w regionie odzyskiwania.

2. Aby uruchomić skrypt, wybierz F5.

3. Po zakończeniu aprowizacji zostanie otwarta strona zdarzenia korytarzy Hawthorn w przeglądarce. 

    Zwróć uwagę, że baza danych Hawthorn korytarz znajduje się w regionie odzyskiwania.

    ![Hawthorn korytarz w regionie odzyskiwania](./media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. W przeglądarce Odśwież stronę centrum zdarzeń biletów Wingtip, aby zobaczyć dołączoną część Hawthorn. 

    Jeśli Zainicjowano obsługę Hawthorn, bez czekania na przywrócenie innych dzierżawców, inne dzierżawy mogą być nadal w trybie offline.

## <a name="review-the-recovered-state-of-the-application"></a>Przejrzyj stan odzyskany aplikacji

Po zakończeniu procesu odzyskiwania aplikacja i wszystkie dzierżawy są w pełni funkcjonalne w regionie odzyskiwania. 

1. Po wyświetleniu okna konsoli programu PowerShell wskazuje, że wszystkie dzierżawy są odzyskiwane, Odśwież centrum zdarzeń. 

    Wszyscy dzierżawcy są wyświetlani w trybie online, łącznie z nową dzierżawą, Hawthorn.

    ![Odzyskane i nowe dzierżawy w centrum zdarzeń](./media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Kliknij pozycję contoso, a następnie otwórz stronę zdarzenia. 

    W stopce należy zauważyć, że baza danych znajduje się na serwerze odzyskiwania znajdującym się w regionie odzyskiwania.

    ![Contoso w regionie odzyskiwania](./media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. W [Azure Portal](https://portal.azure.com)Otwórz listę grup zasobów.  

    Zwróć uwagę na wdrożoną grupę zasobów oraz grupę zasobów odzyskiwania z sufiksem odzyskiwania. Grupa zasobów odzyskiwania zawiera wszystkie zasoby utworzone podczas procesu odzyskiwania oraz nowe zasoby utworzone podczas przestoju. 

4. Otwórz grupę zasobów odzyskiwania i zwróć uwagę na następujące elementy:

   * Wersje odzyskiwania katalogu i serwerów tenants1 z sufiksem odzyskiwania. Przywrócone wykazy i bazy danych dzierżawy na tych serwerach mają nazwy używane w oryginalnym regionie.

   * Program SQL Server tenants2-DPT- &lt; User &gt; -Recovery. Ten serwer jest używany do aprowizacji nowych dzierżawców podczas przestoju.

   * Usługa App Service o nazwie Events-Wingtip-DPT- &lt; recoveryregion &gt; - &lt; User &gt; , czyli wystąpienie odzyskiwania aplikacji Events.

     ![Zasoby firmy Contoso w regionie odzyskiwania](./media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Otwórz program SQL Server tenants2-DPT- &lt; User &gt; -Recovery. Należy zauważyć, że zawiera on hawthornhall bazy danych i pulę elastyczną Pool1. Baza danych hawthornhall jest konfigurowana jako Elastyczna baza danych w elastycznej puli Pool1.

## <a name="change-the-tenant-data"></a>Zmień dane dzierżawy 
W tym zadaniu aktualizujesz jedną z przywróconych baz danych dzierżawy. Proces repatriacji kopiuje przywrócone bazy danych, które zostały zmienione na pierwotny region. 

1. W przeglądarce Znajdź listę zdarzeń dla korytarza z firmy Contoso, przewiń zdarzenia i zwróć uwagę na ostatnie zdarzenie, poważnie Strauss.

2. W ISE programu PowerShell w skrypcie. ..\Learning Modules\Business (ciągłość i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 awarii) Ustaw następującą wartość:

    $DemoScenario = 4: usuwanie zdarzenia z dzierżawy w regionie odzyskiwania.

3. Aby wykonać skrypt, wybierz F5.

4. Odśwież stronę zdarzeń korytarzy w firmie Contoso ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET/contosoconcerthall) i zwróć uwagę na to, że brakuje zdarzenia poważnie Strauss.

W tym punkcie samouczka odzyskasz aplikację, która jest teraz uruchomiona w regionie odzyskiwania. Zainicjowano nową dzierżawę w regionie odzyskiwania i zmodyfikowano dane jednej z przywróconych dzierżawców.  

> [!NOTE]
> Inne samouczki z przykładu nie są przeznaczone do uruchamiania z aplikacją w stanie odzyskiwania. Jeśli chcesz poznać inne samouczki, pamiętaj, aby najpierw wykonać wycofywanie aplikacji.

## <a name="repatriation-process-overview"></a>Przegląd procesu wycofywania

Proces repatriacji przywraca aplikację i jej bazy danych do oryginalnego regionu po rozwiązaniu awarii.

![Wycofywanie przywracania geograficznego](./media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png)   

Proces:

1. Powoduje zatrzymanie wszystkich trwających działań przywracania i anulowanie wszelkich zaległych lub nieaktualnych żądań przywracania bazy danych.

2. Ponownie aktywuje bazy danych dzierżawy oryginalnego regionu, które nie zostały zmienione od czasu przestoju. Te bazy danych obejmują jeszcze te, które nie zostały odzyskane, a także te, które nie zostały później zmienione. Ponownie uaktywnione bazy danych są dokładnie do ostatniego dostępu przez ich dzierżawców.

3. Inicjuje Obraz lustrzany serwera nowej dzierżawy i puli elastycznej w oryginalnym regionie. Po zakończeniu tej akcji nowy alias dzierżawy zostanie zaktualizowany tak, aby wskazywał na ten serwer. Aktualizacja aliasu powoduje, że nowe dołączenie do dzierżawy występuje w oryginalnym regionie zamiast w regionie odzyskiwania.

3. Używa replikacji geograficznej do przenoszenia wykazu do regionu oryginalnego z regionu odzyskiwania.

4. Aktualizuje konfigurację puli w oryginalnym regionie, tak aby była spójna ze zmianami wprowadzonymi w regionie odzyskiwania podczas przestoju.

5. Tworzy wymagane serwery i pule do hostowania nowych baz danych utworzonych podczas przestoju.

6. Używa replikacji geograficznej do wycofywania przywróconych baz danych dzierżawy, które zostały zaktualizowane po przywróceniu i wszystkich nowych baz danych dzierżawy, które zostały zainicjowane podczas przestoju. 

7. Czyści zasoby utworzone w regionie odzyskiwania podczas procesu przywracania.

Aby ograniczyć liczbę baz danych dzierżaw, które muszą zostać cofnięte, kroki od 1 do 3 są wykonywane natychmiastowo.  

Krok 4 jest wykonywany tylko wtedy, gdy katalog w regionie odzyskiwania został zmodyfikowany podczas przestoju. Katalog jest aktualizowany w przypadku utworzenia nowych dzierżawców lub zmiany konfiguracji bazy danych lub puli w regionie odzyskiwania.

Ważne jest, aby krok 7 spowodowało minimalne zakłócenia dzierżaw i żadne dane nie zostały utracone. Aby osiągnąć ten cel, proces używa replikacji geograficznej.

Przed replikacją geograficzną każdej bazy danych, odpowiednia baza danych w pierwotnym regionie zostanie usunięta. Baza danych w regionie odzyskiwania jest następnie replikowana geograficznie, tworząc replikę pomocniczą w pierwotnym regionie. Po zakończeniu replikacji dzierżawa zostanie oznaczona jako przełączony w tryb offline w wykazie, co spowoduje przerwanie wszelkich połączeń z bazą danych w regionie odzyskiwania. Baza danych zostanie przełączona w tryb failover, co spowoduje, że wszystkie oczekujące transakcje przestaną być przetwarzane na pomocniczą, więc żadne dane nie zostaną utracone. 

W przypadku przejścia w tryb failover role bazy danych są odwracane. Pomocniczy w pierwotnym regionie jest podstawową bazą danych do odczytu i zapisu, a baza danych w regionie odzyskiwania jest pomocniczą kopią tylko do odczytu. Wpis dzierżawy w wykazie został zaktualizowany w celu odwoływania się do bazy danych w oryginalnym regionie, a dzierżawa jest oznaczona jako online. W tym momencie wycofywanie bazy danych zostało zakończone. 

Aplikacje powinny być zapisywane z użyciem logiki ponowień, aby upewnić się, że automatycznie łączą się w przypadku przerwania połączeń. W przypadku użycia wykazu do brokera ponownego połączenia program nawiązuje połączenie z wycofywaną bazą danych w oryginalnym regionie. Chociaż krótkie rozłączenie często nie jest zauważalne, możesz zdecydować się na wycofywanie baz danych poza godzinami pracy.

Po repatriacji bazy danych można usunąć pomocniczą bazę danych w regionie odzyskiwania. Baza danych w oryginalnym regionie jest ponownie oparta na przywracaniu geograficznym na potrzeby ochrony za pomocą usługi DR.

W kroku 8 zasoby w regionie odzyskiwania, w tym serwery odzyskiwania i pule, są usuwane.

## <a name="run-the-repatriation-script"></a>Uruchom skrypt repatriacji
Wyobraź sobie, że przestój zostanie rozwiązany i uruchomisz skrypt repatriacji.

Po wykonaniu samouczka skrypt natychmiast ponownie aktywuje klubu z firmy Fabrikam Jazz i Dogwood Dojo w oryginalnym regionie, ponieważ nie są zmieniane. Następnie nowy dzierżawca, dzierżawa Hawthorn i contoso wspólnie z korytarzem, ponieważ został zmodyfikowany. Skrypt również wycofywanie wykazuje, który został zaktualizowany po zainicjowaniu obsługi administracyjnej Hawthorn.
  
1. W ISE programu PowerShell w skrypcie. ..\Learning Modules\Business i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 awarii Sprawdź, czy proces synchronizacji katalogu jest nadal uruchomiony w jego wystąpieniu programu PowerShell. W razie potrzeby uruchom ją ponownie przez ustawienie:

    $DemoScenario = 1: Rozpocznij synchronizowanie informacji o konfiguracji serwera dzierżawy, puli i bazy danych z wykazem.

    Aby uruchomić skrypt, wybierz F5.

2.  Następnie, aby rozpocząć proces wycofywania, ustaw:

    $DemoScenario = 5: wycofywanie aplikacji do jej oryginalnego regionu.

    Aby uruchomić skrypt odzyskiwania w nowym oknie programu PowerShell, wybierz klawisz F5. Wycofywanie trwa kilka minut i może być monitorowane w oknie programu PowerShell.

3. Gdy skrypt jest uruchomiony, Odśwież stronę centrum zdarzeń ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET).

    Zwróć uwagę, że wszyscy dzierżawcy są w trybie online i są dostępni w całym procesie.

4. Wybierz klub Fabrikam Jazz, aby go otworzyć. Jeśli dzierżawa nie została zmodyfikowana, zwróć uwagę z stopki, że serwer został już przywrócony do oryginalnego serwera.

5. Otwórz lub Odśwież stronę zdarzeń korytarzy w firmie Contoso. Zwróć uwagę na to, że początkowo baza danych nadal znajduje się na serwerze odzyskiwania. 

6. Odśwież stronę zdarzeń korytarzy firmy Contoso w przypadku zakończenia procesu wycofywania i Zauważ, że baza danych znajduje się teraz w oryginalnym regionie.

7. Odśwież centrum zdarzeń ponownie i Otwórz korytarz Hawthorn. Należy zauważyć, że jego baza danych znajduje się również w pierwotnym regionie. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Wyczyść zasoby regionu odzyskiwania po repatriacji
Po zakończeniu procesu wycofywania można bezpiecznie usunąć zasoby w regionie odzyskiwania. 

> [!IMPORTANT]
> Natychmiast usuń te zasoby, aby zatrzymać wszystkie rozliczenia.

Proces przywracania tworzy wszystkie zasoby odzyskiwania w grupie zasobów odzyskiwania. Proces oczyszczania usuwa tę grupę zasobów i usuwa wszystkie odwołania do zasobów z wykazu. 

1. W programie PowerShell ISE w skrypcie. ..\Learning Modules\Business (ciągłość i Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 awarii) Ustaw:
    
    $DemoScenario = 6: usuwanie przestarzałych zasobów z regionu odzyskiwania.

2. Aby uruchomić skrypt, wybierz F5.

Po oczyszczeniu skryptów aplikacja zostanie ponownie uruchomiona. W tym momencie możesz ponownie uruchomić skrypt lub wypróbować inne samouczki.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Projektowanie aplikacji w celu upewnienia się, że aplikacja i baza danych są wspólnie zlokalizowane 
Aplikacja została zaprojektowana tak, aby zawsze łączyła się z wystąpienia w tym samym regionie, w którym znajduje się baza danych dzierżawy. Ten projekt zmniejsza opóźnienia między aplikacją a bazą danych. Ta optymalizacja zakłada, że interakcja między bazami danych jest chattier od interakcji z użytkownikiem do aplikacji.  

Bazy danych dzierżawy mogą być rozłożone między odzyskiwaniem i oryginalnymi regionami przez pewien czas podczas procesu wycofywania. Dla każdej bazy danych aplikacja szuka regionu, w którym znajduje się baza danych, przez wykonanie wyszukiwania DNS na nazwie serwera dzierżawy. Nazwa serwera jest aliasem. Nazwa serwera aliasu zawiera nazwę regionu. Jeśli aplikacja nie znajduje się w tym samym regionie, co baza danych, przekierowuje do wystąpienia w tym samym regionie, w którym znajduje się serwer. Przekierowanie do wystąpienia w tym samym regionie, w którym baza danych minimalizuje opóźnienia między aplikacją a bazą danych.  

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> 
> * Użyj wykazu dzierżawców do przechowywania okresowo odświeżanych informacji konfiguracyjnych, co pozwala na Tworzenie dublowanego środowiska odzyskiwania obrazu w innym regionie.
> * Odzyskiwanie baz danych do regionu odzyskiwania przy użyciu funkcji przywracania geograficznego.
> * Zaktualizuj katalog dzierżawy, aby odzwierciedlić przywrócone lokalizacje bazy danych dzierżawy. 
> * Użyj aliasu DNS, aby umożliwić aplikacji łączenie się z wykazem dzierżawy w całej konfiguracji.
> * Po rozwiązaniu awarii Użyj replikacji geograficznej do wycofywania odzyskanych baz danych do ich oryginalnego regionu.

Wypróbuj [odzyskiwanie po awarii dla wielodostępnej aplikacji SaaS za pomocą samouczka replikacji geograficznej bazy danych](./saas-dbpertenant-dr-geo-replication.md) , aby dowiedzieć się, jak za pomocą replikacji geograficznej znacznie skrócić czas wymagany do odzyskania wielodostępnej aplikacji wielodostępnej w dużej skali.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Dodatkowe samouczki, które kompilują się po aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)