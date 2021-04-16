---
title: Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych
description: Konfigurowanie klastrów Hadoop, Kafka, Spark, HBase, R Server lub Storm dla usługi HDInsight z przeglądarki, klasycznego interfejsu wiersza polecenia platformy Azure, interfejsu Azure PowerShell, rest lub zestawu SDK.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18, devx-track-azurecli
ms.date: 08/06/2020
ms.openlocfilehash: 3d1059ab46ff0e3722d1f6538bba61cdc4e8cb59
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482691"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Dowiedz się, jak skonfigurować i skonfigurować usługi Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, usługi ML lub Apache Storm w umacie HDInsight. Dowiedz się również, jak dostosowywać klastry i dodawać zabezpieczenia, przyłączając je do domeny.

Klaster Hadoop składa się z kilku maszyn wirtualnych (węzłów), które są używane do rozproszonego przetwarzania zadań. Azure HDInsight obsługuje szczegóły implementacji instalacji i konfiguracji poszczególnych węzłów, więc wystarczy podać tylko ogólne informacje o konfiguracji.

> [!IMPORTANT]  
> Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Dowiedz się, [jak usunąć klaster.](hdinsight-delete-cluster.md)

Jeśli używasz wielu klastrów jednocześnie, warto utworzyć sieć wirtualną, a jeśli używasz klastra Spark, możesz również użyć tej Hive Warehouse Connector. Aby uzyskać więcej informacji, zobacz [Planowanie sieci wirtualnej](./hdinsight-plan-virtual-network-deployment.md) na Azure HDInsight i [Integrowanie](interactive-query/apache-hive-warehouse-connector.md)Apache Spark i Apache Hive z Hive Warehouse Connector .

## <a name="cluster-setup-methods"></a>Metody konfiguracji klastra

W poniższej tabeli przedstawiono różne metody konfiguracji klastra usługi HDInsight.

