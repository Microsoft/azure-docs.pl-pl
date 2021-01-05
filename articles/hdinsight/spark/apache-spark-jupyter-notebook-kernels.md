---
title: Jądra dla Jupyter Notebook w klastrach Spark w usłudze Azure HDInsight
description: Dowiedz się więcej o jądrach PySpark, PySpark3 i Spark dla Jupyter Notebook dostępnych w przypadku klastrów Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: b1139ce2358d5e5b3b52376892524e7ee0dd5611
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822237"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Jądra dla Jupyter Notebook w klastrach Apache Spark w usłudze Azure HDInsight

Klastry HDInsight Spark zapewniają jądra, których można używać z Jupyter Notebook na [Apache Spark](./apache-spark-overview.md) na potrzeby testowania aplikacji. Jądro jest programem, który uruchamia i interpretuje kod. Trzy jądra:

- **PySpark** — dla aplikacji pisanych w python2.
- **PySpark3** — dla aplikacji pisanych w python3.
- **Spark** — dla aplikacji pisanych w Scala.

W tym artykule dowiesz się, jak używać tych jądr i korzyści z ich używania.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Tworzenie Jupyter Notebook w usłudze HDInsight Spark

1. Na [Azure Portal](https://portal.azure.com/)wybierz klaster Spark.  Aby uzyskać instrukcje, zobacz [listę i wyświetlanie klastrów](../hdinsight-administer-use-portal-linux.md#showClusters) . Zostanie otwarty widok **Przegląd** .

2. W widoku **Przegląd** w polu **pulpity nawigacyjne klastra** wybierz pozycję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia administratora klastra.

    ![Jupyter Notebook na Apache Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook na platformie Spark")
  
   > [!NOTE]  
   > Możesz również uzyskać dostęp do Jupyter Notebook w klastrze Spark, otwierając następujący adres URL w przeglądarce. Zastąp ciąg **CLUSTERNAME** nazwą klastra:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Wybierz pozycję **Nowy**, a następnie wybierz opcję **Pyspark**, **PySpark3** lub **Spark** , aby utworzyć Notes. Użyj jądra Spark dla aplikacji Scala, jądra PySpark dla aplikacji python2 oraz jądra PySpark3 dla aplikacji python3.

    ![Jądra dla Jupyter Notebook na platformie Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Jądra dla Jupyter Notebook na platformie Spark")

4. Zostanie otwarty Notes z wybranym jądrem.

## <a name="benefits-of-using-the-kernels"></a>Korzyści wynikające z używania jądra

Oto kilka korzyści z używania nowych jądra z Jupyter Notebook w klastrach usługi HDInsight Spark.

- **Wstępnie ustawione konteksty**. Przy użyciu  **PySpark**, **PySpark3** lub jądra **platformy Spark** nie trzeba jawnie ustawiać kontekstów platformy Spark ani Hive przed rozpoczęciem pracy z aplikacjami. Te konteksty są domyślnie dostępne. Te konteksty są następujące:

  - **SC** -for Spark — kontekst
  - **SqlContext** — dla kontekstu Hive

    W związku z tym **nie** trzeba uruchamiać takich instrukcji jak następujące, aby ustawić konteksty:

    ```sql
    sc = SparkContext('yarn-client')
    sqlContext = HiveContext(sc)
    ```

    Zamiast tego można bezpośrednio użyć predefiniowanych kontekstów w aplikacji.

- **Magiczna komórka**. Jądro PySpark zawiera wstępnie zdefiniowane "MAGICS", które są specjalnymi poleceniami, które można wywołać za pomocą `%%` (na przykład `%%MAGIC` `<args>` ). Magic polecenia musi być pierwszym słowem w komórce kodu i zezwalać na wiele wierszy zawartości. Słowo magiczne powinno być pierwszym słowem w komórce. Dodanie wszystkiego przed magiczną, parzystą komentarzem powoduje wystąpienie błędu.     Aby uzyskać więcej informacji na temat MAGICS, zobacz [tutaj](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    W poniższej tabeli wymieniono różne Magic dostępne za pomocą jądra.

   | Magic | Przykład | Opis |
   | --- | --- | --- |
   | Pomoc |`%%help` |Generuje tabelę zawierającą wszystkie dostępne magicy z przykładem i opisem |
   | informacje o |`%%info` |Wyprowadza informacje o sesji dla bieżącego punktu końcowego usługi Livy |
   | konfigurowanie |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfiguruje parametry tworzenia sesji. Flaga Force ( `-f` ) jest wymagana, jeśli sesja została już utworzona, co gwarantuje, że sesja zostanie porzucona i utworzona ponownie. Aby uzyskać listę prawidłowych parametrów, spójrz na [treść żądania post/Sessions usługi Livy](https://github.com/cloudera/livy#request-body) . Parametry muszą być przesyłane jako ciąg JSON i muszą znajdować się w następnym wierszu po Magic, jak pokazano w przykładowej kolumnie. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Wykonuje zapytanie programu Hive względem elementu SqlContext. Jeśli `-o` parametr zostanie przesłany, wynik zapytania jest utrwalany w lokalnym kontekście języka Python%% jako [Pandas](https://pandas.pydata.org/) Dataframe. |
   | local |`%%local`<br>`a=1` |Cały kod w późniejszych wierszach jest wykonywany lokalnie. Kod musi być prawidłowym kodem python2 niezależnie od tego, którego jądra używasz. Tak więc, nawet w przypadku wybrania jądra **PySpark3** lub **Spark** podczas tworzenia notesu, jeśli używasz `%%local` Magic w komórce, ta komórka musi mieć tylko prawidłowy kod python2. |
   | dzienniki |`%%logs` |Wyprowadza dzienniki dla bieżącej sesji usługi Livy. |
   | delete |`%%delete -f -s <session number>` |Usuwa określoną sesję bieżącego punktu końcowego usługi Livy. Nie można usunąć sesji, która została uruchomiona dla samego jądra. |
   | oczyszczanie |`%%cleanup -f` |Usuwa wszystkie sesje dla bieżącego punktu końcowego usługi Livy, w tym sesję tego notesu. Flaga Force-f jest obowiązkowa. |

   > [!NOTE]  
   > Oprócz magicznych dodanych przez jądro PySpark, można również użyć [wbudowanych Magic IPython](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), w tym `%%sh` . Możesz użyć Magic, `%%sh` Aby uruchamiać skrypty i blokować kod w klastrze węzła głównego.

- **Autowizualizacja**. Jądro Pyspark automatycznie wizualizuje dane wyjściowe z kwerend Hive i SQL. Można wybrać różne typy wizualizacji, w tym tabelę, wykres kołowy, linię, obszar, pasek.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametry obsługiwane przez Magic%% SQL

`%%sql`Magiczna obsługa różnych parametrów, których można użyć do kontrolowania rodzaju danych wyjściowych otrzymywanych podczas uruchamiania zapytań. W poniższej tabeli przedstawiono dane wyjściowe.

| Parametr | Przykład | Opis |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Użyj tego parametru, aby zachować wynik zapytania w lokalnym kontekście języka Python%% jako [Pandas](https://pandas.pydata.org/) Dataframe. Nazwa zmiennej Dataframe to określona nazwa zmiennej. |
| -q |`-q` |Użyj tego parametru, aby wyłączyć wizualizacje dla komórki. Jeśli nie chcesz autowizualizować zawartości komórki i chcesz, aby przechwycić ją jako ramkę danych, a następnie użyj `-q -o <VARIABLE>` . Jeśli chcesz wyłączyć wizualizacje bez przechwytywania wyników (na przykład w przypadku uruchamiania zapytania SQL, takiego jak `CREATE TABLE` instrukcja), użyj `-q` bez określenia `-o` argumentu. |
| -m |`-m <METHOD>` |Miejsce, w którym **Metoda** jest **pobierana** , lub **próbka** (wartość domyślna to **Zrób**). Jeśli metoda jest **`take`** , jądro wybiera elementy z góry zestawu danych wynikowych określonego przez MAXROWS (opisane w dalszej części tej tabeli). Jeśli metoda jest **próbką**, elementy losowych próbek jądra zestawu danych zgodnie z `-r` parametrem, opisane dalej w tej tabeli. |
| -r |`-r <FRACTION>` |Tutaj **ułamek** jest liczbą zmiennoprzecinkową z zakresu od 0,0 do 1,0. Jeśli przykładowa Metoda zapytania SQL to `sample` , jądro losowo pobiera określoną część elementów zestawu wyników. Na przykład w przypadku uruchomienia zapytania SQL z argumentami `-m sample -r 0.01` , 1% wierszy wynik jest losowo próbkowany. |
| -n |`-n <MAXROWS>` |**MAXROWS** jest wartością całkowitą. Jądro ogranicza liczbę wierszy danych wyjściowych do **MAXROWS**. Jeśli **MAXROWS** jest liczbą ujemną, taką jak **-1**, wówczas liczba wierszy w zestawie wyników nie jest ograniczona. |

**Przykład:**

```sql
%%sql -q -m sample -r 0.1 -n 500 -o query2
SELECT * FROM hivesampletable
```

Powyższa instrukcja wykonuje następujące czynności:

- Wybiera wszystkie rekordy z **hivesampletable**.
- Ponieważ używamy-q, wyłącza funkcję autowizualizacji.
- Ponieważ używamy `-m sample -r 0.1 -n 500` , losowo próbkuje 10% wierszy w hivesampletable i ogranicza rozmiar zestawu wyników do 500 wierszy.
- Na koniec, ponieważ użyto `-o query2` go również do zapisu danych wyjściowych w ramce Dataframe o nazwie **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Zagadnienia dotyczące korzystania z nowych jądra

Niezależnie od tego, z których korzystasz jądro, wyjście z uruchomionych notesów zużywa zasoby klastra.  Z tymi jądrami, ponieważ konteksty są wstępnie zdefiniowane, po prostu wyjście notesu nie powoduje zakończenia tego kontekstu. I dlatego zasoby klastra nadal są używane. Dobrym rozwiązaniem jest użycie opcji **Zamknij i zatrzymywanie** z menu **plik** notesu po zakończeniu korzystania z notesu. Zamknięcie zamyka kontekst, a następnie kończy działanie notesu.

## <a name="where-are-the-notebooks-stored"></a>Gdzie są przechowywane notesy?

Jeśli klaster używa usługi Azure Storage jako domyślnego konta magazynu, notesy Jupyter są zapisywane na koncie magazynu w folderze **/HdiNotebooks** .  Notesy, pliki tekstowe i foldery tworzone w programie Jupyter są dostępne z konta magazynu.  Na przykład, jeśli używasz Jupyter do tworzenia folderu **`myfolder`** i notesu **folderu/Notes. ipynb**, możesz uzyskać dostęp do tego notesu w `/HdiNotebooks/myfolder/mynotebook.ipynb` ramach konta magazynu.  Odwrócenie jest również prawdziwe, to oznacza, że w przypadku przekazania notesu bezpośrednio do konta magazynu w `/HdiNotebooks/mynotebook1.ipynb` programie Notes jest również widoczny w witrynie Jupyter.  Notesy pozostają na koncie magazynu nawet po usunięciu klastra.

> [!NOTE]  
> Klastry usługi HDInsight z Azure Data Lake Storagem domyślnym magazynem nie przechowują notesów w skojarzonym magazynie.

Sposób zapisywania notesów na koncie magazynu jest zgodny z [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Jeśli używasz protokołu SSH do klastra, możesz użyć poleceń zarządzania plikami:

| Polecenie | Opis |
|---------|-------------|
| `hdfs dfs -ls /HdiNotebooks` | # List wszystko w katalogu głównym — wszystko w tym katalogu jest widoczne dla Jupyter ze strony głównej |
| `hdfs dfs –copyToLocal /HdiNotebooks` | # Pobierz zawartość folderu HdiNotebooks|
| `hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks` | # Przekaż przykład notesu. ipynb do folderu głównego, aby był widoczny z Jupyter |

Niezależnie od tego, czy klaster używa usługi Azure Storage, czy Azure Data Lake Storage jako domyślnego konta magazynu, notesy są również zapisywane w klastrze węzła głównego `/var/lib/jupyter` .

## <a name="supported-browser"></a>Obsługiwana przeglądarka

Notesy Jupyter w klastrach usługi HDInsight Spark są obsługiwane tylko w przeglądarce Google Chrome.

## <a name="suggestions"></a>Sugestie

Nowe jądra są w fazie rozwoju i zostaną dojrzałe w czasie. Interfejsy API mogą ulec zmianie, ponieważ te jądra zostały dojrzałe. Będziemy wdzięczni za wszelkie opinie, które są używane podczas korzystania z tych nowych jądra. Opinie są przydatne do kształtowania ostatecznej wersji tych jądra. Komentarze/Opinie możesz opuścić w sekcji **Opinie** w dolnej części tego artykułu.

## <a name="next-steps"></a>Następne kroki

- [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)
- [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
- [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
- [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
