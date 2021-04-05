---
title: Ciągłość biznesowa usługi Azure HDInsight
description: Ten artykuł zawiera omówienie najlepszych rozwiązań, dostępności z jednego regionu i opcji optymalizacji dla planowania ciągłości biznesowej usługi Azure HDInsight.
keywords: Wysoka dostępność usługi Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 74f8bdd26e000b89bfae84102077c241f85abf7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933316"
---
# <a name="azure-hdinsight-business-continuity"></a>Ciągłość biznesowa usługi Azure HDInsight

Klastry usługi Azure HDInsight są zależne od wielu usług platformy Azure, takich jak Storage, Databases, Active Directory, Active Directory Domain Services, Networking i Key Vault. Dobrze zaprojektowana, wysoce dostępna i odporna na błędy aplikacja do analizy powinna być zaprojektowana pod kątem wystarczającej nadmiarowości, aby wytrzymywać zakłócenia regionalne lub lokalne w jednej lub kilku tych usługach. Ten artykuł zawiera omówienie najlepszych rozwiązań, dostępności z jednego regionu i opcji optymalizacji związanych z ciągłością prowadzenia działalności biznesowej.

## <a name="general-best-practices"></a>Ogólne najlepsze praktyki

W tej sekcji omówiono kilka najlepszych rozwiązań, które należy wziąć pod uwagę podczas planowania ciągłości działalności biznesowej.

* Określ minimalne funkcje biznesowe, które będą potrzebne, jeśli wystąpi awaria i dlaczego. Na przykład Oceń, czy potrzebujesz możliwości pracy w trybie failover dla warstwy przekształcenia danych (pokazanej kolorem żółtym) *i* warstwy obsługującej dane (widocznej na niebiesko), lub jeśli potrzebujesz tylko trybu failover dla warstwy usługi danych.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="Przekształcanie danych i zachowywanie danych w warstwach":::

* Segmentowanie klastrów w oparciu o obciążenie, cykl programowania i działy. Więcej klastrów zmniejsza szanse na to, że pojedynczy błąd ma wpływ na wiele różnych procesów roboczych.

* Uczyń region pomocniczy tylko do odczytu. Regiony trybu failover z możliwością odczytu i zapisu mogą prowadzić do złożonych architektur.

* Klastry przejściowe są łatwiejsze do zarządzania w przypadku awarii. Zaprojektuj swoje obciążenia w sposób, w jaki klastry mogą być przetwarzane i nie są obsługiwane żadne Stany w klastrach.

* Często obciążenia są pozostawiane nieukończone w przypadku awarii i konieczności ponownego uruchomienia w nowym regionie. Zaprojektuj swoje obciążenia, tak aby idempotentne się w charakterze.

* Używaj automatyzacji podczas wdrożeń klastra i upewnij się, że ustawienia konfiguracji klastra są obsługiwane w miarę możliwości, aby zapewnić szybkie i w pełni zautomatyzowane wdrażanie w przypadku wystąpienia awarii.

