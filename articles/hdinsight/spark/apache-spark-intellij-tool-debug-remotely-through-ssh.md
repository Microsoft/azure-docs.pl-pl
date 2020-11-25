---
title: 'Azure Toolkit for IntelliJ: debugowanie aplikacji Spark przy użyciu protokołu SSH-HDInsight'
description: Wskazówki krok po kroku dotyczące korzystania z narzędzi usługi HDInsight w Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji w klastrach usługi HDInsight za pośrednictwem protokołu SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 421993ac4aaba551b6fcbd002783d44559ce377d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995339"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Debugowanie Apache Spark aplikacji w klastrze usługi HDInsight z Azure Toolkit for IntelliJ za pośrednictwem protokołu SSH

Ten artykuł zawiera wskazówki krok po kroku dotyczące korzystania z narzędzi usługi HDInsight w [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) do zdalnego debugowania aplikacji w klastrze usługi HDInsight. Aby debugować projekt, możesz również wyświetlić [aplikacje do debugowania usługi HDInsight Spark za pomocą Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) wideo.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Zobacz [Tworzenie klastra Apache Spark](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Użytkownicy systemu Windows: podczas korzystania z lokalnej aplikacji platformy Spark Scala na komputerze z systemem Windows może wystąpić wyjątek, zgodnie z opisem w [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Wyjątek występuje z powodu braku WinUtils.exe w systemie Windows.

    Aby rozwiązać ten problem, Pobierz [Winutils.exe](https://github.com/steveloughran/winutils) do lokalizacji takiej jak **C:\WinUtils\bin**. Następnie Dodaj zmienną środowiskową **HADOOP_HOME** i ustaw wartość zmiennej na **C:\WinUtils**.

* [Pomysł IntelliJ](https://www.jetbrains.com/idea/download/#section=windows) (wersja Community jest bezpłatna).

* [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [Wtyczka Scala dla IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Tworzenie aplikacji platformy Spark Scala

1. Uruchom środowisko IntelliJ IDEA i wybierz pozycję **Create New Project** (Utwórz nowy projekt), aby otworzyć okno **New Project** (Nowy projekt).

1. W lewym okienku wybierz pozycję **Apache Spark/HDInsight** .

1. Wybierz **projekt Spark z przykładami (Scala)** z okna głównego.

1. Z listy rozwijanej **Build tool** (Narzędzie kompilacji), wybierz jedną z następujących pozycji:

    * **Maven** — w celu obsługi kreatora tworzenia projektu Scala.
    * **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala.

     ![IntelliJ Utwórz nowy projekt Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Wybierz pozycję **Dalej**.

1. W następnym oknie **Nowy projekt** podaj następujące informacje:

    |Właściwość |Opis |
    |---|---|
    |Project name (Nazwa projektu)|Wprowadź nazwę. W tym instruktażu opisano użycie `myApp` .|
    |Lokalizacja projektu|Wprowadź żądaną lokalizację do zapisania projektu.|
    |Zestaw SDK projektu|Jeśli to pole jest puste, wybierz pozycję **Nowy...** i przejdź do JDK.|
    |Wersja platformy Spark|Kreator tworzenia integruje poprawną wersję dla zestawów Spark SDK i Scala SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz pozycję **Spark 2. x.**. W tym przykładzie używana jest wersja **Spark 2.3.0 (Scala 2.11.8)**.|

   ![IntelliJ nowy projekt wybierz wersję platformy Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Wybierz pozycję **Zakończ**. Udostępnienie projektu może potrwać kilka minut. Obejrzyj w prawym dolnym rogu, aby postępować.

1. Rozwiń projekt i przejdź do **src**  >  **main**  >  **scala**  >  **przykładu** src Main Scala. Kliknij dwukrotnie **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Wykonaj przebieg lokalny

1. W skrypcie **SparkCore_WasbIOTest** kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz opcję **Uruchom "SparkCore_WasbIOTest"** , aby wykonać przebieg lokalny.

1. Po zakończeniu lokalnego uruchomienia można zobaczyć plik wyjściowy Zapisz w bieżącym domyślnym **danych** Eksploratora projektu  >  **__default__**.

    ![Wynik lokalnego uruchomienia projektu IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Nasze narzędzia ustawili domyślną konfigurację uruchamiania lokalnego podczas przeprowadzania lokalnego uruchamiania i debugowania lokalnego. Otwórz konfigurację **[Spark w usłudze HDInsight] XXX** w prawym górnym rogu, zobaczysz, że **[Spark w usłudze HDInsight] XXX** już utworzone w obszarze **Apache Spark w usłudze HDInsight**. Przejdź do karty **uruchamiania lokalnego** .

    ![IntelliJ uruchamianie konfiguracji debugowania — uruchomienie lokalne](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Zmienne środowiskowe](#prerequisites): Jeśli ustawisz już zmienną środowiskową system **HADOOP_HOME** na **C:\WinUtils**, może ona automatycznie wykryć, że nie trzeba ręcznie dodawać.
    - [WinUtils.exe lokalizacja](#prerequisites): Jeśli nie ustawisz zmiennej środowiskowej systemowej, możesz znaleźć tę lokalizację, klikając jej przycisk.
    - Po prostu wybierz jedną z dwóch opcji i nie są one potrzebne w systemach MacOS i Linux.

1. Konfigurację można również ustawić ręcznie przed wykonaniem operacji lokalnego uruchamiania i debugowania lokalnego. Na poprzednim zrzucie ekranu wybierz znak plus ( **+** ). Następnie wybierz opcję **Apache Spark w usłudze HDInsight** . Wprowadź informacje w polu **Nazwa**, **Nazwa klasy głównej** do zapisania, a następnie kliknij przycisk Uruchom lokalnie.

## <a name="perform-local-debugging"></a>Wykonaj debugowanie lokalne

1. Otwórz skrypt **SparkCore_wasbloTest** , ustaw punkty przerwania.

1. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz opcję **Debuguj "[Spark w usłudze HDInsight] XXX"** , aby przeprowadzić debugowanie lokalne.

## <a name="perform-remote-run"></a>Wykonaj przebieg zdalny

1. Przejdź do opcji **Uruchom**  >  **Edytowanie konfiguracji..**.. Z tego menu można utworzyć lub edytować konfiguracje zdalnego debugowania.

1. W oknie dialogowym **konfiguracje uruchamiania/debugowania** wybierz znak plus ( **+** ). Następnie wybierz opcję **Apache Spark w usłudze HDInsight** .

   ![IntelliJ Dodaj nową konfigurację](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Przełącz się do **zdalnego uruchamiania na karcie klaster** . Wprowadź informacje o **nazwie**, **klastrze Spark** i **nazwie klasy głównej**. Następnie kliknij pozycję **Konfiguracja zaawansowana (debugowanie zdalne)**. Nasze narzędzia obsługują debugowanie za pomocą **modułów wykonujących**. **NumExectors** wartość domyślna to 5. Lepiej nie ustawiono wartości większej niż 3.

   ![IntelliJ uruchamiaj konfiguracje debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. W części **Konfiguracja zaawansowana (debugowanie zdalne)** wybierz pozycję **Włącz debugowanie zdalne Spark**. Wprowadź nazwę użytkownika SSH, a następnie wprowadź hasło lub użyj pliku klucza prywatnego. Jeśli chcesz przeprowadzić debugowanie zdalne, musisz je ustawić. Nie trzeba ustawiać go, jeśli chcesz tylko korzystać z zdalnego uruchomienia.

   ![IntelliJ zaawansowaną konfigurację Włącz debugowanie zdalne Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfiguracja jest teraz zapisywana o podanej nazwie. Aby wyświetlić szczegóły konfiguracji, wybierz nazwę konfiguracji. Aby wprowadzić zmiany, wybierz pozycję **Edytuj konfiguracje**.

1. Po zakończeniu ustawień konfiguracji można uruchomić projekt względem klastra zdalnego lub wykonać zdalne debugowanie.

   ![Zdalny przycisk uruchamiania zdalnego zadania Spark IntelliJ debugowania](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Kliknij przycisk **Rozłącz** , aby dzienniki przesłane nie były wyświetlane w lewym panelu. Jednak nadal działa w zapleczu.

   ![Wynik zdalnego uruchomienia zdalnego zadania Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Wykonaj debugowanie zdalne

1. Skonfiguruj punkty przerwania, a następnie kliknij ikonę **debugowania zdalnego** . Różnica polega na tym, że należy skonfigurować nazwę użytkownika/hasło SSH.

   ![Ikona debugowania zdalnego zadania platformy Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Gdy wykonanie programu dociera do punktu przerwania, zobaczysz kartę **sterownika** i dwie karty **wykonawcze** w okienku **debuger** . Wybierz ikonę **Wznów program** , aby kontynuować uruchamianie kodu, który następnie osiągnie następny punkt przerwania. Aby znaleźć docelowy moduł wykonujący debugowanie, należy przełączyć się na poprawną kartę programu **wykonującego** . Dzienniki wykonywania można wyświetlić na odpowiedniej karcie **konsoli** .

   ![Karta debugowania zdalnego zadania platformy Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Wykonaj debugowanie zdalne i naprawianie błędów

1. Skonfiguruj dwa punkty przerwania, a następnie wybierz ikonę **debugowania** , aby rozpocząć proces zdalnego debugowania.

1. Kod zostaje zatrzymany w pierwszym punkcie przerwania, a informacje o parametrze i zmiennej są wyświetlane w okienku **zmienne** .

1. Wybierz ikonę **Wznów program** , aby kontynuować. Kod zostaje zatrzymany w drugim punkcie. Wyjątek jest przechwytywany zgodnie z oczekiwaniami.

   ![Błąd rzutowania zdalnego zadania Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Wybierz ponownie ikonę **programu Resume** . W oknie **przesyłanie usługi HDInsight Spark** zostanie wyświetlony komunikat o błędzie "uruchomienie zadania nie powiodło się".

   ![Błąd podczas wysyłania zdalnego zadania Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Aby dynamicznie zaktualizować wartość zmiennej przy użyciu funkcji debugowania IntelliJ, należy ponownie wybrać **Debuguj** . Zostanie wyświetlone okienko **zmienne** .

1. Kliknij prawym przyciskiem myszy obiekt docelowy na karcie **debugowanie** , a następnie wybierz pozycję **Ustaw wartość**. Następnie wprowadź nową wartość dla zmiennej. Następnie wybierz klawisz **Enter** , aby zapisać wartość.

   ![Wartość zestawu zadań zdalnej Spark dla debugowania IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Wybierz ikonę **Wznów program** , aby kontynuować uruchamianie programu. Tym razem nie jest przechwytywany żaden wyjątek. Można zobaczyć, że projekt zostanie uruchomiony pomyślnie bez żadnych wyjątków.

   ![Debuguj zdalne zadanie Spark IntelliJ debugowania bez wyjątku](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja

* Tworzenie projektu Scala (wideo): [tworzenie Apache Spark Scala aplikacji](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Debugowanie zdalne (wideo): [użyj Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark w klastrze usługi HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z usługą BI: wykonywanie interakcyjnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](./apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Tworzenie aplikacji Apache Spark przy użyciu narzędzi usługi HDInsight w Azure Toolkit for Eclipse](./apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notesu Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)