---
title: Samouczek — narzędzia platformy Spark & Hive dla programu vscode (aplikacja Spark)
description: Samouczek — używanie narzędzi Hive & platformy Spark dla programu programu vscode do tworzenia aplikacji platformy Spark, które są zapisywane w języku Python, i przesyłania ich do puli bezserwerowej Apache Spark.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: e5d335ee14709ec330405419f5be8ac5fbd6ce75
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943765"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Samouczek: Tworzenie aplikacji Apache Spark za pomocą programu vscode przy użyciu obszaru roboczego Synapse

Dowiedz się, jak używać narzędzi Apache Spark & Hive dla Visual Studio Code. Narzędzia służą do tworzenia i przesyłania Apache Hive zadań wsadowych, interakcyjnych zapytań Hive i skryptów PySpark dla Apache Spark. Najpierw opisano sposób instalowania narzędzi Hive & platformy Spark w programie Visual Studio Code. Następnie przeprowadzimy Cię przez proces przesyłania zadań do narzędzi programu Hive & platformy Spark.

Narzędzia programu Hive & platformy Spark można instalować na platformach obsługiwanych przez Visual Studio Code. Należy pamiętać o następujących wymaganiach wstępnych dla różnych platform.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są następujące elementy:

- Bezserwerowa Pula Apache Spark. Aby utworzyć bezserwerową pulę Apache Spark, zobacz [Tworzenie puli Apache Spark przy użyciu Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Narzędzie mono jest wymagane tylko dla systemów Linux i macOS.
- [Środowisko interaktywne PySpark dla Visual Studio Code](../../hdinsight/set-up-pyspark-interactive-environment.md).
- Katalog lokalny. W tym artykule jest używanych  **C:\HD\Synaseexample**.

## <a name="install-spark--hive-tools"></a>Instalowanie narzędzi Hive & platformy Spark

Po spełnieniu wymagań wstępnych można zainstalować narzędzia Hive & platformy Spark dla Visual Studio Code, wykonując następujące czynności:

1. Otwórz program Visual Studio Code.

2. Na pasku menu Przejdź do **widoku Wyświetl**  >  **rozszerzenia**.

3. W polu wyszukiwania wprowadź **gałąź & platformy Spark**.

4. W wynikach wyszukiwania wybierz pozycję **Narzędzia platformy Spark & Hive** , a następnie wybierz pozycję **Zainstaluj**:

     ![Gałąź & platformy Spark dla Visual Studio Code instalacji języka Python](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. W razie potrzeby wybierz pozycję **Załaduj ponownie** .

## <a name="open-a-work-folder"></a>Otwieranie folderu służbowego

Aby otworzyć folder roboczy i utworzyć plik w Visual Studio Code, wykonaj następujące kroki:

1. Na pasku menu Przejdź do **pliku**  >  **Otwórz folder...**  >  **C:\HD\Synaseexample**, a następnie wybierz przycisk **Wybierz folder** . Folder zostanie wyświetlony w widoku **Eksploratora** po lewej stronie.

2. W widoku **Eksploratora** wybierz folder **Synaseexample** , a następnie wybierz ikonę **nowy plik** obok folderu Work:

     ![ikona nowego pliku programu Visual Studio Code](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Nazwij nowy plik przy użyciu `.py` rozszerzenia pliku (skryptu Spark). W tym przykładzie używa **HelloWorld.py**.

## <a name="connect-to-your-spark-pools"></a>Nawiązywanie połączenia z pulami platformy Spark

Zaloguj się do subskrypcji platformy Azure, aby nawiązać połączenie z pulami platformy Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

Wykonaj następujące kroki, aby nawiązać połączenie z platformą Azure:

1. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń...**, a następnie wprowadź **Azure: Logowanie**:

     ![Narzędzia programu Hive & platformy Spark dla Visual Studio Code logowania](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Postępuj zgodnie z instrukcjami logowania, aby zalogować się do platformy Azure. Po nawiązaniu połączenia nazwa konta platformy Azure zostanie wyświetlona na pasku stanu u dołu okna Visual Studio Code.

## <a name="set-the-default-spark-pool"></a>Ustawianie domyślnej puli platformy Spark

1. Ponownie otwórz folder **Synaseexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Wybierz plik **HelloWorld.py** , który został utworzony [wcześniej](#open-a-work-folder). Zostanie on otwarty w Edytorze skryptów.

3. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Synapse: Ustaw domyślną pulę platformy Spark**.  

4. [Połącz](#connect-to-your-spark-pools) się z kontem platformy Azure, jeśli jeszcze tego nie zrobiono.

5. Wybierz pulę platformy Spark jako domyślną pulę platformy Spark dla bieżącego pliku skryptu. Narzędzia automatycznie aktualizują **.VSCode\settings.jsw** pliku konfiguracyjnym:

     ![Ustaw domyślną konfigurację klastra](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Przesyłaj interakcyjne zapytania Synapse PySpark do puli platformy Spark

Użytkownicy mogą wykonywać Synapse PySpark Interactive w puli platformy Spark w następujący sposób:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>Korzystanie z polecenia Synapse PySpark Interactive w pliku z pr
Aby przesłać zapytania przy użyciu polecenia PySpark Interactive, wykonaj następujące kroki:

1. Ponownie otwórz folder **Synaseexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

2. Utwórz nowy plik **HelloWorld.py** , postępując zgodnie z [wcześniejszymi](#open-a-work-folder) krokami.

3. Skopiuj i wklej następujący kod do pliku skryptu:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. Monit o zainstalowanie jądra PySpark/Synapse Pyspark jest wyświetlany w prawym dolnym rogu okna. Możesz kliknąć przycisk **Instaluj** , aby rozpocząć instalację PySpark PySpark/Synapse. lub kliknij przycisk **Pomiń** , aby pominąć ten krok.

     ![Zainstaluj jądro pyspark](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Jeśli chcesz zainstalować ją później, możesz przejść do   >    >  pozycji **Ustawienia** preferencji plików, a następnie odznaczyć pozycję **HDInsight: Włącz opcję Pomiń instalację Pyspark** w ustawieniach. 
    
     ![Włącz instalację pomijania pyspark](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Jeśli instalacja zakończyła się pomyślnie w kroku 4, w prawym dolnym rogu okna pojawi się okno komunikatu "PySpark/Synapse Pyspark prawidłowo zainstalowane". Kliknij przycisk **Załaduj ponownie** , aby ponownie załadować okno.

     ![pomyślnie zainstalowano pyspark](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń...** lub użyj skrótu klawiaturowego **Shift + Ctrl + P** , a następnie wprowadź **Python: Wybierz interpreter, aby uruchomić serwer Jupyter**.

     ![Wybierz interpreter, aby uruchomić serwer Jupyter](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Wybierz opcję Python poniżej.

     ![Wybierz poniższą opcję](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. Na pasku menu Przejdź do **widoku**  >  **paleta poleceń...** lub użyj skrótu klawiaturowego **Shift + Ctrl + P** , a następnie wprowadź **polecenie Deweloper: Załaduj ponownie okno**.

     ![Załaduj ponownie okno](./media/vscode-tool-synapse/reload-window.png)

10. [Połącz](#connect-to-your-spark-pools) się z kontem platformy Azure, jeśli jeszcze tego nie zrobiono.

11. Zaznacz cały kod, kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Synapse: Pyspark Interactive** , aby przesłać zapytanie. 

     ![interakcyjne menu kontekstowe pyspark](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Wybierz pulę Spark, jeśli nie określono domyślnej puli platformy Spark. Po kilku chwilach interaktywne wyniki języka **Python** pojawiają się na nowej karcie. Kliknij pozycję PySpark, aby przełączyć jądro do **Synapse PySpark**, a następnie prześlij ponownie wybrany kod, a kod zostanie uruchomiony pomyślnie. Narzędzia umożliwiają również przesyłanie bloku kodu zamiast całego pliku skryptu za pomocą menu kontekstowego:

     ![interaktywne](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Wykonaj zapytanie interaktywne w pliku z PR przy użyciu komentarza #%%

1. Dodaj **#%%** przed kodem, aby uzyskać środowisko notesu.

     ![Dodaj #%%](./media/vscode-tool-synapse/run-cell.png)

2. Kliknij pozycję **Uruchom komórkę**. Po kilku chwilach interaktywne wyniki języka Python pojawiają się na nowej karcie. Kliknij pozycję PySpark, aby przełączyć jądro do **Synapse PySpark**, a następnie kliknij ponownie przycisk **Uruchom komórkę** , a kod zostanie uruchomiony pomyślnie. 

     ![wyniki przebiegu komórki](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Korzystanie z pomocy technicznej IPYNB z rozszerzenia Python

1. Możesz utworzyć Jupyter Notebook za pomocą polecenia z palety poleceń lub tworząc nowy plik. ipynb w obszarze roboczym. Aby uzyskać więcej informacji, zobacz [Praca z notesami Jupyter w Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)

2. Kliknij przycisk **Uruchom komórkę** , postępuj zgodnie z monitami, aby **ustawić domyślną pulę platformy Spark** (zdecydowanie Zachęcaj do ustawienia domyślnego klastra/puli za każdym razem przed otwarciem notesu), a następnie **Załaduj ponownie** okno.

     ![Ustaw domyślną pulę platformy Spark i Załaduj ponownie](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Kliknij pozycję PySpark, aby przełączyć jądro do **Synapse PySpark**, a następnie kliknij opcję **Run Cell**(po czasie), gdy zostanie wyświetlony wynik.

     ![Uruchom wyniki ipynb](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. W tym zakresie nie jest obsługiwana wersja MS-Python >= 2020.5.78807. jest to [znany problem](#known-issues).
>  
>2. Zaleca się przełączenie do jądra usługi Synapse Pyspark, co umożliwia wyłączenie ustawień autosettings w witrynie Azure Portal. W przeciwnym razie może upłynąć dużo czasu, aby wznowić klaster i ustawić jądro Synapse przy pierwszym użyciu. 
>
>    ![Ustawienia Autostart](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>Prześlij zadanie wsadowe PySpark do puli platformy Spark

1. Ponownie otwórz folder **Synaseexample** , który został omówiony [wcześniej](#open-a-work-folder), jeśli został zamknięty.  

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

4. [Połącz](#connect-to-your-spark-pools) się z kontem platformy Azure, jeśli jeszcze tego nie zrobiono.

5. Kliknij prawym przyciskiem myszy Edytor skryptów, a następnie wybierz pozycję **Synapse: PySpark Batch**.

6. Wybierz pulę platformy Spark, w której ma zostać przesłane zadanie PySpark:

     ![Przesyłanie danych wyjściowych wyników zadania w języku Python](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Po przesłaniu zadania wsadowego do puli platformy Spark dzienniki przesyłania są wyświetlane w oknie **dane wyjściowe** w Visual Studio Code. Wyświetlany jest również adres URL **interfejsu użytkownika platformy Spark** i adres URL **interfejsu użytkownika aplikacji zadania platformy Spark** . Możesz otworzyć adres URL w przeglądarce sieci Web, aby śledzić stan zadania.

## <a name="access-and-manage-synapse-workspace"></a>Dostęp do obszaru roboczego Synapse i zarządzanie nim

Możesz wykonywać różne operacje w Eksploratorze platformy Azure w ramach narzędzi Hive & platformy Spark dla programu vscode. Z poziomu Eksploratora platformy Azure.

![obraz platformy Azure](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Uruchom obszar roboczy

1. W Eksploratorze Azure przejdź do **Synapse**, rozwiń go i Wyświetl listę subskrypcji Synapse.

     ![Eksplorator Synapse](./media/vscode-tool-synapse/synapse-explorer.png)

2. Kliknij pozycję subskrypcja obszaru roboczego Synapse, rozwiń go i Wyświetl listę obszarów roboczych.

3. Kliknij prawym przyciskiem myszy obszar roboczy, a następnie wybierz pozycję **wyświetl Apache Spark aplikacje**. zostanie otwarta strona Apache Spark aplikacji w witrynie sieci Web Synapse Studio.

     ![Kliknij prawym przyciskiem myszy obszar roboczy](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![Aplikacja Synapse Studio](./media/vscode-tool-synapse/synapse-studio-application.png)

4. Rozwiń obszar roboczy, zostaną wyświetlone **domyślne magazyny** i **Pule platformy Spark** .

5. Kliknij prawym przyciskiem myszy **domyślny magazyn**, zostanie wyświetlona **pełna ścieżka kopiowania** i **otwarta w programie Synapse Studio** . 

     ![Kliknij prawym przyciskiem myszy magazyn domyślny](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - Kliknięcie przycisku **Kopiuj pełną ścieżkę** spowoduje SKOPIOWANIE adresu URL konta podstawowego ADLS Gen2, można wkleić go w miejscu, w którym potrzebujesz.

     - Kliknij pozycję **Otwórz w programie Synapse Studio**, a podstawowe konto magazynu zostanie otwarte w programie Synapse Studio.

     ![magazyn domyślny w programie Synapse Studio](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. Rozwiń **magazyn domyślny**. zostanie wyświetlone podstawowe konto magazynu.

7. Rozszerzanie **pul platformy Spark**, zostaną wyświetlone wszystkie pule platformy Spark w obszarze roboczym.

     ![Rozwiń pulę magazynu](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Znane problemy

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>MS-Python >= wersja 2020.5.78807 nie jest obsługiwana w tym zakresie 

"Nie można nawiązać połączenia z notesem Jupyter". jest znanym problemem w wersji języka Python >= 2020.5.78807. Aby uniknąć tego problemu, zaleca się, aby użytkownicy korzystali z **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** wersji MS-Python.

![znane problemy](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](../overview-what-is.md)
- [Utwórz nową pulę Apache Spark dla obszaru roboczego analizy usługi Azure Synapse](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
