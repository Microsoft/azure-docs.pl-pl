---
title: Debugowanie zadania platformy Spark za pomocą zestawu narzędzi platformy Azure IntelliJ (wersja zapoznawcza) — HDInsight
description: Wskazówki dotyczące korzystania z narzędzi usługi HDInsight w Azure Toolkit for IntelliJ do debugowania aplikacji
keywords: debugowanie zdalne IntelliJ, debugowanie zdalne IntelliJ, SSH, IntelliJ, HDInsight, debugowanie IntelliJ, debugowanie
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5abbb26e7582d8ddabb73f3a178cf6d87e24db21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942536"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Błąd debugowania zadań platformy Spark z użyciem Azure Toolkit for IntelliJ (wersja zapoznawcza)

Ten artykuł zawiera wskazówki krok po kroku dotyczące korzystania z narzędzi usługi HDInsight w [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) do uruchamiania aplikacji **debugowania błędów usługi Spark** .

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Oracle Java Development](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). W tym samouczku jest używany język Java w wersji 8.0.202.
  
* IntelliJ pomysł. W tym artykule [jest stosowany pomysł IntelliJ Community Ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Zobacz [Installing the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation) (Instalowanie zestawu Azure Toolkit for IntelliJ).

* Nawiąż połączenie z klastrem usługi HDInsight. Zobacz [nawiązywanie połączenia z klastrem usługi HDInsight](apache-spark-intellij-tool-plugin.md).

* Eksplorator usługi Microsoft Azure Storage. Zobacz [pobieranie Eksplorator usługi Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Utwórz projekt z szablonem debugowania

Utwórz projekt Spark 2.3.2, aby kontynuować debugowanie niepowodzeń, należy pobrać przykładowy plik debugowania zadania błędu w tym dokumencie.

1. Otwórz środowisko IntelliJ IDEA. Otwórz okno **Nowy projekt** .

   a. Wybierz pozycję **Azure Spark/HDInsight** w okienku po lewej stronie.

   b. Wybierz **projekt platformy Spark z przykładem debugowania zadania błędu (wersja zapoznawcza) (Scala)** z okna głównego.

     ![IntelliJ Tworzenie projektu debugowania](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Wybierz pozycję **Next** (Dalej).

2. W oknie **Nowy projekt** wykonaj następujące czynności:

   ![IntelliJ nowy projekt wybierz wersję platformy Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Wprowadź nazwę projektu i lokalizację projektu.

   b. Z listy rozwijanej **zestaw SDK projektu** wybierz pozycję **Java 1,8** dla klastra **Spark** .

   c. Z listy rozwijanej **wersja platformy Spark** wybierz pozycję **Spark 2.3.2 (Scala 2.11.8)**.

   d. Wybierz pozycję **Zakończ**.

3. Wybierz pozycję **src**  >  **Main**  >  **Scala** , aby otworzyć swój kod w projekcie. Ten przykład używa skryptu **AgeMean_Div ()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Uruchamianie aplikacji platformy Spark Scala/Java w klastrze usługi HDInsight

Utwórz aplikację platformy Spark Scala/Java, a następnie uruchom aplikację w klastrze Spark, wykonując następujące czynności:

1. Kliknij pozycję **Dodaj konfigurację** , aby otworzyć okno **konfiguracje uruchamiania/debugowania** .

   ![HDI IntelliJ Dodaj konfigurację](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. W oknie dialogowym **konfiguracje uruchamiania/debugowania** wybierz znak plus ( **+** ). Następnie wybierz opcję **Apache Spark w usłudze HDInsight** .

   ![IntelliJ Dodaj nową konfigurację](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Przełącz się do **zdalnego uruchamiania na karcie klaster** . Wprowadź informacje o **nazwie**, **klastrze Spark** i **nazwie klasy głównej**. Nasze narzędzia obsługują debugowanie za pomocą **modułów wykonujących**. **NumExectors**, wartość domyślna to 5, a lepiej nie ustawić wyższej niż 3. Aby skrócić czas wykonywania, można dodać do **konfiguracji zadania** platformę **Spark. przędzy. maxAppAttempts** i ustawić wartość 1. Kliknij przycisk **OK** , aby zapisać konfigurację.

   ![IntelliJ uruchamiaj konfiguracje debugowania nowe](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Konfiguracja jest teraz zapisywana o podanej nazwie. Aby wyświetlić szczegóły konfiguracji, wybierz nazwę konfiguracji. Aby wprowadzić zmiany, wybierz pozycję **Edytuj konfiguracje**.

5. Po zakończeniu ustawień konfiguracji można uruchomić projekt względem klastra zdalnego.

   ![Zdalny przycisk uruchamiania zdalnego zadania Spark IntelliJ debugowania](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Identyfikator aplikacji można sprawdzić w oknie danych wyjściowych.

   ![Wynik zdalnego uruchomienia zdalnego zadania Spark debugowania IntelliJ](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Pobieranie profilu zadania zakończonego niepowodzeniem

Jeśli przesyłanie zadania nie powiedzie się, można pobrać profil zadania zakończonego niepowodzeniem na komputer lokalny w celu dalszej debugowania.

1. Otwórz **Eksplorator usługi Microsoft Azure Storage**, Znajdź konto usługi HDInsight dla klastra dla zadania zakończonego niepowodzeniem, Pobierz zasoby zakończone niepowodzeniem z odpowiedniej lokalizacji: **\hdp\spark2-Events \\ . Spark — błędy \\ \<application ID>** do folderu lokalnego. W oknie **działania** zostanie wyświetlony postęp pobierania.

   ![Niepowodzenie pobierania Eksplorator usługi Azure Storage](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Pobieranie Eksplorator usługi Azure Storage powiodło się](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Konfigurowanie lokalnego środowiska debugowania i debugowanie w przypadku niepowodzenia

1. Otwórz oryginalny projekt lub Utwórz nowy projekt i skojarz go z oryginalnym kodem źródłowym. Dla debugowania błędów jest obecnie obsługiwana tylko wersja platformy Spark 2.3.2.

1. W IntelliJ POMYSŁu Utwórz plik konfiguracji **debugowania błędów usługi Spark** , wybierz plik FTD z wcześniej pobranych zasobów zadań zakończonych niepowodzeniem dla pola **Lokalizacja kontekstu niepowodzenia zadania platformy Spark** .

   ![Konfiguracja niepowodzeń Crete](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Kliknij przycisk Uruchom lokalnie na pasku narzędzi, a w oknie uruchamiania zostanie wyświetlony komunikat o błędzie.

   ![Run-Failure-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Run-Failure-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Ustaw punkt przerwania w postaci dziennika, a następnie kliknij przycisk Debugowanie lokalne, aby przeprowadzić lokalne debugowanie tak jak normalne projekty Scala/Java w IntelliJ.

1. Po zakończeniu debugowania, jeśli projekt zostanie zakończony pomyślnie, można ponownie przesłać zadanie zakończone niepowodzeniem do platformy Spark w klastrze usługi HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Następne kroki

* [Przegląd: debugowanie aplikacji Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demonstracja

* Tworzenie projektu Scala (wideo): [tworzenie Apache Spark Scala aplikacji](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Debugowanie zdalne (wideo): [użyj Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark w klastrze usługi HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z usługą BI: wykonaj interaktywną analizę danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](./apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z narzędzi HDInsight Tools for IntelliJ z piaskownicą Hortonworks](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Tworzenie aplikacji Apache Spark przy użyciu narzędzi usługi HDInsight w Azure Toolkit for Eclipse](./apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla Jupyter Notebook w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)