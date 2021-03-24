---
title: Środowiska obliczeniowe obsługiwane przez Azure Data Factory
description: Środowiska obliczeniowe, które mogą być używane z potokami Azure Data Factory (na przykład Azure HDInsight) do przekształcania lub przetwarzania danych.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 05/08/2019
ms.openlocfilehash: 71e02ea1265a81da7dd2e85549f6d1390a46311a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952239"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Środowiska obliczeniowe obsługiwane przez Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano różne środowiska obliczeniowe, za pomocą których można przetwarzać lub przekształcać dane. Zawiera on także szczegółowe informacje o różnych konfiguracjach (na żądanie a), które są obsługiwane przez Data Factory podczas konfigurowania połączonych usług łączących te środowiska obliczeniowe z fabryką danych Azure.

Poniższa tabela zawiera listę środowisk obliczeniowych obsługiwanych przez Data Factory oraz działania, które można na nich uruchamiać. 

| Środowisko obliczeniowe                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Klaster HDInsight na żądanie](#azure-hdinsight-on-demand-linked-service) lub [własny klaster usługi HDInsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [świnie](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), usługa [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Usługa Azure Batch](#azure-batch-linked-service)                   | [Niestandardowe](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio (klasyczny)](#azure-machine-learning-studio-classic-linked-service) | [Działania Machine Learning Studio (klasyczne): wykonywanie wsadowe i aktualizowanie zasobu](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning wykonywania potoku](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Procedura składowana](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notes](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Funkcja platformy Azure](#azure-function-linked-service)         | [Działanie funkcji platformy Azure](control-flow-azure-function-activity.md)
>  

## <a name="hdinsight-compute-environment"></a>Środowisko obliczeniowe usługi HDInsight

Zapoznaj się z poniższą tabelą, aby uzyskać szczegółowe informacje na temat obsługiwanych typów połączonych usług magazynu na potrzeby konfiguracji na żądanie i BYOC (Przenieś własne dane obliczeniowe).

| W połączonej usłudze COMPUTE | Nazwa właściwości                | Opis                                                  | Obiekt blob | ADLS Gen2 | Azure SQL DB | Usługa ADLS Gen 1 |
| ------------------------- | ---------------------------- | ------------------------------------------------------------ | ---- | --------- | ------------ | ---------- |
| Na żądanie                 | linkedServiceName            | Połączona usługa Azure Storage, która będzie używana przez klaster na żądanie do przechowywania i przetwarzania danych. | Tak  | Tak       | Nie           | Nie         |
|                           | additionalLinkedServiceNames | Określa dodatkowe konta magazynu dla połączonej usługi HDInsight, aby usługa Data Factory mogła zarejestrować je w Twoim imieniu. | Tak  | Nie        | Nie           | Nie         |
|                           | hcatalogLinkedServiceName    | Nazwa połączonej usługi Azure SQL, która wskazuje bazę danych HCatalog. Klaster usługi HDInsight na żądanie jest tworzony przy użyciu bazy danych Azure SQL Database jako magazynu metadanych. | Nie   | Nie        | Tak          | Nie         |
| BYOC                      | linkedServiceName            | Odwołanie do połączonej usługi Azure Storage.                | Tak  | Tak       | Nie           | Nie         |
|                           | additionalLinkedServiceNames | Określa dodatkowe konta magazynu dla połączonej usługi HDInsight, aby usługa Data Factory mogła zarejestrować je w Twoim imieniu. | Nie   | Nie        | Nie           | Nie         |
|                           | hcatalogLinkedServiceName    | Odwołanie do połączonej usługi Azure SQL, która wskazuje bazę danych HCatalog. | Nie   | Nie        | Nie           | Nie         |

### <a name="azure-hdinsight-on-demand-linked-service"></a>Połączona usługa Azure HDInsight na żądanie

W tym rodzaju konfiguracji środowisko komputerowe jest w pełni zarządzane przez usługę Azure Data Factory. Jest automatycznie tworzony przez usługę Data Factory przed przesłaniem zadania w celu przetworzenia danych i usunięciu po zakończeniu zadania. Można utworzyć połączoną usługę dla środowiska obliczeniowego na żądanie, skonfigurować ją i kontrolować szczegółowe ustawienia wykonywania zadań, zarządzania klastrami i uruchamiania akcji.

> [!NOTE]
> Konfiguracja na żądanie jest obecnie obsługiwana tylko w przypadku klastrów usługi Azure HDInsight. Azure Databricks obsługuje również zadania na żądanie przy użyciu klastrów zadań. Aby uzyskać więcej informacji, zobacz [połączonej usługi Azure datakostks](#azure-databricks-linked-service).

Usługa Azure Data Factory może automatycznie utworzyć klaster usługi HDInsight na żądanie, aby przetworzyć dane. Klaster jest tworzony w tym samym regionie co konto magazynu (Właściwość linkedServiceName w formacie JSON) skojarzone z klastrem. Konto magazynu to `must` standardowe konto usługi Azure Storage ogólnego przeznaczenia. 

Zwróć uwagę na następujące **Ważne** punkty dotyczące połączonej usługi HDInsight na żądanie:

* Klaster usługi HDInsight na żądanie jest tworzony w ramach subskrypcji platformy Azure. Klaster można zobaczyć w Azure Portal, gdy klaster jest uruchomiony. 
* Dzienniki zadań uruchomionych w klastrze usługi HDInsight na żądanie są kopiowane do konta magazynu skojarzonego z klastrem usługi HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword zdefiniowane w definicji połączonej usługi są używane do logowania się do klastra w celu uzyskania szczegółowego rozwiązywania problemów podczas cyklu życia klastra. 
* Opłata jest naliczana tylko za czas, gdy klaster usługi HDInsight jest uruchomiony i trwa wykonywanie zadań.
* **Akcji skryptu** można użyć w połączeniu z usługą Azure HDInsight na żądanie.  

> [!IMPORTANT]
> Inicjowanie obsługi klastra usługi Azure HDInsight na żądanie trwa zwykle **20 minut** lub dłużej.

#### <a name="example"></a>Przykład

Poniższy kod JSON definiuje połączoną usługę HDInsight na żądanie z systemem Linux. Usługa Data Factory automatycznie tworzy klaster usługi HDInsight **oparty na systemie Linux** w celu przetworzenia wymaganego działania. 

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
> Klaster usługi HDInsight tworzy **kontener domyślny** w magazynie obiektów BLOB określonym w kodzie JSON (**linkedServiceName**). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. W przypadku połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest tworzony za każdym razem, gdy trzeba przetworzyć wycinek — o ile w tym momencie nie istnieje aktywny klaster (**timeToLive**) — i zostaje usunięty po zakończeniu przetwarzania. 
>
> W przypadku większej liczby przebiegów działania zobaczysz wiele kontenerów w magazynie obiektów blob platformy Azure. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Użyj narzędzi, takich jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/) , aby usunąć kontenery w usłudze Azure Blob Storage.

#### <a name="properties"></a>Właściwości

| Właściwość                     | Opis                              | Wymagane |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Właściwość Type powinna mieć wartość **HDInsightOnDemand**. | Tak      |
| clusterSize                  | Liczba węzłów procesu roboczego/danych w klastrze. Klaster usługi HDInsight jest tworzony z 2 węzłami głównymi wraz z liczbą węzłów procesu roboczego określonych dla tej właściwości. Węzły mają rozmiar Standard_D3, które mają 4 rdzenie, więc klaster z 4 węzłami roboczymi zajmuje 24 rdzenie (4 rdzenie \* dla węzłów procesu roboczego, a 2 \* 4 = 8 rdzeni dla węzłów głównych). Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie klastrów w usłudze HDInsight za pomocą usługi Hadoop, Spark, Kafka i innych](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) . | Tak      |
| linkedServiceName            | Połączona usługa Azure Storage, która będzie używana przez klaster na żądanie do przechowywania i przetwarzania danych. Klaster HDInsight jest tworzony w tym samym regionie co konto usługi Azure Storage. Usługa Azure HDInsight ma ograniczenia całkowitej liczby rdzeni, których możesz użyć w każdym obsługiwanym przez nią regionie platformy Azure. Upewnij się, że masz wystarczającą liczbę podstawowych przydziałów w tym regionie platformy Azure, aby spełnić wymagania clusterSize. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie klastrów w usłudze HDInsight za pomocą usługi Hadoop, Spark, Kafka i innych](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Obecnie nie można utworzyć klastra usługi HDInsight na żądanie, który używa Azure Data Lake Storage (Gen 2) jako magazynu. Jeśli chcesz przechowywać dane wynikowe z przetwarzania HDInsight w Azure Data Lake Storage (Gen 2), Użyj działania kopiowania, aby skopiować dane z usługi Azure Blob Storage do Azure Data Lake Storage (Gen 2). </p> | Tak      |
| clusterResourceGroup         | Klaster usługi HDInsight jest tworzony w tej grupie zasobów. | Tak      |
| TimeToLive                   | Dozwolony czas bezczynności dla klastra usługi HDInsight na żądanie. Określa, jak długo klaster usługi HDInsight na żądanie pozostaje aktywny po zakończeniu działania, jeśli nie ma żadnych innych aktywnych zadań w klastrze. Minimalna dozwolona wartość to 5 minut (00:05:00).<br/><br/>Na przykład, Jeśli uruchomienie działania trwa 6 minut, a TimeToLive jest ustawiony na 5 minut, klaster pozostaje aktywny przez 5 minut po 6 minutach przetwarzania działania. Jeśli zostanie wykonane inne uruchomienie działania z oknem 6 minut, jest ono przetwarzane przez ten sam klaster.<br/><br/>Tworzenie klastra usługi HDInsight na żądanie jest kosztowną operacją (może to trochę potrwać), dlatego użyj tego ustawienia w razie potrzeby w celu zwiększenia wydajności fabryki danych przez ponowne użycie klastra usługi HDInsight na żądanie.<br/><br/>Jeśli wartość TimeToLive jest ustawiona na 0, klaster zostanie usunięty zaraz po zakończeniu działania. W przypadku ustawienia wysokiej wartości klaster może pozostać w stanie bezczynności, aby można było zalogować się w celu rozwiązania problemu, ale może to spowodować wysokie koszty. W związku z tym ważne jest, aby ustawić odpowiednią wartość na podstawie Twoich potrzeb.<br/><br/>Jeśli wartość właściwości TimeToLive jest odpowiednio ustawiona, wiele potoków może współdzielić wystąpienie klastra usługi HDInsight na żądanie. | Tak      |
| clustertype                  | Typ klastra usługi HDInsight, który ma zostać utworzony. Dozwolone wartości to "Hadoop" i "Spark". Jeśli nie zostanie określony, wartością domyślną jest Hadoop. Klastra z włączonym pakiet Enterprise Security nie można utworzyć na żądanie, zamiast tego użyj [istniejącego klastra/przeniesiej własne obliczenia](#azure-hdinsight-linked-service). | Nie       |
| Wersja                      | Wersja klastra usługi HDInsight. Jeśli nie zostanie określony, używana jest bieżąca wersja domyślna zdefiniowana w usłudze HDInsight. | Nie       |
| hostSubscriptionId           | Identyfikator subskrypcji platformy Azure używany do tworzenia klastra usługi HDInsight. Jeśli nie zostanie określony, używa identyfikatora subskrypcji kontekstu logowania platformy Azure. | Nie       |
| clusterNamePrefix           | Prefiks nazwy klastra HDI, sygnatura czasowa automatycznie dołączany na końcu nazwy klastra| Nie       |
| sparkVersion                 | Wersja platformy Spark, jeśli typ klastra to "Spark" | Nie       |
| additionalLinkedServiceNames | Określa dodatkowe konta magazynu dla połączonej usługi HDInsight, aby usługa Data Factory mogła zarejestrować je w Twoim imieniu. Te konta magazynu muszą znajdować się w tym samym regionie co klaster usługi HDInsight, który jest tworzony w tym samym regionie co konto magazynu określone przez linkedServiceName. | Nie       |
| osType                       | Typ systemu operacyjnego. Dozwolone wartości to: Linux i Windows (tylko w przypadku usługi HDInsight 3,3). Wartość domyślna to Linux. | Nie       |
| hcatalogLinkedServiceName    | Nazwa połączonej usługi Azure SQL, która wskazuje bazę danych HCatalog. Klaster usługi HDInsight na żądanie jest tworzony przy użyciu Azure SQL Database jako magazynu metadanych. | Nie       |
| Właściwością connectvia                   | Integration Runtime używany do wysyłania działań do tej połączonej usługi HDInsight. W przypadku połączonej usługi HDInsight na żądanie obsługuje ona tylko Azure Integration Runtime. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie       |
| clusterUserName                   | Nazwa użytkownika, aby uzyskać dostęp do klastra. | Nie       |
| clusterPassword                   | Hasło w typie bezpiecznego ciągu, aby uzyskać dostęp do klastra. | Nie       |
| clusterSshUserName         | Nazwa użytkownika do protokołu SSH zdalnie nawiązuje połączenie z węzłem klastra (dla systemu Linux). | Nie       |
| clusterSshPassword         | Hasło w polu Typ bezpiecznego ciągu protokołu SSH Połącz się zdalnie z węzłem klastra (dla systemu Linux). | Nie       |
| scriptActions | Określ skrypt dla [dostosowań klastra usługi HDInsight](../hdinsight/hdinsight-hadoop-customize-cluster-linux.md) podczas tworzenia klastra na żądanie. <br />Obecnie narzędzie do tworzenia interfejsu użytkownika Azure Data Factory obsługuje tylko 1 akcję skryptu, ale można je uzyskać przez to ograniczenie w formacie JSON (należy określić wiele akcji skryptu w formacie JSON). | Nie |


> [!IMPORTANT]
> Usługa HDInsight obsługuje wiele wersji klastra Hadoop, które można wdrożyć. Każda opcja wyboru wersji tworzy określoną wersję dystrybucji Hortonworks Data Platform (HDP) i zestaw składników zawartych w tej dystrybucji. Lista obsługiwanych wersji usługi HDInsight jest aktualizowana w celu zapewnienia najnowszych składników i poprawek ekosystemu usługi Hadoop. Upewnij się, że zawsze zapoznaj się z najnowszymi informacjami o [obsługiwanej wersji usługi HDInsight i typie systemu operacyjnego](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) , aby upewnić się, że używasz obsługiwanej wersji usługi HDInsight. 
>
> [!IMPORTANT]
> Obecnie połączone usługi HDInsight nie obsługują HBase, Interactive Query (Hive LLAP), burzy. 

* przykład JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

#### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Połączona Usługa HDInsight na żądanie wymaga uwierzytelniania jednostki usługi w celu utworzenia klastrów HDInsight w Twoim imieniu. Aby skorzystać z uwierzytelniania jednostki usługi, zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD) i Udziel jej roli **współautor** lub grupy zasobów, w której jest tworzony klaster usługi HDInsight. Aby uzyskać szczegółowe instrukcje, zobacz temat [Używanie portalu do tworzenia Azure Active Directory aplikacji i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji 
- Identyfikator dzierżawy

Użyj uwierzytelniania nazwy głównej usługi, określając następujące właściwości:

| Właściwość                | Opis                              | Wymagane |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Określ identyfikator klienta aplikacji.     | Tak      |
| **servicePrincipalKey** | Określ klucz aplikacji.           | Tak      |
| **tenant**              | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak      |

#### <a name="advanced-properties"></a>Właściwości zaawansowane

Można również określić następujące właściwości dla szczegółowej konfiguracji klastra usługi HDInsight na żądanie.

| Właściwość               | Opis                              | Wymagane |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Określa podstawowe parametry konfiguracji (jak w core-site.xml) dla klastra usługi HDInsight, który ma zostać utworzony. | Nie       |
| hBaseConfiguration     | Określa parametry konfiguracji HBase (hbase-site.xml) dla klastra usługi HDInsight. | Nie       |
| hdfsConfiguration      | Określa parametry konfiguracji systemu plików HDFS (hdfs-site.xml) dla klastra usługi HDInsight. | Nie       |
| hiveConfiguration      | Określa parametry konfiguracji programu Hive (hive-site.xml) dla klastra usługi HDInsight. | Nie       |
| mapReduceConfiguration | Określa parametry konfiguracji MapReduce (mapred-site.xml) dla klastra usługi HDInsight. | Nie       |
| oozieConfiguration     | Określa parametry konfiguracji Oozie (oozie-site.xml) dla klastra usługi HDInsight. | Nie       |
| stormConfiguration     | Określa parametry konfiguracji burzy (storm-site.xml) dla klastra usługi HDInsight. | Nie       |
| yarnConfiguration      | Określa parametry konfiguracji przędzy (yarn-site.xml) dla klastra usługi HDInsight. | Nie       |

* Przykład: Konfiguracja klastra usługi HDInsight na żądanie z zaawansowanymi właściwościami

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

#### <a name="node-sizes"></a>Rozmiary węzłów
Możesz określić rozmiary węzłów głowy, danych i dozorcy, korzystając z następujących właściwości: 

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| Dodano      | Określa rozmiar węzła głównego. Wartość domyślna to: Standard_D3. Aby uzyskać szczegółowe informacje, zobacz sekcję **Określanie rozmiarów węzłów** . | Nie       |
| dataNodeSize      | Określa rozmiar węzła danych. Wartość domyślna to: Standard_D3. | Nie       |
| zookeeperNodeSize | Określa rozmiar węzła programu zoo. Wartość domyślna to: Standard_D3. | Nie       |

* Określanie rozmiarów węzłów zobacz [rozmiary Virtual Machines](../virtual-machines/sizes.md) artykułu dla wartości ciągów, które należy określić dla właściwości wymienionych w poprzedniej sekcji. Wartości muszą być zgodne z **poleceniami cmdlet & interfejsów API** , do których odwołuje się w artykule. Jak widać w artykule, węzeł danych o dużym rozmiarze (domyślnym) ma 7 GB pamięci, co może nie być wystarczające dla danego scenariusza. 

Jeśli chcesz utworzyć węzły główne i węzły procesu roboczego o rozmiarach D4, określ **Standard_D4** jako wartość właściwości dodano i dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

W przypadku określenia nieprawidłowej wartości tych właściwości może zostać wyświetlony następujący **błąd:** nie można utworzyć klastra. Wyjątek: nie można ukończyć operacji tworzenia klastra. Operacja zakończona niepowodzeniem z kodem „400”. Końcowy stan klastra: „Błąd”. Komunikat: "PreClusterCreationValidationFailure". Po otrzymaniu tego błędu upewnij się, że używasz **polecenia CMDLET & nazwy interfejsów API** z tabeli w obszarze [rozmiary Virtual Machines](../virtual-machines/sizes.md) artykułu.

### <a name="bring-your-own-compute-environment"></a>Korzystanie z własnego środowiska obliczeniowego
W tym rodzaju konfiguracji użytkownicy mogą rejestrować już istniejące środowisko obliczeniowe jako połączoną usługę w Data Factory. Środowisko komputerowe jest zarządzane przez użytkownika, a usługa Data Factory używa go do wykonywania działań.

Ten typ konfiguracji jest obsługiwany dla następujących środowisk obliczeniowych:

* Azure HDInsight
* Usługa Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure Synapse Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Połączona usługa Azure HDInsight
Możesz utworzyć połączoną usługę Azure HDInsight, aby zarejestrować własny klaster usługi HDInsight przy użyciu Data Factory.

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
| Właściwość          | Opis                                                  | Wymagane |
| ----------------- | ------------------------------------------------------------ | -------- |
| typ              | Właściwość Type powinna być ustawiona na **HDInsight**.            | Tak      |
| clusterUri        | Identyfikator URI klastra usługi HDInsight.                            | Tak      |
| nazwa użytkownika          | Określ nazwę użytkownika, który ma być używany do nawiązywania połączenia z istniejącym klastrem usługi HDInsight. | Tak      |
| hasło          | Określ hasło dla konta użytkownika.                       | Tak      |
| linkedServiceName | Nazwa połączonej usługi Azure Storage odwołująca się do magazynu obiektów blob platformy Azure używanego przez klaster usługi HDInsight. <p>Obecnie nie można określić usługi połączonej Azure Data Lake Storage (Gen 2) dla tej właściwości. Jeśli klaster usługi HDInsight ma dostęp do Data Lake Store, możesz uzyskać dostęp do danych w Azure Data Lake Storage (Gen 2) ze skryptów Hive/wieprz. </p> | Tak      |
| isEspEnabled      | Określ *wartość "true*", Jeśli klaster usługi HDInsight jest [pakiet Enterprise Security](../hdinsight/domain-joined/apache-domain-joined-architecture.md) włączony. Wartość domyślna to "*false*". | Nie       |
| Właściwością connectvia        | Integration Runtime używany do wysyłania działań do tej połączonej usługi. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. <br />W przypadku pakiet Enterprise Security (ESP) klaster usługi HDInsight korzysta z własnego środowiska Integration Runtime, które ma linię wglądu w klaster lub należy ją wdrożyć w tym samym Virtual Network co klaster usługi HDInsight ESP. | Nie       |

> [!IMPORTANT]
> Usługa HDInsight obsługuje wiele wersji klastra Hadoop, które można wdrożyć. Każda opcja wyboru wersji tworzy określoną wersję dystrybucji Hortonworks Data Platform (HDP) i zestaw składników zawartych w tej dystrybucji. Lista obsługiwanych wersji usługi HDInsight jest aktualizowana w celu zapewnienia najnowszych składników i poprawek ekosystemu usługi Hadoop. Upewnij się, że zawsze zapoznaj się z najnowszymi informacjami o [obsługiwanej wersji usługi HDInsight i typie systemu operacyjnego](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) , aby upewnić się, że używasz obsługiwanej wersji usługi HDInsight. 
>
> [!IMPORTANT]
> Obecnie połączone usługi HDInsight nie obsługują HBase, Interactive Query (Hive LLAP), burzy. 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch połączona usługa

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można utworzyć połączoną usługę Azure Batch, aby zarejestrować pulę usługi Batch maszyn wirtualnych w fabryce danych. Działanie niestandardowe można uruchomić przy użyciu Azure Batch.

Jeśli jesteś nowym usługą Azure Batch Service, zobacz następujące artykuły:

* [Azure Batch podstawy](../batch/batch-technical-overview.md) dotyczące omówienia usługi Azure Batch.
* Polecenie cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) służące do tworzenia konta Azure Batch (lub) [Azure Portal](../batch/batch-account-create-portal.md) do tworzenia konta Azure Batch przy użyciu Azure Portal. Szczegółowe instrukcje dotyczące korzystania z polecenia cmdlet można znaleźć w artykule [Używanie programu PowerShell do zarządzania artykułem konta Azure Batch](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) .
* Polecenie cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) w celu utworzenia puli Azure Batch.

> [!IMPORTANT]
> Podczas tworzenia nowej puli Azure Batch należy użyć elementu "VirtualMachineConfiguration", a nie "CloudServiceConfiguration". Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące migracji puli Azure Batch](../batch/batch-pool-cloud-service-to-virtual-machine-configuration.md). 

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
| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Właściwość Type powinna mieć wartość **AzureBatch**. | Tak      |
| accountName       | Nazwa konta Azure Batch.         | Tak      |
| accessKey         | Klucz dostępu dla konta Azure Batch.  | Tak      |
| batchUri          | Adres URL konta Azure Batch w formacie https://*batchaccountname. region*. Batch.Azure.com. | Tak      |
| poolName          | Nazwa puli maszyn wirtualnych.    | Tak      |
| linkedServiceName | Nazwa połączonej usługi Azure Storage skojarzonej z tą Azure Batch połączoną usługą. Ta połączona usługa jest używana na potrzeby plików tymczasowych wymaganych do uruchomienia działania. | Tak      |
| Właściwością connectvia        | Integration Runtime używany do wysyłania działań do tej połączonej usługi. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie       |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Połączona usługa Azure Machine Learning Studio (klasyczna)
Utworzysz połączoną usługę Azure Machine Learning Studio (klasyczną), aby zarejestrować punkt końcowy oceniania Machine Learning Studio (klasyczny) do fabryki danych.

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
| Właściwość               | Opis                              | Wymagane                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Właściwość Type powinna mieć wartość: **Azure**. | Tak                                      |
| mlEndpoint             | Adres URL oceniania partii.                   | Tak                                      |
| apiKey                 | Interfejs API opublikowanego modelu obszaru roboczego.     | Tak                                      |
| Właściwości updateresourceendpoint | Adres URL aktualizacji zasobu dla Azure Machine Learning Studio (klasyczny) punkt końcowy usługi sieci Web używany do aktualizacji predykcyjnej usługi sieci Web przy użyciu pliku z wyszkolonym modelem | Nie                                       |
| servicePrincipalId     | Określ identyfikator klienta aplikacji.     | Wymagane, jeśli określono właściwości updateresourceendpoint |
| servicePrincipalKey    | Określ klucz aplikacji.           | Wymagane, jeśli określono właściwości updateresourceendpoint |
| dzierżaw                 | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Wymagane, jeśli określono właściwości updateresourceendpoint |
| Właściwością connectvia             | Integration Runtime używany do wysyłania działań do tej połączonej usługi. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie                                       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning połączona usługa
Tworzysz Azure Machine Learning połączonej usługi, aby połączyć obszar roboczy Azure Machine Learning z fabryką danych.

> [!NOTE]
> Dla połączonej usługi Azure Machine Learning jest obsługiwane obecnie tylko uwierzytelnianie główne usługi.

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
| Właściwość               | Opis                              | Wymagane                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Typ                   | Właściwość Type powinna mieć wartość: **AzureMLService**. | Tak                                      |
| subscriptionId         | Identyfikator subskrypcji platformy Azure              | Tak                                      |
| resourceGroupName      | name | Tak                                      |
| mlWorkspaceName        | Nazwa obszaru roboczego Azure Machine Learning | Tak  |
| servicePrincipalId     | Określ identyfikator klienta aplikacji.     | Nie |
| servicePrincipalKey    | Określ klucz aplikacji.           | Nie |
| dzierżaw                 | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Wymagane, jeśli określono właściwości updateresourceendpoint |
| Właściwością connectvia             | Integration Runtime używany do wysyłania działań do tej połączonej usługi. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics połączona usługa
Tworzysz **Azure Data Lake Analytics** połączonej usługi, aby połączyć usługę obliczeniową Azure Data Lake Analytics z fabryką danych Azure. Działanie Data Lake Analytics U-SQL w potoku odwołuje się do tej połączonej usługi. 

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

| Właściwość             | Opis                              | Wymagane                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| typ                 | Właściwość Type powinna mieć wartość: **AzureDataLakeAnalytics**. | Tak                                      |
| accountName          | Nazwa konta Azure Data Lake Analytics.  | Tak                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics identyfikator URI.           | Nie                                       |
| subscriptionId       | Identyfikator subskrypcji platformy Azure                    | Nie                                       |
| resourceGroupName    | Nazwa grupy zasobów platformy Azure                | Nie                                       |
| servicePrincipalId   | Określ identyfikator klienta aplikacji.     | Tak                                      |
| servicePrincipalKey  | Określ klucz aplikacji.           | Tak                                      |
| dzierżaw               | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak                                      |
| Właściwością connectvia           | Integration Runtime używany do wysyłania działań do tej połączonej usługi. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime. Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks połączona usługa
Można utworzyć **Azure Databricks połączonej usługi** , aby zarejestrować obszar roboczy datakostki, który jest używany do uruchamiania obciążeń datakostek (Notes, jar, Python). 
> [!IMPORTANT]
> Połączone usługi datakostki obsługują [Pule wystąpień](https://aka.ms/instance-pools) & uwierzytelniania tożsamości zarządzanej przypisanego do systemu.

### <a name="example---using-new-job-cluster-in-databricks"></a>Przykład — używanie nowego klastra zadań w kostkach

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

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Przykład — używanie istniejącego klastra interaktywnego w kostkach

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

| Właściwość             | Opis                              | Wymagane                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | Nazwa połączonej usługi               | Tak   |
| typ                 | Właściwość Type powinna mieć wartość: **Azure Databricks**. | Tak                                      |
| domena               | Określ odpowiednio region platformy Azure na podstawie regionu obszaru roboczego datakostki. Przykład: https://eastus.azuredatabricks.net | Tak                                 |
| accessToken          | Token dostępu jest wymagany do uwierzytelniania Data Factory Azure Databricks. Token dostępu musi być wygenerowany z obszaru roboczego datakostki. Bardziej szczegółowe instrukcje znajdowania tokenu dostępu można znaleźć [tutaj](/azure/databricks/dev-tools/api/latest/authentication#generate-token)  | Nie                                       |
| Tożsamość usługi zarządzanej          | Użyj zarządzanej tożsamości Data Factory (przypisanej do systemu) do uwierzytelniania, aby Azure Databricks. Nie potrzebujesz tokenu dostępu podczas korzystania z uwierzytelniania "MSI"  | Nie                                       |
| existingClusterId    | Identyfikator klastra istniejącego klastra, w którym mają zostać uruchomione wszystkie zadania. Powinien to być już utworzony interaktywny klaster. Może być konieczne ręczne ponowne uruchomienie klastra, jeśli przestanie odpowiadać. Datakostki sugerują uruchomione zadania w nowych klastrach w celu zapewnienia większej niezawodności. Identyfikator klastra interaktywnego klastra można znaleźć w obszarze roboczym datakosteks — > klastrów — > Interactive Nazwa klastra — > Configuration-> Tagi. [Więcej szczegółów](https://docs.databricks.com/user-guide/clusters/tags.html) | Nie 
| instancePoolId    | Identyfikator puli wystąpień istniejącej puli w obszarze roboczym datakosteks.  | Nie  |
| newClusterVersion    | Wersja platformy Spark klastra. Tworzy klaster zadań w kostkach. | Nie  |
| newClusterNumOfWorker| Liczba węzłów procesu roboczego, które powinien mieć ten klaster. Klaster ma jeden sterownik platformy Spark i num_workers wykonywania w sumie num_workers + 1 platformy Spark. Ciąg w formacie Int32, taki jak "1" oznacza, że numOfWorker ma wartość 1 lub "1:10" oznacza, że Skalowanie automatyczne jest z przedziału od 1 do minimum i 10 jako maksymalne.  | Nie                |
| newClusterNodeType   | To pole koduje, za pomocą pojedynczej wartości, zasobów dostępnych dla każdego z węzłów platformy Spark w tym klastrze. Na przykład węzły platformy Spark mogą być obsługiwane i zoptymalizowane pod kątem obciążeń pamięci lub intensywnych obliczeń. To pole jest wymagane dla nowego klastra                | Nie               |
| newClusterSparkConf  | zestaw opcjonalnych par klucz-wartość konfiguracji platformy Spark określony przez użytkownika. Użytkownicy mogą również przekazać ciąg dodatkowych opcji JVM do sterownika i modułów wykonujących za pośrednictwem platformy Spark. Driver. extraJavaOptions i spark.executor. extraJavaOptions. | Nie  |
| newClusterInitScripts| zestaw opcjonalnych, zdefiniowanych przez użytkownika skryptów inicjalizacji dla nowego klastra. Określanie ścieżki DBFS do skryptów init. | Nie  |


## <a name="azure-sql-database-linked-service"></a>Połączona usługa Azure SQL Database

Utworzysz połączoną usługę Azure SQL i użyjesz jej razem z [działaniem procedury składowanej](transform-data-using-stored-procedure.md) , aby wywołać procedurę składowaną z potoku Data Factory. Aby uzyskać szczegółowe informacje na temat tej połączonej usługi, zobacz artykuł dotyczący [łącznika usługi Azure SQL](connector-azure-sql-database.md#linked-service-properties) .

## <a name="azure-synapse-analytics-linked-service"></a>Połączona usługa Azure Synapse Analytics

Tworzenie połączonej usługi Azure Synapse Analytics i używanie jej z [działaniem procedury składowanej](transform-data-using-stored-procedure.md) w celu wywołania procedury składowanej z potoku Data Factory. Aby uzyskać szczegółowe informacje na temat tej połączonej usługi, zobacz artykuł dotyczący [łącznika usługi Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#linked-service-properties) .

## <a name="sql-server-linked-service"></a>SQL Server połączona usługa

Utworzysz połączoną usługę SQL Server i użyj jej z [działaniem procedury składowanej](transform-data-using-stored-procedure.md) , aby wywołać procedurę składowaną z potoku Data Factory. Aby uzyskać szczegółowe informacje na temat tej połączonej usługi, zobacz artykuł dotyczący [łącznika SQL Server](connector-sql-server.md#linked-service-properties) .

## <a name="azure-function-linked-service"></a>Połączona usługa funkcji platformy Azure

Utworzysz połączoną usługę funkcji platformy Azure i użyjesz jej razem z [działaniem funkcji platformy Azure](control-flow-azure-function-activity.md) , aby uruchamiać Azure Functions w potoku Data Factory. Zwracany typ funkcji platformy Azure musi być prawidłowy `JObject` . (Należy pamiętać, że [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *nie* jest `JObject` .) Każdy typ zwracany inny niż błąd `JObject` i podnosi zawartość odpowiedzi na błędy użytkownika *nie jest prawidłowym JObject*.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| typ   | Właściwość Type musi mieć wartość: **AzureFunction** | tak |
| adres URL aplikacji funkcji | Adres URL aplikacja funkcji platformy Azure. Format to `https://<accountname>.azurewebsites.net` . Ten adres URL jest wartością w sekcji **adresu URL** podczas wyświetlania aplikacja funkcji w Azure Portal  | tak |
| klucz funkcji | Klucz dostępu dla funkcji platformy Azure. Kliknij sekcję **Zarządzanie** odpowiednią funkcją i skopiuj **klucz funkcji** lub **klucz hosta**. Dowiedz się więcej tutaj: [Azure Functions wyzwalacze i powiązania HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | tak |
|   |   |   |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę działań transformacji obsługiwanych przez Azure Data Factory, zobacz [Przekształć dane](transform-data.md).
