---
title: Generowanie rekomendacji przy użyciu oprogramowania Apache Mahout w usłudze Azure HDInsight
description: Dowiedz się, jak wygenerować zalecenia dotyczące filmów za pomocą usługi HDInsight przy użyciu biblioteki usługi Machine Learning w usłudze Apache Mahout.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: c31ffaf094801bdd49e5800bd338a15d8b8315f6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946499"
---
# <a name="generate-recommendations-using-apache-mahout-in-azure-hdinsight"></a>Generowanie rekomendacji przy użyciu oprogramowania Apache Mahout w usłudze Azure HDInsight

Dowiedz się, jak generować zalecenia dotyczące filmu przy użyciu biblioteki [Apache Mahout](https://mahout.apache.org) Machine Learning z usługą Azure HDInsight.

Mahout to biblioteka [uczenia maszynowego](https://en.wikipedia.org/wiki/Machine_learning) dla Apache Hadoop. Mahout zawiera algorytmy przetwarzania danych, takie jak filtrowanie, klasyfikacja i klastrowanie. W tym artykule opisano użycie aparatu rekomendacji do generowania zaleceń dotyczących filmów, które są oparte na oglądanych przez znajomych filmach.

Aby uzyskać więcej informacji na temat wersji programu Mahout w usłudze HDInsight, zobacz [wersje usługi HDInsight i składniki Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="understanding-recommendations"></a>Omówienie zaleceń

Jedną z funkcji dostarczanych przez Mahout jest aparat rekomendacji. Ten aparat akceptuje dane w formacie `userID` , `itemId` i `prefValue` (Preferencja dla elementu). Mahout może następnie wykonać analizę wspólnego wystąpienia, aby określić: *Użytkownicy, którzy mają preferencję dla elementu, również mają preferencję dla tych innych elementów*. Mahout następnie określa użytkowników z preferencjami podobne elementy, które mogą służyć do uzyskania rekomendacji.

Poniższy przepływ pracy to uproszczony przykład, który używa danych filmowych:

* **Wspólne wystąpienie**: Jan, Alicja i Robert wszystkie Niemniej *,* *Empire uderzają z powrotem* i *zwracają Jedi*. Mahout określa, że użytkownicy, którzy lubią jeden z tych filmów, lubią również pozostałe dwa.

* **Wspólne wystąpienie**: Robert i Alicja również polubili *Menace Fantom*, *atakujący klony* i *Revenge Sith*. Mahout określa, że użytkownicy, którzy lubią poprzednie trzy filmy, również lubią te trzy filmy.

* **Zalecenie dotyczące podobieństwa**: ze względu na to, że Jan polubili pierwsze trzy filmy, Mahout przegląda filmy, które przypominają inne podobne preferencje, ale Janusz nie zobaczył (podobieństwa/sklasyfikowany). W takim przypadku Mahout zaleca *Fantom Menace*, *ataki klonów* i *Revenge Sith*.

### <a name="understanding-the-data"></a>Zrozumienie danych

Wygodnie [GroupLens Research](https://grouplens.org/datasets/movielens/) udostępnia dane klasyfikacji dla filmów w formacie zgodnym z Mahout. Te dane są dostępne w domyślnym magazynie klastra pod adresem `/HdiSamples/HdiSamples/MahoutMovieData` .

Istnieją dwa pliki `moviedb.txt` i `user-ratings.txt` . Ten `user-ratings.txt` plik jest używany podczas analizy. Służy `moviedb.txt` do udostępniania informacji tekstowych przyjaznych dla użytkownika podczas przeglądania wyników.

Dane zawarte w programie `user-ratings.txt` mają strukturę `userID` , `movieID` , `userRating` , i `timestamp` , która wskazuje, jak wysoce każdy użytkownik ocenił film. Oto przykład danych:

```output
    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596
```

## <a name="run-the-analysis"></a>Uruchamianie analizy

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Użyj następującego polecenia, aby uruchomić zadanie rekomendacji:

    ```bash
    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
    ```

> [!NOTE]  
> Ukończenie zadania może potrwać kilka minut i może działać wiele zadań MapReduce.

## <a name="view-the-output"></a>Wyświetlanie danych wyjściowych

1. Po zakończeniu zadania użyj następującego polecenia, aby wyświetlić wygenerowane dane wyjściowe:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Dane wyjściowe są wyświetlane w następujący sposób:

    ```output
    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
    ```

    Pierwsza kolumna to `userID` . Wartości zawarte w "[" i "]" są `movieId` następujące: `recommendationScore` .

2. Możesz użyć danych wyjściowych wraz z moviedb.txt, aby uzyskać więcej informacji na temat zaleceń. Najpierw skopiuj pliki przy użyciu następujących poleceń:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    To polecenie kopiuje dane wyjściowe do pliku o nazwie **recommendations.txt** w bieżącym katalogu wraz z plikami danych filmu.

3. Użyj następującego polecenia, aby utworzyć skrypt języka Python, który wyszukuje nazwy filmów w danych wyjściowych zaleceń:

    ```bash
    nano show_recommendations.py
    ```

    Gdy Edytor zostanie otwarty, użyj następującego tekstu jako zawartości pliku:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Naciśnij **klawisze Ctrl-X**, **Y** i **Enter** , aby zapisać dane.

4. Uruchom skrypt języka Python. Następujące polecenie założono, że jesteś w katalogu, w którym wszystkie pliki zostały pobrane:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    To polecenie sprawdza zalecenia wygenerowane dla użytkownika o IDENTYFIKATORze 4.

   * Plik **user-ratings.txt** jest używany do pobierania filmów, które zostały sklasyfikowane.

   * Plik **moviedb.txt** służy do pobierania nazw filmów.

   * **recommendations.txt** jest używany do pobierania zaleceń dotyczących filmu dla tego użytkownika.

     Dane wyjściowe tego polecenia są podobne do następującego tekstu:

        ```output
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        ```

## <a name="delete-temporary-data"></a>Usuń dane tymczasowe

Zadania Mahout nie usuwają danych tymczasowych, które są tworzone podczas przetwarzania zadania. `--tempDir`Parametr jest określony w przykładowym zadaniu do izolowania plików tymczasowych do określonej ścieżki w celu łatwego usunięcia. Aby usunąć pliki tymczasowe, użyj następującego polecenia:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Jeśli chcesz ponownie uruchomić polecenie, musisz również usunąć katalog wyjściowy. Aby usunąć ten katalog, wykonaj następujące czynności:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z usługi Mahout, odkryj inne sposoby pracy z danymi w usłudze HDInsight:

* [Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
