---
title: Architektury ciągłości biznesowej usługi Azure HDInsight
description: W tym artykule omówiono różne możliwe architektury zapewniania ciągłości biznesowej dla usługi HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Wysoka dostępność usługi Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: cb3ef1e802546d5a8b1574b304770fe7a364e2df
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91844082"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Architektury ciągłości biznesowej usługi Azure HDInsight

W tym artykule przedstawiono kilka przykładów architektury ciągłości biznesowej, które można wziąć pod uwagę w usłudze Azure HDInsight. Tolerancja w przypadku ograniczonej funkcjonalności w trakcie awarii to decyzja biznesowa, która różni się od jednej aplikacji do następnej. Niektóre aplikacje mogą być niedostępne lub być częściowo dostępne z ograniczoną funkcjonalnością lub opóźnionym przetwarzaniem przez okres. W przypadku innych aplikacji można nie zaakceptować żadnych ograniczonych funkcji.

> [!NOTE]
> Architektury przedstawione w tym artykule nie są wyczerpujące. Należy zaprojektować własne unikalne architektury po dokonaniu obiektywnych ustaleń wokół oczekiwanej ciągłości biznesowej, złożoności operacyjnej i kosztu posiadania.

## <a name="apache-hive-and-interactive-query"></a>Zapytanie Apache Hive i interakcyjne

[Replikacja programu Hive v2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) jest zalecana w przypadku ciągłości działania w ramach klastrów usługi HDInsight Hive i interaktywnych zapytań. Trwałe sekcje autonomicznego klastra Hive, które muszą być replikowane, to warstwa magazynu i magazyn metadanych Hive. Klastry Hive w scenariuszu obejmującym wiele użytkowników pakiet Enterprise Security potrzebują Azure Active Directory Domain Services i magazynu metadanych Ranger.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Architektura zapytań Hive i Interactive":::

Replikacja oparta na zdarzeniach Hive jest konfigurowana między klastrem podstawowym i pomocniczym. Obejmuje to dwie odrębne fazy, uruchamianie i przyrostowe uruchomienia:

* Ponowne uruchomienie replikuje cały magazyn programu Hive, w tym informacje o magazyn metadanych Hive z podstawowego do pomocniczego.

* Przyrostowe uruchomienia są zautomatyzowane w klastrze podstawowym, a zdarzenia wygenerowane podczas przyrostowych uruchomień są odtwarzane w klastrze pomocniczym. Klaster pomocniczy przechwytuje do zdarzeń wygenerowanych z klastra podstawowego, upewniając się, że klaster pomocniczy jest spójny ze zdarzeniami podstawowego klastra po uruchomieniu replikacji.

Klaster pomocniczy jest potrzebny tylko w czasie replikacji do uruchamiania kopii rozproszonej, `DistCp` ale magazyn i magazyn metadanych muszą być trwałe. Po pomyślnej replikacji można uruchomić skrypt pomocniczy z obsługą skryptów na żądanie przed rozpoczęciem replikacji.

Klaster pomocniczy jest zwykle tylko do odczytu. Można utworzyć dodatkowy klaster do odczytu i zapisu, ale zwiększa to złożoność, która obejmuje Replikowanie zmian z dodatkowego klastra do klastra podstawowego.

### <a name="hive-event-based-replication-rpo--rto"></a>Cel punktu odzyskiwania replikacji oparty na zdarzeniach Hive & RTO

* Cel punktu odzyskiwania: utrata danych jest ograniczona do ostatniego pomyślnego zdarzenia replikacji przyrostowej z podstawowego do pomocniczego.

* RTO: czas między awarią i wznowieniem transakcji nadrzędnych i podrzędnych z pomocniczym.

### <a name="apache-hive-and-interactive-query-architectures"></a>Architektury zapytań Apache Hive i interaktywnych

#### <a name="hive-active-primary-with-on-demand-secondary"></a>Usługa Hive Active podstawowa z serwerem pomocniczym na żądanie

