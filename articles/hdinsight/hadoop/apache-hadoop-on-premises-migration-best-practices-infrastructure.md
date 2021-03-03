---
title: 'Infrastruktura: Apache Hadoop lokalna do usługi Azure HDInsight'
description: Poznaj najlepsze rozwiązania dotyczące infrastruktury dotyczące migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 309053c2d7a0f9482016f1bd83e0c61dcd31bec5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740668"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania dotyczące infrastruktury

W tym artykule przedstawiono zalecenia dotyczące zarządzania infrastrukturą klastrów usługi Azure HDInsight. Jest ona częścią serii, która oferuje najlepsze rozwiązania ułatwiające Migrowanie lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Planowanie pojemności klastra usługi HDInsight

Kluczowe Opcje planowania pojemności klastra usługi HDInsight są następujące:

**Region**  
Region platformy Azure określa, gdzie klaster jest fizycznie zainicjowany. Aby zminimalizować opóźnienie operacji odczytu i zapisu, klaster powinien znajdować się w tym samym regionie co dane.

**Lokalizacja i rozmiar magazynu**  
Domyślny magazyn musi znajdować się w tym samym regionie co klaster. W przypadku klastra z 48 węzłami zaleca się używanie 4 do 8 kont magazynu. Mimo że może już być wystarczająca ilość miejsca w magazynie, każde konto magazynu oferuje dodatkową przepustowość sieci dla węzłów obliczeniowych. Jeśli istnieje wiele kont magazynu, użyj nazwy losowej dla każdego konta magazynu, bez prefiksu. Celem przypadkowego nazewnictwa jest zmniejszenie ryzyka związanego z wąskimi gardłami magazynowania (ograniczanie) lub awariami trybu wspólnego na wszystkich kontach. Aby uzyskać lepszą wydajność, należy użyć tylko jednego kontenera na konto magazynu.

**Rozmiar i typ maszyny wirtualnej (teraz obsługuje serię G)**  
Każdy typ klastra ma zestaw typów węzłów, a każdy typ węzła ma konkretne opcje rozmiaru i typu maszyny wirtualnej. Rozmiar i typ maszyny wirtualnej są określane przez procesor CPU, rozmiar pamięci RAM i opóźnienie sieci. Symulowane obciążenie może służyć do określenia optymalnego rozmiaru i typu maszyn wirtualnych dla każdego typu węzła.

**Liczba węzłów procesu roboczego**  
Początkową liczbę węzłów procesu roboczego można określić przy użyciu symulowanych obciążeń. Klaster można skalować później, dodając więcej węzłów procesu roboczego, aby spełnić wymagania dotyczące obciążenia szczytowego. Klaster można później skalować do tyłu, gdy nie są wymagane dodatkowe węzły procesu roboczego.

Aby uzyskać więcej informacji, zapoznaj się z artykułem [Planowanie pojemności dla klastrów usługi HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Użyj zalecanego typu maszyny wirtualnej dla klastra

Sprawdź [domyślną konfigurację węzła i rozmiary maszyn wirtualnych dla klastrów](../hdinsight-supported-node-configuration.md) dla zalecanych typów maszyn wirtualnych dla każdego typu klastra usługi HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Sprawdzanie dostępności składników usługi Hadoop w usłudze HDInsight

Każda wersja usługi HDInsight to dystrybucja w chmurze zestawu składników systemu Hadoop. Zobacz [przechowywanie wersji składników usługi HDInsight](../hdinsight-component-versioning.md) , aby uzyskać szczegółowe informacje na temat wszystkich składników usługi HDInsight i ich bieżących wersji.

Aby sprawdzić składniki i wersje usługi Hadoop w usłudze HDInsight, można także użyć interfejsu użytkownika Apache Ambari lub interfejsu API REST Ambari.

Aplikacje lub składniki, które były dostępne w klastrach lokalnych, ale nie są częścią klastrów usługi HDInsight, można dodawać w węźle brzegowym lub na maszynie wirtualnej w tej samej sieci wirtualnej co klaster usługi HDInsight. Aplikację Hadoop innej firmy, która nie jest dostępna w usłudze Azure HDInsight, można zainstalować przy użyciu opcji "aplikacje" w klastrze usługi HDInsight. Niestandardowe aplikacje usługi Hadoop można zainstalować w klastrze usługi HDInsight przy użyciu funkcji "akcje skryptu". W poniższej tabeli wymieniono niektóre typowe aplikacje i ich opcje integracji usługi HDInsight:

|**Aplikacja**|**Integracja**
|---|---|
|Przepływ powietrza|IaaS lub węzeł brzegowy usługi HDInsight
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Brak (tylko HDP)
|Datameer|Węzeł krawędzi usługi HDInsight
|DataStax (Cassandra)|IaaS (CosmosDB alternatywę na platformie Azure)
|DataTorrent|IaaS 
|Przechodzenie do szczegółów|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB alternatywę na platformie Azure)
|NiFi|IaaS 
|Presto|IaaS lub węzeł brzegowy usługi HDInsight
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW alternatywę na platformie Azure)
|Tableau|IaaS 
|Wodną|Węzeł krawędzi usługi HDInsight
|StreamSets|Krawędź usługi HDInsight 
|Palantir|IaaS 
|Sailpoint|IaaS 

