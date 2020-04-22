---
title: 'Zestaw narzędzi platformy Azure dla technologii IntelliJ: Aplikacje Debugowania platformy Spark z usługą SSH — HDInsight'
description: Wskazówki krok po kroku dotyczące używania narzędzi HDInsight tools w usłudze Azure Toolkit dla technologii IntelliJ do zdalnego debugowania aplikacji w klastrach usługi HDInsight za pośrednictwem usługi SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: a012c3ce8f7c9e105a42d8383a502f3608c84070
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732906"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Debugowanie aplikacji Platformy Spark w klastrze HDInsight za pomocą zestawu narzędzi Azure toolkit dla technologii IntelliJ przez SSH

Ten artykuł zawiera wskazówki krok po kroku dotyczące używania narzędzi HDInsight w [usłudze Azure Toolkit dla intellij](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) do zdalnego debugowania aplikacji w klastrze HDInsight. Aby debugować projekt, można również wyświetlić [debugowania HDInsight Spark aplikacji z azure toolkit dla intellij](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) wideo.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Zobacz [Tworzenie klastra Platformy Spark apache](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Dla użytkowników systemu Windows: Podczas uruchamiania lokalnej aplikacji Spark Scala na komputerze z systemem Windows może zostać uruchomiony wyjątek, jak wyjaśniono w [spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Wyjątek występuje, ponieważ w systemie Windows brakuje programu WinUtils.exe.

    Aby rozwiązać ten problem, pobierz plik [Winutils.exe](https://github.com/steveloughran/winutils) do lokalizacji takiej jak **C:\WinUtils\bin**. Następnie dodaj zmienną środowiskową **HADOOP_HOME**i ustaw wartość zmiennej na **C:\WinUtils**.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (Wydanie społecznościowe jest bezpłatne.).

* [Zestaw narzędzi platformy Azure dla technologii IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/installation).

* [Scala plugin dla IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Tworzenie aplikacji Spark Scala

1. Uruchom środowisko IntelliJ IDEA i wybierz pozycję **Create New Project** (Utwórz nowy projekt), aby otworzyć okno **New Project** (Nowy projekt).

1. Z lewego okienka wybierz **pozycję Apache Spark/HDInsight.**

1. Z okna głównego wybierz **opcję Spark Project with Samples (Scala).**

1. Z listy rozwijanej **Build tool** (Narzędzie kompilacji), wybierz jedną z następujących pozycji:

    * **Maven** — w celu obsługi kreatora tworzenia projektu Scala.
    * **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala.

     ![Intellij Tworzenie nowego projektu Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Wybierz opcję **Dalej**.

1. W następnym oknie **Nowego projektu** podaj następujące informacje:

    |Właściwość |Opis |
    |---|---|
    |Project name (Nazwa projektu)|Wprowadź nazwę. Ten spacer przez `myApp`używa .|
    |Lokalizacja projektu|Wprowadź żądaną lokalizację do zapisania projektu.|
    |Zestaw SDK projektu|Jeśli jest puste, wybierz **pozycję Nowy...** i przejdź do jdk.|
    |Wersja platformy Spark|Kreator tworzenia integruje poprawną wersję dla zestawów Spark SDK i Scala SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz spark **2.x.**. W tym przykładzie używana jest wersja **Spark 2.3.0 (Scala 2.11.8)**.|

   ![Intellij Nowy projekt wybierz wersję Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Wybierz **pozycję Zakończ**. Może upłynąć kilka minut, zanim projekt stanie się dostępny. Obserwuj postęp w prawym dolnym rogu.

1. Rozwiń swój projekt i przejdź do**głównego** > **przykładu****scali** >  **src** > . Kliknij dwukrotnie **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Wykonywanie biegu lokalnego

1. W **SparkCore_WasbIOTest** skrypcie kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz opcję **Uruchom polecenie "SparkCore_WasbIOTest",** aby wykonać uruchamianie lokalne.

1. Po zakończeniu uruchamiania lokalnego można wyświetlić domyślny zapis pliku wyjściowego w bieżących **danych** > eksploratora projektu**__.__**

    ![Wynik uruchomienia lokalnego projektu Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Nasze narzędzia ustawiły domyślną konfigurację uruchamiania lokalnego automatycznie podczas wykonywania uruchamiania lokalnego i debugowania lokalnego. Otwórz konfigurację **[Iskra na HDInsight] XXX** w prawym górnym rogu, możesz zobaczyć **[Iskra na HDInsight]XXX** już utworzona w **obszarze Apache Spark on HDInsight**. Przełącz się na kartę **Uruchom lokalnie.**

    ![Intellij Uruchom konfiguracje debugowania uruchom lokalnie](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Zmienne środowiskowe:](#prerequisites)Jeśli już ustawisz zmienną środowiskową systemu **HADOOP_HOME** na **C:\WinUtils,** może automatycznie wykryć, że nie ma potrzeby ręcznego dodawania.
    - [WinUtils.exe Lokalizacja:](#prerequisites)Jeśli nie ustawiono zmiennej środowiskowej systemu, można znaleźć lokalizację, klikając jej przycisk.
    - Wystarczy wybrać jedną z dwóch opcji i nie są one potrzebne na MacOS i Linux.

1. Konfigurację można również ustawić ręcznie przed wykonaniem uruchamiania lokalnego i debugowania lokalnego. Na poprzednim zrzucie ekranu**+** wybierz znak plus ( ). Następnie wybierz opcję **Apache Spark on HDInsight.** Wprowadź informacje o **nazwie**, **Głównej nazwie klasy** do zapisania, a następnie kliknij przycisk uruchom lokalny.

## <a name="perform-local-debugging"></a>Wykonywanie lokalnego debugowania

1. Otwórz skrypt **SparkCore_wasbloTest,** ustaw punkty przerwania.

1. Kliknij prawym przyciskiem myszy edytor skryptów, a następnie wybierz opcję **Debugowanie '[Spark on HDInsight]XXX',** aby wykonać lokalne debugowanie.

## <a name="perform-remote-run"></a>Wykonywanie zdalnego uruchamiania

1. Przejdź do **uruchamiania** > **konfiguracji edycji...**. Z tego menu można utworzyć lub edytować konfiguracje do zdalnego debugowania.

1. W oknie dialogowym **Uruchamianie/Debugowanie konfiguracji** **+** wybierz znak plus ( ). Następnie wybierz opcję **Apache Spark on HDInsight.**

   ![Intellij Dodaj nową konfigurację](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Przełączanie do **uruchamiania zdalnego na karcie Klaster.** Wprowadź informacje o **nazwie,** **klastrze platformy Spark**i nazwie klasy **głównej**. Następnie kliknij pozycję **Konfiguracja zaawansowana (zdalne debugowanie)**. Nasze narzędzia obsługują debugowanie z **Executors**. **NumExectors**, wartość domyślna to 5. Lepiej nie ustawiać wyższych niż 3.

   ![Intellij Uruchom konfiguracje debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. W części **Konfiguracja zaawansowana (zdalne debugowanie)** wybierz pozycję **Włącz zdalne debugowanie platformy Spark**. Wprowadź nazwę użytkownika SSH, a następnie wprowadź hasło lub użyj pliku klucza prywatnego. Jeśli chcesz wykonać zdalne debugowanie, musisz go ustawić. Nie ma potrzeby, aby go ustawić, jeśli chcesz po prostu użyć zdalnego uruchamiania.

   ![Intellij Advanced Configuration umożliwia zdalne debugowanie iskrą](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfiguracja jest teraz zapisywana z podaną nazwą. Aby wyświetlić szczegóły konfiguracji, wybierz nazwę konfiguracji. Aby wprowadzić zmiany, wybierz pozycję **Edytuj konfiguracje**.

1. Po zakończeniu ustawień konfiguracji można uruchomić projekt względem klastra zdalnego lub wykonać zdalne debugowanie.

   ![Intellij Debug Remote Spark Job Remote Run przycisk uruchamiania zdalnego uruchomienia](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Kliknij przycisk **Rozłącz,** aby dzienniki przesyłania nie były wyświetlane w lewym panelu. Jednak nadal jest uruchomiony na wewnętrznej bazy danych.

   ![Wynik zdalnego uruchomienia zadania zdalnego uruchomienia platformy Iskra Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Wykonywanie zdalnego debugowania

1. Skonfiguruj punkty podziału, a następnie kliknij ikonę **zdalnego debugowania.** Różnica w przesyłaniu zdalnym polega na tym, że należy skonfigurować nazwę użytkownika/hasło SSH.

   ![Ikona debugowania zdalnej platformy Spark Intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Gdy wykonanie programu osiągnie punkt zerwania, w **okienku Debugera** pojawi się karta **Sterownik** i dwie karty **Executor.** Wybierz ikonę **Wznów program,** aby kontynuować uruchamianie kodu, który następnie osiągnie następny punkt przerwania. Musisz przełączyć się do poprawnej karty **Executor,** aby znaleźć docelowego wykonawcę do debugowania. Dzienniki wykonania można wyświetlić na odpowiedniej karcie **Konsola.**

   ![Karta Debugowanie zdalnej platformy Spark intellij](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Wykonywanie zdalnego debugowania i naprawiania błędów

1. Skonfiguruj dwa punkty przerwania, a następnie wybierz ikonę **debugowania,** aby rozpocząć proces zdalnego debugowania.

1. Kod zatrzymuje się w pierwszym punkcie zerwania, a informacje o parametrach i zmiennych są wyświetlane w okienku **Zmienne.**

1. Wybierz ikonę **Wznów program,** aby kontynuować. Kod zatrzymuje się w drugim punkcie. Wyjątek zostanie przechwycony zgodnie z oczekiwaniami.

   ![Intellij Debug Remote Spark Job throw error Intellij Debug Remote Spark Job throw error Intellij Debug Remote Spark Job throw error Intelli](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Ponownie wybierz ikonę **Wznów program.** W oknie **Przesyłanie platformy Spark hdinsight** jest wyświetlany błąd "niepowodzenie uruchamiania zadania".

   ![Intellij Debug Remote Spark Błąd zadania zgłaszania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Aby dynamicznie aktualizować wartość zmiennej przy użyciu funkcji debugowania IntelliJ, wybierz ponownie **opcję Debugowanie.** Okienko **Zmienne** pojawi się ponownie.

1. Kliknij prawym przyciskiem myszy obiekt docelowy na karcie **Debugowanie,** a następnie wybierz polecenie **Ustaw wartość**. Następnie wprowadź nową wartość zmiennej. Następnie wybierz pozycję **Enter,** aby zapisać wartość.

   ![Intellij Debug Remote Spark Job set value Intellij Debug Remote Spark Job set value Intellij Debug Remote Spark Job set value Intelli](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Wybierz ikonę **Wznów program,** aby kontynuować uruchamianie programu. Tym razem nie zostanie przechwycony wyjątek. Widać, że projekt działa pomyślnie bez żadnych wyjątków.

   ![Intellij Debug Remote Spark Job bez wyjątku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja

* Utwórz projekt Scala (wideo): [Tworzenie aplikacji Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Zdalne debugowanie (wideo): [używanie zestawu narzędzi Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark w klastrze USŁUGI HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z analizą biznesową: wykonywanie interaktywnej analizy danych przy użyciu funkcji Spark in HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight do analizy temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z uczeniem maszynowym: użyj iskry w hdinsight, aby przewidzieć wyniki inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie aplikacji Apache Spark dla klastra USŁUGI HDInsight za pomocą zestawu narzędzi Azure toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Użyj zestawu narzędzi Azure toolkit dla intellij do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Tworzenie aplikacji Apache Spark za pomocą narzędzi HDInsight Tools w usłudze Azure Toolkit for Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
