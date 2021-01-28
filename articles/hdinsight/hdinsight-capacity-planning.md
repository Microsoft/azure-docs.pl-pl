---
title: Planowanie pojemności klastra w usłudze Azure HDInsight
description: Zidentyfikuj kluczowe pytania dotyczące planowania pojemności i wydajności klastra usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 45cfa90f2156dba87cbec2b6313bc24e5d030572
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933229"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planowanie wydajności klastrów usługi HDInsight

Przed wdrożeniem klastra usługi HDInsight zaplanuj odpowiednią pojemność klastra, określając wymaganą wydajność i skalę. Takie planowanie pozwala zoptymalizować zarówno użyteczność, jak i koszty. Niektórych decyzji dotyczących pojemności klastra nie można zmienić po wdrożeniu. W przypadku zmiany parametrów wydajności klaster może zostać rozbudowany i utworzony bez utraty przechowywanych danych.

Kluczowe pytania dotyczące planowania pojemności są następujące:

* W którym regionie geograficznym należy wdrożyć klaster?
* Ile miejsca do magazynowania potrzebujesz?
* Jakiego typu klastra należy wdrożyć?
* Jakiego rozmiaru i typu maszyny wirtualnej należy używać w węzłach klastra?
* Ile węzłów procesu roboczego ma mieć klaster?

## <a name="choose-an-azure-region"></a>Wybierz region platformy Azure

Region platformy Azure określa, gdzie klaster jest fizycznie zainicjowany. Aby zminimalizować opóźnienie operacji odczytu i zapisu, klaster powinien znajdować się w sąsiedztwie danych.

Usługa HDInsight jest dostępna w wielu regionach świadczenia usługi Azure. Aby znaleźć najbliższy region, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Wybierz lokalizację i rozmiar magazynu

### <a name="location-of-default-storage"></a>Lokalizacja magazynu domyślnego

