---
title: Uruchamianie zapytań analitycznych dla baz danych dzierżawy
description: Zapytania analizy obejmującej wiele dzierżawców korzystające z danych wyodrębnionych z Azure SQL Database, Azure Synapse Analytics, Azure Data Factory lub Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: dc47c996748b126841cbeff1ea3f6f18f423951f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96457639"
---
# <a name="explore-saas-analytics-with-azure-sql-database-azure-synapse-analytics-data-factory-and-power-bi"></a>Eksploruj SaaS Analytics, korzystając z Azure SQL Database, Azure Synapse Analytics, Data Factory i Power BI
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym samouczku przedstawiono kompleksowe scenariusze analizy. W tym scenariuszu pokazano, jak analiza danych dzierżawcy może dać dostawcom oprogramowania możliwość podejmowania przemyślanych decyzji. Korzystając z danych wyodrębnionych z każdej bazy danych dzierżawy, można używać analiz do uzyskiwania wglądu w zachowanie dzierżawy, w tym ich używania przykładowych biletów Wingtip SaaS aplikacji. Ten scenariusz obejmuje trzy kroki:

1. **Wyodrębnij dane** z każdej bazy danych dzierżawy do sklepu analitycznego, w tym przypadku dedykowanej puli SQL.
2. **Zoptymalizuj wyodrębnione dane** na potrzeby przetwarzania analizy.
3. Skorzystaj z narzędzi **analizy biznesowej** , aby narysować przydatne informacje, które mogą posłużyć do podejmowania decyzji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Utwórz magazyn analizy dzierżawy do załadowania.
> - Użyj Azure Data Factory (ADF) do wyodrębnienia danych z każdej bazy danych dzierżawy do magazynu danych analizy.
> - Zoptymalizuj wyodrębnione dane (uporządkuj je w schemat gwiazdki).
> - Zbadaj magazyn danych analizy.
> - Użyj Power BI do wizualizacji danych, aby wyróżnić trendy w danych dzierżawy i zaleceń dotyczących ulepszeń.

