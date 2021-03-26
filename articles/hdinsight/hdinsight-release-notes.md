---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 324d8b4c9fc53ca24e62fe339065d4452577cb1f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607222"
---
# <a name="azure-hdinsight-release-notes"></a>Informacje o wersji usługi Azure HDInsight

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

Jeśli chcesz subskrybować informacje o wersji, zobacz wersje w [tym repozytorium GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-03242021"></a>Data wydania: 03/24/2021

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i usługi HDInsight 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w kilka dni.

## <a name="new-features"></a>Nowe funkcje
### <a name="spark-30-preview"></a>Wersja zapoznawcza platformy Spark 3,0
Usługa HDInsight dodała obsługę [platformy Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) do usługi HDInsight 4,0 jako funkcję w wersji zapoznawczej. 

### <a name="kafka-24-preview"></a>Kafka 2,4 — wersja zapoznawcza
Usługa HDInsight dodała obsługę [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) do usługi HDInsight 4,0 jako funkcję w wersji zapoznawczej.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight teraz używa maszyn wirtualnych platformy Azure do aprowizacji klastra. Usługa jest stopniowo migrowana do [zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/overview.md). Cały proces może potrwać miesiące. Po przeprowadzeniu migracji regionów i subskrypcji nowo utworzone klastry usługi HDInsight będą uruchamiane w zestawach skalowania maszyn wirtualnych bez akcji klienta. Nie oczekiwano zmiany krytycznej.

## <a name="deprecation"></a>Przestarzałe
Brak zaniechania w tej wersji.

## <a name="behavior-changes"></a>Zmiany zachowania
### <a name="default-cluster-version-is-changed-to-40"></a>Domyślna wersja klastra została zmieniona na 4,0
Domyślna wersja klastra usługi HDInsight została zmieniona z 3,6 na 4,0. Aby uzyskać więcej informacji na temat dostępnych wersji, zobacz [dostępne wersje](./hdinsight-component-versioning.md). Dowiedz się więcej na temat Nowości w usłudze [HDInsight 4,0](./hdinsight-version-release.md).

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Domyślne rozmiary maszyn wirtualnych klastra są zmieniane na serie EV3 
Domyślne rozmiary maszyn wirtualnych klastra są zmieniane z serii D na EV3. Ta zmiana dotyczy węzłów głównych i węzłów procesu roboczego. Aby uniknąć zmiany wpływającej na przetestowane przepływy pracy, Określ rozmiary maszyn wirtualnych, które mają być używane w szablonie ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Zasób interfejsu sieciowego nie jest widoczny dla klastrów uruchomionych w zestawach skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight jest stopniowo migrowana do zestawów skalowania maszyn wirtualnych platformy Azure. Interfejsy sieciowe dla maszyn wirtualnych nie są już widoczne dla klientów klastrów korzystających z zestawów skalowania maszyn wirtualnych platformy Azure.

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wydaniach.

### <a name="os-version-upgrade"></a>Uaktualnienie wersji systemu operacyjnego
Usługa HDInsight będzie uaktualniać wersję systemu operacyjnego z Ubuntu 16,04 do 18,04. Uaktualnienie zakończyło się przed 2021 kwietnia.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Usługa HDInsight 3,6 koniec wsparcia z 30 2021 czerwca
Usługa HDInsight 3,6 zostanie zakończona. Od czerwca 30 2021 klienci nie mogą tworzyć nowych klastrów usługi HDInsight 3,6. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przeniesienie do usługi HDInsight 4,0, aby uniknąć potencjalnych przerw w działaniu systemu/obsługi.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Dodano obsługę dla platformy Spark 3.0.0 i Kafka 2.4.1 jako wersja zapoznawcza. Bieżące wersje składników usługi HDInsight 4,0 i HDInsight 3,6 można znaleźć w [tym dokumencie](./hdinsight-component-versioning.md).
