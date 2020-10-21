---
title: Analiza wielu dzierżawców z użyciem wyodrębnionych danych
description: Zapytania analizy obejmującej wiele dzierżawców korzystające z danych wyodrębnionych z wielu baz danych Azure SQL w aplikacji pojedynczej dzierżawy.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 8076b417c8043a4f6796ccca0e67db79360ede73
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331672"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Analiza wielu dzierżawców przy użyciu wyodrębnionych aplikacji z jedną dzierżawą
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
W tym samouczku przedstawiono kompletny scenariusz analizy dla implementacji pojedynczej dzierżawy. W tym scenariuszu pokazano, jak analiza może umożliwić firmom podejmowanie szybkich decyzji. Korzystając z danych wyodrębnionych z każdej bazy danych dzierżawy, można używać analiz do uzyskiwania wglądu w zachowanie dzierżawy, w tym ich używania przykładowych biletów Wingtip SaaS aplikacji. Ten scenariusz obejmuje trzy kroki: 

1.  **Wyodrębnij** dane z każdej bazy danych dzierżawy i **Załaduj** je do sklepu analitycznego.
2.  **Przekształć wyodrębnione dane** na potrzeby przetwarzania analizy.
3.  Skorzystaj z narzędzi **analizy biznesowej** , aby narysować przydatne informacje, które mogą posłużyć do podejmowania decyzji. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Utwórz magazyn analizy dzierżawców, aby wyodrębnić dane do programu.
> - Korzystaj z zadań elastycznych, aby wyodrębnić dane z każdej bazy danych dzierżawy do magazynu analizy.
> - Zoptymalizuj wyodrębnione dane (uporządkuj je w schemat gwiazdki).
> - Zbadaj bazę danych analizy.
> - Użyj Power BI do wizualizacji danych, aby wyróżnić trendy w danych dzierżawy i zaleceń dotyczących ulepszeń.

