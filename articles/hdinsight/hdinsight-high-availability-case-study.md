---
title: Analiza przypadku architektury rozwiązania o wysokiej dostępności usługi Azure HDInsight
description: Ten artykuł stanowi fikcyjną analizę przypadku możliwej architektury rozwiązań o wysokiej dostępności usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Wysoka dostępność usługi Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: a77dba165d76cc131f7a2a25a4b2f62e945a3089
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91844039"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Analiza przypadku architektury rozwiązania o wysokiej dostępności usługi Azure HDInsight

Mechanizmy replikacji usługi Azure HDInsight można zintegrować z architekturą rozwiązania o wysokiej dostępności. W tym artykule, fikcyjne studium przypadku dla sieci sprzedaży firmy Contoso służy do wyjaśnienia możliwych podejścia do odzyskiwania po awarii o wysokiej dostępności, zagadnienia dotyczące kosztów i odpowiednie projekty.

Zalecenia dotyczące odzyskiwania po awarii o wysokiej dostępności mogą mieć wiele permutacji i kombinacji. Te rozwiązania należy uzyskać po wyzwoleniu specjalistów i wad każdej opcji. W tym artykule omówiono tylko jedno z możliwych rozwiązań.

## <a name="customer-architecture"></a>Architektura klienta

Na poniższej ilustracji przedstawiono podstawową architekturę sieci sprzedaży firmy Contoso. Architektura składa się z obciążeń przesyłania strumieniowego, obciążeń wsadowych, obsługi warstwy, warstwy zużycia, warstwy magazynowania i kontroli wersji.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Architektura sieci sprzedaży firmy Contoso":::

### <a name="streaming-workload"></a>Obciążenie przesyłania strumieniowego

Urządzenia i czujniki tworzą dane do usługi HDInsight Kafka, która stanowi strukturę obsługi komunikatów. HDInsight An odczyty użytkowników platformy Spark z tematów Kafka. Platforma Spark przekształca przychodzące komunikaty i zapisuje je w klastrze HBase usługi HDInsight w warstwie obsługującej.

### <a name="batch-workload"></a>Obciążenie wsadowe

HDInsight An klastra Hadoop z uruchomionym programem Hive i MapReduce pobiera dane z lokalnych systemów transakcyjnych. Nieprzetworzone dane przekształcone przez gałąź Hive i MapReduce są przechowywane w tabelach programu Hive na partycji logicznej w usłudze Data Lake, która została utworzona przez Azure Data Lake Storage Gen2. Dane przechowywane w tabelach programu Hive są również udostępniane dla platformy Spark SQL, która wykonuje przekształcenia wsadowe przed zapisaniem nadzorowanych danych w HBase na potrzeby obsługi.

### <a name="serving-layer"></a>Warstwa obsługująca

HDInsight An klaster HBase z Apache Phoenix służy do obsłużenia danych do aplikacji sieci Web i pulpitów nawigacyjnych wizualizacji. HDInsight An klaster LLAP jest używany do spełnienia wymagań związanych z raportowaniem wewnętrznym.

### <a name="consumption-layer"></a>Warstwa zużycia

API Apps platformy Azure i warstwy API Management z powrotem do publicznej strony sieci Web. Wymagania dotyczące raportowania wewnętrznego są spełnione przez Power BI.

### <a name="storage-layer"></a>Warstwa magazynowania

Logicznie podzielony Azure Data Lake Storage Gen2 jest używany jako przedsiębiorstwo Data Lake. Magazyny metadanych usługi HDInsight są obsługiwane przez usługę Azure SQL DB.

### <a name="version-control-system"></a>System kontroli wersji

System kontroli wersji zintegrowany z Azure Pipelinesą i hostowaną poza platformą Azure.

## <a name="customer-business-continuity-requirements"></a>Wymagania dotyczące ciągłości biznesowej klienta

Ważne jest, aby określić minimalną funkcjonalność biznesową, która będzie potrzebna w przypadku awarii.

