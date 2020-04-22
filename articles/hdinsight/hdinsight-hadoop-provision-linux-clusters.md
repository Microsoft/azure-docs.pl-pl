---
title: Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych
description: Konfigurowanie klastrów Hadoop, Kafka, Spark, HBase, R Server lub Storm dla usługi HDInsight z przeglądarki, klasycznego interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell, REST lub zestawu SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 6bba71ab35920027cc7296e774936c3d1deb8f92
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770315"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Dowiedz się, jak skonfigurować i skonfigurować Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services lub Apache Storm w programie HDInsight. Dowiedz się również, jak dostosować klastry i dodać zabezpieczenia, dołączając je do domeny.

Klaster Hadoop składa się z kilku maszyn wirtualnych (węzłów), które są używane do rozproszonego przetwarzania zadań. Usługa Azure HDInsight obsługuje szczegóły implementacji instalacji i konfiguracji poszczególnych węzłów, więc musisz tylko podać ogólne informacje o konfiguracji.

> [!IMPORTANT]  
> Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Dowiedz się, jak [usunąć klaster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Metody konfiguracji klastra

W poniższej tabeli przedstawiono różne metody konfigurowania klastra HDInsight.

| Klastry utworzone za pomocą | Przeglądarka sieci Web | Wiersz polecenia | Interfejs API REST | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Szablony usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

W tym artykule otrzymasz od konfiguracji w [witrynie Azure portal](https://portal.azure.com), w której można utworzyć klaster USŁUGI HDInsight.

## <a name="basics"></a>Podstawy

![hdinsight tworzenie opcji niestandardowe szybkie](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Szczegóły projektu

[Usługa Azure Resource Manager](../azure-resource-manager/management/overview.md) ułatwia pracę z zasobami w aplikacji jako grupa, zwana [grupą zasobów](../azure-resource-manager/management/overview.md#resource-groups)platformy Azure. Można wdrożyć, zaktualizować, monitorować lub usunąć wszystkie zasoby dla aplikacji w jednej skoordynowanej operacji.

### <a name="cluster-details"></a>Szczegóły klastra

#### <a name="cluster-name"></a>Nazwa klastra

Nazwy klastra usługi HDInsight mają następujące ograniczenia:

* Dozwolone znaki: a-z, 0-9, A-Z
* Maksymalna długość: 59
* Nazwy zastrzeżone: aplikacje
* Zakres nazewnictwa klastra jest dla całej platformy Azure, we wszystkich subskrypcjach. Tak więc nazwa klastra musi być unikatowa na całym świecie.
* Pierwsze sześć znaków musi być unikatowe w sieci wirtualnej

#### <a name="region"></a>Region

Nie trzeba wyraźnie określać lokalizacji klastra: klaster znajduje się w tej samej lokalizacji co magazyn domyślny. Aby uzyskać listę obsługiwanych regionów, wybierz listę rozwijaną **Region** w [cenniku HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Typ klastra

Usługa Azure HDInsight udostępnia obecnie następujące typy klastrów, z których każdy zawiera zestaw składników, aby zapewnić pewne funkcje.

> [!IMPORTANT]  
> Klastry HDInsight są dostępne w różnych typach, każdy dla pojedynczego obciążenia lub technologii. Nie ma żadnej obsługiwanej metody tworzenia klastra, który łączy wiele typów, takich jak Storm i HBase w jednym klastrze. Jeśli rozwiązanie wymaga technologii, które są rozłożone na wiele typów klastra USŁUGI HDInsight, [sieć wirtualna platformy Azure](https://docs.microsoft.com/azure/virtual-network) można połączyć wymagane typy klastra.

| Typ klastra | Funkcjonalność |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Kwerenda wsadowa i analiza przechowywanych danych |
| [HBase](hbase/apache-hbase-overview.md) |Przetwarzanie dużych ilości danych bez schematu, NoSQL |
| [Zapytanie interakcyjne](./interactive-query/apache-interactive-query-get-started.md) |Buforowanie w pamięci dla interaktywnych i szybszych zapytań hive |
| [Kafka](kafka/apache-kafka-introduction.md) | Rozproszona platforma przesyłania strumieniowego, która może służyć do tworzenia potoków i aplikacji przesyłania strumieniowego danych w czasie rzeczywistym |
| [Usługi ML](r-server/r-server-overview.md) |Różne statystyki dużych zbiorów danych, modelowanie predykcyjne i funkcje uczenia maszynowego |
| [platforma Spark](spark/apache-spark-overview.md) |Przetwarzanie w pamięci, zapytania interaktywne, przetwarzanie strumienia mikro wsadowego |
| [Storm](storm/apache-storm-overview.md) |Przetwarzanie zdarzeń w czasie rzeczywistym |

#### <a name="version"></a>Wersja

Wybierz wersję programu HDInsight dla tego klastra. Aby uzyskać więcej informacji, zobacz [Obsługiwane wersje programu HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Poświadczenia klastra

Za pomocą klastrów HDInsight można skonfigurować dwa konta użytkowników podczas tworzenia klastra:

* Nazwa użytkownika logowania do klastra: domyślną nazwą użytkownika jest *admin*. Używa podstawowej konfiguracji w witrynie Azure portal. Czasami jest nazywany "użytkownik klastra" lub "użytkownik HTTP".
* Nazwa użytkownika secure shell (SSH): służy do łączenia się z klastrem za pośrednictwem SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Nazwa użytkownika HTTP ma następujące ograniczenia:

* Dozwolone znaki specjalne: `_` i`@`
* Znaki niedozwolone: #;."',\/:'!*?$(){}[]<>|&--=+%~^spacja
* Maksymalna długość: 20

Nazwa użytkownika SSH ma następujące ograniczenia:

* Dozwolone znaki specjalne:`_` i`@`
* Znaki niedozwolone: #;."',\/:'!*?$(){}[]<>|&--=+%~^spacja
* Maksymalna długość: 64
* Zastrzeżone nazwy: hadoop, użytkownicy, oozie, ul, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, przędza, hcat, ams, hbase, burza, administrator, administrator, użytkownik, użytkownik1, test, użytkownik2, test1, użytkownik3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, konsola, David, gość, John, właściciel, korzeń, serwer, sql, wsparcie, support_388945a0, sys, test2, test3, użytkownik4, użytkownik5, iskra

## <a name="storage"></a>Magazyn

![Ustawienia pamięci masowej klastra: punkty końcowe zgodne z systemem HDFS](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Chociaż lokalna instalacja usługi Hadoop używa rozproszonego systemu plików Hadoop (HDFS) do przechowywania w klastrze, w chmurze używane są punkty końcowe magazynu połączone z klastrem. Korzystanie z magazynu w chmurze oznacza, że można bezpiecznie usunąć klastry HDInsight używane do obliczeń przy jednoczesnym zachowaniu danych.

Klastry HDInsight mogą korzystać z następujących opcji magazynu:

* Usługa Azure Data Lake Storage 2. generacji
* Usługa Azure Data Lake Storage 1. generacji
* Ogólnego przeznaczenia usługi Azure Storage w wersji 2
* Ogólnego przeznaczenia usługi Azure Storage w wersji 1
* Obiekt blob bloku usługi Azure Storage **(obsługiwany tylko jako magazyn pomocniczy)**

Aby uzyskać więcej informacji na temat opcji magazynu za pomocą usługi HDInsight, zobacz [Porównywanie opcji magazynu do użycia z klastrami usługi Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Korzystanie z dodatkowego konta magazynu w innej lokalizacji niż klaster HDInsight nie jest obsługiwane.

Podczas konfiguracji dla domyślnego punktu końcowego magazynu można określić kontener obiektów blob konta usługi Azure Storage lub usługi Data Lake Storage. Domyślny magazyn zawiera dzienniki aplikacji i systemu. Opcjonalnie można określić dodatkowe połączone konta usługi Azure Storage i konta usługi Data Lake Storage, do których klaster może uzyskać dostęp. Klaster HDInsight i zależne konta magazynu muszą znajdować się w tej samej lokalizacji platformy Azure.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="metastore-settings"></a>Ustawienia metasklepu

Można utworzyć opcjonalne hive lub Apache Oozie metastores. Jednak nie wszystkie typy klastrów obsługują metasklepy, a usługa Azure SQL Data Warehouse nie jest zgodna z metasklepami.

Aby uzyskać więcej informacji, zobacz [Używanie zewnętrznych magazynów metadanych w usłudze Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Podczas tworzenia niestandardowego magazynu metastore nie należy używać myślników, łączników ani spacji w nazwie bazy danych. Może to spowodować niepowodzenie procesu tworzenia klastra.

#### <a name="sql-database-for-hive"></a>Baza danych SQL dla gałęzi

Jeśli chcesz zachować tabele hive po usunięciu klastra HDInsight, użyj niestandardowego magazynu metastore. Następnie można dołączyć magazyn meta do innego klastra HDInsight.

Metasklepu HDInsight, który jest tworzony dla jednej wersji klastra HDInsight nie można udostępnić w różnych wersjach klastra HDInsight. Aby uzyskać listę wersji HDInsight, zobacz [Obsługiwane wersje HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

#### <a name="sql-database-for-oozie"></a>Baza danych SQL dla Oozie

Aby zwiększyć wydajność podczas korzystania z Oozie, użyj niestandardowego magazynu metastore. Metasklep może również zapewnić dostęp do danych zadania Oozie po usunięciu klastra.

#### <a name="sql-database-for-ambari"></a>Baza danych SQL dla Ambari

Ambari służy do monitorowania klastrów HDInsight, wprowadzania zmian konfiguracji i przechowywania informacji o zarządzaniu klastrami, a także historii zadań. Niestandardowa funkcja Ambari DB umożliwia wdrożenie nowego klastra i konfigurację Ambari w zewnętrznej bazie danych, którą zarządzasz. Aby uzyskać więcej informacji, zobacz [Niestandardowe ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Nie można ponownie użyć niestandardowego magazynu metasezonu Oozie. Aby użyć niestandardowego magazynu metasezonu Oozie, należy podać pustą usługę Azure SQL Database podczas tworzenia klastra USŁUGI HDInsight.

## <a name="security--networking"></a>Bezpieczeństwo + sieć

![hdinsight tworzenie opcji wybierz pakiet zabezpieczeń przedsiębiorstwa](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Pakiet zabezpieczeń przedsiębiorstwa

W przypadku typów klastrów Hadoop, Spark, HBase, Kafka i Interactive Query można włączyć **pakiet zabezpieczeń przedsiębiorstwa**. Ten pakiet zapewnia opcję bardziej bezpiecznej konfiguracji klastra przy użyciu Apache Ranger i integracji z usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Pakiet zabezpieczeń Enterprise umożliwia integrację funkcji HDInsight z usługą Active Directory i Apache Ranger. Przy użyciu pakietu zabezpieczeń Enterprise można utworzyć wielu użytkowników.

Aby uzyskać więcej informacji na temat tworzenia klastra HDInsight przyłączony do domeny, zobacz [Tworzenie środowiska piaskownicy HDInsight przyłączone do domeny](./domain-joined/apache-domain-joined-configure.md).

### <a name="tls"></a>TLS

Aby uzyskać więcej informacji, zobacz [Zabezpieczenia warstwy transportu](./transport-layer-security.md)

### <a name="virtual-network"></a>Sieć wirtualna

Jeśli rozwiązanie wymaga technologii, które są rozłożone na wiele typów klastra USŁUGI HDInsight, [sieć wirtualna platformy Azure](https://docs.microsoft.com/azure/virtual-network) można połączyć wymagane typy klastra. Ta konfiguracja umożliwia klastrów i każdy kod, który można wdrożyć do nich, aby bezpośrednio komunikować się ze sobą.

Aby uzyskać więcej informacji na temat korzystania z sieci wirtualnej platformy Azure z usługą HDInsight, zobacz [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md).

Na przykład przy użyciu dwóch typów klastrów w sieci wirtualnej platformy Azure zobacz [Korzystanie z apache Spark Structured Streaming z Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight z siecią wirtualną, w tym określonych wymagań konfiguracyjnych dla sieci [wirtualnej, zobacz Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Ustawienie szyfrowania dysku

Aby uzyskać więcej informacji, zobacz [Szyfrowanie dysku klucza zarządzanego przez klienta](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Serwer proxy REST Kafka

To ustawienie jest dostępne tylko dla typu klastra Platformy Kafka. Aby uzyskać więcej informacji, zobacz [Korzystanie z serwera proxy REST](./kafka/rest-proxy.md).

### <a name="identity"></a>Tożsamość

Aby uzyskać więcej informacji, zobacz [Tożsamości zarządzane w usłudze Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfiguracja + ceny

![HdInsight wybierz rozmiar węzła](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

Opłaty za użycie węzła są naliczane tak długo, jak długo istnieje klaster. Rozliczenia rozpoczynają się po utworzeniu klastra i zatrzymuje się po usunięciu klastra. Klastrów nie można de-przydzielone lub wstrzymane.

### <a name="node-configuration"></a>Konfiguracja węzła

Każdy typ klastra ma własną liczbę węzłów, terminologię dla węzłów i domyślny rozmiar maszyny Wirtualnej. W poniższej tabeli liczba węzłów dla każdego typu węzła jest w nawiasach.

| Typ | Węzły | Diagram |
| --- | --- | --- |
| Hadoop |Węzeł główny (2), węzeł roboczy (1+) |![Węzły klastra HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Serwer główny (2), serwer regionu (1+), węzeł master/ZooKeeper (3) |![Konfiguracja typu klastra HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Węzeł Nimbusa (2), serwer nadzoru (1+), węzeł ZooKeeper (3) |![Konfiguracja typu klastra burzowego usługi HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| platforma Spark |Węzeł główny (2), węzeł pracownika (1+), węzeł ZooKeeper (3) (bezpłatnie dla rozmiaru maszyny wirtualnej A1 ZooKeeper) |![Konfiguracja typu klastra iskrowego hdinsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Aby uzyskać więcej informacji, zobacz [Domyślna konfiguracja węzłów i rozmiary maszyn wirtualnych dla klastrów](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) w części "Jakie są składniki i wersje usługi Hadoop w programie HDInsight?"

Koszt klastrów HDInsight zależy od liczby węzłów i rozmiarów maszyn wirtualnych dla węzłów.

Różne typy klastrów mają różne typy węzłów, liczbę węzłów i rozmiary węzłów:
* Domyślny typ klastra Hadoop:
    * Dwa *węzły głowy*  
    * Cztery *węzły procesu roboczego*
* Domyślny typ klastra burzowego:
    * Dwa *węzły Nimbusa*
    * Trzy *węzły ZooKeeper*
    * Cztery *węzły nadzorcy*

Jeśli po prostu wypróbujesz hdinsight, zalecamy użycie jednego węzła worker. Aby uzyskać więcej informacji na temat cen HDInsight, zobacz [CENY HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Limit rozmiaru klastra różni się w zależności od subskrypcji platformy Azure. Skontaktuj się z [pomocą techniczną rozliczeń platformy Azure,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) aby zwiększyć limit.

Podczas konfigurowania klastra za pomocą portalu Azure rozmiar węzła jest dostępny za pośrednictwem karty **Konfiguracja + cennik.** W portalu można również wyświetlić koszt skojarzony z różnymi rozmiarami węzłów.

### <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych

Podczas wdrażania klastrów wybierz zasoby obliczeniowe na podstawie rozwiązania, które planujesz wdrożyć. Następujące maszyny wirtualne są używane dla klastrów HDInsight:

* Maszyny wirtualne z serii A i D1-4: [uniwersalne rozmiary maszyn wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Maszyna wirtualna z serii D11-14: [zoptymalizowane pod kątem pamięci rozmiary maszyn wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Aby dowiedzieć się, jakiej wartości należy użyć do określenia rozmiaru maszyny Wirtualnej podczas tworzenia klastra przy użyciu różnych zestawów SDK lub podczas korzystania z programu Azure PowerShell, zobacz [rozmiary maszyn wirtualnych do użycia dla klastrów HDInsight.](../cloud-services/cloud-services-sizes-specs.md#size-tables) Z tego połączonego artykułu użyj wartości w kolumnie **Rozmiar** tabel.

> [!IMPORTANT]  
> Jeśli potrzebujesz więcej niż 32 węzłów procesu roboczego w klastrze, należy wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.

Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych](../virtual-machines/windows/sizes.md). Aby uzyskać informacje na temat cen różnych rozmiarów, zobacz [HDInsight cennik](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Dodawanie aplikacji

Aplikacja usługi HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight opartym na systemie Linux. Użytkownik może korzystać z aplikacji dostarczonych przez firmę Microsoft, strony trzecie lub samodzielnie się rozwijać. Aby uzyskać więcej informacji, zobacz [Instalowanie aplikacji Apache Hadoop innych firm na platformie Azure HDInsight](hdinsight-apps-install-applications.md).

Większość aplikacji HDInsight są instalowane na pustym węźle krawędzi.  Pusty węzeł krawędzi to maszyna wirtualna systemu Linux z tymi samymi narzędziami klienckimi zainstalowanymi i skonfigurowanym, jak w węźle głównym. Węzeł brzegowy służy do uzyskiwania dostępu do klastra, testowania aplikacji klienckich i hostowania aplikacji klienckich. Aby uzyskać więcej informacji, zobacz [Używanie pustych węzłów krawędzi w aplikacji HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Akcje skryptu

Podczas tworzenia można zainstalować dodatkowe składniki lub dostosować konfigurację klastra przy użyciu skryptów. Takie skrypty są wywoływane za pomocą **akcji skryptu**, która jest opcją konfiguracji, która może być używana z portalu Azure Portal, poleceń cmdlet programu HDInsight systemu Windows PowerShell lub zestawu SDK .NET usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastra HDInsight przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Niektóre natywne składniki Java, takie jak Apache Mahout i Cascading, można uruchomić w klastrze jako pliki Java Archive (JAR). Te pliki JAR mogą być dystrybuowane do usługi Azure Storage i przesyłane do klastrów HDInsight za pomocą mechanizmów przesyłania zadań Hadoop. Aby uzyskać więcej informacji, zobacz [Programowo przesyłanie zadań Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Jeśli występują problemy z wdrażaniem plików JAR w klastrach HDInsight lub wywoływaniem plików JAR w klastrach HDInsight, skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/).
>
> Kaskadowa usługa HDInsight nie jest obsługiwana przez program HDInsight i nie kwalifikuje się do korzystania z pomocy technicznej firmy Microsoft. Aby uzyskać listę obsługiwanych składników, zobacz [Co nowego w wersjach klastra dostarczonych przez program HDInsight](hdinsight-component-versioning.md).

Czasami chcesz skonfigurować następujące pliki konfiguracyjne podczas procesu tworzenia:

* clusterIdentity.xml
* core-site.xml
* plik gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* strona ul.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* burza-site.xml
* tez-site.xml
* webhcat-site.xml
* przędza-site.xml

Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastrów HDInsight przy użyciu pliku Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z błędami tworzenia klastra za pomocą usługi Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Czym są HDInsight, ekosystem Apache Hadoop i klastry Hadoop?](hadoop/apache-hadoop-introduction.md)
* [Wprowadzenie do korzystania z Apache Hadoop w hdinsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Praca w Apache Hadoop na HDInsight z komputera z systemem Windows](hdinsight-hadoop-windows-tools.md)
