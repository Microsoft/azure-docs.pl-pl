---
title: Składniki wysokiej dostępności w usłudze Azure HDInsight
description: Omówienie różnych składników wysokiej dostępności używanych przez klastry usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: ac63846e2679e9b4a51cb26b32415eb81a4b76ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842584"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Usługi wysokiej dostępności obsługiwane przez usługę Azure HDInsight

W celu zapewnienia optymalnego poziomu dostępności dla składników analitycznych Usługa HDInsight została opracowana przy użyciu unikatowej architektury zapewniającej wysoką dostępność (HA) krytycznych usług. Niektóre składniki tej architektury zostały opracowane przez firmę Microsoft w celu zapewnienia automatycznego przejścia w tryb failover. Inne składniki to standardowe składniki Apache wdrożone w celu obsługi określonych usług. W tym artykule wyjaśniono architekturę modelu usług HA w usłudze HDInsight, jak Usługa HDInsight obsługuje tryb failover dla usług HA oraz najlepsze rozwiązania w zakresie odzyskiwania z innych przerw w działaniu usługi.
 
> [!NOTE]
> Komunikacja bezpłatna bez opłat
>
> Firma Microsoft obsługuje różnorodne i dołączane środowiska. Ten artykuł zawiera odwołania do programu Word _podrzędny_. Przewodnik po [stylu firmy Microsoft dla komunikacji bezpłatnej](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) jest rozpoznawany jako wykluczony wyraz. Słowo jest używane w tym artykule w celu zapewnienia spójności, ponieważ jest to obecnie słowo, które jest wyświetlane w oprogramowaniu. W przypadku zaktualizowania oprogramowania w celu usunięcia wyrazu ten artykuł zostanie zaktualizowany w celu wyrównania.
>

## <a name="high-availability-infrastructure"></a>Infrastruktura wysokiej dostępności

Usługa HDInsight zapewnia dostosowaną infrastrukturę w celu zapewnienia wysokiej dostępności czterech podstawowych usług dzięki automatycznym funkcjom pracy awaryjnej:

- Serwer Apache Ambari
- Serwer Oś czasu aplikacji dla PRZĘDZy Apache
- Serwer historii zadań dla usługi Hadoop MapReduce
- Apache Livy

Ta infrastruktura składa się z wielu usług i składników oprogramowania, z których niektóre zostały zaprojektowane przez firmę Microsoft. Następujące składniki są unikatowe dla platformy usługi HDInsight:

- Podrzędny kontroler trybu failover
- Główny kontroler trybu failover
- Usługa podrzędna wysokiej dostępności
- Główna usługa wysokiej dostępności