### <a name="contoso-retails-business-continuity-requirements"></a>Wymagania dotyczące ciągłości biznesowej firmy Contoso

* Firma Microsoft musi być chroniona przed awarią regionalną lub z regionalnymi problemami dotyczącymi usług.
* Moi klienci nigdy nie mogą zobaczyć błędu 404. Zawartość publiczna musi być zawsze obsługiwana. (RTO = 0)  
* W przypadku większości części roku możemy pokazać publiczną zawartość, która jest przestarzała przez 5 godzin. (Cel punktu odzyskiwania = 5 godzin)
* W sezonach świątecznych publiczna zawartość musi być zawsze aktualna. (CEL PUNKTU ODZYSKIWANIA = 0)
* Moje wewnętrzne wymagania dotyczące raportowania nie są uważane za krytyczne dla ciągłości biznesowej.
* Optymalizuj koszty ciągłości biznesowej.

## <a name="proposed-solution"></a>Proponowane rozwiązanie

Na poniższej ilustracji przedstawiono architekturę odzyskiwania po awarii o wysokiej dostępności w sieci sprzedaży firmy Contoso.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Architektura sieci sprzedaży firmy Contoso":::

**Kafka** używa replikacji [Active-pasywnej](hdinsight-business-continuity-architecture.md#apache-kafka) w celu dublowania tematów Kafka z regionu podstawowego do regionu pomocniczego. Alternatywą dla replikacji Kafka może być produkcja na Kafka w obu regionach.

Usługi **Hive i Spark** wykorzystują [aktywne podstawowe modele replikacji na żądanie w](hdinsight-business-continuity-architecture.md#apache-spark) normalnych godzinach. Proces replikacji programu Hive jest uruchamiany okresowo i towarzyszy replikacji metadanych usługi Hive Azure SQL i konta magazynu Hive. Konto magazynu platformy Spark jest okresowo replikowane przy użyciu funkcji ADF pomocą distcp. Przejściowy charakter tych klastrów ułatwia optymalizację kosztów. Replikacja jest zaplanowana co 4 godziny, aby dotrzeć do punktu odzyskiwania, który jest dobrze w okresie pięciu godzin.

Replikacja **HBase** korzysta z modelu [lidera —](hdinsight-business-continuity-architecture.md#apache-hbase) w normalnych godzinach, aby zapewnić, że dane są zawsze obsługiwane niezależnie od regionu, a cel punktu odzyskiwania wynosi zero.

Jeśli w regionie podstawowym wystąpi awaria regionalna, Strona sieci Web i zawartość zaplecza są obsługiwane z regionu pomocniczego przez 5 godzin w pewnym stopniu nieodświeżoności. Jeśli pulpit nawigacyjny kondycji usług platformy Azure nie wskazuje na odzyskiwanie w oknie 5-godzinnym, firma Contoso utworzy warstwę Hive i transformację platformy Spark w regionie pomocniczym, a następnie wskaże wszystkie nadrzędne źródła danych do regionu pomocniczego. Przetworzenie regionu pomocniczego może spowodować proces powrotu po awarii, który obejmuje replikację z powrotem do podstawowego.

W trakcie szczytowego okresu zakupów cały potok pomocniczy jest zawsze aktywny i uruchomiony. Kafka producenci wytwarzają się w obu regionach, a replikacja HBase została zmieniona z Leader-Follower na Leader-Leader, aby zapewnić, że publiczna zawartość jest zawsze aktualna.

Żadne rozwiązanie trybu failover nie musi zostać zaprojektowane na potrzeby raportowania wewnętrznego, ponieważ nie jest to krytyczne dla ciągłości działania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat elementów omówionych w tym artykule, zobacz:

* [Dokumentacja REST Ambari Apache](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* [Zainstaluj i skonfiguruj moduł Azure PowerShell AZ](/powershell/azure/)
* [Zarządzanie usługą HDInsight przy użyciu usługi Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Inicjowanie obsługi klastrów usługi HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)