| Klastry utworzone za pomocą | Przeglądarka sieci Web | Wiersz polecenia | Interfejs API REST | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Witryna Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |â za" |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |â za" |â za" |â za" |â za" |
| [Interfejs wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |â za" |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |â za" |&nbsp; |&nbsp; |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |â za" |â za" |&nbsp; |
| [Szablony usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |â za" |&nbsp; |&nbsp; |

W tym artykule otworzymy konfigurację w Azure Portal [,](https://portal.azure.com)w którym można utworzyć klaster usługi HDInsight.

## <a name="basics"></a>Podstawy

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png" alt-text="hdinsight create options custom quick":::

### <a name="project-details"></a>Szczegóły projektu

[Azure Resource Manager](../azure-resource-manager/management/overview.md) ułatwia pracę z zasobami w aplikacji jako grupą określaną jako grupa [zasobów platformy](../azure-resource-manager/management/overview.md#resource-groups)Azure. Wszystkie zasoby aplikacji można wdrażać, aktualizować, monitorować i usuwać w ramach jednej skoordynowanej operacji.

### <a name="cluster-details"></a>Szczegóły klastra

#### <a name="cluster-name"></a>Nazwa klastra

Nazwy klastrów usługi HDInsight mają następujące ograniczenia:

* Dozwolone znaki: a–z, 0–9, A–Z
* Maksymalna długość: 59
* Nazwy zarezerwowane: aplikacje
* Zakres nazewnictwa klastra jest dla całej platformy Azure we wszystkich subskrypcjach. Dlatego nazwa klastra musi być unikatowa na całym świecie.
* Pierwsze sześć znaków musi być unikatowych w sieci wirtualnej

#### <a name="region"></a>Region

Nie musisz jawnie określać lokalizacji klastra: klaster znajduje się w tej samej lokalizacji co magazyn domyślny. Aby uzyskać listę obsługiwanych regionów, wybierz **listę rozwijaną Region** w [cenniku usługi HDInsight.](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)

#### <a name="cluster-type"></a>Typ klastra

Azure HDInsight obecnie udostępnia następujące typy klastrów, z których każdy zawiera zestaw składników w celu zapewnienia pewnych funkcji.

> [!IMPORTANT]  
> Klastry HDInsight są dostępne w różnych typach, z których każdy jest dostępny dla jednego obciążenia lub technologii. Nie ma obsługiwanej metody tworzenia klastra, który łączy wiele typów, takich jak Storm i HBase w jednym klastrze. Jeśli Twoje rozwiązanie wymaga technologii, które są rozłożone na wiele typów klastrów usługi HDInsight, sieć [wirtualna](../virtual-network/index.yml) platformy Azure może połączyć wymagane typy klastrów.

| Typ klastra | Funkcjonalność |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Zapytanie wsadowe i analiza przechowywanych danych |
| [HBase](hbase/apache-hbase-overview.md) |Przetwarzanie dużych ilości danych NoSQL bez schematu |
| [Zapytanie interakcyjne](./interactive-query/apache-interactive-query-get-started.md) |Buforowanie w pamięci dla interaktywnych i szybszych zapytań Hive |
| [Kafka](kafka/apache-kafka-introduction.md) | Rozproszona platforma przesyłania strumieniowego, która może służyć do tworzenia aplikacji i potoków danych przesyłania strumieniowego w czasie rzeczywistym |
| [Usługi ML](r-server/r-server-overview.md) |Różne statystyki danych big data, modelowanie predykcyjne i możliwości uczenia maszynowego |
| [Spark](spark/apache-spark-overview.md) |Przetwarzanie w pamięci, interakcyjne zapytania, przetwarzanie strumienia mikrosadowego |
| [Storm](storm/apache-storm-overview.md) |Przetwarzanie zdarzeń w czasie rzeczywistym |

#### <a name="version"></a>Wersja

Wybierz wersję usługi HDInsight dla tego klastra. Aby uzyskać więcej informacji, zobacz [Obsługiwane wersje hdInsight.](hdinsight-component-versioning.md#supported-hdinsight-versions)

### <a name="cluster-credentials"></a>Poświadczenia klastra

Za pomocą klastrów usługi HDInsight można skonfigurować dwa konta użytkowników podczas tworzenia klastra:

* Nazwa użytkownika logowania klastra: domyślna nazwa użytkownika to *admin*. Używa podstawowej konfiguracji na Azure Portal. Czasami jest on nazywany "użytkownikiem klastra" lub "użytkownikiem HTTP".
* Secure Shell użytkownika (SSH): służy do nawiązywania połączenia z klastrem za pośrednictwem połączenia SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Nazwa użytkownika HTTP ma następujące ograniczenia:

* Dozwolone znaki specjalne: `_` i `@`
* Niedozwolone znaki: #;."', \/ :'!*?$() {} []<>|&--=+%~^space
* Maksymalna długość: 20

Nazwa użytkownika SSH ma następujące ograniczenia:

* Dozwolone znaki specjalne: `_` i `@`
* Niedozwolone znaki: #;."', \/ :'!*?$() {} []<>|&--=+%~^space
* Maksymalna długość: 64
* Nazwy zarezerwowane: hadoop, users, oozie, hive, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, yarn, hcat, ams, hbase, storm, administrator, admin, user, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, console, david, guest, john, owner, root, server, sql, support, support_388945a0, sys, test2, test3, user4, user5, spark

## <a name="storage"></a>Storage

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png" alt-text="Ustawienia magazynu klastra: punkty końcowe zgodne z systemem plików HDFS":::

Mimo że lokalna instalacja usługi Hadoop używa usługi Hadoop rozproszony system plików (HDFS) na użytek magazynu w klastrze, w chmurze są używane punkty końcowe magazynu połączone z klastrem. Użycie magazynu w chmurze oznacza, że można bezpiecznie usunąć klastry usługi HDInsight używane do obliczeń przy zachowaniu danych.

Klastry usługi HDInsight mogą korzystać z następujących opcji magazynowania:

* Usługa Azure Data Lake Storage 2. generacji
* Usługa Azure Data Lake Storage 1. generacji
* Azure Storage Ogólnego przeznaczenia v2
* Azure Storage Ogólnego przeznaczenia v1
* Blokowy obiekt blob usługi Azure Storage **(obsługiwany tylko jako magazyn pomocniczy)**

Aby uzyskać więcej informacji na temat opcji magazynu w umacie USŁUGI HDInsight, zobacz Porównanie opcji magazynu do użycia z [Azure HDInsight klastrami.](hdinsight-hadoop-compare-storage-options.md)

> [!WARNING]  
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

Podczas konfiguracji dla domyślnego punktu końcowego magazynu należy określić kontener obiektów blob konta usługi Azure Storage lub Data Lake Storage. Domyślny magazyn zawiera dzienniki aplikacji i systemu. Opcjonalnie możesz określić dodatkowe połączone konta usługi Azure Storage i Data Lake Storage, do których klaster może uzyskać dostęp. Klaster usługi HDInsight i zależne konta magazynu muszą znajdować się w tej samej lokalizacji platformy Azure.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Włączenie bezpiecznego transferu magazynu po utworzeniu klastra może spowodować błędy podczas korzystania z konta magazynu i nie jest zalecane. Lepiej jest utworzyć nowy klaster przy użyciu konta magazynu z włączonym bezpiecznym transferem.

> [!Note]  
> Azure HDInsight nie przesyła automatycznie, nie przenosi ani nie kopiuje danych przechowywanych w usłudze Azure Storage z jednego regionu do innego.

### <a name="metastore-settings"></a>Ustawienia magazynu metadanych

Możesz utworzyć opcjonalne magazyny metadanych Hive lub Apache Oozie. Jednak nie wszystkie typy klastrów obsługują magazyny metadanych i Azure Synapse Analytics nie są zgodne z magazynami metadanych.

Aby uzyskać więcej informacji, zobacz [Używanie zewnętrznych magazynów metadanych w Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Podczas tworzenia niestandardowego magazynu metadanych nie używaj łączników, łączników ani spacji w nazwie bazy danych. Może to spowodować niepowodzenie procesu tworzenia klastra.

#### <a name="sql-database-for-hive"></a>Baza danych SQL dla programu Hive

Jeśli chcesz zachować tabele hive po usunięciu klastra usługi HDInsight, użyj niestandardowego magazynu metadanych. Następnie możesz dołączyć magazyn metadanych do innego klastra usługi HDInsight.

Magazyn metadanych usługi HDInsight utworzony dla jednej wersji klastra usługi HDInsight nie może być współużytowany w różnych wersjach klastra usługi HDInsight. Aby uzyskać listę wersji programu HDInsight, zobacz [Obsługiwane wersje hdInsight.](hdinsight-component-versioning.md#supported-hdinsight-versions)

> [!IMPORTANT]
> Domyślny magazyn metadanych zapewnia Azure SQL Database z limitem jednostek DTU warstwy **Podstawowej 5 (nie można go uaktualnić)**! Odpowiednie do podstawowych celów testowych. W przypadku dużych obciążeń lub obciążeń produkcyjnych zalecamy migrowanie do zewnętrznego magazynu metadanych.

#### <a name="sql-database-for-oozie"></a>Baza danych SQL dla Oozie

Aby zwiększyć wydajność podczas korzystania z systemu Oozie, użyj niestandardowego magazynu metadanych. Magazyn metadanych może również zapewnić dostęp do danych zadania Oozie po usunięciu klastra.

#### <a name="sql-database-for-ambari"></a>Baza danych SQL dla systemu Ambari

System Ambari służy do monitorowania klastrów usługi HDInsight, zmieniania konfiguracji i przechowywania informacji o zarządzaniu klastrem, a także historii zadań. Niestandardowa funkcja bazy danych Ambari umożliwia wdrażanie nowego klastra i konfigurowanie systemu Ambari w zewnętrznej bazie danych, która jest zarządzana. Aby uzyskać więcej informacji, zobacz [Custom Ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Nie można ponownie użyć niestandardowego magazynu metadanych Oozie. Aby użyć niestandardowego magazynu metadanych Oozie, należy podać pustą Azure SQL Database podczas tworzenia klastra usługi HDInsight.

## <a name="security--networking"></a>Zabezpieczenia i sieć

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png" alt-text="opcje tworzenia usługi hdinsight — wybieranie pakietu Enterprise Security":::

### <a name="enterprise-security-package"></a>Pakiet zabezpieczeń przedsiębiorstwa

W przypadku typów klastrów Hadoop, Spark, HBase, Kafka i Interactive Query można włączyć tę **pakiet Enterprise Security**. Ten pakiet udostępnia opcję bezpieczniejszej konfiguracji klastra przy użyciu platformy Apache Ranger i integracji z usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Overview of enterprise security in Azure HDInsight (Omówienie](./domain-joined/hdinsight-security-overview.md)zabezpieczeń przedsiębiorstwa w Azure HDInsight ).

Pakiet zabezpieczeń przedsiębiorstwa umożliwia integrację usługi HDInsight z usługami Active Directory i Apache Ranger. Przy użyciu pakietu Enterprise Security można utworzyć wielu użytkowników.

Aby uzyskać więcej informacji na temat tworzenia przyłączony do domeny klastra usługi HDInsight, zobacz [Create domain-joined HDInsight sandbox environment](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)(Tworzenie przyłączone do domeny środowiska piaskownicy usługi HDInsight).

### <a name="tls"></a>TLS

Aby uzyskać więcej informacji, zobacz [Transport Layer Security](./transport-layer-security.md)

### <a name="virtual-network"></a>Sieć wirtualna

Jeśli rozwiązanie wymaga technologii, które są rozłożone na wiele typów klastrów usługi HDInsight, sieć wirtualna platformy [Azure](../virtual-network/index.yml) może połączyć wymagane typy klastrów. Ta konfiguracja umożliwia klastrom i każdemu wdrażanemu w nich kodowi bezpośrednią komunikację między sobą.

Aby uzyskać więcej informacji na temat używania sieci wirtualnej platformy Azure z usługą HDInsight, zobacz Planowanie sieci [wirtualnej dla usługi HDInsight.](hdinsight-plan-virtual-network-deployment.md)

Aby uzyskać przykład użycia dwóch typów klastrów w sieci wirtualnej platformy Azure, zobacz Use Apache Spark Structured Streaming with Apache Kafka (Używanie przesyłania strumieniowego ze strukturą Apache Spark za [pomocą Apache Kafka).](hdinsight-apache-kafka-spark-structured-streaming.md) Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight z siecią wirtualną, w tym określone wymagania dotyczące konfiguracji dla sieci wirtualnej, zobacz Planowanie sieci wirtualnej [dla usługi HDInsight.](hdinsight-plan-virtual-network-deployment.md)

### <a name="disk-encryption-setting"></a>Ustawienie szyfrowania dysków

Aby uzyskać więcej informacji, zobacz [Szyfrowanie dysków kluczem zarządzanym przez klienta.](./disk-encryption.md)

### <a name="kafka-rest-proxy"></a>Serwer proxy REST Kafka

To ustawienie jest dostępne tylko dla klastra typu Kafka. Aby uzyskać więcej informacji, zobacz [Using a REST proxy (Używanie serwera proxy REST).](./kafka/rest-proxy.md)

### <a name="identity"></a>Tożsamość

Aby uzyskać więcej informacji, zobacz [Tożsamości zarządzane w Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfiguracja i cennik

:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png" alt-text="Wybieranie rozmiaru węzła w umacie HDInsight":::

Opłaty za użycie węzłów są naliczane tak długo, jak długo istnieje klaster. Rozliczanie rozpoczyna się po utworzeniu klastra i zatrzymuje się po usunięciu klastra. Klastrów nie można cokońcowo przydzielać ani wstrzymywać.

### <a name="node-configuration"></a>Konfiguracja węzła

Każdy typ klastra ma własną liczbę węzłów, terminologię dla węzłów i domyślny rozmiar maszyny wirtualnej. W poniższej tabeli liczba węzłów dla każdego typu węzła jest w nawiasach.

| Typ | Węzły | Diagram |
| --- | --- | --- |
| Hadoop |Węzeł główny (2), węzeł procesu roboczego (1 lub więcej) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png" alt-text="Węzły klastra hadoop usługi HDInsight" border="false"::: |
| HBase |Serwer główny (2), serwer regionu (1 lub więcej), węzeł główny/węzeł ZooKeeper (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png" alt-text="Konfiguracja typu klastra HBase usługi HDInsight" border="false"::: |
| Storm |Węzeł Nimbus (2), serwer nadzorcy (1+), węzeł ZooKeeper (3) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png" alt-text="Konfiguracja typu klastra storm usługi HDInsight" border="false"::: |
| platforma Spark |Węzeł główny (2), węzeł procesu roboczego (1 lub więcej), węzeł ZooKeeper (3) (bezpłatnie dla rozmiaru maszyny wirtualnej usługi ZooKeeper A1) |:::image type="content" source="./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png" alt-text="Konfiguracja typu klastra Spark w umacie HDInsight" border="false"::: |

Aby uzyskać więcej informacji, zobacz [Domyślna](hdinsight-supported-node-configuration.md) konfiguracja węzła i rozmiary maszyn wirtualnych dla klastrów w temacie "What are the Hadoop components and versions in HDInsight?" (Jakie są składniki i wersje platformy Hadoop w umacie HDInsight?)

Koszt klastrów usługi HDInsight zależy od liczby węzłów i rozmiarów maszyn wirtualnych dla węzłów.

Różne typy klastrów mają różne typy węzłów, liczbę węzłów i rozmiary węzłów:
* Domyślny typ klastra Hadoop:
    * Dwa *węzły główne*
    * Cztery *węzły procesu roboczego*
* Domyślny typ klastra Storm:
    * Dwa *węzły Nimbus*
    * Trzy *węzły dozorcy*
    * Cztery *węzły nadzorcy*

Jeśli po prostu próbujesz użyć funkcji HDInsight, zalecamy użycie jednego węzła procesu roboczego. Aby uzyskać więcej informacji na temat cen usługi HDInsight, zobacz [cennik usługi HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Limit rozmiaru klastra różni się w zależności od subskrypcji platformy Azure. Skontaktuj się [z działem pomocy technicznej dotyczącej](../azure-portal/supportability/how-to-create-azure-support-request.md) rozliczeń platformy Azure, aby zwiększyć limit.

W przypadku konfigurowania klastra Azure Portal konfiguracji rozmiar węzła jest dostępny za pośrednictwem **karty Konfiguracja i** cennik. W portalu można również zobaczyć koszt związany z różnymi rozmiarami węzłów.

### <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych

Podczas wdrażania klastrów wybierz zasoby obliczeniowe na podstawie rozwiązania, które planujesz wdrożyć. Następujące maszyny wirtualne są używane w przypadku klastrów usługi HDInsight:

* Maszyny wirtualne z serii A i D1–4: [rozmiary maszyn wirtualnych ogólnego przeznaczenia z systemem Linux](../virtual-machines/sizes-general.md)
* Maszyny wirtualne serii D11–14: rozmiary maszyn wirtualnych [z systemem Linux zoptymalizowanych pod kątem pamięci](../virtual-machines/sizes-memory.md)

Aby dowiedzieć się, jakiej wartości należy użyć do określenia rozmiaru maszyny wirtualnej podczas tworzenia klastra przy użyciu różnych zestawów SDK lub podczas korzystania z usługi Azure PowerShell, zobacz Rozmiary maszyn wirtualnych do użycia dla klastrów usługi [HDInsight.](../cloud-services/cloud-services-sizes-specs.md#size-tables) W tym połączonym artykule użyj wartości w kolumnie **Size** (Rozmiar) tabel.

> [!IMPORTANT]  
> Jeśli potrzebujesz więcej niż 32 węzłów procesu roboczego w klastrze, musisz wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.

Aby uzyskać więcej informacji, zobacz [Sizes for virtual machines (Rozmiary maszyn wirtualnych).](../virtual-machines/sizes.md) Aby uzyskać informacje o cenach różnych rozmiarów, zobacz [Cennik usługi HDInsight.](https://azure.microsoft.com/pricing/details/hdinsight)

### <a name="add-application"></a>Dodawanie aplikacji

Aplikacja usługi HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight opartym na systemie Linux. Możesz korzystać z aplikacji dostarczanych przez firmę Microsoft, inne firmy lub samodzielnie przez Siebie tworzyć. Aby uzyskać więcej informacji, zobacz [Install third-party Apache Hadoop applications on Azure HDInsight](hdinsight-apps-install-applications.md)(Instalowanie aplikacji apache Hadoop innych firm na platformie Azure HDInsight).

Większość aplikacji HDInsight jest instalowana w pustym węźle krawędzi.  Pusty węzeł krawędzi to maszyna wirtualna z systemem Linux z zainstalowanymi i skonfigurowanymi narzędziami klienta, które są zainstalowane i skonfigurowane w węźle głównym. Możesz użyć węzła brzegowego do uzyskiwania dostępu do klastra, testowania aplikacji klienckich i hostowania aplikacji klienckich. Aby uzyskać więcej informacji, zobacz Use empty edge nodes in HDInsight (Używanie [pustych węzłów brzegowych w umacie HDInsight).](hdinsight-apps-use-edge-node.md)

### <a name="script-actions"></a>Akcje skryptu

Podczas tworzenia można zainstalować dodatkowe składniki lub dostosować konfigurację klastra za pomocą skryptów. Takie skrypty są wywoływane za pośrednictwem akcji skryptu **,** która jest opcją konfiguracji, której można użyć z polecenia cmdlet programu Azure Portal, polecenia cmdlet usługi HDInsight Windows PowerShell lub zestawu .NET SDK usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastra usługi HDInsight przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Niektóre natywne składniki języka Java, takie jak Apache Mahout i Cascading, można uruchamiać w klastrze jako pliki Archiwum Java (JAR). Te pliki JAR mogą być dystrybuowane do usługi Azure Storage i przesyłane do klastrów usługi HDInsight za pomocą mechanizmów przesyłania zadań usługi Hadoop. Aby uzyskać więcej informacji, zobacz [Programowe przesyłanie zadań Apache Hadoop.](hadoop/submit-apache-hadoop-jobs-programmatically.md)

> [!NOTE]  
> Jeśli masz problemy z wdrażaniem plików JAR w klastrach usługi HDInsight lub wywoływaniem plików JAR w klastrach usługi HDInsight, skontaktuj się z [Pomoc techniczna Microsoft](https://azure.microsoft.com/support/options/).
>
> Kaskadowe działania nie są obsługiwane przez program HDInsight i nie kwalifikują się do Pomoc techniczna Microsoft. Aby uzyskać listę obsługiwanych składników, zobacz [Co nowego w wersjach klastra dostarczanych przez usługę HDInsight.](hdinsight-component-versioning.md)

Czasami podczas procesu tworzenia chcesz skonfigurować następujące pliki konfiguracji:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Aby uzyskać więcej informacji, zobacz [Customize HDInsight clusters using Bootstrap (Dostosowywanie klastrów usługi HDInsight przy użyciu narzędzia Bootstrap).](hdinsight-hadoop-customize-cluster-bootstrap.md)

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z błędami tworzenia klastra za pomocą Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Co to jest usługa HDInsight, ekosystem Apache Hadoop i klastry Hadoop?](hadoop/apache-hadoop-introduction.md)
* [Rozpoczynanie pracy z usługą Apache Hadoop w umacie HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Praca z platformą Apache Hadoop w umacie HDInsight z komputera z systemem Windows](hdinsight-hadoop-windows-tools.md)
