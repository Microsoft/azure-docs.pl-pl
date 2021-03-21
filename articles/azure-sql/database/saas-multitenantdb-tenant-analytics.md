---
title: Uruchamianie zapytań analitycznych
description: Zapytania analizy obejmującej wiele dzierżawców korzystające z danych wyodrębnionych z wielu baz danych Azure SQL Database w aplikacji wielodostępnej.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: f12c823f609ac309d4b5ddbbaa7d5a076a7bb9ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96447300"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Analiza wielu dzierżawców z użyciem wyodrębnionych danych — aplikacji z wieloma dzierżawcami
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
W tym samouczku przedstawiono kompletny scenariusz analizy dla implementacji wielodostępnej. W tym scenariuszu pokazano, jak analiza może umożliwić firmom podejmowanie szybkich decyzji. Korzystając z danych wyodrębnionych z bazy danych podzielonej na fragmenty, możesz korzystać z analiz, aby uzyskiwać wgląd w zachowanie dzierżawy, w tym ich użycie w przykładowej aplikacji Wingtip biletów SaaS. Ten scenariusz obejmuje trzy kroki: 

1.  **Wyodrębnij dane** z każdej bazy danych dzierżawy do sklepu analitycznego.
2.  **Zoptymalizuj wyodrębnione dane** na potrzeby przetwarzania analizy.
3.  Skorzystaj z narzędzi **analizy biznesowej** , aby narysować przydatne informacje, które mogą posłużyć do podejmowania decyzji. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Utwórz magazyn analizy dzierżawców, aby wyodrębnić dane do programu.
> - Korzystaj z zadań elastycznych, aby wyodrębnić dane z każdej bazy danych dzierżawy do magazynu analizy.
> - Zoptymalizuj wyodrębnione dane (uporządkuj je w schemat gwiazdki).
> - Zbadaj bazę danych analizy.
> - Użyj Power BI do wizualizacji danych, aby wyróżnić trendy w danych dzierżawy i zaleceń dotyczących ulepszeń.

