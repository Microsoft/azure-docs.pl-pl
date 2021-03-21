---
title: Optymalizowanie Apache Hive w usłudze Apache Ambari w usłudze Azure HDInsight
description: Za pomocą interfejsu użytkownika sieci Web Apache Ambari można konfigurować i optymalizować Apache Hive.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 349f58720e6fff52191dfff65108cd1320e41eed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939246"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Optymalizowanie Apache Hive w usłudze Apache Ambari w usłudze Azure HDInsight

Apache Ambari to interfejs sieci Web umożliwiający zarządzanie i monitorowanie klastrów usługi HDInsight. Wprowadzenie do interfejsu użytkownika sieci Web Ambari można znaleźć w temacie [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

W poniższych sekcjach opisano opcje konfiguracji dotyczące optymalizowania ogólnej wydajności Apache Hive.

1. Aby zmodyfikować parametry konfiguracji programu Hive, wybierz pozycję **Hive** z paska bocznego usług.
1. Przejdź **do karty konfiguracje** .

## <a name="set-the-hive-execution-engine"></a>Ustawianie aparatu wykonywania programu Hive

Program Hive oferuje dwa aparaty wykonywania: Apache Hadoop MapReduce i Apache TEZ. Tez jest większa niż MapReduce. Klastry usługi HDInsight w systemie Linux mają tez jako domyślny aparat wykonywania. Aby zmienić aparat wykonywania:

1. Na **karcie konfiguracje** programu Hive wpisz **aparat wykonywania** w polu Filtr.

    ![Aparat wykonywania wyszukiwania Apache Ambari](./media/optimize-hive-ambari/ambari-search-execution.png)

1. Wartość domyślna właściwości **optymalizacji** to **tez**.

    ![Optymalizacja — aparat Apache Tez](./media/optimize-hive-ambari/optimization-apache-tez.png)

## <a name="tune-mappers"></a>Dostrajaj mapowania

Usługa Hadoop próbuje podzielić (*zmapować*) pojedynczy plik na wiele plików i przetwarzać pliki w sposób równoległy. Liczba odwzorowań zależy od liczby podziałów. Dwa następujące parametry konfiguracji dotyczą liczby podziałów dla aparatu wykonywania tez:

* `tez.grouping.min-size`: Dolny limit rozmiaru zgrupowanego podziału przy wartości domyślnej wynoszącej 16 MB (16 777 216 bajtów).
* `tez.grouping.max-size`: Górny limit rozmiaru zgrupowanego podziału z wartością domyślną 1 GB (1 073 741 824 bajtów).

Jako wytyczne dotyczące wydajności, należy obniżyć oba te parametry, aby zwiększyć czas oczekiwania i zwiększyć przepływność.

Na przykład, aby ustawić cztery zadania mapowania dla rozmiaru danych 128 MB, należy ustawić oba parametry na 32 MB dla każdego (33 554 432 bajty).

1. Aby zmodyfikować parametry limitu, przejdź **do karty konfiguracje** usługi tez. Rozwiń panel **Ogólne** i Znajdź `tez.grouping.max-size` `tez.grouping.min-size` Parametry i.

1. Ustaw oba parametry na **33 554 432** bajtów (32 MB).

    ![Rozmiary grupowania Apache Ambari tez](./media/optimize-hive-ambari/apache-tez-grouping-size.png)

Te zmiany wpływają na wszystkie zadania tez na serwerze. Aby uzyskać optymalny wynik, wybierz odpowiednie wartości parametrów.

## <a name="tune-reducers"></a>Dostrajaj ograniczenia

Funkcje Apache ORC i przyciągania zapewniają wysoką wydajność. Jednak program Hive może mieć domyślnie zbyt mało zmniejszeń, co powoduje wąskie gardła.

Załóżmy na przykład, że masz rozmiar danych wejściowych wynoszący 50 GB. Te dane w formacie ORC z kompresją przyciągania to 1 GB. Program Hive szacuje liczbę elementów ograniczających potrzebną jako: (liczba bajtów wejściowych do mapera/ `hive.exec.reducers.bytes.per.reducer` ).

W przypadku ustawień domyślnych ten przykład ma cztery zmniejszenia.

`hive.exec.reducers.bytes.per.reducer`Parametr określa liczbę bajtów przetworzonych na zmniejszenie. Wartość domyślna to 64 MB. Dostrajanie tej wartości powoduje zwiększenie równoległości i może poprawić wydajność. Dostrajanie go za mało może również generować zbyt wiele obniżyć, co może mieć negatywny wpływ na wydajność. Ten parametr jest oparty na określonych wymaganiach dotyczących danych, ustawieniach kompresji i innych czynnikach środowiskowych.

1. Aby zmodyfikować parametr, przejdź **do karty konfiguracje** programu Hive i Znajdź na stronie Ustawienia parametr **dane dla ograniczenia** .

    ![Dane Apache Ambari na zmniejszenie](./media/optimize-hive-ambari/ambari-data-per-reducer.png)

1. Wybierz pozycję **Edytuj** , aby zmodyfikować wartość na 128 MB (134 217 728 bajtów), a następnie naciśnij klawisz **Enter** , aby zapisać.

    ![Ambari danych na mniejszą liczbę modyfikacji](./media/optimize-hive-ambari/data-per-reducer-edited.png)
  
    Mając rozmiar danych wejściowych wynoszący 1 024 MB, z 128 MB na mniejszą liczbę obniżek (1024/128).

1. Niepoprawna wartość w parametrze **danych na zmniejszenie** wartości może spowodować powstanie dużej liczby obniżyć wydajności zapytań. Aby ograniczyć maksymalną liczbę elementów ograniczających, ustaw `hive.exec.reducers.max` odpowiednią wartość. Wartość domyślna to 1009.

## <a name="enable-parallel-execution"></a>Włącz wykonywanie równoległe

Zapytanie programu Hive jest wykonywane w jednym lub kilku etapach. Jeśli niezależne etapy można uruchomić równolegle, spowoduje to zwiększenie wydajności zapytania.

1. Aby włączyć równoległe wykonywanie zapytań, przejdź do karty **Konfiguracja** programu Hive i Wyszukaj `hive.exec.parallel` Właściwość. Wartość domyślna to false. Zmień wartość na true, a następnie naciśnij klawisz **Enter** , aby zapisać wartość.

1. Aby ograniczyć liczbę zadań, które mają być uruchamiane równolegle, należy zmodyfikować `hive.exec.parallel.thread.number` Właściwość. Wartość domyślna to 8.

    ![Wyświetlanie równoległe Apache Hive exec](./media/optimize-hive-ambari/apache-hive-exec-parallel.png)

## <a name="enable-vectorization"></a>Włącz wektoryzacji

Program Hive przetwarza wiersz danych według wiersza. Wektoryzacji kieruje gałąź do przetwarzania danych w blokach wierszy 1 024, a nie w jednym wierszu naraz. Wektoryzacji ma zastosowanie tylko do formatu pliku ORC.

1. Aby włączyć wektorowe wykonywanie zapytań, przejdź **do karty konfiguracje** programu Hive i Wyszukaj `hive.vectorized.execution.enabled` parametr. Wartość domyślna to true dla programu Hive 0.13.0 lub nowszego.

1. Aby włączyć wykonywanie wektorowe dla strony ze zmniejszeniem zapytania, ustaw dla `hive.vectorized.execution.reduce.enabled` parametru wartość true. Wartość domyślna to false.

    ![Apache Hive wykonywanie wektorowe](./media/optimize-hive-ambari/hive-vectorized-execution.png)

## <a name="enable-cost-based-optimization-cbo"></a>Włącz optymalizację opartą na kosztach (CBO)

Domyślnie gałąź jest zgodna z zestawem reguł, aby znaleźć jeden optymalny plan wykonywania zapytań. Optymalizacja oparta na kosztach (CBO) szacuje wiele planów, aby wykonać zapytanie. I przypisuje koszt do każdego planu, a następnie określa najtańszy plan wykonywania zapytania.

Aby włączyć program CBO, przejdź do  >  **ustawień konfiguracji** programu Hive  >   i Znajdź pozycję **Włącz Optymalizator oparty na kosztach**, a następnie Przełącz przycisk przełączania na pozycję **włączone**.

![Optymalizator oparty na kosztach usługi HDInsight](./media/optimize-hive-ambari/hdinsight-cbo-config.png)

Następujące dodatkowe parametry konfiguracji zwiększają wydajność zapytań Hive, gdy jest włączony CBO:

* `hive.compute.query.using.stats`

    Po ustawieniu na wartość true, funkcja Hive używa statystyk przechowywanych w magazynie metadanych do odpowiedzi prostych zapytań, takich jak `count(*)` .

    ![Apache Hive kwerendy obliczeniowej przy użyciu statystyk](./media/optimize-hive-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Statystyki kolumn są tworzone, gdy funkcja CBO jest włączona. Program Hive używa statystyk kolumn, które są przechowywane w magazynie metadanych w celu optymalizowania zapytań. Pobieranie statystyk kolumn dla każdej kolumny trwa dłużej, gdy liczba kolumn jest wysoka. Po ustawieniu na wartość false to ustawienie wyłącza Pobieranie statystyk kolumn z magazynu metadanych.

    ![Statystyka kolumny zestawu statystyk Apache Hive](./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Podstawowe statystyki partycji, takie jak liczba wierszy, rozmiar danych i rozmiar pliku, są przechowywane w magazynie metadanych. W przypadku ustawienia wartości true statystyki partycji są pobierane z magazynu metadanych. W przypadku wartości false rozmiar pliku jest pobierany z systemu plików. I liczba wierszy pobieranych z schematu wierszy.

    ![Statystyka partycji zestawu statystyk Hive](./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png)

## <a name="enable-intermediate-compression"></a>Włącz kompresję pośrednią

Zadania map tworzą pliki pośrednie, które są używane przez zadania zmniejszania. Kompresja pośrednia zmniejsza rozmiar pliku pośredniego.

Zadania usługi Hadoop są zwykle w wąskim obłączeniem we/wy. Kompresowanie danych może przyspieszyć operacji we/wy i ogólnego transferu sieciowego.

Dostępne typy kompresji to:

| Format | Narzędzie | Algorytm | Rozszerzenie pliku | Podzielne? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | WKLĘŚNIĘCIE | `.gz` | Nie |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Tak |
| LZO | `Lzop` | LZO | `.lzo` | Tak, jeśli indeksowane |
| Snappy | Nie dotyczy | Snappy | Snappy | Nie |

Ogólną zasadą jest, że podział metody kompresji jest istotny, w przeciwnym razie zostaną utworzone pewne mapowania. Jeśli dane wejściowe są tekstem, `bzip2` najlepiej jest wybrać opcję. W przypadku formatu ORC przyciąganie jest najszybszą opcją kompresji.

1. Aby włączyć kompresję pośrednią, przejdź do **karty konfiguracje** programu Hive, a następnie ustaw `hive.exec.compress.intermediate` parametr na wartość true. Wartość domyślna to false.

    !["Hive exec Kompresuj pośredni"](./media/optimize-hive-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Aby skompresować pliki pośrednie, wybierz koder-dekoder kompresji z niższym kosztem procesora, nawet jeśli koder-dekoder nie ma danych wyjściowych o dużej kompresji.

1. Aby ustawić pośredni koder-dekoder kompresji, Dodaj właściwość niestandardową `mapred.map.output.compression.codec` do `hive-site.xml` `mapred-site.xml` pliku lub.

1. Aby dodać ustawienie niestandardowe:

    a. Przejdź do konfiguracji programu **Hive**  >    >    >  **— Zaawansowane niestandardowe gałęzie — lokacja**.

    b. Wybierz pozycję **Dodaj właściwość...** w dolnej części okienka niestandardowe gałęzie — lokacja.

    c. W oknie Dodawanie właściwości wprowadź `mapred.map.output.compression.codec` jako klucz i `org.apache.hadoop.io.compress.SnappyCodec` jako wartość.

    d. Wybierz pozycję **Dodaj**.

    !["Apache Hive dodanie właściwości niestandardowej"](./media/optimize-hive-ambari/hive-custom-property.png)

    To ustawienie kompresuje plik pośredni przy użyciu kompresji przyciągania. Po dodaniu właściwości zostanie ona wyświetlona w okienku niestandardowe gałęzie — lokacja.

    > [!NOTE]  
    > Ta procedura modyfikuje `$HADOOP_HOME/conf/hive-site.xml` plik.

## <a name="compress-final-output"></a>Kompresuj końcowe dane wyjściowe

Ostateczne dane wyjściowe programu Hive mogą być również skompresowane.

1. Aby skompresować końcową wersję wyjściową programu Hive, przejdź **do karty konfiguracje** programu Hive, a następnie ustaw `hive.exec.compress.output` parametr na wartość true. Wartość domyślna to false.

1. Aby wybrać koder-dekoder kompresji wyjściowej, należy dodać `mapred.output.compression.codec` właściwość niestandardową do niestandardowego okienka programu Hive, zgodnie z opisem w poprzedniej sekcji Krok 3.

    ![Apache Hive właściwości niestandardowej ADD2](./media/optimize-hive-ambari/hive-custom-property2.png)

## <a name="enable-speculative-execution"></a>Włącz wykonywanie spekulacyjne

Wykonanie spekulacyjne uruchamia określoną liczbę zduplikowanych zadań, aby wykrywać i odrzucać listę powolnego uruchomionego śledzenia zadań. Podczas ulepszania ogólnego wykonywania zadań poprzez optymalizację poszczególnych wyników zadań.

Wykonanie spekulacyjne nie powinno być włączone dla długotrwałych zadań MapReduce z dużymi ilościami danych wejściowych.

* Aby włączyć wykonywanie spekulacyjne, przejdź **do karty konfiguracje** programu Hive, a następnie ustaw `hive.mapred.reduce.tasks.speculative.execution` dla parametru wartość true. Wartość domyślna to false.

    !["Hive mapred Redukuj zadania spekulacyjne wykonywanie"](./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

## <a name="tune-dynamic-partitions"></a>Dostrajanie partycji dynamicznych

Program Hive umożliwia tworzenie partycji dynamicznych podczas wstawiania rekordów do tabeli bez definiowania wstępnej definicji każdej partycji. Ta możliwość jest zaawansowaną funkcją. Chociaż może to spowodować utworzenie dużej liczby partycji. I duża liczba plików dla każdej partycji.

1. W przypadku platformy Hive do wykonywania partycji dynamicznych `hive.exec.dynamic.partition` wartość parametru powinna być równa true (domyślnie).

1. Zmień tryb partycji dynamicznej na *Strict*. W trybie ścisłym co najmniej jedna partycja musi być statyczna. To ustawienie zapobiega kwerendom bez filtru partycji w klauzuli WHERE, oznacza to, że *rygorystyczne* uniemożliwiają zapytania, które skanują wszystkie partycje. Przejdź **do karty konfiguracje** programu Hive, a następnie ustaw wartość `hive.exec.dynamic.partition.mode` **Strict**. Wartość domyślna to **nierygorystyczne**.

1. Aby ograniczyć liczbę partycji dynamicznych do utworzenia, należy zmodyfikować `hive.exec.max.dynamic.partitions` parametr. Wartość domyślna to 5000.

1. Aby ograniczyć łączną liczbę partycji dynamicznych na węzeł, należy zmodyfikować `hive.exec.max.dynamic.partitions.pernode` . Wartość domyślna to 2000.

## <a name="enable-local-mode"></a>Włącz tryb lokalny

Tryb lokalny umożliwia usłudze Hive wykonywanie wszystkich zadań zadania na pojedynczym komputerze. Lub czasami w pojedynczym procesie. To ustawienie zwiększa wydajność zapytań, jeśli dane wejściowe są małe. A koszt uruchamiania zadań dla zapytań zużywa znaczną część całkowitego wykonania zapytania.

Aby włączyć tryb lokalny, należy dodać `hive.exec.mode.local.auto` parametr do niestandardowego panelu programu Hive, jak wyjaśniono w kroku 3 w sekcji [Włączanie kompresji pośredniej](#enable-intermediate-compression) .

![Lokalne Autokonfiguracja trybu Apache Hive exec](./media/optimize-hive-ambari/hive-exec-mode-local-auto.png)

## <a name="set-single-mapreduce-multigroup-by"></a>Ustaw pojedyncze MapReduce wielogrupuj według

Gdy ta właściwość ma wartość true, kwerenda wielogrupowanie według wspólnych kluczy GROUP BY generuje pojedyncze zadanie MapReduce.  

Aby włączyć to zachowanie, należy dodać `hive.multigroupby.singlereducer` parametr do okienka niestandardowe gałęzie witryny, jak wyjaśniono w kroku 3 w sekcji [Włączanie kompresji pośredniej](#enable-intermediate-compression) .

![Zestaw Hive MapReduce pojedynczej grupie](./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png)

## <a name="additional-hive-optimizations"></a>Dodatkowe optymalizacje Hive

W poniższych sekcjach opisano dodatkowe optymalizacje dotyczące technologii Hive, które można ustawić.

### <a name="join-optimizations"></a>Przełączaj optymalizacje

Domyślny typ sprzężenia w programie Hive to *sprzężenie losowe*. W programie Hive specjalne mapowania odczytują dane wejściowe i emitują parę klucz/wartość sprzężenia do pliku pośredniego. Usługa Hadoop sortuje i scala te pary na etapie losowym. Ten etap losowy jest kosztowny. Wybranie odpowiedniego sprzężenia na podstawie danych może znacząco poprawić wydajność.

| Typ sprzężenia | Kiedy | Jak | Ustawienia programu Hive | Komentarze |
| --- | --- | --- | --- | --- |
| Rozłączenie losowe | <ul><li>Wybór domyślny</li><li>Zawsze działa</li></ul> | <ul><li>Odczytuje z części jednej z tabel</li><li>Zasobniki i sortuje według klucza sprzężenia</li><li>Wysyła jeden zasobnik do każdego zmniejszenia</li><li>Przyłączanie odbywa się po stronie Zmniejsz</li></ul> | Nie jest wymagana znaczna wartość ustawienia Hive | Działa za każdym razem |
| Sprzężenie mapy | <ul><li>Jedna tabela może zmieścić się w pamięci</li></ul> | <ul><li>Odczytuje małą tabelę w tabeli skrótów pamięci</li><li>Strumienie w ramach dużego pliku</li><li>Sprzęga każdy rekord z tabeli skrótów</li><li>Sprzężenia są zależne od mapowania</li></ul> | `hive.auto.confvert.join=true` | Szybkie, ale ograniczone |
| Sortuj zasobnik scalania | Jeśli obie tabele są: <ul><li>Sortowane tak samo</li><li>Przedziały te same</li><li>Przyłączanie do kolumny posortowanej/przedziału</li></ul> | Każdy proces: <ul><li>Odczytuje zasobnik z każdej tabeli</li><li>Przetwarza wiersz o najniższej wartości</li></ul> | `hive.auto.convert.sortmerge.join=true` | Sprawniej |

### <a name="execution-engine-optimizations"></a>Optymalizacje aparatu wykonywania

Dodatkowe zalecenia dotyczące optymalizacji aparatu wykonywania programu Hive:

| Ustawienie | Zalecane | Domyślna Usługa HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Prawda = bezpieczniejsze, wolniejsze; FAŁSZ = szybsze | fałsz |
| `tez.am.resource.memory.mb` | Górna granica 4 GB dla większości | Dostrajanie autodostrajania |
| `tez.session.am.dag.submit.timeout.secs` | 300 + | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20 000 + | 10 000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40 000 + | 20000 |

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Interfejs API REST usługi Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optymalizowanie zapytań technologii Apache Hive w usłudze Azure HDInsight](./hdinsight-hadoop-optimize-hive-query.md)
* [Optymalizowanie klastrów](./optimize-hive-ambari.md)
* [Optymalizowanie usługi Apache HBase](./optimize-hbase-ambari.md)
* [Optymalizowanie platformy Apache Pig](./optimize-pig-ambari.md)