* Użyj narzędzi monitorowania platformy Azure w usłudze HDInsight, aby wykryć nietypowe zachowanie w klastrze i ustawić odpowiednie powiadomienia o alertach. Można wdrożyć wstępnie skonfigurowane rozwiązania do zarządzania specyficzne dla klastra usługi HDInsight, które zbierają ważne metryki wydajności określonego typu klastra. Aby uzyskać więcej informacji, zobacz [Azure Monitoring for HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Zasubskrybuj alerty dotyczące kondycji platformy Azure, aby otrzymywać powiadomienia o problemach z usługą, planowanej konserwacji, kondycji i klasyfikatorach zabezpieczeń dla subskrypcji, usługi lub regionu. Powiadomienia o kondycji, które obejmują przyczynę problemu, i Resolute EZT pomagają w lepszym przejściu do trybu failover i failbacks. Aby uzyskać więcej informacji, zobacz [dokumentację Azure Service Health](../service-health/index.yml).

## <a name="single-region-availability"></a>Dostępność pojedynczego regionu

Podstawowy system HDInsight ma następujące składniki. Wszystkie składniki mają własne mechanizmy odporności na uszkodzenia w jednym regionie.

* Obliczenia (maszyny wirtualne): klaster usługi Azure HDInsight
* Magazyny metadanych: Azure SQL Database
* Magazyn: Azure Data Lake Gen2 lub BLOB Storage
* Uwierzytelnianie: Azure Active Directory, Azure Active Directory Domain Services, pakiet Enterprise Security
* Rozpoznawanie nazw domen: Azure DNS

Istnieją inne opcjonalne usługi, które mogą być używane, takie jak Azure Key Vault i Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="Składniki usługi HDInsight":::

### <a name="azure-hdinsight-cluster-compute"></a>Klaster usługi Azure HDInsight (usługa COMPUTE)

Usługa HDInsight oferuje umowę SLA dotyczącą dostępności 99,9%. Aby zapewnić wysoką dostępność w jednym wdrożeniu, domyślnie towarzyszy wiele usług, które są w trybie wysokiej dostępności. Mechanizmy odporności na uszkodzenia w usłudze HDInsight są udostępniane przez usługi wysokiej dostępności w ekosystemie firmy Microsoft i Apache OSS.

Następujące usługi zostały zaprojektowane tak, aby były wysoce dostępne:

#### <a name="infrastructure"></a>Infrastruktura

* Aktywne i węzłów głównych w stanie wstrzymania
* Wiele węzłów bramy
* Trzy węzły kworum dozorcy
* Węzły procesu roboczego dystrybuowane według domen błędów i aktualizacji

#### <a name="service"></a>Usługa

* Serwer Apache Ambari
* Serwery osi czasu aplikacji dla PRZĘDZy
* Serwer historii zadań dla usługi Hadoop MapReduce
* Apache Livy
* SYSTEM PLIKÓW HDFS
* Menedżer zasobów PRZĘDZy
* HBase Master

Zapoznaj się z dokumentacją dotyczącą [usług wysokiej dostępności obsługiwanych przez usługę Azure HDInsight](./hdinsight-high-availability-components.md) , aby dowiedzieć się więcej.

Nie zawsze trwa katastrofalne zdarzenie mające wpływ na funkcjonalność biznesową. Zdarzenia usługi w co najmniej jednej z następujących usług w jednym regionie mogą również prowadzić do utraty oczekiwanej funkcjonalności biznesowej.

### <a name="hdinsight-metastore"></a>Magazyn metadanych usługi HDInsight

Usługa HDInsight używa [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) jako magazynu metadanych, który oferuje umowę SLA wynoszącą 99,99%. Trzy repliki danych pozostają w centrum danych z replikacją synchroniczną. W przypadku utraty repliki alternatywna replika jest obsługiwana bezproblemowo. [Aktywna replikacja geograficzna](../azure-sql/database/active-geo-replication-overview.md) jest obsługiwana z użyciem maksymalnie czterech centrów danych. W przypadku przejścia w tryb failover lub centrum danych pierwsza replika w hierarchii zostanie automatycznie przeniesiona do trybu odczytu i zapisu. Aby uzyskać więcej informacji, zobacz [Azure SQL Database ciągłość](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)działania.

### <a name="hdinsight-storage"></a>HDInsight Storage

Usługa HDInsight zaleca Azure Data Lake Storage Gen2 jako podstawową warstwę magazynu. [Usługa Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), w tym Azure Data Lake Storage Gen2, oferuje umowę SLA na 99,9%. Usługa HDInsight korzysta z usługi LRS, w której trzy repliki danych pozostają w centrum danych, a replikacja jest synchroniczna. W przypadku utraty repliki, replika jest obsługiwana bezproblemowo.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) oferuje umowę SLA na 99,9%. Active Directory to globalna usługa obejmująca wiele poziomów wewnętrznej nadmiarowości i automatyczne odzyskiwanie. Aby uzyskać więcej informacji, zobacz jak [firma Microsoft nieustannie ulepsza niezawodność Azure Active Directory](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/).

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) oferuje umowę SLA na 99,9%. Azure AD DS to usługa o wysokiej dostępności hostowana w globalnie rozproszonych centrach danych. Zestawy replik to funkcja w wersji zapoznawczej platformy Azure AD DS, która umożliwia odzyskiwanie po awarii geograficznej, jeśli region platformy Azure przejdzie w tryb offline. Aby uzyskać więcej informacji, zobacz [pojęcia i funkcje programu Replica dotyczące Azure Active Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md) , aby dowiedzieć się więcej.  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) oferuje umowę SLA na 100%. Usługa HDInsight używa Azure DNS w różnych miejscach do rozpoznawania nazw domen.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Optymalizacje kosztów i złożoności dla wieloregionu

