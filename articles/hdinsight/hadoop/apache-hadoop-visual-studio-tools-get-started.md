---
title: Apache Hadoop & narzędzia Data Lake programu Visual Studio — usługa Azure HDInsight
description: Dowiedz się, jak zainstalować narzędzia Data Lake Tools for Visual Studio i korzystać z nich. Użyj narzędzia, aby nawiązać połączenie z klastrami Apache Hadoop w usłudze Azure HDInsight, a następnie uruchom zapytania programu Hive.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: 8d8e9784ea21bf5f2b6902e3d93c5c09c1ec5670
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944563"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Używanie Data Lake narzędzi dla programu Visual Studio do nawiązywania połączenia z usługą Azure HDInsight i uruchamiania zapytań Apache Hive

Dowiedz się, jak używać narzędzi Microsoft Azure Data Lake i Stream Analytics Tools for Visual Studio (Data Lake Tools). Użyj narzędzia, aby nawiązać połączenie z [klastrami Apache Hadoop w usłudze Azure HDInsight](apache-hadoop-introduction.md) i przesłać zapytania programu Hive.  

Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight, zobacz [Rozpoczynanie pracy z usługą HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Aby uzyskać więcej informacji na temat łączenia się z Apache Storm, zobacz [Tworzenie topologii C# dla Apache Storm przy użyciu narzędzi Data Lake](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Możesz użyć narzędzia Data Lake Tools for Visual Studio do uzyskiwania dostępu do usług Azure Data Lake Analytics i HDInsight. Aby uzyskać informacje na temat narzędzi Data Lake Tools, zobacz [Tworzenie skryptów U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł i korzystać z narzędzi Data Lake Tools for Visual Studio, potrzebne są następujące elementy:

* Klaster usługi Azure HDInsight. Aby utworzyć klaster usługi HDInsight, zobacz [Rozpoczynanie pracy przy użyciu Apache Hadoop w usłudze Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Aby uruchamiać interakcyjne zapytania Apache Hive, wymagany jest klaster [interakcyjnych zapytań usługi HDInsight](../interactive-query/apache-interactive-query-get-started.md) .  

* [Program Visual Studio](https://visualstudio.microsoft.com/downloads/). [Program Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) jest bezpłatny. Poniższe instrukcje dotyczą [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalacja narzędzi Data Lake Tools for Visual Studio  

Postępuj zgodnie z odpowiednimi instrukcjami, aby zainstalować narzędzia Data Lake dla używanej wersji programu Visual Studio:

* Dla programu Visual Studio 2017 lub Visual Studio 2019:

    Podczas instalacji programu Visual Studio upewnij się, że dołączysz obciążenie **Programowanie na platformie Azure** lub **Magazyn danych i obciążenie przetwarzaniem** .  

    W przypadku istniejących instalacji programu Visual Studio przejdź do paska menu IDE, a następnie wybierz pozycję **Narzędzia**  >  **Pobierz narzędzia i funkcje** , aby otworzyć Instalator programu Visual Studio. Na karcie **obciążenia** wybierz co najmniej obciążenie **Programowanie na platformie Azure** (w obszarze **chmura & sieci Web**). Lub wybierz obciążenie **magazynu i przetwarzania danych** (w obszarze **inne zestawy narzędzi**).

  ![Wybór obciążenia, Instalator programu Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* Dla programu Visual Studio 2015:

    [Pobierz narzędzia Data Lake](https://www.microsoft.com/download/details.aspx?id=49504). Należy wybrać wersję narzędzi Data Lake Tools zgodną z używaną wersją programu Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Narzędzia aktualizacji Data Lake Tools for Visual Studio  

Następnie upewnij się, że Data Lake narzędzia zostały zaktualizowane do najnowszej wersji.

1. Otwórz program Visual Studio.

2. W oknie **uruchamiania** wybierz pozycję **Kontynuuj bez kodu**.

3. Na pasku menu środowiska IDE programu Visual Studio wybierz pozycję **rozszerzenia**  >  **Zarządzanie rozszerzeniami**.

4. W oknie dialogowym **Zarządzanie rozszerzeniami** rozwiń węzeł **aktualizacje** .

5. Jeśli lista dostępnych aktualizacji zawiera **narzędzia Azure Data Lake i Stream Analytics**, zaznacz je. Następnie wybierz przycisk **Aktualizuj** . Po wyświetleniu i usunięciu okna dialogowego **pobieranie i Instalowanie** programu Visual Studio dodaje do harmonogramu aktualizacji rozszerzenie " **Azure Data Lake i Stream Analytics Tools** ".

6. Zamknij wszystkie okna programu Visual Studio. Zostanie wyświetlone okno dialogowe **Instalator VSIX** .

7. Wybierz **licencję** , aby odczytać postanowienia licencyjne, a następnie wybierz pozycję **Zamknij** , aby powrócić do okna dialogowego **Instalator VSIX** .

8. Wybierz pozycję **Modyfikuj**. Rozpocznie się instalacja aktualizacji rozszerzenia. Po czasie okno dialogowe zmienia się, aby pokazać, że wykonuje modyfikacje. Wybierz pozycję **Zamknij**, a następnie uruchom ponownie program Visual Studio, aby zakończyć instalację.

> [!NOTE]  
> Możesz użyć tylko narzędzi Data Lake Tools w wersji 2.3.0.0 lub nowszej, aby nawiązać połączenie z klastrami zapytań interaktywnych oraz uruchamiać zapytania interaktywne usługi Hive.

## <a name="connect-to-azure-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami platformy Azure

Narzędzia Data Lake Tools for Visual Studio umożliwiają łączenie się z klastrami usługi HDInsight, wykonywanie podstawowych operacji zarządzania i uruchamianie zapytań programu Hive.

> [!NOTE]  
> Aby uzyskać informacje na temat nawiązywania połączenia z ogólnym klastrem Hadoop, zobacz [jak pisać i przesyłać zapytania programu Hive przy użyciu programu Visual Studio](/archive/blogs/xiaoyong/how-to-write-and-submit-hive-queries-using-visual-studio).

### <a name="connect-to-an-azure-subscription"></a>Łączenie się z subskrypcją platformy Azure

Aby nawiązać połączenie z subskrypcją platformy Azure:

1. Otwórz program Visual Studio.

2. W oknie **uruchamiania** wybierz pozycję **Kontynuuj bez kodu**.

3. Na pasku menu IDE wybierz polecenie **Wyświetl**  >  **Eksplorator serwera**.

4. W **Eksplorator serwera** kliknij prawym przyciskiem myszy pozycję **Azure**, wybierz pozycję **Połącz z subskrypcją Microsoft Azure** i Ukończ proces uwierzytelniania. W **Eksplorator serwera** rozwiń węzeł **Azure**  >  **HDInsight** , aby wyświetlić listę istniejących klastrów usługi HDInsight.

5. Jeśli nie masz żadnych klastrów, utwórz je za pomocą Azure Portal, Azure PowerShell lub zestawu SDK usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastrów w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Lista klastrów usługi HDInsight, Eksplorator serwera, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Rozwiń węzeł klastra usługi HDInsight. Klaster zawiera węzły dla **baz danych programu Hive**. Ponadto domyślne konto magazynu, wszystkie dodatkowe połączone konta magazynu i **Dziennik usługi Hadoop**. Jednostki można rozwinąć.

Po nawiązaniu połączenia z subskrypcją platformy Azure można wykonać następujące zadania.

### <a name="connect-to-azure-from-visual-studio"></a>Nawiązywanie połączenia z platformą Azure z poziomu programu Visual Studio

Nawiązywanie połączenia z witryną Azure Portal w programie Visual Studio:

1. W **Eksplorator serwera** rozwiń węzeł **Azure**  >  **HDInsight** i wybierz swój klaster.

2. Kliknij prawym przyciskiem myszy klaster usługi HDInsight, a następnie wybierz pozycję **Zarządzaj klastrem w Azure Portal**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Zaproponuj pytania i opinie z programu Visual Studio

Aby zadać pytania i lub przekazać Opinie z programu Visual Studio:

1. W obszarze Eksplorator serwera wybierz pozycję **Azure**  >  **HDInsight**.

2. Kliknij prawym przyciskiem myszy pozycję **HDInsight** i wybierz opcję **forum MSDN** , aby zadawać pytania, lub **Przekaż opinię** , aby przesłać opinię.

## <a name="link-to-or-edit-a-cluster"></a>Łączenie z klastrem lub edytowanie go

> [!NOTE]
> Obecnie jedynym typem klastra usługi HDInsight, z którym można utworzyć połączenie, jest typ Hive.

Aby połączyć klaster usługi HDInsight:

1. Kliknij prawym przyciskiem myszy pozycję **HDInsight**, a następnie wybierz pozycję **Połącz klaster usługi HDInsight** , aby wyświetlić okno dialogowe **łączenie klastra usługi HDInsight** .

2. Wprowadź **adres URL połączenia** w formularzu `https://CLUSTERNAME.azurehdinsight.net` . **Nazwa klastra** jest automatycznie wypełniana nazwą klastra w adresie URL po przejściu do innego pola. Następnie wprowadź **nazwę użytkownika** i **hasło**, a następnie wybierz pozycję **dalej**.

    ![Łączenie klastra, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Wybierz pozycję **Zakończ**. Jeśli powiązanie klastra zakończy się pomyślnie, klaster zostanie wyświetlony w węźle **HDInsight** .

Aby zaktualizować połączony klaster, kliknij prawym przyciskiem myszy klaster i wybierz polecenie **Edytuj**. Następnie można zaktualizować informacje o klastrze.

![Edytowanie połączonego klastra, usługi HDInsight i programu Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Eksplorowanie połączonych zasobów

Z poziomu Eksploratora serwera można zobaczyć domyślne konto magazynu i wszystkie połączone konta magazynu. Po rozwinięciu domyślnego konta magazynu można wyświetlić kontenery konta magazynu. Domyślne konto magazynu i domyślny kontener są oznaczone.

![Data Lake narzędzia dla połączonych zasobów programu Visual Studio w programie Eksplorator serwera](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Kliknij prawym przyciskiem myszy kontener i wybierz pozycję **Wyświetl kontener** , aby wyświetlić zawartość kontenera. Po otwarciu kontenera można użyć przycisków paska narzędzi, aby **odświeżyć** listę zawartości, **przekazać obiekt BLOB**, **usunąć wybrane obiekty blob**, **otworzyć obiekt BLOB** i pobrać (**Zapisz jako**) wybrane obiekty blob.

![Lista kontenerów i operacje obiektów blob, klaster usługi HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Uruchamianie interakcyjnych zapytań Apache Hive

[Apache Hive](https://hive.apache.org) to infrastruktura magazynu danych oparta na platformie Hadoop. Usługa Hive jest używana do wykonywania podsumowań danych, zapytań i analizy. Możesz użyć narzędzi Data Lake Tools for Visual Studio, aby uruchomić zapytania usługi Hive z poziomu programu Visual Studio. Aby uzyskać więcej informacji na temat programu Hive, zobacz [co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](hdinsight-use-hive.md).

[Zapytanie interaktywne w usłudze Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) używa programu [Hive w systemie LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) w Apache Hive 2,1. Zapytanie interaktywne umożliwia współdziałanie z złożonymi kwerendami w stylu magazynu danych w dużych, przechowywanych zestawach. Uruchomione zapytania Hive w zapytaniu interaktywnym są znacznie szybsze niż tradycyjne zadania wsadowe Hive. 

> [!NOTE]  
> Uruchamianie interakcyjnych zapytań Hive jest możliwe tylko po nawiązaniu połączenia z klastrem [interakcyjnych zapytań usługi HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Możesz również użyć narzędzi Data Lake Tools for Visual Studio, aby zobaczyć, co znajduje się w zadaniu Hive. Narzędzia Data Lake Tools for Visual Studio zbierają i ujawniają dzienniki Yarn dla wybranych zadań Hive.

W obszarze **Eksplorator serwera** wybierz pozycję **Azure**  >  **HDInsight** i wybierz swój klaster.  Ten węzeł jest punktem początkowym w **Eksplorator serwera** dla sekcji do wykonania.

### <a name="view-hivesampletable"></a>Zobacz tabelę hivesampletable

Wszystkie klastry usługi HDInsight mają domyślną przykładową tabelę programu Hive o nazwie `hivesampletable` .  

W klastrze wybierz opcję **bazy danych Hive**  >  **default**  >  **hivesampletable**.

* Aby wyświetlić `hivesampletable` schemat:

    Rozwiń węzeł **hivesampletable**. Są wyświetlane nazwy i typy danych `hivesampletable` kolumn.

* Aby wyświetlić `hivesampletable` dane:

    Kliknij prawym przyciskiem myszy pozycję **hivesampletable**, a następnie wybierz pozycję **Wyświetl pierwsze 100 wierszy**. Zostanie wyświetlona lista 100 wyników w **tabeli programu Hive: hivesampletable** okno. Ta akcja jest równoznaczna z uruchomieniem następującego zapytania programu Hive przy użyciu sterownika programu Hive ODBC:

    `SELECT * FROM hivesampletable LIMIT 100`

    Liczbę wierszy można dostosować, zmieniając **liczbę wierszy**. z listy rozwijanej można wybrać 50, 100, 200 lub 1000 wierszy.

### <a name="create-hive-tables"></a>Tworzenie tabel programu Hive

Do utworzenia tabeli programu Hive można użyć graficznego interfejsu użytkownika lub zapytań programu Hive. Aby uzyskać informacje o używaniu zapytań Hive, zobacz [Tworzenie i uruchamianie zapytań programu Hive](#create-and-run-hive-queries).

1. W klastrze wybierz opcję **bazy danych Hive**  >  **Domyślnie**.

2. Kliknij prawym przyciskiem myszy pozycję **domyślne**, a następnie wybierz pozycję **Utwórz tabelę**.

3. Skonfiguruj tabelę.

4. Wybierz przycisk **Utwórz tabelę** , aby przesłać zadanie, które tworzy nową tabelę programu Hive.

    ![Tworzenie okna tabeli, Hive, klaster usługi HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Tworzenie i uruchamianie zapytań programu Hive

Masz dwie opcje umożliwiające utworzenie i uruchomienie zapytań Hive:

* Tworzenie zapytań ad hoc
* Tworzenie aplikacji Hive

#### <a name="create-an-ad-hoc-query"></a>Tworzenie zapytania ad hoc

Aby utworzyć i uruchomić zapytanie ad hoc:

1. Kliknij prawym przyciskiem myszy klaster, w którym chcesz uruchomić zapytanie, a następnie wybierz polecenie **Napisz zapytanie programu Hive**.  

2. Wprowadź zapytanie programu Hive.

    Edytor Hive obsługuje funkcję IntelliSense. Narzędzia Data Lake Tools for Visual Studio obsługują ładowanie zdalnych metadanych podczas edycji skryptu Hive. Na przykład, jeśli wpiszesz `SELECT * FROM` , IntelliSense wyświetla wszystkie sugerowane nazwy tabel. Po określeniu nazwy tabeli funkcja IntelliSense wyświetla nazwy kolumn. Narzędzia obsługują większość instrukcji DML programu Hive, podzapytań i wbudowanych sterowników UDF.

    ![Przykład IntelliSense 1, zapytanie ad hoc Hive, klaster usługi HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![Przykład IntelliSense 2, zapytanie ad hoc Hive, klaster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > Funkcja IntelliSense zasugeruje tylko metadane klastra zaznaczonego na pasku narzędzi usługi HDInsight.

    Oto przykładowe zapytanie, którego można użyć:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Wybierz tryb wykonywania:

    * **Interaktywne**  

        Z pierwszej listy rozwijanej wybierz pozycję **interaktywny**, a następnie wybierz pozycję **Wykonaj**.

        ![Tryb interaktywny, kwerenda ad hoc Hive, klaster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Z pierwszej listy rozwijanej wybierz pozycję **Batch**, a następnie wybierz pozycję **Prześlij**. Lub wybierz ikonę listy rozwijanej obok pozycji **Prześlij** i wybierz pozycję **Zaawansowane**.

        ![Tryb wsadowy, kwerenda ad hoc Hive, klaster usługi HDInsight, program Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        W przypadku wybrania opcji przesyłania zaawansowane zostanie wyświetlone okno dialogowe **Prześlij skrypt** . Skonfiguruj **nazwę zadania**, **argumenty**, **dodatkowe konfiguracje** i **katalog stanu** dla skryptu.

        ![Okno dialogowe przesyłania skryptu, zapytanie ad hoc Hive, klaster usługi HDInsight, program Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Nie można przesyłać partii do interaktywnych klastrów zapytań.  Należy używać trybu interaktywnego.

#### <a name="create-a-hive-application"></a>Tworzenie aplikacji Hive

Tworzenie i uruchamianie rozwiązania Hive:

1. Na pasku menu wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

2. W oknie **Tworzenie nowego projektu** zaznacz pole wyszukiwania i wpisz **Hive**. Następnie wybierz pozycję **aplikacja Hive** i wybierz pozycję **dalej**.

3. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu**, wybierz lub Utwórz **lokalizację** projektu, a następnie wybierz pozycję **Utwórz**.

    ![Nowa aplikacja Hive, Konfigurowanie okna nowego projektu, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. W **Eksploratorze rozwiązań** kliknij dwukrotnie plik **Script.hql**, aby otworzyć skrypt.

### <a name="view-job-summary-and-output"></a>Wyświetlanie podsumowania i danych wyjściowych zadania

Podsumowanie zadania różni się **nieco w zależności od trybu** **wsadowego** .

![Podsumowanie zadań programu Hive Windows, Batch i tryb interaktywny, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Użyj ikony **Odśwież** , aby zaktualizować stan do momentu, gdy stan zadania zmieni się na **zakończone**.  

* W obszarze Szczegóły zadania w trybie **wsadowym** wybierz linki u dołu, aby wyświetlić **zapytanie dotyczące zadania**, **dane wyjściowe zadania** lub **Dziennik zadań** lub **wyświetlić dzienniki przędzy**.

* Aby uzyskać szczegółowe informacje o zadaniu w trybie **interaktywnym** , zobacz okienka danych **wyjściowych** i **serwera hiveserver2** .

    ![Interaktywne dane wyjściowe zadania Hive, klaster usługi HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Wyświetl wykres zadania

Obecnie wykresy zadań są wyświetlane tylko w przypadku zadań Hive, które używają tez jako aparatu wykonywania.  Aby uzyskać informacje o włączaniu tez, zobacz [co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](hdinsight-use-hive.md).  Zobacz również, [użyj Apache tez zamiast mapowania mapy](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Aby wyświetlić wszystkie operatory wewnątrz wierzchołka, kliknij dwukrotnie wierzchołki wykresu zadań. Możesz też wskazać konkretny operator, aby wyświetlić więcej jego szczegółów.

Nawet jeśli tez jest określony jako aparat wykonywania, wykres zadania może nie być wyświetlany, jeśli nie zostanie uruchomiona żadna aplikacja tez.  Taka sytuacja może wystąpić, ponieważ zadanie nie zawiera instrukcji DML. Lub, ponieważ Instrukcje DML mogą zwracać bez uruchamiania aplikacji tez. Na przykład `SELECT * FROM table1` nie zostanie uruchomiona aplikacja tez.

![Apache Hive Graf zadań, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Wyświetl szczegóły wykonania zadania

Na wykresie zadania można wybrać **szczegóły wykonania zadania** , aby uzyskać informacje o strukturze i wizualizacji zadań Hive. Możesz również uzyskać więcej informacji o zadaniach. Jeśli wystąpią problemy z wydajnością, możesz użyć widoku, aby uzyskać więcej szczegółów dotyczących problemu. Na przykład możesz pobrać informacje o tym, jak działa każde zadanie i szczegółowe informacje o każdym zadaniu (odczyt/zapis danych, czas trwania harmonogramu/rozpoczęcia/zakończenia itd.). Skorzystaj z informacji, aby dostosować konfiguracje zadań lub architekturę systemu w oparciu o wizualizowane informacje.

![Okno widoku wykonywania zadań, Data Lake Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Wyświetlanie zadań Hive

Istnieje możliwość wyświetlenia zapytań dotyczących zadań, danych wyjściowych zadań, dzienników zadań oraz dzienników Yarn dla zadań Hive.

W najnowszej wersji narzędzi można sprawdzić zawartość zadań Hive, zbierając i wyświetlając dzienniki przędzy. Dziennik Yarn może być pomocny w badaniu problemów z wydajnością. Aby uzyskać więcej informacji na temat sposobu zbierania dzienników przędzy przez usługę HDInsight, zobacz [dostęp do dzienników aplikacji przędzy Apache Hadoop](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Aby wyświetlić zadania Hive:

1. Kliknij prawym przyciskiem myszy klaster usługi HDInsight, a następnie wybierz pozycję **Wyświetl zadania**.

    ![Wyświetlanie zadań, Apache Hive, klastra usługi HDInsight i programu Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Zostanie wyświetlona lista zadań Hive uruchomionych w klastrze.  

2. Wybierz zadanie. W oknie **podsumowania zadania programu Hive** wybierz jedno z następujących linków:
    - **Zapytanie zadania**
    - **Dane wyjściowe zadania**
    - **Dziennik zadań**  
    - **Dziennik przędzy**

## <a name="run-apache-pig-scripts"></a>Uruchamianie skryptów Apache świni

1. Na pasku menu wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.

2. W oknie **uruchamiania** wybierz pole wyszukiwania i wprowadź **świnie**. Następnie wybierz pozycję **aplikacja świniowa** i wybierz pozycję **dalej**.

3. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu**, a następnie wybierz lub Utwórz **lokalizację** dla projektu. Następnie wybierz pozycję **Utwórz**.

4. W okienku **Eksplorator rozwiązań** IDE kliknij dwukrotnie **skrypt. świni** , aby otworzyć skrypt.

## <a name="feedback-and-known-issues"></a>Opinie i znane problemy

* Problem, w którym wyniki uruchamiane z wartościami null nie są wyświetlane jako rozwiązane. W przypadku zablokowania na tym problemie należy skontaktować się z zespołem pomocy technicznej.

* Skrypt HQL tworzony przez program Visual Studio jest kodowany w zależności od ustawienia lokalnego regionu użytkownika. Skrypt nie jest wykonywany poprawnie, jeśli został przesłany do klastra jako plik binarny.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawierał informacje dotyczące sposobu używania pakietu Data Lake Tools for Visual Studio w celu nawiązywania połączenia z klastrami usługi HDInsight z programu Visual Studio. Przedstawiono też sposób uruchamiania zapytania Hive. 

* [Uruchamianie zapytań Apache Hive przy użyciu wtyczki Data Lake Tools for Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](hdinsight-use-hive.md)
* [Tworzenie klastra Apache Hadoop — szablon](apache-hadoop-linux-tutorial-get-started.md)
* [Przesyłanie Apache Hadoop zadań w usłudze HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analizowanie danych usługi Twitter przy użyciu Apache Hive i Apache Hadoop w usłudze HDInsight](../hdinsight-analyze-twitter-data-linux.md)