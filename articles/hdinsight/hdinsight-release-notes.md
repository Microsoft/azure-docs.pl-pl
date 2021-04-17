---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji dla Azure HDInsight. Uzyskaj porady i szczegółowe informacje dotyczące rozwoju platform Hadoop, Spark, R Server, Hive i innych.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 838eb517697c0625139058a19c7def764e869ed5
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588177"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight informacji o wersji

Ten artykuł zawiera informacje o **najnowszych aktualizacjach** Azure HDInsight wersji. Aby uzyskać informacje na temat wcześniejszych wersji, zobacz HdInsight Release Notes Archive (Archiwum informacji o wersji [hdinsight).](hdinsight-release-notes-archive.md)

## <a name="summary"></a>Podsumowanie

Azure HDInsight jest jedną z najpopularniejszych usług wśród klientów korporacyjnych na potrzeby analizy typu open source na platformie Azure.

Jeśli chcesz subskrybować informacje o wersji, obejrzyj wydania w tym [repozytorium GitHub.](https://github.com/hdinsight/release-notes/releases)

## <a name="release-date-03242021"></a>Data wydania: 24.03.2021

Ta wersja dotyczy zarówno hdinsight 3.6, jak i HDInsight 4.0. Wersja hdInsight jest dostępna we wszystkich regionach w ciągu kilku dni. Data wydania wskazuje datę wydania pierwszego regionu. Jeśli nie widzisz poniższych zmian, zaczekaj na wydanie w Twoim regionie w ciągu kilku dni.

## <a name="new-features"></a>Nowe funkcje
### <a name="spark-30-preview"></a>Spark 3.0 (wersja zapoznawcza)
Dodano obsługę [platformy Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) w umacie HDInsight 4.0 jako funkcję w wersji zapoznawczej. 

### <a name="kafka-24-preview"></a>Kafka 2.4 (wersja zapoznawcza)
W umacie HDInsight dodano obsługę platformy [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) do usługi HDInsight 4.0 jako funkcji w wersji zapoznawczej.

### <a name="eav4-series-support"></a>Obsługa serii Eav4
W tej wersji dodano obsługę serii Eav4 w umacie HDInsight. Dowiedz się więcej [o serii Dav4 tutaj.](../virtual-machines/eav4-easv4-series.md) Seria została udostępnione w następujących regionach: 

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

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przenoszenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight aprowizuje klaster przy użyciu maszyn wirtualnych platformy Azure. Usługa jest stopniowo migrowana do zestawów [skalowania maszyn wirtualnych platformy Azure.](../virtual-machine-scale-sets/overview.md) Cały proces może potrwać kilka miesięcy. Po migracji regionów i subskrypcji nowo utworzone klastry usługi HDInsight będą uruchamiane w zestawach skalowania maszyn wirtualnych bez akcji klienta. Zmiana nie jest oczekiwana.

## <a name="deprecation"></a>Przestarzałe
W tej wersji nie ma możliwości jej cofania.

## <a name="behavior-changes"></a>Zmiany zachowania
### <a name="default-cluster-version-is-changed-to-40"></a>Domyślna wersja klastra zostanie zmieniona na 4.0
Domyślna wersja klastra usługi HDInsight zostanie zmieniona z 3.6 na 4.0. Aby uzyskać więcej informacji na temat dostępnych wersji, zobacz [dostępne wersje](./hdinsight-component-versioning.md). Dowiedz się więcej o nowościach w [umacie HDInsight 4.0.](./hdinsight-version-release.md)

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Domyślne rozmiary maszyn wirtualnych klastra zostały zmienione na serię Ev3 
Domyślne rozmiary maszyn wirtualnych klastra są zmieniane z serii D na serie Ev3. Ta zmiana dotyczy węzłów głównych i węzłów roboczych. Aby uniknąć tej zmiany wpływającej na przetestowane przepływy pracy, określ rozmiary maszyn wirtualnych, których chcesz użyć w szablonie usługi ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Zasób interfejsu sieciowego nie jest widoczny dla klastrów uruchomionych w zestawach skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight stopniowo migruje do zestawów skalowania maszyn wirtualnych platformy Azure. Interfejsy sieciowe dla maszyn wirtualnych nie są już widoczne dla klientów w przypadku klastrów korzystających z zestawów skalowania maszyn wirtualnych platformy Azure.

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wprowadzone w przyszłych wersjach.

### <a name="os-version-upgrade"></a>Uaktualnianie wersji systemu operacyjnego
Klastry usługi HDInsight są obecnie uruchomione w systemie Ubuntu 16.04 LTS. Jak powołuje się w cyklu wydawniowym systemu [Ubuntu,](https://ubuntu.com/about/release-cycle)jądro Ubuntu 16.04 osiągnie koniec cyklu życia (EOL) w kwietniu 2021 r. W maju 2021 r. zaczniemy wprowadzać nowy obraz klastra usługi HDInsight 4.0 uruchomiony w systemie Ubuntu 18.04. Nowo utworzone klastry usługi HDInsight 4.0 będą domyślnie uruchamiane w systemie Ubuntu 18.04, gdy będą dostępne. Istniejące klastry w systemie Ubuntu 16.04 będą działać tak, jak są z pełną obsługą.

HdInsight 3.6 będzie nadal działać w systemie Ubuntu 16.04. Obsługa standardowa zakończy się do 30 czerwca 2021 r. i zmieni się na pomoc techniczna Basic 1 lipca 2021 r. Aby uzyskać więcej informacji na temat dat i opcji pomocy technicznej, [zobacz Azure HDInsight wersji](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). System Ubuntu 18.04 nie będzie obsługiwany w przypadku wersji HDInsight 3.6. Jeśli chcesz używać systemu Ubuntu 18.04, musisz przeprowadzić migrację klastrów do usługi HDInsight 4.0. 

Jeśli chcesz przenieść istniejące klastry do systemu Ubuntu 18.04, musisz usunąć i ponownie utworzyć klastry. Zaplanuj utworzenie lub ponowne utworzenie klastra po tym, jak obsługa systemu Ubuntu 18.04 stanie się dostępna. Po tym, jak nowy obraz stanie się dostępny we wszystkich regionach, wyślemy kolejne powiadomienie.

Zdecydowanie zaleca się wcześniejsze przetestowanie akcji skryptu i aplikacji niestandardowych wdrożonych w węzłach brzegowych na maszynie wirtualnej z systemem Ubuntu 18.04. Możesz utworzyć prostą maszynę wirtualną usługi Ubuntu Linux na maszynie wirtualnej w trybie [18.04-LTS, a](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)następnie utworzyć parę kluczy Secure [Shell (SSH)](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys#ssh-into-your-vm) na maszynie wirtualnej i użyć jej do uruchamiania i testowania akcji skryptu i niestandardowych aplikacji wdrożonych w węzłach brzegowych.

### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsgiht-40"></a>Wyłącz Stardard_A5 maszyny wirtualnej jako węzeł główny dla usługi HDInsgiht 4.0
Węzeł główny klastra usługi HDInsight jest odpowiedzialny za inicjowanie klastra i zarządzanie tym klastrem. Standard_A5 maszyny wirtualnej ma problemy z niezawodnością jako węzeł główny dla usługi HDInsight 4.0. Począwszy od następnej wersji w maju 2021 r., klienci nie będą mogli tworzyć nowych klastrów z rozmiarem maszyny Standard_A5 jako węzłem głównym. Możesz użyć innych 2-rdzeniowych maszyn wirtualnych, takich jak E2_v3 lub E2s_v3. Istniejące klastry będą działać bez skojarów. 4-rdzeniowa maszyna wirtualna jest zdecydowanie zalecana dla węzła głównego, aby zapewnić wysoką dostępność i niezawodność produkcyjnych klastrów usługi HDInsight.

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>pomoc techniczna Basic dla hdinsight 3.6 począwszy od 1 lipca 2021 r.
Od 1 lipca 2021 r. firma Microsoft będzie pomoc techniczna Basic [dla](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) niektórych typów klastrów usługi HDInsight 3.6. Plan pomoc techniczna Basic będzie dostępny do 3 kwietnia 2022 r. Od 1 lipca 2021 pomoc techniczna Basic 2021 r. Rezygnacja z tej opcji nie wymaga żadnej akcji. Zapoznaj [się z naszą dokumentacją](hdinsight-36-component-versioning.md) dotyczącą typów klastrów uwzględnionych w pomoc techniczna Basic. 

Nie zalecamy budowania nowych rozwiązań w umacie HDInsight 3.6, blokowania zmian w istniejących środowiskach 3.6. Zalecamy migrację [klastrów do usługi HDInsight 4.0.](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40) Dowiedz się więcej [o nowościach w umacie HDInsight 4.0.](hdinsight-version-release.md#whats-new-in-hdinsight-40)

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight w dalszym ciągu ulepsza niezawodność i wydajność klastra. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Dodano obsługę platform Spark 3.0.0 i Kafka 2.4.1 jako wersji zapoznawczej. Bieżące wersje składników dla hdinsight 4.0 i HDInsight 3.6 można znaleźć w [tym doc.](./hdinsight-component-versioning.md)

## <a name="recommanded-features"></a>Recommanded features (Recommanded features)
### <a name="service-tags"></a>Tagi usługi
Tagi usług upraszczają ograniczanie dostępu sieciowego do usług platformy Azure dla maszyn wirtualnych platformy Azure i sieci wirtualnych platformy Azure. Tagi usług w zasadach sieciowej grupy zabezpieczeń zezwalają na ruch do określonej usługi platformy Azure lub go odrzucają. Regułę można ustawić globalnie lub dla każdego regionu świadczenia usługi Azure. Platforma Azure zapewnia obsługę adresów IP poszczególnych tagów. Tagi usługi HDInsight dla sieciowych grup zabezpieczeń (NSG) to grupy adresów IP dla usług kondycji i zarządzania. Te grupy pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń. Klienci usługi HDInsight mogą włączyć tag usługi za pośrednictwem Azure Portal, programu PowerShell i interfejsu API REST. Aby uzyskać więcej informacji, zobacz Tagi usługi sieciowej grupy zabezpieczeń dla [Azure HDInsight](./hdinsight-service-tags.md).
