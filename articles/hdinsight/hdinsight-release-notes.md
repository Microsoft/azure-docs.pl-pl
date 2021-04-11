---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: a648ff3aa0c042aaefe16eaae0f9d73953241b3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065501"
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

### <a name="eav4-series-support"></a>Obsługa serii Eav4
Usługa HDInsight dodała obsługę serii Eav4 w tej wersji. Dowiedz się więcej o [serii Dav4 tutaj](../virtual-machines/eav4-easv4-series.md). Seria została udostępniona w następujących regionach: 

* Australia Wschodnia
* Brazylia Południowa
* Central US
* Azja Wschodnia
* East US
* Japonia Wschodnia
* Southeast Asia
* Południowe Zjednoczone Królestwo
* West Europe
* Zachodnie stany USA 2

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

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Pomoc techniczna Basic usługi HDInsight 3,6 od 1 lipca 2021
Od 1 lipca 2021 firma Microsoft będzie oferować [Pomoc techniczna Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) dla niektórych typów klastrów usługi HDInsight 3,6. Plan pomoc techniczna Basic będzie dostępny do 3 kwietnia 2022. Użytkownik zostanie automatycznie zarejestrowany w pomoc techniczna Basic od 1 lipca 2021. Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika. Zapoznaj się z [naszą dokumentacją](hdinsight-36-component-versioning.md) , w której znajdują się typy klastrów w obszarze Pomoc techniczna Basic. 

Nie zalecamy tworzenia nowych rozwiązań w usłudze HDInsight 3,6, Zablokuj zmiany w istniejących środowiskach 3,6. Zalecamy [przeprowadzenie migracji klastrów do usługi HDInsight 4,0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). Dowiedz się więcej o [nowościach w usłudze HDInsight 4,0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Dodano obsługę dla platformy Spark 3.0.0 i Kafka 2.4.1 jako wersja zapoznawcza. Bieżące wersje składników usługi HDInsight 4,0 i HDInsight 3,6 można znaleźć w [tym dokumencie](./hdinsight-component-versioning.md).

## <a name="recommanded-features"></a>Funkcje przepolecenia
### <a name="service-tags"></a>Tagi usługi
Tagi usług upraszczają ograniczanie dostępu sieciowego do usług Azure Virtual Machines i Azure Virtual Networks. Tagi usług w regułach sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) zezwalają lub nie mogą odrzucać ruchu do określonej usługi platformy Azure. Regułę można ustawić globalnie lub dla regionu platformy Azure. Platforma Azure zapewnia konserwację adresów IP poszczególnych tagów. Tagi usługi HDInsight dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) to grupy adresów IP dla usług kondycji i zarządzania. Te grupy pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń. Klienci usługi HDInsight mogą włączyć tag usług za poorednictwem Azure Portal, programu PowerShell i interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Tagi usług sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) dla usługi Azure HDInsight](./hdinsight-service-tags.md).