Aby uzyskać więcej informacji, zobacz artykuł [Apache Hadoop składniki dostępne w różnych wersjach usługi HDInsight](../hdinsight-component-versioning.md)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu

Usługa HDInsight udostępnia metodę konfiguracji klastra nazywaną **akcją skryptu**. Akcja skryptu to skrypt bash, który działa w węzłach klastra usługi HDInsight i może służyć do instalowania dodatkowych składników i zmieniania ustawień konfiguracji.

Akcje skryptu muszą być przechowywane w identyfikatorze URI dostępnym z klastra usługi HDInsight. Mogą one być używane podczas tworzenia klastra lub po nim, a także mogą być ograniczone do uruchamiania tylko dla określonych typów węzłów.

Skrypt może być utrwalony lub wykonywany jeden raz. Utrwalone skrypty są używane do dostosowywania nowych węzłów procesu roboczego dodanych do klastra za pomocą operacji skalowania. Utrwalony skrypt może również zastosować zmiany do innego typu węzła, takiego jak węzeł główny, gdy wystąpią operacje skalowania.

Usługa HDInsight udostępnia wstępnie zapisane skrypty umożliwiające zainstalowanie następujących składników w klastrach usługi HDInsight:

- Dodawanie konta usługi Azure Storage
- Zainstaluj odcień
- Zainstaluj Presto
- Zainstaluj Solr
- Zainstaluj Giraph
- Wstępne ładowanie bibliotek Hive
- Instalowanie lub aktualizowanie środowiska Mono

> [!Note]  
> Usługa HDInsight nie zapewnia bezpośredniej obsługi niestandardowych składników lub składników usługi Hadoop zainstalowanych przy użyciu akcji skryptu.