Domyślny magazyn, konto usługi Azure Storage lub Azure Data Lake Storage, musi znajdować się w tej samej lokalizacji co klaster. Usługa Azure Storage jest dostępna we wszystkich lokalizacjach. Data Lake Storage Gen1 jest dostępny w niektórych regionach — zapoznaj się z bieżącą [Data Lake Storage dostępności](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Lokalizacja istniejących danych

Jeśli chcesz użyć istniejącego konta magazynu lub Data Lake Storage jako domyślnego magazynu klastra, musisz wdrożyć klaster w tej samej lokalizacji.

### <a name="storage-size"></a>Rozmiar magazynu

W wdrożonym klastrze można dołączyć dodatkowe konta usługi Azure Storage lub uzyskać dostęp do innych Data Lake Storage. Wszystkie konta magazynu muszą znajdować się w tej samej lokalizacji co klaster. Data Lake Storage może znajdować się w innej lokalizacji, chociaż duże odległości mogą powodować pewne opóźnienia.

Usługa Azure Storage ma pewne [limity pojemności](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), a Data Lake Storage Gen1 jest niemal nieograniczona.

Klaster może uzyskać dostęp do kombinacji różnych kont magazynu. Typowe przykłady to:

* Gdy ilość danych prawdopodobnie przekroczy pojemność magazynu pojedynczego kontenera magazynu obiektów BLOB.
* Gdy częstotliwość dostępu do kontenera obiektów BLOB może przekroczyć próg, w którym występuje ograniczenie przepustowości.
* Gdy chcesz wprowadzić dane, przekazano już do kontenera obiektów BLOB dostępnego w klastrze.
* Jeśli chcesz wyizolować różne części magazynu z powodu zabezpieczeń lub uprościć administrację.

Aby uzyskać lepszą wydajność, należy użyć tylko jednego kontenera na konto magazynu.

## <a name="choose-a-cluster-type"></a>Wybierz typ klastra

Typ klastra określa obciążenie skonfigurowane do uruchomienia klastra usługi HDInsight. Typy obejmują [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)lub [Apache Spark](./spark/apache-spark-overview.md). Aby uzyskać szczegółowy opis dostępnych typów klastrów, zobacz [wprowadzenie do usługi Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Każdy typ klastra ma określoną topologię wdrożenia, która obejmuje wymagania dotyczące rozmiaru i liczby węzłów.

## <a name="choose-the-vm-size-and-type"></a>Wybierz rozmiar i typ maszyny wirtualnej

Każdy typ klastra ma zestaw typów węzłów, a każdy typ węzła ma konkretne opcje rozmiaru i typu maszyny wirtualnej.

Aby określić optymalny rozmiar klastra dla aplikacji, można testować pojemność klastra i zwiększyć rozmiar zgodnie z oczekiwaniami. Można na przykład użyć symulowanego obciążenia lub zapytania w języku *Kanaryjskie*. Uruchamianie symulowanych obciążeń w różnych klastrach rozmiarów. Stopniowo zwiększaj rozmiar do momentu osiągnięcia zamierzonej wydajności. Zapytanie typu Kanaryjskie można wstawiać okresowo między innymi zapytaniami produkcyjnymi, aby pokazać, czy klaster ma wystarczającą ilość zasobów.

Aby uzyskać więcej informacji na temat wybierania odpowiedniej rodziny maszyn wirtualnych dla obciążenia, zobacz [Wybieranie odpowiedniego rozmiaru maszyny wirtualnej w klastrze](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Wybierz skalę klastra

Skalowanie klastra zależy od liczby węzłów maszyn wirtualnych. Dla wszystkich typów klastrów istnieją typy węzłów, które mają określoną skalę i typy węzłów obsługujące skalowanie w poziomie. Na przykład klaster może wymagać dokładnie trzech [Apache ZooKeeper](https://zookeeper.apache.org/) węzłów lub dwóch węzłów głównych. Węzły procesu roboczego, które przetwarzają dane w sposób rozproszony, z dodatkowych węzłów procesu roboczego.

W zależności od typu klastra zwiększenie liczby węzłów procesu roboczego dodaje dodatkową pojemność obliczeniową (na przykład więcej rdzeni). Więcej węzłów spowoduje zwiększenie łącznej ilości pamięci wymaganej przez cały klaster do obsługi magazynu danych przetwarzanych w pamięci. Podobnie jak w przypadku wybrania rozmiaru i typu maszyny wirtualnej, wybór odpowiedniej skali klastra zwykle odbywa się w sposób empiryczny. Używaj symulowanych obciążeń lub zapytań Kanaryjskich.

Klaster można skalować w poziomie, aby spełniał wymagania dotyczące obciążeń szczytowych. Następnie Skaluj ją z powrotem w dół, gdy te dodatkowe węzły nie będą już potrzebne. [Funkcja automatycznego skalowania](hdinsight-autoscale-clusters.md) umożliwia automatyczne skalowanie klastra na podstawie wstępnie określonych metryk i chronometrażu. Aby uzyskać więcej informacji na temat ręcznego skalowania klastrów, zobacz [skalowanie klastrów usługi HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Cykl życia klastra

Opłata jest naliczana za okres istnienia klastra. Jeśli jest potrzebny tylko określony czas, należy [utworzyć klastry na żądanie przy użyciu Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Możesz również utworzyć skrypty programu PowerShell, które inicjują i usuwają klaster, a następnie Zaplanuj te skrypty za pomocą [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Po usunięciu klastra zostanie również usunięty jego domyślny magazyn metadanych Hive. Aby zachować magazyn metadanych dla następnego ponownego tworzenia klastra, Użyj zewnętrznego magazynu danych, takiego jak Azure Database lub [Apache Oozie](https://oozie.apache.org/).

### <a name="isolate-cluster-job-errors"></a>Izolowanie błędów zadań klastra

Czasami mogą wystąpić błędy spowodowane równoległym wykonywaniem wielu map i zmniejszeniem składników w klastrze z wieloma węzłami. Aby pomóc wyizolować ten problem, spróbuj przeprowadzić testowanie rozproszone. Uruchamianie współbieżnych wielu zadań w klastrze węzłów procesu roboczego. Następnie rozwiń to podejście, aby uruchamiać wiele zadań współbieżnie w klastrach zawierających więcej niż jeden węzeł. Aby utworzyć klaster HDInsight z jednym węzłem na platformie Azure, użyj *`Custom(size, settings, apps)`* opcji i użyj wartości 1 dla *Liczba węzłów procesu roboczego* w sekcji **rozmiar klastra** podczas aprowizacji nowego klastra w portalu.

## <a name="quotas"></a>Przydziały

Aby uzyskać więcej informacji na temat zarządzania przydziałami subskrypcji, zobacz temat [żądanie limitu przydziału](quota-increase-request.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastrów w usłudze HDInsight za pomocą Apache Hadoop, Spark, Kafka i innych](hdinsight-hadoop-provision-linux-clusters.md): informacje na temat konfigurowania i konfigurowania klastrów w usłudze HDInsight.
* [Monitorowanie wydajności klastra](hdinsight-key-scenarios-to-monitor.md): informacje na temat kluczowych scenariuszy monitorowania klastrów usługi HDInsight, które mogą wpływać na wydajność klastra.
