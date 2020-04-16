---
title: Samouczek — zestaw narzędzi platformy Azure dla intellij (aplikacja Spark)
description: Samouczek — użyj zestawu narzędzi azure dla intellij do tworzenia aplikacji Platformy Spark, które są zapisywane w Scali i przesyłać je do puli Platformy Spark Apache (wersja zapoznawcza).
services: synapse-analytics
author: v-jiche
ms.author: v-jiche
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 310dfec2bbd1bf6cb69f6293becead30487d6cbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422655"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-spark-pools-preview"></a>Samouczek: Tworzenie aplikacji Platformy Spark dla platformy Azure toolkit for IntelliJ (wersja zapoznawcza)

W tym samouczku pokazano, jak używać zestawu narzędzi Azure dla wtyczki IntelliJ do tworzenia aplikacji Apache Spark, które są zapisywane w [Scali](https://www.scala-lang.org/), a następnie przesyłać je do puli platformy Spark (wersja zapoznawcza) bezpośrednio ze zintegrowanego środowiska programistycznego IntelliJ (IDE). Wtyczkę można używać na kilka sposobów:

- Tworzenie i przesyłanie aplikacji Scala Spark na puli platformy Spark.
- Dostęp do zasobów basenów platformy Spark.
- Lokalnie twórz i uruchamiaj aplikację Scala Spark.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> - Korzystanie z zestawu narzędzi platformy Azure dla wtyczki IntelliJ
> - Tworzenie aplikacji Apache Spark
> - Przesyłanie wniosku do pul platformy Spark

## <a name="prerequisites"></a>Wymagania wstępne

- [IntelliJ IDEA Wersja społecznościowa](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Wtyczka zestawu narzędzi platformy Azure 3.27.0-2019.2 – Zainstaluj z [repozytorium wtyczek IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (wersja 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala Plugin - Zainstaluj z [repozytorium IntelliJ Plugin](/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Ten warunek wstępny jest przeznaczony tylko dla użytkowników systemu Windows.

  Podczas uruchamiania lokalnej aplikacji Spark Scala na komputerze z systemem Windows może zostać uruchomiony wyjątek, jak wyjaśniono w [spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Wyjątek występuje, ponieważ w systemie Windows brakuje programu WinUtils.exe.
  Aby rozwiązać ten problem, pobierz [plik wykonywalny WinUtils](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji, takiej jak **C:\WinUtils\bin**. Następnie dodaj zmienną środowiskową **HADOOP_HOME**i ustaw wartość zmiennej na **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Tworzenie aplikacji Spark Scala dla puli platformy Spark

1. Uruchom środowisko IntelliJ IDEA i wybierz pozycję **Create New Project** (Utwórz nowy projekt), aby otworzyć okno **New Project** (Nowy projekt).
2. Z lewego okienka wybierz **pozycję Apache Spark/HDInsight.**
3. Wybierz opcję **Spark Project with Samples(Scala)** z okna głównego.
4. Z listy rozwijanej **Narzędzia Kompilacja** wybierz jeden z następujących typów:

   - **Maven** — w celu obsługi kreatora tworzenia projektu Scala.
   - **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala.

    ![IntelliJ IDEA Nowy projekt dialogowy](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Wybierz opcję **Dalej**.
6. W oknie **New Project** (Nowy projekt) podaj następujące informacje:

    | Właściwość | Opis |
    | ----- | ----- |
    |Project name (Nazwa projektu)| Wprowadź nazwę. W tym samouczku użyto regionu `myApp`.|
    |Lokalizacja&nbsp;projektu| Wprowadź poszukiwaną lokalizację, aby zapisać projekt.|
    |Zestaw SDK projektu| Może to być puste przy pierwszym użyciu IDEA. Wybierz pozycję **New...** (Nowy...) i przejdź do swojego zestawu JDK.|
    |Wersja platformy Spark|Kreator tworzenia integruje poprawną wersję dla zestawów Spark SDK i Scala SDK. Synapse obsługuje tylko **spark 2.4.0**.|

    ![Wybieranie sdk Apache Spark](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Wybierz **pozycję Zakończ**. Może upłynąć kilka minut, zanim projekt stanie się dostępny.
8. Projekt Spark automatycznie tworzy artefakt dla Ciebie. Aby wyświetlić artefakt, wykonaj następujące czynności:

   a. Na pasku menu przejdź do pozycji Struktura projektu **pliku...** > **Project Structure...**.

   b. W oknie **Struktura projektu** wybierz pozycję **Artefakty**.

   d. Wybierz **pozycję Anuluj** po wyświetleniu artefaktu.

    ![Informacje o artefaktach w oknie dialogowym](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Znajdź **LogQuery** z **myApp** > **src** > **główny** > **przykład**> **scala**> **LogQuery**. Ten samouczek używa **LogQuery** do uruchomienia.

   ![Polecenia do tworzenia klasy Scala z projektu](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Łączenie się z pulami platformy Spark

Zaloguj się do subskrypcji platformy Azure, aby połączyć się z pulami platformy Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

1. Na pasku menu przejdź do **pozycji Wyświetl** > narzędzie**Eksploratora****Tool Windows** > Windows Azure .

   ![IntelliJ IDEA pokazuje Eksploratora Platformy Azure](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. W Eksploratorze platformy Azure kliknij prawym przyciskiem myszy węzeł **platformy Azure,** a następnie wybierz polecenie **Zaloguj się**.

   ![IntelliJ IDEA Explorer kliknij prawym przyciskiem myszy pozycję Azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. W oknie dialogowym **Logowanie do platformy Azure** wybierz pozycję **Logowanie urządzenia,** a następnie wybierz pozycję **Zaloguj**się .

    ![Logowanie na platformie Azure IntelliJ IDEA](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. W oknie dialogowym **Logowanie urządzenia platformy Azure** kliknij pozycję **Kopiuj&Otwórz**.

   ![IntelliJ IDEA azure logowania urządzenia](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. W interfejsie przeglądarki wklej kod, a następnie kliknij przycisk **Dalej**.

   ![Microsoft wprowadza okno dialogowe kodu dla hdi](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Wprowadź poświadczenia platformy Azure, a następnie zamknij przeglądarkę.

   ![Microsoft wprowadza okno dialogowe poczty e-mail dla interfejsu HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Po zalogowaniu się okno dialogowe **Wybierz subskrypcje** zawiera listę wszystkich subskrypcji platformy Azure, które są skojarzone z poświadczeniami. Wybierz subskrypcję, a następnie kliknij przycisk **Wybierz**.

    ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Z **Usługi Azure Explorer**rozwiń **apache spark na Synapse,** aby wyświetlić obszary robocze, które znajdują się w subskrypcjach.

    ![Widok główny IntelliJ IDEA Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Aby wyświetlić pulę platformy Spark, można dalej rozszerzać obszar roboczy.

    ![Konta magazynu usługi Azure Explorer](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Zdalne uruchamianie aplikacji Spark Scala w puli platformy Spark

Po utworzeniu aplikacji Scala można ją zdalnie uruchomić.

1. Otwórz okno **Konfiguracje uruchamiania/debugowania,** klikając ikonę.

    ![Polecenie Prześlij aplikację iskrę do usługi HDInsight](./media/intellij-tool-synapse/open-configuration-window.png)

2. W oknie dialogowym **Uruchamianie/Debugowanie konfiguracji** kliknij przycisk **+**, a następnie wybierz polecenie **Apache Spark on Synapse**.

    ![Polecenie Prześlij aplikację iskrę do usługi HDInsight](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. W oknie **Uruchom/Debugowanie konfiguracji** podaj następujące wartości, a następnie wybierz przycisk **OK:**

    |Właściwość |Wartość |
    |----|----|
    |Baseny iskrowe|Wybierz pule platformy Spark, na których chcesz uruchomić aplikację.|
    |Wybierz artefakt do przesłania|Pozostaw ustawienie domyślne.|
    |Nazwa głównej klasy|Wartością domyślną jest klasa główna z wybranego pliku. Klasę można zmienić, wybierając wielokropek(**...**) i wybierając inną klasę.|
    |Konfiguracje zadań|Można zmienić domyślny klucz i wartości. Aby uzyskać więcej informacji, zobacz [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumenty wiersza polecenia|W razie potrzeby można wprowadzić argumenty oddzielone spacją dla klasy głównej.|
    |Słoiki odniesienia i pliki odniesienia|Można wprowadzić ścieżki dla smąków i plików, do których istnieją odwołania. Można również przeglądać pliki w wirtualnym systemie plików platformy Azure, który obecnie obsługuje tylko klaster ADLS Gen 2. Aby uzyskać więcej informacji: [Konfiguracja platformy Spark apache](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) i [sposób przekazywania zasobów do klastra](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Miejsce do przekazywania zadań|Rozwiń, aby wyświetlić dodatkowe opcje.|
    |Typ magazynu|Wybierz **pozycję Użyj obiektu blob platformy Azure, aby przekazać** je z listy rozwijanej.|
    |Konto magazynu|Wprowadź swoje konto magazynu.|
    |Klucz magazynu|Wprowadź klucz pamięci masowej.|
    |Kontener do przechowywania|Wybierz kontener magazynu z listy rozwijanej po wprowadzeniu **konta magazynu** i **klucza magazynu.**|

    ![Okno dialogowe Przesyłanie platformy Spark](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Kliknij ikonę **SparkJobRun,** aby przesłać projekt do wybranej puli platformy Spark. Na karcie **Zdalne zadanie iskrzenie w klastrze** jest wyświetlany postęp wykonywania zadania u dołu. Aplikację można zatrzymać, klikając czerwony przycisk.

    ![Apache Spark Okno składania](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Okno dialogowe Przesyłanie platformy Spark](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Lokalne aplikacje Run/Debug Apache Spark

Możesz wykonać poniższe instrukcje, aby skonfigurować lokalne uruchamianie i lokalne debugowanie dla zadania Apache Spark.

### <a name="scenario-1-do-local-run"></a>Scenariusz 1: Wykonaj uruchamianie lokalne

1. Otwórz okno dialogowe **Uruchom/Debugowanie konfiguracji,** wybierz znak plus (**+**). Następnie wybierz opcję **Apache Spark on Synapse.Then select the Apache Spark on Synapse** option. Wprowadź informacje o **nazwie**, **Głównej nazwie klasy** do zapisania.

    ![Intellij Uruchom konfiguracje debugowania uruchom lokalnie](./media/intellij-tool-synapse/local-run-synapse.png)

    - Zmienne środowiskowe i Lokalizacja programu WinUtils.exe są przeznaczone tylko dla użytkowników systemu Windows.
    - Zmienne środowiskowe: Systemowa zmienna środowiskowa może być wykrywana automatycznie, jeśli została ustawiona wcześniej i nie ma potrzeby ręcznego dodawania.
    - [WinUtils.exe Lokalizacja:](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)Można określić lokalizację WinUtils, klikając ikonę folderu po prawej stronie.

2. Następnie kliknij lokalny przycisk odtwarzania.

    ![Intellij Uruchom konfiguracje debugowania uruchom lokalnie](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Po zakończeniu uruchamiania lokalnego, jeśli skrypt zawiera dane wyjściowe, można sprawdzić plik wyjściowy z **danych** > **__domyślnych__**.

    ![Wynik uruchomienia lokalnego projektu Intellij](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scenariusz 2: Wykonaj debugowanie lokalne

1. Otwórz skrypt **LogQuery,** ustaw punkty przerwania.
2. Kliknij **ikonę lokalnego debugowania,** aby wykonać lokalne debugowanie.

    ![Wynik uruchomienia lokalnego projektu Intellij](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Dostęp do obszaru roboczego Synapse i zarządzanie nim

Można wykonywać różne operacje w Eksploratorze Platformy Azure w ramach narzędzia Azure Toolkit dla intellij. Na pasku menu przejdź do **pozycji Wyświetl** > narzędzie**Eksploratora****Tool Windows** > Windows Azure .

### <a name="launch-workspace"></a>Uruchamianie obszaru roboczego

1. Z Usługi Azure Explorer przejdź do **Apache Spark w Synapse**, a następnie rozwiń ją.

    ![Widok główny IntelliJ IDEA Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Kliknij prawym przyciskiem myszy obszar roboczy, a następnie wybierz pozycję **Uruchom obszar roboczy**, zostanie otwarta witryna sieci Web.

    ![Szczegóły aplikacji spark Job View](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Szczegóły aplikacji spark Job View](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Konsola iskra

Możesz uruchomić platformę Spark Local Console (Scala) lub uruchomić interaktywną konsolę sesji Spark Livy (Scala).

### <a name="spark-local-console-scala"></a>Platforma spark, konsola lokalna (Scala)

Upewnij się, że jesteś zadowolony z WINUTILS. Warunek wstępny EXE.

1. Na pasku menu przejdź do **pozycji Uruchom** > **konfiguracje edycji...**.
2. W oknie **Uruchom/Debugowanie konfiguracji** w lewym okienku przejdź do **aplikacji Apache Spark on Synapse** > **[Spark on Synapse] myApp**.
3. W oknie głównym wybierz kartę **Uruchom lokalnie.**
4. Podaj następujące wartości, a następnie wybierz **przycisk OK:**

    |Właściwość |Wartość |
    |----|----|
    |Zmienne środowiskowe|Upewnij się, że wartość HADOOP_HOME jest poprawna.|
    |Lokalizacja winutils.exe|Upewnij się, że ścieżka jest poprawna.|

    ![Konfiguracja zestawu konsoli lokalnej](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Z projektu przejdź do **myApp** > **src** > **main** > **scala** > **myApp**.
6. Na pasku menu przejdź do pozycji **Narzędzia** > **Platformy Spark konsoli** > **Run Spark Local Console (Scala)**.
7. Następnie mogą być wyświetlane dwa okna dialogowe z pytaniem, czy chcesz automatycznie naprawić zależności. Jeśli tak, wybierz **opcję Automatyczne naprawianie**.

    ![IntelliJ IDEA Spark Auto Fix dialog1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark Auto Fix dialog2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. Konsola powinna wyglądać podobnie do poniższego zdjęcia. W oknie konsoli `sc.appName`wpisz , a następnie naciśnij klawisze ctrl+Enter. Wynik zostanie wyświetlony. Możesz zatrzymać konsolę lokalną, klikając czerwony przycisk.

    ![Wynik konsoli lokalnej IntelliJ IDEA](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Interaktywna konsola sesji Spark Livy (Scala)

Jest obsługiwany tylko na IntelliJ 2018.2 i 2018.3.

1. Na pasku menu przejdź do **pozycji Uruchom** > **konfiguracje edycji...**.

2. W oknie **Uruchom/Debugowanie konfiguracji** w lewym okienku przejdź do **aplikacji Apache Spark on synapse** > **[Spark on synapse] myApp**.

3. W oknie głównym wybierz pozycję **Remotely Run in Cluster** tab.From the main window, select the Remotely Run in Cluster tab.

4. Podaj następujące wartości, a następnie wybierz **przycisk OK:**

    |Właściwość |Wartość |
    |----|----|
    |Baseny iskrowe|Wybierz pule platformy Spark, na których chcesz uruchomić aplikację.|
    ||

    ![Konfiguracja zestawu konsoli interaktywnej](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Z projektu przejdź do **myApp** > **src** > **main** > **scala** > **myApp**.

6. Na pasku menu przejdź do**konsoli** >  **Narzędzia** > Spark**Run Spark Livy Interactive Session Console (Scala).**
7. Konsola powinna wyglądać podobnie do poniższego zdjęcia. W oknie konsoli `sc.appName`wpisz , a następnie naciśnij klawisze ctrl+Enter. Wynik zostanie wyświetlony. Możesz zatrzymać konsolę lokalną, klikając czerwony przycisk.

    ![IntelliJ IDEA Interaktywny wynik konsoli](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Wyślij zaznaczenie do konsoli Spark

Wygodnie jest przewidzieć wynik skryptu, wysyłając kod do konsoli lokalnej lub konsoli sesji interaktywnej Livy (Scala). Możesz wyróżnić kod w pliku Scala, a następnie kliknąć prawym przyciskiem myszy **konsolę Wyślij zaznaczenie do platformy Spark**. Wybrany kod zostanie wysłany do konsoli i zostanie wykonany. Wynik zostanie wyświetlony po kodzie w konsoli. Konsola sprawdzi błędy, jeśli istnieją.

   ![Wyślij zaznaczenie do konsoli Spark](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](../overview-what-is.md)
- [Tworzenie nowej puli platformy Spark apache dla obszaru roboczego usługi Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md)
