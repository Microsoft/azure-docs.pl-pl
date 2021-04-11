---
title: Informacje o ustawieniach konfiguracji usługi Azure HDInsight
description: Wprowadź konfigurację rozszerzenia usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: a9a444c2557ea17114123cbd5084cbbd9fe6dac6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259996"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Informacje o ustawieniach konfiguracji usługi Azure HDInsight

Rozszerzenie narzędzi platformy Spark & Hive dla Visual Studio Code jest wysoce konfigurowalne. Na tej stronie opisano ustawienia kluczy, z którymi można korzystać.  

Aby uzyskać ogólne informacje na temat pracy z ustawieniami w VS Code, zapoznaj się z [ustawieniami użytkownika i obszaru roboczego](https://code.visualstudio.com/docs/getstarted/settings), a następnie odwołaj się do [zmiennych](https://code.visualstudio.com/docs/editor/variables-reference) , aby uzyskać informacje o obsłudze wstępnie zdefiniowanych zmiennych.

## <a name="open-the-azure-hdinsight-configuration"></a>Otwórz konfigurację usługi Azure HDInsight

1. Najpierw otwórz folder, aby utworzyć ustawienia obszaru roboczego.
2. Naciśnij **klawisze Ctrl + Shift + P** lub przejdź do **widoku**  ->  **paleta poleceń...** , aby wyświetlić wszystkie polecenia.
3. **Konfiguracja zestawu** wyszukiwania.
4. Rozwiń węzeł **rozszerzenia** w lewym katalogu i wybierz pozycję **Konfiguracja usługi HDInsight**.

 ![obraz konfiguracji HDI](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>Ustawienia ogólne   

|  Właściwość   | Domyślne | Opis   |
| ----- | ----- |----- |
| HDInsight: środowisko platformy Azure | Azure | Środowisko platformy Azure |
| HDInsight: wyłączanie linku Otwórz ankietę | Zaznaczono | Włącz/Wyłącz otwieranie ankiety usługi HDInsight |
| HDInsight: Włączanie instalacji pomijania Pyspark | Niezaznaczone | Włączenie/wyłączenie instalacji pyspark pomijania |
| HDInsight: Włącz porady dotyczące logowania | Niezaznaczone | Po zaznaczeniu tej opcji po zalogowaniu się na platformie Azure pojawi się monit. |
| HDInsight: Poprzednia wersja rozszerzenia | Wyświetl numer wersji bieżącego rozszerzenia | Pokaż poprzednią wersję rozszerzenia|
| HDInsight: Rodzina czcionek wyników | -Apple-system, BlinkMacSystemFont, Segoe UI WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid Sans, sans-serif | Ustawianie rodziny czcionek dla siatki wyników; Ustaw wartość pustą, aby używać czcionki edytora |
| HDInsight: rozmiar czcionki wyników | 13 |Ustaw rozmiar czcionki dla wyników GIRD; Ustaw wartość pustą, aby użyć rozmiaru edytora |
| Klaster usługi HDInsight: połączony klaster | -- | Adresy URL połączonych klastrów. Można również edytować plik JSON w celu ustawienia |
| Gałąź usługi HDInsight: Zastosuj lokalizację | Niezaznaczone | Obowiązkowe Opcje konfiguracji na potrzeby lokalizowania w skonfigurowanych ustawieniach regionalnych programu vscode (należy ponownie uruchomić programu vscode, aby ustawienia zaczęły obowiązywać)|
| Gałąź usługi HDInsight: Kopiuj nagłówki dołączenia | Niezaznaczone | Obowiązkowe Opcja konfiguracji służąca do kopiowania wyników z widoku wyników |
| Gałąź usługi HDInsight: Kopiuj Usuń nowy wiersz | Zaznaczono | Obowiązkowe Opcje konfiguracji kopiowania wielowierszowych wyników z widoku wyników |
| Format › usługi HDInsight Hive: Wyrównuje definicje kolumn w kolumnach | Niezaznaczone | Czy definicja kolumny powinna być wyrównana |
| Format › Hive usługi HDInsight: wielkość liter | brak | Czy typy danych powinny być sformatowane jako wielkie litery, małe litery, czy nie (niesformatowane) |
| Format › usługi HDInsight Hive: wielkość liter słowa kluczowego | brak | Czy słowa kluczowe mają być sformatowane jako wielkie litery, małe litery, czy nie (niesformatowane) |
| Format › Hive usługi HDInsight: Umieść przecinki przed następną instrukcją | Niezaznaczone | Należy umieścić przecinki na początku każdej instrukcji na liście na przykład ', ', ' ' zamiast na końcu '|
| Format › usługi HDInsight Hive: Umieść odwołania instrukcji SELECT w nowym wierszu | Niezaznaczone | Czy odwołania do obiektów w instrukcji SELECT mają być dzielone na oddzielne wiersze? Na przykład dla opcji "SELECT C1, C2 z T1" zarówno C1, jak i C2 będą znajdować się w osobnych wierszach
| Gałąź usługi HDInsight: informacje debugowania dziennika | Niezaznaczone | Obowiązkowe Rejestruj dane wyjściowe debugowania do konsoli VS Code (przełącznik pomocy > Narzędzia deweloperskie) 
| Gałąź usługi HDInsight: wiadomości są domyślnie otwarte | Zaznaczono | Wartość true, aby okienko wiadomości było domyślnie otwarte; wartość false dla zamkniętego|
| Gałąź usługi HDInsight: Rodzina czcionek wyników | -Apple-system, BlinkMacSystemFont, Segoe UI WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid Sans, sans-serif | Ustawianie rodziny czcionek dla siatki wyników; Ustaw wartość pustą, aby używać czcionki edytora |
| Gałąź usługi HDInsight: rozmiar czcionki wyników | 13 | Ustaw rozmiar czcionki w siatce wyników; Ustaw wartość pustą, aby użyć rozmiaru edytora |
| Usługa HDInsight Hive › Zapisz jako plik CSV: Dołącz nagłówki | Zaznaczono | Obowiązkowe W przypadku wartości true nagłówki kolumn są uwzględniane podczas zapisywania wyników w formacie CSV |
| Gałąź usługi HDInsight: skróty | -- | Skróty dotyczące okna wyników |
| Gałąź usługi HDInsight: wyświetlanie czasu partii| Niezaznaczone | Obowiązkowe Czas wykonywania powinien być pokazywany dla poszczególnych partii |
| Gałąź usługi HDInsight: wybór okienka dzielonego | dalej | Obowiązkowe Opcje konfiguracji, dla których będą otwierane nowe okienka wyników w kolumnie |
| Przesyłanie zadania usługi HDInsight: klaster conf | -- | Konfiguracja klastra |
| Przesłane zadanie usługi HDInsight: usługi Livy | -- | Konfiguracja usługi Livy. Księgowanie/partie |
| HDInsight Jupyter: Dołączanie wyników| Zaznaczono | Czy dołączać wyniki do okna wyników, a także czyścić i wyświetlać. |
| HDInsight Jupyter: Języki | -- | Ustawienia domyślne dla każdego języka. |
| Dziennik usługi HDInsight Jupyter ›: verbose | Niezaznaczone | W przypadku włączenia pełnego rejestrowania |
| Notes usługi HDInsight Jupyter ›: argumenty uruchamiania | Może dodać element | argumenty wiersza polecenia "Notes Jupyter". Każdy argument jest osobnym elementem tablicy. Aby uzyskać pełną listę typu "Notes Jupyter — pomoc" w oknie terminalu. |
| Notes usługi HDInsight Jupyter ›: folder startowy | $ {workspaceRoot} |-- |
| HDInsight Jupyter: włączono rozszerzenie języka Python | Zaznaczono | Przy przesyłaniu interaktywnych zadań pySpark Użyj języka Python-Interactive-Window rozszerzenia MS-Python. W przeciwnym razie użyj własnego okna Jupyter |
| HDInsight Spark.NET: 7z | C:\Program Files\7-Zip | <ścieżkę do 7z.exe> |
| HDInsight Spark.NET: HADOOP_HOME | D:\winutils | <ścieżka do bin\winutils.exe> tylko system operacyjny Windows |
| HDInsight Spark.NET: JAVA_HOME | C:\Program Files\Java\ jdk1.8.0_201 \ | Ścieżka do strony głównej Java|
| HDInsight Spark.NET: SCALA_HOME | C:\Program Files (x86) \scala\ | Ścieżka do strony głównej Scala |
| HDInsight Spark.NET: SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Ścieżka do narzędzia Spark Home |
| Hive: utrwalanie kart wyników zapytania | Niezaznaczone | PersistQueryResultTabs Hive |
| Gałąź: wybór okienka podziału | dalej | Obowiązkowe Opcje konfiguracji, dla których będą otwierane nowe okienka wyników w kolumnie |
| Hive Interactive: Kopiuj folder wykonywalny | Niezaznaczone | Jeśli Skopiuj folder środowiska uruchomieniowego usługi Hive w wersji interaktywnej do folderu tmp użytkownika |
| Serwer interaktywny HQL: Port otoki | 13424 | Port usługi interaktywnej Hive |
| Serwer języka HQL: Port otoki języka | 12342 | Serwery portów usługi języka Hive nasłuchują. |
| Serwer języka HQL: Maksymalna liczba problemów | 100 | Określa maksymalną liczbę problemów generowanych przez serwer. |
| Synapse Spark COMPUTE: Synapse Spark COMPUTE Azure Environment | puste | środowisko Azure Synapse Spark COMPUTE |
| Przesyłanie zadania puli Synapse Spark: usługi Livy conf | -- | Konfiguracja usługi Livy. Księgowanie/partie
| Przesyłanie zadania puli Spark Synapse: Synapse w klastrze puli Spark | -- | Konfiguracja puli Spark Synapse |


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat usługi Azure HDInsight dla programu programu vscode, zobacz [gałąź & platformy Spark dla narzędzi Visual Studio Code](https://docs.microsoft.com/sql/big-data-cluster/spark-hive-tools-vscode).
- Film przedstawiający korzystanie z usługi Spark & Hive dla Visual Studio Code można znaleźć w temacie [gałąź & platformy Spark dla Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).