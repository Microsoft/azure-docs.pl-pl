---
title: Zapytania ad hoc dotyczące raportowania w wielu bazach danych
description: Wykonaj zapytania dotyczące raportów ad hoc w wielu bazach danych Azure SQL w przykładowej aplikacji wielodostępnej.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/30/2018
ms.openlocfilehash: 800592b7a8b263fea2883fdd3e030f78f72647dd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459928"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-databases-azure-sql-database"></a>Uruchamianie zapytań analizy ad hoc w wielu bazach danych (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku uruchomiono zapytania rozproszone w całym zestawie baz danych dzierżawy, aby umożliwić interakcyjne raportowanie ad hoc. Te zapytania mogą wyodrębnić szczegółowe dane dotyczące codziennych danych operacyjnych Wingtip biletów SaaS aplikacji. Aby wykonać te wyodrębnienia, należy wdrożyć dodatkową bazę danych analitycznych na serwerze wykazu i użyć zapytania elastycznego w celu włączenia zapytań rozproszonych.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Jak wdrożyć bazę danych raportowania ad hoc
> * Jak uruchamiać zapytania rozproszone we wszystkich bazach danych dzierżaw


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono Wingtip biletów SaaS aplikację bazy danych z wieloma dzierżawcami. Aby wdrożyć program w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji bazy danych z obsługą wielu dzierżawców Wingtip SaaS](saas-multitenantdb-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) jest zainstalowany. Aby pobrać i zainstalować program SSMS, zobacz [pobieranie SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Wzorzec sprawozdawczości ad hoc

![wzorzec raportowania ad hoc](./media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Aplikacje SaaS mogą analizować ogromną ilość danych dzierżawy, które są przechowywane centralnie w chmurze. Analizy ujawniają wgląd w operacje i użycie aplikacji. Te informacje mogą być pomocne podczas opracowywania funkcji, ulepszania użyteczności i innych inwestycji w aplikacje i usługi.

Uzyskiwanie dostępu do tych danych w jednej wielodostępnej bazie danych jest łatwe, ale nie jest tak proste, gdy są one znacznie rozproszone, potencjalnie nawet na tysiące baz danych. Jednym z metod jest użycie [elastycznej kwerendy](elastic-query-overview.md), która umożliwia wykonywanie zapytań w rozproszonym zestawie baz danych ze wspólnym schematem. Te bazy danych mogą być dystrybuowane między różnymi grupami zasobów i subskrypcjami. Jeszcze jedna wspólna nazwa logowania musi mieć dostęp do wyodrębniania danych ze wszystkich baz danych. Zapytanie elastyczne korzysta *z pojedynczej* bazy danych, w której są zdefiniowane tabele zewnętrzne, które stanowią duplikaty tabel lub widoków w rozproszonych bazach danych (dzierżawców). Zapytania skierowane do tej głównej bazy danych są kompilowane w celu utworzenia planu zapytania rozproszonego, gdzie części zapytania są w razie potrzeby wypychane do baz danych dzierżawy. Elastyczne zapytanie używa mapy fragmentu w bazie danych wykazu, aby określić lokalizację wszystkich baz danych dzierżaw. Instalator i zapytanie są proste przy użyciu standardowego [języka Transact-SQL](/sql/t-sql/language-reference)i obsługują zapytania ad hoc z narzędzi takich jak Power BI i Excel.

Dzięki rozproszeniu zapytań w bazach danych dzierżaw, elastyczne zapytanie zapewnia natychmiastowy wgląd w dane produkcyjne na żywo. Niemniej jednak, ponieważ elastyczne zapytanie pobiera dane z potencjalnie wielu baz danych, opóźnienie zapytania może być większe niż w przypadku równoważnych zapytań przesyłanych do jednej wielodostępnej bazy danych. Pamiętaj, aby zaprojektować zapytania, aby zminimalizować dane, które są zwracane. Elastyczne zapytanie jest często najlepiej dostosowane do wykonywania zapytań dotyczących niewielkich ilości danych w czasie rzeczywistym, zamiast tworzyć często używane lub złożone zapytania lub raporty analityczne. Jeśli zapytania nie działają prawidłowo, należy zapoznać się z [planem wykonywania](/sql/relational-databases/performance/display-an-actual-execution-plan) , aby sprawdzić, jaka część zapytania została przekazana do zdalnej bazy danych. I Oceń, ile danych jest zwracanych. Zapytania wymagające złożonego przetwarzania analitycznego mogą być lepiej obsługiwane przez zapisanie wyodrębnionych danych dzierżawy do bazy danych zoptymalizowanej pod kątem zapytań analitycznych. SQL Database i usługa Azure Synapse Analytics może obsługiwać taką bazę danych analitycznych.

Ten wzorzec dla analizy jest wyjaśniony w [samouczku dotyczącym analizy dzierżawców](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobierz Wingtip bilety SaaS kodu źródłowego i skryptów aplikacji bazy danych z wieloma dzierżawcami

Wingtip bilety SaaS wielodostępnych skryptów bazy danych i kodu źródłowego aplikacji są dostępne w repozytorium GitHub [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) . Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania Wingtip biletów SaaS.

## <a name="create-ticket-sales-data"></a>Tworzenie danych sprzedaży biletów

Aby uruchamiać zapytania dla bardziej interesującego zestawu danych, Utwórz dane sprzedaży biletów, uruchamiając Generator biletów.

1. W *ISE programu PowerShell* Otwórz pozycję... \\ Moduły uczenia \\ analizy \\ ad hoc \\ *Demo-AdhocReporting.ps1* skrypt i ustaw następujące wartości:
   * **$DemoScenario** = 1, **bilety zakupu dla zdarzeń we wszystkich miejsc**.
2. Naciśnij klawisz **F5** , aby uruchomić skrypt i wygenerować sprzedaż biletów. Po uruchomieniu skryptu wykonaj kroki opisane w tym samouczku. W sekcji *Uruchom zapytania rozproszone ad hoc* są wysyłane zapytania o dane biletów, więc poczekaj na ukończenie generatora biletów.

## <a name="explore-the-tenant-tables"></a>Eksplorowanie tabel dzierżaw 

W Wingtip bilety SaaS wielodostępnej aplikacji bazy danych dzierżawcy są przechowywane w modelu zarządzania dzierżawcą hybrydową, gdzie dane dzierżawy są przechowywane w bazie danych z wieloma dzierżawami lub w pojedynczej bazie danych dzierżawy i mogą być przenoszone między nimi. Podczas wykonywania zapytań we wszystkich bazach danych dzierżawców ważne jest, aby elastyczne zapytanie było traktowane jako część pojedynczej logicznej bazy danych podzielonej na fragmenty przez dzierżawcę. 

Aby osiągnąć ten wzorzec, wszystkie tabele dzierżaw zawierają kolumnę *VenueId* , która identyfikuje dzierżawcę, do którego należą dane. *VenueId* jest obliczany jako skrót nazwy miejsca, ale można użyć dowolnego podejścia do wprowadzenia unikatowej wartości dla tej kolumny. To podejście jest podobne do sposobu, w jaki klucz dzierżawy jest obliczany do użycia w wykazie. Tabele zawierające *VenueId* są używane przez zapytania elastyczne do zrównoleglanie zapytań i wypchnięcia ich do odpowiedniej zdalnej bazy danych dzierżawy. Znacznie zmniejsza to ilość zwracanych danych i skutkuje wzrostem wydajności szczególnie w przypadku wielu dzierżawców, których dane są przechowywane w bazach danych z pojedynczymi dzierżawcami.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Wdrażanie bazy danych używanej na potrzeby zapytań rozproszonych ad hoc

W tym ćwiczeniu zostanie wdrożona baza danych *adhocreporting* . Jest to główna baza danych zawierająca Schemat używany do wykonywania zapytań we wszystkich bazach danych dzierżaw. Baza danych jest wdrażana na istniejącym serwerze wykazu, który jest serwerem używanym dla wszystkich baz danych związanych z zarządzaniem w przykładowej aplikacji.

1. Otwórz... \\ Moduły szkoleniowe \\ analizy \\ ad hoc \\ *Demo-AdhocReporting.ps1* w programie *PowerShell ISE* i ustaw następujące wartości:
   * **$DemoScenario** = 2, **Wdróż bazę danych analizy ad hoc**.

2. Naciśnij klawisz **F5** , aby uruchomić skrypt i utworzyć bazę danych *adhocreporting* .

W następnej sekcji Dodaj schemat do bazy danych, aby można było używać go do uruchamiania zapytań rozproszonych.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurowanie bazy danych "nagłówkowej" na potrzeby uruchamiania zapytań rozproszonych

W tym ćwiczeniu do bazy danych raportowania ad hoc jest dodawany schemat (zewnętrzne definicje zewnętrznych źródeł danych i tabel zewnętrznych), który umożliwia wykonywanie zapytań we wszystkich bazach danych dzierżaw.

1. Otwórz SQL Server Management Studio i nawiąż połączenie z bazą danych raportowania AdHoc utworzoną w poprzednim kroku. Nazwa bazy danych to *adhocreporting*.
2. Otwórz. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ *Initialize-AdhocReportingDB. SQL* w programie SSMS.
3. Przejrzyj skrypt SQL i zwróć uwagę na następujące kwestie:

   Zapytanie elastyczne używa poświadczeń z zakresem bazy danych w celu uzyskania dostępu do wszystkich baz danych dzierżaw. To poświadczenie musi być dostępne we wszystkich bazach danych i powinny być zwykle przyznawane minimalnymi prawami wymaganymi do włączenia tych zapytań ad hoc.

    ![tworzenie poświadczenia](./media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Korzystając z bazy danych katalogu jako zewnętrznego źródła danych, zapytania są dystrybuowane do wszystkich baz danych zarejestrowanych w wykazie podczas uruchamiania zapytania. Ponieważ nazwy serwerów są różne dla każdego wdrożenia, ten skrypt inicjujący Pobiera lokalizację bazy danych wykazu przez pobranie bieżącego serwera (@), @servername w którym skrypt jest wykonywany.

    ![Utwórz zewnętrzne źródło danych](./media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Tabele zewnętrzne odwołujące się do tabel dzierżawców są zdefiniowane za pomocą **dystrybucji = podzielonej na fragmenty (VenueId)**. To kieruje zapytanie dla konkretnej *VenueId* do odpowiedniej bazy danych i poprawi wydajność dla wielu scenariuszy, jak pokazano w następnej sekcji.

    ![Tworzenie tabel zewnętrznych](./media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Lokalna tabela *VenueTypes* , która jest tworzona i wypełniana. Ta tabela danych referencyjnych jest wspólna we wszystkich bazach danych dzierżawy, więc może być reprezentowana w tym miejscu jako tabela lokalna i uzupełniona o wspólne dane. W przypadku niektórych zapytań może to zmniejszyć ilość danych przenoszonych między bazami danych dzierżawy i bazą danych *adhocreporting* .

    ![Utwórz tabelę](./media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Jeśli dołączysz tabele referencyjne w ten sposób, pamiętaj, aby zaktualizować schemat i dane tabeli przy każdej aktualizacji baz danych dzierżaw.

4. Naciśnij klawisz **F5** , aby uruchomić skrypt i zainicjować bazę danych *adhocreporting* . 

Teraz można uruchamiać zapytania rozproszone i zbierać szczegółowe informacje we wszystkich dzierżawach.

## <a name="run-ad-hoc-distributed-queries"></a>Uruchamianie rozproszonych zapytań ad hoc

Teraz, gdy baza danych *adhocreporting* została skonfigurowana, należy uruchomić niektóre zapytania rozproszone. Uwzględnij plan wykonania, aby lepiej zrozumieć, gdzie jest wykonywane przetwarzanie zapytań. 

Podczas sprawdzania planu wykonywania Umieść wskaźnik myszy nad ikonami planu, aby uzyskać szczegółowe informacje. 

1. W programie *SSMS* Otwórz pozycję... \\ Learning modules \\ Operational Analytics \\ AdHoc Reporting \\ *demo-AdhocReportingQueries. SQL*.
2. Upewnij się, że nawiązano połączenie z bazą danych **adhocreporting** .
3. Wybierz menu **zapytania** , a następnie kliknij pozycję **Dołącz rzeczywisty plan wykonania**
4. Zaznacz, *które miejsca są obecnie zarejestrowane?* zapytanie i naciśnij klawisz **F5**.

   Zapytanie zwraca całą listę miejsc, ilustrujące, jak szybka i łatwa jest kwerenda między wszystkimi dzierżawcami i zwracać dane z poszczególnych dzierżawców.

   Sprawdź plan i sprawdź, czy cały koszt to zapytanie zdalne, ponieważ po prostu przejdziemy do każdej bazy danych dzierżawy i wybierzesz informacje o miejscu.

   ![Wybierz pozycję * z dbo. Lokalizacje](./media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Wybierz następne zapytanie i naciśnij klawisz **F5**.

   To zapytanie sprzęga dane z baz danych dzierżaw i lokalnej tabeli *VenueTypes* (lokalnego, tak jak w przypadku tabeli w bazie danych *adhocreporting* ).

   Sprawdź plan i sprawdź, czy większość kosztów jest kwerendą zdalną, ponieważ wysyłamy zapytania do informacji o miejscu poszczególnych dzierżawców (dbo. Miejsca), a następnie wykonaj szybkie sprzężenie lokalne z lokalną tabelą *VenueTypes* , aby wyświetlić przyjazną nazwę.

   ![Dołącz do danych zdalnych i lokalnych](./media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Teraz wybierz, *dla którego dnia były sprzedawane najwięcej biletów?* zapytanie i naciśnij klawisz **F5**.

   To zapytanie wykonuje nieco bardziej złożoną dołączenie i agregację. Ważne, aby zauważyć, że większość przetwarzania jest wykonywana zdalnie, a raz, przywracamy tylko te wiersze, których potrzebujemy, zwracając tylko jeden wiersz dla łącznej liczby sprzedaży biletów każdego miejsca dziennie.

   ![query](./media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżawy
> * Wdróż bazę danych raportowania ad hoc i Dodaj do niej schemat do uruchamiania zapytań rozproszonych.

Wypróbuj teraz [samouczek analizy dzierżawy](saas-multitenantdb-tenant-analytics.md) , aby poznać wyodrębnianie danych do oddzielnej bazy danych analitycznych w celu przetworzenia bardziej złożonej analizy.

## <a name="additional-resources"></a>Zasoby dodatkowe

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Zapytanie elastyczne](elastic-query-overview.md)