Akcje skryptu można również publikować w portalu Azure Marketplace jako aplikację usługi HDInsight.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Instalowanie aplikacji Apache Hadoop innych firm w usłudze HDInsight](../hdinsight-apps-install-applications.md)
- [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publikowanie aplikacji usługi HDInsight w portalu Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Dostosowywanie konfiguracji usługi HDInsight przy użyciu narzędzia Bootstrap

Zmiany w konfiguracjach w plikach konfiguracyjnych, takich jak `core-site.xml` , `hive-site.xml` i `oozie-env.xml` można wykonać przy użyciu Bootstrap. Poniższy skrypt jest przykładem przy użyciu polecenia [AZ module](/powershell/azure/new-azureps-module-az) cmdlet [New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightcluster)programu PowerShell:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Aby uzyskać więcej informacji, zobacz artykuł [Dostosowywanie klastrów usługi HDInsight przy użyciu narzędzia Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Zobacz również [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu API REST usługi Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Dostęp do narzędzi klienckich z węzłów brzegowych klastra usługi HDInsight Hadoop

Pusty węzeł brzegowy to maszyna wirtualna z systemem Linux z tymi samymi narzędziami klienta zainstalowanymi i skonfigurowanymi jako na węzłach głównych, ale bez usług Hadoop uruchomionych. Węzeł brzegowy może być używany w następujących celach:

- Uzyskiwanie dostępu do klastra
- Testowanie aplikacji klienckich
- Hostowanie aplikacji klienckich

Węzły brzegowe można tworzyć i usuwać za pomocą Azure Portal i mogą być używane podczas tworzenia klastra lub po nim. Po utworzeniu węzła brzegowego można połączyć się z węzłem brzegowym przy użyciu protokołu SSH, a następnie uruchomić narzędzia klienckie w celu uzyskania dostępu do klastra Hadoop w usłudze HDInsight. Punkt końcowy SSH węzła brzegowego to `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22` .


Aby uzyskać więcej informacji, zobacz [używanie pustych węzłów brzegowych w klastrach Apache Hadoop w usłudze HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Korzystanie z funkcji skalowania w górę i w dół klastrów

Usługa HDInsight zapewnia elastyczność, zapewniając możliwość skalowania w górę i w dół liczby węzłów procesu roboczego w klastrach. Ta funkcja umożliwia zmniejszanie klastra po godzinach lub weekendy oraz rozszerzanie go w czasie szczytowego zapotrzebowania na działalność biznesową. Aby uzyskać więcej informacji, zobacz:

* [Skalowanie klastrów usługi HDInsight](../hdinsight-scaling-best-practices.md).
* [Skalowanie klastrów](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Korzystanie z usługi HDInsight w usłudze Azure Virtual Network

Usługa Azure Virtual Networks umożliwia korzystanie z zasobów platformy Azure, takich jak Azure Virtual Machines, w celu bezpiecznego komunikowania się ze sobą, Internetu i sieci lokalnych, przez filtrowanie i kierowanie ruchu sieciowego.

Korzystanie z usługi Azure Virtual Network w usłudze HDInsight umożliwia wykonywanie następujących scenariuszy:

- Łączenie z usługą HDInsight bezpośrednio z sieci lokalnej.
- Łączenie usługi HDInsight z magazynami danych w sieci wirtualnej platformy Azure.
- Bezpośredni dostęp do usług Hadoop, które nie są dostępne publicznie za pośrednictwem Internetu. Na przykład interfejsy API Kafka lub interfejs API Java HBase.

Usługę HDInsight można dodać do nowego lub istniejącego Virtual Network platformy Azure. Jeśli Usługa HDInsight jest dodawana do istniejącej Virtual Network, istniejące sieciowe grupy zabezpieczeń i trasy zdefiniowane przez użytkownika muszą zostać zaktualizowane, aby zezwolić na nieograniczony dostęp do [kilku adresów IP](../hdinsight-management-ip-addresses.md) w centrum danych platformy Azure. Upewnij się również, że nie Zablokuj ruchu do [portów](../control-network-traffic.md#required-ports), które są używane przez usługi HDInsight.

> [!Note]  
> Usługa HDInsight nie obsługuje obecnie wymuszonego tunelowania. Wymuszone tunelowanie to ustawienie podsieci, które wymusza wychodzący ruch internetowy do urządzenia w celu przeprowadzenia inspekcji i rejestrowania. Usuń wymuszone tunelowanie przed zainstalowaniem usługi HDInsight w podsieci lub Utwórz nową podsieć dla usługi HDInsight. Usługa HDInsight nie obsługuje również ograniczania łączności sieciowej wychodzącej.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Azure Virtual-Networks — Omówienie](../../virtual-network/virtual-networks-overview.md)
- [Rozszerzanie usługi Azure HDInsight za pomocą usługi Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Bezpieczne łączenie z usługami platformy Azure za pomocą punktów końcowych usługi Azure Virtual Network

Usługa HDInsight obsługuje [punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md), które umożliwiają bezpieczne łączenie się z bazami danych platformy Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB i SQL. Po włączeniu punktu końcowego usługi Azure HDInsight ruch przepływów odbywa się za pośrednictwem zabezpieczonej trasy z centrum danych platformy Azure. Dzięki temu zwiększonemu poziomowi zabezpieczeń w warstwie sieciowej można zablokować konta magazynu danych Big Data do określonych sieci wirtualnych (sieci wirtualnych) i nadal używać klastrów usługi HDInsight bezproblemowo, aby uzyskać dostęp do tych danych i ich przetworzyć.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Punkty końcowe usługi dla sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Rozszerzanie zabezpieczeń usługi HDInsight za pomocą punktów końcowych usług](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Łączenie usługi HDInsight z siecią lokalną

Usługa HDInsight może być połączona z siecią lokalną przy użyciu sieci wirtualnych platformy Azure i bramy sieci VPN. Następujące kroki mogą służyć do ustanowienia łączności:

- Użyj usługi HDInsight w usłudze Azure Virtual Network, która ma łączność z siecią lokalną.
- Skonfiguruj rozpoznawanie nazw DNS między siecią wirtualną a siecią lokalną.
- Skonfiguruj sieciowe grupy zabezpieczeń lub trasy zdefiniowane przez użytkownika (UDR), aby kontrolować ruch sieciowy.

Aby uzyskać więcej informacji, zobacz artykuł [łączenie usługi HDInsight z siecią lokalną](../connect-on-premises-network.md)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następnym artykułem z tej serii: [najlepsze rozwiązania dotyczące usługi Storage do migracji Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-storage.md).