---
title: Usługa Azure HDInsight dla Visual Studio Code
description: Dowiedz się, jak używać narzędzi Hive & platformy Spark (Azure HDInsight) do Visual Studio Code. Narzędzia służą do tworzenia i przesyłania zapytań i skryptów.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: d7e2bd2987e7eaf7798093f3918f72b7e09a3cb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313486"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Użyj narzędzi Hive & platformy Spark dla Visual Studio Code

Dowiedz się, jak używać narzędzi Apache Spark & Hive dla Visual Studio Code. Narzędzia służą do tworzenia i przesyłania Apache Hive zadań wsadowych, interakcyjnych zapytań Hive i skryptów PySpark dla Apache Spark. Najpierw opisano sposób instalowania narzędzi Hive & platformy Spark w programie Visual Studio Code. Następnie przeprowadzimy Cię przez proces przesyłania zadań do narzędzi programu Hive & platformy Spark.  

Narzędzia programu Hive & platformy Spark można instalować na platformach obsługiwanych przez Visual Studio Code. Należy pamiętać o następujących wymaganiach wstępnych dla różnych platform.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są następujące elementy:

- Klaster usługi Azure HDInsight. Aby utworzyć klaster, zobacz [Rozpoczynanie pracy z usługą HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Lub użyj klastra Spark i Hive obsługującego punkt końcowy Apache usługi Livy.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Narzędzie mono jest wymagane tylko dla systemów Linux i macOS.
- [Środowisko interaktywne PySpark dla Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Katalog lokalny. W tym artykule jest używanych  **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalowanie narzędzi Hive & platformy Spark

Po spełnieniu wymagań wstępnych można zainstalować narzędzia Hive & platformy Spark dla Visual Studio Code, wykonując następujące czynności:

1. Otwórz program Visual Studio Code.

2. Na pasku menu Przejdź do **widoku Wyświetl**  >  **rozszerzenia**.

3. W polu wyszukiwania wprowadź **gałąź & platformy Spark**.

4. W wynikach wyszukiwania wybierz pozycję **Narzędzia platformy Spark & Hive** , a następnie wybierz pozycję **Zainstaluj**:

   :::image type="content" source="./media/hdinsight-for-vscode/install-hdInsight-plugin.png" alt-text="Gałąź & platformy Spark dla Visual Studio Code instalacji języka Python":::

5. W razie potrzeby wybierz pozycję **Załaduj ponownie** .

## <a name="open-a-work-folder"></a>Otwieranie folderu służbowego

Aby otworzyć folder roboczy i utworzyć plik w Visual Studio Code, wykonaj następujące kroki:

1. Na pasku menu Przejdź do **pliku**  >  **Otwórz folder...**  >  **C:\HD\HDexample**, a następnie wybierz przycisk **Wybierz folder** . Folder zostanie wyświetlony w widoku **Eksploratora** po lewej stronie.

2. W widoku **Eksploratora** wybierz folder **HDexample** , a następnie wybierz ikonę **nowy plik** obok folderu Work:

   :::image type="content" source="./media/hdinsight-for-vscode/visual-studio-code-new-file.png" alt-text="ikona nowego pliku programu Visual Studio Code":::

3. Nazwij nowy plik przy użyciu programu `.hql` (zapytania Hive) lub `.py` rozszerzenia pliku (skrypt platformy Spark). W tym przykładzie zastosowano **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Ustawianie środowiska platformy Azure

W przypadku użytkownika w chmurze krajowej wykonaj następujące kroki, aby najpierw ustawić środowisko platformy Azure, a następnie zaloguj się do platformy Azure przy użyciu polecenia **Azure: Signing** :

1. Przejdź do   >    >  **ustawień** preferencji plików.
2. Wyszukaj następujący ciąg: **Azure: Cloud**.
3. Wybierz z listy chmurę krajową:

   :::image type="content" source="./media/hdinsight-for-vscode/set-default-login-entry-configuration.png" alt-text="Ustaw domyślną konfigurację wpisu logowania":::

## <a name="connect-to-an-azure-account"></a>Nawiązywanie połączenia z kontem platformy Azure

Aby można było przesłać skrypty do klastrów Visual Studio Code, użytkownik może zalogować się do subskrypcji platformy Azure lub [połączyć klaster usługi HDInsight](#link-a-cluster). Aby nawiązać połączenie z klastrem usługi HDInsight, użyj poświadczenia Ambari username/Password lub Join dołączone do domeny dla klastra ESP. Wykonaj następujące kroki, aby nawiązać połączenie z platformą Azure:

1. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń...**, a następnie wprowadź **Azure: Logowanie**:

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png" alt-text="Narzędzia programu Hive & platformy Spark dla Visual Studio Code logowania":::

2. Postępuj zgodnie z instrukcjami logowania, aby zalogować się do platformy Azure. Po nawiązaniu połączenia nazwa konta platformy Azure zostanie wyświetlona na pasku stanu u dołu okna Visual Studio Code.  

## <a name="link-a-cluster"></a>Łączenie klastra

### <a name="link-azure-hdinsight"></a>Link: usługa Azure HDInsight

Można połączyć normalny klaster przy użyciu nazwy użytkownika zarządzanej przez firmę [Apache Ambari](https://ambari.apache.org/)lub połączyć klaster usługi Hadoop z pakietem zabezpieczeń przedsiębiorstwa przy użyciu nazwy użytkownika domeny (takiej jak: `user1@contoso.com` ).

1. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń...**, a następnie wprowadź **Spark/Hive: Połącz klaster**.

   :::image type="content" source="./media/hdinsight-for-vscode/link-cluster-command.png" alt-text="Link do palety poleceń — polecenie cluster":::

2. Wybierz pozycję połączony klaster wpisz **Azure HDInsight**.

3. Wprowadź adres URL klastra usługi HDInsight.

4. Wprowadź nazwę użytkownika Ambari; wartość domyślna to **admin**.

5. Wprowadź hasło Ambari.

6. Wybierz typ klastra.

7. Ustaw nazwę wyświetlaną klastra (opcjonalnie).

8. Przejrzyj widok **danych wyjściowych** na potrzeby weryfikacji.

   > [!NOTE]  
   > Połączona nazwa użytkownika i hasło są używane, Jeśli klaster jest zalogowany do subskrypcji platformy Azure i połączony z klastrem.  

### <a name="link-generic-livy-endpoint"></a>Link: ogólny punkt końcowy usługi Livy

1. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń...**, a następnie wprowadź **Spark/Hive: Połącz klaster**.

2. Wybierz pozycję połączony klaster typ **rodzajowy punkt końcowy usługi Livy**.

3. Wprowadź ogólny punkt końcowy usługi Livy. Na przykład: http \: //10.172.41.42:18080.

4. Wybierz pozycję typ autoryzacji **podstawowa** lub **Brak**.  W przypadku wybrania opcji **podstawowa**:  
   
   1. Wprowadź nazwę użytkownika Ambari; wartość domyślna to **admin**.  

   2. Wprowadź hasło Ambari.

5. Przejrzyj widok **danych wyjściowych** na potrzeby weryfikacji.

## <a name="list-clusters"></a>Wyświetl listę klastrów

1. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń...**, a następnie wprowadź **Spark/Hive: Lista klaster**.

2. Wybierz żądaną subskrypcję.

3. Przejrzyj widok **danych wyjściowych** . Ten widok przedstawia połączony klaster (lub klastry) i wszystkie klastry w ramach subskrypcji platformy Azure:

   :::image type="content" source="./media/hdinsight-for-vscode/list-cluster-result1.png" alt-text="Ustaw domyślną konfigurację klastra":::

## <a name="set-the-default-cluster"></a>Ustaw domyślny klaster

1. Ponownie otwórz folder **HDexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Wybierz plik **HelloWorld. HQL** , który został utworzony [wcześniej](#open-a-work-folder). Zostanie on otwarty w Edytorze skryptów.

3. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Spark/Hive: Ustaw domyślny klaster**.  

4. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Wybierz klaster jako domyślny klaster dla bieżącego pliku skryptu. Narzędzia automatycznie aktualizują **.VSCode\settings.jsw** pliku konfiguracyjnym:

   :::image type="content" source="./media/hdinsight-for-vscode/set-default-cluster-configuration.png" alt-text="Ustaw domyślną konfigurację klastra":::

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Przesyłanie interakcyjnych zapytań Hive i skryptów wsadowych Hive

Za pomocą narzędzi platformy Spark & Hive dla Visual Studio Code można przesyłać interaktywne zapytania Hive i skrypty wsadowe Hive do klastrów.

1. Ponownie otwórz folder **HDexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Wybierz plik **HelloWorld. HQL** , który został utworzony [wcześniej](#open-a-work-folder). Zostanie on otwarty w Edytorze skryptów.

3. Skopiuj i wklej następujący kod do pliku Hive, a następnie zapisz go:

   ```hiveql
   SELECT * FROM hivesampletable;
   ```

4. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Kliknij prawym przyciskiem myszy Edytor skryptów i wybierz polecenie **Hive: Interactive** , aby przesłać zapytanie, lub użyj skrótu klawiaturowego Ctrl + Alt + I.  Wybierz **gałąź Hive: Batch** , aby przesłać skrypt, lub użyj skrótu klawiaturowego CTRL + ALT + H.  

6. Jeśli klaster domyślny nie został określony, wybierz klaster. Narzędzia umożliwiają również przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego. Po kilku chwilach wyniki zapytania pojawią się na nowej karcie:

   :::image type="content" source="./media/hdinsight-for-vscode/interactive-hive-result.png" alt-text="Interaktywny wynik zapytania Apache Hive":::

   - Panel **wyników** : można zapisać cały wynik jako plik CSV, JSON lub Excel w ścieżce lokalnej lub po prostu wybrać wiele wierszy.

   - Panel **komunikatów** : po wybraniu numeru **wiersza** zostanie on przeskoczy do pierwszego wiersza uruchomionego skryptu.

## <a name="submit-interactive-pyspark-queries"></a>Prześlij interaktywne zapytania PySpark

Użytkownicy mogą wykonywać PySpark interaktywnie w następujący sposób:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>Używanie interakcyjnego polecenia PySpark w pliku z pr
Aby przesłać zapytania przy użyciu polecenia PySpark Interactive, wykonaj następujące kroki:

1. Ponownie otwórz folder **HDexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Utwórz nowy plik **HelloWorld.py** , postępując zgodnie z [wcześniejszymi](#open-a-work-folder) krokami.

3. Skopiuj i wklej następujący kod do pliku skryptu:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. Monit o zainstalowanie jądra PySpark/Synapse Pyspark jest wyświetlany w prawym dolnym rogu okna. Możesz kliknąć przycisk **Instaluj** , aby rozpocząć instalację PySpark PySpark/Synapse. lub kliknij przycisk **Pomiń** , aby pominąć ten krok.

   :::image type="content" source="./media/hdinsight-for-vscode/install-the-pyspark-kernel.png" alt-text="Zrzut ekranu przedstawia opcję pomijania instalacji PySpark.":::

5. Jeśli chcesz zainstalować ją później, możesz przejść do   >    >  pozycji **Ustawienia** preferencji plików, a następnie odznaczyć pozycję **HDInsight: Włącz opcję Pomiń instalację Pyspark** w ustawieniach. 
    
    :::image type="content" source="./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png" alt-text="Zrzut ekranu przedstawia opcję włączenia instalacji pomijania Pyspark.":::

6. Jeśli instalacja zakończy się pomyślnie w kroku 4, w prawym dolnym rogu okna zostanie wyświetlone okno komunikatu "PySpark zainstalowany pomyślnie". Kliknij przycisk **Załaduj ponownie** , aby ponownie załadować okno.

   :::image type="content" source="./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png" alt-text="pomyślnie zainstalowano pyspark":::


7. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń...** lub użyj skrótu klawiaturowego **Shift + Ctrl + P** , a następnie wprowadź **Python: Wybierz interpreter, aby uruchomić serwer Jupyter**.

   :::image type="content" source="./media/hdinsight-for-vscode/select-interpreter-to-start-jupyter-server.png" alt-text="Wybierz interpreter, aby uruchomić serwer Jupyter":::

8. Wybierz opcję Python poniżej.

   :::image type="content" source="./media/hdinsight-for-vscode/choose-the-below-option.png" alt-text="Wybierz poniższą opcję":::
    
9. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń...** lub użyj skrótu klawiaturowego **Shift + Ctrl + P** , a następnie wprowadź **polecenie Deweloper: Załaduj ponownie okno**.

    :::image type="content" source="./media/hdinsight-for-vscode/reload-window.png" alt-text="Załaduj ponownie okno":::

10. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

11. Zaznacz cały kod, kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Spark: PySpark Interactive/Synapse: PySpark Interactive** , aby przesłać zapytanie. 

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-right-click.png" alt-text="interakcyjne menu kontekstowe pyspark":::

12. Wybierz klaster, jeśli nie został określony domyślny klaster. Po kilku chwilach interaktywne wyniki języka **Python** pojawiają się na nowej karcie. Kliknij pozycję PySpark, aby przełączyć jądro do **PySpark/Synapse PySpark**, a kod zostanie uruchomiony pomyślnie. Jeśli chcesz przełączyć się na jądro Synapse Pyspark, zaleca się wyłączenie ustawień autosettings w Azure Portal. W przeciwnym razie może upłynąć dużo czasu, aby wznowić klaster i ustawić jądro Synapse przy pierwszym użyciu. Jeśli narzędzia umożliwiają również przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego:

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png" alt-text="interaktywne okno interaktywnego środowiska Python pyspark":::

13. Wprowadź **%% info**, a następnie naciśnij klawisze SHIFT + ENTER, aby wyświetlić informacje o zadaniu (opcjonalnie):

    :::image type="content" source="./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png" alt-text="Informacje o zadaniach pyspark Interactive":::

Narzędzie obsługuje również zapytanie **Spark SQL** :

  :::image type="content" source="./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png" alt-text="wynik widoku interaktywnego pyspark":::


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Wykonaj zapytanie interaktywne w pliku z PR przy użyciu komentarza #%%

1. Dodaj **#%%** przed kodem PR, aby uzyskać środowisko notesu.

   :::image type="content" source="./media/hdinsight-for-vscode/run-cell.png" alt-text="Dodaj #%%":::

2. Kliknij pozycję **Uruchom komórkę**. Po kilku chwilach interaktywne wyniki języka Python pojawiają się na nowej karcie. Kliknij pozycję PySpark, aby przełączyć jądro do PySpark/Synapse PySpark, a następnie kliknij ponownie przycisk **Uruchom komórkę** , a kod zostanie uruchomiony pomyślnie.

   :::image type="content" source="./media/hdinsight-for-vscode/run-cell-get-results.png" alt-text="wyniki przebiegu komórki":::

## <a name="leverage-ipynb-support-from-python-extension"></a>Korzystanie z pomocy technicznej IPYNB z rozszerzenia Python

1. Możesz utworzyć Jupyter Notebook za pomocą polecenia z palety poleceń lub tworząc nowy plik. ipynb w obszarze roboczym. Aby uzyskać więcej informacji, zobacz [Praca z notesami Jupyter w Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Kliknij przycisk **Uruchom komórkę** , postępuj zgodnie z monitami, aby **ustawić domyślną pulę platformy Spark** (zdecydowanie Zachęcaj do ustawienia domyślnego klastra/puli za każdym razem przed otwarciem notesu), a następnie **Załaduj ponownie** okno.

   :::image type="content" source="./media/hdinsight-for-vscode/set-the-default-spark-pool-and-reload.png" alt-text="Ustaw domyślną pulę platformy Spark i Załaduj ponownie":::

3. Kliknij pozycję PySpark, aby przełączyć jądro do **PySpark/Synapse PySpark**, a następnie kliknij opcję **Run Cell**(po czasie), gdy zostanie wyświetlony wynik.

   :::image type="content" source="./media/hdinsight-for-vscode/run-ipynb-file-results.png" alt-text="Uruchom wyniki ipynb":::


> [!NOTE]
>
> [MS-toolsai. jupyter >wersja 2021.3.684299474 nie jest obsługiwana przez to rozszerzenie](#known-issues) jest znanym problemem. Użyj jądra Synapse, nalepki do programu Microsoft Jupyter 2021.3.684299474.

## <a name="submit-pyspark-batch-job"></a>Prześlij zadanie wsadowe PySpark

1. Ponownie otwórz folder **HDexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Utwórz nowy plik **BatchFile.py** , wykonując czynności opisane w [poprzednich](#open-a-work-folder) krokach.

3. Skopiuj i wklej następujący kod do pliku skryptu:

   ```python
   from __future__ import print_function
   import sys
   from operator import add
   from pyspark.sql import SparkSession
   if __name__ == "__main__":
       spark = SparkSession\
           .builder\
           .appName("PythonWordCount")\
           .getOrCreate()
   
       lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
       counts = lines.flatMap(lambda x: x.split(' '))\
                  .map(lambda x: (x, 1))\
                   .reduceByKey(add)
       output = counts.collect()
       for (word, count) in output:
           print("%s: %i" % (word, count))
       spark.stop()
   ```

4. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure lub Połącz klaster, jeśli jeszcze tego nie zrobiono.

5. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Spark: PySpark Batch** lub * * Synapse: PySpark Batch * * *.

6. Wybierz klaster/pulę platformy Spark, w której ma zostać przesłane zadanie PySpark:

   :::image type="content" source="./media/hdinsight-for-vscode/submit-pythonjob-result.png" alt-text="Przesyłanie danych wyjściowych wyników zadania w języku Python":::

Po przesłaniu zadania w języku Python dzienniki przesyłania są wyświetlane w oknie **dane wyjściowe** w Visual Studio Code. Wyświetlany jest również adres URL interfejsu użytkownika platformy Spark i adres URL interfejsu użytkownika przędzy. Jeśli zadanie wsadowe zostanie przesłane do puli Apache Spark, zostanie również wyświetlony adres URL interfejsu użytkownika historii platformy Spark i adres URL interfejsu użytkownika aplikacji zadań platformy Spark. Możesz otworzyć adres URL w przeglądarce sieci Web, aby śledzić stan zadania.

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integracja z usługą HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Nawiązywanie połączenia z klastrem usługi HDInsight ESP z brokerem identyfikatorów (HIB)

Aby zalogować się do subskrypcji platformy Azure w celu nawiązania połączenia z klastrem usługi HDInsight ESP z brokerem identyfikatorów (HIB), wykonaj kroki opisane w temacie. Po zalogowaniu zostanie wyświetlona lista klastrów w Eksploratorze Azure. Aby uzyskać więcej instrukcji, zobacz [nawiązywanie połączenia z klastrem usługi HDInsight](#connect-to-an-azure-account).

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Uruchamianie zadania Hive/PySpark w klastrze usługi HDInsight ESP z IDENTYFIKATORem brokera (HIB)

W przypadku uruchamiania zadania programu Hive można wykonać normalne kroki w celu przesłania zadania do klastra usługi HDInsight ESP z IDENTYFIKATORem brokera (HIB). Aby uzyskać więcej instrukcji, zapoznaj się z tematem [przesyłanie interaktywnych zapytań Hive i skryptów usługi Hive](#submit-interactive-hive-queries-and-hive-batch-scripts) .

W przypadku uruchamiania interaktywnego zadania PySpark można wykonać normalne kroki, aby przesłać zadanie do klastra usługi HDInsight ESP z IDENTYFIKATORem brokera (HIB). Zapoznaj się z tematem [przesyłanie interaktywnych zapytań PySpark](#submit-interactive-pyspark-queries) , aby uzyskać więcej instrukcji.

W przypadku uruchamiania zadania wsadowego PySpark można wykonać normalne kroki, aby przesłać zadanie do klastra usługi HDInsight ESP z IDENTYFIKATORem brokera (HIB). Więcej instrukcji można znaleźć w części [przesyłanie zadania usługi Batch PySpark](#submit-pyspark-batch-job) .

## <a name="apache-livy-configuration"></a>Konfiguracja Apache usługi Livy

Konfiguracja [Apache usługi Livy](https://livy.incubator.apache.org/) jest obsługiwana. Można ją skonfigurować w **.VSCode\settings.jsna** pliku w folderze obszaru roboczego. Obecnie konfiguracja usługi Livy obsługuje tylko skrypt języka Python. Aby uzyskać więcej informacji, zobacz [plik Readme usługi Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Jak wyzwolić konfigurację usługi Livy**

### <a name="method-1"></a>Metoda 1  

1. Na pasku menu Przejdź do   >    >  **ustawień** Preferencje plików.
2. W polu **Wyszukaj ustawienia** wprowadź wartość **przesyłanie zadania usługi HDInsight: usługi Livy conf**.  
3. Wybierz pozycję **Edytuj w settings.js** , aby uzyskać odpowiedni wynik wyszukiwania.

### <a name="method-2"></a>Metoda 2

Prześlij plik i zwróć uwagę, że `.vscode` folder jest automatycznie dodawany do folderu Work. Konfigurację usługi Livy można wyświetlić, wybierając pozycję **.vscode\settings.json**.

- Ustawienia projektu:

  :::image type="content" source="./media/hdinsight-for-vscode/hdi-apache-livy-config.png" alt-text="Konfiguracja usługi HDInsight Apache usługi Livy":::

  >[!NOTE]
  >Dla ustawień **driverMemory** i **executorMemory** ustaw wartość i jednostkę. Na przykład: 1g lub 1024m.

- Obsługiwane konfiguracje usługi Livy:

  **Opublikuj/Batches**
  
  **Treść żądania**

  | name | description (opis) | typ |
  | --- | --- | --- |
  |  — plik | Plik zawierający aplikację do wykonania | Ścieżka (wymagana) |
  | proxyUser | Użytkownik do personifikacji podczas uruchamiania zadania | Ciąg |
  | Nazwą | Aplikacja Java/Spark klasy głównej | Ciąg |
  | args | Argumenty wiersza polecenia dla aplikacji | Lista ciągów |
  | jars | Jars do użycia w tej sesji | Lista ciągów | 
  | pyFiles | Pliki języka Python, które mają być używane w tej sesji | Lista ciągów |
  | files | Pliki, które mają być używane w tej sesji | Lista ciągów |
  | driverMemory | Ilość pamięci do użycia w procesie sterownika | Ciąg |
  | driverCores | Liczba rdzeni do użycia w procesie sterownika | int |
  | executorMemory | Ilość pamięci do użycia na proces wykonawczy | Ciąg |
  | executorCores | Liczba rdzeni do użycia dla każdego wykonawcy | int |
  | numExecutors | Liczba uruchomień do uruchomienia dla tej sesji | int |
  | archiwizowan | Archiwa, które mają być używane w tej sesji | Lista ciągów |
  | kolejka | Nazwa kolejki PRZĘDZy do przesłania| Ciąg |
  | name | Nazwa tej sesji | Ciąg |
  | produkt | Właściwości konfiguracji platformy Spark | Mapa klucza = Val |

  **Treść odpowiedzi** Utworzony obiekt wsadowy.

  | name | description (opis) | typ |
  | --- | ---| --- |
  | ID (Identyfikator) | Identyfikator sesji | int |
  | appId | Identyfikator aplikacji dla tej sesji | Ciąg |
  | appInfo | Szczegółowe informacje o aplikacji | Mapa klucza = Val |
  | Dziennik | Wiersze dziennika | Lista ciągów |
  | stan |Stan partii | Ciąg |

  > [!NOTE]
  > Przypisana usługi Livy konfiguracja zostanie wyświetlona w okienku danych wyjściowych podczas przesyłania skryptu.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integracja z usługą Azure HDInsight z poziomu Eksploratora

Tabelę programu Hive można wyświetlić w klastrach bezpośrednio za pomocą Eksploratora **usługi Azure HDInsight** :

1. [Połącz](#connect-to-an-azure-account) się z kontem platformy Azure, jeśli jeszcze tego nie zrobiono.

2. Wybierz ikonę **platformy Azure** z kolumny z lewej strony.

3. W okienku po lewej stronie rozwiń pozycję **Azure: HDINSIGHT**. Dostępne subskrypcje i klastry są wymienione na liście.

4. Rozwiń klaster, aby wyświetlić bazę danych metadanych programu Hive i schemat tabeli.

5. Kliknij prawym przyciskiem myszy tabelę programu Hive. Na przykład: **hivesampletable**. Wybierz pozycję **Podgląd**.

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png" alt-text="Gałąź & platformy Spark dla Visual Studio Code wersji zapoznawczej tabeli Hive":::

6. Zostanie otwarte okno **Podgląd wyników** :

   :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png" alt-text="Gałąź & platformy Spark dla okna wyników podglądu Visual Studio Code":::

- Panel wyników

   Można zapisać cały wynik jako plik CSV, JSON lub Excel w ścieżce lokalnej lub po prostu wybrać wiele wierszy.

- Panel komunikatów

  1. Gdy liczba wierszy w tabeli jest większa niż 100, zostanie wyświetlony następujący komunikat: "pierwsze 100 wierszy są wyświetlane dla tabeli programu Hive".
  2. Gdy liczba wierszy w tabeli jest mniejsza lub równa 100, zostanie wyświetlony następujący komunikat: "60 wierszy są wyświetlane dla tabeli Hive".
  3. Gdy w tabeli nie ma zawartości, zostanie wyświetlony następujący komunikat: " `0 rows are displayed for Hive table.` "

     >[!NOTE]
     >
     >W systemie Linux Zainstaluj XCLIP, aby włączyć dane kopiowania z tabeli.
     >
     >:::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png" alt-text="Platforma Spark & Hive dla programu Visual Studio Code w systemie Linux":::

## <a name="additional-features"></a>Dodatkowe funkcje

Gałąź & platformy Spark dla Visual Studio Code obsługuje również następujące funkcje:

- **Autouzupełnianie funkcji IntelliSense**. Sugestie są wyskakujące dla słów kluczowych, metod, zmiennych i innych elementów programistycznych. Różne ikony reprezentują różne typy obiektów:

    :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png" alt-text="Narzędzia programu Hive & platformy Spark dla Visual Studio Code obiektów IntelliSense":::

- **Znacznik błędu funkcji IntelliSense**. Usługa językowa podkreśla błędy w skrypcie Hive.     
- **Najważniejsze informacje o składni**. Usługa językowa używa różnych kolorów do odróżniania zmiennych, słów kluczowych, typu danych, funkcji i innych elementów programistycznych:

    :::image type="content" source="./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png" alt-text="Narzędzia programu Hive & platformy Spark dla Visual Studio Code składni":::

## <a name="reader-only-role"></a>Rola tylko do odczytu

Użytkownicy, którym przypisano rolę tylko do odczytu dla klastra, nie mogą przesyłać zadań do klastra usługi HDInsight ani wyświetlać bazy danych programu Hive. Skontaktuj się z administratorem klastra, aby uaktualnić rolę do [**operatora klastra usługi HDInsight**](./hdinsight-migrate-granular-access-cluster-configurations.md#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) w [Azure Portal](https://portal.azure.com/). Jeśli masz prawidłowe poświadczenia Ambari, możesz ręcznie połączyć klaster, korzystając z poniższych wskazówek.

### <a name="browse-the-hdinsight-cluster"></a>Przeglądanie klastra usługi HDInsight  

Po wybraniu programu Azure HDInsight Explorer do rozwinięcia klastra usługi HDInsight zostanie wyświetlony monit o połączenie klastra, jeśli istnieje rola tylko do odczytu dla danego klastra. Użyj następującej metody, aby połączyć się z klastrem przy użyciu poświadczeń Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Prześlij zadanie do klastra usługi HDInsight

Podczas przesyłania zadania do klastra usługi HDInsight zostanie wyświetlony monit o połączenie klastra, jeśli jesteś w roli tylko do odczytu klastra. Wykonaj następujące kroki, aby połączyć się z klastrem przy użyciu poświadczeń Ambari.

### <a name="link-to-the-cluster"></a>Połącz z klastrem

1. Wprowadź prawidłową nazwę użytkownika Ambari.
2. Wprowadź prawidłowe hasło.

   :::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png" alt-text="Narzędzia programu Hive & platformy Spark dla Visual Studio Code użytkownika":::

   :::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png" alt-text="Narzędzia programu Hive & platformy Spark dla hasła Visual Studio Code":::

   > [!NOTE]
   >
   >Możesz użyć, `Spark / Hive: List Cluster` Aby sprawdzić połączony klaster:
   >
   >:::image type="content" source="./media/hdinsight-for-vscode/list-cluster-result1.png" alt-text="Narzędzia programu Hive & platformy Spark dla połączonego czytnika Visual Studio Code":::

## <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

### <a name="browse-a-data-lake-storage-gen2-account"></a>Przeglądanie konta Data Lake Storage Gen2

Wybierz Eksplorator usługi Azure HDInsight, aby rozwinąć konto Data Lake Storage Gen2. Zostanie wyświetlony monit o wprowadzenie klucza dostępu do magazynu, jeśli Twoje konto platformy Azure nie ma dostępu do magazynu Gen2. Po sprawdzeniu poprawności klucza dostępu konto Data Lake Storage Gen2 zostanie rozwinięte.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Przesyłanie zadań do klastra usługi HDInsight przy użyciu Data Lake Storage Gen2

Prześlij zadanie do klastra usługi HDInsight przy użyciu Data Lake Storage Gen2. Zostanie wyświetlony monit o wprowadzenie klucza dostępu do magazynu, jeśli konto platformy Azure nie ma dostępu do zapisu w magazynie Gen2. Po sprawdzeniu poprawności klucza dostępu zadanie zostanie pomyślnie przesłane.

:::image type="content" source="./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png" alt-text="Narzędzia programu Hive & platformy Spark dla Visual Studio Code AccessKey":::

> [!NOTE]
>
> Możesz uzyskać klucz dostępu dla konta magazynu z Azure Portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Odłącz klaster

1. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń**, a następnie wprowadź **Spark/Hive: Odłącz klaster**.  

2. Wybierz klaster do odłączenia.  

3. Zobacz widok **danych wyjściowych** w celu weryfikacji.  

## <a name="sign-out"></a>Wyloguj się  

Na pasku menu Przejdź do **widoku**  >  **paleta poleceń**, a następnie wprowadź **Azure: Wyloguj się**.

## <a name="known-issues"></a>Znane problemy 

 MS-toolsai. Jupyter >wersja 2021.3.684299474 nie jest obsługiwana w tym rozszerzeniu, użyj jądra Synapse, nalepki do programu Microsoft Jupyter 2021.3.684299474.

 1. Wyłącz rozszerzenie autoaktualizowania.

      ![Wyłącz rozszerzenie autoaktualizowania](./media/hdinsight-for-vscode/disable-auto-updating-extension.png)

2. Zainstaluj wybraną wersję programu Microsoft Jupyter.

      ![wybrana wersja programu Microsoft Jupyter](./media/hdinsight-for-vscode/selected-version-of-microsoft-jupyter.png)

3. Zainstaluj program Microsoft Jupyter w wersji 2021.3.684299474


## <a name="next-steps"></a>Następne kroki

Film przedstawiający korzystanie z usługi Spark & Hive dla Visual Studio Code można znaleźć w temacie [gałąź & platformy Spark dla Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).