Poprawa ciągłości działania przy użyciu funkcji odzyskiwania awaryjnego o wysokiej dostępności obejmującej wiele regionów wymaga projektów architektonicznych o wyższej złożoności i wyższych kosztach. W poniższych tabelach szczegółowo przedstawiono niektóre obszary techniczne, które mogą zwiększyć całkowity koszt posiadania.

### <a name="cost-optimizations"></a>Optymalizacje kosztów

|Warstwowy|Przyczyna eskalacji kosztów|Strategie optymalizacji|
|----|------------------------|-----------------------|
|Magazyn danych|Duplikowanie podstawowych danych/tabel w regionie pomocniczym|Replikuj tylko nadzorowane dane|
|Dane wychodzące|Wychodzące transfery danych między regionami są oferowane w cenie. Przegląd wytycznych dotyczących cen przepustowości|Replikuj tylko nadzorowane dane, aby zmniejszyć poziom ruchu wychodzącego z regionu|
|Obliczenia klastra|Dodatkowe klastry usługi HDInsight/s w regionie pomocniczym|Użyj skryptów automatycznych do wdrożenia dodatkowych obliczeń po awarii podstawowej. Użyj skalowania automatycznego, aby zapewnić minimalny rozmiar klastra pomocniczego. Używaj tańszych jednostek SKU maszyn wirtualnych. Tworzenie serwerów pomocniczych w regionach, w których mogą zostać obniżone liczby jednostek SKU maszyn wirtualnych.|
|Authentication |Scenariusze wieloużytkownikom w regionie pomocniczym będą naliczane dodatkowe konfiguracje usługi Azure AD DS|Unikaj konfigurowania wieloużytkownikom w regionie pomocniczym.|

### <a name="complexity-optimizations"></a>Optymalizacje złożoności

|Warstwowy|Przyczyna eskalacji złożoności|Strategie optymalizacji|
|----|------------------------|-----------------------|
|Odczytywanie wzorców zapisu |Wymaganie zarówno odczytu, jak i zapisu |Zaprojektuj pomocniczą wartość tylko do odczytu|
|Zero RPO & RTO |Wymaganie zerowej utraty danych (RPO = 0) i zero przestojów (RTO = 0) |Zaprojektuj cel punktu odzyskiwania i RTO w celu zmniejszenia liczby składników, które wymagają przełączenia w tryb failover.|
|Funkcje biznesowe |Wymaganie pełnej funkcjonalności biznesowej dla elementu podstawowego |Oceń, czy można uruchomić system z minimalnym, krytycznym podzbiorem funkcjonalności biznesowej w części pomocniczej.|
|Łączność |Wymaganie, aby wszystkie systemy nadrzędne i podrzędne były połączone z serwerem pomocniczym.|Ogranicz łączność dodatkową do najmniejszego poziomu krytycznego dla systemu operacyjnego.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat elementów omówionych w tym artykule, zobacz:

* [Architektury ciągłości biznesowej usługi Azure HDInsight](./hdinsight-business-continuity-architecture.md)
* [Analiza przypadku architektury rozwiązania o wysokiej dostępności usługi Azure HDInsight](./hdinsight-high-availability-case-study.md)
* [Co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](./hadoop/hdinsight-use-hive.md)