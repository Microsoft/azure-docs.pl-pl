---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535614"
---
# <a name="azure-hdinsight-release-notes"></a>Informacje o wersji usługi Azure HDInsight

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

## <a name="release-date-09282020"></a>Data wydania: 09/28/2020

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i usługi HDInsight 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w kilka dni.

## <a name="new-features"></a>Nowe funkcje
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>Funkcja automatycznego skalowania dla interakcyjnych zapytań z usługą HDInsight 4,0 jest teraz ogólnie dostępna
Funkcja automatycznego skalowania dla typu klastra interakcyjnego zapytania jest teraz ogólnie dostępna (GA) dla usługi HDInsight 4,0. Wszystkie klastry interakcyjnych zapytań 4,0 utworzone po 27 sierpnia 2020 będą miały rozmyślną obsługę skalowania automatycznego.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>Klaster HBase obsługuje ADLS Gen2 Premium
Usługa HDInsight obsługuje teraz ADLS Gen2 Premium jako konto magazynu podstawowego dla klastrów HDInsight HBase 3,6 i 4,0. Wraz z [przyspieszeniem zapisu](./hbase/apache-hbase-accelerated-writes.md)można uzyskać lepszą wydajność klastrów HBase.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Kafka dystrybucji partycji w domenach błędów platformy Azure
Domena błędów to logiczna grupa bazowego sprzętu w centrum danych platformy Azure. Wszystkie domeny błędów korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Zanim Usługa HDInsight Kafka może przechowywać wszystkie repliki partycji w tej samej domenie błędów. Od tej wersji Usługa HDInsight obsługuje teraz automatyczne dystrybuowanie partycji Kafka na podstawie domen błędów platformy Azure. 

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Klienci mogą włączyć szyfrowanie między węzłami klastra przy użyciu szyfrowania IPSec z kluczami zarządzanymi przez platformę. Tę opcję można włączyć w czasie tworzenia klastra. Zobacz więcej szczegółów na temat [włączania szyfrowania podczas przesyłania](./domain-joined/encryption-in-transit.md).

### <a name="encryption-at-host"></a>Szyfrowanie na hoście
Po włączeniu szyfrowania na hoście dane przechowywane na hoście maszyny wirtualnej są szyfrowane w stanie spoczynku i są zaszyfrowane w usłudze Storage. W tej wersji można **włączyć szyfrowanie na dysku danych tymczasowych** podczas tworzenia klastra. Szyfrowanie na hoście jest obsługiwane tylko dla [niektórych jednostek SKU maszyn wirtualnych w ograniczonych regionach](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal). Usługa HDInsight obsługuje [następujące konfiguracje węzła i jednostki SKU](./hdinsight-supported-node-configuration.md). Zobacz więcej szczegółowych informacji na temat [włączania szyfrowania na hoście](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight teraz używa maszyn wirtualnych platformy Azure do aprowizacji klastra. Począwszy od tej wersji, usługa stopniowo przeprowadzi migrację do [zestawów skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Cały proces może potrwać miesiące. Po przeprowadzeniu migracji regionów i subskrypcji nowo utworzone klastry usługi HDInsight będą uruchamiane w zestawach skalowania maszyn wirtualnych bez akcji klienta. Nie oczekiwano zmiany krytycznej.

## <a name="deprecation"></a>Przestarzałe
Brak zaniechania dla tej wersji.

## <a name="behavior-changes"></a>Zmiany zachowania
Brak zmian w zachowaniu dla tej wersji.

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wydaniach.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Możliwość wybrania innej jednostki SKU dozorcy dla usług Spark, Hadoop i ML
Usługa HDInsight obecnie nie obsługuje zmieniania jednostki SKU dozorcy dla typów klastrów usług Spark, Hadoop i ML. Używa A2_v2 jednostki SKU/a2 dla węzłów dozorcy i klienci nie są dla nich obciążani. W nadchodzącym wydaniu klienci mogą zmienić dozorcy jednostki SKU dla usług Spark, Hadoop i ML zgodnie z potrzebami. Dozorcy węzły z jednostką SKU inną niż A2_v2/a2 zostaną rozliczone. Domyślna jednostka SKU będzie nadal A2_V2/a2 i bezpłatna.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Brak zmian wersji składnika dla tej wersji. Bieżące wersje składników usługi HDInsight 4,0 i HDInsight 3,6 można znaleźć w [tym dokumencie](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).