W *aktywnym serwerze podstawowym z architekturą pomocniczą na żądanie* aplikacje są zapisywane w aktywnym regionie podstawowym, podczas gdy nie są obsługiwane żadne klastry w regionie pomocniczym podczas normalnych operacji. Magazyn metadanych SQL i magazyn w regionie pomocniczym są trwałe, podczas gdy klaster usługi HDInsight jest skryptowy i wdrażany na żądanie dopiero przed uruchomieniem zaplanowanej replikacji programu Hive.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="Architektura zapytań Hive i Interactive":::

#### <a name="hive-active-primary-with-standby-secondary"></a>Aktywna gałąź podstawowa z dodatkowym zapasem pomocniczym

W *aktywnym elemencie podstawowym z rezerwą pomocniczą*, aplikacje zapisują się w aktywnym regionie podstawowym, podczas gdy awaryjny klaster skalowalny w poziomie w trybie tylko do odczytu jest uruchamiany podczas normalnego działania. Podczas normalnych operacji można odciążyć operacje odczytu specyficzne dla regionu do pomocniczego.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="Architektura zapytań Hive i Interactive":::

## <a name="apache-spark"></a>Apache Spark

Obciążenia Spark mogą lub nie obejmują składnika Hive. Aby włączyć obciążenia Spark SQL w celu odczytu i zapisu danych z programu Hive, klastry usługi HDInsight Spark współużytkują niestandardowe magazyny metadanych z klastrów Hive/interaktywnych zapytań w tym samym regionie. W takich scenariuszach replikacja między regionami obciążeń Spark musi być również dołączona do replikacji metadanych i magazynów Hive. Scenariusze pracy awaryjnej w tej sekcji dotyczą obu:

* [Program Spark SQL w tabelach kwasów korzystających z konfiguracji łącznika magazynu Hive (obsługiwane)](./interactive-query/apache-hive-warehouse-connector.md) przy użyciu klastra interakcyjnych zapytań usługi HDInsight.
* Obciążenie Spark SQL na tabelach nienależących do kwasu przy użyciu klastra Hadoop usługi HDInsight.

W scenariuszach, w których platforma Spark działa w trybie autonomicznym, dane nadzorowane i przechowywane Jars platformy Spark (dla zadań usługi Livy) muszą być replikowane z regionu podstawowego do regionu pomocniczego regularnie przy użyciu Azure Data Factory `DistCP` .

Zalecamy używanie systemów kontroli wersji do przechowywania notesów i bibliotek platformy Spark, gdzie można je łatwo wdrożyć w klastrze podstawowym lub pomocniczym. Upewnij się, że rozwiązania na podstawie notesu i inne niż Notes są przygotowane do załadowania odpowiednich instalacji danych w podstawowym lub pomocniczym obszarze roboczym.

Jeśli istnieją biblioteki specyficzne dla klienta, w których Usługa HDInsight jest dostępna w sposób natywny, muszą one być śledzone i okresowo ładowane do rezerwowego klastra pomocniczego.  

### <a name="apache-spark-replication-rpo--rto"></a>Apache Spark punktu odzyskiwania replikacji & RTO

* Cel punktu odzyskiwania: utrata danych jest ograniczona do ostatniej pomyślnej replikacji przyrostowej (Spark i Hive) z podstawowego do pomocniczego.

* RTO: czas między awarią i wznowieniem transakcji nadrzędnych i podrzędnych z pomocniczym.

### <a name="apache-spark-architectures"></a>Architektury Apache Spark

#### <a name="spark-active-primary-with-on-demand-secondary"></a>Platforma Spark Active podstawowa z serwerem pomocniczym na żądanie

