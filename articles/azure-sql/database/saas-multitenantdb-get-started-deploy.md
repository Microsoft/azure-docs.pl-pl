---
title: Wdrażanie aplikacji SaaS Database z wieloma dzierżawcami podzielonej na fragmenty
description: Wdróż i Eksploruj podzielonej na fragmenty Wingtip bilety SaaS wielodostępną aplikację bazy danych, która demonstruje wzorce SaaS przy użyciu Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/16/2018
ms.openlocfilehash: 4de7e428bff0feaafdec00b0c0014bbaf6acb917
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790971"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Wdrażanie i eksplorowanie aplikacji podzielonej na fragmenty z wieloma dzierżawcami
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku opisano wdrażanie i eksplorowanie przykładowej wielodostępnej aplikacji SaaS o nazwie bilety Wingtip. Aplikacja biletów Wingtip została zaprojektowana w celu pokazania funkcji Azure SQL Database, które upraszczają implementację scenariuszy SaaS.

Ta implementacja aplikacji biletów Wingtip korzysta ze wzorca bazy danych z wieloma dzierżawcami podzielonej na fragmenty. Fragmentowania jest według identyfikatora dzierżawy. Dane dzierżawy są dystrybuowane do określonej bazy danych zgodnie z wartościami identyfikatora dzierżawy. 

Ten wzorzec bazy danych umożliwia przechowywanie co najmniej jednej dzierżawy w każdym fragmentu lub bazie danych. Możesz zoptymalizować się pod kątem najniższych kosztów, aby każda baza danych była współdzielona przez wielu dzierżawców. Lub można zoptymalizować pod kątem izolacji, ponieważ każda baza danych przechowuje tylko jedną dzierżawę. Wybór optymalizacji można przeprowadzić niezależnie dla każdej konkretnej dzierżawy. Wybór można wykonać, gdy dzierżawca jest po raz pierwszy zapisany, lub później możesz zmienić zdanie. Aplikacja została zaprojektowana tak, aby działała prawidłowo.

## <a name="app-deploys-quickly"></a>Szybkie wdrażanie aplikacji

Aplikacja działa w chmurze platformy Azure i używa Azure SQL Database. W poniższej sekcji wdrożenia znajduje się przycisk "niebieskie **wdrażanie na platformie Azure** ". Po naciśnięciu przycisku aplikacja jest w pełni wdrażana w ramach subskrypcji platformy Azure w ciągu pięciu minut. Masz pełny dostęp do pracy z pojedynczymi składnikami aplikacji.

Aplikacja jest wdrażana z danymi dla trzech przykładowych dzierżawców. Dzierżawy są przechowywane razem w jednej wielodostępnej bazie danych.

Każdy może pobrać kod źródłowy języka C# i programu PowerShell dla biletów Wingtip z [repozytorium GitHub][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Zapoznaj się z tym samouczkiem

> [!div class="checklist"]
> - Jak wdrożyć aplikację Wingtip bilety SaaS.
> - Gdzie można uzyskać kod źródłowy aplikacji oraz skrypty zarządzania.
> - Informacje o serwerach i bazach danych, które tworzą aplikację.
> - Jak dzierżawcy są zamapowane na dane przy użyciu *wykazu*.
> - Jak udostępnić nową dzierżawę.
> - Jak monitorować aktywność dzierżawy w aplikacji.

Dostępna jest seria powiązanych samouczków, które kompilują po wdrożeniu początkowym. Samouczki eksplorują zakres wzorców projektowania i zarządzania SaaS. Podczas pracy nad samouczkami zaleca się przechodzenie przez dostarczone skrypty, aby zobaczyć, w jaki sposób są implementowane różne wzorce SaaS.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Najnowsza Azure PowerShell jest zainstalowana. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Wdrażanie aplikacji biletów Wingtip

### <a name="plan-the-names"></a>Planowanie nazw

