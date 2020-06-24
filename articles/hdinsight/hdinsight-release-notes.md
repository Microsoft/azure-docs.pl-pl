---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: c7bc818133a0bd708f9f850f0ad258dccc6c02cc
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737992"
---
# <a name="release-notes"></a>Informacje o wersji

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

## <a name="release-date-06112020"></a>Data wydania: 06/11/2020

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w kilka dni.

## <a name="new-features"></a>Nowe funkcje
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight używa teraz usługi Azure Virtual Machines w celu udostępnienia klastra. W tej wersji nowe utworzone klastry usługi HDInsight zaczynają korzystać z zestawu skalowania maszyn wirtualnych platformy Azure. Zmiana jest stopniowo wdrażana. Nie należy oczekiwać żadnej zmiany. Zobacz więcej informacji o [zestawach skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>Ponowne uruchamianie maszyn wirtualnych w klastrze usługi HDInsight
W tej wersji obsługujemy ponowne uruchamianie maszyn wirtualnych w klastrze usługi HDInsight w celu ponownego uruchomienia węzłów, które nie odpowiadają. Obecnie można wykonać te czynności tylko za pomocą interfejsu API, obsługi programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać więcej informacji na temat interfejsu API, zobacz [ten dokument](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json).
 
## <a name="deprecation"></a>Przestarzałe
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Zakończenie obsługi platformy Spark 2.1 i 2.2 w klastrze platformy Spark w usłudze HDInsight 3.6
Począwszy od lipca 1 2020, klienci nie mogą tworzyć nowych klastrów Spark z platformami Spark 2,1 i 2,2 w usłudze HDInsight 3,6. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście do platformy Spark 2,3 w usłudze HDInsight 3,6 do czerwca 30 2020, aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Zakończenie obsługi platformy Spark 2.3 w klastrze platformy Spark w usłudze HDInsight 4.0
Począwszy od 1 2020 lipca, klienci nie mogą tworzyć nowych klastrów Spark z platformą Spark 2,3 w usłudze HDInsight 4,0. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście na platformę Spark 2.4 w usłudze HDInsight 4.0 do 30 czerwca 2020 r., aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Zakończenie obsługi platformy Kafka 1.1 w klastrze platformy Kafka w usłudze HDInsight 4.0
Począwszy od lipca 1 2020, klienci nie będą mogli tworzyć nowych klastrów Kafka z Kafka 1,1 w usłudze HDInsight 4,0. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście na platformę Kafka 2.1 w usłudze HDInsight 4.0 do 30 czerwca 2020 r., aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.
 
## <a name="behavior-changes"></a>Zmiany zachowania
### <a name="esp-spark-cluster-head-node-size-change"></a>Zmiana rozmiaru węzła głównego klastra platformy Spark (ESP) 
Minimalny dozwolony rozmiar węzła głównego dla klastra ESP Spark został zmieniony na Standard_D13_V2. Maszyny wirtualne z niskimi rdzeniami i pamięcią jako węzeł główny mogą powodować problemy z klastrem ESP ze względu na stosunkowo niską pojemność procesora i pamięci. Począwszy od wersji, użyj jednostek SKU wyższych niż Standard_D13_V2 i Standard_E16_V3 jako węzeł główny dla klastrów ESP Spark.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Minimalna 4-rdzeniowa maszyna wirtualna jest wymagana dla węzła głównego. 
Aby węzeł główny zapewniał wysoką dostępność i niezawodność klastrów usługi HDInsight, wymagana jest co najmniej 4 rdzenie maszyny wirtualnej. Począwszy od kwietnia 6 2020, klienci mogą wybrać tylko 4-rdzeniową lub wyższą maszynę wirtualną jako węzeł główny dla nowych klastrów usługi HDInsight. Istniejące klastry będą nadal działać zgodnie z oczekiwaniami. 
 
### <a name="cluster-worker-node-provisioning-change"></a>Zmiana aprowizacji węzła procesu roboczego klastra
Gdy 80% węzłów procesu roboczego jest gotowych, klaster przechodzi do etapu **operacyjnego** . Na tym etapie klienci mogą wykonywać wszystkie operacje płaszczyzny danych, takie jak uruchamianie skryptów i zadań. Jednak klienci nie mogą wykonywać operacji na płaszczyźnie sterowania, takich jak skalowanie w górę/w dół. Tylko usuwanie jest obsługiwane.
 
Po etapie **działania** klaster czeka na kolejne 60 minut dla pozostałych 20% węzłów procesu roboczego. Po upływie tego czasu 60 minut klaster przechodzi do **uruchomionego** etapu, nawet jeśli wszystkie węzły procesu roboczego nadal są niedostępne. Gdy klaster przechodzi na **uruchomiony** etap, można go użyć jako normalnego. Oba operacje planu kontroli, takie jak skalowanie w górę/w dół, oraz operacje planowania danych, takie jak uruchamianie skryptów i zadań, są akceptowane. Jeśli niektóre z żądanych węzłów procesu roboczego są niedostępne, klaster zostanie oznaczony jako częściowe powodzenie. Opłaty są naliczone za węzły, które zostały pomyślnie wdrożone. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>Utwórz nową nazwę główną usługi za usługą HDInsight
Wcześniej dzięki utworzeniu klastra klienci mogą utworzyć nową nazwę główną usługi, aby uzyskać dostęp do połączonego konta ADLS Gen 1 w Azure Portal. Od czerwca 15 2020 klienci nie mogą utworzyć nowej jednostki usługi w przepływie pracy tworzenia w usłudze HDInsight, obsługiwana jest tylko Istniejąca jednostka usługi. Zobacz [Tworzenie nazwy głównej usługi i certyfikatów przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
 
## <a name="upcoming-changes"></a>Nadchodzące zmiany
Brak przyszłych zmian, do których należy zwrócić uwagę.
 
## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 
 
## <a name="component-version-change"></a>Zmiana wersji składnika
### <a name="hbase-20-to-216"></a>HBase 2,0 do 2.1.6
Wersja HBase jest uaktualniana z wersji 2,0 do 2.1.6.
 
### <a name="spark-240-to-244"></a>Platforma Spark 2.4.0 do 2.4.4
Wersja platformy Spark została uaktualniona z wersji 2.4.0 do 2.4.4.
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 do 2.1.1
Wersja Kafka jest uaktualniana z wersji 2.1.0 do 2.1.1.
 
Bieżące wersje składników usługi HDInsight 4,0 AD HDInsight 3,6 można znaleźć w [tym dokumencie](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

