---
title: Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych
description: Konfigurowanie klastrów Hadoop, Kafka, Spark, HBase, R Server lub burzy dla usługi HDInsight z poziomu przeglądarki, klasycznego interfejsu wiersza polecenia platformy Azure, Azure PowerShell, REST lub zestawu SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 08/06/2020
ms.openlocfilehash: 6ce5de354583da04905f9f889cfabe36e6da6667
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546130"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Dowiedz się, jak skonfigurować i skonfigurować Apache Hadoop, Apache Spark, Apache Kafka, interakcyjne zapytania, Apache HBase, usługi ML lub Apache Storm w usłudze HDInsight. Dowiedz się również, jak dostosować klastry i dodać zabezpieczenia, dołączając je do domeny.

Klaster Hadoop składa się z kilku maszyn wirtualnych (węzłów), które są używane do rozproszonego przetwarzania zadań. Usługa Azure HDInsight obsługuje szczegóły implementacji instalacji i konfiguracji poszczególnych węzłów, dlatego należy jedynie podać ogólne informacje o konfiguracji.

> [!IMPORTANT]  
> Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Dowiedz się, jak [usunąć klaster.](hdinsight-delete-cluster.md)

Jeśli używasz wielu klastrów razem, należy utworzyć sieć wirtualną, a jeśli używasz klastra Spark, należy również użyć łącznika magazynu programu Hive. Aby uzyskać więcej informacji, zobacz [Planowanie sieci wirtualnej dla usługi Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) i [integrowanie Apache Spark i Apache Hive z łącznikiem magazynu Hive](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Metody konfiguracji klastra

W poniższej tabeli przedstawiono różne metody konfigurowania klastra usługi HDInsight.

| Klastry utworzone za pomocą | Przeglądarka sieci Web | Wiersz polecenia | Interfejs API REST | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Witryna Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Szablony usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Ten artykuł przeprowadzi Cię przez proces instalacji w [Azure Portal](https://portal.azure.com), w którym można utworzyć klaster usługi HDInsight.

## <a name="basics"></a>Podstawy

![Tworzenie niestandardowych opcji usługi HDInsight — szybkie](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Szczegóły projektu

[Azure Resource Manager](../azure-resource-manager/management/overview.md) pomaga w pracy z zasobami w aplikacji jako grupą, nazywaną [grupą zasobów](../azure-resource-manager/management/overview.md#resource-groups)platformy Azure. Można wdrożyć, zaktualizować, monitorować lub usunąć wszystkie zasoby aplikacji w ramach jednej skoordynowanej operacji.

### <a name="cluster-details"></a>Szczegóły klastra

#### <a name="cluster-name"></a>Nazwa klastra

Nazwy klastrów usługi HDInsight mają następujące ograniczenia:

* Dozwolone znaki: a-z, 0-9, A-Z
* Maksymalna długość: 59
* Zastrzeżone nazwy: aplikacje
* Zakres nazewnictwa klastrów dotyczy wszystkich subskrypcji platformy Azure. Nazwa klastra musi być unikatowa na całym świecie.
* Pierwsze sześć znaków musi być unikatowe w obrębie sieci wirtualnej

#### <a name="region"></a>Region

Nie musisz jawnie określać lokalizacji klastra: klaster znajduje się w tej samej lokalizacji co magazyn domyślny. Aby uzyskać listę obsługiwanych regionów, wybierz listę rozwijaną **region** w [cenniku usługi HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Typ klastra

Usługa Azure HDInsight obecnie udostępnia następujące typy klastrów, z których każdy zawiera zestaw składników zapewniających pewne funkcje.

> [!IMPORTANT]  
> Klastry usługi HDInsight są dostępne w różnych typach, z których każdy jest przeznaczony dla jednego obciążenia lub technologii. Nie ma obsługiwanej metody tworzenia klastra, który łączy wiele typów, takich jak burza i HBase w jednym klastrze. Jeśli rozwiązanie wymaga technologii, które są rozłożone na wiele typów klastrów usługi HDInsight, [Sieć wirtualna platformy Azure](../virtual-network/index.yml) może połączyć wymagane typy klastrów.

| Typ klastra | Funkcja |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Zapytanie wsadowe i analiza przechowywanych danych |
| [HBase](hbase/apache-hbase-overview.md) |Przetwarzanie dużych ilości danych NoSQL |
| [Zapytanie interakcyjne](./interactive-query/apache-interactive-query-get-started.md) |Buforowanie w pamięci na potrzeby interaktywnych i szybszych zapytań Hive |
| [Kafka](kafka/apache-kafka-introduction.md) | Platforma przesyłania strumieniowego rozproszonego, która może służyć do tworzenia potoków danych i aplikacji w czasie rzeczywistym |
| [Usługi ML](r-server/r-server-overview.md) |Różne statystyki danych Big Data, modelowanie predykcyjne i możliwości uczenia maszynowego |
| [Spark](spark/apache-spark-overview.md) |Przetwarzanie w pamięci, interaktywne zapytania, przetwarzanie strumienia mikropartii |
| [Storm](storm/apache-storm-overview.md) |Przetwarzanie zdarzeń w czasie rzeczywistym |

#### <a name="version"></a>Wersja

Wybierz wersję usługi HDInsight dla tego klastra. Aby uzyskać więcej informacji, zobacz [obsługiwane wersje usługi HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Poświadczenia klastra

Klastry usługi HDInsight umożliwiają skonfigurowanie dwóch kont użytkowników podczas tworzenia klastra:

* Nazwa użytkownika logowania klastra: domyślna nazwa użytkownika to *admin* . Używa konfiguracji podstawowej w Azure Portal. Czasami jest to nazywane "użytkownikiem klastra" lub "HTTP User".
* Nazwa użytkownika Secure Shell (SSH): służy do nawiązywania połączenia z klastrem za pośrednictwem protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Nazwa użytkownika HTTP ma następujące ograniczenia:

* Dozwolone znaki specjalne: `_` i `@`
* Znaki nie są dozwolone: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ Space
* Maksymalna długość: 20

Nazwa użytkownika SSH ma następujące ograniczenia:

* Dozwolone znaki specjalne: `_` i `@`
* Znaki nie są dozwolone: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ Space
* Maksymalna długość: 64
* Nazwy zastrzeżone: Hadoop, users, Oozie, Hive, mapred, Ambari-pytań i odpowiedzi, dozorcy, tez, HDFS, Sqoop, przędza, hcat, AMS, HBase, burza, administrator, administrator, użytkownik, Użytkownik1, test,, TEST1, user3, admin1, 1, 123, a, actuser, adm, admin2, ASPNET, Backup, Console, David, gość, Jan, właściciel, katalog_główny Support_388945a0

## <a name="storage"></a>Magazyn

![Ustawienia magazynu klastra: punkty końcowe zgodne z systemem HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Mimo że lokalna instalacja usługi Hadoop wykorzystuje rozproszony system plików platformy Hadoop (HDFS) do magazynowania w klastrze, w chmurze używane są punkty końcowe usługi Storage połączone z klastrem. Korzystanie z magazynu w chmurze pozwala bezpiecznie usuwać klastry usługi HDInsight używane do obliczeń przy zachowaniu danych.

Klastry usługi HDInsight mogą korzystać z następujących opcji magazynu:

* Usługa Azure Data Lake Storage 2. generacji
* Azure Data Lake Storage Gen1
* Usługa Azure Storage Ogólnego przeznaczenia v2
* Usługa Azure Storage Ogólnego przeznaczenia wersja 1
* Blokowy obiekt BLOB usługi Azure Storage ( **obsługiwany tylko jako magazyn pomocniczy** )

Aby uzyskać więcej informacji na temat opcji magazynu w usłudze HDInsight, zobacz [porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

Podczas konfiguracji dla domyślnego punktu końcowego magazynu należy określić kontener obiektów BLOB dla konta usługi Azure Storage lub Data Lake Storage. Domyślny magazyn zawiera Dzienniki aplikacji i systemu. Opcjonalnie można określić dodatkowe połączone konta usługi Azure Storage i konta Data Lake Storage, do których może uzyskać dostęp klaster. Klaster usługi HDInsight i zależne konta magazynu muszą znajdować się w tej samej lokalizacji platformy Azure.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Włączenie bezpiecznego transferu magazynu po utworzeniu klastra może spowodować błędy przy użyciu konta magazynu i nie jest to zalecane. Lepszym rozwiązaniem jest utworzenie nowego klastra przy użyciu konta magazynu z już włączonym bezpiecznym transferem.

> [!Note]  
> Usługa Azure HDInsight nie umożliwia automatycznego transferu, przenoszenia ani kopiowania danych przechowywanych w usłudze Azure Storage z jednego regionu do innego.

### <a name="metastore-settings"></a>Ustawienia magazynu metadanych

Możesz tworzyć opcjonalne magazyny Hive lub Apache Oozie. Nie wszystkie typy klastrów obsługują jednak magazyny metadanych, a usługa Azure Synapse Analytics nie jest zgodna z magazynami metadanych.

Aby uzyskać więcej informacji, zobacz [Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Podczas tworzenia niestandardowego magazynu metadanych nie należy używać kresek, łączników ani spacji w nazwie bazy danych. Może to spowodować niepowodzenie procesu tworzenia klastra.

#### <a name="sql-database-for-hive"></a>Baza danych SQL dla programu Hive

Jeśli tabele programu Hive mają być zachowywane po usunięciu klastra usługi HDInsight, Użyj niestandardowego magazynu metadanych. Następnie można dołączyć magazyn metadanych do innego klastra usługi HDInsight.

HDInsight An magazynu metadanych utworzonego dla jednej wersji klastra usługi HDInsight nie można współużytkować w różnych wersjach klastra usługi HDInsight. Aby zapoznać się z listą wersji usługi HDInsight, zobacz [obsługiwane wersje usługi HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Domyślny magazyn metadanych zawiera Azure SQL Database z **limitem jednostek DTU warstwy Podstawowa 5 (bez uaktualnienia).** Odpowiednie dla podstawowych celów testowych. W przypadku obciążeń dużych lub produkcyjnych zalecamy Migrowanie do zewnętrznego magazynu metadanych.

#### <a name="sql-database-for-oozie"></a>Baza danych SQL dla Oozie

Aby zwiększyć wydajność podczas korzystania z programu Oozie, Użyj niestandardowego magazynu metadanych. Magazyn metadanych może także zapewnić dostęp do danych zadania Oozie po usunięciu klastra.

#### <a name="sql-database-for-ambari"></a>Baza danych SQL dla Ambari

Ambari służy do monitorowania klastrów usługi HDInsight, wprowadzania zmian konfiguracji i przechowywania informacji o zarządzaniu klastrami oraz historii zadań. Funkcja Custom Ambari DB pozwala wdrożyć nowy klaster i skonfigurować Ambari w zewnętrznej zarządzanej bazie danych. Aby uzyskać więcej informacji, zobacz temat [Custom AMBARI DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Nie można ponownie użyć niestandardowego magazynu metadanych Oozie. Aby użyć niestandardowego magazynu metadanych Oozie, należy podać puste Azure SQL Database podczas tworzenia klastra usługi HDInsight.

## <a name="security--networking"></a>Bezpieczeństwo i obsługa sieci

![Opcje tworzenia usługi HDInsight wybierz pozycję pakiet zabezpieczeń przedsiębiorstwa](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Pakiet zabezpieczeń przedsiębiorstwa

W przypadku typów klastrów usługi Hadoop, Spark, HBase, Kafka i interakcyjnych zapytań można włączyć **pakiet Enterprise Security** . Ten pakiet zapewnia bezpieczniejsze Konfigurowanie klastra przy użyciu usługi Apache Ranger i integrację z usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Pakiet Enterprise Security Package umożliwia integrację usługi HDInsight z usługami Active Directory i Apache Ranger. Wielu użytkowników można utworzyć przy użyciu pakietu Enterprise Security.

Aby uzyskać więcej informacji na temat tworzenia przyłączonych do domeny klastrów usługi HDInsight, zobacz [Tworzenie przyłączonych do domeny środowiska piaskownicy usługi HDInsight](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

### <a name="tls"></a>TLS

Aby uzyskać więcej informacji, zobacz [Transport Layer Security](./transport-layer-security.md)

### <a name="virtual-network"></a>Sieć wirtualna

Jeśli rozwiązanie wymaga technologii, które są rozłożone na wiele typów klastrów usługi HDInsight, [Sieć wirtualna platformy Azure](../virtual-network/index.yml) może połączyć wymagane typy klastrów. Ta konfiguracja umożliwia Klastrom oraz wszelki wdrożony kod, aby bezpośrednio komunikować się ze sobą.

Aby uzyskać więcej informacji na temat używania sieci wirtualnej platformy Azure z usługą HDInsight, zobacz [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md).

Aby zapoznać się z przykładem użycia dwóch typów klastrów w ramach sieci wirtualnej platformy Azure, zobacz [używanie Apache Spark strukturalnych przesyłania strumieniowego z Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight z siecią wirtualną, w tym określonych wymagań konfiguracji dla sieci wirtualnej, zobacz [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Ustawienie szyfrowania dysku

Aby uzyskać więcej informacji, zobacz [szyfrowanie dysków kluczy zarządzanych przez klienta](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Serwer proxy REST Kafka

To ustawienie jest dostępne tylko dla typu klastra Kafka. Aby uzyskać więcej informacji, zobacz [Korzystanie z serwera proxy REST](./kafka/rest-proxy.md).

### <a name="identity"></a>Tożsamość

Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości w usłudze Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfiguracja i Cennik

![HDInsight Wybieranie rozmiaru węzła](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

Opłaty za użycie węzłów są naliczane tak długo, jak klaster istnieje. Rozliczanie zaczyna się w momencie utworzenia klastra i zostaje zatrzymane po usunięciu klastra. Klastrów nie można cofnąć przydziału ani wstrzymać.

### <a name="node-configuration"></a>Konfiguracja węzła

Każdy typ klastra ma własną liczbę węzłów, terminologię dla węzłów i domyślny rozmiar maszyny wirtualnej. W poniższej tabeli liczba węzłów dla każdego typu węzła jest w nawiasach.

| Typ | Węzły | Diagram |
| --- | --- | --- |
| Hadoop |Węzeł główny (2), węzeł procesu roboczego (1 +) |![Węzły klastra Hadoop usługi HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Serwer głównych (2), serwer regionu (1 +), węzeł główny/dozorcy (3) |![Konfiguracja typu klastra HBase usługi HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Węzeł Nimbus (2), serwer nadzorujący (1 +), węzeł dozorcy (3) |![Konfiguracja typu klastra burzy usługi HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| platforma Spark |Węzeł główny (2), węzeł procesu roboczego (1 +), węzeł dozorcy (3) (bezpłatnie dla rozmiaru maszyny wirtualnej a1 dozorcy) |![Konfiguracja typu klastra Spark usługi HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Aby uzyskać więcej informacji, zobacz [domyślną konfigurację węzła i rozmiary maszyn wirtualnych dla klastrów](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) w artykule "Jakie są składniki i wersje usługi Hadoop w usłudze HDInsight?".

Koszt klastrów usługi HDInsight jest określany przez liczbę węzłów i rozmiary maszyn wirtualnych dla węzłów.

Różne typy klastrów mają różne typy węzłów, liczby węzłów i rozmiary węzłów:
* Typ klastra usługi Hadoop wartość domyślna:
    * Dwa *węzły główne*  
    * Cztery *węzły procesu roboczego*
* Typ klastra burzy jest domyślny:
    * Dwa *węzły Nimbus*
    * Trzy *węzły dozorcy*
    * Cztery *węzły nadzoru*

W przypadku wypróbowania usługi HDInsight zalecamy użycie jednego węzła procesu roboczego. Aby uzyskać więcej informacji na temat cennika usługi HDInsight, zobacz [Cennik usługi HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Limit rozmiaru klastra różni się między subskrypcjami platformy Azure. Aby zwiększyć limit, skontaktuj się z [pomocą techniczną dla usługi Azure rozliczenia](../azure-portal/supportability/how-to-create-azure-support-request.md) .

W przypadku konfigurowania klastra przy użyciu Azure Portal rozmiar węzła jest dostępny za pomocą karty **Konfiguracja i Cennik** . W portalu można także sprawdzić koszt związany z różnymi rozmiarami węzłów.

### <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych

Podczas wdrażania klastrów Wybierz zasoby obliczeniowe na podstawie rozwiązania, które planujesz wdrożyć. Następujące maszyny wirtualne są używane na potrzeby klastrów usługi HDInsight:

* Maszyny wirtualne serii a i D1-4: [rozmiary maszyn wirtualnych z systemem Linux ogólnego przeznaczenia](../virtual-machines/sizes-general.md)
* Maszyna wirtualna z serii D11-14: [rozmiary maszyn wirtualnych z systemem Linux zoptymalizowane pod kątem pamięci](../virtual-machines/sizes-memory.md)

Aby dowiedzieć się, jakiej wartości należy użyć do określenia rozmiaru maszyny wirtualnej podczas tworzenia klastra przy użyciu różnych zestawów SDK lub podczas korzystania z Azure PowerShell, zobacz [rozmiary maszyn wirtualnych, które mają być używane dla klastrów usługi HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Z tego połączonego artykułu Użyj wartości z kolumny **rozmiar** w tabelach.

> [!IMPORTANT]  
> Jeśli potrzebujesz więcej niż 32 węzłów procesu roboczego w klastrze, musisz wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../virtual-machines/sizes.md). Aby uzyskać informacje o cenach różnych rozmiarów, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Dodawanie aplikacji

Aplikacja usługi HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight opartym na systemie Linux. Możesz korzystać z aplikacji udostępnianych przez firmę Microsoft, inne firmy lub samodzielnie. Aby uzyskać więcej informacji, zobacz [Instalowanie aplikacji Apache Hadoop innych firm w usłudze Azure HDInsight](hdinsight-apps-install-applications.md).

Większość aplikacji usługi HDInsight jest instalowanych w pustym węźle krawędzi.  Pusty węzeł brzegowy to maszyna wirtualna z systemem Linux z tymi samymi narzędziami klienta zainstalowanymi i skonfigurowanymi jako w węźle głównym. Węzeł brzegowy służy do uzyskiwania dostępu do klastra, testowania aplikacji klienckich i hostowania aplikacji klienckich. Aby uzyskać więcej informacji, zobacz [używanie pustych węzłów brzegowych w usłudze HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Akcje skryptu

Możesz zainstalować dodatkowe składniki lub dostosować konfigurację klastra przy użyciu skryptów podczas tworzenia. Takie skrypty są wywoływane za pośrednictwem **akcji skryptu** , która jest opcją konfiguracji, której można użyć z Azure Portal, poleceń cmdlet programu Windows PowerShell usługi HDInsight lub zestawu .NET SDK usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastra usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Niektóre natywne składniki języka Java, takie jak Apache Mahout i kaskadowe, można uruchamiać w klastrze jako pliki archiwum Java (JAR). Te pliki JAR mogą być dystrybuowane do usługi Azure Storage i przesyłane do klastrów HDInsight przy użyciu mechanizmów przesyłania zadań w usłudze Hadoop. Aby uzyskać więcej informacji, zobacz artykuł [przesyłanie zadań Apache Hadoop programowo](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Jeśli masz problemy z wdrażaniem plików JAR w klastrach usługi HDInsight lub wywoływanie plików JAR w klastrach usługi HDInsight, skontaktuj się z [Pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support/options/).
>
> Łączenie kaskadowe nie jest obsługiwane przez usługi HDInsight i nie kwalifikuje się do pomoc techniczna firmy Microsoft. Aby uzyskać listę obsługiwanych składników, zobacz [co nowego w wersjach klastra dostarczonych przez usługi HDInsight](hdinsight-component-versioning.md).

Czasami podczas procesu tworzenia należy skonfigurować następujące pliki konfiguracji:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred — lokacja
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastrów usługi HDInsight przy użyciu narzędzia Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z błędami tworzenia klastra za pomocą usługi Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Co to są usługi HDInsight, ekosystem Apache Hadoop i klastry Hadoop?](hadoop/apache-hadoop-introduction.md)
* [Wprowadzenie do korzystania z Apache Hadoop w usłudze HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Pracuj w Apache Hadoop w usłudze HDInsight z komputera z systemem Windows](hdinsight-hadoop-windows-tools.md)