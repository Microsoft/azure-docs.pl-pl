---
title: Apache Ambari optymalizuje konfiguracje klastrów — Usługa Azure HDInsight
description: Użyj interfejsu użytkownika sieci Web Apache Ambari, aby skonfigurować i zoptymalizować klastry usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: c88882175ff256300dee486e680a9b63e9a65c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532536"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Korzystanie z systemu Apache Ambari w celu optymalizacji konfiguracji klastrów usługi HDInsight

HdInsight udostępnia klastry [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) dla aplikacji do przetwarzania danych na dużą skalę. Zarządzanie, monitorowanie i optymalizacja tych złożonych klastrów wielowęzłowych może być trudne. [Apache Ambari](https://ambari.apache.org/) to interfejs internetowy do zarządzania i monitorowania klastrów HDInsight Linux.  W przypadku klastrów systemu Windows użyj [interfejsu API Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md).

Aby zapoznać się z wprowadzeniem do korzystania z interfejsu użytkownika sieci Web Ambari, zobacz [Zarządzanie klastrami USŁUGI HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Zaloguj się do Ambari `https://CLUSTERNAME.azurehdidnsight.net` przy użyciu poświadczeń klastra. Na ekranie początkowym zostanie wyświetlony pulpit nawigacyjny przeglądu.

![Apache Ambari wyświetlany pulpit nawigacyjny użytkownika](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

Interfejs użytkownika sieci Web Ambari służy do zarządzania hostami, usługami, alertami, konfiguracjami i widokami. Ambari nie może służyć do tworzenia klastra HDInsight lub uaktualniania usług. Nie można również zarządzać stosami i wersjami, likwidować lub ponownie wykasować hostów ani dodawać usług do klastra.

## <a name="manage-your-clusters-configuration"></a>Zarządzanie konfiguracją klastra

Ustawienia konfiguracji pomagają dostroić określoną usługę. Aby zmodyfikować ustawienia konfiguracji usługi, wybierz usługę z paska bocznego **Usługi** (po lewej stronie). Następnie przejdź do karty **Configs** na stronie szczegółów usługi.

![Pasek boczny Usług Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modyfikowanie rozmiaru sterty Oprogramowania Java NameNode

Rozmiar sterty Java NameNode zależy od wielu czynników, takich jak obciążenie klastra. Ponadto liczba plików i liczba bloków. Domyślny rozmiar 1 GB działa dobrze w przypadku większości klastrów, chociaż niektóre obciążenia mogą wymagać więcej lub mniej pamięci.

Aby zmodyfikować rozmiar sterty Java NameNode:

1. Wybierz **hdfs** z paska bocznego usługi i przejdź do karty **Configs.**

    ![Konfiguracja Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Znajdź ustawienie **NameNode Java heap size**. Można również użyć pola tekstowego **filtru,** aby wpisać i znaleźć określone ustawienie. Wybierz ikonę **pióra** obok nazwy ustawienia.

    ![Apache Ambari NameNode Rozmiar sterty Java](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Wpisz nową wartość w polu tekstowym, a następnie naciśnij klawisz **Enter,** aby zapisać zmianę.

    ![Ambari Edytuj namenode rozmiar sterty Java1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Rozmiar sterty Oprogramowania Java NameNode zostanie zmieniony na 1 GB z 2 GB.

    ![Edytowana nazwaNodzonka java rozmiar sterty2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Zapisz zmiany, klikając zielony przycisk **Zapisz** u góry ekranu konfiguracji.

    !["Apache Ambari zapisać konfiguracje"](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Optymalizacja ula Apache

W poniższych sekcjach opisano opcje konfiguracji w celu optymalizacji ogólnej wydajności gałęzi Apache.

1. Aby zmodyfikować parametry konfiguracji gałęzi, wybierz **pozycję Gałąź** z paska bocznego Usługi.
1. Przejdź do karty **Konfiguracje.**

### <a name="set-the-hive-execution-engine"></a>Ustawianie aparatu wykonywania gałęzi

Hive udostępnia dwa aparaty [wykonawcze: Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) i [Apache TEZ.](https://tez.apache.org/) Tez jest szybszy niż MapReduce. Klastry SYSTEMU LINUX HDInsight mają Tez jako domyślny aparat wykonywania. Aby zmienić aparat wykonywania:

1. Na karcie **Konfiguracje** gałęzi wpisz **aparat wykonywania** w polu filtru.

    ![Apache Ambari Wyszukiwarka](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. Wartością domyślną właściwości **Optymalizacja** jest **Tez**.

    ![Optymalizacja - silnik Apache Tez](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Dostrajanie maperów

Hadoop próbuje podzielić *(map)* pojedynczy plik na wiele plików i przetwarzać powstałe pliki równolegle. Liczba maperów zależy od liczby podziałów. Następujące dwa parametry konfiguracji napędzają liczbę podziałów dla aparatu wykonywania Tez:

* `tez.grouping.min-size`: Dolny limit rozmiaru podziału zgrupowanego z domyślną wartością 16 MB (16 777 216 bajtów).
* `tez.grouping.max-size`: Górny limit rozmiaru podziału zgrupowanego z domyślną wartością 1 GB (1 073 741 824 bajtów).

Jako wytyczne dotyczące wydajności, obniżyć oba te parametry, aby poprawić opóźnienie, zwiększyć większą przepływność.

Na przykład, aby ustawić cztery zadania mapowania dla rozmiaru danych 128 MB, należy ustawić oba parametry na 32 MB każdy (33,554,432 bajtów).

1. Aby zmodyfikować parametry limitu, przejdź do karty **Konfiguracje** usługi Tez. Rozwiń panel **Ogólne** i `tez.grouping.max-size` `tez.grouping.min-size` znajdź parametry i parametry.

1. Ustaw oba parametry na **33 554 432** bajtów (32 MB).

    ![Apache Ambari Tez grupowanie rozmiarów](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Te zmiany mają wpływ na wszystkie zadania Tez na serwerze. Aby uzyskać optymalny wynik, wybierz odpowiednie wartości parametrów.

### <a name="tune-reducers"></a>Dostrajanie reduktorów

[Apache ORC](https://orc.apache.org/) i [Snappy](https://google.github.io/snappy/) oferują wysoką wydajność. Jednak Hive może mieć zbyt mało reduktorów domyślnie, powodując wąskie gardła.

Na przykład załóżmy, że masz rozmiar danych wejściowych 50 GB. Te dane w formacie ORC z kompresją Snappy jest 1 GB. Hive szacuje liczbę potrzebnych reduktorów jako: (liczba bajtów wprowadzanych do maperów / `hive.exec.reducers.bytes.per.reducer`).

W ustawieniach domyślnych w tym przykładzie są cztery reduktory.

Parametr `hive.exec.reducers.bytes.per.reducer` określa liczbę bajtów przetworzonych na reduktor. Wartość domyślna to 64 MB. Dostrajanie tej wartości w dół zwiększa równoległość i może poprawić wydajność. Dostrajanie go zbyt nisko może również produkować zbyt wiele reduktorów, potencjalnie niekorzystnie wpływając na wydajność. Ten parametr jest oparty na wymaganiach dotyczących określonych danych, ustawieniach kompresji i innych czynnikach środowiskowych.

1. Aby zmodyfikować ten parametr, przejdź do karty **Gałąź** gałęzi i znajdź parametr **Dane na reduktor** na stronie Ustawienia.

    ![Apache Ambari Dane na reduktor](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Wybierz **pozycję Edytuj,** aby zmodyfikować wartość do 128 MB (134 217 728 bajtów), a następnie naciśnij **klawisz Enter,** aby zapisać.

    ![Dane Ambari na reduktor - edytowane](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Biorąc pod uwagę rozmiar wejściowy 1024 MB, z 128 MB danych na reduktor, istnieje osiem reduktorów (1024/128).

1. Niepoprawna wartość parametru **Dane na reduktor** może spowodować dużą liczbę reduktorów, co negatywnie wpływa na wydajność kwerendy. Aby ograniczyć maksymalną liczbę `hive.exec.reducers.max` reduktorów, należy ustawić odpowiednią wartość. Wartość domyślna to 1009.

### <a name="enable-parallel-execution"></a>Włączanie wykonywania równoległego

Kwerenda hive jest wykonywana w co najmniej jednym etapie. Jeśli niezależne etapy mogą być uruchamiane równolegle, spowoduje to zwiększenie wydajności kwerendy.

1. Aby włączyć równoległe wykonywanie kwerendy, przejdź do karty `hive.exec.parallel` **Konfiguracja** hive i wyszukaj właściwość. Wartość domyślna to false. Zmień wartość true, a następnie naciśnij klawisz **Enter,** aby zapisać wartość.

1. Aby ograniczyć liczbę zadań do uruchomienia `hive.exec.parallel.thread.number` równolegle, zmodyfikuj właściwość. Wartość domyślna to 8.

    ![Wyświetlacz równoległy Apache Hive exec](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Włącz wektoryzację

Gałąź przetwarza dane wiersz po wierszu. Wektoryzacja kieruje hive do przetwarzania danych w blokach 1024 wierszy, a nie jeden wiersz naraz. Wektoryzacja ma zastosowanie tylko do formatu pliku ORC.

1. Aby włączyć wektorowane wykonanie kwerendy, przejdź do karty **Gałąź** gałęzi i wyszukaj `hive.vectorized.execution.enabled` parametr. Wartość domyślna jest true dla hive 0.13.0 lub nowsze.

1. Aby włączyć wektoryzowane wykonanie dla strony zmniejszenia `hive.vectorized.execution.reduce.enabled` kwerendy, ustaw parametr na true. Wartość domyślna to false.

    ![Apache Hive wektoryzowane wykonanie](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Włączanie optymalizacji opartej na kosztach (CBO)

Domyślnie Hive następuje zestaw reguł, aby znaleźć jeden plan wykonywania kwerendy optymalne. Optymalizacja oparta na kosztach (CBO) ocenia wiele planów wykonania kwerendy. I przypisuje koszt do każdego planu, a następnie określa najtańszy plan wykonania kwerendy.

Aby włączyć cbo, przejdź do**ustawień** konfiguracji **gałęzi** > i znajdź **Włącz Optymalizator oparty na****kosztach,** > a następnie przełącz przycisk przełączania **na On**.

![Optymalizator oparty na kosztach HDInsight](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

Następujące dodatkowe parametry konfiguracji zwiększają wydajność zapytań hive, gdy cbo jest włączone:

* `hive.compute.query.using.stats`

    Gdy ustawiona na true, Hive używa statystyk przechowywanych w `count(*)`jego metastore odpowiedzieć na proste pytania, takie jak .

    ![Apache Hive kwerendy obliczeniowej przy użyciu statystyk](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Statystyki kolumn są tworzone, gdy cbo jest włączone. Hive używa statystyk kolumn, które są przechowywane w metastore, do optymalizacji zapytań. Pobieranie statystyk kolumn dla każdej kolumny trwa dłużej, gdy liczba kolumn jest wysoka. Po ustawieniu false to ustawienie wyłącza pobieranie statystyki kolumny z metastore.

    ![Statystyki apache hive zestaw kolumn statystyki](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Podstawowe statystyki partycji, takie jak liczba wierszy, rozmiar danych i rozmiar pliku są przechowywane w metastore. Jeśli ustawiona na true, statystyki partycji są pobierane z metastore. Gdy false, rozmiar pliku jest pobierany z systemu plików. A liczba wierszy jest pobierana ze schematu wiersza.

    ![Statystyki gałęzi ustawiają statystyki partycji](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Włączanie kompresji pośredniej

Zadania mapowania tworzą pliki pośrednie, które są używane przez zadania reduktora. Kompresja pośrednia zmniejsza rozmiar pliku pośredniego.

Zadania Hadoop są zwykle wąskie gardło we/wy. Kompresja danych może przyspieszyć we/wy i ogólny transfer sieciowy.

Dostępne typy kompresji to:

| Format | Narzędzie | Algorytm | Rozszerzenie pliku | Splittable? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | Deflate | `.gz` | Nie |
| Okręg wyborczy Bzip2 | Okręg wyborczy Bzip2 | Okręg wyborczy Bzip2 |`.bz2` | Tak |
| LZO ( LZO ) | `Lzop` | LZO ( LZO ) | `.lzo` | Tak, jeśli jest indeksowany |
| Żwawy | Nie dotyczy | Żwawy | Żwawy | Nie |

Zgodnie z ogólną zasadą o splittable metody kompresji jest ważne, w przeciwnym razie kilka maperów zostaną utworzone. Jeśli dane wejściowe `bzip2` są tekstem, najlepszym rozwiązaniem jest. W przypadku formatu ORC Snappy jest najszybszą opcją kompresji.

1. Aby włączyć kompresję pośrednią, przejdź do karty **Konfiguracje** gałęzi, a następnie ustaw parametr na `hive.exec.compress.intermediate` true. Wartość domyślna to false.

    !["Hive exec compress intermediate"](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Aby skompresować pliki pośrednie, wybierz kodek kompresji o niższym koszcie procesora, nawet jeśli kodek nie ma dużej wydajności kompresji.

1. Aby ustawić pośredni kodek kompresji, `mapred.map.output.compression.codec` dodaj `hive-site.xml` `mapred-site.xml` właściwość niestandardową do pliku lub pliku.

1. Aby dodać ustawienie niestandardowe:

    a. Przejdź do **witryny hive** > **configs** > **Advanced** > Custom**hive site**.

    b. Wybierz **pozycję Dodaj właściwość...** u dołu okienka Lokacja gałęzi niestandardowej.

    d. W oknie Dodaj właściwość wprowadź `mapred.map.output.compression.codec` `org.apache.hadoop.io.compress.SnappyCodec` jako klucz i jako wartość.

    d. Wybierz pozycję **Dodaj**.

    !['Apache Hive custom property add' 'Apache Hive custom property add' 'Apache Hive custom property add' 'Ap](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    To ustawienie spowoduje skompresowanie pliku pośredniego przy użyciu kompresji Snappy. Po dodaniu właściwości pojawia się w okienku Niestandardowa lokacja gałęzi.

    > [!NOTE]  
    > Ta procedura modyfikuje `$HADOOP_HOME/conf/hive-site.xml` plik.

### <a name="compress-final-output"></a>Kompresowanie końcowego wyjścia

Końcowe dane wyjściowe gałęzi można również skompresować.

1. Aby skompresować końcowe dane wyjściowe gałęzi, przejdź do karty `hive.exec.compress.output` **Konfiguracje** gałęzi, a następnie ustaw parametr na true. Wartość domyślna to false.

1. Aby wybrać kodek kompresji `mapred.output.compression.codec` wyjściowej, dodaj właściwość niestandardową do okienka Lokacja gałęzi niestandardowej, zgodnie z opisem w kroku 3 poprzedniej sekcji.

    ![Apache Hive właściwość niestandardowa add2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Włącz wykonanie spekulacyjne

Wykonanie spekulacyjne uruchamia pewną liczbę zduplikowanych zadań do wykrywania i odrzucania listy wolno działającego modułu śledzenia zadań. Poprawiając ogólne wykonanie zadania poprzez optymalizację wyników poszczególnych zadań.

Wykonanie spekulacyjne nie powinny być włączone dla długotrwałych zadań MapReduce z dużą ilością danych wejściowych.

* Aby włączyć wykonanie spekulacyjne, przejdź do karty **Konfiguracje** `hive.mapred.reduce.tasks.speculative.execution` gałęzi, a następnie ustaw parametr na true. Wartość domyślna to false.

    !["Hive mapred zmniejszyć zadania spekulacyjne wykonanie"](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dostrajanie partycji dynamicznych

Gałąź umożliwia tworzenie partycji dynamicznych podczas wstawiania rekordów do tabeli, bez wstępnego zdefiniowania każdej partycji. Ta umiejętność jest potężną funkcją. Chociaż może to spowodować utworzenie dużej liczby partycji. I duża liczba plików dla każdej partycji.

1. Aby partycje dynamiczne hive `hive.exec.dynamic.partition` były ważne, wartość parametru powinna być prawdziwa (wartość domyślna).

1. Zmień tryb partycji dynamicznej na *ścisły*. W trybie ścisłym co najmniej jedna partycja musi być statyczna. To ustawienie zapobiega kwerendom bez filtru partycji w klauzuli WHERE, czyli *ścisłe* uniemożliwia kwerendy, które skanują wszystkie partycje. Przejdź do karty **Konfiguracje** gałęzi, a `hive.exec.dynamic.partition.mode` następnie ustaw **na ścisłe**. Wartość domyślna to **nonstrict**.

1. Aby ograniczyć liczbę partycji dynamicznych do `hive.exec.max.dynamic.partitions` utworzenia, zmodyfikuj parametr. Wartość domyślna to 5000.

1. Aby ograniczyć całkowitą liczbę partycji dynamicznych na węzeł, zmodyfikuj `hive.exec.max.dynamic.partitions.pernode`program . Wartość domyślna to 2000.

### <a name="enable-local-mode"></a>Włączanie trybu lokalnego

Tryb lokalny umożliwia hive wykonać wszystkie zadania zadania na jednym komputerze. Lub czasami w jednym procesie. To ustawienie zwiększa wydajność kwerendy, jeśli dane wejściowe są małe. A obciążenie związane z uruchamianiem zadań dla kwerend zużywa znaczny procent ogólnego wykonywania kwerend.

Aby włączyć tryb lokalny, dodaj `hive.exec.mode.local.auto` parametr do niestandardowego panelu lokacji gałęzi, jak wyjaśniono w kroku 3 sekcji Włącz [kompresję pośrednią.](#enable-intermediate-compression)

![Apache Hive exec tryb lokalny auto](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Ustaw pojedynczą mapęDeksece MultiGROUP BY

Gdy ta właściwość jest ustawiona na true, multigroup by kwerendy ze wspólnymi kluczami grupy przez generuje jedno zadanie MapReduce.  

Aby włączyć to zachowanie, dodaj `hive.multigroupby.singlereducer` parametr do okienka Lokacja gałęzi niestandardowej, jak wyjaśniono w kroku 3 sekcji Włącz [kompresję pośrednią.](#enable-intermediate-compression)

![Zestaw gałęzi pojedynczy MapReduce MultiGROUP BY](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Dodatkowe optymalizacje gałęzi

W poniższych sekcjach opisano dodatkowe optymalizacje związane z gałęzią, które można ustawić.

#### <a name="join-optimizations"></a>Dołącz do optymalizacji

Domyślnym typem sprzężenia w obszarze Gałęzi jest *sprzężenie losowe.* W hive specjalne maperów odczytać dane wejściowe i emitują sprzężenie klucz/wartość pary do pliku pośredniego. Hadoop sortuje i łączy te pary w etapie losowania. Ten etap shuffle jest drogie. Wybranie odpowiedniego sprzężenia na podstawie danych może znacznie poprawić wydajność.

| Typ sprzężenia | Kiedy | W jaki sposób? | Ustawienia gałęzi | Komentarze |
| --- | --- | --- | --- | --- |
| Sprzężenie losowe | <ul><li>Wybór domyślny</li><li>Zawsze działa</li></ul> | <ul><li>Odczyty z części jednej z tabel</li><li>Zasobniki i sortuje na klucz join</li><li>Wysyła jedno wiadro do każdego</li><li>Sprzężenie odbywa się po stronie Redukcji</li></ul> | Nie jest potrzebne znaczące ustawienie ula | Działa za każdym razem |
| Dołącz do mapy | <ul><li>Jedna tabela może zmieścić się w pamięci</li></ul> | <ul><li>Odczytuje małą tabelę do tabeli mieszania pamięci</li><li>Strumienie przez część dużego pliku</li><li>Łączy każdy rekord z tabeli mieszania</li><li>Dołączanie są tylko przez mapera</li></ul> | `hive.auto.confvert.join=true` | Szybko, ale ograniczona |
| Sortowanie zasobnika scalania | Jeśli obie tabele są: <ul><li>Posortowane w ten sam sposób</li><li>Wiadro to samo</li><li>Łączenie w posortowanym/wysuniętym wiaderku</li></ul> | Każdy proces: <ul><li>Odczytuje wiadro z każdej tabeli</li><li>Przetwarza wiersz o najniższej wartości</li></ul> | `hive.auto.convert.sortmerge.join=true` | Wydajne |

#### <a name="execution-engine-optimizations"></a>Optymalizacje silnika wykonania

Dodatkowe zalecenia dotyczące optymalizacji aparatu wykonywania hive:

| Ustawienie | Zalecane | Domyślna wartość HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Prawda = bezpieczniejsze, wolniejsze; false = szybciej | false |
| `tez.am.resource.memory.mb` | 4-GB górnej granicy dla większości | Automatyczne dostrojenie |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10 000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Optymalizacja Apache Pig

[Apache Pig](https://pig.apache.org/) właściwości mogą być modyfikowane z interfejsu użytkownika sieci Web Ambari do strojenia kwerend pig. Modyfikowanie Właściwości Świnia z Ambari bezpośrednio modyfikuje `/etc/pig/2.4.2.0-258.0/pig.properties` Pig właściwości w pliku.

1. Aby zmodyfikować właściwości Świnia, przejdź do karty **Konfiguracje świni,** a następnie rozwiń okienko **Zaawansowane właściwości świni.**

1. Znajdź, odkomentuj i zmień wartość właściwości, którą chcesz zmodyfikować.

1. Wybierz **pozycję Zapisz** w prawym górnym rogu okna, aby zapisać nową wartość. Niektóre właściwości mogą wymagać ponownego uruchomienia usługi.

    ![Zaawansowane właściwości świni Apache](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Wszystkie ustawienia na poziomie sesji zastępują `pig.properties` wartości właściwości w pliku.

### <a name="tune-execution-engine"></a>Dostroić silnik wykonawczy

Dostępne są dwa aparaty wykonawcze do wykonywania skryptów Pig: MapReduce i Tez. Tez jest zoptymalizowany silnik i jest znacznie szybszy niż MapReduce.

1. Aby zmodyfikować aparat wykonywania, w **zaawansowane właściwości świni** okienka, znajdź właściwość `exectype`.

1. Wartością domyślną jest **MapReduce**. Zmień go na **Tez**.

### <a name="enable-local-mode"></a>Włączanie trybu lokalnego

Podobnie jak hive, tryb lokalny jest używany do przyspieszenia zadań ze stosunkowo mniejszą ilością danych.

1. Aby włączyć tryb lokalny, ustaw `pig.auto.local.enabled` wartość **true**. Wartość domyślna to false.

1. Zadania o rozmiarze danych `pig.auto.local.input.maxbytes` wejściowych mniejszym niż wartość właściwości są uważane za małe zadania. Wartość domyślna to 1 GB.

### <a name="copy-user-jar-cache"></a>Kopiowanie pamięci podręcznej słoika użytkownika

Pig kopiuje pliki JAR wymagane przez pliki UDF do rozproszonej pamięci podręcznej, aby udostępnić je dla węzłów zadań. Te słoiki nie zmieniają się często. Jeśli ta opcja `pig.user.cache.enabled` jest włączona, ustawienie umożliwia umieszczanie w pamięci podręcznej w pamięci podręcznej w celu ponownego użycia ich w zadaniach uruchamianych przez tego samego użytkownika. To ustawienie powoduje niewielki wzrost wydajności zadania.

1. Aby włączyć, `pig.user.cache.enabled` ustaw wartość true. Wartością domyślną jest false.

1. Aby ustawić ścieżkę podstawową buforowanych `pig.user.cache.location` słoików, ustaw ścieżkę podstawową. Wartość domyślna to `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Optymalizacja wydajności za pomocą ustawień pamięci

Następujące ustawienia pamięci mogą pomóc w optymalizacji wydajności skryptu Pig.

* `pig.cachedbag.memusage`: Ilość pamięci przekazy tej torby. Torba to kolekcja krotek. Krotka jest uporządkowanym zestawem pól, a pole jest fragmentem danych. Jeśli dane w torbie są poza daną pamięcią, są rozlane na dysk. Wartość domyślna to 0.2, co stanowi 20 procent dostępnej pamięci. Ta pamięć jest współużytkowana we wszystkich workach w aplikacji.

* `pig.spill.size.threshold`: Worki większe niż ten próg wielkości wycieku (w bajtach) są rozlane na dysk. Wartość domyślna to 5 MB.

### <a name="compress-temporary-files"></a>Kompresowanie plików tymczasowych

Pig generuje pliki tymczasowe podczas wykonywania zadania. Kompresja plików tymczasowych powoduje wzrost wydajności podczas odczytu lub zapisu plików na dysku. Do kompresji plików tymczasowych można użyć następujących ustawień.

* `pig.tmpfilecompression`: Jeśli true, umożliwia kompresję plików tymczasowych. Wartość domyślna to false.

* `pig.tmpfilecompression.codec`: Kodek kompresji używany do kompresji plików tymczasowych. Zalecane kodeki kompresji są [LZO](https://www.oberhumer.com/opensource/lzo/) i Snappy dla mniejszego wykorzystania procesora.

### <a name="enable-split-combining"></a>Włącz łączenie dzielenia

Gdy ta opcja jest włączona, małe pliki są łączone w celu zmniejszenia liczby zadań na mapie. To ustawienie zwiększa wydajność zadań z wielu małych plików. Aby włączyć, `pig.noSplitCombination` ustaw wartość true. Wartość domyślna to false.

### <a name="tune-mappers"></a>Dostrajanie maperów

Liczba maperów jest kontrolowana przez `pig.maxCombinedSplitSize`modyfikację właściwości . Ta właściwość określa rozmiar danych, które mają być przetwarzane przez jedno zadanie mapy. Wartością domyślną jest domyślny rozmiar bloku systemu plików. Zwiększenie tej wartości powoduje mniejszą liczbę zadań mapowania.

### <a name="tune-reducers"></a>Dostrajanie reduktorów

Liczba reduktorów jest obliczana `pig.exec.reducers.bytes.per.reducer`na podstawie parametru . Parametr określa liczbę bajtów przetworzonych na reduktor, domyślnie 1 GB. Aby ograniczyć maksymalną liczbę reduktorów, należy ustawić `pig.exec.reducers.max` właściwość, domyślnie 999.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Apache HBase optymalizacji z interfejsu użytkownika sieci Ambari

[Konfiguracja Apache HBase](https://hbase.apache.org/) jest modyfikowana na karcie **Konfiguracja HBase.** W poniższych sekcjach opisano niektóre z ważnych ustawień konfiguracji, które wpływają na wydajność bazy danych HBase.

### <a name="set-hbase_heapsize"></a>Ustawianie HBASE_HEAPSIZE

Rozmiar sterty bazy danych HBase określa maksymalną ilość sterty, która ma być używana w megabajtach według serwerów *regionu* i *głównego.* Wartość domyślna to 1000 MB. Ta wartość powinna być dostrojona dla obciążenia klastra.

1. Aby zmodyfikować, przejdź do okienka **Zaawansowane HBase-env** na karcie **Konfiguracje** HBase, a następnie znajdź `HBASE_HEAPSIZE` to ustawienie.

1. Zmień wartość domyślną na 5000 MB.

    !["Apache Ambari HBase pamięci steryliza"](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Optymalizacja obciążeń odczytu

Następujące konfiguracje są ważne, aby poprawić wydajność obciążeń intensywnie odczytu.

#### <a name="block-cache-size"></a>Rozmiar pamięci podręcznej bloku

Pamięć podręczna bloku jest pamięcią podręczną odczytu. Jego rozmiar jest `hfile.block.cache.size` kontrolowany przez parametr. Wartość domyślna to 0.4, czyli 40 procent całkowitej pamięci serwera regionu. Im większy rozmiar pamięci podręcznej bloku, tym szybciej losowe odczyty będą.

1. Aby zmodyfikować ten parametr, przejdź do karty **Ustawienia** na karcie **Konfiguracje** HBase, a następnie znajdź **% regionuServer przydzielonego do buforów odczytu**.

    ![Rozmiar pamięci bloku pamięci Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Aby zmienić wartość, wybierz ikonę **Edytuj.**

#### <a name="memstore-size"></a>Rozmiar Memstore

Wszystkie edycje są przechowywane w buforze pamięci, zwany *Memstore*. Ten bufor zwiększa całkowitą ilość danych, które mogą być zapisywane na dysku w jednej operacji. Przyspiesza również dostęp do ostatnich zmian. Rozmiar Memstore jest zdefiniowany przez następujące dwa parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: Określa maksymalny procent serwera regionu, którego może używać połączony magazyn Memstore.

* `hbase.regionserver.global.memstore.LowerLimit`: Definiuje minimalny procent serwera regionu, którego może używać połączony magazyn Memstore.

Aby zoptymalizować dla losowych odczytów, można zmniejszyć memstore górnej i dolnej granicy.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Liczba wierszy pobranych podczas skanowania z dysku

Ustawienie `hbase.client.scanner.caching` definiuje liczbę wierszy odczytywanych `next` z dysku, gdy metoda jest wywoływana na skanerze.  Wartość domyślna to 100. Im wyższa liczba, tym mniej połączeń zdalnych wykonanych przez klienta do serwera regionu, co powoduje szybsze skanowanie. Jednak to ustawienie zwiększy również ciśnienie pamięci na kliencie.

![Apache HBase liczba pobranych wierszy](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Nie należy ustawiać wartości w taki sposób, aby czas między wywołaniem następnej metody na skanerze był większy niż limit czasu skanera. Czas trwania limitu czasu skanera jest zdefiniowany `hbase.regionserver.lease.period` przez właściwość.

### <a name="optimize-write-heavy-workloads"></a>Optymalizacja obciążeń o dużej obciążeniach związanych z zapisami

Następujące konfiguracje są ważne, aby poprawić wydajność obciążeń pracy z dużą wydajnością zapisu.

#### <a name="maximum-region-file-size"></a>Maksymalny rozmiar pliku regionu

HBase przechowuje dane w wewnętrznym formacie pliku o nazwie *HFile*. Właściwość `hbase.hregion.max.filesize` definiuje rozmiar pojedynczego pliku H dla regionu.  Region jest podzielony na dwa regiony, jeśli suma wszystkich plików HFiles w regionie jest większa niż to ustawienie.

!["Apache HBase HRegion max filesize"](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Im większy rozmiar pliku regionu, tym mniejsza liczba podziałów. Można zwiększyć rozmiar pliku, aby określić wartość, która powoduje maksymalną wydajność zapisu.

#### <a name="avoid-update-blocking"></a>Unikaj blokowania aktualizacji

* Właściwość `hbase.hregion.memstore.flush.size` definiuje rozmiar, w którym Memstore jest opróżniany na dysku. Domyślny rozmiar to 128 MB.

* Mnożnik bloku regionu Bazy `hbase.hregion.memstore.block.multiplier`danych HBase jest definiowany przez . Wartość domyślna to 4. Maksymalna dozwolona wartość to 8.

* HBase blokuje aktualizacje, jeśli`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`Memstore jest ( ) bajtów.

    Z domyślnymi wartościami flush size i block mnożnik, aktualizacje są blokowane, gdy Memstore jest 128 * 4 = 512 MB w rozmiarze. Aby zmniejszyć liczbę blokowania aktualizacji, `hbase.hregion.memstore.block.multiplier`zwiększ wartość programu .

![Mnożnik bloku regionu Apache HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Zdefiniuj rozmiar Memstore

Rozmiar memstore jest `hbase.regionserver.global.memstore.UpperLimit` definiowany przez parametry i. `hbase.regionserver.global.memstore.LowerLimit` Ustawienie tych wartości równych sobie zmniejsza przerwy podczas zapisów (również powoduje częstsze opróżnianie) i powoduje zwiększoną wydajność zapisu.

### <a name="set-memstore-local-allocation-buffer"></a>Ustawianie buforu alokacji lokalnej memstore

Użycie buforu alokacji lokalnej memstore jest określane przez właściwość `hbase.hregion.memstore.mslab.enabled`. Po włączeniu (true), to ustawienie zapobiega fragmentacji sterty podczas operacji intensywnego zapisu. Wartością domyślną jest true.

![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami HDInsight za pomocą interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