W krokach tej sekcji podajesz wartość *użytkownika* , która jest używana do zapewnienia, że nazwy zasobów są unikatowe globalnie, oraz nazwę *grupy zasobów* , która zawiera wszystkie zasoby utworzone przez wdrożenie aplikacji. W przypadku osoby o nazwie *Ann Finley* sugerujemy:
- *User:* **AF1**  *(ich inicjały, plus cyfra). Jeśli aplikacja jest wdrażana po raz drugi, użyj innej wartości (np. AF2).*
- *Grupa zasobów:* **Wingtip-MT-AF1** *(Wingtip-MT wskazuje, że jest to aplikacja podzielonej na fragmenty z wieloma dzierżawcami. dołączenie nazwy użytkownika AF1 powoduje skorelowanie nazw grup zasobów z nazwami zasobów, które zawiera.)*

Wybierz swoje nazwy teraz i Zapisz je w dół. 

### <a name="steps"></a>Kroki

1. Kliknij poniższy niebieski przycisk **Wdróż na platformie Azure** .
   - Zostanie otwarty Azure Portal z szablonem wdrożenia SaaS biletów Wingtip.

     [![Przycisk wdrażania na platformie Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Wprowadź wymagane wartości parametrów wdrożenia.

    > [!IMPORTANT]
    > Dla tej demonstracji nie należy używać żadnych istniejących wcześniej grup zasobów, serwerów ani pul. Zamiast tego wybierz opcję **Utwórz nową grupę zasobów**. Po zakończeniu pracy z aplikacją usuń tę grupę zasobów, aby zatrzymać ich rozliczanie.
    > Nie należy używać tej aplikacji ani żadnych tworzonych przez nie zasobów dla środowiska produkcyjnego. Niektóre aspekty uwierzytelniania i ustawienia zapory serwera są celowo zabezpieczane w aplikacji w celu ułatwienia pokazu.

    - W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, a następnie podaj **nazwę** grupy zasobów (z uwzględnieniem wielkości liter).
        - Z listy rozwijanej wybierz **lokalizację** .
    - Dla **użytkownika** — zalecamy wybranie krótkiej wartości **użytkownika** .

1. **Wdróż aplikację**.

    - Kliknij, aby zaakceptować warunki i postanowienia.
    - Kliknij pozycję **Kup**.

1. Monitoruj stan wdrożenia, klikając pozycję **powiadomienia**, czyli ikonę dzwonka z prawej strony pola wyszukiwania. Wdrażanie aplikacji Wingtip trwa około 5 minut.

   ![wdrażanie zakończyło się pomyślnie](./media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Pobierz i Odblokuj skrypty zarządzania

Podczas wdrażania aplikacji Pobierz kod źródłowy aplikacji i skrypty zarządzania.

> [!NOTE]
> Zawartość wykonywalna (skrypty, biblioteki dll) może być blokowana przez system Windows, gdy pliki zip są pobierane ze źródła zewnętrznego i wyodrębniane. Podczas wyodrębniania skryptów z pliku zip wykonaj następujące kroki, aby odblokować plik. zip przed wyodrębnieniem. Odblokowanie pliku zip pozwala upewnić się, że skrypty mogą być uruchamiane.

1. Przejdź do [WingtipTicketsSaaS-MultiTenantDb repozytorium GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Kliknij pozycję **Klonuj lub Pobierz**.
3. Kliknij pozycję **Pobierz kod pocztowy** i Zapisz plik.
4. Kliknij prawym przyciskiem myszy plik **WingtipTicketsSaaS-MultiTenantDb-master.zip** i wybierz polecenie **Właściwości**.
5. Na karcie **Ogólne** wybierz pozycję **Odblokuj**, a następnie kliknij pozycję **Zastosuj**.
6. Kliknij przycisk **OK**.
7. Wyodrębnij pliki.

Skrypty znajdują się w *... \\ WingtipTicketsSaaS-MultiTenantDb-Master \\ Learning modules \\* folder.

## <a name="update-the-configuration-file-for-this-deployment"></a>Zaktualizuj plik konfiguracji dla tego wdrożenia

Przed uruchomieniem jakichkolwiek skryptów, należy ustawić *grupę zasobów* i wartości *użytkownika* w **userconfig. PSM1**. Ustaw te zmienne na te same wartości, które zostały ustawione podczas wdrażania.

1. Otwórz... \\ Moduły uczenia \\ *userconfig. PSM1* w programie *PowerShell ISE*.
2. Zaktualizuj *ResourceGroupName* i *nadaj nazwę* określonym wartościom dla danego wdrożenia (tylko wiersze 10 i 11).
3. Zapisz zmiany.

Wartości ustawione w tym pliku są używane przez wszystkie skrypty, dlatego ważne jest, aby były poprawne. W przypadku ponownego wdrożenia aplikacji należy wybrać różne wartości dla użytkowników i grup zasobów. Następnie ponownie zaktualizuj plik UserConfig. PSM1 przy użyciu nowych wartości.

## <a name="run-the-application"></a>Uruchamianie aplikacji

W aplikacji Wingtip dzierżawcy są miejscami. Miejscem może być skoncentrowanie się, klub sportowy lub jakakolwiek inna lokalizacja, która hostuje zdarzenia. Miejsca zarejestrowane w usłudze Wingtip jako klienci i identyfikator dzierżawy są generowane dla każdego miejsca. Każdy z miejsc zawiera wszystkie nadchodzące wydarzenia w Wingtip, dzięki czemu publiczna może kupić bilety do wydarzeń.

Każdy z miejsc otrzymuje spersonalizowaną aplikację sieci Web, aby wyświetlić listę swoich wydarzeń i sprzedawać bilety. Każda aplikacja sieci Web jest niezależna i odizolowana od innych dzierżawców. Wewnętrznie w Azure SQL Database wszystkie dane dla poszczególnych dzierżawców są domyślnie przechowywane w bazie danych podzielonej na fragmenty z wieloma dzierżawami. Wszystkie dane są znakowane przy użyciu identyfikatora dzierżawy.

Centralna Strona sieci Web **centrum zdarzeń** zawiera listę linków do dzierżawców w określonym wdrożeniu. Wykonaj następujące kroki, aby skorzystać z witryny sieci Web **centrum zdarzeń** i pojedynczej aplikacji internetowej:

1. Otwórz **centrum zdarzeń** w przeglądarce internetowej:
   - http://events.wingtip-mt.&lt; User &gt; . trafficmanager.NET &nbsp; *(Zamień &lt; użytkownika &gt; na wartość użytkownika wdrożenia).*

     ![centrum zdarzeń](./media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Kliknij przycisk **Fabrikam Jazz Club** w **Centrum zdarzeń**.

   ![Zdarzenia](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Aby kontrolować dystrybucję żądań przychodzących, aplikacja Wingtip używa [platformy Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). Strona zdarzenia dla każdej dzierżawy zawiera nazwę dzierżawy w adresie URL. Każdy adres URL zawiera również określoną wartość użytkownika. Każdy adres URL jest przestrzegany w pokazanym formacie, wykonując następujące czynności:

- http://events.wingtip-mt.&lt; User &gt; . trafficmanager.NET/*fabrikamjazzclub*

1. Aplikacja Events analizuje nazwę dzierżawy na podstawie adresu URL. Nazwa dzierżawy jest *fabrikamjazzclub* w powyższym PRZYKŁADowym adresie URL.
2. Następnie aplikacja miesza nazwę dzierżawy w celu utworzenia klucza w celu uzyskania dostępu do wykazu przy użyciu funkcji [zarządzania mapami fragmentu](elastic-scale-shard-map-management.md).
3. Aplikacja odnajdzie klucz w katalogu i uzyskuje odpowiadającą lokalizację bazy danych dzierżawy.
4. Aplikacja używa informacji o lokalizacji, aby znaleźć i uzyskać dostęp do tej bazy danych, która zawiera wszystkie dane dla dzierżawy.

### <a name="events-hub"></a>Centrum zdarzeń

1. **Centrum zdarzeń** wyświetla listę wszystkich dzierżawców zarejestrowanych w wykazie i ich miejsc.
2. **Centrum zdarzeń** używa rozszerzonych metadanych w wykazie, aby pobrać nazwę dzierżawy skojarzoną z każdym mapowaniem w celu skonstruowania adresów URL.

W środowisku produkcyjnym zwykle tworzony jest rekord DNS CNAME, który [wskazuje firmową domenę internetową](../../traffic-manager/traffic-manager-point-internet-domain.md) do profilu usługi Traffic Manager.

## <a name="start-generating-load-on-the-tenant-databases"></a>Rozpoczęcie generowanie obciążenia baz danych dzierżawy

Teraz, gdy aplikacja jest wdrożona, przyjrzyjmy ją. *LoadGenerator* skrypt programu PowerShell uruchamia obciążenie dla każdej dzierżawy. Rzeczywiste obciążenie wielu aplikacji SaaS jest zwykle sporadyczne i nieprzewidywalne. W celu symulowania tego typu obciążenia Generator tworzy obciążenie rozproszone dla wszystkich dzierżawców. Obciążenie obejmuje losowe przyrosty dla każdej dzierżawy występujące w interwałach losowych. Aby wzorzec obciążenia był uruchamiany przez kilka minut, najlepszym rozwiązaniem jest umożliwienie generatora przez co najmniej trzy minuty przed monitorowaniem obciążenia.

1. W *ISE programu PowerShell* Otwórz pozycję... \\ Moduły uczenia \\ narzędzi \\ *Demo-LoadGenerator.ps1* Script.
2. Naciśnij klawisz **F5**, aby uruchomić skrypt i generator obciążenia (na razie pozostaw bez zmian wartości domyślne parametrów).

Skrypt *Demo-LoadGenerator.ps1* otwiera kolejną sesję programu PowerShell, w której jest uruchamiany Generator obciążenia. Generator obciążenia jest uruchamiany w tej sesji jako zadanie pierwszego planu, które wywołuje zadania generowania obciążenia w tle, po jednym dla każdej dzierżawy.

Po rozpoczęciu zadania pierwszego planu pozostanie ono w stanie niewywołującym. Zadanie uruchamia dodatkowe zadania w tle dla wszystkich nowych dzierżawców, które są następnie inicjowane.

Zamknięcie sesji programu PowerShell powoduje zatrzymanie wszystkich zadań.

Możesz chcieć ponownie uruchomić sesję generatora obciążenia, aby użyć innych wartości parametrów. Jeśli tak, Zamknij sesję generacji programu PowerShell, a następnie ponownie uruchom *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Inicjowanie obsługi administracyjnej nowej dzierżawy w bazie danych podzielonej na fragmenty

Początkowe wdrożenie obejmuje trzy przykładowe dzierżawy w bazie danych *Tenants1* . Utwórzmy kolejną dzierżawę i obserwuj jej wpływ na wdrożoną aplikację. W tym kroku należy nacisnąć jeden klawisz, aby utworzyć nową dzierżawę:

1. Otwórz... \\ Moduły szkoleniowe udostępniają \\ i wykazują \\ *Demo-ProvisionTenants.ps1* w programie *PowerShell ISE*.
2. Naciśnij klawisz **F5** (nie **F8**), aby uruchomić skrypt (pozostaw wartości domyślne teraz).

   > [!NOTE]
   > Skrypty programu PowerShell należy uruchamiać tylko przez naciśnięcie klawisza **F5** , a nie przez naciśnięcie klawisza **F8** w celu uruchomienia wybranej części skryptu. Problem z **F8** polega na tym, że zmienna *$PSScriptRoot* nie jest szacowana. Ta zmienna jest wymagana przez wiele skryptów do nawigowania po folderach, wywoływanie innych skryptów lub importowanie modułów.

Nowa dzierżawa wyścigów z czerwonym klonem jest dodawana do bazy danych *Tenants1* i zarejestrowana w wykazie. W przeglądarce zostanie otwarta Witryna **zdarzeń** sprzedaży biletów nowej dzierżawy:

![Nowa dzierżawa](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Odśwież **centrum zdarzeń**, a nowa dzierżawa zostanie wyświetlona na liście.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Inicjowanie obsługi administracyjnej nowej dzierżawy we własnej bazie danych

Model z wieloma dzierżawcami podzielonej na fragmenty umożliwia wybranie, czy należy udostępnić nową dzierżawę do bazy danych zawierającej inne dzierżawców, czy też do własnej bazy danych. Dzierżawa izolowana w swojej bazie danych ma następujące zalety:

- Wydajność bazy danych dzierżawy może być zarządzana bez konieczności naruszania potrzeb innych dzierżawców.
- W razie potrzeby baza danych może zostać przywrócona do wcześniejszego punktu w czasie, ponieważ nie wpłynie to na inne dzierżawy.

W przypadku baz danych z wieloma dzierżawcami możesz wybrać klientów z bezpłatnymi wersjami próbnymi lub klientów w gospodarce. Każdą dzierżawę w warstwie Premium można umieścić w swojej własnej dedykowanej bazie danych. Jeśli tworzysz wiele baz danych, które zawierają tylko jedną dzierżawę, możesz zarządzać nimi wszystkie zbiorowo w puli elastycznej w celu zoptymalizowania kosztów zasobów.

Następnie oferujemy inną dzierżawcę, tym razem z własną bazą danych:

1. W... \\ Moduły szkoleniowe udostępniają \\ i wykazują \\ *Demo-ProvisionTenants.ps1*, modyfikują *$TenantName* do **Salix Salsa**, *$VenueType* do **odpowiedzialna** i *$Scenario* do **2**.

2. Naciśnij klawisz **F5** , aby ponownie uruchomić skrypt.
    - Naciśnięcie klawisza **F5** spowoduje zarezerwowanie nowej dzierżawy w oddzielnej bazie danych. Baza danych i dzierżawca są zarejestrowane w wykazie. Następnie w przeglądarce zostanie otwarta strona zdarzenia dzierżawy.

   ![Strona zdarzeń Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Przewiń stronę do dołu. Na banerze zobaczysz nazwę bazy danych, w której są przechowywane dane dzierżawy.

3. Odśwież **centrum zdarzeń** , a na liście zostaną wyświetlone dwa nowe dzierżawy.

## <a name="explore-the-servers-and-tenant-databases"></a>Eksplorowanie serwerów i baz danych dzierżawy

Teraz korzystamy z niektórych wdrożonych zasobów:

1. W [Azure Portal](https://portal.azure.com)przejdź do listy grup zasobów. Otwórz grupę zasobów utworzoną podczas wdrażania aplikacji.

   ![grupa zasobów](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Kliknij pozycję **katalog — &lt; MT &gt; User** Server. Serwer wykazu zawiera dwie bazy danych o nazwie *tenantcatalog* i *basetenantdb*. Baza danych *basetenantdb* jest pustą bazą danych szablonów. Jest on kopiowany w celu utworzenia nowej bazy danych dzierżawy, niezależnie od tego, czy jest używany dla wielu dzierżawców, czy tylko dla jednej dzierżawy.

   ![serwer wykazu](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Wróć do grupy zasobów i wybierz serwer *tenants1-MT* , który zawiera bazy danych dzierżawy.
    - Baza danych tenants1 to baza danych z wieloma dzierżawami, w której znajdują się pierwotne trzy dzierżawy oraz pierwsza dodana dzierżawa. Jest ona konfigurowana jako standardowa baza danych 50 jednostek DTU.
    - Baza danych **salixsalsa** przechowuje jako jedyną dzierżawę Salix Salsa odpowiedzialna. Jest ona konfigurowana domyślnie jako baza danych Standard Edition z 50 DTU.

   ![serwer dzierżawców](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Monitorowanie wydajności bazy danych

Jeśli Generator obciążenia był uruchomiony przez kilka minut, dostępna jest wystarczająca ilość danych telemetrycznych, aby przyjrzeć się funkcjom monitorowania w Azure Portal.

1. Przejdź do serwera **&lt; użytkownika &gt; tenants1-MT** , a następnie kliknij pozycję **tenants1** , aby wyświetlić wykorzystanie zasobów dla bazy danych, która ma cztery dzierżawców. Każda dzierżawa podlega sporadycznemu obciążeniu od generatora obciążenia:

   ![monitorowanie tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Wykres wykorzystania jednostek DTU dobrze ilustruje, w jaki sposób baza danych z wieloma dzierżawcami może obsługiwać nieprzewidywalne obciążenie dla wielu dzierżawców. W takim przypadku Generator obciążenia stosuje sporadyczne obciążenie o około 30 DTU do każdej dzierżawy. To obciążenie jest równe 60% wykorzystania bazy danych DTU 50. Wartości szczytowe przekraczające 60% są wynikiem obciążenia zastosowanego do więcej niż jednej dzierżawy w tym samym czasie.

2. Przejdź do serwera **&lt; użytkownika &gt; tenants1-MT** i kliknij bazę danych **salixsalsa** . Możesz zobaczyć użycie zasobów w tej bazie danych, która zawiera tylko jedną dzierżawę.

   ![Baza danych salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Generator obciążenia stosuje podobne obciążenie do każdej dzierżawy, niezależnie od tego, która baza danych znajduje się w każdej dzierżawie. W przypadku tylko jednej dzierżawy w bazie danych **salixsalsa** można zobaczyć, że baza danych może utrzymać znacznie wyższe obciążenie niż baza danych z kilkoma dzierżawcami. 

### <a name="resource-allocations-vary-by-workload"></a>Alokacje zasobów różnią się w zależności od obciążenia

Czasami baza danych z wieloma dzierżawcami wymaga większej ilości zasobów, niż jest to baza danych o pojedynczej dzierżawie, ale nie zawsze. Optymalna alokacja zasobów zależy od konkretnych cech obciążeń dla dzierżawców w systemie.

Obciążenia generowane przez skrypt generatora obciążenia są przeznaczone tylko do celów ilustracyjnych.

## <a name="additional-resources"></a>Dodatkowe zasoby

- Aby dowiedzieć się więcej o wielodostępnych aplikacjach SaaS, zobacz [wzorce projektowe dla wielodostępnych aplikacji SaaS](saas-tenancy-app-design-patterns.md).

- Aby dowiedzieć się więcej o pulach elastycznych, zobacz:

  - [Pule elastyczne ułatwiają zarządzanie wieloma bazami danych w Azure SQL Database i skalowanie ich.](elastic-pool-overview.md)
  - [Scaling out with Azure SQL Database (Skalowanie w poziomie za pomocą usługi Azure SQL Database)](elastic-scale-introduction.md)

## <a name="next-steps"></a>Następne kroki

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Jak wdrożyć bilety Wingtip SaaS aplikacji bazy danych z wieloma dzierżawami.
> - Informacje o serwerach i bazach danych, które tworzą aplikację.
> - Dzierżawy są mapowane na ich dane przy użyciu *wykazu*.
> - Jak udostępnić nowe dzierżawy w bazie danych z wieloma dzierżawcami i bazą danych z jedną dzierżawą.
> - Jak wyświetlić użycie puli do monitorowania aktywności dzierżawy.
> - Jak usunąć przykładowe zasoby, aby zatrzymać powiązane rozliczenia.

Wypróbuj teraz [Samouczek dotyczący aprowizacji i katalogu](saas-multitenantdb-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: /powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Przycisk służący do wdrażania na platformie Azure."