![Infrastruktura wysokiej dostępności](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Istnieją również inne usługi wysokiej dostępności, które są obsługiwane przez składniki Apache niezawodności Open Source. Te składniki są również obecne w klastrach usługi HDInsight:

- System plików Hadoop (HDFS) NameNode
- Datasourcemanager
- HBase Master

Poniższe sekcje zawierają szczegółowe informacje o tym, jak te usługi współpracują ze sobą.

## <a name="hdinsight-high-availability-services"></a>Usługi HDInsight wysokiej dostępności

Firma Microsoft zapewnia pomoc techniczną dla czterech usług Apache w poniższej tabeli w klastrach usługi HDInsight. Aby odróżnić je od usług wysokiej dostępności obsługiwanych przez składniki z platformy Apache, są one nazywane *usługami HDINSIGHT ha*.

| Usługa | Węzły klastra | Typy klastrów | Przeznaczenie |
|---|---|---|---|
| Serwer Apache Ambari| Aktywne węzła głównego | Wszystkie | Monitoruje klaster i zarządza nim.|
| Serwer Oś czasu aplikacji dla PRZĘDZy Apache | Aktywne węzła głównego | Wszystkie z wyjątkiem Kafka | Utrzymuje informacje debugowania dotyczące zadań PRZĘDZy uruchomionych w klastrze.|
| Serwer historii zadań dla usługi Hadoop MapReduce | Aktywne węzła głównego | Wszystkie z wyjątkiem Kafka | Utrzymuje dane debugowania dla zadań MapReduce.|
| Apache Livy | Aktywne węzła głównego | platforma Spark | Umożliwia łatwą interakcję z klastrem Spark za pośrednictwem interfejsu REST |

>[!Note]
> Klastry usługi HDInsight pakiet Enterprise Security (ESP) obecnie zapewniają wysoką dostępność serwera Ambari.

### <a name="architecture"></a>Architektura

Każdy klaster usługi HDInsight ma odpowiednio dwa węzłów głównych w trybie aktywnym i w stanie gotowości. Usługi HDInsight HA działają tylko na węzłów głównych. Te usługi powinny być zawsze uruchamiane w ramach aktywnego węzła głównego i zatrzymane i włączone w trybie konserwacji w węzła głównego w stanie wstrzymania.

Aby zachować poprawne Stany usług HA i zapewnić szybką pracę w trybie failover, Usługa HDInsight wykorzystuje Apache ZooKeeper, która jest usługą koordynacyjną dla aplikacji rozproszonych, do przeprowadzania aktywnych węzła głównego wyborów. Usługa HDInsight udostępnia również kilka procesów Java w tle, które koordynują procedurę przełączania do trybu failover usług HDInsight HA. Te usługi są następujące: główny kontroler trybu failover, podrzędny kontroler trybu failover, *Master-ha-Service*i *slave-ha-Service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper to usługa koordynacji o wysokiej wydajności dla aplikacji rozproszonych. W środowisku produkcyjnym dozorcy zazwyczaj działa w trybie replikowanym, w którym replikowana Grupa serwerów dozorcy tworzy kworum. Każdy klaster usługi HDInsight ma trzy węzły dozorcy, które umożliwiają trzy serwery dozorcy do tworzenia kworum. Usługa HDInsight ma dwa kworum dozorcy uruchomione równolegle ze sobą. Jeden kworum decyduje o aktywnym węzła głównego w klastrze, w którym powinny być uruchamiane usługi HDInsight HA. Inny kworum służy do koordynowania usług HA dostarczonych przez Apache, jak opisano w dalszych sekcjach.

### <a name="slave-failover-controller"></a>Podrzędny kontroler trybu failover

Podrzędny kontroler trybu failover działa na każdym węźle klastra usługi HDInsight. Ten kontroler jest odpowiedzialny za uruchamianie agenta Ambari oraz *usługi slave-ha-Service* w każdym węźle. Okresowo wysyła zapytanie do pierwszego kworum dozorcy o aktywnej węzła głównego. Po zmianie węzłów głównych aktywnego i wstrzymania kontroler trybu failover podrzędny wykonuje następujące czynności:

1. Aktualizuje plik konfiguracji hosta.
1. Ponownie uruchamia agenta Ambari.

*Usługa slave-ha* jest odpowiedzialna za zatrzymywanie usług HDInsight ha (z wyjątkiem serwera Ambari) w węzła głównego w stanie wstrzymania.

### <a name="master-failover-controller"></a>Główny kontroler trybu failover

Główny kontroler trybu failover działa na obu węzłów głównychach. Oba kontrolery główne trybu failover komunikują się z pierwszym kworum dozorcy, aby wyznaczyć węzła głównego, na którym są uruchamiane jako aktywne węzła głównego.

Na przykład jeśli główny kontroler trybu failover na węzła głównego 0 WINS wybierze następujące zmiany:

1. Węzła głównego 0 zostanie uaktywniona.
1. Główny kontroler trybu failover uruchamia serwer Ambari oraz *wzorzec-ha-Service* w węzła głównego 0.
1. Inny główny kontroler trybu failover zatrzyma serwer Ambari oraz *wzorzec-ha-Service* w węzła głównego 1.

Master-ha-Service jest uruchamiany tylko na aktywnych węzła głównegoach, a usługi HDInsight HA (z wyjątkiem serwera Ambari) w węzła głównego w stanie wstrzymania i uruchamiają je na aktywnych węzła głównego.

### <a name="the-failover-process"></a>Proces trybu failover

![proces trybu failover](./media/hdinsight-high-availability-components/failover-steps.png)

Monitor kondycji jest uruchamiany na każdym węzła głównego wraz z głównym kontrolerem trybu failover w celu wysyłania powiadomień pulsu do dozorcy kworum. Węzła głównego jest uznawana za usługę HA w tym scenariuszu. Monitor kondycji sprawdza, czy każda usługa wysokiej dostępności jest w dobrej kondycji i czy jest gotowa do przyłączenia do wyboru lidera. Jeśli tak, ten węzła głównego będzie konkurować w wyborze. Jeśli nie, zostanie zamknięty wybór, dopóki nie zostanie ponownie przygotowana.

Jeśli stan gotowości węzła głównego kiedykolwiek osiągnie lidera i stanie się aktywny (na przykład w przypadku awarii w poprzednim aktywnym węźle), jego główny kontroler trybu failover rozpocznie uruchamianie wszystkich usług HDInsight HA na tym komputerze. Główny kontroler trybu failover również zatrzymuje te usługi na innych węzła głównego.

W przypadku błędów usługi HDInsight HA, takich jak usługa jest wyłączona lub zła kondycja, główny kontroler trybu failover powinien automatycznie ponownie uruchomić lub zatrzymać usługi zgodnie ze stanem węzła głównego. Użytkownicy nie powinni ręcznie uruchamiać usług HDInsight HA na obu węzłach głównych. Zamiast tego Zezwól na automatyczne lub ręczne przełączenie w tryb failover, aby ułatwić odzyskanie usługi.

### <a name="inadvertent-manual-intervention"></a>Przypadkowa interwencja ręczna

Usługi HDInsight HA powinny działać tylko na aktywnych węzła głównegoach i zostaną automatycznie uruchomione ponownie w razie potrzeby. Ponieważ pojedyncze usługi HA nie mają własnego monitora kondycji, nie można wyzwolić trybu failover na poziomie poszczególnych usług. Przełączenie w tryb failover jest zapewnione na poziomie węzła, a nie na poziomie usługi.

### <a name="some-known-issues"></a>Znane problemy

- Po ręcznym uruchomieniu usługi HA w węzła głównego w stanie wstrzymania nie zostanie ona zatrzymana do momentu następnego przejścia w tryb failover. Gdy usługi HA są uruchomione na obu węzłów głównychach, niektóre potencjalne problemy obejmują: Ambari interfejs użytkownika jest niedostępny, Ambari zgłasza błędy, PRZĘDZę, Spark i Oozie mogą zostać zablokowane.

- Gdy usługa HA w aktywnym węzła głównego zostaje zatrzymana, nie zostanie uruchomiona ponownie do czasu następnego przejścia w tryb failover lub główny kontroler trybu failover/główny — ha. Gdy co najmniej jedna z usług HA zostanie zatrzymana na aktywnym węzła głównego, zwłaszcza w przypadku zatrzymywania serwera Ambari, interfejs użytkownika Ambari jest niedostępny, inne potencjalne problemy obejmują niepowodzenia zadań PRZĘDZy, Spark i Oozie.

## <a name="apache-high-availability-services"></a>Usługi Apache High Availability

Usługa Apache zapewnia wysoką dostępność dla systemu HDFS NameNode, ResourceManager i HBase Master, które są również dostępne w klastrach usługi HDInsight. W przeciwieństwie do usług HDInsight HA, są one obsługiwane w klastrach ESP. Usługi Apache HA komunikują się z drugim kworum dozorcy (opisanym w powyższej sekcji) w celu wybrania Stanów aktywny/wstrzymanie i przeprowadzenie automatycznej pracy awaryjnej. W poniższych sekcjach szczegółowo opisano działanie tych usług.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Rozproszony system plików usługi Hadoop (HDFS) NameNode

Klastry usługi HDInsight oparte na Apache Hadoop 2,0 lub wyższej zapewniają wysoką dostępność NameNode. Istnieją dwa NameNodes uruchomione na węzłów głównych, które są skonfigurowane do automatycznego przełączania do trybu failover. NameNodes używają *ZKFailoverController* do komunikowania się z dozorcy, aby wybrać stan aktywny/gotowość. *ZKFailoverController* działa na obu węzłów głównychach i działa w taki sam sposób jak w przypadku głównego kontrolera trybu failover.

Drugi kworum dozorcy jest niezależny od pierwszego kworum, więc aktywne NameNode może nie działać w aktywnym węzła głównego. Gdy aktywny NameNode jest martwy lub w złej kondycji, NameNode usługi WINS i stanie się aktywny.

### <a name="yarn-resourcemanager"></a>Datasourcemanager

Klastry usługi HDInsight oparte na Apache Hadoop 2,4 lub wyższej obsługują wysoką dostępność dla ResourceManager. Istnieją dwa ResourceManagers, RM1 i RM2, które działają odpowiednio na węzła głównego 0 i węzła głównego 1. Podobnie jak w przypadku NameNode, jest również skonfigurowany do automatycznego przełączania do trybu failover. Inny datasourcemanager jest automatycznie wybierany jako aktywny, gdy bieżące aktywne datasourcemanager wyjdzie lub nie odpowiada.

Obiekt ResourceManager używa swojego osadzonego *ActiveStandbyElector* jako detektora błędów i wyborczyości lidera. W przeciwieństwie do NameNode HDFS, obiekt ResourceManager nie wymaga oddzielnego demona ZKFC. Aktywne datasourcemanager zapisuje swoje Stany w usłudze Apache dozorcy.

Wysoka dostępność elementu ResourceManager jest niezależna od NameNode i innych usług HDInsight HA. Aktywna usługa ResourceManager może nie działać w aktywnym węzła głównego lub węzła głównego, gdzie działa aktywna usługa NameNode. Aby uzyskać więcej informacji o wysokiej dostępności usługi PRZĘDZy, zobacz temat [ResourceManager wysoka dostępność](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HBase Master

Klastry usługi HDInsight HBase obsługują wysoką dostępność HBase Master. W przeciwieństwie do innych usług HA, które działają w węzłów głównych, HBase wzorców działają w trzech węzłach dozorcy, gdzie jeden z nich jest aktywnym wzorcem, a pozostałe dwa są w stanie gotowości. Podobnie jak NameNode, HBase Master współrzędnych z Apache dozorcy dla wyboru lidera i automatycznie przełączenia w tryb failover, gdy bieżący aktywny serwer główny ma problemy. W każdej chwili istnieje tylko jeden aktywny HBase Master.

## <a name="next-steps"></a>Następne kroki

- [Dostępność i niezawodność klastrów Apache Hadoop w usłudze HDInsight](hdinsight-high-availability-linux.md)
- [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)
