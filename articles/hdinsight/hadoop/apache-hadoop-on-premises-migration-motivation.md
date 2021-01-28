---
title: 'Zalety: Migrowanie Apache Hadoop lokalnych do usługi Azure HDInsight'
description: Poznaj motywację i zalety migracji lokalnych klastrów Hadoop do usługi Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 975d72df32027888e217d5da9171dba0ba61f257
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943252"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — motywacji i korzyści

Ten artykuł jest pierwszą z serii z najlepszymi rozwiązaniami dotyczącymi migracji lokalnych Apache Hadoop wdrożeń systemu do usługi Azure HDInsight. Ta seria artykułów dotyczy osób, które są odpowiedzialne za projektowanie, wdrażanie i migrację rozwiązań Apache Hadoop w usłudze Azure HDInsight. Role, które mogą korzystać z tych artykułów, obejmują architektów w chmurze, administratorów usługi Hadoop i inżynierów DevOps. Deweloperzy oprogramowania, inżynierowie danych i analityki danych powinni również skorzystać z wyjaśnienia, jak różne typy klastrów działają w chmurze.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Dlaczego należy przeprowadzić migrację do usługi Azure HDInsight

Usługa Azure HDInsight to dystrybucja w chmurze składników usługi Hadoop. Usługa Azure HDInsight ułatwia i przyspiesza przetwarzanie ogromnych ilości danych przy jednoczesnej minimalizacji kosztów. Usługa HDInsight zawiera najpopularniejsze platformy typu "open source", takie jak:

- Apache Hadoop
- Apache Spark
- Apache Hive z LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Zalety usługi Azure HDInsight za pośrednictwem lokalnej usługi Hadoop

- **Niski koszt** — koszty można zmniejszyć, [tworząc klastry na żądanie](../hdinsight-hadoop-create-linux-clusters-adf.md) i płacąc tylko za to, czego używasz. Rozbudowane zasoby obliczeniowe i magazyn zapewniają elastyczność przez utrzymywanie ilości danych niezależnie od rozmiaru klastra.

- **Automatyczne tworzenie klastra** — automatyczne tworzenie klastra wymaga minimalnej instalacji i konfiguracji. Automatyzacja może być używana w przypadku klastrów na żądanie.

- **Zarządzanie sprzętem i konfiguracją** — nie trzeba martwić się o fizyczny sprzęt lub infrastrukturę z klastrem usługi HDInsight. Po prostu Określ konfigurację klastra, a platforma Azure skonfiguruje ją.

- **Łatwa skalowalność** — Usługa HDInsight umożliwia [skalowanie](../hdinsight-administer-use-portal-linux.md) obciążeń w górę lub w dół. Platforma Azure obsługuje ponowną dystrybucję danych i ponowne równoważenie obciążenia bez przerywania zadań przetwarzania danych.

