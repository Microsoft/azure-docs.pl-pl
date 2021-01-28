---
title: Uruchamianie Apache Hadoop przykładów MapReduce w usłudze HDInsight — Azure
description: Rozpocznij korzystanie z przykładów MapReduce w plikach jar zawartych w usłudze HDInsight. Użyj protokołu SSH do nawiązania połączenia z klastrem, a następnie użyj polecenia Hadoop do uruchomienia przykładowych zadań.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 5e75a39b1f9e8503097914b0c9e735915f9ae667
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943222"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Uruchamianie przykładów MapReduce zawartych w usłudze HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Dowiedz się, jak uruchamiać przykłady MapReduce dołączone do Apache Hadoop w usłudze HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Przykłady MapReduce

Przykłady znajdują się w klastrze usługi HDInsight pod adresem `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` . Kod źródłowy tych przykładów znajduje się w klastrze usługi HDInsight pod adresem `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples` .

Następujące przykłady są zawarte w tym archiwum:

|Sample |Opis |
|---|---|
|aggregatewordcount|Zlicza wyrazy w plikach wejściowych.|
|aggregatewordhist|Oblicza histogram słów w plikach wejściowych.|
|bbp|Używa Bailey-Borwein-Plouffe, aby obliczyć dokładne cyfry liczby pi.|
|dbcount|Zlicza dzienniki pageview przechowywane w bazie danych.|
|distbbp|Używa formuły typu BBP, aby obliczyć dokładne bity liczby pi.|
|grep|Zlicza dopasowania wyrażenia regularnego w danych wejściowych.|
|join|Wykonuje sprzężenie przez posortowane, równo partycjonowane zestawy danych.|
|multifilewc|Zlicza wyrazy z kilku plików.|
|pentomino|Program do rozmieszczania kafelków, aby znaleźć rozwiązania do pentomino problemów.|
|przetwarzania|Szacuje pi przy użyciu metody quasi-Monte Carlo.|
|randomtextwriter|Zapisuje 10 GB losowych danych tekstowych na węzeł.|
|randomwriter|Zapisuje 10 GB danych losowych na węzeł.|
|secondarysort|Definiuje dodatkowe sortowanie do fazy zmniejszania.|
|sort|Sortuje dane zapisywane przez losowy moduł zapisujący.|
|sudoku|Sudoku.|
|teragen|Generuj dane dla terasort.|
|terasort|Uruchom terasort.|
|teravalidate|Sprawdzanie wyników terasort.|
|WORDCOUNT|Zlicza wyrazy w plikach wejściowych.|
|wordmean|Zlicza średnią długość wyrazów w plikach wejściowych.|
|wordmedian|Zlicza średnią długość wyrazów w plikach wejściowych.|
|wordstandarddeviation|Zlicza odchylenie standardowe długości wyrazów w plikach wejściowych.|

## <a name="run-the-wordcount-example"></a>Uruchamianie przykładu WORDCOUNT

1. Nawiązywanie połączenia z usługą HDInsight przy użyciu protokołu SSH. Zamień `CLUSTER` na nazwę klastra, a następnie wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. W sesji SSH Użyj następującego polecenia, aby wyświetlić listę przykładów:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    To polecenie generuje listę przykładów z poprzedniej sekcji tego dokumentu.

3. Użyj poniższego polecenia, aby uzyskać pomoc dotyczącą konkretnego przykładu. W tym przypadku przykład **WORDCOUNT** :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Zostanie wyświetlony następujący komunikat:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Ten komunikat oznacza, że można podać kilka ścieżek wejściowych dla dokumentów źródłowych. Końcowa ścieżka to miejsce, w którym przechowywane są dane wyjściowe (liczba wyrazów w dokumentach źródłowych).

4. Użyj następujących elementów, aby obliczyć wszystkie słowa w notesach Leonardo da Vinci, które są dostarczane jako przykładowe dane do klastra:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Dane wejściowe dla tego zadania są odczytywane z `/example/data/gutenberg/davinci.txt` . Dane wyjściowe tego przykładu są przechowywane w `/example/data/davinciwordcount` . Obie ścieżki znajdują się w domyślnym magazynie dla klastra, a nie w lokalnym systemie plików.

   > [!NOTE]  
   > Jak wskazano w pomocy dla przykładu WORDCOUNT, można również określić wiele plików wejściowych. Na przykład `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` Liczba wyrazów w obu davinci.txt i ulysses.txt.

5. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić dane wyjściowe:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    To polecenie łączy wszystkie pliki wyjściowe wytwarzane przez zadanie. Dane wyjściowe są wyświetlane w konsoli programu. Dane wyjściowe będą podobne do następującego tekstu:

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    Każdy wiersz reprezentuje słowo i liczbę przypadków, w których wystąpiły dane wejściowe.

## <a name="the-sudoku-example"></a>Przykład Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) to logika logiki składająca się z dziewięciu siatek 3x3. Niektóre komórki w siatce mają liczby, a inne są puste i celem jest rozwiązanie dla pustych komórek. Poprzedni link zawiera więcej informacji na temat układanki, ale celem tego przykładu jest rozwiązanie dla pustych komórek. Dlatego nasze dane wejściowe powinny być plikami w następującym formacie:

