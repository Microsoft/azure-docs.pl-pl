---
title: Samouczek SaaS bazy danych na dzierżawcę
description: Wdróż i Eksploruj wielodostępną aplikację Wingtip biletów SaaS, która demonstruje wzorzec bazy danych na dzierżawcę i inne wzorce SaaS przy użyciu Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 3851e6a784d244b101c2c71c67b4b2c9a8f5cbee
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618944"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Wdróż i Eksploruj wielodostępną aplikację SaaS, która używa wzorca bazy danych na dzierżawcę z Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku opisano wdrażanie i eksplorowanie biletów Wingtip, SaaS bazę danych dla dzierżawców (Wingtip). Aplikacja używa wzorca bazy danych na dzierżawcę do przechowywania danych wielu dzierżawców. Aplikacja została zaprojektowana w celu pokazania funkcji Azure SQL Database, które upraszczają sposób włączania scenariuszy SaaS.

Pięć minut po wybraniu opcji **Wdróż na platformie Azure**masz wielodostępną aplikację SaaS. Aplikacja zawiera bazę danych, która działa w Azure SQL Database. Aplikacja jest wdrażana z trzema przykładowymi dzierżawcami, z których każda ma własną bazę danych. Wszystkie bazy danych są wdrażane w elastycznej puli SQL. Aplikacja została wdrożona w ramach subskrypcji platformy Azure. Masz pełny dostęp do eksplorowania i pracy z poszczególnymi składnikami aplikacji. Kod źródłowy aplikacji C# i skrypty zarządzania są dostępne w [repozytorium GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację Wingtip SaaS.
> - Gdzie można uzyskać kod źródłowy aplikacji i skrypty zarządzania.
> - Informacje o serwerach, pulach i bazach danych, które tworzą aplikację.
> - Jak dzierżawcy są zamapowane na dane przy użyciu *wykazu*.
> - Jak udostępnić nową dzierżawę.
> - Jak monitorować aktywność dzierżawy w aplikacji.

[Seria powiązanych samouczków](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) oferuje różne wzorce projektowania i zarządzania SaaS. Samouczki kompilują się poza tym początkowym wdrożeniem. Korzystając z samouczków, można sprawdzić dostarczone skrypty, aby zobaczyć, jak są implementowane różne wzorce SaaS. Skrypty przedstawiają sposób, w jaki funkcje SQL Database upraszczają opracowywanie aplikacji SaaS.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki tego samouczka, upewnij się, że zainstalowano Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wdrażanie aplikacji SaaS biletów Wingtip

### <a name="plan-the-names"></a>Planowanie nazw

W krokach tej sekcji podajesz wartość użytkownika, która jest używana do upewnienia się, że nazwy zasobów są unikatowe globalnie. Należy również podać nazwę grupy zasobów, która zawiera wszystkie zasoby utworzone przez wdrożenie aplikacji. W przypadku fikcyjnej osoby o nazwie Ann Finley sugerujemy:

- **Użytkownik**: *AF1* składa się z inicjałów Ann Finley i cyfr. Jeśli aplikacja zostanie wdrożona po raz drugi, użyj innej wartości. Przykładem jest AF2.
- **Grupa zasobów**: *Wingtip-DPT-AF1* wskazuje, że jest to aplikacja dla dzierżawców w bazie danych. Dołącz nazwę użytkownika AF1 do skorelowania nazwy grupy zasobów z nazwami zasobów, które zawiera.

Wybierz swoje nazwy teraz i Zapisz je w dół.

### <a name="steps"></a>Kroki

1. Aby otworzyć szablon wdrożenia Wingtip bilety SaaS bazy danych dla dzierżawy w Azure Portal, wybierz pozycję **Wdróż na platformie Azure**.

   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://azuredeploy.net/deploybutton.png)](https://aka.ms/deploywingtipdpt)

1. Wprowadź wartości w szablonie dla wymaganych parametrów.

    > [!IMPORTANT]
    > Pewne uwierzytelnianie i zapory serwera są celowo niezabezpieczone w celach demonstracyjnych. Zalecamy utworzenie nowej grupy zasobów. Nie używaj istniejących grup zasobów, serwerów ani pul. Nie używaj tej aplikacji, skryptów ani żadnych wdrożonych zasobów dla środowiska produkcyjnego. Usuń tę grupę zasobów po zakończeniu pracy z aplikacją, aby zatrzymać powiązane rozliczenia.

    - **Grupa zasobów**: wybierz pozycję **Utwórz nową**i podaj unikatową nazwę wybraną wcześniej dla grupy zasobów.
    - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej.
    - **Użytkownik**: Użyj wybranej wcześniej wartości nazwy użytkownika.

