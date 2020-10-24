---
title: Porady dotyczące korzystania z usługi Hadoop w usłudze HDInsight opartej na systemie Linux — Azure
description: Uzyskaj porady dotyczące implementacji dotyczące korzystania z klastrów usługi HDInsight opartych na systemie Linux w znanym środowisku systemu Linux działającym w chmurze platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 1b3c694b4d6134f30d04ba8bafee9a6ffabdd959
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488116"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informacje dotyczące korzystania z usługi HDInsight w systemie Linux

Klastry usługi Azure HDInsight zapewniają Apache Hadoop w znanym środowisku systemu Linux uruchomionym w chmurze platformy Azure. Większość rzeczy powinna pracować dokładnie z każdą inną instalacją usługi Hadoop w systemie Linux. Ten dokument wywołuje konkretne różnice, z którymi należy się zapoznać.

## <a name="prerequisites"></a>Wymagania wstępne

Wiele kroków opisanych w tym dokumencie korzysta z następujących narzędzi, które mogą wymagać zainstalowania w systemie.

* [zwinięcie](https://curl.haxx.se/) — używany do komunikowania się z usługami sieci Web.
* **JQ**, procesor JSON w wierszu polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) — służy do zdalnego zarządzania usługami platformy Azure.
* **Klient SSH**. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Użytkownicy

Jeśli [przyłączony do domeny](./domain-joined/hdinsight-security-overview.md), Usługa HDInsight powinna być traktowana jako system **pojedynczego użytkownika** . Z klastrem zostanie utworzone pojedyncze konto użytkownika SSH z uprawnieniami na poziomie administratora. Można utworzyć dodatkowe konta SSH, ale mają one również dostęp administratora do klastra.

