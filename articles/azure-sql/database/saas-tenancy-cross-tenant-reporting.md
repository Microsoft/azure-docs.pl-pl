---
title: Raportowanie zapytań w wielu bazach danych
description: Raportowanie krzyżowe przy użyciu zapytań rozproszonych.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewers: ''
ms.date: 01/25/2019
ms.openlocfilehash: 03e8719b256fc758874bd7375deed0637da9447e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620311"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Raportowanie krzyżowe przy użyciu zapytań rozproszonych
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku uruchomiono zapytania rozproszone w całym zestawie baz danych dzierżaw na potrzeby raportowania. Te zapytania mogą wyodrębnić szczegółowe dane dotyczące codziennych danych operacyjnych Wingtip biletów SaaS dzierżawców. W tym celu należy wdrożyć dodatkową bazę danych raportowania na serwerze wykazu i użyć zapytania elastycznego w celu włączenia zapytań rozproszonych.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Jak wdrożyć bazę danych raportowania
> * Jak uruchamiać zapytania rozproszone we wszystkich bazach danych dzierżaw
> * Jak widoki globalne w każdej bazie danych mogą umożliwić wydajne wykonywanie zapytań w dzierżawach


Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:


* Wdrożono Wingtip bilety bazy danych SaaS dla dzierżawy. Aby wdrożyć program w ciągu mniej niż pięciu minut, zobacz [wdrażanie i eksplorowanie bazy danych Wingtip biletów SaaS na aplikację dzierżawców](../../sql-database/saas-dbpertenant-get-started-deploy.md)
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) jest zainstalowany. Aby pobrać i zainstalować program SSMS, zobacz [pobieranie SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Wzorzec raportowania między dzierżawcami

![wzorzec zapytania rozproszonego dla wielu dzierżawców](./media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Jedną z możliwości aplikacji SaaS jest użycie ogromnych ilości danych dzierżawy przechowywanych w chmurze, aby uzyskać wgląd w operacje i użycie aplikacji. Te informacje mogą być pomocne podczas opracowywania funkcji, ulepszania użyteczności i innych inwestycji w aplikacje i usługi.

Uzyskiwanie dostępu do tych danych w jednej wielodostępnej bazie danych jest łatwe, ale nie jest tak proste, gdy są one znacznie rozproszone, potencjalnie nawet na tysiące baz danych. Jednym z metod jest użycie [elastycznej kwerendy](elastic-query-overview.md), która umożliwia wykonywanie zapytań w rozproszonym zestawie baz danych ze wspólnym schematem. Te bazy danych mogą być dystrybuowane między różnymi grupami zasobów i subskrypcjami, ale muszą korzystać z wspólnej nazwy logowania. Zapytanie elastyczne korzysta *z pojedynczej* bazy danych, w której są zdefiniowane tabele zewnętrzne, które stanowią duplikaty tabel lub widoków w rozproszonych bazach danych (dzierżawców). Zapytania skierowane do tej głównej bazy danych są kompilowane w celu utworzenia planu zapytania rozproszonego, gdzie części zapytania są w razie potrzeby wypychane do baz danych dzierżawy. Elastyczne zapytanie używa mapy fragmentu w bazie danych wykazu, aby określić lokalizację wszystkich baz danych dzierżaw. Instalator i zapytanie bazy danych są proste przy użyciu standardowego [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)i obsługują zapytania z narzędzi takich jak Power BI i Excel.

Dzięki rozproszeniu zapytań w bazach danych dzierżaw, elastyczne zapytanie zapewnia natychmiastowy wgląd w dane produkcyjne na żywo. Ponieważ elastyczne zapytanie pobiera dane z potencjalnie wielu baz danych, opóźnienie zapytania może być większe niż równoważne kwerendy przesłane do pojedynczej wielodostępnej bazy danych. Projektuj zapytania, aby zminimalizować dane zwracane do bazy danych. Elastyczne zapytanie jest często najlepiej dostosowane do wykonywania zapytań dotyczących niewielkich ilości danych w czasie rzeczywistym, zamiast tworzyć często używane lub złożone zapytania lub raporty analityczne. Jeśli zapytania nie działają prawidłowo, zapoznaj się z [planem wykonywania](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) , aby sprawdzić, jaka część zapytania jest wypychana do zdalnej bazy danych i ile danych jest zwracana. Zapytania, które wymagają złożonej agregacji lub przetwarzania analitycznego, mogą być lepszymi dojściami przez wyodrębnienie danych dzierżawy do bazy danych lub magazynu danych zoptymalizowanego pod kątem zapytań analitycznych. Ten wzorzec jest objaśniony w [samouczku dotyczącym analizy dzierżawcy](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz Wingtip bilety bazy danych SaaS na skrypty aplikacji dzierżawców

Wingtip bilety SaaS wielodostępnych skryptów bazy danych i kodu źródłowego aplikacji są dostępne w repozytorium GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania Wingtip biletów SaaS.

## <a name="create-ticket-sales-data"></a>Tworzenie danych sprzedaży biletów

Aby uruchamiać zapytania dla bardziej interesującego zestawu danych, Utwórz dane sprzedaży biletów, uruchamiając Generator biletów.

1. W *ISE programu PowerShell*Otwórz pozycję... \\ Moduły uczenia \\ analizy \\ ad hoc \\ *Demo-AdhocReporting.ps1* skrypt i Ustaw następującą wartość:
   * **$DemoScenario** = 1, **bilety zakupu dla zdarzeń we wszystkich miejsc**.
2. Naciśnij klawisz **F5** , aby uruchomić skrypt i wygenerować sprzedaż biletów. Po uruchomieniu skryptu wykonaj kroki opisane w tym samouczku. W sekcji *Uruchom zapytania rozproszone ad hoc* są wysyłane zapytania o dane biletów, więc poczekaj na ukończenie generatora biletów.

## <a name="explore-the-global-views"></a>Eksplorowanie widoków globalnych

W przypadku aplikacji Wingtip biletów SaaS baza danych dla dzierżawców każdy dzierżawca otrzymuje bazę danych. W ten sposób dane zawarte w tabelach bazy danych są objęte zakresem perspektywy pojedynczej dzierżawy. Jednak podczas wykonywania zapytań we wszystkich bazach danych ważne jest, aby zapytanie elastyczne było traktowane jako część pojedynczej logicznej bazy danych podzielonej na fragmenty przez dzierżawcę. 

Aby zasymulować ten wzorzec, zestaw widoków "Global" jest dodawany do bazy danych dzierżawy, która umożliwia zaprojektowanie identyfikatora dzierżawy w każdej tabeli, w której są wykonywane zapytania globalnie. Na przykład widok *VenueEvents* dodaje obliczony *VenueId* do kolumn rzutowanych z tabeli *Events* . Podobnie widoki *VenueTicketPurchases* i *VenueTickets* dodają obliczoną kolumnę *VenueId* , która jest rzutowana z odpowiednich tabel. Te widoki są używane przez zapytanie elastyczne do zrównoleglanie zapytań i wypchnięcia ich do odpowiedniej zdalnej bazy danych dzierżawy, gdy znajduje się kolumna *VenueId* . Znacznie zmniejsza to ilość zwracanych danych i daje znaczny wzrost wydajności dla wielu zapytań. Te widoki globalne zostały wstępnie utworzone we wszystkich bazach danych dzierżaw.

1. Otwórz narzędzie SSMS i Połącz się z [ &lt; &gt; serwerem tenants1-użytkownika](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Rozwiń węzeł **bazy danych**, kliknij prawym przyciskiem myszy pozycję _contosoconcerthall_, a następnie wybierz pozycję **nowe zapytanie**.
1. Uruchom następujące zapytania, aby poznać różnice między tabelami z jedną dzierżawą i widokami globalnymi:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

W tych widokach *VenueId* jest obliczany jako skrót nazwy miejsca, ale dowolna Metoda może zostać użyta w celu wprowadzenia unikatowej wartości. To podejście jest podobne do sposobu, w jaki klucz dzierżawy jest obliczany do użycia w wykazie.

Aby sprawdzić definicję widoku *miejsc* :

1. W **Eksplorator obiektów**rozwiń węzeł **contosoconcerthall**  >  **widoki**contosoconcerthall:

   ![Zrzut ekranu przedstawia zawartość węzła widoki, w tym cztery typy miejsc d b.](./media/saas-tenancy-cross-tenant-reporting/views.png)

2. Kliknij prawym przyciskiem myszy pozycję **dbo. Miejsc**.
3. Wybierz **Widok skryptu jako**  >  **Utwórz do**  >  **nowego okna edytora zapytań**

Skrypt dowolnego z widoków innych *miejsc* , aby zobaczyć, jak dodają *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Wdrażanie bazy danych używanej w zapytaniach rozproszonych

W tym ćwiczeniu zostanie wdrożona baza danych _adhocreporting_ . Jest to główna baza danych zawierająca Schemat używany do wykonywania zapytań we wszystkich bazach danych dzierżaw. Baza danych jest wdrażana na istniejącym serwerze wykazu, który jest serwerem używanym dla wszystkich baz danych związanych z zarządzaniem w przykładowej aplikacji.

1. w programie *POWERSHELL ISE*Otwórz... \\ Moduły uczenia usługi \\ Operational Analytics \\ AdHoc \\ *Demo-AdhocReporting.ps1*. 

1. Ustaw **$DemoScenario = 2**, _Wdróż bazę danych raportowania ad hoc_.

1. Naciśnij klawisz **F5** , aby uruchomić skrypt i utworzyć bazę danych *adhocreporting* .

W następnej sekcji Dodaj schemat do bazy danych, aby można było używać go do uruchamiania zapytań rozproszonych.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurowanie bazy danych "nagłówkowej" na potrzeby uruchamiania zapytań rozproszonych

W tym ćwiczeniu do bazy danych _adhocreporting_ są dodawane schematy (zewnętrzne źródło danych i definicje tabel zewnętrznych), aby umożliwić wykonywanie zapytań we wszystkich bazach danych dzierżaw.

1. Otwórz SQL Server Management Studio i nawiąż połączenie z bazą danych raportowania AdHoc utworzoną w poprzednim kroku. Nazwa bazy danych to *adhocreporting*.
2. Otwórz. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ _Initialize-AdhocReportingDB. SQL_ w programie SSMS.
3. Przejrzyj skrypt SQL i Uwaga:

   Zapytanie elastyczne używa poświadczeń z zakresem bazy danych w celu uzyskania dostępu do wszystkich baz danych dzierżaw. To poświadczenie musi być dostępne we wszystkich bazach danych i powinny być zwykle przyznawane minimalnymi prawami wymaganymi do włączenia tych zapytań.

    ![tworzenie poświadczenia](./media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   W przypadku bazy danych wykazu jako zewnętrznego źródła danych zapytania są dystrybuowane do wszystkich baz danych zarejestrowanych w wykazie w momencie uruchomienia zapytania. Ponieważ nazwy serwerów są różne dla każdego wdrożenia, ten skrypt Pobiera lokalizację bazy danych wykazu z bieżącego serwera (@ @servername ), na którym skrypt jest wykonywany.

    ![Utwórz zewnętrzne źródło danych](./media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Tabele zewnętrzne odwołujące się do widoków globalnych opisanych w poprzedniej sekcji i zdefiniowane za pomocą **dystrybucji = podzielonej na fragmenty (VenueId)**. Ponieważ każdy *VenueId* jest mapowany na pojedynczą bazę danych, zwiększa to wydajność wielu scenariuszy, jak pokazano w następnej sekcji.

    ![Tworzenie tabel zewnętrznych](./media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Lokalna tabela _VenueTypes_ , która jest tworzona i wypełniana. Ta tabela danych referencyjnych jest wspólna we wszystkich bazach danych dzierżawy, więc może być reprezentowana w tym miejscu jako tabela lokalna i uzupełniona o wspólne dane. W przypadku niektórych zapytań, których tabela jest zdefiniowana w bazie danych, można zmniejszyć ilość danych, które muszą zostać przeniesione do bazy danych.

    ![Utwórz tabelę](./media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Jeśli dołączysz tabele referencyjne w ten sposób, pamiętaj, aby zaktualizować schemat i dane tabeli przy każdej aktualizacji baz danych dzierżaw.

4. Naciśnij klawisz **F5** , aby uruchomić skrypt i zainicjować bazę danych *adhocreporting* . 

Teraz można uruchamiać zapytania rozproszone i zbierać szczegółowe informacje we wszystkich dzierżawach.

## <a name="run-distributed-queries"></a>Uruchamianie zapytań rozproszonych

Teraz, gdy baza danych *adhocreporting* została skonfigurowana, należy uruchomić niektóre zapytania rozproszone. Uwzględnij plan wykonania, aby lepiej zrozumieć, gdzie jest wykonywane przetwarzanie zapytań. 

Podczas sprawdzania planu wykonywania Umieść wskaźnik myszy nad ikonami planu, aby uzyskać szczegółowe informacje. 

Należy pamiętać, że ustawienie **dystrybucja = podzielonej na fragmenty (VenueId)** w przypadku zdefiniowania zewnętrznego źródła danych zwiększa wydajność dla wielu scenariuszy. Ponieważ każdy *VenueId* jest mapowany na pojedynczą bazę danych, filtrowanie jest łatwo wykonywane zdalnie, zwracając tylko niezbędne dane.

1. Otwórz... \\ Learning modules \\ Operational Analytics \\ AdHoc raportowanie \\ *demo-AdhocReportingQueries. SQL* w programie SSMS.
2. Upewnij się, że nawiązano połączenie z bazą danych **adhocreporting** .
3. Wybierz menu **zapytania** , a następnie kliknij pozycję **Dołącz rzeczywisty plan wykonania**
4. Zaznacz, *które miejsca są obecnie zarejestrowane?* zapytanie i naciśnij klawisz **F5**.

   Zapytanie zwraca całą listę miejsc, pokazujące, jak szybko i łatwo można wykonywać zapytania dla wszystkich dzierżawców i zwracać dane z poszczególnych dzierżawców.

   Sprawdź plan i sprawdź, czy cały koszt znajduje się w zapytaniu zdalnym. Każda baza danych dzierżawcy wykonuje zapytanie zdalnie i zwraca informacje o jego miejscu do bazy danych.

   ![Wybierz pozycję * z dbo. Lokalizacje](./media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Wybierz następne zapytanie i naciśnij klawisz **F5**.

   To zapytanie sprzęga dane z baz danych dzierżaw i lokalnej tabeli *VenueTypes* (lokalnego, tak jak w przypadku tabeli w bazie danych *adhocreporting* ).

   Sprawdź plan i sprawdź, czy większość kosztów jest zdalną kwerendą. Każda baza danych dzierżawy zwraca informacje o miejscu i wykonuje lokalne sprzężenie z lokalną tabelą *VenueTypes* , aby wyświetlić przyjazną nazwę.

   ![Dołącz do danych zdalnych i lokalnych](./media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Teraz wybierz, *dla którego dnia były sprzedawane najwięcej biletów?* zapytanie i naciśnij klawisz **F5**.

   To zapytanie wykonuje nieco bardziej złożoną dołączenie i agregację. Większość przetwarzania odbywa się zdalnie.  Tylko pojedyncze wiersze zawierające dzienną liczbę sprzedanych biletów każdego miejsca dziennie są zwracane do bazy danych głównych.

   ![query](./media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Uruchamiania rozproszonych zapytań dla wszystkich baz danych dzierżawy
> * Wdróż bazę danych raportowania i zdefiniuj schemat wymagany do uruchamiania zapytań rozproszonych.


Wypróbuj teraz [samouczek analizy dzierżawy](saas-tenancy-tenant-analytics.md) , aby poznać wyodrębnianie danych do oddzielnej bazy danych analitycznych w celu przetworzenia bardziej złożonej analizy.

## <a name="additional-resources"></a>Zasoby dodatkowe

* Dodatkowe [samouczki, które kompilują się w oparciu o Wingtip bilety bazy danych SaaS na aplikację dzierżawców](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Zapytanie elastyczne](elastic-query-overview.md)
