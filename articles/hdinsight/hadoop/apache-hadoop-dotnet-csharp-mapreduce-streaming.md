---
title: Używanie języka C# z MapReduce na platformie Hadoop w usłudze HDInsight — Azure
description: Dowiedz się, jak używać języka C# do tworzenia rozwiązań MapReduce z Apache Hadoop w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 8093db9c53d9c34014d1b315d53539b3f2cffb30
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866713"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Używanie języka C# z MapReduce przesyłania strumieniowego na Apache Hadoop w usłudze HDInsight

Dowiedz się, jak za pomocą języka C# utworzyć rozwiązanie MapReduce w usłudze HDInsight.

Apache Hadoop Streaming umożliwia uruchamianie zadań MapReduce przy użyciu skryptu lub pliku wykonywalnego. W tym miejscu platforma .NET służy do implementowania mapowania i redukcji dla rozwiązania zliczania wyrazów.

## <a name="net-on-hdinsight"></a>Platforma .NET w usłudze HDInsight

Klastry usługi HDInsight wykorzystują [mono ( https://mono-project.com) ](https://mono-project.com) do uruchamiania aplikacji platformy .NET). W usłudze HDInsight w wersji 3,6 jest dołączony system mono w wersji. Aby uzyskać więcej informacji na temat wersji programu mono zawartej w usłudze HDInsight, zobacz [składniki Apache Hadoop dostępne w wersji usługi HDInsight](../hdinsight-component-versioning.md).

Aby uzyskać więcej informacji na temat zgodności z programem mono z wersjami .NET Framework, zobacz Zgodność z usługą [mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Jak działa przesyłanie strumieniowe Hadoop

Podstawowy proces używany do przesyłania strumieniowego w tym dokumencie jest następujący:

1. Usługa Hadoop przekazuje dane do mapowania (*mapper.exe* w tym przykładzie) na stdin.
2. Maper przetwarza dane i emituje rozdzielaną tabulatorami pary klucz/wartość do STDOUT.
3. Dane wyjściowe są odczytywane przez platformę Hadoop, a następnie przekazywane do redukcji (*reducer.exe* w tym przykładzie) w stdin.
4. W ramach tego ograniczenia są odczytywane pary klucz/wartość rozdzielane znakami tabulacji, przetwarzanie danych, a następnie emitowanie wyniku jako pary klucz/wartość z ograniczeniami tabulacji.
5. Dane wyjściowe są odczytywane przez platformę Hadoop i zapisywane w katalogu wyjściowym.

Aby uzyskać więcej informacji na temat przesyłania strumieniowego, zobacz Usługa [przesyłania strumieniowego Hadoop](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Wymagania wstępne

* Programu Visual Studio.

* Znajomość pisania i kompilowania kodu w języku C#, który jest przeznaczony dla .NET Framework 4,5.

* Sposób przekazywania plików exe do klastra. Kroki opisane w tym dokumencie wykorzystują Data Lake Tools for Visual Studio do przekazywania plików do magazynu podstawowego klastra.

* W przypadku korzystania z programu PowerShell należy użyć polecenia [AZ module](/powershell/azure/).

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Schemat identyfikatora URI magazynu podstawowego klastrów. Ten schemat dotyczy `wasb://` usługi Azure Storage, `abfs://` Azure Data Lake Storage Gen2 lub `adl://` Azure Data Lake Storage Gen1. Jeśli włączono bezpieczny transfer dla usługi Azure Storage lub Data Lake Storage Gen2, identyfikator URI będzie `wasbs://` odpowiednio lub `abfss://` .

## <a name="create-the-mapper"></a>Tworzenie mapowania

W programie Visual Studio Utwórz nową aplikację konsolową .NET Framework o nazwie *Maper*. Użyj następującego kodu dla aplikacji:

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

Po utworzeniu aplikacji Skompiluj ją, aby utworzyć plik *mapper.exe/bin/debug/* w katalogu projektu.

## <a name="create-the-reducer"></a>Utwórz ograniczenie

W programie Visual Studio Utwórz nową aplikację konsolową .NET Framework o nazwie *zmniejszającej*. Użyj następującego kodu dla aplikacji:

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

Po utworzeniu aplikacji Skompiluj ją, aby utworzyć plik *reducer.exe/bin/debug/* w katalogu projektu.

## <a name="upload-to-storage"></a>Przekazywanie do magazynu

Następnie musisz przekazać do magazynu usługi HDInsight aplikacje do *odnajdywania* i *zmniejszania* .

1. W programie Visual Studio wybierz pozycję **Wyświetl**  >  **Eksplorator serwera**.

1. Kliknij prawym przyciskiem myszy pozycję **Azure**, wybierz pozycję **Połącz z subskrypcją Microsoft Azure...** i Ukończ proces logowania.

1. Rozwiń klaster usługi HDInsight, do którego chcesz wdrożyć tę aplikację. Zostanie wyświetlona pozycja z tekstem **(domyślne konto magazynu)** .

   :::image type="content" source="./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png" alt-text="Konto magazynu, klaster usługi HDInsight, Eksplorator serwera, Visual Studio" border="true":::

   * Jeśli wpis **(domyślne konto magazynu)** można rozszerzyć, używasz **konta usługi Azure Storage** jako magazynu domyślnego dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, rozwiń wpis, a następnie kliknij dwukrotnie **(kontener domyślny)**.

   * Jeśli nie można rozszerzyć wpisu **(domyślnego konta magazynu)** , używasz **Azure Data Lake Storage** jako domyślnego magazynu dla klastra. Aby wyświetlić pliki w domyślnym magazynie klastra, kliknij dwukrotnie wpis **(domyślne konto magazynu)** .

1. Aby przekazać pliki. exe, należy użyć jednej z następujących metod:

    * Jeśli używasz **konta usługi Azure Storage**, wybierz ikonę **Przekaż obiekt BLOB** .

        :::image type="content" source="./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png" alt-text="Ikona przekazywania do usługi HDInsight dla programu mapowania, Visual Studio" border="true":::

        W oknie dialogowym **Przekaż nowy plik** w obszarze **Nazwa pliku** wybierz pozycję **Przeglądaj**. W oknie dialogowym **przekazywanie obiektu BLOB** przejdź do folderu *bin\Debug* dla projektu *mapowania* , a następnie wybierz plik *mapper.exe* . Na koniec wybierz pozycję **Otwórz** , a następnie kliknij **przycisk OK** , aby ukończyć przekazywanie.

    * W przypadku **Azure Data Lake Storage** kliknij prawym przyciskiem myszy pusty obszar na liście plików, a następnie wybierz polecenie **Przekaż**. Na koniec wybierz plik *mapper.exe* a następnie wybierz pozycję **Otwórz**.

    Po zakończeniu przekazywania *mapper.exe* Powtórz proces przesyłania pliku *reducer.exe* .

## <a name="run-a-job-using-an-ssh-session"></a>Uruchamianie zadania: używanie sesji SSH

Poniższa procedura opisuje sposób uruchamiania zadania MapReduce przy użyciu sesji SSH:

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Użyj jednego z następujących poleceń, aby uruchomić zadanie MapReduce:

   * Jeśli domyślnym magazynem jest **usługa Azure Storage**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Jeśli domyślnym magazynem jest **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Jeśli domyślnym magazynem jest **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   Na poniższej liście opisano, co każdy parametr i opcja reprezentuje:

   |Parametr | Opis |
   |---|---|
   |Hadoop-Streaming. jar|Określa plik JAR, który zawiera funkcje MapReduce przesyłania strumieniowego.|
   |-Files|Określa *mapper.exe* i *reducer.exe* plików dla tego zadania. `wasbs:///` `adl:///` Deklaracja protokołu, lub `abfs:///` przed każdym plikiem jest ścieżką do katalogu głównego magazynu domyślnego dla klastra.|
   |-Maper|Określa plik, który implementuje mapowanie.|
   |-Redukcja|Określa plik, który implementuje zmniejszenie.|
   |-danych wejściowych|Określa dane wejściowe.|
   |-Output|Określa katalog wyjściowy.|

1. Po zakończeniu zadania MapReduce Użyj następującego polecenia, aby wyświetlić wyniki:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   Następujący tekst to przykład danych zwróconych przez to polecenie:

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

Użyj poniższego skryptu programu PowerShell, aby uruchomić zadanie MapReduce i pobrać wyniki.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Ten skrypt poprosi o nazwę i hasło konta logowania do klastra wraz z nazwą klastra usługi HDInsight. Po zakończeniu zadania dane wyjściowe zostaną pobrane do pliku o nazwie *output.txt*. Następujący tekst to przykład danych w `output.txt` pliku:

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

* [Użyj MapReduce w Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md).
* [Używanie funkcji zdefiniowanej przez użytkownika w języku C# z Apache Hive i Apache świni](apache-hadoop-hive-pig-udf-dotnet-csharp.md).
* [Opracowywanie programów MapReduce w języku Java](apache-hadoop-develop-deploy-java-mapreduce-linux.md)