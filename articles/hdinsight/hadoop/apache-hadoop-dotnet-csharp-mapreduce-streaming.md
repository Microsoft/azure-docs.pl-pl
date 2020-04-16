---
title: Użyj języka C# z mapreduce na Hadoop w hdinsight - Azure
description: Dowiedz się, jak używać języka C# do tworzenia rozwiązań MapReduce za pomocą apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 7a299ce16f6e9c7292cebf198c9c3077f8e05fcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417605"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Użyj języka C# z mapreduce przesyłania strumieniowego na Apache Hadoop w HDInsight

Dowiedz się, jak użyć języka C# do utworzenia rozwiązania MapReduce w programie HDInsight.

Apache Hadoop streaming pozwala na uruchamianie MapReduce zadań przy użyciu skryptu lub pliku wykonywalnego. W tym miejscu .NET jest używany do implementowania mapera i reduktora dla rozwiązania liczby wyrazów.

## <a name="net-on-hdinsight"></a>.NET na HDInsight

Klastry HDInsight używają [mono (https://mono-project.com) ](https://mono-project.com) do uruchamiania aplikacji .NET. Mono w wersji 4.2.1 jest dołączony do hdinsight w wersji 3.6. Aby uzyskać więcej informacji na temat wersji mono dołączonej do programu HDInsight, zobacz [Komponenty Apache Hadoop dostępne w różnych wersjach HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).

Aby uzyskać więcej informacji na temat zgodności mono z wersjami programu .NET Framework, zobacz [Zgodność mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak działa streaming Hadoop

Podstawowy proces używany do przesyłania strumieniowego w tym dokumencie jest następujący:

1. Hadoop przekazuje dane do mapera *(mapper.exe* w tym przykładzie) na STDIN.
2. Maper przetwarza dane i emituje pary klucz/wartość rozdzielane kartą do STDOUT.
3. Dane wyjściowe są odczytywane przez Hadoop, a następnie przekazywane do reduktora *(reduktor.exe* w tym przykładzie) na STDIN.
4. Reduktor odczytuje pary klucz/wartość rozdzielane tabulatorem, przetwarza dane, a następnie emituje wynik jako pary klucz/wartość rozdzielane kartą na STDOUT.
5. Dane wyjściowe są odczytywane przez Hadoop i zapisywane w katalogu wyjściowym.

Aby uzyskać więcej informacji na temat przesyłania strumieniowego, zobacz [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio.

* Znajomość pisania i tworzenia kodu Języka C#, który jest przeznaczony dla platformy .NET Framework 4.5.

* Sposób przekazywania plików exe do klastra. Kroki opisane w tym dokumencie używają narzędzia usługi Data Lake Tools for Visual Studio do przekazywania plików do magazynu podstawowego dla klastra.

* Jeśli korzystasz z programu PowerShell, potrzebny jest [moduł Az](https://docs.microsoft.com/powershell/azure/overview).

* Klaster Apache Hadoop w programie HDInsight. Zobacz [Wprowadzenie do HDInsight w systemie Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* [Schemat identyfikatorów URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) dla magazynu podstawowego klastrów. Ten schemat `wasb://` będzie dla `abfs://` usługi Azure Storage, dla `adl://` usługi Azure Data Lake Storage Gen2 lub dla usługi Azure Data Lake Storage Gen1. Jeśli bezpieczny transfer jest włączony dla usługi Azure Storage lub Data `wasbs://` `abfss://`Lake Storage Gen2, identyfikator URI będzie lub , odpowiednio, Zobacz też, [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

## <a name="create-the-mapper"></a>Tworzenie mapera

W programie Visual Studio utwórz nową aplikację konsoli platformy .NET Framework o nazwie *mapera*. Użyj następującego kodu dla aplikacji:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Po utworzeniu aplikacji utwórz ją w celu utworzenia pliku */bin/debug/mapper.exe* w katalogu projektu.

## <a name="create-the-reducer"></a>Tworzenie reduktora

W programie Visual Studio utwórz nową aplikację konsoli .NET Framework o nazwie *reduktor*. Użyj następującego kodu dla aplikacji:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Po utworzeniu aplikacji utwórz ją w celu utworzenia pliku */bin/debug/reducer.exe* w katalogu projektu.

## <a name="upload-to-storage"></a>Przekazywanie do magazynu

Następnie należy przesłać aplikacje *mapera* i *reduktora* do pamięci HDInsight.

1. W programie Visual Studio wybierz pozycję **Wyświetl** > **Eksploratora serwera**.

1. Kliknij prawym przyciskiem myszy **pozycję Azure**, wybierz pozycję **Połącz z subskrypcją platformy Microsoft Azure...** i ukończ proces logowania.

1. Rozwiń klaster HDInsight, do którego chcesz wdrożyć tę aplikację. Na liście znajduje się wpis z **tekstem (Domyślne konto magazynu).**

   ![Konto magazynu, klaster HDInsight, Eksplorator serwera, program Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Jeśli można rozwinąć wpis **(domyślne konto magazynu),** używasz **konta usługi Azure Storage** jako domyślnego magazynu dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, rozwiń wpis, a następnie kliknij dwukrotnie **(kontener domyślny).**

   * Jeśli nie można rozwinąć wpisu **(domyślne konto magazynu),** używasz **usługi Azure Data Lake Storage** jako domyślnego magazynu dla klastra. Aby wyświetlić pliki w magazynie domyślnym dla klastra, kliknij dwukrotnie wpis **(Domyślne konto magazynu).**

1. Aby przekazać pliki exe, użyj jednej z następujących metod:

    * Jeśli używasz **konta usługi Azure Storage,** wybierz ikonę Przekaż obiekt **blob.**

        ![Ikona przekazywania programu HDInsight dla mapera, visual studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        W oknie dialogowym **Przekazywanie nowego pliku** w obszarze **Nazwa pliku**wybierz pozycję **Przeglądaj**. W oknie dialogowym **Przekazywanie obiektów blob** przejdź do folderu *bin\debug* dla projektu *mapera,* a następnie wybierz plik *mapper.exe.* Na koniec wybierz pozycję **Otwórz,** a następnie **przycisk OK,** aby zakończyć przesyłanie.

    * W przypadku **usługi Azure Data Lake Storage**kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz polecenie **Przekaż**. Na koniec wybierz plik *mapper.exe,* a następnie wybierz pozycję **Otwórz**.

    Po zakończeniu przekazywania *pliku mapper.exe* powtórz proces przekazywania pliku *redukator.exe.*

## <a name="run-a-job-using-an-ssh-session"></a>Uruchamianie zadania: korzystanie z sesji SSH

W poniższej procedurze opisano sposób uruchamiania zadania MapReduce przy użyciu sesji SSH:

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aby uruchomić zadanie MapReduce, użyj jednego z następujących poleceń:

   * Jeśli domyślnym magazynem jest **usługa Azure Storage:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Jeśli domyślnym magazynem jest **Data Lake Storage Gen1:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Jeśli domyślnym magazynem jest **Data Lake Storage Gen2:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   Na poniższej liście opisano, co reprezentuje każdy parametr i opcja:

   |Parametr | Opis |
   |---|---|
   |hadoop-streaming.jar|Określa plik jar, który zawiera funkcję map mapreduce przesyłania strumieniowego.|
   |-pliki|Określa pliki *mapper.exe* i *reducer.exe* dla tego zadania. `wasbs:///`Deklaracja `adl:///`, `abfs:///` lub protokołu przed każdym plikiem jest ścieżką do katalogu głównego domyślnego magazynu dla klastra.|
   |-mapper|Określa plik, który implementuje mapera.|
   |-reduktor|Określa plik, który implementuje reduktor.|
   |-danych wejściowych|Określa dane wejściowe.|
   |-wyjście|Określa katalog wyjściowy.|

1. Po zakończeniu zadania MapReduce użyj następującego polecenia, aby wyświetlić wyniki:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   Poniższy tekst jest przykładem danych zwróconych przez to polecenie:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Uruchamianie zadania: korzystanie z programu PowerShell

Użyj następującego skryptu programu PowerShell, aby uruchomić zadanie MapReduce i pobrać wyniki.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ten skrypt monituje o podanie nazwy konta logowania klastra i hasła wraz z nazwą klastra HDInsight. Po zakończeniu zadania dane wyjściowe są pobierane do pliku o nazwie *output.txt*. Poniższy tekst jest przykładem danych `output.txt` w pliku:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z MapReduce z HDInsight, zobacz [Korzystanie MapReduce w Apache Hadoop na HDInsight](hdinsight-use-mapreduce.md).

Aby uzyskać informacje na temat używania języka C# z hive i pig, zobacz [Użyj funkcji zdefiniowanej przez użytkownika języka C# z Apache Hive i Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Aby uzyskać informacje na temat używania języka C# z burzą w programie HDInsight, zobacz [Rozwiń topologie języka C# dla usługi Apache Storm w programie HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
