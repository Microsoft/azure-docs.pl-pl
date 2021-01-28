---
title: C#, Apache Hive & Apache chlewnej w Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak używać funkcji języka C# zdefiniowanej przez użytkownika (UDF) z Apache Hive i Apache chlewnej streaming w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: 97b23a2b9e3b95a5ea0efcd27d0ec185b1c456f1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946555"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Korzystanie z funkcji zdefiniowanych przez użytkownika w języku C# przy użyciu Apache Hive i Apache chlewnej na Apache Hadoop w usłudze HDInsight

Dowiedz się, jak używać funkcji języka C# zdefiniowanej przez użytkownika (UDF) z usługami [Apache Hive](https://hive.apache.org) i [Apache chlewnej](https://pig.apache.org) w usłudze HDInsight.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie współpracują z klastrami usługi HDInsight opartymi na systemie Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji składników usługi HDInsight](../hdinsight-component-versioning.md).

Zarówno Hive, jak i świnie mogą przekazywać dane do aplikacji zewnętrznych do przetworzenia. Ten proces jest nazywany _przesyłaniem strumieniowym_. W przypadku korzystania z aplikacji .NET dane są przesyłane do aplikacji w STDIN, a aplikacja zwraca wyniki na STDOUT. Aby odczytać i zapisać dane z STDIN i STDOUT, można użyć `Console.ReadLine()` i `Console.WriteLine()` z aplikacji konsolowej.

## <a name="prerequisites"></a>Wymagania wstępne