Aplikacje odczytują i zapisują do klastrów Spark i Hive w regionie podstawowym, podczas gdy nie są obsługiwane żadne klastry w regionie pomocniczym podczas normalnych operacji. Magazyn metadanych SQL, magazyn Hive i magazyn Spark są trwałe w regionie pomocniczym. Klastry Spark i Hive są skryptami i wdrażane na żądanie. Replikacja programu Hive jest używana do replikowania magazynu Hive i magazynu metadanych Hive, podczas gdy Azure Data Factory `DistCP` może służyć do kopiowania autonomicznego magazynu Spark. Klastry Hive muszą zostać wdrożone przed uruchomieniem każdej replikacji programu Hive z powodu `DistCp` obliczeń zależności.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="Architektura zapytań Hive i Interactive":::

#### <a name="spark-active-primary-with-standby-secondary"></a>Platforma Spark Active podstawowa z dodatkowym zapasem pomocniczym

Aplikacje odczytują i zapisują w klastrach platformy Spark i Hive w regionie podstawowym, podczas gdy tryb wstrzymania skalowany w poziomie i klastry Spark w trybie tylko do odczytu są uruchamiane w regionie pomocniczym podczas normalnych operacji. Podczas normalnych operacji można odciążyć określony region Hive i operacje odczytu platformy Spark do pomocniczego.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="Architektura zapytań Hive i Interactive":::

## <a name="apache-hbase"></a>Apache HBase

HBase eksportu i HBase są typowymi sposobami zapewniania ciągłości biznesowej między klastrami usługi HDInsight HBase.

HBase Export to proces replikacji wsadowej, który używa narzędzia eksportu HBase do eksportowania tabel z podstawowego klastra HBase do jego podstawowego Azure Data Lake Storage magazynu generacji 2. Dostęp do wyeksportowanych danych będzie możliwy z pomocniczego klastra HBase i zaimportowany do tabel, które muszą znajdować się w pomocniczym. Chociaż eksport HBase ma stopień szczegółowości poziomu tabeli oferty, w sytuacjach aktualizacji przyrostowych aparat automatyzacji eksportu kontroluje zakres wierszy przyrostowych do uwzględnienia w każdym uruchomieniu. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i replikacja w usłudze HDInsight HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#export-then-import).

Replikacja HBase używa replikacji niemal w czasie rzeczywistym między klastrami HBase w pełni zautomatyzowany sposób. Replikacja odbywa się na poziomie tabeli. Wszystkie tabele lub konkretne tabele mogą być przeznaczone do replikacji. Replikacja HBase jest ostatecznie spójna, co oznacza, że ostatnie modyfikacje tabeli w regionie podstawowym mogą być od razu niedostępne dla wszystkich serwerów pomocniczych. Serwery pomocnicze mogą ostatecznie stać się spójne z podstawowym. Replikację HBase można skonfigurować między co najmniej dwa klastry usługi HDInsight HBase, jeśli:

* Podstawowa i pomocnicza znajdują się w tej samej sieci wirtualnej.
* Podstawowe i pomocnicze znajdują się w różnych sieci wirtualnych równorzędnych w tym samym regionie.
* Podstawowe i pomocnicze znajdują się w różnych sieci wirtualnychach równorzędnych w różnych regionach.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji klastra Apache HBase w usłudze Azure Virtual Networks](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-replication).

