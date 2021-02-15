---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 1a0b1a0400ae3d43817921e8a336421aee35ccd6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378149"
---
# <a name="azure-hdinsight-release-notes"></a>Informacje o wersji usługi Azure HDInsight

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

Jeśli chcesz subskrybować informacje o wersji, zobacz wersje w [tym repozytorium GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-02052021"></a>Data wydania: 02/05/2021

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i usługi HDInsight 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w kilka dni.

## <a name="new-features"></a>Nowe funkcje
### <a name="dav4-series-support"></a>Obsługa serii Dav4
Usługa HDInsight dodała obsługę serii Dav4 w tej wersji. Dowiedz się więcej o [serii Dav4 tutaj](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series).

### <a name="kafka-rest-proxy-ga"></a>Kafka serwer proxy REST 
Serwer proxy REST Kafka umożliwia współdziałanie z klastrem Kafka za pośrednictwem interfejsu API REST za pośrednictwem protokołu HTTPS. Serwer proxy REST Kafka jest ogólnie dostępny od tej wersji. Więcej informacji na temat [serwera proxy REST Kafka znajdziesz tutaj](https://docs.microsoft.com/azure/hdinsight/kafka/rest-proxy).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight teraz używa maszyn wirtualnych platformy Azure do aprowizacji klastra. Usługa jest stopniowo migrowana do [zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/overview.md). Cały proces może potrwać miesiące. Po przeprowadzeniu migracji regionów i subskrypcji nowo utworzone klastry usługi HDInsight będą uruchamiane w zestawach skalowania maszyn wirtualnych bez akcji klienta. Nie oczekiwano zmiany krytycznej.

## <a name="deprecation"></a>Przestarzałe
### <a name="disabled-vm-sizes"></a>Wyłączone rozmiary maszyn wirtualnych
Począwszy od stycznia 9 2021, Usługa HDInsight będzie blokować wszystkim klientom tworzenie klastrów przy użyciu standand_A8, standand_A9, standand_A10 i standand_A11 rozmiary maszyn wirtualnych. Istniejące klastry będą działać w postaci, w jakiej jest. Rozważ przeniesienie do usługi HDInsight 4,0, aby uniknąć potencjalnych przerw w działaniu systemu/obsługi.

## <a name="behavior-changes"></a>Zmiany zachowania
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>Domyślne zmiany rozmiaru maszyny wirtualnej klastra na serię EV3 
Domyślne rozmiary maszyn wirtualnych klastra zostaną zmienione z serii D na EV3. Ta zmiana dotyczy węzłów głównych i węzłów procesu roboczego. Aby uniknąć zmiany wpływającej na przetestowane przepływy pracy, Określ rozmiary maszyn wirtualnych, które mają być używane w szablonie ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Zasób interfejsu sieciowego nie jest widoczny dla klastrów uruchomionych w zestawach skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight jest stopniowo migrowana do zestawów skalowania maszyn wirtualnych platformy Azure. Interfejsy sieciowe dla maszyn wirtualnych nie są już widoczne dla klientów klastrów korzystających z zestawów skalowania maszyn wirtualnych platformy Azure.


### <a name="breaking-change-for-net-for-apache-spark-100"></a>Nieprzerwana zmiana dla programu .NET dla Apache Spark 1.0.0
W najnowszej wersji Usługa HDInsight wprowadza pierwszą oficjalną wersję v 1.0.0 biblioteki [".net for Apache Spark"](https://github.com/dotnet/spark) . Zapewnia kompletność interfejsu API Dataframe dla platformy Spark 2.4. x i Spark 3.0. x wraz z hostem [innych funkcji](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md). Zmiany w tej wersji głównej zostaną zmienione. Zapoznaj się z [przewodnikiem migracji programu .net for Apache Spark](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) , aby poznać kroki wymagane do zaktualizowania kodu i potoków. Aby dowiedzieć się więcej, zapoznaj się z tym [środowiskiem .NET dla Apache Spark v 1.0 w przewodniku usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).


## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wydaniach.

### <a name="default-cluster-version-will-be-changed-to-40"></a>Domyślna wersja klastra zostanie zmieniona na 4,0
Od lutego 2021 domyślna wersja klastra usługi HDInsight zostanie zmieniona z 3,6 na 4,0. Aby uzyskać więcej informacji na temat dostępnych wersji, zobacz [dostępne wersje](./hdinsight-component-versioning.md#available-versions). Dowiedz się więcej na temat Nowości w usłudze [HDInsight 4,0](./hdinsight-version-release.md).

### <a name="os-version-upgrade"></a>Uaktualnienie wersji systemu operacyjnego
Usługa HDInsight uaktualnia wersję systemu operacyjnego z Ubuntu 16,04 do 18,04. Uaktualnienie zakończyło się przed 2021 kwietnia.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Usługa HDInsight 3,6 koniec wsparcia z 30 2021 czerwca
Usługa HDInsight 3,6 zostanie zakończona. Od czerwca 30 2021 klienci nie mogą tworzyć nowych klastrów usługi HDInsight 3,6. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przeniesienie do usługi HDInsight 4,0, aby uniknąć potencjalnych przerw w działaniu systemu/obsługi.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Brak zmian wersji składnika dla tej wersji. Bieżące wersje składników usługi HDInsight 4,0 i HDInsight 3,6 można znaleźć w [tym dokumencie](./hdinsight-component-versioning.md).