![architectureOverView](./media/saas-tenancy-tenant-analytics-adf/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analiza przez wyodrębnione dane dzierżawy

Aplikacje SaaS przechowują potencjalnie ogromną ilość danych dzierżawy w chmurze. Te dane mogą zapewnić bogate źródło szczegółowych informacji o operacjach i użyciu aplikacji oraz zachowanie dzierżawców. Te informacje mogą być pomocne podczas opracowywania funkcji, ulepszeń użyteczności i innych inwestycji w aplikacje i platformę.

Uzyskiwanie dostępu do danych dla wszystkich dzierżawców jest proste, gdy wszystkie dane są w tylko jednej bazie danych z wieloma dzierżawcami. Jednak dostęp jest bardziej skomplikowany, gdy jest dystrybuowany na dużą skalę w tysiącach baz danych. Jednym ze sposobów oswoić złożoności jest wyodrębnienie danych do bazy danych analizy lub magazynu danych na potrzeby zapytania.

Ten samouczek przedstawia kompleksowy scenariusz analityczny dla aplikacji biletów Wingtip. Najpierw [Azure Data Factory (ADF)](../../data-factory/introduction.md) jest używany jako narzędzie aranżacji do wyodrębniania sprzedaży biletów i pokrewnych danych z każdej bazy danych dzierżawy. Te dane są ładowane do tabel przemieszczania w sklepie analitycznym. Magazyn analityczny może być SQL Database lub dedykowaną pulą SQL. Ten samouczek używa [usługi Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) jako sklepu analitycznego.

Następnie wyodrębnione dane są przekształcane i ładowane do zestawu tabel ze [schematem gwiazdy](https://www.wikipedia.org/wiki/Star_schema) . Tabele składają się z centralnej tabeli faktów oraz powiązanych tabel wymiarów:

- Centralna tabela faktów w schemacie gwiazdy zawiera dane biletów.
- Tabele wymiarów zawierają dane dotyczące miejsc, zdarzeń, klientów i dat zakupu.

Tabele centralne i wymiary umożliwiają wydajne przetwarzanie analityczne. Schemat gwiazdy używany w tym samouczku jest wyświetlany na poniższej ilustracji:

![Diagram przedstawiający schemat gwiazdy, który jest używany w tym samouczku.](./media/saas-tenancy-tenant-analytics-adf/starschematables.JPG)

Na koniec są badane zapytania dotyczące tabel schematu gwiazdy. Wyniki zapytania są wyświetlane wizualnie przy użyciu Power BI, aby wyróżnić wgląd w zachowanie dzierżawy i korzystanie z aplikacji. Dzięki temu schematowi gwiazdy można uruchamiać zapytania, które uwidaczniają:

- Kto kupuje bilety i od których miejsca.
- Wzorce i trendy w sprzedaży biletów.
- Relatywna popularność każdego miejsca.

Ten samouczek zawiera podstawowe przykłady szczegółowych informacji, które można wydobyć z danych biletów Wingtip. Zrozumienie, w jaki sposób każdy z miejsc używa usługi, może spowodować, że dostawca biletów Wingtip będzie myśleć o różnych planach usług przeznaczonych dla więcej lub mniej aktywnych miejsc, na przykład.

## <a name="setup"></a>Konfiguracja

### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Wdrożono bazę danych SaaS biletów Wingtip na aplikację dzierżawców. Aby wdrożyć program w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip SaaS](./saas-dbpertenant-get-started-deploy.md).
- W witrynie GitHub są pobierane Wingtip bilety bazy danych SaaS i [kod źródłowy](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) aplikacji. Zobacz instrukcje pobierania. Pamiętaj, aby *odblokować plik zip* przed wyodrębnieniem jego zawartości.
- Power BI Desktop jest zainstalowany. [Pobierz Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Partia dodatkowych dzierżawców została zainicjowana, zapoznaj się z [**samouczkiem Inicjowanie obsługi dzierżaw**](./saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Utwórz dane dla pokazu

Ten samouczek eksploruje analizę danych sprzedaży biletów. W tym kroku wygenerujesz dane biletu dla wszystkich dzierżawców. W późniejszym kroku te dane są wyodrębniane do analizy. Upewnij się, że została _zainicjowana partia dzierżawców_ (zgodnie z wcześniejszym opisem), dzięki czemu masz wystarczającą ilość danych, aby uwidocznić różne wzorce zakupów biletów.

1. W programie PowerShell ISE Otwórz pozycję *..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* i Ustaw następującą wartość:
    - **$DemoScenario**  =  **1** bilety zakupu dla zdarzeń we wszystkich miejscu
2. Naciśnij klawisz **F5** , aby uruchomić skrypt i utworzyć historię zakupów biletów dla wszystkich miejsc. W przypadku 20 dzierżawców skrypt generuje dziesiątki tysięcy biletów i może trwać 10 minut lub dłużej.

### <a name="deploy-azure-synapse-analytics-data-factory-and-blob-storage"></a>Wdrażanie usługi Azure Synapse Analytics, Data Factory i Blob Storage

W aplikacji bilety Wingtip dane transakcyjne dzierżawców są dystrybuowane za pośrednictwem wielu baz danych. Azure Data Factory (ADF) służy do organizowania wyodrębniania, ładowania i przekształcania (ELT) tych danych w magazynie danych. Aby załadować dane do usługi Azure Synapse Analytics w sposób najbardziej wydajny, funkcja ADF wyodrębnia dane do pośrednich plików obiektów blob, a następnie ładuje [je do hurtowni](../../synapse-analytics/sql-data-warehouse/design-elt-data-loading.md) danych.

Ten krok obejmuje wdrożenie dodatkowych zasobów używanych w samouczku: dedykowanej puli SQL o nazwie _tenantanalytics_, Azure Data Factory o nazwie _dbtodwload- \<user\>_ i konta usługi Azure Storage o nazwie _wingtipstaging \<user\>_. Konto magazynu jest używane do tymczasowego przechowywania wyodrębnionych plików danych jako obiektów BLOB przed ich załadowaniem do magazynu danych. Ten krok umożliwia również wdrożenie schematu magazynu danych i Definiowanie potoków ADF, które organizują proces ELT.

1. W programie PowerShell ISE Otwórz pozycję *..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* i ustaw:
    - **$DemoScenario**  =  **2** wdrażanie magazynu danych analizy dzierżawy, usługi BLOB Storage i usługi Data Factory
1. Naciśnij klawisz **F5** , aby uruchomić skrypt demonstracyjny i wdrożyć zasoby platformy Azure.

Teraz Przejrzyj wdrożone zasoby platformy Azure:

#### <a name="tenant-databases-and-analytics-store"></a>Bazy danych dzierżaw i magazyn analityczny

Użyj [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) , aby połączyć się z serwerami **tenants1-DPT- &lt; User &gt;** i **Catalog-DPT- &lt; User &gt;** . Zastąp &lt; &gt; wartość User wartością używaną podczas wdrażania aplikacji. Użyj nazwy logowania = *Developer* i Password = *P \@ ssword1*. Zobacz [samouczek wprowadzający](./saas-dbpertenant-wingtip-app-overview.md) , aby uzyskać więcej wskazówek.

![Nawiązywanie połączenia z SQL Databaseami z programu SSMS](./media/saas-tenancy-tenant-analytics-adf/ssmsSignIn.JPG)

W Eksplorator obiektów:

1. Rozwiń serwer *tenants1-DPT- &lt; User &gt;* .
1. Rozwiń węzeł bazy danych i Zobacz listę baz danych dzierżaw.
1. Rozwiń węzeł *katalog-DPT- &lt; użytkownika &gt;* .
1. Sprawdź, czy widzisz magazyn analityczny zawierający następujące obiekty:
    1. Tabele **raw_Tickets**, **raw_Customers**, **raw_Events** i **raw_Venues** przechowują nieprzetworzone dane z baz danych dzierżaw.
    1. Tabele schematu gwiazdy to **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** i **dim_Dates**.
    1. Procedura składowana **sp_transformExtractedData** służy do przekształcania danych i ładowania ich do tabel schematu gwiazdy.

![Zrzut ekranu przedstawia Eksplorator obiektów z rozwiniętymi tabelami, aby pokazać różne obiekty bazy danych.](./media/saas-tenancy-tenant-analytics-adf/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage

1. W [Azure Portal](https://ms.portal.azure.com)przejdź do grupy zasobów, która została użyta do wdrożenia aplikacji. Sprawdź, czy zostało dodane konto magazynu o nazwie **wingtipstaging \<user\>** .

   ![DWtables](./media/saas-tenancy-tenant-analytics-adf/adf-staging-storage.PNG)

1. Kliknij pozycję konto magazynu **wingtipstaging \<user\>** , aby poznać obiekty obecne.
1. Kliknij kafelek **obiektów BLOB**
1. Kliknij kontener **ConfigFile**
1. Sprawdź, czy **ConfigFile** zawiera plik JSON o nazwie **TableConfig.json**. Ten plik zawiera nazwy tabel źródłowych i docelowych, nazwy kolumn i nazwy kolumn śledzenia.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)

W [Azure Portal](https://ms.portal.azure.com) w grupie zasobów Sprawdź, czy dodano Azure Data Factory o nazwie _dbtodwload- \<user\>_ .

 ![adf_portal](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory-portal.png)

Ta sekcja eksploruje utworzoną fabrykę danych.
Wykonaj poniższe kroki, aby uruchomić fabrykę danych:

1. W portalu kliknij fabrykę danych o nazwie **dbtodwload- \<user\>**.
2. Kliknij kafelek **tworzenie & monitor** , aby uruchomić projektanta Data Factory na oddzielnej karcie.

## <a name="extract-load-and-transform-data"></a>Wyodrębnij, Załaduj i Przekształć dane

Azure Data Factory służy do organizowania wyodrębniania, ładowania i przekształcania danych. W tym samouczku wyodrębnisz dane z czterech różnych widoków SQL z poszczególnych baz danych dzierżaw: **rawTickets**, **rawCustomers**, **rawEvents** i **rawVenues**. Te widoki obejmują identyfikator miejsca, dzięki czemu można rozróżniać dane z każdego miejsca w magazynie danych. Dane są ładowane do odpowiednich tabel przejściowych w magazynie danych: **raw_Tickets**, **raw_customers**, **raw_Events** i **raw_Venue**. Procedura składowana przekształca pierwotne dane i wypełnia tabele schematu gwiazdy: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** i **dim_Dates**.

W poprzedniej sekcji wdrożono i zainicjowano niezbędne zasoby platformy Azure, w tym fabrykę danych. Wdrożona Fabryka danych obejmuje potoki, zestawy danych, połączone usługi itd., wymagane do wyodrębniania, ładowania i przekształcania danych dzierżawy. Zapoznaj się z tymi obiektami, a następnie Wyzwól potok, aby przenieść dane z baz danych dzierżaw do magazynu danych.

### <a name="data-factory-pipeline-overview"></a>Przegląd potoku usługi Data Factory

Ta sekcja Eksplorowanie obiektów utworzonych w fabryce danych. Na poniższej ilustracji przedstawiono ogólny przepływ pracy potoków ADF użytych w tym samouczku. Jeśli wolisz eksplorować potok później i najpierw zobaczyć wyniki, przejdź do następnej sekcji **Wyzwól uruchomienie potoku**.

![adf_overview](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory.PNG)

Na stronie Przegląd przejdź do karty **autor** w lewym panelu i sprawdź, czy utworzono trzy [potoki](../../data-factory/concepts-pipelines-activities.md) i trzy [zestawy danych](../../data-factory/concepts-datasets-linked-services.md) .
![adf_author](./media/saas-tenancy-tenant-analytics-adf/adf_author_tab.JPG)

Trzy zagnieżdżone potoki to: SQLDBToDW, dbcopy i TableCopy.

**Potok 1 — SQLDBToDW** wyszukuje nazwy baz danych dzierżaw przechowywanych w bazie danych wykazu (nazwa tabeli: [__ShardManagement]. [ ShardsGlobal]) i dla każdej bazy danych dzierżawcy wykonuje potok **dbcopy** . Po zakończeniu podane **sp_TransformExtractedData** schemat procedury składowanej jest wykonywany. Ta procedura składowana przekształca dane załadowane w tabelach przemieszczania i wypełnia tabele schematu gwiazdy.

**Potok 2 — dbcopy** wyszukuje nazwy tabel źródłowych i kolumn z pliku konfiguracji przechowywanego w magazynie obiektów BLOB.  Potok **TableCopy** jest następnie uruchamiany dla każdej z czterech tabel: TicketFacts, CustomerFacts, EventFacts i VenueFacts. Działanie **[foreach](../../data-factory/control-flow-for-each-activity.md)** jest wykonywane równolegle dla wszystkich 20 baz danych. ADF umożliwia równoległe uruchamianie maksymalnie 20 iteracji pętli. Rozważ utworzenie wielu potoków dla większej liczby baz danych.

**Potok 3 — TableCopy** używa numerów wersji wierszy w SQL Database (_rowversion_) do identyfikowania wierszy, które zostały zmienione lub zaktualizowane. To działanie wyszukuje początkową i końcową wersję wiersza w celu wyodrębnienia wierszy z tabel źródłowych. Tabela **CopyTracker** przechowywana w każdej bazie danych dzierżawy śledzi ostatni wiersz wyodrębniony z każdej tabeli źródłowej w każdym uruchomieniu. Nowe lub zmienione wiersze są kopiowane do odpowiednich tabel przejściowych w magazynie danych: **raw_Tickets**, **raw_Customers**, **raw_Venues** i **raw_Events**. Na koniec Ostatnia wersja wiersza jest zapisywana w tabeli **CopyTracker** , która będzie używana jako początkowa wersja wiersza dla następnej wyodrębniania.

Istnieją również trzy sparametryzowane połączone usługi, które łączą fabrykę danych z źródłowymi bazami danych SQL, docelową dedykowaną pulą SQL i pośredni magazyn obiektów BLOB. Na karcie **autor** kliknij pozycję **połączenia** , aby poznać połączone usługi, jak pokazano na poniższej ilustracji:

![adf_linkedservices](./media/saas-tenancy-tenant-analytics-adf/linkedservices.JPG)

Odpowiadające trzem połączonym usługom istnieją trzy zestawy danych, które odwołują się do danych używanych w działaniach potoku jako dane wejściowe lub wyjściowe. Przejrzyj wszystkie zestawy danych, aby obserwować używane połączenia i parametry. _AzureBlob_ wskazuje plik konfiguracji zawierający źródłową i docelową tabelę i kolumny, a także kolumnę śledzenia w poszczególnych źródłach.
  
### <a name="data-warehouse-pattern-overview"></a>Wzorzec magazynu danych — przegląd

Usługa Azure Synapse jest używana jako magazyn analityczny do przeprowadzania agregacji danych dzierżawy. W tym przykładzie baza jest używana do ładowania danych do magazynu danych. Dane pierwotne są ładowane do tabel przemieszczania, które mają kolumnę tożsamości, aby śledzić wiersze, które zostały przekształcone w tabele schematu gwiazdy. Na poniższej ilustracji przedstawiono wzorzec ładowania: ![ diagram przedstawia wzorzec ładowania tabel bazy danych.](./media/saas-tenancy-tenant-analytics-adf/loadingpattern.JPG)

W tym przykładzie użyto wolno zmieniającego się tabel wymiarów typu 1 (SCD). Każdy wymiar ma klucz zastępczy zdefiniowany przy użyciu kolumny tożsamości. Najlepszym rozwiązaniem jest wstępne wypełnienie tabeli wymiarów daty w celu zaoszczędzenia czasu. W przypadku innych tabel wymiarów CREATE TABLE jako wybrane... (CTAS) służy do tworzenia tymczasowej tabeli zawierającej istniejące zmodyfikowane i niemodyfikowane wiersze wraz z kluczami zastępczymi. Jest to realizowane z IDENTITY_INSERT = ON. Nowe wiersze są następnie wstawiane do tabeli przy użyciu IDENTITY_INSERT = OFF. W celu zapewnienia łatwego wycofywania Nazwa istniejącej tabeli wymiarów jest zmieniana, a nazwa tabeli tymczasowej zostanie zmieniona, aby stała się nową tabelą wymiarów. Przed każdym uruchomieniem, stara tabela wymiarów zostanie usunięta.

Tabele wymiarów są ładowane przed tabelą faktów. Ta kolejność zapewnia, że dla każdego dochodzącego faktu wszystkie wymiary, do których istnieją odwołania, już istnieją. Po załadowaniu faktów klucz biznesowy dla każdego odpowiadającego wymiaru jest dopasowywany, a odpowiednie klucze zastępcze są dodawane do każdego faktu.

Ostatni krok przekształcenia powoduje usunięcie danych przemieszczania gotowych do następnego wykonania potoku.

### <a name="trigger-the-pipeline-run"></a>Wyzwalanie uruchomienia potoku

Wykonaj poniższe kroki, aby uruchomić kompletny potok wyodrębniania, ładowania i przekształcania dla wszystkich baz danych dzierżawy:

1. Na karcie **autor** w interfejsie użytkownika ADF wybierz pozycję potok **SQLDBToDW** w okienku po lewej stronie.
1. Kliknij pozycję **wyzwalacz** i w menu rozwijanym kliknij pozycję **Wyzwól teraz**. Ta akcja powoduje natychmiastowe uruchomienie potoku. W scenariuszu produkcyjnym należy zdefiniować harmonogram uruchamiania potoku w celu odświeżenia danych zgodnie z harmonogramem.
  ![Zrzut ekranu przedstawia zasoby fabryki dla potoku o nazwie S Q L D B do D w z opcją wyzwalacza rozwinięte i wyzwalane teraz.](./media/saas-tenancy-tenant-analytics-adf/adf_trigger.JPG)
1. Na stronie **uruchomienie potoku** kliknij przycisk **Zakończ**.

### <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. W interfejsie użytkownika usługi ADF przejdź do karty **monitor** z menu po lewej stronie.
1. Kliknij przycisk **Odśwież** , dopóki stan potoku SQLDBToDW **zakończył się pomyślnie**.
  ![Zrzut ekranu przedstawia potok S p D B do D W potoku ze stanem powodzenie.](./media/saas-tenancy-tenant-analytics-adf/adf_monitoring.JPG)
1. Połącz się z magazynem danych za pomocą programu SSMS i zbadaj tabele schematu gwiazdy, aby sprawdzić, czy dane zostały załadowane w tych tabelach.

Po zakończeniu potoku tabela faktów zawiera dane sprzedaży biletów dla wszystkich miejsc, a tabele wymiarów są wypełniane odpowiednimi miejscami, zdarzeniami i klientami.

## <a name="data-exploration"></a>Eksploracja danych

### <a name="visualize-tenant-data"></a>Wizualizacja danych dzierżawy

Dane w schemacie gwiazdy zawierają wszystkie dane sprzedaży biletów potrzebnych do analizy. Wizualizacja danych ułatwia wyświetlanie trendów w dużych zestawach danych. W tej sekcji użyto **Power BI** do manipulowania i wizualizacji danych dzierżawy w magazynie danych.

Wykonaj następujące kroki, aby nawiązać połączenie z usługą Power BI i zaimportować utworzone wcześniej widoki:

1. Uruchom Power BI pulpicie.
2. Na Wstążce Narzędzia główne wybierz pozycję **Pobierz dane**, a następnie wybierz pozycję **więcej...** z menu.
3. W oknie **pobieranie danych** wybierz pozycję **Azure SQL Database**.
4. W oknie Logowanie do bazy danych wprowadź nazwę serwera (**Catalog-DPT- &lt; User &gt; . Database.Windows.NET**). Wybierz pozycję **Importuj** dla **trybu łączności danych**, a następnie kliknij przycisk **OK**.

    ![Logowanie do usługi Power BI](./media/saas-tenancy-tenant-analytics-adf/powerBISignIn.PNG)

5. W lewym okienku wybierz pozycję **baza danych** , a następnie wprowadź nazwę użytkownika = *deweloper* i wprowadź hasło = *P \@ ssword1*. Kliknij przycisk **Połącz**.  

    ![Baza danych — logowanie](./media/saas-tenancy-tenant-analytics-adf/databaseSignIn.PNG)

6. W okienku **Nawigator** w obszarze baza danych analizy wybierz tabele ze schematem gwiazdy: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** i **dim_Dates**. Następnie wybierz pozycję **Załaduj**.

Gratulacje! Dane zostały pomyślnie załadowane do Power BI. Teraz Eksploruj interesujące wizualizacje, aby uzyskać wgląd w dzierżawy. Zapoznaj się z tematem, jak analizy mogą zapewnić pewne zalecenia oparte na danych w zespole biznesowym biletów Wingtip. Zalecenia mogą pomóc zoptymalizować model biznesowy i obsługę klienta.

Zacznij od analizowania danych sprzedaży biletów, aby zobaczyć różnice w użyciu w miejscu. Wybierz opcje widoczne w Power BI, aby wykreślić wykres słupkowy łącznej liczby biletów sprzedawanych przez każdy z miejsc. (Ze względu na losową odmianę generatora biletu wyniki mogą się różnić).

![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/TotalTicketsByVenues-DW.PNG)

Powyższy wykres potwierdza, że liczba biletów sprzedawanych przez każdy z miejsc jest różna. Miejsca, w których sprzedawane są więcej biletów, są bardziej silniej niż miejsca, w których sprzedawane są mniejsze bilety. W tym miejscu może istnieć możliwość dostosowywania alokacji zasobów zgodnie z różnymi potrzebami dzierżawców.

Możesz przeanalizować dane, aby zobaczyć, w jaki sposób sprzedaż biletów różni się w miarę upływu czasu. Wybierz opcje widoczne na poniższej ilustracji w Power BI, aby wykreślić łączną liczbę biletów sprzedawanych każdego dnia przez okres 60 dni.

![SaleVersusDate](./media/saas-tenancy-tenant-analytics-adf/SaleVersusDate-DW.PNG)

Na poprzednim wykresie przedstawiono wzrost sprzedaży biletów dla niektórych miejsc. Te szczyty wzmacniają, że niektóre miejsca mogą zużywać zasoby systemowe w nieproporcjonalnym charakterze. Do tej pory nie ma żadnego oczywistego wzorca podczas wystąpienia.

Następnie Zbadaj istotność tych dni sprzedaży szczytowej. Kiedy te wartości szczytowe występują po sprzedaży biletów? Aby wykreślić bilety sprzedawane dziennie, wybierz opcje pokazane na poniższej ilustracji w Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics-adf/SaleDistributionPerDay-DW.PNG)

Ten wykres pokazuje, że niektóre miejsca sprzedajeją dużą liczbę biletów w pierwszym dniu sprzedaży. Gdy tylko bilety przechodzą do sprzedaży w tych miejscu, prawdopodobnie istnieje Mad — szczytu. Ta seria działań przez kilka miejsc może mieć wpływ na usługę dla innych dzierżawców.

Możesz ponownie przejść do danych, aby sprawdzić, czy ten Mad — szczytu ma wartość true dla wszystkich zdarzeń hostowanych przez te miejsca. W poprzednich wykresach wykorzystano, że korytarze w sieci Contoso sprzedaje wiele biletów, a firma Contoso ma także wzrost sprzedaży biletów w określonych dniach. Załącz się z opcjami Power BI, aby wykreślić skumulowaną sprzedaż biletów dla korytarza wspólnie otaczającego, skupiając się na trendach sprzedaży dla każdego z jego wydarzeń. Czy wszystkie zdarzenia są zgodne z tym samym wzorcem sprzedaży? Spróbuj utworzyć wykres podobny do przedstawionego poniżej.

![ContosoSales](./media/saas-tenancy-tenant-analytics-adf/EventSaleTrends.PNG)

Ten wykres łącznej sprzedaży biletów w czasie dla korytarza wspólnie Contoso dla każdego zdarzenia wskazuje, że Mad — szczytu nie występuje dla wszystkich zdarzeń. Zapoznaj się z opcjami filtru, aby poznać trendy sprzedaży dla innych miejsc.

Szczegółowe informacje na temat wzorców sprzedaży biletów mogą prowadzić do Wingtip biletów, aby zoptymalizować model biznesowy. Zamiast naliczania wszystkich dzierżawców w równym stopniu, prawdopodobnie Wingtip powinny wprowadzać warstwy usług o różnych rozmiarach obliczeniowych. Większą liczbą miejsc, które muszą sprzedawać więcej biletów dziennie, może być oferowana wyższa warstwa z wyższą umową dotyczącą poziomu usług (SLA). Te miejsca mogą mieć swoje bazy danych umieszczane w puli z wyższymi limitami zasobów dla poszczególnych baz danych. Każda warstwa usług może mieć co godzinę alokację sprzedaży przy użyciu dodatkowych opłat naliczanych za przekroczenie przydziału. Większe miejsca, w których okresowe rozbicie sprzedaży byłyby korzystne dla wyższych warstw, a bilety Wingtip mogą wydajnie Zarabiaj swoją usługę.

Tymczasem niektórzy Wingtip bilety, z których klienci mogą się zapewnić, aby sprzedać wystarczającą liczbę biletów, aby uzasadnić koszt usługi. W tych informacjach można zwiększyć sprzedaż biletów w przypadku niedopełnienia miejsc. Wyższa sprzedaż mogłaby zwiększyć postrzeganą wartość usługi. Kliknij prawym przyciskiem myszy fact_Tickets i wybierz pozycję **Nowa miara**. Wprowadź następujące wyrażenie dla nowej miary o nazwie **AverageTicketsSold**:

```sql
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Wybierz poniższe opcje wizualizacji, aby wykreślić bilety procentowe sprzedane przez każdy z nich, aby określić ich względną skuteczność.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/AvgTicketsByVenues-DW.PNG)

Na wykreślonym przykładzie widać, że chociaż większość miejsc sprzedaje ponad 80% swoich biletów, niektóre z nich zoptymalizowaniem się, aby wypełnić więcej niż połowę ich stanowisk. Zanotuj wszystkie wartości, aby wybrać maksymalną lub minimalną stawkę procentową biletów sprzedawanych w każdym miejscu.

## <a name="embedding-analytics-in-your-apps"></a>Osadzanie analiz w aplikacjach

Ten samouczek koncentruje się na analizie obejmującej wiele dzierżawców służącej do ulepszania ich dzierżawców przez dostawcę oprogramowania. Analiza może również udostępniać informacje dla _dzierżawców_, aby ułatwić im efektywniejsze zarządzanie swoimi firmami.

W przykładzie biletów Wingtip został wcześniej odnaleziony, że sprzedaż biletów była zgodna z przewidywalnymi wzorcami. Ten wgląd w szczegółowe dane może pomóc w podkorzystaniu miejsc, które zwiększają sprzedaż biletów. Prawdopodobnie istnieje możliwość zastosowania technik uczenia maszynowego do przewidywania sprzedaży biletów dla zdarzeń. Efekty zmian cen mogą być również modelowane, co pozwala na przewidywanie wpływu rabatów na oferty. Power BI Embedded można zintegrować z aplikacją zarządzania zdarzeniami w celu wizualizowania przewidywanych sprzedaży, w tym wpływu rabatów na łączną liczbę sprzedanych miejsc i przychodów w przypadku wydarzeń z niską sprzedażą. Korzystając z Power BI Embedded, możesz nawet zintegrować rzeczywiste stosowanie rabatu do cen biletów, bezpośrednio w środowisku wizualizacji.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Utwórz magazyn analizy dzierżawy do załadowania.
> - Użyj Azure Data Factory (ADF) do wyodrębnienia danych z każdej bazy danych dzierżawy do magazynu danych analizy.
> - Zoptymalizuj wyodrębnione dane (uporządkuj je w schemat gwiazdki).
> - Zbadaj magazyn danych analizy.
> - Użyj Power BI do wizualizacji danych, aby wyróżnić trendy w danych dzierżawy i zaleceń dotyczących ulepszeń.

Gratulacje!

## <a name="additional-resources"></a>Dodatkowe zasoby

- Dodatkowe [samouczki, które kompilują się po aplikacji Wingtip SaaS](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).