Przyłączony do domeny Usługa HDInsight obsługuje wielu użytkowników i bardziej szczegółowe uprawnienia i ustawienia roli. Aby uzyskać więcej informacji, zobacz [Zarządzanie przyłączonymi do domeny klastrami usługi HDInsight](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Nazwy domen

W pełni kwalifikowana nazwa domeny (FQDN) do użycia podczas nawiązywania połączenia z klastrem z Internetu jest `CLUSTERNAME.azurehdinsight.net` lub `CLUSTERNAME-ssh.azurehdinsight.net` (tylko w przypadku protokołu SSH).

Wewnętrznie każdy węzeł w klastrze ma nazwę, która jest przypisana podczas konfiguracji klastra. Aby znaleźć nazwy klastrów, zobacz stronę **hosty** w interfejsie użytkownika sieci Web Ambari. Aby zwrócić listę hostów z interfejsu API REST Ambari, można również użyć następujących elementów:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'
```

Zastąp ciąg `CLUSTERNAME` nazwą klastra. Po wyświetleniu monitu wprowadź hasło dla konta administratora. To polecenie zwraca dokument JSON zawierający listę hostów w klastrze. [JQ](https://stedolan.github.io/jq/) służy do wyodrębniania `host_name` wartości elementu dla każdego hosta.

Jeśli konieczne jest znalezienie nazwy węzła dla określonej usługi, można zbadać Ambari dla tego składnika. Na przykład aby znaleźć hosty dla węzła nazwa systemu plików HDFS, użyj następującego polecenia:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'
```

To polecenie zwraca dokument JSON opisujący usługę, a następnie [JQ](https://stedolan.github.io/jq/) pobiera tylko `host_name` wartość dla hostów.

## <a name="remote-access-to-services"></a>Dostęp zdalny do usług

* **Ambari (sieć Web)** - `https://CLUSTERNAME.azurehdinsight.net`

    Uwierzytelnij się przy użyciu użytkownika i hasła administratora klastra, a następnie zaloguj się do usługi Ambari.

    Uwierzytelnianie jest zwykłym tekstem — zawsze używaj protokołu HTTPS, aby upewnić się, że połączenie jest bezpieczne.

    > [!IMPORTANT]  
    > Niektóre z interfejsów użytkownika sieci Web dostępne za pośrednictwem węzłów dostępu Ambari przy użyciu nazwy domeny wewnętrznej. Nazwy domen wewnętrznych nie są publicznie dostępne przez Internet. Podczas próby dostępu do niektórych funkcji za pośrednictwem Internetu może zostać wyświetlony komunikat o błędzie "nie znaleziono serwera".
    >
    > Aby użyć pełnej funkcjonalności interfejsu użytkownika sieci Web Ambari, użyj tunelu SSH do ruchu internetowego serwera proxy do węzła głównego klastra. Zobacz [Używanie tunelowania SSH do uzyskiwania dostępu do interfejsu użytkownika sieci Web Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie i innych witryn sieci Web interfejsów użytkownika](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Uwierzytelnianie przy użyciu użytkownika i hasła administratora klastra.
    >
    > Uwierzytelnianie jest zwykłym tekstem — zawsze używaj protokołu HTTPS, aby upewnić się, że połączenie jest bezpieczne.

* **WebHCat (Templeton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Uwierzytelnianie przy użyciu użytkownika i hasła administratora klastra.
    >
    > Uwierzytelnianie jest zwykłym tekstem — zawsze używaj protokołu HTTPS, aby upewnić się, że połączenie jest bezpieczne.

* **SSH** -CLUSTERNAME-SSH.azurehdinsight.NET na porcie 22 lub 23. Port 22 jest używany do nawiązywania połączenia z podstawowym węzła głównego, podczas gdy 23 jest używany do nawiązywania połączenia z serwerem pomocniczym. Aby uzyskać więcej informacji na temat węzłów głównych, zobacz [dostępność i niezawodność klastrów Apache Hadoop w usłudze HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Dostęp do węzłów głównych klastra można uzyskać tylko za pośrednictwem protokołu SSH z komputera klienckiego. Po nawiązaniu połączenia można uzyskać dostęp do węzłów procesu roboczego przy użyciu protokołu SSH z węzła głównego.

Aby uzyskać więcej informacji, zobacz [porty używane przez Apache Hadoop Services w usłudze HDInsight](hdinsight-hadoop-port-settings-for-services.md) .

## <a name="file-locations"></a>Lokalizacje plików

Pliki związane z usługą Hadoop znajdują się w węzłach klastra pod adresem `/usr/hdp` . Ten katalog zawiera następujące podkatalogi:

* **2.6.5.3009-43**: Nazwa katalogu jest wersją platformy Hadoop używanej przez usługi HDInsight. Liczba w klastrze może być różna od wymienionej w tym miejscu.
* **bieżący**: ten katalog zawiera linki do podkatalogów w katalogu **2.6.5.3009-43** . Ten katalog istnieje, aby nie trzeba było pamiętać numeru wersji.

Przykładowe dane i pliki JAR można znaleźć w rozproszony system plików Hadoop w systemach `/example` i `/HdiSamples` .

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage i Data Lake Storage

W większości dystrybucji usługi Hadoop dane są przechowywane w systemie plików HDFS. System plików HDFS jest objęty magazynem lokalnym na maszynach w klastrze. Korzystanie z magazynu lokalnego może być kosztowne dla rozwiązania opartego na chmurze, w którym opłata jest naliczana co godzinę lub za minutę w przypadku zasobów obliczeniowych.

W przypadku korzystania z usługi HDInsight pliki danych są przechowywane w sposób dostosowywalny i odporny w chmurze przy użyciu usług Azure Blob Storage i opcjonalnie Azure Data Lake Storage Gen1/Gen2. Te usługi zapewniają następujące korzyści:

* Tanie przechowywanie długoterminowe.
* Ułatwienia dostępu z usług zewnętrznych, takich jak witryny sieci Web, narzędzia do przekazywania/pobierania plików, różne zestawy SDK języka i przeglądarki sieci Web.
* Duża pojemność pliku i duża możliwość dostosowania magazynu.

Aby uzyskać więcej informacji, zobacz [Azure Blob Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)lub [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

W przypadku korzystania z usługi Azure Blob Storage lub Data Lake Storage Gen1/Gen2 nie trzeba wykonywać żadnych specjalnych działań w usłudze HDInsight, aby uzyskiwać dostęp do danych. Na przykład następujące polecenie wyświetla listę plików w folderze, niezależnie od tego, `/example/data` czy są one przechowywane w usłudze Azure Storage, czy Data Lake Storage:

```console
hdfs dfs -ls /example/data
```

W usłudze HDInsight zasoby magazynu danych (Azure Blob Storage i Azure Data Lake Storage) są rozłączone od zasobów obliczeniowych. Można utworzyć klastry usługi HDInsight w celu wykonywania obliczeń w miarę potrzeb, a następnie usunąć klaster po zakończeniu pracy. W ten sposób pliki danych są bezpiecznie przechowywane w magazynie w chmurze, o ile jest to konieczne.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>Identyfikator URI i schemat

Niektóre polecenia mogą wymagać określenia schematu jako części identyfikatora URI podczas uzyskiwania dostępu do pliku. Na przykład składnik burzy HDFS wymaga określenia schematu. W przypadku korzystania z magazynu innego niż domyślny (Magazyn dodany jako magazyn "dodatkowy" do klastra) należy zawsze używać schematu jako części identyfikatora URI.

Korzystając z [**usługi Azure Storage**](./hdinsight-hadoop-use-blob-storage.md), należy użyć jednego z następujących schematów URI:

* `wasb:///`: Dostęp do magazynu domyślnego przy użyciu nieszyfrowanej komunikacji.

* `wasbs:///`: Dostęp do magazynu domyślnego przy użyciu komunikacji szyfrowanej.  Schemat wasbs jest obsługiwany tylko w usłudze HDInsight w wersji 3,6 lub nowszej.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Używane podczas komunikacji z kontem magazynu innym niż domyślne. Na przykład jeśli masz dodatkowe konto magazynu lub dostęp do danych przechowywanych na publicznie dostępnym koncie magazynu.

W przypadku korzystania z [**Azure Data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)Użyj następującego schematu URI:

* `abfs://`: Dostęp do magazynu domyślnego przy użyciu komunikacji szyfrowanej.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Używane podczas komunikacji z kontem magazynu innym niż domyślne. Na przykład jeśli masz dodatkowe konto magazynu lub dostęp do danych przechowywanych na publicznie dostępnym koncie magazynu.

Korzystając z [**Azure Data Lake Storage Gen1**](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md), użyj jednego z następujących schematów URI:

* `adl:///`: Dostęp do Data Lake Storage domyślnego klastra.

* `adl://<storage-name>.azuredatalakestore.net/`: Używane podczas komunikowania się z Data Lake Storageem innym niż domyślny. Służy również do uzyskiwania dostępu do danych poza katalogiem głównym klastra usługi HDInsight.

> [!IMPORTANT]  
> W przypadku używania Data Lake Storage jako domyślnego magazynu dla usługi HDInsight należy określić ścieżkę w sklepie, która ma być używana jako katalog główny magazynu usługi HDInsight. Ścieżka domyślna to `/clusters/<cluster-name>/` .
>
> W przypadku korzystania z `/` `adl:///` programu lub do uzyskiwania dostępu do danych można uzyskać dostęp tylko do danych przechowywanych w katalogu głównym (na przykład `/clusters/<cluster-name>/` ) klastra. Aby uzyskać dostęp do danych w dowolnym miejscu w sklepie, użyj `adl://<storage-name>.azuredatalakestore.net/` formatu.

### <a name="what-storage-is-the-cluster-using"></a>Magazyn używany przez klaster

Możesz użyć Ambari, aby pobrać domyślną konfigurację magazynu dla klastra. Użyj poniższego polecenia, aby pobrać informacje o konfiguracji systemu plików HDFS przy użyciu narzędzia zwinięcie i przefiltrować je przy użyciu [JQ](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> To polecenie zwraca pierwszą konfigurację zastosowana do serwera ( `service_config_version=1` ), która zawiera te informacje. Może być konieczne wyświetlenie listy wszystkich wersji konfiguracji, aby znaleźć najnowsze.

To polecenie zwraca wartość podobną do następujących identyfikatorów URI:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` w przypadku korzystania z konta usługi Azure Storage.

    Nazwa konta jest nazwą konta usługi Azure Storage. Nazwa kontenera jest kontenerem obiektów blob, który jest katalogiem głównym magazynu klastra.

* `adl://home` w przypadku korzystania z Azure Data Lake Storage. Aby uzyskać nazwę Data Lake Storage, użyj następującego wywołania REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    To polecenie zwraca następującą nazwę hosta: `<data-lake-store-account-name>.azuredatalakestore.net` .

    Aby uzyskać katalog znajdujący się w magazynie, który jest katalogiem głównym usługi HDInsight, użyj następującego wywołania REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    To polecenie zwraca ścieżkę podobną do następującej: `/clusters/<hdinsight-cluster-name>/` .

Informacje o magazynie można także znaleźć za pomocą Azure Portal, wykonując następujące czynności:

1. Na [Azure Portal](https://portal.azure.com/)wybierz klaster usługi HDInsight.

2. W sekcji **Właściwości** wybierz pozycję **konta magazynu**. Zostanie wyświetlona informacja o magazynie dla klastra.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Jak mogę dostęp do plików spoza usługi HDInsight

Istnieją różne sposoby uzyskiwania dostępu do danych spoza klastra usługi HDInsight. Poniżej przedstawiono kilka linków do narzędzi i zestawów SDK, których można użyć do pracy z danymi:

Jeśli korzystasz z __usługi Azure Blob Storage__, Skorzystaj z poniższych linków, aby uzyskać dostęp do danych:

* Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2): Command-Line poleceń interfejsu do pracy z platformą Azure. Po zainstalowaniu programu Użyj `az storage` polecenia, aby uzyskać pomoc dotyczącą korzystania z magazynu, lub `az storage blob` dla poleceń specyficznych dla obiektu BLOB.
* [blobxfer.py](https://github.com/Azure/blobxfer): skrypt języka Python służący do pracy z obiektami BLOB w usłudze Azure Storage.
* Różne zestawy SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Interfejs API REST magazynu](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Jeśli używasz __Azure Data Lake Storage Gen1__, zobacz następujące linki, aby poznać sposoby dostępu do danych:

* [Przeglądarka sieci Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [Program PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [Interfejs API REST usługi WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Narzędzia Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Skalowanie klastra

Funkcja skalowania klastra umożliwia dynamiczną zmianę liczby węzłów danych używanych przez klaster. Można wykonywać operacje skalowania, podczas gdy inne zadania lub procesy działają w klastrze.  Zobacz [skalowanie klastrów usługi HDInsight](./hdinsight-scaling-best-practices.md)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Jak mogę instalacji odcienia (lub innego składnika usługi Hadoop)?

HDInsight jest usługą zarządzaną. Jeśli platforma Azure wykryje problem z klastrem, może usunąć węzeł niepowodzenie i utworzyć węzeł, aby go zastąpić. W przypadku ręcznej instalacji elementów w klastrze nie są one utrwalane po wystąpieniu tej operacji. Zamiast tego należy użyć [akcji skryptu usługi HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Akcja skryptu może służyć do wprowadzania następujących zmian:

* Zainstaluj i skonfiguruj usługę lub witrynę sieci Web.
* Zainstaluj i Skonfiguruj składnik, który wymaga zmian konfiguracji w wielu węzłach w klastrze.

Akcje skryptu są skryptami bash. Skrypty są uruchamiane podczas tworzenia klastra i służą do instalowania i konfigurowania dodatkowych składników. Informacje na temat tworzenia własnych akcji skryptu można znaleźć w temacie [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Tworzenie akcji skryptu za pomocą usługi HDInsight).

### <a name="jar-files"></a>Pliki JAR

Niektóre technologie Hadoop zapewniają własne pliki jar. Te pliki zawierają funkcje używane jako część zadania MapReduce lub z wewnątrz świni lub Hive. Często nie wymagają one żadnej konfiguracji i mogą być przekazywane do klastra po utworzeniu i użyciu bezpośrednio. Jeśli chcesz upewnić się, że składnik zapełnił odtwarzanie klastra, Zapisz plik JAR w domyślnym magazynie klastra.

Jeśli na przykład chcesz użyć najnowszej wersji programu [Apache korzystanie funkcji datafu](https://datafu.incubator.apache.org/), możesz pobrać plik JAR zawierający projekt i przekazać go do klastra usługi HDInsight. Następnie postępuj zgodnie z dokumentacją korzystanie funkcji datafu, jak z niej korzystać z trzody chlewnej lub Hive.

> [!IMPORTANT]  
> Niektóre składniki, które są autonomicznym plikami jar, są dostarczane z usługą HDInsight, ale nie znajdują się w ścieżce. Jeśli szukasz określonego składnika, możesz użyć poniższego polecenia, aby wyszukać je w klastrze:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> To polecenie zwraca ścieżkę do dowolnych pasujących plików jar.

Aby użyć innej wersji składnika, Przekaż potrzebną wersję i użyj jej do zadań.

> [!IMPORTANT]
> Składniki dostarczane z klastrem usługi HDInsight są w pełni obsługiwane i pomoc techniczna firmy Microsoft ułatwiają izolowanie i rozwiązywanie problemów związanych z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, która ułatwia dalsze Rozwiązywanie problemu. Może to skutkować rozwiązaniem problemu lub zapytaniem o zaangażowanie dostępnych kanałów dla technologii open source, w których znajduje się Szczegółowa wiedza dla tej technologii. Na przykład istnieje wiele witryn społeczności, których można użyć, takich jak: [Microsoft Q&stronie pytania dotyczącej usługi HDInsight](https://docs.microsoft.com/answers/topics/azure-hdinsight.html) [https://stackoverflow.com](https://stackoverflow.com) . Również projekty Apache mają witryny projektu [https://apache.org](https://apache.org) , na przykład: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu API REST usługi Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)