* Znajomość pisania i kompilowania kodu w języku C#, który jest przeznaczony dla .NET Framework 4,5.

    Używaj dowolnego środowiska IDE. Zalecamy korzystanie z [programu Visual Studio](https://www.visualstudio.com/vs) lub [Visual Studio Code](https://code.visualstudio.com/). W procedurach przedstawionych w tym dokumencie użyto programu Visual Studio 2019.

* Sposób przekazywania plików exe do klastra i uruchamiania zadań dotyczących trzody chlewnej i Hive. Zalecamy [Data Lake narzędzi dla programu Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md), [Azure PowerShell](/powershell/azure)i [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Kroki opisane w tym dokumencie wykorzystują Data Lake Tools for Visual Studio do przekazywania plików i uruchamiania przykładowego zapytania Hive.

    Aby uzyskać informacje na temat innych sposobów uruchamiania zapytań programu Hive, zobacz [co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](hdinsight-use-hive.md).

* Usługa Hadoop w klastrze usługi HDInsight. Aby uzyskać więcej informacji na temat tworzenia klastra, zobacz [Tworzenie klastrów usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>Platforma .NET w usłudze HDInsight

Klastry *HDInsight oparte na systemie Linux* używają systemu [ https://mono-project.com) mono (](https://mono-project.com) do uruchamiania aplikacji .NET. W usłudze HDInsight w wersji 3,6 jest dołączony system mono w wersji.

Aby uzyskać więcej informacji na temat zgodności z programem mono z wersjami .NET Framework, zobacz Zgodność z usługą [mono](https://www.mono-project.com/docs/about-mono/compatibility/).

Aby uzyskać więcej informacji na temat wersji .NET Framework i narzędzia mono dołączonego do wersji usługi HDInsight, zobacz [wersje składników usługi HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Tworzenie projektów C \#

W poniższych sekcjach opisano sposób tworzenia projektu w języku C# w programie Visual Studio dla Apache Hive UDF i UDF świni Apache.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Aby utworzyć projekt C# dla Apache Hive UDF:

1. Uruchom program Visual Studio.

2. Wybierz pozycję **Utwórz nowy projekt**.

3. W oknie **Tworzenie nowego projektu** wybierz szablon **aplikacja konsoli (.NET Framework)** (wersja języka C#). Następnie wybierz przycisk **Dalej**.

4. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu** *HiveCSharp*, a następnie przejdź do lokalizacji lub Utwórz **lokalizację** , w której ma zostać zapisany nowy projekt. Następnie wybierz pozycję **Utwórz**.

5. W środowisku IDE programu Visual Studio Zastąp zawartość *program.cs* następującym kodem:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. Na pasku menu wybierz kolejno opcje **Kompiluj** kompilacje  >  **rozwiązanie** , aby skompilować projekt.

7. Zamknij rozwiązanie.

### <a name="apache-pig-udf"></a>UDF świni Apache

Aby utworzyć projekt C# dla Apache Hive UDF:

1. Otwórz program Visual Studio.

2. W oknie **uruchamiania** wybierz pozycję **Utwórz nowy projekt**.

3. W oknie **Tworzenie nowego projektu** wybierz szablon **aplikacja konsoli (.NET Framework)** (wersja języka C#). Następnie wybierz przycisk **Dalej**.

4. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu** *PigUDF*, a następnie przejdź do lub Utwórz **lokalizację** , w której ma zostać zapisany nowy projekt. Następnie wybierz pozycję **Utwórz**.

5. W środowisku IDE programu Visual Studio Zastąp zawartość *program.cs* następującym kodem:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Ten kod analizuje wiersze wysyłane z świni i ponownie formatuje linie, które zaczynają się od `java.lang.Exception` .

6. Na pasku menu wybierz polecenie **Kompiluj**  >  **kompilację rozwiązania** , aby skompilować projekt.

7. Pozostaw otwarte rozwiązanie.

## <a name="upload-to-storage"></a>Przekazywanie do magazynu

Następnie należy przekazać aplikacje Hive i UDF do magazynu w klastrze usługi HDInsight.

1. W programie Visual Studio przejdź do **widoku**  >  **Eksplorator serwera**.

1. W **Eksplorator serwera** kliknij prawym przyciskiem myszy pozycję **Azure**, wybierz pozycję **Połącz z subskrypcją Microsoft Azure** i Ukończ proces logowania.

1. Rozwiń klaster usługi HDInsight, do którego chcesz wdrożyć tę aplikację. Zostanie wyświetlona pozycja z tekstem **(domyślne konto magazynu)** .

    ![Domyślne konto magazynu, klaster usługi HDInsight, Eksplorator serwera](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Jeśli ten wpis można rozszerzyć, używasz **konta usługi Azure Storage** jako magazynu domyślnego dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, rozwiń wpis, a następnie kliknij dwukrotnie **(kontener domyślny)**.

    * Jeśli tego wpisu nie można rozszerzyć, używany jest **Azure Data Lake Storage** jako magazyn domyślny dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, kliknij dwukrotnie wpis **(domyślne konto magazynu)** .

1. Aby przekazać pliki. exe, należy użyć jednej z następujących metod:

    * Jeśli używasz **konta usługi Azure Storage**, wybierz ikonę **Przekaż obiekt BLOB** .

        ![Ikona przekazywania do usługi HDInsight dla nowego projektu](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        W oknie dialogowym **Przekaż nowy plik** w obszarze **Nazwa pliku** wybierz pozycję **Przeglądaj**. W oknie dialogowym **przekazywanie obiektu BLOB** przejdź do folderu *bin\Debug* projektu *HiveCSharp* , a następnie wybierz plik *HiveCSharp.exe* . Na koniec wybierz pozycję **Otwórz** , a następnie kliknij **przycisk OK** , aby ukończyć przekazywanie.

    * Jeśli używasz **Azure Data Lake Storage**, kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz polecenie **Przekaż**. Na koniec wybierz plik *HiveCSharp.exe* i wybierz pozycję **Otwórz**.

    Po zakończeniu przekazywania *HiveCSharp.exe* Powtórz proces przesyłania pliku *PigUDF.exe* .

## <a name="run-an-apache-hive-query"></a>Uruchamianie zapytania Apache Hive

Teraz można uruchomić zapytanie programu Hive korzystające z aplikacji w formacie UDF.

1. W programie Visual Studio przejdź do **widoku**  >  **Eksplorator serwera**.

2. Rozwiń węzeł **Azure**, a następnie rozwiń węzeł **HDInsight**.

3. Kliknij prawym przyciskiem myszy klaster, w którym wdrożono aplikację *HiveCSharp* , a następnie wybierz polecenie **Napisz zapytanie Hive**.

4. Użyj następującego tekstu zapytania programu Hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Usuń komentarz z `add file` instrukcji, która jest zgodna z typem domyślnego magazynu używanego w klastrze.

    To zapytanie wybiera `clientid` pola, `devicemake` , i `devicemodel` z `hivesampletable` , a następnie przekazuje pola do aplikacji *HiveCSharp.exe* . Zapytanie oczekuje, że aplikacja zwróci trzy pola, które są przechowywane jako `clientid` , `phoneLabel` i `phoneHash` . Zapytanie oczekuje również na znalezienie *HiveCSharp.exe* w katalogu głównym domyślnego kontenera magazynu.

5. Zmień wartość domyślną **interaktywny** na **Batch**, a następnie wybierz pozycję **Prześlij** , aby przesłać zadanie do klastra usługi HDInsight. Zostanie otwarte okno **podsumowania zadania Hive** .

6. Wybierz pozycję **Odśwież** , aby odświeżyć podsumowanie do czasu **zakończenia** zmiany **stanu zadania** . Aby wyświetlić dane wyjściowe zadania, wybierz pozycję **dane wyjściowe zadania**.

## <a name="run-an-apache-pig-job"></a>Uruchamianie zadania Apache świni

Możesz również uruchomić zadanie Świniowe korzystające z aplikacji do UDF.

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. (Na przykład uruchom polecenie `ssh sshuser@<clustername>-ssh.azurehdinsight.net` ). Aby uzyskać więcej informacji, zobacz Korzystanie z protokołu [SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następującego polecenia, aby uruchomić wiersz polecenia świni:

    ```shell
    pig
    ```

    `grunt>`Zostanie wyświetlony monit.

3. Wprowadź następujące, aby uruchomić zadanie trzody chlewnej używające aplikacji .NET Framework:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    `DEFINE`Instrukcja tworzy alias `streamer` dla aplikacji *PigUDF.exe* i `CACHE` ładuje ją z magazynu domyślnego dla klastra. Później `streamer` jest używany z `STREAM` operatorem do przetwarzania pojedynczych wierszy zawartych w `LOG` i zwracają dane jako serie kolumn.

    > [!NOTE]
    > Nazwa aplikacji, która jest używana na potrzeby przesyłania strumieniowego, musi być otoczona \` znakiem (odwróconym), gdy jest on aliasem, i przez znak "(pojedynczego cudzysłowu), gdy jest używany z `SHIP` .

4. Po wprowadzeniu ostatniego wiersza zadanie powinno zostać uruchomione. Zwraca dane wyjściowe podobne do następującego tekstu:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Użyj `exit` , aby zamknąć świnie.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korzystania z aplikacji .NET Framework z usługi Hive i trzody chlewnej w usłudze HDInsight. Jeśli chcesz dowiedzieć się, jak używać języka Python z usługą Hive i świnią, zobacz [Używanie języka Python z usługami Apache Hive i Apache chlewnej w usłudze HDInsight](python-udf-hdinsight.md).

Aby poznać inne sposoby korzystania z usługi Hive i uzyskać informacje o używaniu programu MapReduce, zobacz następujące artykuły:

* [Korzystanie z programu Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
* [Podstawy dla trzody chlewnej](https://pig.apache.org/docs/latest/basic.html)
