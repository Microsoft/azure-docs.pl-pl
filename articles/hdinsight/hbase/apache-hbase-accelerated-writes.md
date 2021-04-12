---
title: Usługa Azure HDInsight — przyspieszone zapisy dla oprogramowania Apache HBase
description: Zawiera omówienie funkcji rejestrowania przyspieszonego usługi Azure HDInsight, która korzysta z dysków zarządzanych w warstwie Premium w celu poprawy wydajności systemu Apache HBase zapis z wyprzedzeniem.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: c42a9d47cabd803944b858fc470c1bedc39d2d77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868906"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Usługa Azure HDInsight — przyspieszone zapisy dla oprogramowania Apache HBase

Ten artykuł zawiera informacje o funkcji **szybsze zapisy** dla oprogramowania Apache HBase w usłudze Azure HDInsight i sposobach ich użycia w celu zwiększenia wydajności zapisu. **Szybsze operacje zapisu** używają [dysków zarządzanych przez usługę Azure Premium SSD](../../virtual-machines/disks-types.md#premium-ssd) , aby zwiększyć wydajność usługi Apache HBASE Write log (WAL). Aby dowiedzieć się więcej o platformie Apache HBase, zobacz artykuł [co to jest Apache HBase w usłudze HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Przegląd architektury HBase

W HBase **wiersz** składa się z co najmniej jednej **kolumny** i jest identyfikowany przez **klucz wiersza**. Wiele wierszy tworzy **tabelę**. Kolumny zawierają **komórki**, które są wersjami znaczników wartości w tej kolumnie. Kolumny są pogrupowane w **rodziny kolumn**, a wszystkie kolumny w rodzinie kolumn są przechowywane razem w plikach magazynu o nazwie **HFiles**.

**Regiony** w HBase są używane do równoważenia obciążenia przetwarzania danych. HBase najpierw przechowuje wiersze tabeli w jednym regionie. Wiersze są rozłożone w wielu regionach w miarę wzrostu ilości danych w tabeli. **Serwery regionów** mogą obsługiwać żądania dla wielu regionów.

## <a name="write-ahead-log-for-apache-hbase"></a>Dziennik zapisu z wyprzedzeniem dla platformy Apache HBase

HBase najpierw zapisuje aktualizacje danych do typu dziennika zatwierdzeń o nazwie zapis z wyprzedzeniem (WAL). Gdy aktualizacja zostanie zapisana w WAL, jest zapisywana w **magazynu** w pamięci. Gdy dane w pamięci osiągną maksymalną pojemność, jest ona zapisywana na dysku jako **HFile**.

Jeśli **RegionServer** ulegnie awarii lub stanie się niedostępna przed opróżnieniem magazynu, można użyć dziennika zapisu z wyprzedzeniem, aby powtórzyć aktualizacje. Bez WAL, jeśli **RegionServer** ulega awarii przed opróżnieniem aktualizacji do **HFile**, wszystkie te aktualizacje zostaną utracone.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funkcja zapisu przyspieszonego w usłudze Azure HDInsight dla platformy Apache HBase

Funkcja przyspieszone zapisy rozwiązuje problem z wyższymi opóźnieniami zapisu spowodowanymi przez użycie dzienników zapisu z wyprzedzeniem, które znajdują się w magazynie w chmurze.  Funkcja przyspieszone zapisy dla klastrów usługi HDInsight Apache HBase dołącza dyski zarządzane dysk SSD w warstwie Premium do każdego RegionServer (węzeł procesu roboczego). Dzienniki zapisu z wyprzedzeniem są następnie zapisywane w systemie plików Hadoop (HDFS) zainstalowanym na tych dyskach w warstwie Premium, a nie w magazynie w chmurze.  Dyski zarządzane w warstwie Premium używają Solid-State dysków (dysków SSD) i oferują doskonałą wydajność we/wy dzięki odporności na uszkodzenia.  W przeciwieństwie do dysków niezarządzanych, jeśli jedna Jednostka magazynowa ulegnie awarii, nie wpłynie to na inne jednostki magazynowe w tym samym zestawie dostępności.  W związku z tym dyski zarządzane zapewniają małe opóźnienia zapisu i lepszą odporność aplikacji. Aby dowiedzieć się więcej o dyskach zarządzanych przez platformę Azure, zobacz [wprowadzenie do usługi Azure Managed disks](../../virtual-machines/managed-disks-overview.md).

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Jak włączyć przyspieszone zapisy dla HBase w usłudze HDInsight

Aby utworzyć nowy klaster HBase z funkcją szybsze zapisy, wykonaj kroki opisane w temacie [Konfigurowanie klastrów w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Na karcie **podstawowe** wybierz pozycję typ klastra jako HBase, określ wersję składnika, a następnie kliknij pole wyboru obok pozycji **Włącz szybsze zapisywanie HBase**. Następnie kontynuuj pracę z pozostałymi krokami tworzenia klastra.

:::image type="content" source="./media/apache-hbase-accelerated-writes/azure-portal-create-hbase-wals.png" alt-text="Włącz opcję szybsze zapisy dla usługi HDInsight Apache HBase" border="true":::

## <a name="other-considerations"></a>Inne zagadnienia

Aby zachować trwałość danych, należy utworzyć klaster z co najmniej trzema węzłami procesu roboczego. Po utworzeniu nie można przeskalować klastra do mniej niż trzech węzłów procesu roboczego.

Opróżniaj lub wyłączaj tabele HBase przed usunięciem klastra, aby nie utracić danych dziennika zapisu z wyprzedzeniem.

```
flush 'mytable'
```

```
disable 'mytable'
```

Postępuj zgodnie z podobnymi krokami podczas skalowania w dół klastra: Opróżniaj tabele i Wyłącz tabele, aby zatrzymać przychodzące dane. Nie można skalować klastra do mniejszej liczby niż trzy węzły.

Wykonanie tych kroków zapewni pomyślne skalowanie i uniknięcie możliwości namenode do trybu awaryjnego z powodu plików replikowanych lub tymczasowych.

Jeśli namenode przechodzi do trybu awaryjnego po skalowaniu w dół, użyj polecenia systemu HDFS, aby przeprowadzić ponowną replikację wbudowanych bloków i pobrać system plików HDFS w trybie awaryjnym. Ta ponowna replikacja umożliwi pomyślne ponowne uruchomienie HBase.

## <a name="next-steps"></a>Następne kroki

* Oficjalna dokumentacja oprogramowania Apache HBase w przypadku [funkcji dziennika zapisu z wyprzedzeniem](https://hbase.apache.org/book.html#wal)
* Aby uaktualnić klaster usługi HDInsight Apache HBase do korzystania z szybszych zapisów, zobacz [Migrowanie klastra Apache HBase do nowej wersji](apache-hbase-migrate-new-version.md).