![Diagram przedstawia przegląd architektury używanej w tym artykule.](./media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Wzorzec analizy dzierżawy w trybie offline

Wdrażane aplikacje SaaS mają dostęp do ogromnych ilości danych dzierżawy przechowywanych w chmurze. Dane zawierają bogate źródło szczegółowych informacji o operacjach i użyciu aplikacji oraz o zachowaniu dzierżawców. Te informacje mogą być pomocne podczas opracowywania funkcji, ulepszeń użyteczności i innych inwestycji w aplikację i platformę.

Uzyskiwanie dostępu do danych dla wszystkich dzierżawców jest proste, gdy wszystkie dane są w tylko jednej bazie danych z wieloma dzierżawcami. Jednak dostęp jest bardziej złożony, gdy jest dystrybuowany na dużą skalę w tysiącach baz danych. Jednym ze sposobów oswoić złożoności jest wyodrębnienie danych do bazy danych analiz lub hurtowni danych. Następnie należy wykonać zapytanie dotyczące magazynu danych w celu zebrania szczegółowych informacji z biletów wszystkich dzierżawców.

Ten samouczek przedstawia kompletny scenariusz analizy dla tej przykładowej aplikacji SaaS. Najpierw zadania elastyczne są używane do planowania wyodrębniania danych z każdej bazy danych dzierżawy. Dane są wysyłane do sklepu analitycznego. Sklep analityczny może być SQL Database lub analizą usługi Azure Synapse. W przypadku wyodrębniania danych na dużą skalę [Azure Data Factory](../../data-factory/introduction.md) jest Commended.

Następnie zagregowane dane są Shredded do zestawu tabel ze [schematem gwiazdy](https://www.wikipedia.org/wiki/Star_schema) . Tabele składają się z centralnej tabeli faktów oraz powiązanych tabel wymiarów:

- Centralna tabela faktów w schemacie gwiazdy zawiera dane biletów.
- Tabele wymiarów zawierają dane dotyczące miejsc, zdarzeń, klientów i dat zakupu.

Tabele centralne i wymiary umożliwiają wydajne przetwarzanie analityczne. Schemat gwiazdy używany w tym samouczku jest wyświetlany na poniższej ilustracji:
 
![Diagram bazy danych pokazuje cztery obiekty bazy danych połączone z centralnym obiektem bazy danych.](./media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Na koniec są badane zapytania dotyczące tabel schematu gwiazdy. Wyniki zapytania są wyświetlane wizualnie, aby wyróżnić szczegółowe informacje o zachowaniu dzierżawy i ich użyciu. Za pomocą tego schematu gwiazdy można uruchamiać zapytania, które ułatwiają odnajdywanie elementów, takich jak następujące:

- Kto kupuje bilety i od których miejsca.
- Ukryte wzorce i trendy w następujących obszarach:
    - Sprzedaż biletów.
    - Relatywna popularność każdego miejsca.

Zrozumienie, jak spójne każdy dzierżawca korzysta z usługi, oferuje możliwość tworzenia planów usług w celu zaspokojenia ich potrzeb. Ten samouczek zawiera podstawowe przykłady szczegółowych informacji, które mogą być wydobyć z danych dzierżawy.

## <a name="setup"></a>Konfiguracja

### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

- Wdrożono Wingtip biletów SaaS aplikację bazy danych z wieloma dzierżawami. Aby wdrożyć program w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji bazy danych z obsługą wielu dzierżawców Wingtip SaaS](./saas-multitenantdb-get-started-deploy.md)
- Skrypty Wingtip SaaS i [kod źródłowy](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) aplikacji są pobierane z usługi GitHub. Pamiętaj, aby *odblokować plik zip* przed wyodrębnieniem jego zawartości. Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania Wingtip biletów SaaS.
- Power BI Desktop jest zainstalowany. [Pobieranie programu Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Partia dodatkowych dzierżawców została zainicjowana, zapoznaj się z [**samouczkiem Inicjowanie obsługi dzierżaw**](./saas-multitenantdb-provision-and-catalog.md).
- Utworzono agenta zadań i bazy danych agenta zadań. Zapoznaj się z odpowiednimi krokami w [**samouczku dotyczącym zarządzania schematami**](./saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Utwórz dane dla pokazu

W tym samouczku analiza jest wykonywana na danych sprzedaży biletów. W bieżącym kroku są generowane dane biletów dla wszystkich dzierżawców.  Później te dane są wyodrębniane do analizy. *Upewnij się, że masz zainicjowaną partię dzierżawców zgodnie z wcześniejszym opisem, aby uzyskać zrozumiałą ilość danych*. Wystarczająco duże ilości danych mogą uwidaczniać różne wzorce zakupów biletów.

1. W programie **POWERSHELL ISE** Otwórz pozycję *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* i Ustaw następującą wartość:
    - **$DemoScenario**  =  **1** bilety zakupu dla zdarzeń we wszystkich miejscu
2. Naciśnij klawisz **F5** , aby uruchomić skrypt i utworzyć historię zakupów biletów dla każdego zdarzenia w każdym miejscu.  Skrypt jest uruchamiany przez kilka minut na wygenerowanie dziesiątek tysięcy biletów.

### <a name="deploy-the-analytics-store"></a>Wdrażanie sklepu analitycznego
Często istnieje wiele transakcyjnych baz danych podzielonej na fragmenty, które razem przechowują wszystkie dane dzierżawy. Należy agregować dane dzierżawy z bazy danych podzielonej na fragmenty do jednego sklepu analitycznego. Agregacja umożliwia wydajne wykonywanie zapytań dotyczących danych. W tym samouczku baza danych Azure SQL Database jest używana do przechowywania zagregowanych danych.

W poniższych krokach zostanie wdrożony magazyn analityczny o nazwie **tenantanalytics**. Wdrożono również wstępnie zdefiniowane tabele, które są umieszczane w dalszej części tego samouczka:
1. W programie PowerShell ISE Otwórz *..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Ustaw zmienną $DemoScenario w skrypcie, aby pasowała do wybranego sklepu analitycznego. W celach edukacyjnych nie zaleca się korzystania z bazy danych bez magazynu kolumn.
    - Aby użyć SQL Database bez magazynu kolumn, ustaw **$DemoScenario**  =  **2**
    - Aby użyć SQL Database z magazynem kolumn, ustaw **$DemoScenario**  =  **3**  
3. Naciśnij klawisz **F5** , aby uruchomić skrypt demonstracyjny (wywołujący skrypt *Deploy-TenantAnalytics \<XX> . ps1* ), który tworzy magazyn analizy dzierżawców. 

Teraz, gdy aplikacja została wdrożona i uzupełniona o interesujące dane dzierżawy, użyj [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) , aby połączyć program **tenants1 \<User\> -MT-** and **Catalog \<User\> -MT-** Server przy użyciu polecenia login = *Developer*, Password = *P \@ ssword1*.

![architectureOverView](./media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

W Eksplorator obiektów wykonaj następujące czynności:

1. Rozwiń węzeł *tenants1-MT- \<User\>* Server.
2. Rozwiń węzeł bazy danych i zobacz *tenants1* Database zawierający wiele dzierżawców.
3. Rozwiń węzeł *katalog-MT- \<User\>* Server.
4. Sprawdź, czy jest widoczny magazyn analizy i baza danych jobaccount.

Zobacz następujące elementy bazy danych w narzędziu SSMS Eksplorator obiektów, rozszerzając węzeł sklepu Analytics:

- Tabele **TicketsRawData** i **EventsRawData** przechowują nieprzetworzone dane wyodrębnione z baz danych dzierżaw.
- Tabele schematu gwiazdy to **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** i **dim_Dates**.
- **Sp_ShredRawExtractedData** procedury składowanej służy do wypełniania tabel schematu gwiazdy z nieprzetworzonych tabel danych.

![Zrzut ekranu przedstawia Eksplorator obiektów S s S dla węzła magazyn analityczny, w tym tabele, widoki i węzły.](./media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Wyodrębnianie danych 

### <a name="create-target-groups"></a>Tworzenie grup docelowych 

Przed kontynuowaniem upewnij się, że wdrożono konto zadania i bazę danych jobaccount. W następnym zestawie kroków zadania elastyczne są używane do wyodrębniania danych z bazy danych dzierżaw podzielonej na fragmenty oraz do przechowywania danych w sklepie analitycznym. Następnie drugie zadanie shreds dane i zapisuje je w tabelach w schemacie gwiazdy. Te dwa zadania są uruchamiane w odniesieniu do dwóch różnych grup docelowych, a mianowicie **dzierżawców** i grupy **analizy**. Zadanie wyodrębniania działa w odniesieniu do grupy dzierżawców, która zawiera wszystkie bazy danych dzierżaw. Zadanie shredding jest uruchamiane względem grupy analiz, która zawiera tylko magazyn analityczny. Utwórz grupy docelowe, wykonując następujące czynności:

1. W programie SSMS Nawiąż połączenie z bazą danych **jobaccount** w katalogu-MT- \<User\> .
2. W programie SSMS Otwórz pozycję *. ..\Learning Modules\Operational Analytics\Tenant Analytics \ TargetGroups. SQL* 
3. Zmodyfikuj @User zmienną u góry skryptu, zastępując `<User>` wartość użytkownika używaną podczas wdrażania wielodostępnej Wingtip biletów SaaS aplikacji bazy danych z obsługą wielu dzierżawców.
4. Naciśnij klawisz **F5** , aby uruchomić skrypt, który tworzy dwie grupy docelowe.

### <a name="extract-raw-data-from-all-tenants"></a>Wyodrębnij dane pierwotne ze wszystkich dzierżawców

Transakcje mogą występować częściej dla danych *biletu i klienta* niż w przypadku danych dotyczących *zdarzeń i miejsc* . W związku z tym Rozważ wyodrębnienie danych biletu i klienta oddzielnie i częściej niż wyodrębnianie danych zdarzeń i miejsc. W tej sekcji definiujesz i planujesz dwa oddzielne zadania:

- Wyodrębnij dane biletu i klienta.
- Wyodrębnij dane zdarzenia i miejsca.

Każde zadanie wyodrębnia swoje dane i zapisuje je w sklepie analitycznym. Oddzielne zadanie shreds wyodrębnione dane do programu gwiazdy Analytics-Schema.

1. W programie SSMS Nawiąż połączenie z bazą danych **jobaccount** w katalogu-MT- \<User\> Server.
2. W programie SSMS Otwórz pozycję *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.SQL*.
3. Zmodyfikuj @User u góry skryptu i Zastąp `<User>` ciąg nazwą użytkownika używaną podczas wdrażania wielodostępnej aplikacji bazy danych Wingtip SaaS. 
4. Naciśnij klawisz **F5** , aby uruchomić skrypt, który tworzy i uruchamia zadanie wyodrębniające bilety i dane klientów z każdej bazy danych dzierżaw. Zadanie zapisuje dane w sklepie analizy.
5. Wykonaj zapytanie dotyczące tabeli TicketsRawData w bazie danych tenantanalytics, aby upewnić się, że tabela została wypełniona informacjami o biletach ze wszystkich dzierżawców.

![Zrzut ekranu przedstawia bazę danych ExtractTickets z TicketsRawDataą d b o wybranym w Eksplorator obiektów.](./media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Powtórz powyższe kroki, z wyjątkiem tego, że Zastąp **\ExtractTickets.SQL** with **\ExtractVenuesEvents.SQL** w kroku 2.

Pomyślne uruchomienie zadania spowoduje wypełnienie tabeli EventsRawData w sklepie analitycznym nowymi zdarzeniami i umieszczenie informacji ze wszystkich dzierżawców. 

## <a name="data-reorganization"></a>Reorganizacja danych

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Shred wyodrębnione dane, aby wypełnić tabele schematu gwiazdy

Następnym krokiem jest Shred wyodrębnionych nieprzetworzonych danych do zestawu tabel, które są zoptymalizowane pod kątem zapytań analitycznych. Używany jest schemat gwiazdy. Centralna tabela faktów zawiera poszczególne rekordy sprzedaży biletów. Tabele wymiarów są wypełniane danymi dotyczącymi miejsc, zdarzeń, klientów i dat zakupu. 

W tej części samouczka zdefiniujesz i uruchomisz zadanie, które scala wyodrębnione dane pierwotne z danymi w tabelach schematu gwiazdy. Po zakończeniu zadania scalania dane pierwotne zostaną usunięte, pozostawiając tabele gotowe do wypełnienia przez następne zadanie wyodrębnienia danych dzierżawy.

1. W programie SSMS Nawiąż połączenie z bazą danych **jobaccount** w katalogu-MT- \<User\> .
2. W programie SSMS Otwórz pozycję *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.SQL*.
3. Naciśnij klawisz **F5** , aby uruchomić skrypt w celu zdefiniowania zadania, które wywołuje procedurę składowaną sp_ShredRawExtractedData w sklepie analizy.
4. Poczekaj, aż zadanie zostanie pomyślnie uruchomione.
    - Sprawdź kolumnę **cykl życia** tabeli jobs.jobs_execution dla stanu zadania. Przed kontynuowaniem upewnij się, że zadanie **zakończyło się pomyślnie** . Pomyślne uruchomienie wyświetla dane podobne do poniższego wykresu:

![Zrzut ekranu przedstawia pomyślne uruchomienie procedury sp_ShredRawExtractedData.](./media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Eksploracja danych

### <a name="visualize-tenant-data"></a>Wizualizacja danych dzierżawy

Dane w tabeli ze schematem gwiazdy zawierają wszystkie dane sprzedaży biletów potrzebnych do analizy. Aby ułatwić wyświetlanie trendów w dużych zestawach danych, należy wizualizować ją graficznie.  W tej sekcji dowiesz się, jak używać **Power BI** do manipulowania i wizualizacji danych dzierżawy, które zostały wyodrębnione i zorganizowane.

Wykonaj następujące kroki, aby nawiązać połączenie z usługą Power BI i zaimportować utworzone wcześniej widoki:

1. Uruchom Power BI pulpicie.
2. Na Wstążce Narzędzia główne wybierz pozycję **Pobierz dane**, a następnie wybierz pozycję **więcej...** z menu.
3. W oknie **pobieranie danych** wybierz pozycję Azure SQL Database.
4. W oknie Logowanie do bazy danych wprowadź nazwę serwera (Catalog-MT- \<User\> . Database.Windows.NET). Wybierz pozycję **Importuj** dla **trybu łączności danych**, a następnie kliknij przycisk OK. 

    ![Zrzut ekranu przedstawia okno dialogowe SQL Server Database, w którym można wprowadzić serwer i bazę danych.](./media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. W lewym okienku wybierz pozycję **baza danych** , a następnie wprowadź nazwę użytkownika = *deweloper* i wprowadź hasło = *P \@ ssword1*. Kliknij przycisk **Połącz**.  

    ![Zrzut ekranu przedstawia okno dialogowe baza danych SQL Server, w którym można wprowadzić nazwę użytkownika i hasło.](./media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. W okienku **Nawigator** w obszarze baza danych analizy wybierz tabele ze schematem gwiazdy: fact_Tickets, dim_Events, dim_Venues, dim_Customers i dim_Dates. Następnie wybierz pozycję **Załaduj**. 

Gratulacje! Dane zostały pomyślnie załadowane do Power BI. Teraz możesz zacząć Eksplorowanie interesujących wizualizacji, aby ułatwić uzyskanie wglądu w dzierżawy. Następnym zapoznaj się z tematem, w jaki sposób analiza może pozwolić na zapewnienie zaleceń opartych na danych w zespole biznesowym biletów Wingtip. Zalecenia mogą pomóc zoptymalizować model biznesowy i obsługę klienta.

Zacznij od analizowania danych sprzedaży biletów, aby zobaczyć zmiany w użyciu w miejscu. Wybierz poniższe opcje w Power BI, aby wyświetlić wykres słupkowy łącznej liczby biletów sprzedawanych przez każdy z miejsc. Ze względu na losową odmianę generatora biletów wyniki mogą się różnić.
 
![Zrzut ekranu przedstawia wizualizację i kontrolki dla wizualizacji danych po prawej stronie.](./media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Powyższy wykres potwierdza, że liczba biletów sprzedawanych przez każdy z miejsc jest różna. Miejsca, w których sprzedawane są więcej biletów, są bardziej silniej niż miejsca, w których sprzedawane są mniejsze bilety. W tym miejscu może istnieć możliwość dostosowywania alokacji zasobów zgodnie z różnymi potrzebami dzierżawców.

Możesz przeanalizować dane, aby zobaczyć, w jaki sposób sprzedaż biletów różni się w miarę upływu czasu. Wybierz poniższe opcje w Power BI, aby wykreślić łączną liczbę biletów sprzedawanych każdego dnia przez okres 60 dni.
 
![Zrzut ekranu przedstawia wizualizację B I o tytule dystrybucja sprzedaży biletów I dzień sprzedaży.](./media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Powyższy wykres przedstawia wzrost sprzedaży biletów dla niektórych miejsc. Te szczyty wzmacniają, że niektóre miejsca mogą zużywać zasoby systemowe w nieproporcjonalnym charakterze. Do tej pory nie ma żadnego oczywistego wzorca podczas wystąpienia.

Następnie chcesz dokładniej zbadać znaczenie tych dni sprzedaży szczytowej. Kiedy te wartości szczytowe występują po sprzedaży biletów? Aby wykreślić bilety sprzedawane dziennie, wybierz poniższe opcje w Power BI.

![SaleDayDistribution](./media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Powyższy wykres pokazuje, że niektóre miejsca sprzedajeją wiele biletów w pierwszym dniu sprzedaży. Gdy tylko bilety przechodzą do sprzedaży w tych miejscu, prawdopodobnie istnieje Mad — szczytu. Ta seria działań przez kilka miejsc może mieć wpływ na usługę dla innych dzierżawców.

Możesz ponownie przejść do danych, aby sprawdzić, czy ten Mad — szczytu ma wartość true dla wszystkich zdarzeń hostowanych przez te miejsca. W poprzednich wykresach zauważono, że korytarze w firmie Contoso sprzedaje wiele biletów, a firma Contoso ma także wzrost sprzedaży biletów w określonych dniach. Załącz się z opcjami Power BI, aby wykreślić skumulowaną sprzedaż biletów dla korytarza wspólnie otaczającego, skupiając się na trendach sprzedaży dla każdego z jego wydarzeń. Czy wszystkie zdarzenia są zgodne z tym samym wzorcem sprzedaży?

![ContosoSales](./media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Powyższy wykres z korytarzem z firmy Contoso wspólnie pokazuje, że Mad — szczytu nie występuje dla wszystkich zdarzeń. Zapoznaj się z opcjami filtru, aby zobaczyć trendy sprzedaży dla innych miejsc.

Szczegółowe informacje na temat wzorców sprzedaży biletów mogą prowadzić do Wingtip biletów, aby zoptymalizować model biznesowy. Zamiast naliczania wszystkich dzierżawców w równym stopniu, prawdopodobnie Wingtip powinny wprowadzać warstwy usług o różnych rozmiarach obliczeniowych. Większą liczbą miejsc, które muszą sprzedawać więcej biletów dziennie, może być oferowana wyższa warstwa z wyższą umową dotyczącą poziomu usług (SLA). Te miejsca mogą mieć swoje bazy danych umieszczane w puli z wyższymi limitami zasobów dla poszczególnych baz danych. Każda warstwa usług może mieć co godzinę alokację sprzedaży przy użyciu dodatkowych opłat naliczanych za przekroczenie przydziału. Większe miejsca, w których okresowe rozbicie sprzedaży byłyby korzystne dla wyższych warstw, a bilety Wingtip mogą wydajnie Zarabiaj swoją usługę.

Tymczasem niektórzy Wingtip bilety, z których klienci mogą się zapewnić, aby sprzedać wystarczającą liczbę biletów, aby uzasadnić koszt usługi. W tych informacjach może być możliwe zwiększenie sprzedaży biletów w ramach wykonywania miejsc. Wyższa sprzedaż mogłaby zwiększyć postrzeganą wartość usługi. Kliknij prawym przyciskiem myszy fact_Tickets i wybierz pozycję **Nowa miara**. Wprowadź następujące wyrażenie dla nowej miary o nazwie **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Wybierz poniższe opcje wizualizacji, aby wykreślić bilety procentowe sprzedane przez każdy z nich, aby określić ich względną skuteczność.

![analyticsViews](./media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Powyższy wykres pokazuje, że mimo że większość miejsc sprzedaje ponad 80% swoich biletów, niektóre z nich zoptymalizowaniem się, aby wypełnić więcej niż połowę stanowisk. Zanotuj wszystkie wartości, aby wybrać maksymalną lub minimalną stawkę procentową biletów sprzedawanych w każdym miejscu.

Wcześniej można było pogłębić analizę, aby odkryć, że sprzedaż biletów jest zgodna z przewidywalnymi wzorcami. To odnajdowanie może umożliwić biletom Wingtip zwiększenie sprzedaży biletów dzięki zalecaniu dynamicznych cen. To odnajdowanie może ujawnić możliwość zastosowania technik uczenia maszynowego do przewidywania sprzedaży biletów dla każdego zdarzenia. Możliwe jest również prognozowanie wpływu przychodów oferty na sprzedaż biletów. Power BI Embedded można zintegrować z aplikacją zarządzania zdarzeniami. Integracja może pomóc w wizualizowaniu przewidywanych sprzedaży i wpływie różnych rabatów. Aplikacja może pomóc w opracowaniu optymalnego rabatu do zastosowania bezpośrednio z poziomu ekranu analizy.

Zaobserwowano trendy dotyczące danych dzierżawy z Wingtip biletów SaaS wielodostępnej aplikacji bazy danych. Można z nich naSaaSać inne sposoby, aby aplikacja mogła informować decyzje biznesowe dotyczące dostawców aplikacji. Dostawcy mogą lepiej zaspokojyć potrzeby swoich dzierżawców. Miejmy nadzieję ten samouczek zawiera narzędzia niezbędne do przeprowadzenia analizy danych dzierżawy, aby umożliwić firmom podejmowanie decyzji opartych na danych.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Wdrożono bazę danych analizy dzierżawy ze wstępnie zdefiniowanymi tabelami schematów gwiazdy
> - Wykorzystano zadania elastyczne do wyodrębnienia danych ze wszystkich dzierżawców
> - Scalanie wyodrębnionych danych do tabel w schemacie gwiazdy zaprojektowanym do analizy
> - Tworzenie zapytań względem bazy danych analizy 
> - Używanie Power BI do wizualizacji danych w celu obserwowania trendów danych dzierżawy 

Gratulacje!

## <a name="additional-resources"></a>Dodatkowe zasoby

Dodatkowe [samouczki, które kompilują się po aplikacji Wingtip SaaS](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Zadania elastyczne](./elastic-jobs-overview.md).
- [Analiza wielu dzierżawców przy użyciu wyodrębnionych aplikacji z jedną dzierżawą](saas-tenancy-tenant-analytics.md)