* Dziewięć wierszy dziewięciu kolumn
* Każda kolumna może zawierać liczbę lub `?` (która wskazuje pustą komórkę)
* Komórki są rozdzielone spacjami

Istnieje pewien sposób konstruowania Sudoku układanki; nie można powtórzyć liczby w kolumnie lub wierszu. Istnieje przykład w klastrze usługi HDInsight, który jest prawidłowo skonstruowany. Znajduje się on pod adresem `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` i zawiera następujący tekst:

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

Aby uruchomić ten przykładowy problem za pomocą przykładu Sudoku, użyj następującego polecenia:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Wyniki wyglądają podobnie do następującego tekstu:

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>Przykład PI (π)

Przykład pi używa metody statystycznej (quasi-Monte Carlo) do oszacowania wartości liczby pi. Punkty są umieszczane losowo w kwadracie Unit. Kwadrat zawiera również okrąg. Prawdopodobieństwo, że punkty mieszczą się w okręgu, jest równe powierzchni okręgu, PI/4. Wartość pi można oszacować na podstawie wartości 4R. R jest stosunkiem liczby punktów, które znajdują się w okręgu, do całkowitej liczby punktów w kwadracie. Im większy przykład używanych punktów, tym lepsze jest oszacowanie.

Użyj następującego polecenia, aby uruchomić ten przykład. To polecenie używa 16 map z 10 000 000 próbek każdego do oszacowania wartości pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Wartość zwracana przez to polecenie jest podobna do **3.14159155000000000000**. W przypadku odwołań pierwsze 10 miejsc dziesiętnych Pi to 3,1415926535.

## <a name="10-gb-graysort-example"></a>przykład 10 GB GraySort

GraySort jest sortowaniem porównawczym. Metryka jest szybkością sortowania (TB/minutę), która jest osiągana podczas sortowania dużych ilości danych, zazwyczaj minimum 100 TB.

Ten przykład używa umiarkowanie 10 GB danych, aby można go było uruchomić stosunkowo szybko. Używa aplikacji MapReduce opracowanych przez Owen O'Malley i Arun Murthy. Te aplikacje zdobyły roczny wynik sortowania ("Daytona") terabajt w 2009 z częstotliwością 0,578 TB/min (100 TB w ciągu 173 minut). Aby uzyskać więcej informacji na temat tego i innych testów porównawczych, zobacz stronę [porównawczą sortowania](https://sortbenchmark.org/) .

Ten przykład używa trzech zestawów programów MapReduce:

* **TeraGen**: program MapReduce, który generuje wiersze danych do sortowania

* **TeraSort**: próbkuje dane wejściowe i używa MapReduce do sortowania danych w całkowitej kolejności

    TeraSort jest standardowym sortowaniem MapReduce, z wyjątkiem niestandardowych partycji. Program Partitioner używa posortowanej listy kluczy próbkowanych N-1, które definiują zakres kluczy dla każdego zmniejszenia. W szczególności wszystkie klucze takie jak przykład [i-1] <= Key < przykład [i] są wysyłane, aby zmniejszyć i. Ta partycja gwarantuje, że dane wyjściowe zmniejszenia i są mniejsze niż wyniki zmniejszenia i + 1.

* **TeraValidate**: program MapReduce, który sprawdza, czy dane wyjściowe są sortowane globalnie

    Tworzy jedną mapę na plik w katalogu wyjściowym, a każda Mapa gwarantuje, że każdy klucz jest mniejszy lub równy poprzedniemu. Funkcja map generuje rekordy z pierwszego i ostatniego klucza każdego pliku. Funkcja Zmniejsz zapewnia, że pierwszy klucz pliku jest większy niż ostatni klucz pliku i-1. Wszelkie problemy są raportowane jako dane wyjściowe fazy zmniejszania z nieuporządkowanymi kluczami.

Wykonaj następujące kroki, aby wygenerować dane, sortować, a następnie sprawdzić poprawność danych wyjściowych:

1. Wygeneruj 10 GB danych, które są przechowywane w domyślnym magazynie klastra usługi HDInsight w lokalizacji `/example/data/10GB-sort-input` :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks`Wskazuje, ile zadań mapy ma używać dla tego zadania. Ostatnie dwa parametry instruują zadanie, aby utworzyć 10 GB danych i przechowywać ją w lokalizacji `/example/data/10GB-sort-input` .

2. Użyj następującego polecenia, aby posortować dane:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    Zawiera informacje o tym, `-Dmapred.reduce.tasks` jak wiele zmniejszy zadania do użycia w ramach zadania. Ostatnie dwa parametry są tylko lokalizacjami wejściowymi i wyjściowymi danych.

3. Aby sprawdzić dane wygenerowane przez sortowanie, wykonaj następujące czynności:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób uruchamiania przykładów dołączonych do klastrów usługi HDInsight opartych na systemie Linux. Samouczki dotyczące używania świń, Hive i MapReduce z usługą HDInsight można znaleźć w następujących tematach:

* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
