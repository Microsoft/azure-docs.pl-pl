---
title: 'Azure Toolkit for IntelliJ: Spark App-HDInsight'
description: Użyj Azure Toolkit for IntelliJ, aby opracować aplikacje Spark zapisane w Scala i przesłać je do klastra usługi HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: 85d9da10650028b0f04458ea3553d010e5589ffd
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491380"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą Azure Toolkit for IntelliJ

W tym artykule przedstawiono sposób tworzenia aplikacji Apache Spark w usłudze Azure HDInsight przy użyciu wtyczki **zestawu Azure Toolkit** dla środowiska IDE IntelliJ. [Azure HDInsight](../hdinsight-overview.md) to zarządzana usługa analizy typu open source w chmurze. Usługa umożliwia korzystanie z platform typu open source, takich jak Hadoop, Apache Spark, Apache Hive i Apache Kafka.

Możesz użyć wtyczki **zestawu narzędzi platformy Azure** na kilka sposobów:

* Tworzenie i przesyłanie aplikacji Scala Spark do klastra usługi HDInsight Spark.
* Uzyskaj dostęp do zasobów klastra Azure HDInsight Spark.
* Tworzenie i uruchamianie aplikacji Scala Spark lokalnie.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
> * Korzystanie z wtyczki Azure Toolkit for IntelliJ
> * Opracowywanie aplikacji Apache Spark
> * Przesyłanie aplikacji do klastra usługi Azure HDInsight

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Zestaw Oracle Java Development](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  W tym artykule jest używane środowisko Java w wersji 8.0.202.

* IntelliJ pomysł. W tym artykule [jest stosowany pomysł IntelliJ Community Ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Zobacz [Installing the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) (Instalowanie zestawu Azure Toolkit for IntelliJ).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalowanie wtyczki Scala dla środowiska IntelliJ IDEA

Procedura instalacji wtyczki Scala:

1. Otwórz środowisko IntelliJ IDEA.

2. Na ekranie powitalnym przejdź do **konfigurowania**  >  **wtyczek** , aby otworzyć okno **wtyczki** .

    ![IntelliJ pomysł włącza wtyczkę Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Wybierz pozycję **Install** (Instaluj) dla wtyczki Scala, która zostanie wyświetlona w nowym oknie.  

    ![IntelliJ pomysł instaluje wtyczkę Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Po pomyślnym zainstalowaniu wtyczki musisz ponownie uruchomić środowisko IDE.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Tworzenie aplikacji Spark Scala dla klastra usługi HDInsight Spark

1. Uruchom środowisko IntelliJ IDEA i wybierz pozycję **Create New Project** (Utwórz nowy projekt), aby otworzyć okno **New Project** (Nowy projekt).

2. Wybierz pozycję **Azure Spark/HDInsight** w okienku po lewej stronie.

3. Wybierz pozycję **Spark Project (Scala)** w głównym oknie.

4. Z listy rozwijanej **Narzędzie kompilacji** wybierz jedną z następujących opcji:
   * **Maven** — w celu obsługi kreatora tworzenia projektu Scala.
   * **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala.

     ![Okno dialogowe Nowy projekt IntelliJ pomysłów](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Wybierz pozycję **Dalej**.

6. W oknie **New Project** (Nowy projekt) podaj następujące informacje:  

    |  Właściwość   | Opis   |  
    | ----- | ----- |  
    |Project name (Nazwa projektu)| Wprowadź nazwę.  W tym artykule `myApp` .|  
    |Lokalizacja&nbsp;projektu| Wprowadź lokalizację, w której ma zostać zapisany projekt.|
    |Zestaw SDK projektu| To pole może być puste przy pierwszym użyciu POMYSŁu.  Wybierz pozycję **New...** (Nowy...) i przejdź do swojego zestawu JDK.|
    |Wersja platformy Spark|Kreator tworzenia integruje poprawną wersję dla zestawów Spark SDK i Scala SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie używana jest wersja **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Wybieranie zestawu SDK Apache Spark](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Wybierz pozycję **Zakończ**.  Udostępnienie projektu może potrwać kilka minut.

8. Projekt Spark automatycznie tworzy artefakt. Aby wyświetlić artefakt, wykonaj następujące czynności:

   a. Na pasku menu Przejdź do **File**  >  **struktury projektu pliku..**..

   b. W oknie **Struktura projektu** wybierz pozycję **artefakty**.  

   c. Po wyświetleniu artefaktu wybierz pozycję **Anuluj**  .

      ![Informacje o artefaktach w oknie dialogowym](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Dodaj swój kod źródłowy aplikacji, wykonując następujące czynności:

    a. W obszarze Projekt przejdź do usługi **MojaApl**  >  **src**  >  **Main**  >  **Scala**.  

    b. Kliknij prawym przyciskiem myszy pozycję **Scala**, a następnie przejdź do **nowej**  >  **klasy Scala**.

   ![Polecenia służące do tworzenia klasy Scala z projektu](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. W oknie dialogowym **Utwórz nową klasę Scala** Podaj nazwę, wybierz pozycję **obiekt** z listy rozwijanej **rodzaj** , a następnie wybierz przycisk **OK**.

     ![Okno dialogowe Tworzenie nowej klasy Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Plik **MojaApl. scala** zostanie otwarty w widoku głównym. Zastąp kod domyślny kodem znalezionym poniżej:  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    Kod odczytuje dane z HVAC.csv (dostępne we wszystkich klastrach usługi HDInsight Spark), pobiera wiersze mające tylko jedną cyfrę w siódmej kolumnie w pliku CSV i zapisuje dane wyjściowe w `/HVACOut` obszarze domyślnego kontenera magazynu dla klastra.

## <a name="connect-to-your-hdinsight-cluster"></a>Nawiązywanie połączenia z klastrem usługi HDInsight

Użytkownik może [zalogować się do subskrypcji platformy Azure](#sign-in-to-your-azure-subscription)lub [połączyć klaster usługi HDInsight](#link-a-cluster). Do nawiązania połączenia z klastrem usługi HDInsight służy poświadczenie Ambari username/Password lub dołączona do domeny.

### <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

1. Na pasku menu Przejdź do **widoku**  >  **Narzędzia**  >  **Eksplorator platformy Microsoft Azure**.

   ![IntelliJ pomysł przedstawia Eksplorator platformy Azure](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. W programie Azure Explorer kliknij prawym przyciskiem myszy węzeł **Azure** , a następnie wybierz pozycję **Zaloguj się**.

   ![IntelliJ pomysł Explorer kliknij prawym przyciskiem myszy pozycję Azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. W oknie dialogowym **Logowanie do platformy Azure** wybierz pozycję **Logowanie do urządzenia**, a następnie wybierz pozycję **Zaloguj**.

    !["IntelliJ pomysł platformy Azure — login' urządzenia](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. W oknie dialogowym **Logowanie do urządzenia Azure** kliknij pozycję **Kopiuj&Otwórz**.

   !["IntelliJ pomysł Azure Device login'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. W interfejsie przeglądarki Wklej kod, a następnie kliknij przycisk **dalej**.

   !["Wprowadzanie kodu w oknie dialogowym Microsoft dla HDI"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Wprowadź swoje poświadczenia platformy Azure, a następnie zamknij przeglądarkę.

   !["Microsoft Enter e-mail okno dialogowe dla HDI"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Po zalogowaniu się w oknie dialogowym **Wybieranie subskrypcji** zostaną wyświetlone wszystkie subskrypcje platformy Azure skojarzone z poświadczeniami. Wybierz swoją subskrypcję, a następnie wybierz przycisk **Wybierz** .

    ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. W **Eksploratorze Azure**rozwiń węzeł **HDInsight** , aby wyświetlić klastry usługi HDInsight Spark, które znajdują się w Twoich subskrypcjach.

    ![Widok główny Eksploratora Azure IntelliJ pomysł](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Aby wyświetlić zasoby (na przykład konta magazynu) skojarzone z klastrem, można rozszerzyć węzeł Nazwa klastra.

    ![Konta usługi Azure Explorer Storage](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Łączenie klastra

Można połączyć klaster usługi HDInsight przy użyciu nazwy użytkownika zarządzanego przez Apache Ambari. Podobnie w przypadku klastra usługi HDInsight przyłączonego do domeny można łączyć się za pomocą domeny i nazwy użytkownika, takich jak `user1@contoso.com` . Ponadto można połączyć klaster usługi usługi Livy.

1. Na pasku menu Przejdź do **widoku**  >  **Narzędzia**  >  **Eksplorator platformy Microsoft Azure**.

1. W programie Azure Explorer kliknij prawym przyciskiem myszy węzeł **HDInsight** , a następnie wybierz pozycję **Połącz klaster**.

   ![Menu kontekstowe klastra linków programu Azure Explorer](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Dostępne opcje w oknie **łączenie klastra** różnią się w zależności od wybranej wartości z listy rozwijanej **Typ zasobu linku** .  Wprowadź wartości, a następnie wybierz przycisk **OK**.

    * **Klaster usługi HDInsight**  
  
        |Właściwość |Wartość |
        |----|----|
        |Typ zasobu linku|Z listy rozwijanej wybierz pozycję **klaster usługi HDInsight** .|
        |Nazwa/adres URL klastra| Wprowadź nazwę klastra.|
        |Typ uwierzytelniania| Pozostaw jako **uwierzytelnianie podstawowe**|
        |Nazwa użytkownika| Wprowadź nazwę użytkownika klastra. wartość domyślna to admin.|
        |Hasło| Wprowadź hasło dla nazwy użytkownika.|

        ![IntelliJ POMYSŁu — okno dialogowe klastra](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Usługa usługi Livy**  
  
        |Właściwość |Wartość |
        |----|----|
        |Typ zasobu linku|Z listy rozwijanej wybierz pozycję **Usługa usługi Livy** .|
        |Punkt końcowy usługi Livy| Wprowadź punkt końcowy usługi Livy|
        |Nazwa klastra| Wprowadź nazwę klastra.|
        |Punkt końcowy przędzy|Opcjonalny.|
        |Typ uwierzytelniania| Pozostaw jako **uwierzytelnianie podstawowe**|
        |Nazwa użytkownika| Wprowadź nazwę użytkownika klastra. wartość domyślna to admin.|
        |Hasło| Wprowadź hasło dla nazwy użytkownika.|

        ![Okno dialogowe klastra usługi Livy IntelliJ POMYSŁu](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Połączony klaster można zobaczyć z węzła usługi **HDInsight** .

   ![Klaster1 połączonej Eksploratora platformy Azure](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. Możesz również odłączyć klaster od programu **Azure Explorer**.

   ![Niepołączony klaster programu Azure Explorer](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Uruchamianie aplikacji platformy Spark Scala w klastrze usługi HDInsight Spark

Po utworzeniu aplikacji Scala można przesłać ją do klastra.

1. W programie Project przejdź do usługi **MojaApl**  >  **src**  >  **Main**  >  **Scala**  >  **MojaApl**.  Kliknij prawym przyciskiem myszy pozycję **MojaApl**i wybierz pozycję **Prześlij aplikację Spark** (prawdopodobnie będzie ona znajdować się w dolnej części listy).

      ![Polecenie Prześlij aplikację platformy Spark do usługi HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. W oknie dialogowym **przesyłanie aplikacji platformy Spark** wybierz pozycję **1. Platforma Spark w usłudze HDInsight**.

3. W oknie **Edycja konfiguracji** podaj następujące wartości, a następnie wybierz przycisk **OK**:

    |Właściwość |Wartość |
    |----|----|
    |Klastry Spark (tylko system Linux)|Wybierz klaster usługi HDInsight Spark, na którym chcesz uruchomić aplikację.|
    |Wybierz artefakt do przesłania|Pozostaw ustawienie domyślne.|
    |Nazwa klasy głównej|Wartością domyślną jest główna Klasa z wybranego pliku. Możesz zmienić klasę, wybierając wielokropek (**...**)  i wybierając inną klasę.|
    |Konfiguracje zadań|Można zmienić domyślne klucze i lub wartości. Aby uzyskać więcej informacji, zobacz [interfejs API REST usługi Apache usługi Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Argumenty wiersza polecenia|W razie potrzeby można wprowadzać argumenty rozdzielone spacją dla klasy głównej.|
    |Odwołania do Jars i plików, do których istnieją odwołania|Możesz wprowadzić ścieżki do przywoływanych Jars i plików, jeśli istnieją. Można również przeglądać pliki w wirtualnym systemie plików platformy Azure, który obecnie obsługuje tylko klaster ADLS generacji 2. Aby uzyskać więcej informacji: [konfiguracja Apache Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Zobacz również, [jak przekazywać zasoby do klastra](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Magazyn przekazywania zadań|Rozwiń, aby wyświetlić dodatkowe opcje.|
    |Typ magazynu|Wybierz pozycję **Użyj obiektów blob platformy Azure do przekazania** z listy rozwijanej.|
    |Konto magazynu|Wprowadź konto magazynu.|
    |Klucz magazynu|Wprowadź klucz magazynu.|
    |Kontener magazynu|Z listy rozwijanej wybierz kontener magazynu, po wprowadzeniu **konta magazynu** i **klucza magazynu** .|

    ![Okno dialogowe przesyłanie danych platformy Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Wybierz pozycję **SparkJobRun** , aby przesłać projekt do wybranego klastra. **Zadanie zdalnej platformy Spark na karcie klaster** wyświetla postęp wykonywania zadania u dołu. Aby zatrzymać aplikację, kliknij przycisk czerwony.

    ![Okno przesłania Apache Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Debugowanie aplikacji Apache Spark lokalnie lub zdalnie w klastrze usługi HDInsight

Zalecamy również inny sposób przesłania aplikacji platformy Spark do klastra. Można to zrobić przez ustawienie parametrów w środowisku IDE **konfiguracji uruchamiania/debugowania** . Zobacz [debugowanie Apache Spark aplikacji lokalnie lub zdalnie w klastrze usługi HDInsight z Azure Toolkit for IntelliJ za pośrednictwem protokołu SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Dostęp do klastrów usługi HDInsight Spark i zarządzanie nimi przy użyciu Azure Toolkit for IntelliJ

Różne operacje można wykonywać za pomocą Azure Toolkit for IntelliJ.  Większość operacji jest uruchamiana z poziomu **Eksploratora platformy Azure**.  Na pasku menu Przejdź do **widoku**  >  **Narzędzia**  >  **Eksplorator platformy Microsoft Azure**.

### <a name="access-the-job-view"></a>Dostęp do widoku zadania

1. W Eksploratorze Azure przejdź do zadania usługi **HDInsight**  >  \<Your Cluster>  >  **Jobs**.

    ![Węzeł widoku zadań w Eksploratorze IntelliJ systemu Azure](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. W okienku po prawej stronie karta **widok zadania platformy Spark** wyświetla wszystkie aplikacje, które zostały uruchomione w klastrze. Wybierz nazwę aplikacji, dla której chcesz zobaczyć więcej szczegółów.

    ![Szczegóły aplikacji w widoku zadania platformy Spark](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Aby wyświetlić podstawowe informacje o zadaniach, umieść wskaźnik myszy na grafie zadania. Aby wyświetlić wykres etapów i informacje, które generują każde zadanie, wybierz węzeł na grafie zadania.

    ![Szczegóły etapu zadania widoku zadania platformy Spark](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Aby wyświetlić często używane dzienniki, takie jak *Sterownik stderr*, *Sterownik stdout*i *Informacje o katalogu*, wybierz kartę **Dziennik** .

    ![Szczegóły dziennika widoku zadania platformy Spark](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Można wyświetlić interfejs użytkownika historii platformy Spark i interfejs użytkownika PRZĘDZy (na poziomie aplikacji). Wybierz łącze w górnej części okna.

### <a name="access-the-spark-history-server"></a>Dostęp do serwera historii platformy Spark

1. W Eksploratorze Azure rozwiń węzeł **HDInsight**, kliknij prawym przyciskiem myszy nazwę klastra Spark, a następnie wybierz pozycję **Otwórz interfejs użytkownika historii platformy Spark**.  
2. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra, które zostały określone podczas konfigurowania klastra.

3. Na pulpicie nawigacyjnym serwera historii platformy Spark możesz użyć nazwy aplikacji, aby wyszukać aplikację, która właśnie została uruchomiona. W powyższym kodzie, należy ustawić nazwę aplikacji przy użyciu `val conf = new SparkConf().setAppName("myApp")` . Nazwa aplikacji platformy Spark to **MojaApl**.

### <a name="start-the-ambari-portal"></a>Uruchom Portal Ambari

1. W Eksploratorze Azure rozwiń węzeł **HDInsight**, kliknij prawym przyciskiem myszy nazwę klastra Spark, a następnie wybierz pozycję **Otwórz klaster Portal zarządzania (Ambari)**.  

2. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra. Te poświadczenia zostały określone podczas procesu instalacji klastra.

### <a name="manage-azure-subscriptions"></a>Zarządzanie subskrypcjami platformy Azure

Domyślnie Azure Toolkit for IntelliJ wyświetla listę klastrów Spark ze wszystkich subskrypcji platformy Azure. W razie potrzeby możesz określić subskrypcje, do których chcesz uzyskać dostęp.  

1. W programie Azure Explorer kliknij prawym przyciskiem myszy węzeł główny **platformy Azure** , a następnie wybierz pozycję **wybierz subskrypcje**.  

2. W oknie **Wybieranie subskrypcji** wyczyść pola wyboru obok subskrypcji, do których chcesz uzyskać dostęp, a następnie wybierz pozycję **Zamknij**.

## <a name="spark-console"></a>Konsola platformy Spark

Można uruchomić konsolę lokalną Spark (Scala) lub uruchomić konsolę sesji interaktywnej Spark usługi Livy (Scala).

### <a name="spark-local-consolescala"></a>Konsola lokalna Spark (Scala)

Upewnij się, że spełniono wymagania wstępne WINUTILS.EXE.

1. Na pasku menu Przejdź do opcji **Uruchom**  >  **Edytowanie konfiguracji..**..

2. W oknie **konfiguracje uruchamiania/debugowania** w lewym okienku przejdź do **Apache Spark w usłudze HDInsight**  >  **[Spark w usłudze HDInsight] MojaApl**.

3. W oknie głównym wybierz **`Locally Run`** kartę.

4. Podaj następujące wartości, a następnie wybierz przycisk **OK**:

    |Właściwość |Wartość |
    |----|----|
    |Klasa główna zadania|Wartością domyślną jest główna Klasa z wybranego pliku. Możesz zmienić klasę, wybierając wielokropek (**...**)  i wybierając inną klasę.|
    |Zmienne środowiskowe|Upewnij się, że wartość HADOOP_HOME jest poprawna.|
    |Lokalizacja WINUTILS.exe|Upewnij się, że ścieżka jest poprawna.|

    ![Lokalna konfiguracja zestawu konsoli](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. W programie Project przejdź do usługi **MojaApl**  >  **src**  >  **Main**  >  **Scala**  >  **MojaApl**.  

6. Na pasku menu Przejdź do menu **Narzędzia**  >  **platformy Spark**  >  **Uruchom konsolę usługi Spark (Scala)**.

7. Następnie można wyświetlić dwa okna dialogowe z monitem, jeśli chcesz automatyczne naprawić zależności. W takim przypadku wybierz pozycję **Automatyczne rozwiązywanie**.

    ![IntelliJ pomysł Spark automatycznej poprawki dialog1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ pomysł Spark automatycznej poprawki dialog2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. Konsola powinna wyglądać podobnie do poniższej ilustracji. W oknie konsoli wpisz `sc.appName` , a następnie naciśnij klawisze CTRL + ENTER.  Zostanie wyświetlony wynik. Aby zakończyć konsolę lokalną, kliknij przycisk czerwony.

    ![Wynik z lokalnej konsoli POMYSŁu IntelliJ](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Konsola sesji interaktywnej Spark usługi Livy (Scala)

1. Na pasku menu Przejdź do opcji **Uruchom**  >  **Edytowanie konfiguracji..**..

2. W oknie **konfiguracje uruchamiania/debugowania** w lewym okienku przejdź do **Apache Spark w usłudze HDInsight**  >  **[Spark w usłudze HDInsight] MojaApl**.

3. W oknie głównym wybierz **`Remotely Run in Cluster`** kartę.

4. Podaj następujące wartości, a następnie wybierz przycisk **OK**:

    |Właściwość |Wartość |
    |----|----|
    |Klastry Spark (tylko system Linux)|Wybierz klaster usługi HDInsight Spark, na którym chcesz uruchomić aplikację.|
    |Nazwa klasy głównej|Wartością domyślną jest główna Klasa z wybranego pliku. Możesz zmienić klasę, wybierając wielokropek (**...**)  i wybierając inną klasę.|

    ![Interaktywna konfiguracja zestawu konsolowego](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. W programie Project przejdź do usługi **MojaApl**  >  **src**  >  **Main**  >  **Scala**  >  **MojaApl**.  

6. Na pasku menu Przejdź do menu **Narzędzia**  >  **platformy Spark**  >  **Uruchom konsolę sesji programu Spark usługi Livy Interactive (Scala)**.

7. Konsola powinna wyglądać podobnie do poniższej ilustracji. W oknie konsoli wpisz `sc.appName` , a następnie naciśnij klawisze CTRL + ENTER.  Zostanie wyświetlony wynik. Aby zakończyć konsolę lokalną, kliknij przycisk czerwony.

    ![Wynik interaktywnej konsoli POMYSŁu IntelliJ](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Wyślij zaznaczenie do konsoli platformy Spark

Jest to wygodne, aby przewidzieć wynik skryptu, wysyłając jakiś kod do konsoli lokalnej lub konsoli sesji interakcyjnej usługi Livy (Scala). Można wyróżnić kod w pliku Scala, a następnie kliknąć prawym przyciskiem myszy pozycję **Wyślij zaznaczenie do konsoli platformy Spark**. Wybrany kod zostanie wysłany do konsoli. Wynik zostanie wyświetlony po kodzie w konsoli programu. Konsola sprawdzi błędy, jeśli istnieją.  

   ![Wyślij zaznaczenie do konsoli platformy Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integracja z usługą HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Nawiązywanie połączenia z klastrem usługi HDInsight ESP z brokerem identyfikatorów (HIB)

Aby zalogować się do subskrypcji platformy Azure w celu nawiązania połączenia z klastrem usługi HDInsight ESP z brokerem identyfikatorów (HIB), wykonaj kroki opisane w temacie. Po zalogowaniu zostanie wyświetlona lista klastrów w Eksploratorze Azure. Aby uzyskać więcej instrukcji, zobacz [nawiązywanie połączenia z klastrem usługi HDInsight](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Uruchamianie aplikacji Spark Scala w klastrze usługi HDInsight ESP z IDENTYFIKATORem brokera (HIB)

Aby przesłać zadanie do klastra usługi HDInsight ESP z IDENTYFIKATORem brokera (HIB), wykonaj kroki opisane w sekcji normalne. Aby uzyskać więcej instrukcji, zobacz [Uruchamianie aplikacji platformy Spark Scala w klastrze usługi HDInsight Spark](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) .

Pliki wymagane do folderu o nazwie przy użyciu konta logowania są przekazywane i można zobaczyć ścieżkę przekazywania w pliku konfiguracji.

   ![Przekaż ścieżkę w konfiguracji](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Konsola platformy Spark w klastrze usługi HDInsight ESP z IDENTYFIKATORem brokera (HIB)

Można uruchomić konsolę usługi Spark (Scala) lub uruchomić konsolę sesji programu Spark usługi Livy Interactive (Scala) w klastrze usługi HDInsight ESP z IDENTYFIKATORem Broker (HIB). Więcej instrukcji można znaleźć w [konsoli platformy Spark](#spark-console) .

   > [!NOTE]  
   > W przypadku klastra usługi HDInsight ESP z identyfikatorem brokera (HIB) [Połącz klaster](#link-a-cluster) i [Debuguj Apache Spark aplikacje zdalnie](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) nie są obecnie obsługiwane.

## <a name="reader-only-role"></a>Rola tylko do odczytu

Gdy użytkownicy przesyłają zadanie do klastra z uprawnieniami roli tylko do odczytu, wymagane są poświadczenia Ambari.

### <a name="link-cluster-from-context-menu"></a>Połącz klaster z menu kontekstowego

1. Zaloguj się przy użyciu konta roli tylko czytelnik.

2. W **Eksploratorze Azure**rozwiń węzeł **HDInsight** , aby wyświetlić klastry usługi HDInsight, które znajdują się w Twojej subskrypcji. Klastry oznaczone jako **"role: Reader"** mają uprawnienia roli tylko do odczytu.

    !["IntelliJ rolę programu Azure Explorer: Reader"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Kliknij prawym przyciskiem myszy klaster z uprawnieniami roli tylko czytelnik. Wybierz opcję **Połącz ten klaster** z menu kontekstowego, aby połączyć klaster. Wprowadź nazwę użytkownika i hasło Ambari.

    ![IntelliJ systemu Azure Explorer Połącz ten klaster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Jeśli klaster zostanie połączony pomyślnie, Usługa HDInsight zostanie odświeżona.
   Etap klastra zostanie połączony.
  
    ![Połączone okno dialogowe programu IntelliJ Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Łączenie klastra przez rozwijanie węzła zadania

1. Kliknij pozycję **zadania** węzeł, okna **odmowa dostępu do zadania klastra** .

2. Kliknij przycisk **Połącz ten klaster** , aby dołączyć klaster.

    ![okno dialogowe odmowa dostępu do zadania klastra](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Połącz klaster z okna konfiguracji uruchamiania/debugowania

1. Utwórz konfigurację usługi HDInsight. Następnie wybierz pozycję **Zdalnie Uruchom w klastrze**.

2. Wybierz klaster, który ma uprawnienia roli tylko do odczytu dla **klastrów Spark (tylko system Linux)**. Komunikat ostrzegawczy jest wyświetlany. Możesz kliknąć przycisk **Połącz ten klaster** , aby połączyć klaster.

   ![Tworzenie konfiguracji IntelliJ/debugowanie przebiegu POMYSŁu](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Wyświetlanie kont magazynu

* W przypadku klastrów z uprawnieniem tylko do odczytu, kliknij węzeł **konta magazynu** , zostanie wystawione okno **odmowa dostępu do magazynu** . Możesz kliknąć pozycję **otwórz Eksplorator usługi Azure Storage** , aby otworzyć Eksplorator usługi Storage.

   !["Odmowa dostępu do magazynu POMYSŁu IntelliJ"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Przycisk odmowy dostępu do magazynu POMYSŁu IntelliJ](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* W przypadku połączonych klastrów kliknij węzeł **konta magazynu** , w którym znajduje się okno **odmowa dostępu do magazynu** . Możesz kliknąć pozycję **Otwórz usługę Azure Storage** , aby otworzyć Eksplorator usługi Storage.

   !["IntelliJ pomysł do magazynu Denied2"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![IntelliJ pomysł — dostęp do magazynu Denied2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konwertuj istniejące aplikacje IntelliJ pomysł na używanie Azure Toolkit for IntelliJ

Istniejące aplikacje Spark Scala, które zostały utworzone w programie IntelliJ, są zgodne z Azure Toolkit for IntelliJ. Możesz następnie użyć wtyczki do przesyłania aplikacji do klastra usługi HDInsight Spark.

1. W przypadku istniejącej aplikacji Spark Scala, która została utworzona za pomocą POMYSŁu IntelliJ, Otwórz skojarzony `.iml` plik.

2. Na poziomie głównym jest elementem **modułu** podobnym do następującego:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   Edytuj element do dodania, aby `UniqueKey="HDInsightTool"` element **modułu** wyglądał jak następujący tekst:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. Zapisz zmiany. Aplikacja powinna być teraz zgodna z Azure Toolkit for IntelliJ. Możesz go przetestować, klikając prawym przyciskiem myszy nazwę projektu w programie Project. Menu podręczne zawiera teraz opcję **przesyłania aplikacji platformy Spark do usługi HDInsight**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń klaster, który został utworzony, wykonując następujące czynności:

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).

1. W polu **Wyszukaj** w górnej części wpisz **HDInsight**.

1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi**.

1. Na liście wyświetlonych klastrów usługi HDInsight wybierz pozycję **...** obok klastra utworzonego w tym artykule.

1. Wybierz pozycję **Usuń**. Wybierz pozycję **Tak**.

![Azure Portal usuwa klaster usługi HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania z wtyczki Azure Toolkit for IntelliJ w celu opracowywania aplikacji Apache Spark utworzonych w [Scala](https://www.scala-lang.org/). Następnie przesłać je do klastra usługi HDInsight Spark bezpośrednio z poziomu zintegrowanego środowiska projektowego IntelliJ (IDE). Przejdź do następnego artykułu, aby dowiedzieć się, w jaki sposób można ściągnąć dane zarejestrowane na platformie Apache Spark do narzędzia analizy biznesowej, takiego jak usługa Power BI.

> [!div class="nextstepaction"]
> [Analizowanie danych Apache Spark przy użyciu Power BI](apache-spark-use-bi-tools.md)
