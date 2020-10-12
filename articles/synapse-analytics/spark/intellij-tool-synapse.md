---
title: Samouczek — Azure Toolkit for IntelliJ (aplikacja Spark)
description: Samouczek — Użyj Azure Toolkit for IntelliJ do opracowania aplikacji platformy Spark, które są zapisywane w Scala, i prześlij je do puli Apache Spark (wersja zapoznawcza).
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: c17addc313954fbca5b81c4594d7317065350c09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91249573"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Samouczek: Tworzenie aplikacji Apache Spark za pomocą IntelliJ przy użyciu obszaru roboczego Synapse

W tym samouczku pokazano, jak za pomocą wtyczki Azure Toolkit for IntelliJ tworzyć aplikacje Apache Spark, które są zapisywane w [Scala](https://www.scala-lang.org/), a następnie przesyłać je do puli platformy Spark (wersja zapoznawcza) bezpośrednio z poziomu zintegrowanego środowiska projektowego INTELLIJ (IDE). Możesz użyć wtyczki na kilka sposobów:

- Tworzenie i przesyłanie aplikacji Scala Spark w puli platformy Spark.
- Uzyskaj dostęp do zasobów pul platformy Spark.
- Tworzenie i uruchamianie aplikacji Scala Spark lokalnie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> - Korzystanie z wtyczki Azure Toolkit for IntelliJ
> - Opracowywanie aplikacji Apache Spark
> - Przesyłanie aplikacji do pul platformy Spark

## <a name="prerequisites"></a>Wymagania wstępne

- [INTELLIJ pomysł](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Wtyczka zestawu Azure Toolkit 3.27.0-2019.2 — Instalacja z [repozytorium wtyczki IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (wersja 1,8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Wtyczka Scala — Zainstaluj z [repozytorium wtyczki IntelliJ](/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea).
- Następujące wymagania wstępne są przeznaczone tylko dla użytkowników systemu Windows:

  Podczas uruchamiania lokalnej aplikacji platformy Spark Scala na komputerze z systemem Windows może wystąpić wyjątek, zgodnie z opisem w platformie [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Wyjątek występuje z powodu braku WinUtils.exe w systemie Windows.
  Aby rozwiązać ten problem, Pobierz [plik wykonywalny WinUtils](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do lokalizacji takiej jak **C:\WinUtils\bin**. Następnie Dodaj zmienną środowiskową **HADOOP_HOME**i ustaw wartość zmiennej na **C:\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Tworzenie aplikacji Spark Scala dla puli platformy Spark

1. Uruchom środowisko IntelliJ IDEA i wybierz pozycję **Create New Project** (Utwórz nowy projekt), aby otworzyć okno **New Project** (Nowy projekt).
2. W lewym okienku wybierz pozycję **Apache Spark/HDInsight** .
3. Wybierz **projekt Spark z przykładami (Scala)** z okna głównego.
4. Z listy rozwijanej **Narzędzie kompilacji** wybierz jeden z następujących typów:

   - **Maven** — w celu obsługi kreatora tworzenia projektu Scala.
   - **SBT** — na potrzeby zarządzania zależnościami i kompilacji projektu Scala.

    ![Okno dialogowe Nowy projekt IntelliJ pomysłów](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Wybierz opcję **Dalej**.
6. W oknie **New Project** (Nowy projekt) podaj następujące informacje:

    | Właściwość | Opis |
    | ----- | ----- |
    |Project name (Nazwa projektu)| Wprowadź nazwę. W tym samouczku użyto regionu `myApp`.|
    |Lokalizacja&nbsp;projektu| Wprowadź żądaną lokalizację, aby zapisać projekt.|
    |Zestaw SDK projektu| Może ona być pusta przy pierwszym użyciu POMYSŁu. Wybierz pozycję **New...** (Nowy...) i przejdź do swojego zestawu JDK.|
    |Wersja platformy Spark|Kreator tworzenia integruje poprawną wersję dla zestawów Spark SDK i Scala SDK. Synapse obsługuje tylko platformę **Spark 2.4.0**.|
    |||

    ![Wybieranie zestawu SDK Apache Spark](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Wybierz pozycję **Zakończ**. Udostępnienie projektu może potrwać kilka minut.
8. Projekt Spark automatycznie tworzy artefakt. Aby wyświetlić artefakt, wykonaj następujące czynności:

   a. Na pasku menu Przejdź do **File**  >  **struktury projektu pliku..**..

   b. W oknie **Struktura projektu** wybierz pozycję **artefakty**.

   c. Po wyświetleniu artefaktu wybierz pozycję **Anuluj** .

    ![Informacje o artefaktach w oknie dialogowym](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Znajdź **LogQuery** z usługi **MojaApl**  >  **src**  >  **Main**  >  **Scala** >  **Sample** >  **LogQuery**. W tym samouczku do uruchomienia programu **LogQuery** .

   ![Polecenia służące do tworzenia klasy Scala z projektu](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Nawiązywanie połączenia z pulami platformy Spark

Zaloguj się do subskrypcji platformy Azure, aby nawiązać połączenie z pulami platformy Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

1. Na pasku menu Przejdź do **widoku**  >  **Narzędzia**  >  **Eksplorator platformy Microsoft Azure**.

   ![IntelliJ pomysł przedstawia Eksplorator platformy Azure](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. W programie Azure Explorer kliknij prawym przyciskiem myszy węzeł **Azure** , a następnie wybierz pozycję **Zaloguj się**.

   ![IntelliJ pomysł Explorer kliknij prawym przyciskiem myszy pozycję Azure](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. W oknie dialogowym **Logowanie do platformy Azure** wybierz pozycję **Logowanie do urządzenia**, a następnie wybierz pozycję **Zaloguj**.

    ![IntelliJ pomysł platformy Azure](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. W oknie dialogowym **Logowanie do urządzenia Azure** wybierz pozycję **Kopiuj&Otwórz**.

   ![IntelliJ pomysł na logowanie do urządzenia Azure](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. W interfejsie przeglądarki Wklej kod, a następnie wybierz przycisk **dalej**.

   ![Wprowadzenie do okna dialogowego kodu dla HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Wprowadź swoje poświadczenia platformy Azure, a następnie zamknij przeglądarkę.

   ![Microsoft wprowadza do okna dialogowego poczty e-mail dla HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Po zalogowaniu się w oknie dialogowym **Wybieranie subskrypcji** zostaną wyświetlone wszystkie subskrypcje platformy Azure skojarzone z poświadczeniami. Wybierz swoją subskrypcję, a następnie wybierz pozycję **Wybierz**.

    ![Okno dialogowe Select Subscriptions (Wybieranie subskrypcji)](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. W **Eksploratorze Azure**rozwiń węzeł **Apache Spark na Synapse** , aby wyświetlić obszary robocze, które znajdują się w Twoich subskrypcjach.

    ![Widok główny Eksploratora Azure IntelliJ pomysł](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Aby wyświetlić pule platformy Spark, można rozszerzyć obszar roboczy.

    ![Konta usługi Azure Explorer Storage](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Zdalne uruchamianie aplikacji platformy Spark Scala w puli platformy Spark

Po utworzeniu aplikacji Scala można uruchomić ją zdalnie.

1. Otwórz okno **konfiguracje uruchamiania/debugowania** , wybierając ikonę.

    ![Polecenie przesłania aplikacji platformy Spark do usługi HDInsight 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. W oknie dialogowym **konfiguracje uruchamiania/debugowania** wybierz pozycję **+** , a następnie wybierz pozycję **Apache Spark w Synapse**.

    ![Przesyłanie aplikacji platformy Spark do usługi HDInsight — polecenie 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. W oknie **konfiguracje uruchamiania/debugowania** podaj następujące wartości, a następnie wybierz przycisk **OK**:

    |Właściwość |Wartość |
    |----|----|
    |Pule platformy Spark|Wybierz pule platformy Spark, na których chcesz uruchomić aplikację.|
    |Wybierz artefakt do przesłania|Pozostaw ustawienie domyślne.|
    |Nazwa klasy głównej|Wartością domyślną jest główna Klasa z wybranego pliku. Możesz zmienić klasę, wybierając wielokropek (**...**) i wybierając inną klasę.|
    |Konfiguracje zadań|Można zmienić domyślny klucz i wartości. Aby uzyskać więcej informacji, zobacz [interfejs API REST usługi Apache usługi Livy](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumenty wiersza polecenia|W razie potrzeby można wprowadzać argumenty rozdzielone spacją dla klasy głównej.|
    |Odwołania do Jars i plików, do których istnieją odwołania|Możesz wprowadzić ścieżki do przywoływanych Jars i plików, jeśli istnieją. Można również przeglądać pliki w wirtualnym systemie plików platformy Azure, który obecnie obsługuje tylko ADLS Gen2 klaster. Aby uzyskać więcej informacji: [Apache Spark konfiguracja](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) i [sposób przekazywania zasobów do klastra](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Magazyn przekazywania zadań|Rozwiń, aby wyświetlić dodatkowe opcje.|
    |Typ magazynu|Wybierz pozycję **Użyj obiektu blob platformy Azure, aby przekazać** lub **użyć domyślnego konta magazynu klastra do przekazania** z listy rozwijanej.|
    |Konto magazynu|Wprowadź konto magazynu.|
    |Klucz magazynu|Wprowadź klucz magazynu.|
    |Kontener magazynu|Z listy rozwijanej wybierz kontener magazynu, po wprowadzeniu **konta magazynu** i **klucza magazynu** .|

    ![Okno dialogowe przesyłanie danych platformy Spark 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Wybierz ikonę **SparkJobRun** , aby przesłać projekt do wybranej puli platformy Spark. **Zadanie zdalnej platformy Spark na karcie klaster** wyświetla postęp wykonywania zadania u dołu. Możesz zatrzymać aplikację, wybierając czerwony przycisk.

    ![Okno przesłania Apache Spark](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Okno dialogowe przesyłanie danych platformy Spark 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Lokalne uruchamianie/debugowanie Apache Spark aplikacji

Poniższe instrukcje umożliwiają skonfigurowanie lokalnego uruchomienia i lokalnego debugowania dla zadania Apache Spark.

### <a name="scenario-1-do-local-run"></a>Scenariusz 1. Uruchamianie lokalne

1. Otwórz okno dialogowe **konfiguracje uruchamiania/debugowania** , a następnie wybierz znak plus ( **+** ). Następnie wybierz opcję **Apache Spark on Synapse** . Wprowadź informacje o **nazwie**i **nazwie klasy głównej** do zapisania.

    ![IntelliJ uruchamianie konfiguracji debugowania — uruchomienie lokalne 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - Zmienne środowiskowe i lokalizacja WinUtils.exe są przeznaczone tylko dla użytkowników systemu Windows.
    - Zmienne środowiskowe: zmienna środowiskowa systemu może być wykrywana, jeśli została ustawiona wcześniej, i nie trzeba jej ręcznie dodawać.
    - [WinUtils.exe lokalizację](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): możesz określić lokalizację WinUtils, wybierając ikonę folderu po prawej stronie.

2. Następnie wybierz przycisk odtwarzania lokalnego.

    ![IntelliJ uruchamianie konfiguracji debugowania — lokalny przebieg 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Po zakończeniu lokalnego uruchomienia, jeśli skrypt zawiera dane wyjściowe, można sprawdzić plik wyjściowy z **data**  >  **__ustawień domyślnych__** danych.

    ![Wynik lokalnego uruchomienia projektu IntelliJ 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scenariusz 2: debugowanie lokalne

1. Otwórz skrypt **LogQuery** , ustaw punkty przerwania.
2. Wybierz ikonę **debugowania lokalnego** , aby przeprowadzić debugowanie lokalne.

    ![Wynik lokalnego uruchomienia projektu IntelliJ 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Dostęp do obszaru roboczego Synapse i zarządzanie nim

W programie Azure Toolkit for IntelliJ można wykonywać różne operacje w Eksploratorze Azure. Na pasku menu Przejdź do **widoku**  >  **Narzędzia**  >  **Eksplorator platformy Microsoft Azure**.

### <a name="launch-workspace"></a>Uruchom obszar roboczy

1. W Eksploratorze Azure przejdź do **Apache Spark na Synapse**, a następnie rozwiń go.

    ![Widok główny Eksploratora Azure IntelliJ pomysł](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Kliknij prawym przyciskiem myszy obszar roboczy, a następnie wybierz pozycję **Uruchom obszar roboczy**. zostanie otwarta witryna sieci Web.

    ![Szczegóły aplikacji widoku zadania platformy Spark 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Szczegóły aplikacji widoku zadania platformy Spark 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Konsola platformy Spark

Można uruchomić konsolę lokalną Spark (Scala) lub uruchomić konsolę sesji interaktywnej Spark usługi Livy (Scala).

### <a name="spark-local-console-scala"></a>Konsola lokalna Spark (Scala)

Upewnij się, że spełniono wymagania wstępne WINUTILS.EXE.

1. Na pasku menu Przejdź do opcji **Uruchom**  >  **Edytowanie konfiguracji..**..
2. W oknie **konfiguracje uruchamiania/debugowania** w lewym okienku przejdź do **Apache Spark na Synapse**  >  **[Spark on Synapse] MojaApl**.
3. W oknie głównym wybierz kartę **uruchomiono lokalnie** .
4. Podaj następujące wartości, a następnie wybierz przycisk **OK**:

    |Właściwość |Wartość |
    |----|----|
    |Zmienne środowiskowe|Upewnij się, że wartość HADOOP_HOME jest poprawna.|
    |Lokalizacja WINUTILS.exe|Upewnij się, że ścieżka jest poprawna.|

    ![Lokalna konfiguracja zestawu konsoli](./media/intellij-tool-synapse/local-console-synapse01.png)

5. W programie Project przejdź do usługi **MojaApl**  >  **src**  >  **Main**  >  **Scala**  >  **MojaApl**.
6. Na pasku menu Przejdź do menu **Narzędzia**  >  **platformy Spark**  >  **Uruchom konsolę usługi Spark (Scala)**.
7. Następnie można wyświetlić dwa okna dialogowe z monitem, jeśli chcesz automatyczne naprawić zależności. W takim przypadku wybierz pozycję **Automatyczne rozwiązywanie**.

    ![IntelliJ pomysł Spark automatycznej poprawki dialog1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ pomysł Spark automatycznej poprawki dialog2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. Konsola powinna wyglądać podobnie do poniższej ilustracji. W oknie konsoli wpisz `sc.appName` , a następnie naciśnij klawisze CTRL + ENTER. Zostanie wyświetlony wynik. Możesz zatrzymać konsolę lokalną, wybierając czerwony przycisk.

    ![Wynik z lokalnej konsoli POMYSŁu IntelliJ](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Konsola sesji interaktywnej Spark usługi Livy (Scala)

Jest on obsługiwany tylko w systemach IntelliJ 2018,2 i 2018,3.

1. Na pasku menu Przejdź do opcji **Uruchom**  >  **Edytowanie konfiguracji..**..

2. W oknie **konfiguracje uruchamiania/debugowania** w lewym okienku przejdź do **Apache Spark na Synapse**  >  **[Spark on Synapse] MojaApl**.

3. W oknie głównym wybierz kartę **zdalne uruchamianie na klastrze** .

4. Podaj następujące wartości, a następnie wybierz przycisk **OK**:

    |Właściwość |Wartość |
    |----|----|
    |Nazwa klasy głównej| Wybierz nazwę klasy głównej.| 
    |Pule platformy Spark|Wybierz pule platformy Spark, na których chcesz uruchomić aplikację.|
    ||

    ![Interaktywna konfiguracja zestawu konsolowego](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. W programie Project przejdź do usługi **MojaApl**  >  **src**  >  **Main**  >  **Scala**  >  **MojaApl**.

6. Na pasku menu Przejdź do menu **Narzędzia**  >  **platformy Spark**  >  **Uruchom konsolę sesji programu Spark usługi Livy Interactive (Scala)**.
7. Konsola powinna wyglądać podobnie do poniższej ilustracji. W oknie konsoli wpisz `sc.appName` , a następnie naciśnij klawisze CTRL + ENTER. Zostanie wyświetlony wynik. Możesz zatrzymać konsolę lokalną, wybierając czerwony przycisk.

    ![Wynik interaktywnej konsoli POMYSŁu IntelliJ](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Wyślij zaznaczenie do konsoli platformy Spark

Możesz chcieć zobaczyć wynik skryptu, wysyłając jakiś kod do konsoli lokalnej lub konsoli sesji interakcyjnej usługi Livy (Scala). W tym celu można wyróżnić kod w pliku Scala, a następnie kliknąć prawym przyciskiem myszy pozycję **Wyślij zaznaczenie do konsoli platformy Spark**. Wybrany kod zostanie wysłany do konsoli i zostanie wykonany. Wynik zostanie wyświetlony po kodzie w konsoli programu. Konsola sprawdzi istniejące błędy.

   ![Wyślij zaznaczenie do konsoli platformy Spark](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](../overview-what-is.md)
- [Utwórz nową pulę Apache Spark dla obszaru roboczego analizy usługi Azure Synapse](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