Istnieje kilka innych sposobów wykonywania kopii zapasowych klastrów HBase, takich jak [Kopiowanie folderu HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-the-hbase-folder), [Kopiowanie tabel](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-tables) i [migawek](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#snapshots).

### <a name="hbase-rpo--rto"></a>HBase RPO & RTO

#### <a name="hbase-export"></a>Eksport HBase

* Cel punktu odzyskiwania: utrata danych jest ograniczona do ostatniego pomyślnego importowania przyrostowego partii przez pomocniczą z poziomu podstawowego.
* RTO: czas między awarią podstawową i wznowieniem operacji we/wy na serwerze pomocniczym.

#### <a name="hbase-replication"></a>Replikacja HBase

* Cel punktu odzyskiwania: utrata danych jest ograniczona do ostatniego wydania WalEdit otrzymanego w dodatkowej.
* RTO: czas między awarią podstawową i wznowieniem operacji we/wy na serwerze pomocniczym.

### <a name="hbase-architectures"></a>Architektury HBase

Replikację HBase można skonfigurować w trzech trybach: lidera, Leader-Leader i cyklicznie.

#### <a name="hbase-replication--leader--follower-model"></a>Replikacja HBase: lider — model

W tej konfiguracji międzyregionowej replikacja jest jednokierunkowe z regionu podstawowego do regionu pomocniczego. Dla jednokierunkowej replikacji można zidentyfikować wszystkie tabele lub konkretne tabele na serwerze podstawowym. Podczas normalnych operacji klaster pomocniczy może służyć do obsługi żądań odczytu w jego własnym regionie.

Klaster pomocniczy działa jako normalny klaster HBase, który może obsługiwać własne tabele i może obsługiwać operacje odczytu i zapisu z aplikacji regionalnych. Jednak zapisy w replikowanych tabelach lub tabelach natywnych do pomocniczych nie są replikowane z powrotem do podstawowego.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="Architektura zapytań Hive i Interactive":::

#### <a name="hbase-replication--leader--leader-model"></a>Replikacja HBase: lider — model lidera

Ta konfiguracja międzyregionowa jest bardzo podobna do konfiguracji jednokierunkowej, z wyjątkiem tego, że replikacja odbywa się dwukierunkowo między regionem podstawowym i pomocniczym. Aplikacje mogą używać obu klastrów w trybach odczytu i zapisu, a aktualizacje są wymieniane asynchronicznie między nimi.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="Architektura zapytań Hive i Interactive":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>Replikacja HBase: wiele regionów lub cyklicznie

Model replikacji wieloregionowej/cyklicznej to rozszerzenie replikacji HBase i może służyć do tworzenia globalnie nadmiarowej architektury HBase z wieloma aplikacjami, które odczytują i zapisują do określonych regionów klastrów HBase. Klastry można skonfigurować w różnych kombinacjach lidera/lidera lub lidera/osoby wykonujące w zależności od wymagań firmy.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="Architektura zapytań Hive i Interactive":::

## <a name="apache-kafka"></a>Apache Kafka

Aby włączyć obsługę wielu regionów Usługa HDInsight 4,0 obsługuje Kafka narzędzia MirrorMaker, której można użyć do obsługi repliki pomocniczej podstawowego klastra Kafka w innym regionie. Narzędzia MirrorMaker działa jako para klientów na wysokim poziomie, zużywa z określonego tematu w klastrze podstawowym i tworzy do tematu o tej samej nazwie w pomocniczym. Replikacja między klastrami w celu odzyskiwania po awarii o wysokiej dostępności przy użyciu usługi narzędzia MirrorMaker obejmuje założenie, że producenci i konsumenci muszą przejść do trybu failover w klastrze repliki. Aby uzyskać więcej informacji, zobacz temat [Używanie narzędzia MirrorMaker do replikowania tematów Apache Kafka z Kafka w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-mirroring)

W zależności od okresu istnienia replikacji, replikacja tematu narzędzia MirrorMaker może prowadzić do różnych przesunięć między tematami źródłowymi i replikami. Klastry usługi HDInsight Kafka obsługują również replikację partycji tematu, która jest funkcją wysokiej dostępności na poziomie poszczególnych klastrów.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Architektura zapytań Hive i Interactive":::

### <a name="apache-kafka-architectures"></a>Architektury Apache Kafka

#### <a name="kafka-replication-active--passive"></a>Replikacja Kafka: aktywny — pasywny

Konfiguracja Active-Passive umożliwia asynchroniczne jednokierunkowe dublowanie od aktywnego do pasywnego. Producenci i konsumenci muszą mieć świadomość istnienia aktywnego i pasywnego klastra i muszą być gotowi do pracy awaryjnej w trybie failover w przypadku, gdy aktywne zakończy się niepowodzeniem. Poniżej przedstawiono niektóre zalety i wady konfiguracji Active-Passive.

Zalety:

* Opóźnienie sieci między klastrami nie wpływa na wydajność aktywnego klastra.
* Prostota jednokierunkowej replikacji.

Wady:

* Klaster pasywny może pozostać nieużywany.
* Złożoność projektu obejmująca świadomość pracy awaryjnej w przypadku producentów i klientów aplikacji.
* Możliwa utrata danych w przypadku niepowodzenia aktywnego klastra.
* Ostateczna spójność między tematami między klastrami aktywnym i pasywnym.
* Failbacks z podstawową może prowadzić do niespójności komunikatów w tematach.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Architektura zapytań Hive i Interactive":::

#### <a name="kafka-replication-active--active"></a>Replikacja Kafka: aktywny — aktywny

Active-Active konfiguracja obejmuje dwa regionalne rozdzielone klastry usługi HDInsight Kafka, z dwukierunkową replikacją asynchroniczną z narzędzia MirrorMaker. W tym projekcie komunikaty używane przez odbiorców w podstawowym są również udostępniane klientom w trybie dodatkowym i na odwrót. Poniżej przedstawiono niektóre zalety i wady konfiguracji Active-Active.

Zalety:

* Ze względu na stan zduplikowany, tryb failover i failbacks są łatwiejsze do wykonania.

Wady:

* Konfiguracja, zarządzanie i monitorowanie jest bardziej skomplikowane niż aktywne-pasywne.
* Należy rozwiązać problem związany z replikacją cykliczną.  
* Replikacja dwukierunkowa prowadzi do wyższych, regionalnych kosztów ruchu wychodzącego.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Architektura zapytań Hive i Interactive":::

## <a name="hdinsight-enterprise-security-package"></a>pakiet Enterprise Security usługi HDInsight

Ta konfiguracja służy do włączania funkcji dla wszystkich użytkowników zarówno podstawowych, jak i pomocniczych, jak również dla [zestawów replik AD DS platformy Azure](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-replica-set) , aby zapewnić użytkownikom możliwość uwierzytelniania w obu klastrach. W trakcie normalnych operacji zasady Ranger należy skonfigurować w dodatkowej, aby upewnić się, że użytkownicy mają ograniczony dostęp do operacji odczytu. W poniższej architekturze wyjaśniono, jak może wyglądać aktywna podstawowa gałąź ESP — konfiguracja pomocnicza.

Replikacja magazynu metadanych Ranger:

Magazyn metadanych Ranger jest używany do trwałego przechowywania i obsługiwania zasad Ranger w celu kontrolowania autoryzacji danych. Zalecamy zachowanie niezależnych zasad Rangerymi w podstawowych i pomocniczych i konserwowanie pomocniczej jako repliki do odczytu.
  
Jeśli wymaganie ma zachować synchronizację zasad Rangerymi między podstawową i pomocniczą, należy użyć [Ranger Import/Export](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export#:~:text=Ranger%20has%20introduced%20a%20new,can%20import%20and%20export%20policies.&text=Also%20can%20export%2Fimport%20a,repositories\)%20via%20Ranger%20Admin%20UI) , aby okresowo tworzyć kopie zapasowe i importować zasady Ranger z podstawowego do pomocniczego.

Replikowanie zasad Ranger między podstawową i pomocniczą może spowodować, że pomocnicza będzie mogła mieć włączoną opcję zapisu, co może prowadzić do przypadkowego zapisu na pomocniczym serwerze, który prowadzi do niespójności danych.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="Architektura zapytań Hive i Interactive":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat elementów omówionych w tym artykule, zobacz:

* [Dokumentacja REST Ambari Apache](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* [Zainstaluj i skonfiguruj moduł Azure PowerShell AZ](/powershell/azure/)
* [Zarządzanie usługą HDInsight przy użyciu usługi Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Inicjowanie obsługi klastrów usługi HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)