1. Wdrażanie aplikacji.

    a. Wybierz, aby wyrazić zgodę na warunki i postanowienia.

    b. Wybierz pozycję **Kup**.

1. Aby monitorować stan wdrożenia, wybierz pozycję **powiadomienia** (ikona dzwonka z prawej strony pola wyszukiwania). Wdrażanie biletów Wingtip SaaS aplikacji trwa około 5 minut.

   ![Wdrożenie powiodło się](./media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Pobierz i Odblokuj skrypty zarządzania biletów Wingtip

Podczas wdrażania aplikacji należy pobrać kod źródłowy i skrypty zarządzania.

> [!IMPORTANT]
> Zawartość wykonywalna (skrypty i biblioteki dll) może być blokowana przez system Windows, gdy pliki zip są pobierane ze źródła zewnętrznego i wyodrębniane. Postępuj zgodnie z instrukcjami, aby odblokować plik zip przed wyodrębnieniem skryptów. Odblokowywanie sprawia, że skrypty mogą być uruchamiane.

1. Przejdź do [repozytorium GitHub WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].
1. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).
1. Wybierz pozycję **Pobierz kod pocztowy**, a następnie Zapisz plik.
1. Kliknij prawym przyciskiem myszy plik **WingtipTicketsSaaS-DbPerTenant-master.zip** , a następnie wybierz polecenie **Właściwości**.
1. Na karcie **Ogólne** wybierz pozycję **Odblokuj**  >  **Zastosuj**.
1. Wybierz **przycisk OK**, a następnie Wyodrębnij pliki

Skrypty znajdują się w... \\ WingtipTicketsSaaS-DbPerTenant-Master \\ Learning modules folder.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Zaktualizuj plik konfiguracji użytkownika dla tego wdrożenia

Przed uruchomieniem jakichkolwiek skryptów należy zaktualizować grupę zasobów i wartości użytkowników w pliku konfiguracji użytkownika. Ustaw te zmienne na wartości, które były używane podczas wdrażania.

1. W ISE programu PowerShell Otwórz pozycję... \\ Moduły uczenia \\ **userconfig. PSM1**
1. Zaktualizuj **ResourceGroupName** i **nadaj nazwę** określonym wartościom dla danego wdrożenia (tylko wiersze 10 i 11).
1. Zapisz zmiany.

Te wartości są przywoływane niemal każdy skrypt.

## <a name="run-the-application"></a>Uruchamianie aplikacji

W tej aplikacji zaprezentowane są zdarzenia hosta. Typy miejsc obejmują korytarze wspólne, trefle jazzowe i trefle sportowe. W przypadku biletów Wingtip miejsc są rejestrowane jako dzierżawy. Dzierżawy umożliwiają łatwe wyświetlanie listy wydarzeń i sprzedawanie biletów klientom. Każdy z miejsc otrzymuje spersonalizowaną witrynę sieci Web, aby wyświetlić listę swoich wydarzeń i sprzedawać bilety.

Wewnętrznie w aplikacji każda dzierżawa pobiera bazę danych wdrożoną w puli elastycznej.

Centralna strona **centrum zdarzeń** zawiera listę linków do dzierżawców we wdrożeniu.

