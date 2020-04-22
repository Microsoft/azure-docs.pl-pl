---
title: 'Zestaw narzędzi platformy Azure dla programu Eclipse: tworzenie aplikacji Scala dla platformy HDInsight Spark'
description: Użyj narzędzia HDInsight w usłudze Azure Toolkit for Eclipse, aby opracować aplikacje Spark napisane w scalii i przesłać je do klastra platformy SPARK usługi HDInsight bezpośrednio z środowiska IDE programu Eclipse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: 5ae9190213f753f8b9440ced52e4d04626af13f9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732996"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą zestawu narzędzi Azure toolkit for Eclipse

Użyj narzędzia HDInsight w usłudze Azure Toolkit for [Eclipse,](https://www.eclipse.org/) aby opracować [aplikacje Apache Spark](https://spark.apache.org/) napisane w [scalii](https://www.scala-lang.org/) i przesłać je do klastra platformy Azure HDInsight Spark bezpośrednio z środowiska ECLIPSE IDE. Wtyczkę HDInsight Tools można używać na kilka różnych sposobów:

* Aby opracować i przesłać aplikację Scala Spark w klastrze PLATFORMY HDInsight Spark.
* Aby uzyskać dostęp do zasobów klastra platformy Azure HDInsight Spark.
* Aby lokalnie opracować i uruchomić aplikację Scala Spark.

## <a name="prerequisites"></a>Wymagania wstępne

* Apache Spark klastra na HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Zestaw Java Developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks).

