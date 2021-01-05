---
title: Przewodnik dotyczący konserwacji predykcyjnej dla procesu nauki o danych zespołowych
description: Przewodnik techniczny dotyczący szablonu rozwiązania do konserwacji predykcyjnej w programie Aerospace, narzędziach i transportach.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3edeee8f41c806c90f32208c0c4f174c76ba38d0
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "93321992"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Przewodnik techniczny dotyczący szablonu rozwiązania do konserwacji predykcyjnej w programie Aerospace

> [!Important]
> Ten artykuł jest przestarzały. Dyskusja dotycząca konserwacji predykcyjnej w programie Aerospace nadal jest istotna, ale w przypadku bieżących informacji zapoznaj się z tematem [Omówienie rozwiązania dla odbiorców w biznesie](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Szablony rozwiązań zostały zaprojektowane w celu przyspieszenia procesu tworzenia demonstracji E2E. Wdrożony szablon inicjuje subskrypcję z niezbędnymi składnikami, a następnie kompiluje relacje między nimi. Obejmuje on również potoku danych z użyciem przykładowych danych z aplikacji generatora danych, którą można pobrać i zainstalować na komputerze lokalnym po wdrożeniu szablonu rozwiązania. Dane z generatora są odwodnione potoku danych i zaczynają generować przewidywania uczenia maszynowego, które można następnie wizualizować na pulpicie nawigacyjnym Power BI.

Proces wdrażania przeprowadzi Cię przez kilka kroków w celu skonfigurowania poświadczeń rozwiązania. Upewnij się, że zarejestrowano poświadczenia, takie jak nazwa rozwiązania, nazwa użytkownika i hasło, które podano podczas wdrażania. 


Celem tego artykułu jest:
- Opisz architekturę referencyjną i składniki, których obsługa została zainicjowana w ramach subskrypcji.
- Pokazuje, jak zastąpić przykładowe dane własnymi danymi. 
- Pokazuje, jak zmodyfikować szablon rozwiązania.  

## <a name="overview"></a>Omówienie
![Architektura konserwacji predykcyjnej](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Wdrożenie rozwiązania aktywuje usługi platformy Azure, w tym centrum zdarzeń, Stream Analytics, HDInsight, Data Factory i Machine Learning. Diagram architektury pokazuje, w jaki sposób jest konstruowany szablon rozwiązania do konserwacji predykcyjnej dla programu Aerospace. Możesz zbadać te usługi w Azure Portal, klikając je na diagramie szablonu rozwiązania utworzonym przy użyciu wdrożenia rozwiązania (z wyjątkiem usługi HDInsight, która jest obsługiwana na żądanie, gdy powiązane działania potoku są wymagane do uruchomienia i zostaną usunięte później).
Pobierz [pełną wersję diagramu](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

W poniższych sekcjach opisano części rozwiązania.

## <a name="data-source-and-ingestion"></a>Źródło i pozyskiwanie danych
### <a name="synthetic-data-source"></a>Syntetyczne źródło danych
W przypadku tego szablonu używane źródło danych jest generowane na podstawie pobranej aplikacji klasycznej, która jest uruchamiana lokalnie po pomyślnym wdrożeniu.

Aby znaleźć instrukcje do pobrania i zainstalowania tej aplikacji, wybierz pierwszy węzeł, generator danych konserwacji predykcyjnej na diagramie szablonu rozwiązania. Instrukcje znajdują się na pasku właściwości. Ta aplikacja przesyła strumieniowo usługę [Azure Event Hub](#azure-event-hub) do punktów danych lub zdarzeń używanych w pozostałej części przepływu rozwiązania. To źródło danych pochodzi z publicznie dostępnych danych z [repozytorium danych NASA](https://c3.nasa.gov/dashlink/resources/139/) przy użyciu [zestawu danych symulacji degradacji aparatu TurboFan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Aplikacja do generowania zdarzeń wypełnia centrum zdarzeń platformy Azure tylko wtedy, gdy jest wykonywane na komputerze.  

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure  
Usługa [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) to odbiorca danych wejściowych dostarczonych przez syntetyczne źródło danych.

## <a name="data-preparation-and-analysis"></a>Przygotowywanie i analiza danych  
### <a name="azure-stream-analytics"></a>Usługa Azure Stream Analytics
Użyj [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) , aby zapewnić analizę w czasie rzeczywistym w strumieniu wejściowym z usługi [Azure Event Hub](#azure-event-hub) . Następnie można opublikować wyniki na pulpicie nawigacyjnym [Power BI](https://powerbi.microsoft.com) , a także zarchiwizować wszystkie nieprzetworzone zdarzenia przychodzące do usługi [Azure Storage](https://azure.microsoft.com/services/storage/) w celu późniejszego przetworzenia przez usługę [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) .

### <a name="hdinsight-custom-aggregation"></a>Agregacja niestandardowa HDInsight
Uruchamianie skryptów [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) (zorganizowanych przez Azure Data Factory) za pomocą usługi HDInsight w celu zapewnienia agregacji dla nieprzetworzonych zdarzeń archiwizowanych przy użyciu zasobu Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Wykonaj przewidywania dotyczące pozostałego okresu eksploatacji (pozostałego czasu eksploatacji) dla określonego silnika samolotu przy użyciu danych wejściowych otrzymanych z [usługą Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (organizacja Azure Data Factory). 

## <a name="data-publishing"></a>Publikowanie danych
### <a name="azure-sql-database"></a>Usługa Azure SQL Database
Użyj [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) do przechowywania prognoz odebranych przez Azure Machine Learning, które są następnie używane na pulpicie nawigacyjnym [Power BI](https://powerbi.microsoft.com) .

## <a name="data-consumption"></a>Użycie danych
### <a name="power-bi"></a>Power BI
Użyj [Power BI](https://powerbi.microsoft.com) , aby wyświetlić pulpit nawigacyjny zawierający agregacje i alerty udostępniane przez [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), a także przewidywania pozostałego czasu eksploatacji przechowywane w [Azure SQL Databaseach](https://azure.microsoft.com/services/sql-database/) , które zostały utworzone przy użyciu [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Jak wprowadzić własne dane
W tej sekcji opisano sposób przenoszenia własnych danych na platformę Azure oraz tego, jakie obszary wymagają zmian w przypadku danych, które można umieścić w tej architekturze.

Jest mało prawdopodobne, że zestaw danych jest zgodny z zestawem wyników używanym przez [zestawienie symulacji degradacji aparatu TurboFan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) używany dla tego szablonu rozwiązania. Zrozumienie danych i wymagania są kluczowe w zakresie sposobu modyfikacji tego szablonu do pracy z własnymi danymi. 

W poniższych sekcjach omówiono części szablonu, które wymagają modyfikacji po wprowadzeniu nowego zestawu danych.

### <a name="azure-event-hub"></a>Centrum zdarzeń Azure
Centrum zdarzeń platformy Azure jest ogólne; dane można opublikować w centrum w formacie CSV lub JSON. Żadne specjalne przetwarzanie nie odbywa się w centrum zdarzeń platformy Azure, ale ważne jest, aby zrozumieć dane, które są do niego przekazywane.

Ten dokument nie zawiera opisu sposobu pozyskiwania danych, ale można łatwo wysyłać zdarzenia lub dane do centrum zdarzeń platformy Azure przy użyciu interfejsów API centrum zdarzeń.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Usługa Azure Stream Analytics
Użyj zasobu Azure Stream Analytics, aby zapewnić analizę niemal w czasie rzeczywistym przez odczyt z strumieni danych i wyprowadzanie danych do dowolnej liczby źródeł.

W przypadku szablonu rozwiązania do konserwacji predykcyjnej dla programu Aerospace Azure Stream Analytics zapytanie składa się z czterech podzapytań, które zużywają zdarzenia z usługi centrum zdarzeń platformy Azure, z wynikami do czterech różnych lokalizacji. Te dane wyjściowe składają się z trzech Power BI zestawów danych i jednej lokalizacji magazynu platformy Azure.

Zapytanie Azure Stream Analytics można znaleźć w:

* Połącz z Azure Portal
* Lokalizowanie ![ Stream Analytics ikony Stream Analytics zadania ](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) , które zostały wygenerowane podczas wdrażania rozwiązania (*na przykład* **maintenancesa02asapbi** i **maintenancesa02asablob** dla rozwiązania do konserwacji predykcyjnej)
* Opcji
  
  * ***Dane** wejściowe _, aby wyświetlić **wyniki** zapytania _ ***Query** _ w celu wyświetlenia samego zapytania _ * Output _, aby wyświetlić różne dane wyjściowe

Informacje na temat konstrukcji zapytania Azure Stream Analytics można znaleźć w dokumentacji dotyczącej [zapytań Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference) w witrynie MSDN.

W tym rozwiązaniu zapytania wysyłają trzy zestawy danych z informacjami o analizie w czasie rzeczywistym dotyczącymi przychodzącego strumienia danych do pulpitu nawigacyjnego Power BI dostarczonego w ramach tego szablonu rozwiązania. Ponieważ niejawna wiedza o formacie danych przychodzących, te zapytania muszą zostać zmienione na podstawie formatu danych.

Zapytanie w drugim Stream Analytics zadanie _ *maintenancesa02asablob** po prostu wyprowadza wszystkie zdarzenia [centrum zdarzeń](https://azure.microsoft.com/services/event-hubs/) do [usługi Azure Storage](https://azure.microsoft.com/services/storage/) , dlatego nie wymaga żadnych zmian niezależnie od formatu danych, ponieważ pełne informacje o zdarzeniu są przesyłane strumieniowo do magazynu.

### <a name="azure-data-factory"></a>Azure Data Factory
Usługa [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) organizuje przenoszenie i przetwarzanie danych. W szablonie rozwiązania do konserwacji predykcyjnej dla programu Aerospace Fabryka danych składa się z trzech [potoków](../../data-factory/concepts-pipelines-activities.md) , które przechodzą i przetwarzają dane przy użyciu różnych technologii.  Uzyskaj dostęp do fabryki danych, otwierając węzeł Data Factory w dolnej części diagramu szablonu rozwiązania utworzonego wraz z wdrożeniem rozwiązania. Błędy w zestawach danych są spowodowane wdrożeniem fabryki Data Factory przed uruchomieniem generatora danych. Te błędy można zignorować i uniemożliwić działanie fabryki danych.

![Błędy zestawu danych Data Factory](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

W tej sekcji omówiono wymagane [potoki i działania](../../data-factory/concepts-pipelines-activities.md) zawarte w [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Oto widok diagramu rozwiązania.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Dwa potoki tej fabryki zawierają skrypty [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) używane do partycjonowania i agregowania danych. Po zanotowaniu te skrypty znajdują się na koncie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) utworzonym podczas instalacji. Lokalizacja to: maintenancesascript \\ \\ skrypt \\ \\ Hive \\ \\ (lub https://[Nazwa rozwiązania]. blob. Core. Windows. NET/maintenancesascript).

Podobnie jak w przypadku zapytań [Azure Stream Analytics](#azure-stream-analytics-1) , skrypty programu [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) mają niejawną wiedzę na temat przychodzącego formatu danych i należy je zmienić w oparciu o format danych.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Ten [potok](../../data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — działanie [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) za pomocą [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) , które uruchamia skrypt programu [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) w celu partycjonowania danych umieszczonych w [usłudze Azure Storage](https://azure.microsoft.com/services/storage/) w ramach zadania [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) .

Skrypt [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) dla tego zadania partycjonowania to ***AggregateFlightInfo. HQL** _

#### <a name="_mlscoringpipeline"></a>_MLScoringPipeline *
Ten [potok](../../data-factory/concepts-pipelines-activities.md) zawiera kilka działań, których wynikiem jest wynikowe przewidywania z [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu skojarzonym z tym szablonem rozwiązania.

Uwzględnione działania to:

* Działanie [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) przy użyciu [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) , który uruchamia skrypt [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) w celu wykonywania agregacji i inżynierów funkcji niezbędnych do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperymentu.
  Skrypt [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) dla tego zadania partycjonowania to ***PrepareMLInput. HQL** _.
  _ Działanie [kopiowania](/previous-versions/azure/dn835035(v=azure.100)) , które przenosi wyniki z działania [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) do pojedynczego obiektu BLOB [usługi Azure Storage](https://azure.microsoft.com/services/storage/) , do którego uzyskuje dostęp działanie [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) .
* Działanie [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) wywołuje eksperyment [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) z wynikami umieszczanymi w pojedynczym obiekcie blob [usługi Azure Storage](https://azure.microsoft.com/services/storage/) .

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Ten [potok](../../data-factory/concepts-pipelines-activities.md) zawiera jedno działanie — działanie [kopiowania](/previous-versions/azure/dn835035(v=azure.100)) , które przenosi wyniki eksperymentu [Azure Machine Learning](#azure-machine-learning) z ***MLScoringPipeline** _ do [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) obsługiwane w ramach instalacji szablonu rozwiązania.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperyment używany dla tego szablonu rozwiązania zapewnia pozostały okres eksploatacji (pozostałego czasu eksploatacji) silnika samolotu. Eksperyment jest specyficzny dla używanego zestawu danych i wymaga modyfikacji lub zamiany specyficznej dla danych, które zostały wprowadzone.

## <a name="monitor-progress"></a>Monitorowanie postępu
Po uruchomieniu generatora danych potok rozpocznie się dehydratacji i różne składniki rozwiązania rozpoczynają działanie po poleceniach wydanych przez fabrykę danych. Istnieją dwa sposoby monitorowania potoku.

Jedno z Stream Analyticsych zadań zapisuje nieprzetworzone dane przychodzące do magazynu obiektów BLOB. Jeśli klikniesz Blob Storage składnik rozwiązania z ekranu, który pomyślnie wdrożono rozwiązanie, a następnie kliknij przycisk Otwórz w prawym panelu, przeprowadzisz Cię do [Azure Portal](https://portal.azure.com/). Po zakończeniu kliknij pozycję obiekty blob. W następnym panelu zostanie wyświetlona lista kontenerów. Kliknij pozycję **maintenancesadata**. W następnym panelu znajduje się folder **rawData** . W folderze rawData są foldery o nazwach takich jak Hour = 17 i Hour = 18. Obecność tych folderów wskazuje, że dane pierwotne są generowane na komputerze i przechowywane w usłudze BLOB Storage. W tych folderach powinny być widoczne pliki CSV mające skończone rozmiary.
* Ostatnim krokiem potoku jest zapisanie danych (na przykład prognoz z uczenia maszynowego) do SQL Database. Aby dane były wyświetlane w SQL Database, może być konieczne odczekanie maksymalnie trzech godzin. Jednym ze sposobów monitorowania ilości danych dostępnych w SQL Database jest przechodzenie przez [Azure Portal](https://portal.azure.com/). Na panelu po lewej stronie Znajdź bazy danych SQL :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-SQL-databases.png" border="false"::: i kliknij je. Następnie Znajdź **pmaintenancedb** bazy danych i kliknij ją. Na następnej stronie u dołu kliknij pozycję Zarządzaj.
   
    ![Ikona zarządzania](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    W tym miejscu można kliknąć pozycję nowe zapytanie i zapytanie dotyczące liczby wierszy (na przykład wybierz liczbę (*) z PMResult). W miarę zwiększania bazy danych, liczba wierszy w tabeli powinna się zwiększać.

## <a name="power-bi-dashboard"></a>Pulpit nawigacyjny usługi Power BI

Skonfiguruj pulpit nawigacyjny Power BI, aby wizualizować dane Azure Stream Analytics (ścieżką gorącą) i wyniki prognozowania partii z usługi Azure Machine Learning (ścieżka zimna).

### <a name="set-up-the-cold-path-dashboard"></a>Konfigurowanie pulpitu nawigacyjnego zimnej ścieżki
W potoku danych o zimnej ścieżce celem jest uzyskanie pozostałego czasu eksploatacji predykcyjnych (pozostały okres eksploatacji) każdego silnika samolotu po zakończeniu lotu (cykl). Wynik przewidywania jest aktualizowany co 3 godziny w celu przewidywania silników samolotów, które zakończyły lot w ciągu ostatnich 3 godzin.

Power BI nawiązuje połączenie z Azure SQL Database jako źródła danych, w którym są przechowywane wyniki przewidywania. 

Uwaga: 
1.    W przypadku wdrażania rozwiązania prognozowanie będzie widoczne w bazie danych w ciągu 3 godzin. Plik pbix, który został dostarczony z pobieranym generatorem, zawiera pewne dane dotyczące inicjatora, dzięki czemu można od razu utworzyć pulpit nawigacyjny Power BI. 
2.    W tym kroku wymagane jest pobranie i zainstalowanie bezpłatnego oprogramowania [Power BI Desktop](/power-bi/fundamentals/desktop-get-the-desktop).

Poniższe kroki przeprowadzą Cię przez proces łączenia pliku pbix z SQL Database, który był w trakcie wdrażania rozwiązania zawierającego dane (na przykład wyniki przewidywania) dla wizualizacji.

1. Pobierz poświadczenia bazy danych.
   
   Przed przejściem do następnych kroków będziesz potrzebować **nazwy serwera bazy danych, nazwy bazy danych, nazwy użytkownika i hasła** . Poniżej przedstawiono kroki, które należy wykonać, aby dowiedzieć się, jak je znaleźć.
   
   * Gdy **element "Azure SQL Database"** na diagramie szablonu rozwiązania zmieni kolor na zielony, kliknij go, a następnie kliknij przycisk **"Otwórz"**.
   * Zostanie wyświetlona nowa karta/okno przeglądarki, w której zostanie wyświetlona strona Azure Portal. Na panelu po lewej stronie kliknij pozycję **"grupy zasobów"** .
   * Wybierz subskrypcję używaną podczas wdrażania rozwiązania, a następnie wybierz pozycję **"YourSolutionName \_ resourceName"**.
   * W panelu nowy wyskakujący kliknij ikonę,  :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-sql.png" border="false"::: Aby uzyskać dostęp do bazy danych. Nazwa bazy danych jest obok tej ikony (na przykład **"pmaintenancedb"**), a **Nazwa serwera bazy danych** jest wyświetlana w obszarze właściwości Nazwa serwera i powinna wyglądać podobnie do **YourSolutionName.Database.Windows.NET**.
   * **Nazwa użytkownika** i **hasło** bazy danych są takie same jak nazwa użytkownika i hasło, które zostały wcześniej zarejestrowane podczas wdrażania rozwiązania.
2. Zaktualizuj źródło danych pliku raportu zimnej ścieżki przy użyciu Power BI Desktop.
   
   * W folderze, do którego został pobrany i rozpakowany plik generatora, kliknij dwukrotnie plik **pbix usługi Power \\ PredictiveMaintenanceAerospace.** Jeśli po otwarciu pliku zobaczysz ostrzeżenia, zignoruj je. W górnej części pliku kliknij pozycję **"Edytuj zapytania"**.
     
     ![Edytuj zapytania](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Zobaczysz dwie tabele, **RemainingUsefulLife** i **PMResult**. Wybierz pierwszą tabelę, a następnie kliknij ![ ikonę Ustawienia zapytania ](./media/predictive-maintenance-technical-guide/icon-query-settings.png) obok pozycji " **Źródło"** w obszarze **"zastosowane kroki"** po prawej stronie panelu **Ustawienia zapytania** . Zignoruj wszystkie wyświetlane komunikaty ostrzegawcze.
   * W oknie podręcznym Zastąp **wartość "serwer"** i **"baza danych"** własnymi nazwami serwera i bazy danych, a następnie kliknij przycisk **"OK"**. W polu Nazwa serwera upewnij się, że określono port 1433 (**YourSolutionName.Database.Windows.NET, 1433**). Pozostaw pole bazy danych jako **pmaintenancedb**. Ignorowanie komunikatów ostrzegawczych wyświetlanych na ekranie.
   * W następnym oknie podręcznym zobaczysz dwie opcje w okienku po lewej stronie (**system Windows** i **baza danych**). Kliknij pozycję **"baza danych"**, wprowadź nazwę **"username"** i **"Password** " (nazwę użytkownika i hasło wprowadzone podczas pierwszego wdrożenia rozwiązania i utworzyć Azure SQL Database). W obszarze **_Wybierz poziom, do którego mają zostać zastosowane te ustawienia_*, zaznacz opcję poziom bazy danych. Następnie kliknij przycisk _*"Połącz"**.
   * Kliknij drugą tabelę **PMResult** a następnie kliknij ![ ikonę nawigacji ](./media/predictive-maintenance-technical-guide/icon-navigation.png) obok pozycji **"Źródło"** w obszarze **"zastosowane kroki"** w prawym okienku **"Ustawienia zapytania"** i zaktualizuj nazwy serwera i bazy danych tak jak w powyższych krokach, a następnie kliknij przycisk OK.
   * Po powrocie do poprzedniej strony Zamknij okno. Zostanie wyświetlony komunikat — kliknij przycisk **Zastosuj**. Na koniec kliknij przycisk **Zapisz** , aby zapisać zmiany. Plik Power BI ma teraz połączenie z serwerem. Jeśli wizualizacje są puste, pamiętaj o zaznaczeniu opcji wizualizacji, aby wizualizować wszystkie dane, klikając ikonę gumki w prawym górnym rogu legend. Użyj przycisku Odśwież, aby odzwierciedlić nowe dane w wizualizacjach. Początkowo dane inicjatora są widoczne tylko dla wizualizacji, ponieważ Fabryka danych jest zaplanowana do odświeżenia co 3 godziny. Po 3 godzinach zobaczysz nowe przewidywania odzwierciedlone w wizualizacjach podczas odświeżania danych.
3. Obowiązkowe Opublikuj pulpit nawigacyjny zimnej ścieżki, aby [Power BI online](https://www.powerbi.com/). Ten krok wymaga konta Power BI (lub konta służbowego).
   
   * Kliknięcie przycisku **"Publikuj"** i kilku sekund spowoduje wyświetlenie okna zawierającego komunikat "publikowanie w Power BI powodzenie!" z zielonym znacznikiem wyboru. Kliknij link poniżej "Otwórz PredictiveMaintenanceAerospace. pbix w Power BI". Aby uzyskać szczegółowe instrukcje, zobacz temat [Publikowanie z Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Aby utworzyć nowy pulpit nawigacyjny: kliknij **+** znak obok sekcji **pulpity nawigacyjne** w okienku po lewej stronie. Wprowadź nazwę "Demonstracja konserwacji predykcyjnej" dla nowego pulpitu nawigacyjnego.
   * Po otwarciu raportu kliknij przycisk ![ Przypnij ikonę, ](./media/predictive-maintenance-technical-guide/icon-pin.png) Aby przypiąć wszystkie wizualizacje do pulpitu nawigacyjnego. Aby uzyskać szczegółowe instrukcje, zobacz [Przypinanie kafelka do pulpitu nawigacyjnego Power BI z raportu](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Przejdź do strony pulpit nawigacyjny i Dostosuj rozmiar i lokalizację wizualizacji i edytuj ich tytuły. Aby uzyskać szczegółowe instrukcje dotyczące edytowania kafelków, zobacz [Edytowanie kafelka — Zmienianie rozmiaru, przenoszenie, zmienianie nazwy, przypinanie, usuwanie, Dodawanie hiperlinku](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Poniżej znajduje się przykładowy pulpit nawigacyjny z niektórymi wizualizacjami zimnej ścieżki.  W zależności od tego, jak długo uruchamiasz Generator danych, liczby w wizualizacjach mogą być różne.
     <br/>
     ![Widok końcowy](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Aby zaplanować odświeżanie danych, umieść kursor myszy nad **PredictiveMaintenanceAerospace** zestawu danych, kliknij ![ ikonę wielokropka, ](./media/predictive-maintenance-technical-guide/icon-elipsis.png) a następnie wybierz pozycję **Zaplanuj odświeżanie**.
     <br/>
     > [!NOTE]
     > Jeśli zostanie wyświetlony komunikat ostrzegawczy, kliknij pozycję **Edytuj poświadczenia** i upewnij się, że poświadczenia bazy danych są takie same jak te opisane w kroku 1.
     <br/>
     ![Zaplanuj odświeżanie](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Rozwiń sekcję **Zaplanuj odświeżanie** . Włącz opcję "Zachowaj aktualność danych".
     <br/>
   * Zaplanuj odświeżanie na podstawie Twoich potrzeb. Aby uzyskać więcej informacji, zobacz [odświeżanie danych w Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Skonfiguruj pulpit nawigacyjny ścieżki aktywnej
Poniższe kroki przedstawiają sposób wizualizacji danych wyjściowych z Stream Analytics zadań, które zostały wygenerowane w czasie wdrażania rozwiązania. Do wykonania poniższych kroków jest wymagane konto [Power BI online](https://www.powerbi.com/) . Jeśli nie masz konta, możesz go [utworzyć](https://powerbi.microsoft.com/pricing).

1. Dodaj Power BI dane wyjściowe w Azure Stream Analytics (ASA).
   
   * Musisz postępować zgodnie z instrukcjami w [Azure Stream Analytics & Power BI: pulpit nawigacyjny analizy do wglądu w dane przesyłane strumieniowo](../../stream-analytics/stream-analytics-power-bi-dashboard.md) w czasie rzeczywistym w celu skonfigurowania danych wyjściowych zadania Azure Stream Analytics jako pulpitu nawigacyjnego Power BI.
   * Zapytanie ASA ma trzy dane wyjściowe, które są **aircraftmonitor**, **aircraftalert** i **flightsbyhour**. Możesz wyświetlić zapytanie, klikając kartę zapytanie. Odpowiadające każdej z tych tabel, należy dodać dane wyjściowe do ASA. Po dodaniu pierwszego danych wyjściowych (**aircraftmonitor**) Upewnij się, że **alias danych wyjściowych**, **Nazwa zestawu danych** i **Nazwa tabeli** są takie same (**aircraftmonitor**). Powtórz kroki, aby dodać wyjściowe dla **aircraftalert** i **flightsbyhour**. Po dodaniu wszystkich trzech tabel wyjściowych i uruchomieniu zadania ASA powinien zostać wyświetlony komunikat z potwierdzeniem ("Uruchamianie Stream Analytics zadania maintenancesa02asapbi powiodło się").
2. Logowanie do usługi [Power BI online](https://www.powerbi.com)
   
   * W sekcji zestawy **danych** w panelu po lewej stronie w obszarze mój obszar roboczy, powinny być wyświetlane * aircraftmonitor * *, **aircraftalert** i **flightsbyhour** . Jest to dane przesyłane strumieniowo z Azure Stream Analytics w poprzednim kroku. Zestaw danych **flightsbyhour** może nie być wyświetlany w tym samym czasie co pozostałe dwa zbiory, ze względu na charakter zapytania SQL. Jednak powinien on pojawić się po godzinie.
   * Upewnij się, że okienko ***wizualizacje** _ jest otwarte i jest wyświetlane po prawej stronie ekranu.
3. Gdy dane są przepływające do Power BI, można rozpocząć wizualizowanie danych przesyłanych strumieniowo. Poniżej znajduje się przykładowy pulpit nawigacyjny z niektórymi wizualizacjami ścieżek gorąca. Można tworzyć inne kafelki pulpitu nawigacyjnego na podstawie odpowiednich zestawów danych. W zależności od tego, jak długo uruchamiasz Generator danych, liczby w wizualizacjach mogą być różne.

    ![Widok pulpitu nawigacyjnego](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Poniżej przedstawiono kilka kroków, które należy wykonać, aby utworzyć jeden z powyższych kafelków — "widok floty czujnika 11 a próg 48,26":
   
   _ Kliknij pozycję DataSet **aircraftmonitor** w sekcji zestawy danych w panelu po lewej stronie.
   * Kliknij ikonę **wykresu liniowego** .
   * Kliknij pozycję **przetworzone** w okienku **pola** , aby było wyświetlane w obszarze "oś" w okienku **wizualizacje** .
   * Kliknij pozycję "S11" i " \_ alert S11", aby były one wyświetlane w obszarze "wartości". Kliknij małą strzałkę obok **\_ alertu** **S11** i S11, a następnie zmień wartość "Sum" na wartość "Średnia".
   * Kliknij przycisk **Zapisz** u góry i nadaj raportowi nazwę "aircraftmonitor". Raport o nazwie "aircraftmonitor" jest wyświetlany w sekcji **raporty** w okienku **Nawigator** po lewej stronie.
   * Kliknij ikonę **pinezki** w prawym górnym rogu tego wykresu liniowego. Okno "Przypnij do pulpitu nawigacyjnego" może być wyświetlane w celu wybrania pulpitu nawigacyjnego. Wybierz pozycję "Demonstracja konserwacji predykcyjnej", a następnie kliknij pozycję "Przypnij".
   * Umieść wskaźnik myszy nad tym kafelkiem na pulpicie nawigacyjnym, a następnie kliknij ikonę "Edytuj" w prawym górnym rogu, aby zmienić jej tytuł na "widok floty z czujnika 11 vs. próg 48,26" i podtytuł na "Średnia między flotą w czasie".

## <a name="delete-your-solution"></a>Usuwanie rozwiązania
Upewnij się, że zatrzymasz Generator danych, gdy nie korzystasz aktywnie z rozwiązania jako uruchomionego generatora danych spowoduje naliczenie wyższych kosztów. Usuń rozwiązanie, jeśli nie jest używane. Usunięcie rozwiązania spowoduje usunięcie wszystkich składników zainicjowanych w ramach subskrypcji podczas wdrażania rozwiązania. Aby usunąć rozwiązanie, kliknij nazwę rozwiązania w lewym panelu szablonu rozwiązania, a następnie kliknij przycisk **Usuń**.

## <a name="cost-estimation-tools"></a>Narzędzia do szacowania kosztów
Dostępne są następujące dwa narzędzia ułatwiające lepsze zrozumienie łącznego kosztu związanego z uruchamianiem szablonu rozwiązania do konserwacji predykcyjnej dla programu Aerospace w ramach subskrypcji:

* [Narzędzie Microsoft Azure Cost szacowania (online)](https://azure.microsoft.com/pricing/calculator/)
* [Narzędzie Microsoft Azure Cost szacowania (Desktop)](https://www.microsoft.com/download/details.aspx?id=43376)