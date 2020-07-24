---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006538"
---
# <a name="azure-hdinsight-release-notes"></a>Informacje o wersji usługi Azure HDInsight

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

## <a name="release-date-07132020"></a>Data wydania: 07/13/2020

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w kilka dni.

## <a name="new-features"></a>Nowe funkcje
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Obsługa Skrytka klienta dla Microsoft Azure
Usługa Azure HDInsight obsługuje teraz usługę Azure Skrytka klienta. Umożliwia ona klientom przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych klienta. Jest on używany, gdy inżynier firmy Microsoft musi uzyskać dostęp do danych klienta w trakcie żądania obsługi. Aby uzyskać więcej informacji, zobacz [Skrytka klienta Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview).

### <a name="service-endpoint-policies-for-storage"></a>Zasady punktu końcowego usługi dla magazynu
Klienci mogą teraz używać zasad punktu końcowego usług (SEP) w podsieci klastra usługi HDInsight. Dowiedz się więcej na temat [zasad punktu końcowego usługi platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Przestarzałe
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Zakończenie obsługi platformy Spark 2.1 i 2.2 w klastrze platformy Spark w usłudze HDInsight 3.6
Począwszy od lipca 1 2020, klienci nie mogą tworzyć nowych klastrów Spark z platformami Spark 2,1 i 2,2 w usłudze HDInsight 3,6. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście do platformy Spark 2,3 w usłudze HDInsight 3,6 do czerwca 30 2020, aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Zakończenie obsługi platformy Spark 2.3 w klastrze platformy Spark w usłudze HDInsight 4.0
Począwszy od 1 2020 lipca, klienci nie mogą tworzyć nowych klastrów Spark z platformą Spark 2,3 w usłudze HDInsight 4,0. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście na platformę Spark 2.4 w usłudze HDInsight 4.0 do 30 czerwca 2020 r., aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Zakończenie obsługi platformy Kafka 1.1 w klastrze platformy Kafka w usłudze HDInsight 4.0
Począwszy od lipca 1 2020, klienci nie będą mogli tworzyć nowych klastrów Kafka z Kafka 1,1 w usłudze HDInsight 4,0. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przejście na platformę Kafka 2.1 w usłudze HDInsight 4.0 do 30 czerwca 2020 r., aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.

## <a name="behavior-changes"></a>Zmiany zachowania
Nie ma żadnych zmian w zachowaniu, do których należy zwrócić uwagę.

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wydaniach. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Możliwość wybrania innej jednostki SKU dozorcy dla usług Spark, Hadoop i ML
Usługa HDInsight obecnie nie obsługuje zmieniania jednostki SKU dozorcy dla typów klastrów usług Spark, Hadoop i ML. Używa A2_v2 jednostki SKU/a2 dla węzłów dozorcy i klienci nie są dla nich obciążani. W nadchodzącym wydaniu klienci będą mogli zmienić dozorcy jednostki SKU dla usług Spark, Hadoop i ML zgodnie z potrzebami. Dozorcy węzły z jednostką SKU inną niż A2_v2/a2 zostaną rozliczone. Domyślna jednostka SKU będzie nadal A2_V2/a2 i bezpłatna.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Problem z rozwiązanym łącznikiem magazynu Hive
Wystąpił problem z użytecznością łącznika magazynu Hive w poprzedniej wersji. Problem został rozwiązany. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Stały problem z obcinaniem wiodących notesów Zeppelin
Zeppelin nieprawidłowo obciąć wiodących zer w tabeli wyjściowej w formacie ciągu. Rozwiązano ten problem w tej wersji.

## <a name="component-version-change"></a>Zmiana wersji składnika
Brak zmian wersji składnika dla tej wersji. Bieżące wersje składników usługi HDInsight 4,0 i HDInsight 3,6 można znaleźć w [tym dokumencie](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).