1. Użyj adresu URL, aby otworzyć centrum zdarzeń w przeglądarce internetowej: http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.NET. Zamień &lt; użytkownika &gt; na wartość użytkownika wdrożenia.

    ![Centrum zdarzeń](./media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Wybierz **klub Fabrikam Jazz** w centrum zdarzeń.

    ![Zdarzenia](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aplikacja Wingtip używa [*platformy Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md) do sterowania dystrybucją żądań przychodzących. Adres URL dostępu do strony zdarzeń dla określonej dzierżawy używa następującego formatu:

- http://events.wingtip-dpt.&lt; User &gt; . trafficmanager.NET/fabrikamjazzclub

    Części powyższego formatu zostały wyjaśnione w poniższej tabeli.

    | Część adresu URL        | Opis       |
    | :-------------- | :---------------- |
    | Events. Wingtip — DPT | Części zdarzeń aplikacji Wingtip.<br /><br /> *-DPT* *rozróżnia implementacje biletów Wingtip na podstawie* innych implementacji. Przykładami są implementacje *jednej* dzierżawy (*-sa*) lub wielodostępnej *bazy danych* (*-MT*). |
    | . * &lt; użytkownik &gt; * | *AF1* w przykładzie. |
    | . trafficmanager.net/ | Traffic Manager, podstawowy adres URL. |
    | fabrikamjazzclub | Identyfikuje dzierżawcę o nazwie Fabrikam Jazz trefl. |
    | &nbsp; | &nbsp; |

- Nazwa dzierżawy jest analizowana na podstawie adresu URL przez aplikację Events.
- Nazwa dzierżawy jest używana do tworzenia klucza.
- Ten klucz służy do uzyskiwania dostępu do katalogu w celu uzyskania lokalizacji bazy danych dzierżawy.
  - Katalog jest implementowany przy użyciu funkcji *zarządzania mapami fragmentu*.
- Centrum zdarzeń używa rozszerzonych metadanych w wykazie do konstruowania adresów URL stron listy zdarzeń dla każdej dzierżawy.

W środowisku produkcyjnym zazwyczaj należy utworzyć rekord DNS CNAME, aby [*wskazywał domenę internetową firmy*](../../traffic-manager/traffic-manager-point-internet-domain.md)na   Traffic Manager nazwę DNS.

> [!NOTE]
> Korzystanie z Menedżera ruchu w tym samouczku może nie być od razu oczywiste. Celem tej serii samouczków jest zaprezentowanie wzorców, które mogą obsłużyć skalę złożonego środowiska produkcyjnego. W takim przypadku można na przykład korzystać z wielu aplikacji sieci Web rozmieszczonych na całym świecie, w którym znajdują się bazy danych, a do kierowania między tymi wystąpieniami będzie potrzebny Menedżer ruchu.
Innym zbiorem samouczków, które ilustrują korzystanie z Menedżera ruchu, są [Narzędzia do przywracania geograficznego](../../sql-database/saas-dbpertenant-dr-geo-restore.md) i [replikacji geograficznej](../../sql-database/saas-dbpertenant-dr-geo-replication.md) . W tych samouczkach Menedżer ruchu jest używany do przełączania do wystąpienia odzyskiwania aplikacji SaaS w przypadku awarii regionalnej.

## <a name="start-generating-load-on-the-tenant-databases"></a>Rozpoczęcie generowanie obciążenia baz danych dzierżawy

Teraz, gdy aplikacja jest wdrożona, przyjrzyjmy ją.

*LoadGenerator* skrypt programu PowerShell uruchamia obciążenie, które jest uruchamiane dla wszystkich baz danych dzierżawy. Rzeczywiste obciążenie wielu aplikacji SaaS jest sporadyczne i nieprzewidywalne. W celu symulowania tego typu obciążenia Generator generuje obciążenie z losowymi skokami lub seriami aktywności dla każdej dzierżawy. Seria odbywa się w losowo określonych interwałach. Aby wzorzec obciążenia mógł nawiązać, potrwać kilka minut. Pozwól, aby generator działał przez co najmniej trzy minuty przed monitorowaniem obciążenia.

1. W ISE programu PowerShell Otwórz pozycję... \\ Moduły uczenia \\ narzędzi \\ *Demo-LoadGenerator.ps1* Script.
2. Naciśnij klawisz F5, aby uruchomić skrypt i uruchomić generator obciążenia. Pozostaw domyślne wartości parametrów teraz.
3. Zaloguj się do konta platformy Azure i wybierz subskrypcję, której chcesz użyć w razie potrzeby.

Skrypt generatora obciążenia uruchamia zadanie w tle dla każdej bazy danych w wykazie, a następnie kończy działanie. Po ponownym uruchomieniu skryptu generatora obciążenia zostaje zatrzymane wszystkie zadania w tle, które są uruchomione przed rozpoczęciem nowych.

### <a name="monitor-the-background-jobs"></a>Monitorowanie zadań w tle

Jeśli chcesz kontrolować i monitorować zadania w tle, użyj następujących poleceń cmdlet:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 akcje

*Demo-LoadGenerator.ps1* naśladuje aktywne obciążenie transakcji klientów. Poniższe kroki opisują sekwencję akcji, które *Demo-LoadGenerator.ps1* inicjować:

1. *Demo-LoadGenerator.ps1* rozpocznie się *LoadGenerator.ps1* na pierwszym planie.

    - Oba pliki. ps1 są przechowywane w obszarze folderów narzędzia do nauki modułów \\ \\ .

2. *LoadGenerator.ps1* pętle we wszystkich bazach danych dzierżawy w wykazie.

3. *LoadGenerator.ps1* uruchamia zadanie programu PowerShell w tle dla każdej bazy danych dzierżawcy:

    - Domyślnie zadania w tle są uruchamiane przez 120 minut.
    - Każde zadanie powoduje obciążenie oparte na PROCESORAch w jednej bazie danych dzierżawy przez wykonanie *sp_CpuLoadGenerator*. Intensywność i czas trwania obciążenia różnią się w zależności od `$DemoScenario` .
    - *sp_CpuLoadGenerator* pętle wokół instrukcji SELECT języka SQL, która powoduje duże obciążenie procesora CPU. Przedział czasu między problemami wybierz różni się w zależności od wartości parametrów w celu utworzenia kontrolowanego obciążenia procesora CPU. Poziomy obciążenia i interwały są losowe w celu symulowania bardziej realistycznych obciążeń.
    - Ten plik SQL jest przechowywany w obszarze *WingtipTenantDB \\ dbo \\ StoredProcedures \\ *.

4. Jeśli `$OneTime = $false` , generator obciążenia uruchamia zadania w tle, a następnie kontynuuje działanie. Co 10 sekund monitoruje je pod kątem wszystkich nowych dzierżawców, których obsługa administracyjna została zainicjowana. Jeśli ustawisz `$OneTime = $true` , LoadGenerator uruchamia zadania w tle, a następnie przestaje działać na pierwszym planie. Na potrzeby tego samouczka pozostaw `$OneTime = $false` .

   Jeśli chcesz zatrzymać lub ponownie uruchomić generator obciążenia, użyj klawiszy CTRL-C lub zatrzymania operacji Ctrl-Break.

   Jeśli pozostawisz Generator obciążenia uruchomiony na pierwszym planie, użyj innego wystąpienia programu PowerShell ISE do uruchamiania innych skryptów programu PowerShell.

&nbsp;

Przed przejściem do następnej sekcji Pozostaw Generator obciążenia uruchomiony w ramach zadania — wywoływanie stanu.

## <a name="provision-a-new-tenant"></a>Aprowizacja nowej dzierżawy

Początkowe wdrożenie tworzy trzy przykładowe dzierżawy. Teraz utworzysz kolejną dzierżawcę, aby zobaczyć wpływ wdrożenia aplikacji. W aplikacji Wingtip przepływ pracy do aprowizacji nowych dzierżawców znajduje się w [samouczku dotyczącym udostępniania i katalogowania](saas-dbpertenant-provision-and-catalog.md). W tej fazie utworzysz nową dzierżawę, która zajmie mniej niż minutę.

1. Otwórz nowy ISE programu PowerShell.
2. Otwórz... \\ Uczenie Modules\Provision i katalogu \\ *Demo-ProvisionAndCatalog.ps1*.
3. Aby uruchomić skrypt, naciśnij klawisz F5. Pozostaw wartości domyślne teraz.

   > [!NOTE]
   > Wiele skryptów Wingtip SaaS *$PSScriptRoot* używa do przeglądania folderów do wywoływania funkcji w innych skryptach. Ta zmienna jest obliczana tylko wtedy, gdy pełny skrypt jest wykonywany przez naciśnięcie klawisza F5.Wyróżnianie i uruchamianie zaznaczenia z F8 może spowodować błędy. Aby uruchomić skrypty, naciśnij klawisz F5.

Nowa baza danych dzierżawy to:

- Utworzono w elastycznej puli SQL.
- Initialize.
- Zarejestrowane w wykazie.

Po pomyślnej aprowizacji Witryna *zdarzeń* nowej dzierżawy pojawia się w przeglądarce.

![Nowa dzierżawa](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Odśwież centrum zdarzeń, aby nowa dzierżawa była widoczna na liście.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Sprawdzanie działania serwerów, pul i baz danych dzierżaw

Po rozpoczęciu pracy z kolekcją dzierżawców Przyjrzyjmy się pewnym zasobom, które zostały wdrożone.

1. W [Azure Portal](https://portal.azure.com)przejdź do listy serwerów SQL. Następnie otwórz **katalog-DPT- &lt; User &gt; ** Server.
    - Serwer wykazu zawiera dwie bazy danych: **tenantcatalog** i **basetenantdb** (baza danych szablonów, która została skopiowana w celu utworzenia nowych dzierżawców).

   ![Zrzut ekranu przedstawia stronę omówienia serwera wykazu z dwiema bazami danych.](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Wróć do listy serwerów SQL.

3. Otwórz serwer **tenants1-DPT- &lt; User &gt; ** , który zawiera bazy danych dzierżawy.

4. Zobacz następujące elementy:

    - Każda baza danych dzierżawy jest **elastyczną** bazą danych w warstwie Standardowa 50-eDTU.
    - Czerwona baza danych dla tego samego klona to baza danych dzierżawy, która została zainicjowana wcześniej.

   ![Serwer z bazami danych](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorowanie puli

Po uruchomieniu *LoadGenerator.ps1* przez kilka minut należy udostępnić wystarczającą ilość danych, aby rozpocząć przeglądanie niektórych możliwości monitorowania. Te funkcje są wbudowane w pule i bazy danych.

Przejdź do serwera **tenants1-DPT- &lt; User &gt; **i wybierz pozycję **Pool1** , aby wyświetlić wykorzystanie zasobów dla puli. Na poniższych wykresach Generator obciążenia został uruchomiony przez godzinę.

   ![Monitorowanie puli](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Pierwszy wykres, z etykietą **użycie zasobów**, pokazuje użycie jednostek eDTU puli.
- Drugi wykres pokazuje użycie jednostek eDTU z pięciu najbardziej aktywnych baz danych w puli.

Dwa wykresy ilustrują, że elastyczne pule i SQL Database są dobrze dopasowane do nieprzewidywalnych obciążeń aplikacji SaaS. Wykresy pokazują, że cztery bazy danych są poszczególnymi seriami do 40 jednostek eDTU, a jeszcze wszystkie bazy danych są wygodnie obsługiwane przez pulę 50-eDTU. Pula jednostek eDTU 50 może obsługiwać nawet większe obciążenia. Jeśli bazy danych są obsługiwane jako pojedyncze bazy danych, każdy z nich musi być S2 (50 jednostek DTU) do obsługi serii. Koszt czterech pojedynczych baz danych S2 jest niemal trzykrotnie większy niż cena puli. W rzeczywistych sytuacjach SQL Database klienci mają do 500 baz danych w pulach 200 jednostek eDTU. Aby uzyskać więcej informacji, zobacz [Samouczek dotyczący monitorowania wydajności](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- Aby uzyskać więcej informacji, zapoznaj się z dodatkowymi [samouczkami, które kompilują się na Wingtip bilety SaaS bazy danych dla dzierżawców](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Aby dowiedzieć się więcej o pulach elastycznych, zobacz [co to jest pula elastyczna usługi Azure SQL?](elastic-pool-overview.md).
- Aby dowiedzieć się więcej o zadaniach elastycznych, zobacz [Zarządzanie skalowanymi bazami danych w chmurze](../../sql-database/elastic-jobs-overview.md).
- Aby dowiedzieć się więcej o wielodostępnych aplikacjach SaaS, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć aplikację Wingtip bilety SaaS.
> - Informacje o serwerach, pulach i bazach danych, które tworzą aplikację.
> - Jak dzierżawcy są zamapowane na dane przy użyciu *wykazu*.
> - Jak zainicjować obsługę nowych dzierżawców.
> - Jak wyświetlić użycie puli do monitorowania aktywności dzierżawy.
> - Jak usunąć przykładowe zasoby, aby zatrzymać powiązane rozliczenia.

Następnie Wypróbuj [Samouczek dotyczący aprowizacji i katalogu](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant
