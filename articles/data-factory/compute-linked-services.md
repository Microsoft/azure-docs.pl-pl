---
title: Środowiska obliczeniowe obsługiwane przez usługę Azure Data Factory
description: Dowiedz się więcej o środowiskach obliczeniowych, których można używać w potokach usługi Azure Data Factory (takich jak Usługa Azure HDInsight) do przekształcania lub przetwarzania danych.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: 63843230b3d4a521df858b00c8e5c887e8f53a7a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415592"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Środowiska obliczeniowe obsługiwane przez usługę Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule wyjaśniono różne środowiska obliczeniowe, których można używać do przetwarzania lub przekształcania danych. Zawiera również szczegółowe informacje o różnych konfiguracjach (na żądanie i przynieść własne) obsługiwane przez usługę Data Factory podczas konfigurowania połączonych usług łączących te środowiska obliczeniowe z fabryką danych platformy Azure.

Poniższa tabela zawiera listę środowisk obliczeniowych obsługiwanych przez fabrykę danych i działania, które można na nich uruchomić. 

| Środowisko obliczeniowe                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Klaster HDInsight na żądanie](#azure-hdinsight-on-demand-linked-service) lub [własny klaster HDInsight](#azure-hdinsight-linked-service) | [Ul](transform-data-using-hadoop-hive.md), [Świnia](transform-data-using-hadoop-pig.md), [Iskra](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Niestandardowy](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio](#azure-machine-learning-studio-linked-service) | [Działania usługi Machine Learning: wykonywanie wsadowe i aktualizacja zasobów](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Potok wykonywania usługi Azure Machine Learning](transform-data-machine-learning-service.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Potok wykonywania usługi Azure Machine Learning](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Usługa Azure SQL](#azure-sql-database-linked-service), [usługa Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), sql [server](#sql-server-linked-service) | [Procedura składowana](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notatnik](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Funkcja platformy Azure](#azure-function-linked-service)         | [Działanie funkcji platformy Azure](control-flow-azure-function-activity.md)
>  

## <a name="on-demand-hdinsight-compute-environment"></a>Środowisko obliczeniowe HDInsight na żądanie

W tej konfiguracji środowiska obliczeniowego jest w pełni zarządzany przez usługę Azure Data Factory. Jest on automatycznie tworzony przez usługę Data Factory przed przesłaniem zadania do przetwarzania danych i usunięciem po zakończeniu zadania. Można utworzyć połączona usługę dla środowiska obliczeniowego na żądanie, skonfigurować ją i kontrolować szczegółowe ustawienia dla wykonywania zadań, zarządzania klastrami i akcji boottrappingu.

> [!NOTE]
> Konfiguracja na żądanie jest obecnie obsługiwana tylko dla klastrów usługi Azure HDInsight. Usługa Azure Databricks obsługuje również zadania na żądanie przy użyciu klastrów zadań, zapoznaj się z [usługą połączonej z usługą Azure databricks,](#azure-databricks-linked-service) aby uzyskać więcej informacji.

## <a name="azure-hdinsight-on-demand-linked-service"></a>Połączona usługa Azure HDInsight na żądanie

Usługa Azure Data Factory może automatycznie utworzyć klaster HDInsight na żądanie do przetwarzania danych. Klaster jest tworzony w tym samym regionie co konto magazynu (właściwość linkedServiceName w JSON) skojarzone z klastrem. Konto magazynu musi być standardowym kontem usługi Azure Storage ogólnego przeznaczenia. 

Zwróć uwagę na następujące **ważne** kwestie dotyczące usługi połączonej HDInsight na żądanie:

* Klaster HDInsight na żądanie jest tworzony w ramach subskrypcji platformy Azure. Możesz zobaczyć klaster w witrynie Azure portal, gdy klaster jest uruchomiony. 
* Dzienniki dla zadań uruchamianych w klastrze HDInsight na żądanie są kopiowane do konta magazynu skojarzonego z klastrem HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword zdefiniowane w definicji usługi połączonej są używane do logowania się do klastra w celu szczegółowego rozwiązywania problemów w cyklu życia klastra. 
* Opłata jest naliczana tylko za czas, gdy klaster HDInsight jest uruchomiony i działa zadania.
* Akcji **skryptu** z usługą azure HDInsight na żądanie połączone usługi.  

> [!IMPORTANT]
> Zazwyczaj trwa **20 minut** lub więcej do aprowizowania klastra usługi Azure HDInsight na żądanie.

### <a name="example"></a>Przykład

Następujący JSON definiuje usługi połączone HDInsight oparte na systemie Linux na żądanie. Usługa Fabryka danych automatycznie tworzy klaster HDInsight **oparty na systemie Linux** do przetwarzania wymaganej aktywności. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> Klaster HDInsight tworzy **domyślny kontener** w magazynie obiektów blob określonym w JSON (**linkedServiceName**). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. W przypadku połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest tworzony za każdym razem, gdy trzeba przetworzyć wycinek — o ile w tym momencie nie istnieje aktywny klaster (**timeToLive**) — i zostaje usunięty po zakończeniu przetwarzania. 
>
> W miarę działania jest uruchamiana większa liczba kontenerów w magazynie obiektów blob platformy Azure. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Aby usunąć kontenery z usługi Azure Blob Storage, użyj takich narzędzi, jak [Microsoft Storage Explorer](https://storageexplorer.com/).

### <a name="properties"></a>Właściwości

| Właściwość                     | Opis                              | Wymagany |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Właściwość type powinna być ustawiona na **HDInsightOnDemand**. | Tak      |
| clusterSize                  | Liczba węzłów procesu roboczego/danych w klastrze. Klaster HDInsight jest tworzony z 2 węzłami głównymi wraz z liczbą węzłów procesu roboczego określonych dla tej właściwości. Węzły mają rozmiar Standard_D3 który ma 4 rdzenie, więc klaster węzła roboczego 4 zajmuje 24 rdzenie (4\*4 = 16 rdzeni dla węzłów procesu roboczego, plus 2\*4 = 8 rdzeni dla węzłów głównych). Zobacz [Konfigurowanie klastrów w hdinsight z Hadoop, Spark, Kafka i więcej,](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) aby uzyskać szczegółowe informacje. | Tak      |
| linkedServiceName            | Usługa połączona usługi Azure Storage, która ma być używana przez klaster na żądanie do przechowywania i przetwarzania danych. Klaster HDInsight jest tworzony w tym samym regionie co to konto usługi Azure Storage. Usługa Azure HDInsight ma ograniczenia całkowitej liczby rdzeni, których możesz użyć w każdym obsługiwanym przez nią regionie platformy Azure. Upewnij się, że masz wystarczająco dużo podstawowych przydziałów w tym regionie platformy Azure, aby spełnić wymagany rozmiar clusterSize. Szczegółowe informacje można znaleźć [w obszarze Konfigurowanie klastrów w systemie HDInsight z pomocą usługi Hadoop, Spark, Kafka i innych](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Obecnie nie można utworzyć klastra HDInsight na żądanie, który używa usługi Azure Data Lake Storage (Gen 2) jako magazynu. Jeśli chcesz przechowywać dane wynikowe z przetwarzania HDInsight w usłudze Azure Data Lake Storage (Gen 2), użyj działania kopiowania, aby skopiować dane z usługi Azure Blob Storage do usługi Azure Data Lake Storage (Gen 2). </p> | Tak      |
| grupa clusterResourceGroup         | Klaster HDInsight jest tworzony w tej grupie zasobów. | Tak      |
| czasochłowe                   | Dozwolony czas bezczynności dla klastra HDInsight na żądanie. Określa, jak długo klaster HDInsight na żądanie pozostaje aktywny po zakończeniu działania uruchamianego, jeśli w klastrze nie ma żadnych innych aktywnych zadań. Minimalna dozwolona wartość to 5 minut (00:05:00).<br/><br/>Na przykład jeśli przebieg działania trwa 6 minut i timetolive jest ustawiona na 5 minut, klaster pozostaje przy życiu przez 5 minut po 6 minut przetwarzania przebiegu działania. Jeśli inne działanie jest wykonywane z 6-minutowym oknem, jest ono przetwarzane przez ten sam klaster.<br/><br/>Tworzenie klastra HDInsight na żądanie jest kosztowną operacją (może trochę potrwać), więc użyj tego ustawienia w razie potrzeby, aby poprawić wydajność fabryki danych, ponownie korzystając z klastra HDInsight na żądanie.<br/><br/>Jeśli ustawisz wartość czasową na 0, klaster zostanie usunięty zaraz po zakończeniu uruchamiania działania. Natomiast jeśli ustawisz wysoką wartość, klaster może pozostawać bezczynny, aby zalogować się w celu rozwiązywania problemów, ale może to spowodować wysokie koszty. Dlatego ważne jest, aby ustawić odpowiednią wartość w zależności od potrzeb.<br/><br/>Jeśli wartość właściwości czasowej jest odpowiednio ustawiona, wiele potoków może współużytkować wystąpienie klastra HDInsight na żądanie. | Tak      |
| typ klastra                  | Typ klastra HDInsight, który ma zostać utworzony. Dozwolone wartości to "hadoop" i "iskra". Jeśli nie określono, wartość domyślna jest hadoop. Klastra z włączonym pakietem zabezpieczeń przedsiębiorstwa nie można utworzyć na żądanie, zamiast tego użyć [istniejącego klastra/ przynieść własne obliczenia.](#azure-hdinsight-linked-service) | Nie       |
| version                      | Wersja klastra HDInsight. Jeśli nie zostanie określony, używa bieżącej domyślnej wersji zdefiniowanej przez HDInsight. | Nie       |
| hostSubscriptionId           | Identyfikator subskrypcji platformy Azure używany do tworzenia klastra HDInsight. Jeśli nie zostanie określony, używa identyfikatora subskrypcji kontekstu logowania platformy Azure. | Nie       |
| nazwa klastraPrefix           | Prefiks nazwy klastra HDI, sygnatura czasowa automatycznie dołączana na końcu nazwy klastra| Nie       |
| sparkVersion (wersja iskrowa)                 | Wersja iskry, jeśli typem klastra jest "Spark" | Nie       |
| additionalLinkedServiceNames | Określa dodatkowe konta magazynu dla usługi połączonej HDInsight, dzięki czemu usługa Data Factory może zarejestrować je w Twoim imieniu. Te konta magazynu muszą znajdować się w tym samym regionie co klaster HDInsight, który jest tworzony w tym samym regionie co konto magazynu określone przez linkedServiceName. | Nie       |
| osType                       | Typ systemu operacyjnego. Dozwolone wartości to: Linux i Windows (tylko dla HDInsight 3.3). Domyślnie jest Linux. | Nie       |
| hcatalogLinkedServiceName    | Nazwa usługi połączonej sql platformy Azure, które wskazują na bazę danych HCatalog. Klaster HDInsight na żądanie jest tworzony przy użyciu usługi Azure SQL Database jako magazynu metastore. | Nie       |
| connectVia                   | Środowisko wykonawcze integracji, które mają być używane do wysyłania działań do tej usługi połączonej HDInsight. W przypadku usługi połączonej HDInsight na żądanie obsługuje ona tylko środowisko uruchomieniowe integracji platformy Azure. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie       |
| nazwa klastraUsername                   | Nazwa użytkownika, aby uzyskać dostęp do klastra. | Nie       |
| klaster Hasło                   | Hasło w typie bezpiecznego ciągu, aby uzyskać dostęp do klastra. | Nie       |
| nazwa klastraSshUserName         | Nazwa użytkownika do SSH zdalnie łączy się z węzłem klastra (dla systemu Linux). | Nie       |
| klastrySshPassword         | Hasło typu bezpiecznego ciągu do węzła SSH zdalnie łączyć węzeł klastra (dla systemu Linux). | Nie       |
| skryptAkcje | Określ skrypt dla [dostosowań klastra HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) podczas tworzenia klastra na żądanie. <br />Obecnie narzędzie do tworzenia interfejsu użytkownika usługi Azure Data Factory obsługuje określanie tylko 1 akcji skryptu, ale można uzyskać przez to ograniczenie w JSON (określ wiele akcji skryptu w JSON). | Nie |


> [!IMPORTANT]
> Usługa HDInsight obsługuje wiele wersji klastra Hadoop, które można wdrożyć. Każdy wybór wersji tworzy określoną wersję dystrybucji Hortonworks Data Platform (HDP) i zestaw składników, które są zawarte w tej dystrybucji. Lista obsługiwanych wersji HDInsight jest aktualizowana w celu zapewnienia najnowszych składników i poprawek ekosystemu Hadoop. Upewnij się, że zawsze odnoszą się do najnowszych informacji [o obsługiwanej wersji HDInsight i typ systemu operacyjnego,](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) aby upewnić się, że używasz obsługiwanej wersji programu HDInsight. 
>
> [!IMPORTANT]
> Obecnie usługi połączone HDInsight nie obsługują HBase, Interactive Query (Hive LLAP), Storm. 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames Przykład JSON

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Usługa połączona HDInsight na żądanie wymaga uwierzytelniania głównego usługi do tworzenia klastrów HDInsight w Twoim imieniu. Aby użyć uwierzytelniania jednostki usługi, zarejestruj jednostkę aplikacji w usłudze Azure Active Directory (Azure AD) i przyznaj jej rolę **współautora** subskrypcji lub grupę zasobów, w której jest tworzony klaster HDInsight. Aby uzyskać szczegółowe kroki, zobacz [Tworzenie aplikacji i jednostki usługi Azure Active Directory, która może uzyskiwać dostęp do zasobów za pomocą portalu.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji 
- Identyfikator dzierżawy

Użyj uwierzytelniania głównego usługi, określając następujące właściwości:

| Właściwość                | Opis                              | Wymagany |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Określ identyfikator klienta aplikacji.     | Tak      |
| **servicePrincipalKey** | Określ klucz aplikacji.           | Tak      |
| **Dzierżawy**              | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak      |

### <a name="advanced-properties"></a>Właściwości zaawansowane

Można również określić następujące właściwości szczegółowej konfiguracji klastra HDInsight na żądanie.

| Właściwość               | Opis                              | Wymagany |
| :--------------------- | :--------------------------------------- | :------- |
| coreKonfiguracja      | Określa podstawowe parametry konfiguracji (jak w pliku core-site.xml) dla klastra HDInsight, który ma zostać utworzony. | Nie       |
| konfiguracja hBaseConfiguracja     | Określa parametry konfiguracji bazy danych HBase (hbase-site.xml) dla klastra HDInsight. | Nie       |
| konfiguracja hdfs      | Określa parametry konfiguracji hdfs (hdfs-site.xml) dla klastra HDInsight. | Nie       |
| konfiguracja hiveConfiguration      | Określa parametry konfiguracji gałęzi (hive-site.xml) dla klastra HDInsight. | Nie       |
| mapReduceConfiguration | Określa parametry konfiguracji MapReduce (mapred-site.xml) dla klastra HDInsight. | Nie       |
| oozieKonfiguracja     | Określa parametry konfiguracji Oozie (oozie-site.xml) dla klastra HDInsight. | Nie       |
| stormKonfiguracja     | Określa parametry konfiguracji burzy (storm-site.xml) dla klastra HDInsight. | Nie       |
| konfiguracja przędzy      | Określa parametry konfiguracji przędzy (yarn-site.xml) dla klastra HDInsight. | Nie       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Przykład — konfiguracja klastra HDInsight na żądanie z zaawansowanymi właściwościami

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
            "coreConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "hiveConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "mapReduceConfiguration": {
                "mapreduce.reduce.java.opts": "-Xmx4000m",
                "mapreduce.map.java.opts": "-Xmx4000m",
                "mapreduce.map.memory.mb": "5000",
                "mapreduce.reduce.memory.mb": "5000",
                "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
            },
            "yarnConfiguration": {
                "yarn.app.mapreduce.am.resource.mb": "5000",
                "mapreduce.map.memory.mb": "5000"
            },
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>Rozmiary węzłów
Można określić rozmiary węzłów głowy, danych i zookeeperów, korzystając z następujących właściwości: 

| Właściwość          | Opis                              | Wymagany |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Określa rozmiar węzła głównego. Wartość domyślna to: Standard_D3. Zobacz **Określanie rozmiarów węzłów** sekcji, aby uzyskać szczegółowe informacje. | Nie       |
| dataNodeSize      | Określa rozmiar węzła danych. Wartość domyślna to: Standard_D3. | Nie       |
| zookeeperNodeSize | Określa rozmiar węzła Opiekun zoo. Wartość domyślna to: Standard_D3. | Nie       |

#### <a name="specifying-node-sizes"></a>Określanie rozmiarów węzłów
Zobacz [rozmiary maszyn wirtualnych](../virtual-machines/linux/sizes.md) artykuł dla wartości ciągów, które należy określić dla właściwości wymienionych w poprzedniej sekcji. Wartości muszą być zgodne z **CMDLETs & APIS,** o których mowa w artykule. Jak widać w artykule, węzeł danych duży (domyślny) rozmiar ma 7 GB pamięci, która może nie być wystarczająco dobre dla scenariusza. 

Jeśli chcesz utworzyć węzły głowy o rozmiarze D4 i węzły robocze, określ **Standard_D4** jako wartość właściwości headNodeSize i dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Jeśli określisz niewłaściwą wartość dla tych właściwości, może pojawić się następujący **błąd:** Nie można utworzyć klastra. Wyjątek: nie można ukończyć operacji tworzenia klastra. Operacja zakończona niepowodzeniem z kodem „400”. Końcowy stan klastra: „Błąd”. Komunikat: "PreClusterCreationValidationFailure". Po wyświetleniu tego błędu upewnij się, że używasz **polecenia CMDLET &** nazwę APIS z tabeli w [artykule Rozmiary maszyn wirtualnych.](../virtual-machines/linux/sizes.md)        

## <a name="bring-your-own-compute-environment"></a>Przynieś własne środowisko obliczeniowe
W tej konfiguracji użytkownicy mogą zarejestrować już istniejące środowisko obliczeniowe jako połączone usługi w fabryce danych. Środowisko obliczeniowe jest zarządzane przez użytkownika, a usługa Data Factory używa go do wykonywania działań.

Ten typ konfiguracji jest obsługiwany dla następujących środowisk obliczeniowych:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Usługa połączona usługi Azure HDInsight
Można utworzyć usługę połączona usługi Azure HDInsight, aby zarejestrować własny klaster HDInsight w usłudze Data Factory.

### <a name="example"></a>Przykład

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Właściwości
| Właściwość          | Opis                                                  | Wymagany |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | Właściwość typu powinna być ustawiona na **HDInsight**.            | Tak      |
| clusterUri        | Identyfikator URI klastra HDInsight.                            | Tak      |
| nazwa użytkownika          | Określ nazwę użytkownika, który ma być używany do łączenia się z istniejącym klastrem HDInsight. | Tak      |
| hasło          | Określ hasło dla konta użytkownika.                       | Tak      |
| linkedServiceName | Nazwa usługi połączonej usługi Azure Storage, która odwołuje się do magazynu obiektów blob platformy Azure używanego przez klaster HDInsight. <p>Obecnie nie można określić usługi połączonej usługi Usługi Azure Data Lake Storage (Gen 2) dla tej właściwości. Jeśli klaster HDInsight ma dostęp do magazynu Usługi Data Lake, można uzyskać dostęp do danych w usłudze Azure Data Lake Storage (Gen 2) ze skryptów Hive/Pig. </p> | Tak      |
| isEspEnabled      | Określ '*true*', jeśli klaster HDInsight jest włączony [pakiet zabezpieczeń](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-architecture) przedsiębiorstwa. Wartość domyślna to '*false*'. | Nie       |
| connectVia        | Środowisko wykonawcze integracji, które mają być używane do wysyłania działań do tej połączonej usługi. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. <br />W przypadku klastra HDInsight z włączoną funkcją pakietu zabezpieczeń przedsiębiorstwa (ESP) użyj środowiska uruchomieniowego integracji hostowanego samodzielnie, które ma zasięg wzroku klastra lub powinno zostać wdrożone w tej samej sieci wirtualnej co klaster ESP HDInsight. | Nie       |

> [!IMPORTANT]
> Usługa HDInsight obsługuje wiele wersji klastra Hadoop, które można wdrożyć. Każdy wybór wersji tworzy określoną wersję dystrybucji Hortonworks Data Platform (HDP) i zestaw składników, które są zawarte w tej dystrybucji. Lista obsługiwanych wersji HDInsight jest aktualizowana w celu zapewnienia najnowszych składników i poprawek ekosystemu Hadoop. Upewnij się, że zawsze odnoszą się do najnowszych informacji [o obsługiwanej wersji HDInsight i typ systemu operacyjnego,](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) aby upewnić się, że używasz obsługiwanej wersji programu HDInsight. 
>
> [!IMPORTANT]
> Obecnie usługi połączone HDInsight nie obsługują HBase, Interactive Query (Hive LLAP), Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Usługa połączona z usługą Azure Batch

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można utworzyć usługę linked Azure Batch, aby zarejestrować pulę partii maszyn wirtualnych (VM) w fabryce danych. Działanie niestandardowe można uruchomić przy użyciu usługi Azure Batch.

Zobacz następujące artykuły, jeśli jesteś nowy w usłudze Azure Batch:

* [Podstawowe informacje o usłudze Azure Batch](../batch/batch-technical-overview.md) dla przeglądu usługi Azure Batch.
* Polecenie cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) do utworzenia konta usługi Azure Batch (lub) [witryny Azure Portal](../batch/batch-account-create-portal.md) w celu utworzenia konta usługi Azure Batch przy użyciu witryny Azure portal. Zobacz [korzystanie z programu PowerShell do zarządzania kontem wsadowym platformy Azure](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) artykuł szczegółowe instrukcje dotyczące korzystania z polecenia cmdlet.
* Polecenie cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) do utworzenia puli usługi Azure Batch.

### <a name="example"></a>Przykład

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Właściwości
| Właściwość          | Opis                              | Wymagany |
| ----------------- | ---------------------------------------- | -------- |
| type              | Właściwość typu powinna być ustawiona na **AzureBatch**. | Tak      |
| accountName       | Nazwa konta usługi Azure Batch.         | Tak      |
| Accesskey         | Klucz dostępu dla konta usługi Azure Batch.  | Tak      |
| batchUri ( batchUri )          | Adres URL do konta usługi Azure Batch w formacie https://*batchaccountname.region*.batch.azure.com. | Tak      |
| nazwa puli          | Nazwa puli maszyn wirtualnych.    | Tak      |
| linkedServiceName | Nazwa połączonej usługi Usługi Azure Storage skojarzonej z tą usługą linked Azure Batch. Ta połączona usługa jest używana do plików przemieszczania wymaganych do uruchomienia działania. | Tak      |
| connectVia        | Środowisko wykonawcze integracji, które mają być używane do wysyłania działań do tej połączonej usługi. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie       |

## <a name="azure-machine-learning-studio-linked-service"></a>Usługa połączona usługi Azure Machine Learning Studio
Utwórz usługę połączony usługi Azure Machine Learning Studio, aby zarejestrować punkt końcowy oceniania partii uczenia maszynowego w fabryce danych.

### <a name="example"></a>Przykład

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Właściwości
| Właściwość               | Opis                              | Wymagany                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Właściwość typu powinna być ustawiona na: **AzureML**. | Tak                                      |
| mlPunkt             | Adres URL oceniania partii.                   | Tak                                      |
| apiKey (klawisz apiKey)                 | Interfejs API opublikowanego modelu obszaru roboczego.     | Tak                                      |
| updateResourceEndpoint | Adres URL zasobu aktualizacji dla punktu końcowego usługi azure machine learning usługi sieci Web używanej do aktualizowania predykcyjnej usługi sieci Web za pomocą uczonego pliku modelu | Nie                                       |
| servicePrincipalId     | Określ identyfikator klienta aplikacji.     | Wymagane, jeśli określono updateResourceEndpoint |
| servicePrincipalKey    | Określ klucz aplikacji.           | Wymagane, jeśli określono updateResourceEndpoint |
| Dzierżawy                 | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Wymagane, jeśli określono updateResourceEndpoint |
| connectVia             | Środowisko wykonawcze integracji, które mają być używane do wysyłania działań do tej połączonej usługi. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie                                       |

## <a name="azure-machine-learning-linked-service"></a>Usługa połączona usługi usługi Azure Machine Learning
Utwórz usługę połączony usługi Azure Machine Learning, aby połączyć obszar roboczy usługi Azure Machine Learning z fabryką danych.

> [!NOTE]
> Obecnie tylko uwierzytelnianie jednostkowe usługi jest obsługiwane dla usługi połączonej usługi Azure Machine Learning.

### <a name="example"></a>Przykład

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Właściwości
| Właściwość               | Opis                              | Wymagany                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Właściwość typu powinna być ustawiona na: **AzureMLService**. | Tak                                      |
| subscriptionId         | Identyfikator subskrypcji platformy Azure              | Tak                                      |
| resourceGroupName      | name | Tak                                      |
| mlPracaname        | Nazwa obszaru roboczego usługi Azure Machine Learning | Tak  |
| servicePrincipalId     | Określ identyfikator klienta aplikacji.     | Nie |
| servicePrincipalKey    | Określ klucz aplikacji.           | Nie |
| Dzierżawy                 | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Wymagane, jeśli określono updateResourceEndpoint | Nie |
| connectVia             | Środowisko wykonawcze integracji, które mają być używane do wysyłania działań do tej połączonej usługi. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie |    

## <a name="azure-data-lake-analytics-linked-service"></a>Usługa połączona z usługą Azure Data Lake Analytics
Utwórz usługę połączony **usługi Usługi Azure Data Lake Analytics,** aby połączyć usługę obliczeniową usługi Azure Data Lake Analytics z fabryką danych platformy Azure. Działanie U-SQL usługi Data Lake Analytics w potoku odnosi się do tej połączonej usługi. 

### <a name="example"></a>Przykład

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Właściwości

| Właściwość             | Opis                              | Wymagany                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | Właściwość typu powinna być ustawiona na: **AzureDataLakeAnalytics**. | Tak                                      |
| accountName          | Nazwa konta usługi Azure Data Lake Analytics.  | Tak                                      |
| dataLakeAnalyticsUri | Identyfikator URI usługi Azure Data Lake Analytics.           | Nie                                       |
| subscriptionId       | Identyfikator subskrypcji platformy Azure                    | Nie                                       |
| resourceGroupName    | Nazwa grupy zasobów platformy Azure                | Nie                                       |
| servicePrincipalId   | Określ identyfikator klienta aplikacji.     | Tak                                      |
| servicePrincipalKey  | Określ klucz aplikacji.           | Tak                                      |
| Dzierżawy               | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak                                      |
| connectVia           | Środowisko wykonawcze integracji, które mają być używane do wysyłania działań do tej połączonej usługi. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie                                       |



## <a name="azure-databricks-linked-service"></a>Usługa połączona z usługą Azure Databricks
Można utworzyć **usługę azure databricks połączone** do rejestrowania obszaru roboczego Databricks, który służy do uruchamiania obciążeń Databricks(notebook, jar, python). 
> [!IMPORTANT]
> Usługi połączone z databricks obsługują [pule wystąpień](https://aka.ms/instance-pools). 

### <a name="example---using-new-job-cluster-in-databricks"></a>Przykład — używanie nowego klastra zadań w databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Przykład — używanie istniejącego klastra interaktywnego w databricks

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Właściwości

| Właściwość             | Opis                              | Wymagany                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | Nazwa usługi połączonej               | Tak   |
| type                 | Właściwość typu powinna być ustawiona na: **Azure Databricks**. | Tak                                      |
| domena               | Określ region platformy Azure odpowiednio na podstawie regionu obszaru roboczego Databricks. Przykład: https://eastus.azuredatabricks.net | Tak                                 |
| accessToken (dostępToken)          | Token dostępu jest wymagany do uwierzytelniania usługi Data Factory w usłudze Azure Databricks. Token dostępu musi zostać wygenerowany z obszaru roboczego databricks. Bardziej szczegółowe kroki, aby znaleźć token dostępu można znaleźć [tutaj](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | Tak                                       |
| existingClusterId    | Identyfikator klastra istniejącego klastra do uruchomienia wszystkich zadań w tej sprawie. Powinien to być już utworzony klaster interaktywny. Może być konieczne ręczne ponowne uruchomienie klastra, jeśli przestanie odpowiadać. Databricks sugerują uruchamianie zadań w nowych klastrach dla większej niezawodności. Identyfikator klastra klastra interaktywnego w obszarze roboczym Databricks -> klastry -> interaktywna nazwa klastra -> tagi konfiguracji >. [Więcej szczegółów](https://docs.databricks.com/user-guide/clusters/tags.html) | Nie 
| wystąpieniePoolId    | Identyfikator puli wystąpień istniejącej puli w obszarze roboczym databricks.  | Nie  |
| newClusterVersion    | Wersja platformy Spark klastra. Tworzy klaster zadań w databricks. | Nie  |
| newClusterNumOfWorker| Liczba węzłów procesu roboczego, które powinien mieć ten klaster. Klaster ma jeden sterownik platformy Spark i num_workers executory w sumie num_workers + 1 węzły Spark. Ciąg sformatowany Int32, jak "1" oznacza numOfWorker jest 1 lub "1:10" oznacza skalowanie automatyczne od 1 jako min i 10 jako max.  | Nie                |
| newClusterNodeType   | To pole koduje, za pomocą jednej wartości, zasoby dostępne dla każdego z węzłów Platformy Spark w tym klastrze. Na przykład węzły platformy Spark mogą być aprowizowane i zoptymalizowane pod kątem obciążeń intensywnie korzystających z pamięci lub obliczeń. To pole jest wymagane dla nowego klastra                | Nie               |
| nowyClusterSparkConf  | zestaw opcjonalnych par klucza-wartości konfiguracji spark określonej przez użytkownika. Użytkownicy mogą również przekazać w ciągu dodatkowych opcji JVM do sterownika i executors za pośrednictwem spark.driver.extraJavaOptions i spark.executor.extraJavaOptions odpowiednio. | Nie  |
| newClusterInitScripts| zestaw opcjonalnych, zdefiniowanych przez użytkownika skryptów inicjowania dla nowego klastra. Określanie ścieżki DBFS do skryptów init. | Nie  |


## <a name="azure-sql-database-linked-service"></a>Połączona usługa Azure SQL Database

Utwórz usługę połączony sql platformy Azure i używać go z [działaniem procedury składowanej](transform-data-using-stored-procedure.md) do wywołania procedury składowanej z potoku fabryki danych. Zobacz artykuł [programu Azure SQL Connector, aby](connector-azure-sql-database.md#linked-service-properties) uzyskać szczegółowe informacje na temat tej połączonej usługi.

## <a name="azure-sql-data-warehouse-linked-service"></a>Usługa połączona z usługą Azure SQL Data Warehouse

Utwórz usługę linked Azure SQL Data Warehouse i używasz jej z [działaniem procedury składowanej](transform-data-using-stored-procedure.md) do wywoływania procedury składowanej z potoku fabryki danych. Zobacz [artykuł łącznika usługi Azure SQL Data Warehouse, aby](connector-azure-sql-data-warehouse.md#linked-service-properties) uzyskać szczegółowe informacje na temat tej połączonej usługi.

## <a name="sql-server-linked-service"></a>Usługa połączona z programem SQL Server

Utworzysz usługę połączony z programem SQL Server i użyj jej z [działaniem procedury składowanej](transform-data-using-stored-procedure.md) do wywołania procedury składowanej z potoku fabryki danych. Zobacz artykuł [łącznika programu SQL Server,](connector-sql-server.md#linked-service-properties) aby uzyskać szczegółowe informacje na temat tej połączonej usługi.

## <a name="azure-function-linked-service"></a>Usługa połączona z funkcją platformy Azure

Utwórz usługę połączoną z funkcją platformy Azure i użyj jej z [działaniem funkcji platformy Azure](control-flow-azure-function-activity.md) do uruchamiania funkcji platformy Azure w potoku usługi Data Factory. Typ zwracany funkcji platformy Azure musi `JObject`być prawidłowy. (Należy pamiętać, że [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *nie* jest `JObject`.) Każdy typ zwracany inny niż `JObject` kończy się niepowodzeniem i wywołuje błąd użytkownika Zawartość odpowiedzi nie jest *prawidłowym JObject*.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| type   | Właściwość typu musi być ustawiona na: **AzureFunction** | tak |
| url aplikacji funkcji | Adres URL aplikacji funkcji platformy Azure. Format `https://<accountname>.azurewebsites.net`jest . Ten adres URL jest wartością w sekcji **adres URL** podczas wyświetlania aplikacji funkcji w witrynie Azure portal  | tak |
| klawisz funkcyjny | Klucz dostępu dla funkcji platformy Azure. Kliknij sekcję **Zarządzaj** dla danej funkcji i skopiuj **klucz funkcyjny** lub **klucz hosta**. Dowiedz się więcej tutaj: [Wyzwalacze i powiązania HTTP usługi Azure Functions HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | tak |
|   |   |   |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę działań transformacji obsługiwanych przez usługę Azure Data Factory, zobacz [Przekształcanie danych](transform-data.md).