* [Eclipse IDE](https://www.eclipse.org/downloads/). W tym artykule użyto środowiska Eclipse IDE dla deweloperów języka Java.

## <a name="install-required-plug-ins"></a>Instalowanie wymaganych wtyczek

### <a name="install-azure-toolkit-for-eclipse"></a>Instalowanie zestawu Azure Toolkit for Eclipse

Aby uzyskać instrukcje instalacji, zobacz [Instalowanie zestawu narzędzi platformy Azure dla programu Eclipse](https://docs.microsoft.com/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>Instalowanie wtyczki Scala

Po otwarciu programu Eclipse program HDInsight Tools automatycznie wykrywa, czy zainstalowano wtyczkę Scala. Wybierz **przycisk OK,** aby kontynuować, a następnie postępuj zgodnie z instrukcjami, aby zainstalować wtyczkę w portalu Eclipse Marketplace. Uruchom ponownie IDE po zakończeniu instalacji.

![Automatyczna instalacja wtyczki Scala](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

### <a name="confirm-plug-ins"></a>Potwierdzanie wtyczek

1. Przejdź do **witryny Eclipse** > **Marketplace...**.

1. Wybierz kartę **Zainstalowano**.

1. Powinieneś zobaczyć co najmniej:
    * Zestaw narzędzi platformy \<Azure dla wersji Eclipse>.
    * wersja \<Scala IDE>.

## <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

1. Uruchom Eclipse IDE.

1. Przejdź do widoku**pokaż** >  **okno** >  **inne...**  >  **Zaloguj się..**.

1. W oknie dialogowym **Pokaż widok** przejdź do**eksploratora** **Azure** > Azure , a następnie wybierz pozycję **Otwórz**.

   ![Apache Spark Eclipse widok pokazu](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. W **Eksploratorze platformy Azure**kliknij prawym przyciskiem myszy węzeł platformy **Azure,** a następnie wybierz polecenie **Zaloguj się**.

1. W oknie dialogowym **Azure Sign In** wybierz metodę uwierzytelniania, wybierz pozycję Zaloguj **się**i zakończ proces logowania.

   ![Znak platformy Azure Zaćmienia Iskry Apache](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Po zalogowaniu się okno dialogowe **Twoje subskrypcje** zawiera listę wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami. Naciśnij **klawisz Wybierz,** aby zamknąć okno dialogowe.

   ![Okno dialogowe Wybieranie subskrypcji](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. Z **Usługi Azure Explorer**przejdź do usługi **Azure** >  **HDInsight,** aby wyświetlić klastry platformy SPARK usługi HDInsight w ramach subskrypcji.

   ![Klastry platformy Spark usługi HDInsight w Eksploratorze Azure3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. Można dodatkowo rozwinąć węzeł nazwy klastra, aby wyświetlić zasoby (na przykład konta magazynu) skojarzone z klastrem.

   ![Rozwijanie nazwy klastra w celu wyświetlenia zasobów](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Łączenie klastra

Możesz połączyć normalny klaster przy użyciu zarządzanej nazwy użytkownika Ambari. Podobnie w przypadku klastra HDInsight przyłączony do domeny można połączyć `user1@contoso.com`za pomocą domeny i nazwy użytkownika, takich jak .

1. W **eksploratorze platformy Azure**kliknij prawym przyciskiem myszy pozycję **HDInsight**i wybierz polecenie **Połącz klaster**.

   ![Menu klastra łączy Usługi Azure Explorer](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Wprowadź **nazwę klastra,** **nazwę użytkownika**i **hasło**, a następnie wybierz przycisk **OK**. Opcjonalnie wprowadź konto magazynu, klucz magazynu, a następnie wybierz pozycję Kontener magazynu dla Eksploratora magazynu do pracy w widoku drzewa po lewej stronie

   ![Okno dialogowe Łączenie nowego klastra usługi HDInsight](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > Używamy połączonego klucza magazynu, nazwy użytkownika i hasła, jeśli klaster jest zarówno zalogowany w subskrypcji platformy Azure, jak i w klastrze połączonym.
   > ![Konta magazynu usługi Azure Explorer](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > W przypadku klawiatury tylko dla użytkownika, gdy bieżący fokus znajduje się w **klawiszu storage,** należy użyć **klawiszy Ctrl+TAB,** aby skupić się na następnym polu w oknie dialogowym.

1. Połączony klaster można zobaczyć w obszarze **HDInsight**. Teraz możesz przesłać wniosek do tego połączonego klastra.

   ![Połączony klaster usługi Azure Explorer hdi](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. Można również odłączyć klaster od **Usługi Azure Explorer**.

   ![Niezłączony klaster Usługi Azure Explorer](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Konfigurowanie projektu Spark Scala dla klastra platformy SPARK usługi HDInsight

1. W obszarze roboczym Eclipse IDE wybierz pozycję **Plik** > **nowego** > **projektu...**.

1. W kreatorze **Nowego projektu** wybierz pozycję **HDInsight Project** > **Spark on HDInsight (Scala)**. Następnie wybierz **przycisk Dalej**.

   ![Wybieranie projektu Spark on HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. W oknie dialogowym **Nowy projekt hdinsight scala** podaj następujące wartości, a następnie wybierz przycisk **Dalej:**
   * Wprowadź nazwę dla projektu.
   * W obszarze **ŚRODOWISKA JRE** upewnij się, że **środowisko wykonywania środowiska JRE** jest ustawione na **JavaSE-1.7** lub nowszą.
   * W obszarze **Biblioteka iskier** można wybrać opcję **Użyj maven, aby skonfigurować opcję Zestaw SDK platformy Spark.**  Nasze narzędzie integruje właściwą wersję dla Spark SDK i Scala SDK. Można również wybrać opcję **Dodaj zestaw SDK spark ręcznie,** pobrać i dodać zestaw Spark SDK ręcznie.

   ![Nowe okno dialogowe Projektu programu HDInsight Scala](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. W następnym oknie dialogowym przejrzyj szczegóły, a następnie wybierz pozycję **Zakończ**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Tworzenie aplikacji Scala dla klastra hdinsight Spark

1. Z **Eksploratora pakietów**rozwiń projekt utworzony wcześniej. Kliknij prawym przyciskiem myszy **src**, wybierz **pozycję Nowy** > **inny...**.

1. W oknie **dialogowym Wybieranie kreatora** wybierz pozycję **Scala Wizards** > **Scala Object**. Następnie wybierz **przycisk Dalej**.

   ![Wybieranie kreatora Tworzenie obiektu Scala](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)

1. W oknie dialogowym **Tworzenie nowego pliku** wprowadź nazwę obiektu, a następnie wybierz pozycję **Zakończ**. Otworzy się edytor tekstu.

   ![Kreator nowych plików Utwórz nowy plik](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)

1. W edytorze tekstu zastąp bieżącą zawartość poniższym kodem:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Uruchom aplikację w klastrze platformy SPARK usługi HDInsight:

   a. W Eksploratorze pakietów kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz polecenie **Prześlij aplikację platformy Spark do programu HDInsight**.

   b. W oknie dialogowym **Przesyłanie platformy Spark** podaj następujące wartości, a następnie wybierz pozycję **Prześlij:**

   * W obszarze **Nazwa klastra**wybierz klaster platformy Spark usługi HDInsight, na którym chcesz uruchomić aplikację.
   * Wybierz artefakt z projektu Eclipse lub wybierz go z dysku twardego. Wartość domyślna zależy od elementu, który można kliknąć prawym przyciskiem myszy z Eksploratora pakietów.
   * Na liście rozwijanej **Nazwa klasy głównej** kreator przesyłania wyświetla wszystkie nazwy obiektów z projektu. Wybierz lub wprowadź taki, który chcesz uruchomić. Jeśli wybrano artefakt z dysku twardego, należy wprowadzić nazwę klasy głównej ręcznie. 
   * Ponieważ kod aplikacji w tym przykładzie nie wymaga żadnych argumentów wiersza polecenia lub odwołania JARs lub plików, można pozostawić pozostałe pola tekstowe puste.

     ![Okno dialogowe Przesyłanie platformy Spark apache](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. Karta **Przesyłanie platformy Spark** powinna rozpocząć wyświetlanie postępu. Aplikację można zatrzymać, wybierając czerwony przycisk w oknie **Przesyłanie platformy Spark.** Można również wyświetlić dzienniki dla tej konkretnej aplikacji uruchamiane przez wybranie ikony kuli ziemskiej (oznaczonej przez niebieskie pole na obrazie).

   ![Apache Spark Okno składania](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Uzyskiwanie dostępu do klastrów platformy SPARK usługi HDInsight i zarządzanie nimi przy użyciu narzędzi HDInsight Tools w usłudze Azure Toolkit for Eclipse

Można wykonywać różne operacje przy użyciu narzędzi HDInsight, w tym uzyskiwania dostępu do danych wyjściowych zadania.

### <a name="access-the-job-view"></a>Dostęp do widoku zadania

1. W **Eksploratorze Platformy Azure**rozwiń pozycję **HDInsight**, a następnie nazwę klastra Platformy Spark, a następnie wybierz pozycję **Zadania**.

   ![Węzeł widoku zadania za pomocą narzędzia Azure Explorer Eclipse](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Wybierz węzeł **Zadania.** Jeśli wersja Java jest niższa niż **1,8**, narzędzia HDInsight automatycznie przypominają o zainstalowaniu **wtyczki E(fx)clipse.** Wybierz **przycisk OK,** aby kontynuować, a następnie postępuj zgodnie z instrukcjami kreatora, aby zainstalować go w portalu Eclipse Marketplace i ponownie uruchomić aplikację Eclipse.

   ![Zainstaluj brakujące wtyczki E(fx)clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Otwórz widok zadania z węzła **Zadania.** W prawym okienku karta **Widok zadania platformy Spark** wyświetla wszystkie aplikacje uruchomione w klastrze. Wybierz nazwę aplikacji, dla której chcesz wyświetlić więcej szczegółów.

   ![Apache Eclipse zobacz szczegóły pracy](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Następnie można podjąć dowolną z następujących czynności:

   * Umieść wskaźnik myszy na wykresie zadań. Wyświetla podstawowe informacje o uruchomionym zadaniu. Wybierz wykres zadania, aby wyświetlić etapy i informacje generowane przez każde zadanie.

     ![Apache Spark informacje o etapie pracy](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Wybierz kartę **Dziennik,** aby wyświetlić często używane dzienniki, w tym **Sterownik Stderr,** **Driver Stdout**i **Directory Info**.

     ![Apache Spark Eclipse informacje o dzienniku zadań](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Otwórz interfejs użytkownika historii platformy Spark i interfejs użytkownika usługi Apache Hadoop YARN (na poziomie aplikacji), wybierając hiperłącza w górnej części okna.

### <a name="access-the-storage-container-for-the-cluster"></a>Dostęp do kontenera magazynu dla klastra

1. W Eksploratorze platformy Azure rozwiń węzeł główny **usługi HDInsight,** aby wyświetlić listę dostępnych klastrów platformy SPARK usługi HDInsight.

1. Rozwiń nazwę klastra, aby wyświetlić konto magazynu i domyślny kontener magazynu dla klastra.

   ![Konto magazynu i domyślny kontener magazynu](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Wybierz nazwę kontenera magazynu skojarzoną z klastrem. W prawym okienku kliknij dwukrotnie folder **HVACOut.** Otwórz jeden z plików **części,** aby zobaczyć dane wyjściowe aplikacji.

### <a name="access-the-spark-history-server"></a>Dostęp do serwera historii platformy Spark

1. W Eksploratorze platformy Azure kliknij prawym przyciskiem myszy nazwę klastra platformy Spark, a następnie wybierz polecenie **Otwórz interfejs użytkownika historii iskier**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. Określono je podczas inicjowania obsługi administracyjnej klastra.

1. Na pulpicie nawigacyjnym serwera historii platformy Spark używasz nazwy aplikacji do wyszukiwania aplikacji, która właśnie została ukończona. W poprzednim kodzie można ustawić nazwę `val conf = new SparkConf().setAppName("MyClusterApp")`aplikacji za pomocą programu . Tak, nazwa aplikacji Spark był **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Uruchom portal Apache Ambari

1. W Eksploratorze platformy Azure kliknij prawym przyciskiem myszy nazwę klastra Platformy Spark, a następnie wybierz polecenie **Otwórz portal zarządzania klastrami (Ambari).**

1. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. Określono je podczas inicjowania obsługi administracyjnej klastra.

### <a name="manage-azure-subscriptions"></a>Zarządzanie subskrypcjami platformy Azure

Domyślnie narzędzie HDInsight w usłudze Azure Toolkit for Eclipse wyświetla listę klastrów platformy Spark ze wszystkich subskrypcji platformy Azure. W razie potrzeby można określić subskrypcje, dla których chcesz uzyskać dostęp do klastra.

1. W Eksploratorze platformy Azure kliknij prawym przyciskiem myszy węzeł główny **platformy Azure,** a następnie wybierz pozycję **Zarządzaj subskrypcjami**.

1. W oknie dialogowym wyczyść pola wyboru subskrypcji, do której nie chcesz uzyskiwać dostępu, a następnie wybierz pozycję **Zamknij**. Możesz również wybrać opcję **Wyloguj się,** jeśli chcesz wylogować się z subskrypcji platformy Azure.

## <a name="run-a-spark-scala-application-locally"></a>Lokalne uruchamianie aplikacji Spark Scala

Narzędzia HDInsight w usłudze Azure Toolkit for Eclipse umożliwiają lokalne uruchamianie aplikacji Spark Scala na stacji roboczej. Zazwyczaj te aplikacje nie potrzebują dostępu do zasobów klastra, takich jak kontener magazynu, i można uruchomić i przetestować je lokalnie.

### <a name="prerequisite"></a>Wymagania wstępne

Podczas uruchamiania lokalnej aplikacji Spark Scala na komputerze z systemem Windows może zostać uruchomiony wyjątek, jak wyjaśniono w [spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Ten wyjątek występuje, ponieważ w systemie Windows brakuje programu **WinUtils.exe.**

Aby rozwiązać ten problem, musisz [winutils.exe](https://github.com/steveloughran/winutils) do lokalizacji takiej jak **C:\WinUtils\bin**, a następnie dodać zmienną środowiskową **HADOOP_HOME** i ustawić wartość zmiennej na **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Uruchamianie lokalnej aplikacji Spark Scala

1. Uruchom eclipse i utwórz projekt. W oknie dialogowym **Nowy projekt** dokonaj następujących opcji, a następnie wybierz pozycję **Dalej**.

1. W kreatorze **Nowy projekt** wybierz pozycję **HDInsight Project** > **Spark on HDInsight Local Run Sample (Scala)**. Następnie wybierz **przycisk Dalej**.

   ![Nowy projekt wybiera okno dialogowe kreatora](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Aby podać szczegóły projektu, wykonaj kroki od 3 do 6 z wcześniejszej sekcji [Konfiguracja projektu Spark Scala dla klastra platformy SPARK usługi HDInsight](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Szablon dodaje przykładowy kod **(LogQuery**) w folderze **src,** który można uruchomić lokalnie na komputerze.

   ![Lokalizacja lokalnej aplikacji LogQuery scala](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Kliknij prawym przyciskiem myszy **pozycję LogQuery.scala** i wybierz polecenie **Uruchom jako** > **1 aplikację Scala**. Dane wyjściowe w ten sposób pojawiają się na karcie **Konsola:**

   ![Wynik uruchomienia lokalnego aplikacji Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Rola tylko do czytnika

Gdy użytkownicy przesyłają zadanie do klastra z uprawnieniami do roli tylko do czytnika, wymagane są poświadczenia Ambari.

### <a name="link-cluster-from-context-menu"></a>Klaster łączy z menu kontekstowego

1. Zaloguj się przy za pomocą konta roli tylko do czytnika.

2. Z **Usługi Azure Explorer**rozwiń usługę **HDInsight,** aby wyświetlić klastry usługi HDInsight, które znajdują się w ramach subskrypcji. Klastry oznaczone **jako "Rola:Czytnik"** mają tylko uprawnienia roli tylko do czytnika.

    ![Klastry platformy Spark usługi HDInsight w czytniku ról usługi Azure Explorer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Kliknij prawym przyciskiem myszy klaster z uprawnieniem roli tylko do czytnika. Wybierz **pozycję Połącz ten klaster** z menu kontekstowego, aby połączyć klaster. Wprowadź nazwę użytkownika i hasło Ambari.

    ![Łącze platformy Spark usługi HDInsight w Eksploratorze Platformy Azure](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Jeśli klaster jest połączony pomyślnie, hdinsight zostanie odświeżony.
   Etap klastra zostanie połączony.
  
    ![Klastry platformy Spark usługi HDInsight w eksploratorze Platformy Azure połączone](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Łącze klastra przez rozwijanie węzła Zadania

1. Kliknij węzeł **Zadania,** zostanie wyświetlony okno **Odmowa dostępu do zadania klastra.**

2. Kliknij pozycję **Połącz ten klaster,** aby połączyć klaster.

    ![Klastry platformy Spark usługi HDInsight w eksploratorze Azure9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Klaster łączy z okna Przesyłanie platformy Spark

1. Utwórz projekt HDInsight.

2. Kliknij prawym przyciskiem myszy pakiet. Następnie wybierz **pozycję Prześlij aplikację spark do usługi HDInsight**.

   ![Klastry platformy SPARK usługi HDInsight w Eksploratorze Platformy Azure](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Wybierz klaster, który ma uprawnienia roli tylko do czytnika dla **nazwy klastra**. Pojawia się komunikat ostrzegawczy. Możesz kliknąć pozycję **Połącz ten klaster,** aby połączyć klaster.

   ![Klastry platformy SPARK usługi HDInsight w Eksploratorze Platformy Azure łączą to](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Wyświetlanie kont magazynu

* W przypadku klastrów z uprawnieniem roli tylko do czytnika kliknij węzeł **Konta magazynu,** zostanie wyświetlony okno **Odmowa dostępu do magazynu.**

   ![Klastry platformy SPARK usługi HDInsight w magazynie usługi Azure Explorer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![Odmowa klastrów platformy SPARK usługi HDInsight w Eksploratorze Azure](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* W przypadku połączonych klastrów kliknij węzeł **Konta magazynu,** zostanie wyświetlony okno **Odmowa dostępu do** magazynu.

   ![Odmowa klastrów platformy SPARK usługi HDInsight w Eksploratorze Azure2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Znane problemy

Podczas korzystania z **link klastra,** proponuję podać poświadczenia magazynu.

![klaster łączy z zaćmieniami poświadczeń magazynu](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Istnieją dwa tryby przesyłania zadań. Jeśli podano poświadczenia magazynu, tryb wsadowy będzie używany do przesłania zadania. W przeciwnym razie zostanie użyty tryb interaktywny. Jeśli klaster jest zajęty, może pojawić się błąd poniżej.

![zaćmienie uzyskać błąd, gdy klaster zajęty](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "zaćmienie uzyskać błąd, gdy klaster zajęty")

![zaćmienie się błąd, gdy klaster zajęty przędzy](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "zaćmienie się błąd, gdy klaster zajęty przędzy")

## <a name="see-also"></a>Zobacz też

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie i przesyłanie aplikacji Spark Scala za pomocą zestawu narzędzi Platformy Azure dla technologii IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Użyj zestawu narzędzi Azure toolkit dla intellij do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Użyj zestawu narzędzi Azure toolkit dla intellij do zdalnego debugowania aplikacji Apache Spark za pośrednictwem SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