- **Globalna dostępność** — Usługa HDInsight jest dostępna w większej liczbie [regionów](https://azure.microsoft.com/regions/services/) niż w przypadku innych ofert analizy danych Big Data. Usługa Azure HDInsight jest również dostępna w ramach chmur Azure Government, Azure (Chiny) i Azure (Niemcy), dzięki czemu odpowiada na potrzeby Twojego przedsiębiorstwa w najważniejszych obszarach suwerenności.

- **Bezpieczne i zgodne** usługi HDInsight umożliwiają ochronę zasobów danych przedsiębiorstwa przy użyciu [usługi Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md), [szyfrowania](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)i integracji z [Azure Active Directory](../domain-joined/hdinsight-security-overview.md). Usługa HDInsight spełnia również najpopularniejsze branżowe i rządowe [normy zgodności](https://azure.microsoft.com/overview/trusted-cloud).

- **Uproszczone zarządzanie wersjami** — usługa Azure HDInsight zarządza wersjami składników systemu Hadoop i zachowuje je na bieżąco. Aktualizacje oprogramowania są zwykle złożonymi procesami wdrożeń lokalnych.

- **Mniejsze klastry zoptymalizowane pod kątem określonych obciążeń o mniejszej liczbie zależności między składnikami** — typowa lokalna konfiguracja usługi Hadoop korzysta z jednego klastra, który obsługuje wiele celów. Usługa Azure HDInsight umożliwia tworzenie klastrów specyficznych dla obciążenia. Tworzenie klastrów dla określonych obciążeń eliminuje złożoność utrzymywania pojedynczego klastra o rosnącej złożoności.

- **Produktywność** — możesz używać różnych narzędzi dla usługi Hadoop i platformy Spark w preferowanym środowisku programistycznym.

- **Rozszerzalność z narzędziami niestandardowymi lub aplikacjami innych** firm — klastry usługi HDInsight można rozszerzać z zainstalowanymi składnikami i można je również zintegrować z innymi rozwiązaniami do obsługi danych Big Data, korzystając z wdrożeń [jednego kliknięcia](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) z miejsca na rynku platformy Azure.

- **Łatwe zarządzanie, administrowanie i monitorowanie** — usługa Azure HDInsight integruje się z [dziennikami Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) , aby zapewnić pojedynczy interfejs, za pomocą którego można monitorować wszystkie klastry.

- **Integracja z innymi usługami Azure** — HDInsight można łatwo zintegrować z innymi popularnymi usługami platformy Azure, takimi jak następujące:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Usługa Azure Data Lake Storage 2. generacji
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Procesy samonaprawiania i składniki** — Usługa HDInsight stale sprawdza składniki infrastruktury i składników Open Source przy użyciu własnej infrastruktury monitorowania. Powoduje również automatyczne odzyskanie krytycznych błędów, takich jak niedostępność składników i węzłów typu open source. Alerty są wyzwalane w Ambari, jeśli którykolwiek składnik OSS nie zakończył się niepowodzeniem.

Aby uzyskać więcej informacji, zobacz artykuł [co to jest usługa Azure HDInsight i stos technologii Apache Hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Proces planowania migracji

W celu zaplanowania migracji lokalnych klastrów Hadoop do usługi Azure HDInsight zaleca się wykonanie następujących kroków:

1. Zapoznaj się z bieżącymi lokalnymi wdrożeniami i topologiami.
2. Zapoznaj się z bieżącym zakresem projektu, osiami czasu i wiedzą zespołu.
3. Zapoznaj się z wymaganiami dotyczącymi platformy Azure.
4. Utwórz szczegółowy plan oparty na najlepszych rozwiązaniach.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Zbieranie szczegółów do przygotowania do migracji

Ta sekcja zawiera kwestionariusze szablonów ułatwiające zebranie ważnych informacji dotyczących:

- Wdrożenie lokalne
- Szczegóły projektu
- Wymagania systemu Azure

### <a name="on-premises-deployment-questionnaire"></a>Kwestionariusz wdrożenia lokalnego

| **Pytanie** | **Przykład** | **Odpowiedź** |
|---|---|---|
|**Temat**: **środowisko**|||
|Wersja dystrybucji klastra|HDP 2.6.5, PROGRAMU CDH 5,7|
|Duże ilości danych — składniki systemu|HDFS, przędza, Hive, LLAP, Impala, kudu, HBase, Spark, MapReduce, Kafka, dozorcy, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Typy klastrów|Hadoop, Spark, Kafka, burza, Solr|
|Liczba klastrów|4|
|Liczba węzłów głównych|2|
|Liczba węzłów procesu roboczego|100|
|Liczba węzłów krawędzi| 5|
|Całkowite miejsce na dysku|100 TB|
|Konfiguracja węzła głównego|m/y, procesor, dysk itd.|
|Konfiguracja węzłów danych|m/y, procesor, dysk itd.|
|Konfiguracja węzłów brzegowych|m/y, procesor, dysk itd.|
|Szyfrowanie HDFS?|Tak|
|Wysoka dostępność|HDFS HA, magazyn metadanych o wysokiej dostępności|
|Odzyskiwanie po awarii/kopia zapasowa|Utworzyć kopię zapasową klastra?|  
|Systemy, które są zależne od klastra|SQL Server, Teradata, Power BI, MongoDB|
|Integracje innych firm|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Temat**: **zabezpieczenia**|||
|Zabezpieczenia obwodu|Zapory|
|Uwierzytelnianie klastra & autoryzacja|Active Directory, Ambari, Cloudera Manager, brak uwierzytelniania|
|Access Control HDFS|  Użytkownicy z ręcznym protokołem SSH|
|Uwierzytelnianie w usłudze Hive & autoryzacja|Sentry, LDAP, AD z Kerberos, Ranger|
|Inspekcja|Ambari, Nawigator Cloudera, Ranger|
|Monitorowanie|Grafit, zebrany, z telegraf, InfluxDB|
|Generowanie alertów|Kapacitor, Prometheus, usługi Datadog|
|Czas przechowywania danych| 3 lata, 5 lat|
|Administratorzy klastrów|Pojedynczy administrator, wielu administratorów|

### <a name="project-details-questionnaire"></a>Kwestionariusz szczegółów projektu

|**Pytanie**|**Przykład**|**Odpowiedź**|
|---|---|---|
|**Temat**: **obciążenia i częstotliwość**|||
|Zadania MapReduce|10 zadań — dwa razy dziennie||
|Zadania Hive|100 zadań — co godzinę||
|Zadania wsadowe Spark|50 zadań — co 15 minut||
|Zadania przesyłania strumieniowego Spark|5 zadań — co 3 minuty||
|Zadania przesyłania strumieniowego ze strukturą|5 zadań — co minutę||
|Zadania szkoleniowe dotyczące modelu ML|2 zadania — raz w tygodniu||
|Języki programowania|Python, Scala, Java||
|Obsługa skryptów|Shell, Python||
|**Temat**: **dane**|||
|Źródła danych|Pliki płaskie, JSON, Kafka, RDBMS||
|Aranżacja danych|Przepływy pracy Oozie, przepływ powietrza||
|Wyszukiwania w pamięci|Apache zapłon, Redis||
|Miejsca docelowe danych|HDFS, RDBMS, Kafka, MPP ||
|**Temat**: **meta data**|||
|Typ bazy danych Hive|MySQL, Postgres||
|Liczba magazynów metadanych Hive|2||
|Liczba tabel programu Hive|100||
|Liczba zasad Ranger|20||
|Liczba przepływów pracy Oozie|100||
|**Temat**: **skalowanie**|||
|Ilość danych, w tym replikacja|100 TB||
|Dzienny wolumin pozyskiwania|50 GB||
|Szybkość wzrostu danych|10% na rok||
|Tempo wzrostu węzłów klastra|5% na rok
|**Temat**: **wykorzystanie klastra**|||
|Średnie użycie procesora CPU (%)|60%||
|Wykorzystano średnią pamięć (%)|75%||
|Używane miejsce na dysku|75%||
|Użyto średniej sieci%|25%
|**Temat**: **personel**|||
|Liczba administratorów|2||
|Liczba deweloperów|10||
|Liczba użytkowników końcowych|100||
|Umiejętności|Hadoop, Spark||
|Liczba dostępnych zasobów na potrzeby prac związanych z migracją|2||
|**Temat**: **ograniczenia**|||
|Bieżące ograniczenia|Opóźnienie jest wysokie||
|Bieżące wyzwania|Problem współbieżności||

### <a name="azure-requirements-questionnaire"></a>Kwestionariusz wymagań platformy Azure

|**Pytanie**|**Przykład**|**Odpowiedź**|
|---|---|---|
|**Temat**: **infrastruktura** |||
| Preferowany region|Wschodnie stany USA||
|Preferowana Sieć wirtualna?|Tak||
|Wymagana HA/DR?|Tak||
|Integracja z innymi usługami w chmurze?|ADF, CosmosDB||
|**Temat**:   **przenoszenie danych**  |||
|Początkowe preferencje ładowania|Pomocą distcp, pole danych, ADF, WANDisco||
|Różnica transferu danych|Pomocą distcp, AzCopy||
|Ciągły przyrostowy transfer danych|Pomocą distcp, Sqoop||
|**Temat**:   **monitorowanie alertów &** |||
|Korzystanie z usługi Azure Monitoring & alertów vs Integruj monitorowanie innych firm|Korzystanie z alertów usługi Azure Monitoring &||
|**Temat**:   **Preferencje zabezpieczeń** |||
|Prywatny i chroniony Potok danych?|Tak||
|Klaster przyłączony do domeny (ESP)?|     Tak||
|AD Sync lokalnych do chmury?|     Tak||
|Liczba użytkowników usługi AD do synchronizacji?|          100||
|Czy chcesz zsynchronizować hasła z chmurą?|    Tak||
|Tylko użytkownicy w chmurze?|                 Tak||
|Potrzebujesz usługi MFA?|                       Nie|| 
|Wymagania dotyczące autoryzacji danych?|  Tak||
|Kontrola dostępu oparta na rolach?|        Tak||
|Inspekcja jest wymagana?|                  Tak||
|Szyfrowanie danych w spoczynku?|          Tak||
|Szyfrowanie danych jest w trakcie przesyłania?|       Tak||
|**Temat**:   **Preferencje architektury** |||
|Pojedynczy klaster a typy klastrów określonych|Określone typy klastrów||
|Rozłożone magazyny a Magazyn zdalny?|Magazyn zdalny||
|Mniejszy rozmiar klastra jako dane są przechowywane zdalnie?|Mniejszy rozmiar klastra||
|Używać wielu mniejszych klastrów zamiast pojedynczego dużego klastra?|Korzystanie z wielu mniejszych klastrów||
|Używasz zdalnego magazynu metadanych?|Tak||
|Udostępnić magazyny metadanych między różnymi klastrami?|Tak||
|Czy dekonstrukcja obciążeń?|Zastępowanie zadań programu Hive za pomocą zadań platformy Spark||
|Korzystasz z usługi ADF na potrzeby aranżacji danych?|Nie||

## <a name="next-steps"></a>Następne kroki

Przeczytaj następny artykuł z tej serii:

- [Najlepsze rozwiązania dotyczące architektury dla migracji lokalnej na Azure HDInsight Hadoop migrację](apache-hadoop-on-premises-migration-best-practices-architecture.md)