![Diagram przedstawia przegląd architektury używanej w tym artykule.](./media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Wzorzec analizy dzierżawy w trybie offline

Wielodostępne aplikacje SaaS zazwyczaj mają ogromną ilość danych dzierżawy przechowywanych w chmurze. Te dane zawierają bogate źródło szczegółowych informacji o operacjach i użyciu aplikacji oraz zachowanie dzierżawców. Te informacje mogą być pomocne podczas opracowywania funkcji, ulepszeń użyteczności i innych inwestycji w aplikację i platformę.

Uzyskiwanie dostępu do danych dla wszystkich dzierżawców jest proste, gdy wszystkie dane są w tylko jednej bazie danych z wieloma dzierżawcami. Jednak dostęp jest bardziej skomplikowany, gdy jest dystrybuowany na dużą skalę w przypadku potencjalnie tysięcy baz danych. Jednym ze sposobów oswoić złożoności i zminimalizowania wpływu kwerend analitycznych na dane transakcyjne polega na wyodrębnieniu danych do celu zaprojektowanej bazy danych analitycznych lub magazynu danych.

W tym samouczku przedstawiono kompletny scenariusz analityczny dla Wingtip biletów SaaS aplikacji. Najpierw *zadania elastyczne* służą do wyodrębniania danych z każdej bazy danych dzierżaw i ładowania ich do tabel przemieszczania w sklepie analitycznym. Magazyn analityczny może być SQL Database lub pulą SQL. W przypadku wyodrębniania danych na dużą skalę zaleca się [Azure Data Factory](../../data-factory/introduction.md) .

Następnie zagregowane dane są przekształcane w zestaw tabel ze [schematem gwiazdy](https://www.wikipedia.org/wiki/Star_schema) . Tabele składają się z centralnej tabeli faktów oraz powiązanych tabel wymiarów.  Dla biletów Wingtip:

- Centralna tabela faktów w schemacie gwiazdy zawiera dane biletów.
- Tabele wymiarów opisują miejsca, zdarzenia, klientów i daty zakupu.

Przy użyciu centralnych tabel faktów i wymiarów umożliwia wydajne przetwarzanie analityczne. Schemat gwiazdy używany w tym samouczku przedstawiono na poniższej ilustracji:
 
![architectureOverView](./media/saas-tenancy-tenant-analytics/StarSchema.png)

Na koniec w sklepie Analytics jest wysyłana kwerenda **przy użyciu usługi Power BI w celu** wyróżnienia szczegółowych informacji na temat zachowania dzierżawy i korzystania z aplikacji biletów Wingtip. Uruchamiasz zapytania, które:
 
- Pokaż względną popularność każdego miejsca
- Wyróżnij wzorce w sprzedaży biletów dla różnych zdarzeń
- Pokaż względne sukcesy różnych miejsc w sprzedaży swojego zdarzenia

Zrozumienie, w jaki sposób każda dzierżawa korzysta z usługi, jest używana do eksplorowania opcji zarabiając usługi i ulepszania usługi, aby ułatwić dzierżawcom. Ten samouczek zawiera podstawowe przykłady rodzaju szczegółowych informacji, które mogą być wydobyć z danych dzierżawy.

## <a name="setup"></a>Konfigurowanie

### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Wdrożono bazę danych SaaS biletów Wingtip na aplikację dzierżawców. Aby wdrożyć program w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip SaaS](../../sql-database/saas-dbpertenant-get-started-deploy.md)
- W witrynie GitHub są pobierane Wingtip bilety bazy danych SaaS i [kod źródłowy](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) aplikacji. Zobacz instrukcje pobierania. Pamiętaj, aby *odblokować plik zip* przed wyodrębnieniem jego zawartości. Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania Wingtip biletów SaaS.
- Power BI Desktop jest zainstalowany. [Pobieranie programu Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Partia dodatkowych dzierżawców została zainicjowana, zapoznaj się z [**samouczkiem Inicjowanie obsługi dzierżaw**](../../sql-database/saas-dbpertenant-provision-and-catalog.md).
- Utworzono konto zadania i bazę danych konta zadania. Zapoznaj się z odpowiednimi krokami w [**samouczku dotyczącym zarządzania schematami**](../../sql-database/saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Utwórz dane dla pokazu

W tym samouczku analiza jest wykonywana na danych sprzedaży biletów. W bieżącym kroku są generowane dane biletów dla wszystkich dzierżawców.  Później te dane są wyodrębniane do analizy. *Upewnij się, że masz zainicjowaną partię dzierżawców zgodnie z wcześniejszym opisem, aby uzyskać zrozumiałą ilość danych*. Wystarczająco duże ilości danych mogą uwidaczniać różne wzorce zakupów biletów.

1. W programie PowerShell ISE Otwórz pozycję *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*i Ustaw następującą wartość:
    - **$DemoScenario**  =  **1** bilety zakupu dla zdarzeń we wszystkich miejscu
2. Naciśnij klawisz **F5** , aby uruchomić skrypt i utworzyć historię zakupów biletów dla każdego zdarzenia w każdym miejscu.  Skrypt jest uruchamiany przez kilka minut na wygenerowanie dziesiątek tysięcy biletów.

### <a name="deploy-the-analytics-store"></a>Wdrażanie sklepu analitycznego
Często istnieje wiele transakcyjnych baz danych, które razem przechowują wszystkie dane dzierżawy. Należy agregować dane dzierżawy z wielu transakcyjnych baz danych w jednym sklepie analitycznym. Agregacja umożliwia wydajne wykonywanie zapytań dotyczących danych. W tym samouczku Azure SQL Database jest używany do przechowywania zagregowanych danych.

W poniższych krokach zostanie wdrożony magazyn analityczny o nazwie **tenantanalytics**. Wdrożono również wstępnie zdefiniowane tabele, które są umieszczane w dalszej części tego samouczka:
1. W programie PowerShell ISE Otwórz *..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Ustaw zmienną $DemoScenario w skrypcie, aby pasowała do wybranego sklepu analitycznego:
    - Aby użyć SQL Database bez magazynu kolumn, ustaw **$DemoScenario**  =  **2**
    - Aby użyć SQL Database z magazynem kolumn, ustaw **$DemoScenario**  =  **3**  
3. Naciśnij klawisz **F5** , aby uruchomić skrypt demonstracyjny (wywołujący skrypt *Deploy-TenantAnalytics \<XX> . ps1* ), który tworzy magazyn analizy dzierżawców. 

Teraz, gdy aplikacja została wdrożona i uzupełniona o interesujące dane dzierżawy, [użyj SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) do połączenia serwerów **tenants1-DPT- &lt; User &gt; ** i **Catalog-DPT- &lt; Users &gt; ** przy użyciu funkcji login = *Developer*, Password = *P \@ ssword1*. Zobacz [samouczek wprowadzający](../../sql-database/saas-dbpertenant-wingtip-app-overview.md) , aby uzyskać więcej wskazówek.

![Zrzut ekranu pokazujący informacje konieczne do nawiązania połączenia z SQL Server.](./media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

W Eksplorator obiektów wykonaj następujące czynności:

1. Rozwiń serwer *tenants1-DPT- &lt; User &gt; * .
2. Rozwiń węzeł bazy danych i Zobacz listę baz danych dzierżaw.
3. Rozwiń węzeł *katalog-DPT- &lt; użytkownika &gt; * .
4. Sprawdź, czy jest widoczny magazyn analizy i baza danych jobaccount.

Zobacz następujące elementy bazy danych w narzędziu SSMS Eksplorator obiektów, rozszerzając węzeł sklepu Analytics:

- Tabele **TicketsRawData** i **EventsRawData** przechowują nieprzetworzone dane wyodrębnione z baz danych dzierżaw.
- Tabele schematu gwiazdy to **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**i **dim_Dates**.
- Procedura składowana służy do wypełniania tabel schematu gwiazdy z nieprzetworzonych tabel danych.

![Zrzut ekranu przedstawiający elementy bazy danych widoczne w Eksplorator obiektów SSMS.](./media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Wyodrębnianie danych 

### <a name="create-target-groups"></a>Tworzenie grup docelowych 

Przed kontynuowaniem upewnij się, że wdrożono konto zadania i bazę danych jobaccount. W następnym zestawie kroków zadania elastyczne są używane do wyodrębniania danych z każdej bazy danych dzierżawy oraz do przechowywania danych w sklepie analitycznym. Następnie drugie zadanie shreds dane i zapisuje je w tabelach w schemacie gwiazdy. Te dwa zadania są uruchamiane w odniesieniu do dwóch różnych grup docelowych, a mianowicie **dzierżawców** i grupy **analizy**. Zadanie wyodrębniania działa w odniesieniu do grupy dzierżawców, która zawiera wszystkie bazy danych dzierżaw. Zadanie shredding jest uruchamiane względem grupy analiz, która zawiera tylko magazyn analityczny. Utwórz grupy docelowe, wykonując następujące czynności:

1. W programie SSMS Nawiąż połączenie z bazą danych **jobaccount** w katalogu-DPT- &lt; User &gt; .
2. W programie SSMS Otwórz pozycję *. ..\Learning Modules\Operational Analytics\Tenant Analytics \ TargetGroups. SQL* 
3. Zmodyfikuj @User zmienną w górnej części skryptu, zastępując ją `<User>` wartością użytkownika używaną podczas wdrażania aplikacji Wingtip SaaS.
4. Naciśnij klawisz **F5** , aby uruchomić skrypt, który tworzy dwie grupy docelowe.

### <a name="extract-raw-data-from-all-tenants"></a>Wyodrębnij dane pierwotne ze wszystkich dzierżawców

Szczegółowe modyfikacje danych mogą występować częściej dla danych *biletu i klienta* niż w przypadku danych dotyczących *zdarzeń i miejsc* . W związku z tym Rozważ wyodrębnienie danych biletu i klienta oddzielnie i częściej niż wyodrębnianie danych zdarzeń i miejsc. W tej sekcji definiujesz i planujesz dwa oddzielne zadania:

- Wyodrębnij dane biletu i klienta.
- Wyodrębnij dane zdarzenia i miejsca.

Każde zadanie wyodrębnia swoje dane i zapisuje je w sklepie analitycznym. Oddzielne zadanie shreds wyodrębnione dane do programu gwiazdy Analytics-Schema.

1. W programie SSMS Nawiąż połączenie z bazą danych **jobaccount** w katalogu DPT- &lt; User &gt; Server.
2. W programie SSMS Otwórz pozycję *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.SQL*.
3. Zmodyfikuj @User u góry skryptu i Zastąp ciąg `<User>` nazwą użytkownika używaną podczas wdrażania aplikacji Wingtip SaaS 
4. Naciśnij klawisz F5, aby uruchomić skrypt, który tworzy i uruchamia zadanie wyodrębniające bilety i dane klientów z każdej bazy danych dzierżaw. Zadanie zapisuje dane w sklepie analizy.
5. Wykonaj zapytanie dotyczące tabeli TicketsRawData w bazie danych tenantanalytics, aby upewnić się, że tabela została wypełniona informacjami o biletach ze wszystkich dzierżawców.

![Zrzut ekranu przedstawia bazę danych ExtractTickets z TicketsRawDataą d b o wybranym w Eksplorator obiektów.](./media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Powtórz powyższe kroki, z wyjątkiem tego, że Zastąp **\ExtractTickets.SQL** with **\ExtractVenuesEvents.SQL** w kroku 2.

Pomyślne uruchomienie zadania spowoduje wypełnienie tabeli EventsRawData w sklepie analitycznym nowymi zdarzeniami i umieszczenie informacji ze wszystkich dzierżawców. 

## <a name="data-reorganization"></a>Reorganizacja danych

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Shred wyodrębnione dane, aby wypełnić tabele schematu gwiazdy

Następnym krokiem jest Shred wyodrębnionych nieprzetworzonych danych do zestawu tabel, które są zoptymalizowane pod kątem zapytań analitycznych. Używany jest schemat gwiazdy. Centralna tabela faktów zawiera poszczególne rekordy sprzedaży biletów. Inne tabele są wypełniane odpowiednimi danymi dotyczącymi miejsc, zdarzeń i klientów. I istnieją tabele wymiarów czasu. 

W tej części samouczka zdefiniujesz i uruchomisz zadanie, które scala wyodrębnione dane pierwotne z danymi w tabelach schematu gwiazdy. Po zakończeniu zadania scalania dane pierwotne zostaną usunięte, pozostawiając tabele gotowe do wypełnienia przez następne zadanie wyodrębnienia danych dzierżawy.

1. W programie SSMS Nawiąż połączenie z bazą danych **jobaccount** w katalogu-DPT- &lt; User &gt; .
2. W programie SSMS Otwórz pozycję *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.SQL*.
3. Naciśnij klawisz **F5** , aby uruchomić skrypt w celu zdefiniowania zadania, które wywołuje procedurę składowaną sp_ShredRawExtractedData w sklepie analizy.
4. Poczekaj, aż zadanie zostanie pomyślnie uruchomione.
    - Sprawdź kolumnę **cykl życia** tabeli jobs.jobs_execution dla stanu zadania. Przed kontynuowaniem upewnij się, że zadanie **zakończyło się pomyślnie** . Pomyślne uruchomienie wyświetla dane podobne do poniższego wykresu:

![shredding](./media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Eksploracja danych

### <a name="visualize-tenant-data"></a>Wizualizacja danych dzierżawy

Dane w tabeli ze schematem gwiazdy zawierają wszystkie dane sprzedaży biletów potrzebnych do analizy. Aby ułatwić wyświetlanie trendów w dużych zestawach danych, należy wizualizować ją graficznie.  W tej sekcji dowiesz się, jak używać **Power BI** do manipulowania i wizualizacji danych dzierżawy, które zostały wyodrębnione i zorganizowane.

Wykonaj następujące kroki, aby nawiązać połączenie z usługą Power BI i zaimportować utworzone wcześniej widoki:

1. Uruchom Power BI pulpicie.
2. Na Wstążce Narzędzia główne wybierz pozycję **Pobierz dane**, a następnie wybierz pozycję **więcej...** z menu.
3. W oknie **pobieranie danych** wybierz pozycję Azure SQL Database.
4. W oknie Logowanie do bazy danych wprowadź nazwę serwera (Catalog-DPT- &lt; User &gt; . Database.Windows.NET). Wybierz pozycję **Importuj** dla **trybu łączności danych**, a następnie kliknij przycisk OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. W lewym okienku wybierz pozycję **baza danych** , a następnie wprowadź nazwę użytkownika = *deweloper*i wprowadź hasło = *P \@ ssword1*. Kliknij przycisk **Połącz**.  

    ![Zrzut ekranu przedstawia okno dialogowe baza danych SQL Server, w którym można wprowadzić nazwę użytkownika i hasło.](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. W okienku **Nawigator** w obszarze baza danych analizy wybierz tabele ze schematem gwiazdy: fact_Tickets, dim_Events, dim_Venues, dim_Customers i dim_Dates. Następnie wybierz pozycję **Załaduj**. 

Gratulacje! Dane zostały pomyślnie załadowane do Power BI. Teraz możesz zacząć Eksplorowanie interesujących wizualizacji, aby ułatwić uzyskanie wglądu w dzierżawy. Następnym zapoznaj się z tematem, w jaki sposób analiza może pozwolić na zapewnienie zaleceń opartych na danych w zespole biznesowym biletów Wingtip. Zalecenia mogą pomóc zoptymalizować model biznesowy i obsługę klienta.

Zacznij od analizowania danych sprzedaży biletów, aby zobaczyć zmiany w użyciu w miejscu. Wybierz poniższe opcje w Power BI, aby wyświetlić wykres słupkowy łącznej liczby biletów sprzedawanych przez każdy z miejsc. Ze względu na losową odmianę generatora biletów wyniki mogą się różnić.
 
![Zrzut ekranu przedstawia wizualizację i kontrolki dla wizualizacji danych po prawej stronie.](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Powyższy wykres potwierdza, że liczba biletów sprzedawanych przez każdy z miejsc jest różna. Miejsca, w których sprzedawane są więcej biletów, są bardziej silniej niż miejsca, w których sprzedawane są mniejsze bilety. W tym miejscu może istnieć możliwość dostosowywania alokacji zasobów zgodnie z różnymi potrzebami dzierżawców.

Możesz przeanalizować dane, aby zobaczyć, w jaki sposób sprzedaż biletów różni się w miarę upływu czasu. Wybierz poniższe opcje w Power BI, aby wykreślić łączną liczbę biletów sprzedawanych każdego dnia przez okres 60 dni.
 
![Zrzut ekranu przedstawia wizualizację B I o tytule dystrybucja sprzedaży biletów I dzień sprzedaży.](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Powyższy wykres przedstawia wzrost sprzedaży biletów dla niektórych miejsc. Te szczyty wzmacniają, że niektóre miejsca mogą zużywać zasoby systemowe w nieproporcjonalnym charakterze. Do tej pory nie ma żadnego oczywistego wzorca podczas wystąpienia.

Następnie chcesz dokładniej zbadać znaczenie tych dni sprzedaży szczytowej. Kiedy te wartości szczytowe występują po sprzedaży biletów? Aby wykreślić bilety sprzedawane dziennie, wybierz poniższe opcje w Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Powyższy wykres pokazuje, że niektóre miejsca sprzedajeją wiele biletów w pierwszym dniu sprzedaży. Gdy tylko bilety przechodzą do sprzedaży w tych miejscu, prawdopodobnie istnieje Mad — szczytu. Ta seria działań przez kilka miejsc może mieć wpływ na usługę dla innych dzierżawców.

Możesz ponownie przejść do danych, aby sprawdzić, czy ten Mad — szczytu ma wartość true dla wszystkich zdarzeń hostowanych przez te miejsca. W poprzednich wykresach zauważono, że korytarze w firmie Contoso sprzedaje wiele biletów, a firma Contoso ma także wzrost sprzedaży biletów w określonych dniach. Załącz się z opcjami Power BI, aby wykreślić skumulowaną sprzedaż biletów dla korytarza wspólnie otaczającego, skupiając się na trendach sprzedaży dla każdego z jego wydarzeń. Czy wszystkie zdarzenia są zgodne z tym samym wzorcem sprzedaży?

![ContosoSales](./media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Powyższy wykres z korytarzem z firmy Contoso wspólnie pokazuje, że Mad — szczytu nie występuje dla wszystkich zdarzeń. Zapoznaj się z opcjami filtru, aby zobaczyć trendy sprzedaży dla innych miejsc.

Szczegółowe informacje na temat wzorców sprzedaży biletów mogą prowadzić do Wingtip biletów, aby zoptymalizować model biznesowy. Zamiast naliczania wszystkich dzierżawców w równym stopniu, prawdopodobnie Wingtip powinny wprowadzać warstwy usług o różnych rozmiarach obliczeniowych. Większą liczbą miejsc, które muszą sprzedawać więcej biletów dziennie, może być oferowana wyższa warstwa z wyższą umową dotyczącą poziomu usług (SLA). Te miejsca mogą mieć swoje bazy danych umieszczane w puli z wyższymi limitami zasobów dla poszczególnych baz danych. Każda warstwa usług może mieć co godzinę alokację sprzedaży przy użyciu dodatkowych opłat naliczanych za przekroczenie przydziału. Większe miejsca, w których okresowe rozbicie sprzedaży byłyby korzystne dla wyższych warstw, a bilety Wingtip mogą wydajnie Zarabiaj swoją usługę.

Tymczasem niektórzy Wingtip bilety, z których klienci mogą się zapewnić, aby sprzedać wystarczającą liczbę biletów, aby uzasadnić koszt usługi. W tych informacjach można zwiększyć sprzedaż biletów w przypadku niedopełnienia miejsc. Wyższa sprzedaż mogłaby zwiększyć postrzeganą wartość usługi. Kliknij prawym przyciskiem myszy fact_Tickets i wybierz pozycję **Nowa miara**. Wprowadź następujące wyrażenie dla nowej miary o nazwie **AverageTicketsSold**:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Wybierz poniższe opcje wizualizacji, aby wykreślić bilety procentowe sprzedane przez każdy z nich, aby określić ich względną skuteczność.

![Zrzut ekranu przedstawia wizualizację B I o średniej bilety sprzedawanej przez każdy z miejsc.](./media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Powyższy wykres pokazuje, że mimo że większość miejsc sprzedaje ponad 80% swoich biletów, niektóre z nich zoptymalizowaniem się, aby wypełnić więcej niż połowę stanowisk. Zanotuj wszystkie wartości, aby wybrać maksymalną lub minimalną stawkę procentową biletów sprzedawanych w każdym miejscu.

Wcześniej można było pogłębić analizę, aby odkryć, że sprzedaż biletów jest zgodna z przewidywalnymi wzorcami. To odnajdowanie może umożliwić biletom Wingtip zwiększenie sprzedaży biletów dzięki zalecaniu dynamicznych cen. Ta metoda odnajdywania może ujawnić możliwość zastosowania technik uczenia maszynowego do przewidywania sprzedaży biletów dla każdego zdarzenia. Możliwe jest również prognozowanie wpływu przychodów oferty na sprzedaż biletów. Power BI Embedded można zintegrować z aplikacją zarządzania zdarzeniami. Integracja może pomóc w wizualizowaniu przewidywanych sprzedaży i wpływie różnych rabatów. Aplikacja może pomóc w opracowaniu optymalnego rabatu do zastosowania bezpośrednio z poziomu ekranu analizy.

Zaobserwowano trendy w danych dzierżawcy z aplikacji WingTip. Można z nich naSaaSać inne sposoby, aby aplikacja mogła informować decyzje biznesowe dotyczące dostawców aplikacji. Dostawcy mogą lepiej zaspokojyć potrzeby swoich dzierżawców. Miejmy nadzieję ten samouczek zawiera narzędzia niezbędne do przeprowadzenia analizy danych dzierżawy, aby umożliwić firmom podejmowanie decyzji opartych na danych.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Wdrożono bazę danych analizy dzierżawy ze wstępnie zdefiniowanymi tabelami schematów gwiazdy
> - Wykorzystano zadania elastyczne do wyodrębnienia danych ze wszystkich dzierżawców
> - Scalanie wyodrębnionych danych do tabel w schemacie gwiazdy zaprojektowanym do analizy
> - Tworzenie zapytań względem bazy danych analizy 
> - Używanie Power BI do wizualizacji danych w celu obserwowania trendów danych dzierżawy 

Gratulacje!

## <a name="additional-resources"></a>Zasoby dodatkowe

- Dodatkowe [samouczki, które kompilują się po aplikacji Wingtip SaaS](../../sql-database/saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Zadania elastyczne](../../sql-database/elastic-jobs-overview.md).
- [Analiza wielu dzierżawców z użyciem wyodrębnionych danych — aplikacji z wieloma dzierżawcami](../../sql-database/saas-multitenantdb-tenant-analytics.md)