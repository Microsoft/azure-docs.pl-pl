---
title: Środowiska obliczeniowe obsługiwane przez Azure Data Factory wersji 1
description: Informacje o środowiskach obliczeniowych, których można użyć w potokach Azure Data Factory (takich jak usługa Azure HDInsight) do przekształcania lub przetwarzania danych.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 07e13036a427ff7ff4f0cbbd2dfd6fbe272bf915
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377197"
---
# <a name="compute-environments-supported-by-azure-data-factory-version-1"></a>Środowiska obliczeniowe obsługiwane przez Azure Data Factory wersji 1
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli korzystasz z bieżącej wersji usługi Data Factory, zobacz [połączone usługi obliczeniowe w](../compute-linked-services.md)temacie.

W tym artykule wyjaśniono środowiska obliczeniowe, których można użyć do przetwarzania lub przekształcania danych. Zawiera również szczegółowe informacje o różnych konfiguracjach (na żądanie i przyłączeniu do własnych), które Data Factory obsługiwane podczas konfigurowania połączonych usług łączących te środowiska obliczeniowe z fabryką danych Azure.

Poniższa tabela zawiera listę środowisk obliczeniowych obsługiwanych przez Data Factory oraz działania, które mogą być na nich uruchomione. 

| Środowisko obliczeniowe                      | Działania                               |
| ---------------------------------------- | ---------------------------------------- |
| [Klaster usługi Azure HDInsight na żądanie](#azure-hdinsight-on-demand-linked-service) lub [własny klaster HDInsight](#azure-hdinsight-linked-service) | [Dotnet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [świnie](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), usługa [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klasyczny)](#azure-machine-learning-studio-classic-linked-service) | [Działania programu Studio (klasyczne): wykonywanie wsadowe i aktualizowanie zasobu](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Działania procedur składowanych](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>Wersje usługi HDInsight obsługiwane w Data Factory
Usługa Azure HDInsight obsługuje wiele wersji klastra Hadoop, które można wdrożyć w dowolnym momencie. Każda obsługiwana wersja tworzy określoną wersję dystrybucji Hortonworks Data Platform (HDP) i zestaw składników w dystrybucji. 

Firma Microsoft aktualizuje listę obsługiwanych wersji usługi HDInsight z najnowszymi składnikami i poprawkami ekosystemu usługi Hadoop. Aby uzyskać szczegółowe informacje, zobacz [obsługiwane wersje usługi HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Program HDInsight oparty na systemie Linux w wersji 3,3 został wycofany 31 lipca 2017. Do 15 grudnia 2017 w celu przetestowania i przeprowadzenia uaktualnienia do nowszej wersji usługi HDInsight podano klientów połączonych usług HDInsight Data Factory wersja 1. Usługa HDInsight oparta na systemie Windows zostanie wycofana 31 lipca 2018.
>
> 

### <a name="after-the-retirement-date"></a>Po dacie wycofania 

Po 15 grudnia 2017:

- Nie można już tworzyć klastrów opartych na systemie Linux w wersji 3,3 (lub starszych) przy użyciu połączonej usługi HDInsight na żądanie w Data Factory wersji 1. 
- Jeśli właściwości [ **OsType** i **Version**](#azure-hdinsight-on-demand-linked-service) nie zostały jawnie określone w definicji JSON dla istniejącej połączonej usługi HDInsight Data Factory wersja 1 na żądanie, wartość domyślna zostanie zmieniona z **wersji = 3.1, osType = Windows** na **wersję = \<latest HDI default version\> ( https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) , osType = Linux**.

Po 31 lipca 2018:

- Nie można już tworzyć żadnych wersji klastrów usługi HDInsight opartych na systemie Windows przy użyciu połączonej usługi HDInsight na żądanie w Data Factory wersji 1. 

### <a name="recommended-actions"></a>Zalecane akcje

- Aby upewnić się, że można użyć najnowszych składników i poprawek ekosystemu usługi Hadoop, zaktualizuj [ **osType** i właściwości **wersji**](#azure-hdinsight-on-demand-linked-service) , których dotyczy problem Data Factory wersja 1 usługi HDInsight na żądanie, do nowszych wersji programu HDInsight opartych na systemie Linux (HDInsight 3,6). 
- Przed 15 grudnia 2017, test Data Factory wersja 1 Hive, MapReduce i działania przesyłania strumieniowego Hadoop, które odwołują się do połączonej usługi. Upewnij się, że są one zgodne z nowymi wartościami domyślnymi **osType** i **Version** (**Version = 3.6**, **osType = Linux**) lub z jawną wersją usługi HDInsight i typem systemu operacyjnego, do którego uaktualniasz. 
  Aby dowiedzieć się więcej na temat zgodności, zobacz [Migrowanie z klastra usługi HDInsight opartego na systemie Windows do klastra](../../hdinsight/index.yml) z systemem Linux i [jakie są składniki i wersje usługi Hadoop dostępne w usłudze HDInsight?](../../hdinsight/hdinsight-component-versioning.md). 
- Aby nadal Data Factory używać połączonej usługi HDInsight w wersji 1 na żądanie w celu utworzenia klastrów HDInsight opartych na systemie Windows, jawnie ustaw **OsType** **systemu Windows** przed 15 grudnia 2017. Zalecamy Migrowanie do klastrów usługi HDInsight opartych na systemie Linux przed 31 lipca 2018. 
- Jeśli używasz połączonej usługi HDInsight na żądanie w celu wykonania niestandardowego działania programu DotNet Data Factory w wersji 1, zaktualizuj definicję JSON niestandardowego działania DotNet, aby zamiast tego używać połączonej usługi Azure Batch. Aby uzyskać więcej informacji, zobacz [Korzystanie z działań niestandardowych w potoku Data Factory](./data-factory-use-custom-activities.md). 

> [!Note]
> Jeśli korzystasz z istniejącego, wbudowanego urządzenia usługi HDInsight klastra w Data Factory wersji 1 lub połączonej z usługą HDInsight na żądanie w Azure Data Factory, nie jest wymagana żadna akcja. W tych scenariuszach jest już wymuszana Najnowsza wersja zasad obsługi klastrów usługi HDInsight. 
>
> 


## <a name="on-demand-compute-environment"></a>Środowisko obliczeniowe na żądanie
W konfiguracji na żądanie Data Factory w pełni zarządzać środowiskiem obliczeniowym. Data Factory automatycznie tworzy środowisko obliczeniowe przed przesłaniem zadania w celu przetworzenia danych. Po zakończeniu zadania Data Factory usuwa środowisko obliczeniowe. 

Można utworzyć połączoną usługę dla środowiska obliczeniowego na żądanie. Połączona usługa służy do konfigurowania środowiska obliczeniowego oraz do kontrolowania szczegółowych ustawień wykonywania zadań, zarządzania klastrami i uruchamiania akcji.

> [!NOTE]
> Obecnie konfiguracja na żądanie jest obsługiwana tylko w przypadku klastrów usługi HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Połączona usługa Azure HDInsight na żądanie
Data Factory może automatycznie utworzyć klaster usługi HDInsight na żądanie oparty na systemie Windows lub Linux na potrzeby przetwarzania danych. Klaster jest tworzony w tym samym regionie co konto magazynu skojarzone z klastrem. Użyj właściwości **LINKEDSERVICENAME** JSON, aby utworzyć klaster.

Należy zwrócić uwagę na następujące *kluczowe* kwestie dotyczące połączonej usługi HDInsight na żądanie:

* Klaster usługi HDInsight na żądanie nie jest wyświetlany w Twojej subskrypcji platformy Azure. Usługa Data Factory zarządza klastrem usługi HDInsight na żądanie w Twoim imieniu.
* Dzienniki zadań uruchomionych w klastrze usługi HDInsight na żądanie są kopiowane do konta magazynu skojarzonego z klastrem usługi HDInsight. Aby uzyskać dostęp do tych dzienników, w Azure Portal przejdź do okienka **szczegóły uruchomienia działania** . Aby uzyskać więcej informacji, zobacz [monitorowanie potoków i zarządzanie nimi](data-factory-monitor-manage-pipelines.md).
* Opłata jest naliczana tylko za czas, w którym klaster usługi HDInsight jest uruchomiony i trwa wykonywanie zadań.

> [!IMPORTANT]
> Inicjowanie obsługi klastra usługi HDInsight na żądanie trwa zwykle *20 minut* lub dłużej.
>
> 

### <a name="example"></a>Przykład
Poniższy kod JSON definiuje połączoną usługę HDInsight na żądanie z systemem Linux. Data Factory automatycznie tworzy klaster usługi HDInsight *oparty na systemie Linux* podczas przetwarzania wycinka danych. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> Klaster usługi HDInsight tworzy *kontener domyślny* w magazynie obiektów blob platformy Azure, który określono we właściwości **linkedServiceName** json. Po zaprojektowaniu Usługa HDInsight nie usuwa tego kontenera w przypadku usunięcia klastra. W połączonej usłudze HDInsight na żądanie klaster usługi HDInsight jest tworzony za każdym razem, gdy trzeba przetworzyć wycinek, chyba że istnieje istniejący klaster na żywo (**TimeToLive**). Klaster jest usuwany po zakończeniu przetwarzania. 
>
> Po przetworzeniu większej liczby wycinków w magazynie obiektów BLOB jest widocznych wiele kontenerów. Jeśli nie potrzebujesz kontenerów do rozwiązywania problemów z zadaniami, możesz usunąć kontenery, aby zmniejszyć koszty magazynowania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf<your Data Factory name>-<linked service name>-<date and time>`. Za pomocą narzędzia, takiego jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/) , można usunąć kontenery w usłudze BLOB Storage.
>
> 

### <a name="properties"></a>Właściwości
| Właściwość                     | Opis                              | Wymagane |
| ---------------------------- | ---------------------------------------- | -------- |
| typ                         | Ustaw Właściwość Type na **HDInsightOnDemand**. | Tak      |
| clusterSize                  | Liczba węzłów procesu roboczego i danych w klastrze. Klaster usługi HDInsight jest tworzony z 2 węzłami głównymi, a także z liczbą węzłów procesu roboczego określonych dla tej właściwości. Węzły mają rozmiar Standard_D3, który ma 4 rdzenie. Klaster węzeł 4-proces roboczy przyjmuje 24 rdzenie (4 \* 4 = 16 rdzeni dla węzłów procesu roboczego, a 2 \* 4 = 8 rdzeni dla węzłów głównych). Aby uzyskać szczegółowe informacje na temat warstwy Standard_D3, zobacz [Tworzenie klastrów Hadoop opartych na systemie Linux w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Tak      |
| timeToLive                   | Dozwolony czas bezczynności dla klastra usługi HDInsight na żądanie. Określa, jak długo klaster usługi HDInsight na żądanie pozostaje aktywny po zakończeniu uruchomienia działania, jeśli nie ma żadnych innych aktywnych zadań w klastrze.<br /><br />Na przykład, Jeśli uruchomienie działania trwa 6 minut, a **TimeToLive** jest ustawiony na 5 minut, klaster pozostaje aktywny przez 5 minut po 6 minutach przetwarzania działania. Jeśli w oknie 6-minutowy zostanie wykonane inne uruchomienie działania, jest ono przetwarzane przez ten sam klaster.<br /><br />Tworzenie klastra usługi HDInsight na żądanie jest kosztowną operacją (może to potrwać trochę czasu). Użyj tego ustawienia, aby zwiększyć wydajność fabryki danych przez ponowne użycie klastra usługi HDInsight na żądanie.<br /><br />Jeśli wartość **TimeToLive** jest ustawiona na **0**, klaster zostanie usunięty zaraz po zakończeniu uruchomienia działania. Jeśli jednak ustawisz wysoką wartość, klaster może pozostać bezczynny, niekoniecznie powodując wysokie koszty. Ważne jest, aby ustawić odpowiednią wartość na podstawie Twoich potrzeb.<br /><br />Jeśli wartość **TimeToLive** jest odpowiednio ustawiona, wiele potoków może współdzielić wystąpienie klastra usługi HDInsight na żądanie. | Tak      |
| Wersja                      | Wersja klastra usługi HDInsight. Aby uzyskać dozwolone wersje usługi HDInsight, zobacz [obsługiwane wersje usługi HDInsight](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). Jeśli ta wartość nie jest określona, używana jest [Najnowsza wersja HDI](../../hdinsight/hdinsight-component-versioning.md) . | Nie       |
| linkedServiceName            | Połączona usługa Azure Storage, która będzie używana przez klaster na żądanie do przechowywania i przetwarzania danych. Klaster usługi HDInsight jest tworzony w tym samym regionie, w którym znajduje się to konto magazynu.<p>Obecnie nie można utworzyć klastra usługi HDInsight na żądanie, który używa Azure Data Lake Store jako magazynu. Jeśli chcesz przechowywać dane wynikowe z przetwarzania usługi HDInsight w Data Lake Store, Użyj działania kopiowania, aby skopiować dane z magazynu obiektów BLOB do Data Lake Store. </p> | Tak      |
| additionalLinkedServiceNames | Określa dodatkowe konta magazynu dla połączonej usługi HDInsight. Data Factory rejestruje konta magazynu w Twoim imieniu. Te konta magazynu muszą znajdować się w tym samym regionie co klaster usługi HDInsight. Klaster usługi HDInsight jest tworzony w tym samym regionie co konto magazynu określone przez właściwość **linkedServiceName** . | Nie       |
| osType                       | Typ systemu operacyjnego. Dozwolone wartości to **Linux** i **Windows**. Jeśli ta wartość nie jest określona, zostanie użyty system **Linux** .  <br /><br />Zdecydowanie zalecamy korzystanie z klastrów usługi HDInsight opartych na systemie Linux. Data wycofania usługi HDInsight w systemie Windows to 31 lipca 2018. | Nie       |
| hcatalogLinkedServiceName    | Nazwa połączonej usługi Azure SQL, która wskazuje bazę danych HCatalog. Klaster usługi HDInsight na żądanie jest tworzony przy użyciu bazy danych SQL jako magazynu metadanych. | Nie       |

#### <a name="example-linkedservicenames-json"></a>Przykład: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Właściwości zaawansowane
Aby uzyskać szczegółową konfigurację klastra usługi HDInsight na żądanie, można określić następujące właściwości:

| Właściwość               | Opis                              | Wymagane |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Określa podstawowe parametry konfiguracji (core-site.xml) dla klastra usługi HDInsight, który ma zostać utworzony. | Nie       |
| hBaseConfiguration     | Określa parametry konfiguracji HBase (hbase-site.xml) dla klastra usługi HDInsight. | Nie       |
| hdfsConfiguration      | Określa parametry konfiguracji systemu plików HDFS (hdfs-site.xml) dla klastra usługi HDInsight. | Nie       |
| hiveConfiguration      | Określa parametry konfiguracji programu Hive (hive-site.xml) dla klastra usługi HDInsight. | Nie       |
| mapReduceConfiguration | Określa parametry konfiguracji MapReduce (mapred-site.xml) dla klastra usługi HDInsight. | Nie       |
| oozieConfiguration     | Określa parametry konfiguracji Oozie (oozie-site.xml) dla klastra usługi HDInsight. | Nie       |
| stormConfiguration     | Określa parametry konfiguracji burzy (storm-site.xml) dla klastra usługi HDInsight. | Nie       |
| yarnConfiguration      | Określa parametry konfiguracji PRZĘDZy (yarn-site.xml) dla klastra usługi HDInsight. | Nie       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Przykład: Konfiguracja klastra usługi HDInsight na żądanie z zaawansowanymi właściwościami

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
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
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Rozmiary węzłów
Aby określić rozmiar węzłów głowy, danych i dozorcy, użyj następujących właściwości: 

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| Dodano      | Ustawia rozmiar węzła głównego. Wartość domyślna to **Standard_D3**. Aby uzyskać szczegółowe informacje, zobacz [Określanie rozmiarów węzłów](#specify-node-sizes). | Nie       |
| dataNodeSize      | Ustawia rozmiar węzła danych. Wartość domyślna to **Standard_D3**. | Nie       |
| zookeeperNodeSize | Ustawia rozmiar węzła dozorcy. Wartość domyślna to **Standard_D3**. | Nie       |

#### <a name="specify-node-sizes"></a>Określ rozmiary węzłów
W przypadku wartości ciągów, które należy określić dla właściwości opisanych w poprzedniej sekcji, zobacz [rozmiary maszyn wirtualnych](../../virtual-machines/sizes.md). Wartości muszą być zgodne z poleceniami cmdlet i interfejsów API, do których odwołują się [rozmiary maszyn wirtualnych](../../virtual-machines/sizes.md). Duży (domyślny) rozmiar węzła danych ma 7 GB pamięci. Może to nie być wystarczające dla Twojego scenariusza. 

Jeśli chcesz utworzyć węzły główne rozmiaru D4 i węzły procesu roboczego, określ **Standard_D4** jako wartość właściwości **Dodano** i **dataNodeSize** : 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

W przypadku ustawienia nieprawidłowej wartości dla tych właściwości może zostać wyświetlony następujący komunikat:

  Utworzenie klastra nie powiodło się. Wyjątek: nie można ukończyć operacji tworzenia klastra. Operacja zakończona niepowodzeniem z kodem „400”. Końcowy stan klastra: „Błąd”. Komunikat: "PreClusterCreationValidationFailure". 
  
Jeśli zobaczysz ten komunikat, upewnij się, że używasz poleceń cmdlet i nazw interfejsów API z tabeli w obszarze [rozmiary maszyn wirtualnych](../../virtual-machines/sizes.md).  

> [!NOTE]
> Obecnie Data Factory nie obsługuje klastrów usługi HDInsight, które używają Data Lake Store jako magazynu podstawowego. Użyj usługi Azure Storage jako magazynu podstawowego dla klastrów HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Przenoszenie własnego środowiska obliczeniowego
Istnieje możliwość zarejestrowania istniejącego środowiska obliczeniowego jako połączonej usługi w Data Factory. Zarządzanie środowiskiem obliczeniowym jest możliwe. Usługa Data Factory używa środowiska obliczeniowego do wykonywania działań.

Ten typ konfiguracji jest obsługiwany dla następujących środowisk obliczeniowych:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning Studio (klasyczny)
* Azure Data Lake Analytics
* Azure SQL Database, usługa Azure Synapse Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Połączona usługa Azure HDInsight
Można utworzyć połączoną usługę HDInsight, aby zarejestrować własny klaster usługi HDInsight z Data Factory.

### <a name="example"></a>Przykład

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Właściwości
| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Ustaw Właściwość Type na **HDInsight**. | Tak      |
| clusterUri        | Identyfikator URI klastra usługi HDInsight.        | Tak      |
| nazwa użytkownika          | Nazwa konta użytkownika, które ma zostać użyte do nawiązania połączenia z istniejącym klastrem usługi HDInsight. | Tak      |
| hasło          | Hasło konta użytkownika.   | Tak      |
| linkedServiceName | Nazwa połączonej usługi Storage odwołująca się do magazynu obiektów BLOB używanego przez klaster usługi HDInsight. <p>Obecnie nie można określić Data Lake Store połączonej usługi dla tej właściwości. Jeśli klaster usługi HDInsight ma dostęp do Data Lake Store, możesz uzyskać dostęp do danych w Data Lake Store ze skryptów Hive lub świńskiej. </p> | Tak      |

## <a name="azure-batch-linked-service"></a>Azure Batch połączona usługa
Można utworzyć połączoną usługę Batch, aby zarejestrować pulę usługi Batch maszyn wirtualnych w fabryce danych. Działania niestandardowe można uruchamiać Microsoft .NET przy użyciu usługi Batch lub usługi HDInsight.

Jeśli dopiero zaczynasz korzystać z usługi Batch:

* Dowiedz się więcej na temat [Azure Batch podstawowych](../../azure-sql/database/sql-database-paas-overview.md).
* Dowiedz się więcej na temat polecenia cmdlet [New-AzureBatchAccount](/previous-versions/azure/mt125880(v=azure.100)) . Użyj tego polecenia cmdlet, aby utworzyć konto w usłudze Batch. Można też utworzyć konto usługi Batch przy użyciu [Azure Portal](../../batch/batch-account-create-portal.md). Aby uzyskać szczegółowe informacje na temat korzystania z polecenia cmdlet, zobacz [Używanie programu PowerShell do zarządzania kontem usługi Batch](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account).
* Dowiedz się więcej na temat polecenia cmdlet [New-AzureBatchPool](/previous-versions/azure/mt125936(v=azure.100)) . Użyj tego polecenia cmdlet, aby utworzyć pulę wsadową.

### <a name="example"></a>Przykład

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Dla właściwości **AccountName** Dołącz wartość **. \<region name\>** Nazwa konta w usłudze Batch. Na przykład:

```json
"accountName": "mybatchaccount.eastus"
```

Innym rozwiązaniem jest dostarczenie punktu końcowego **batchUri** . Na przykład:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Właściwości
| Właściwość          | Opis                              | Wymagane |
| ----------------- | ---------------------------------------- | -------- |
| typ              | Ustaw Właściwość Type na **AzureBatch**. | Tak      |
| accountName       | Nazwa konta wsadowego.         | Tak      |
| accessKey         | Klucz dostępu dla konta usługi Batch.  | Tak      |
| poolName          | Nazwa puli maszyn wirtualnych.    | Tak      |
| linkedServiceName | Nazwa połączonej usługi Storage, która jest skojarzona z tą połączoną usługą Batch. Ta połączona usługa jest używana w przypadku plików tymczasowych wymaganych do uruchomienia działania oraz do przechowywania dzienników wykonywania działania. | Tak      |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Połączona usługa Azure Machine Learning Studio (klasyczna)
Możesz utworzyć połączoną usługę Azure Machine Learning Studio (klasyczną), aby zarejestrować punkt końcowy oceniania usługi Batch (klasyczny) na potrzeby fabryki danych.

### <a name="example"></a>Przykład

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Właściwości
| Właściwość   | Opis                              | Wymagane |
| ---------- | ---------------------------------------- | -------- |
| Typ       | Ustaw Właściwość Type na **Azure**. | Tak      |
| mlEndpoint | Adres URL oceniania partii.                   | Tak      |
| apiKey     | Interfejs API opublikowanego modelu obszaru roboczego.     | Tak      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics połączona usługa
Można utworzyć Data Lake Analytics połączonej usługi, aby połączyć usługę obliczeniową Data Lake Analytics z fabryką danych Azure. Działanie Data Lake Analytics U-SQL w potoku odwołuje się do tej połączonej usługi. 

W poniższej tabeli opisano ogólne właściwości, które są używane w definicji JSON:

| Właściwość                 | Opis                              | Wymagane                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| typ                 | Ustaw Właściwość Type na **AzureDataLakeAnalytics**. | Tak                                      |
| accountName          | Nazwa konta Data Lake Analytics.  | Tak                                      |
| dataLakeAnalyticsUri | Identyfikator URI Data Lake Analytics.           | Nie                                       |
| subscriptionId       | Identyfikator subskrypcji platformy Azure.                    | Nie<br /><br />(Jeśli nie zostanie określony, używana jest subskrypcja usługi Data Factory). |
| resourceGroupName    | Nazwa grupy zasobów platformy Azure.                | Nie<br /><br /> (Jeśli nie zostanie określony, używana jest Grupa zasobów Fabryka danych). |

### <a name="authentication-options"></a>Opcje uwierzytelniania
W przypadku usługi połączonej Data Lake Analytics można wybrać uwierzytelnianie przy użyciu nazwy głównej usługi lub poświadczenia użytkownika.

#### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie jednostki usługi (zalecane)
Aby użyć uwierzytelniania nazwy głównej usługi, zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD). Następnie Udziel dostępu do usługi Azure AD Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [uwierzytelnianie między usługami](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Użyj uwierzytelniania nazwy głównej usługi, określając następujące właściwości:

| Właściwość                | Opis                              | Wymagane |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Identyfikator klienta aplikacji.     | Tak      |
| servicePrincipalKey | Klucz aplikacji.           | Tak      |
| dzierżaw              | Informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w których znajduje się aplikacja. Aby uzyskać te informacje, umieść wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak      |

**Przykład: Uwierzytelnianie jednostki usługi**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Uwierzytelnianie poświadczeń użytkownika
W przypadku uwierzytelniania poświadczeń użytkowników dla Data Lake Analytics określ następujące właściwości:

| Właściwość          | Opis                              | Wymagane |
| :---------------- | :--------------------------------------- | :------- |
| autoryzacja | W edytorze Data Factory wybierz przycisk **Autoryzuj** . Wprowadź poświadczenia, które przypisuje automatycznie wygenerowany adres URL autoryzacji do tej właściwości. | Tak      |
| sessionId     | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być używany tylko raz. To ustawienie jest generowane automatycznie, gdy jest używany Edytor Data Factory. | Tak      |

**Przykład: uwierzytelnianie poświadczeń użytkownika**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Wygaśnięcie tokenu
Kod autoryzacji, który został wygenerowany przez wybranie przycisku **Autoryzuj** po upływie określonego interwału. 

Po wygaśnięciu tokenu uwierzytelniania może zostać wyświetlony następujący komunikat o błędzie: 

  Błąd operacji poświadczeń: invalid_grant-AADSTS70002: Wystąpił błąd podczas weryfikowania poświadczeń. AADSTS70008: podany przydział dostępu wygasł lub został odwołany. Identyfikator śledzenia: identyfikator korelacji d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 sygnatura czasowa: 2015-12-15 21:09:31Z

W poniższej tabeli przedstawiono wygaśnięcia według typu konta użytkownika: 

| Typ użytkownika                                | Wygasa po                            |
| :--------------------------------------- | :--------------------------------------- |
| Konta użytkowników, które *nie* są zarządzane przez usługę Azure AD (Hotmail, Live itd.) | 12 godzin.                                 |
| Konta *użytkowników zarządzane przez* usługę Azure AD | 14 dni od ostatniego uruchomienia wycinka. <br /><br />90 dni, jeśli Plasterek oparty na połączonej usłudze opartej na protokole OAuth jest uruchamiany co najmniej raz na 14 dni. |

Aby uniknąć tego błędu lub rozwiązać ten błąd, należy przeprowadzić autoryzację, wybierając przycisk **Autoryzuj** po wygaśnięciu tokenu. Następnie ponownie Wdróż połączoną usługę. Możesz również generować wartości właściwości **SessionID** i **Authorization** programowo przy użyciu następującego kodu:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Aby uzyskać szczegółowe informacje na temat klas Data Factory, które są używane w tym przykładzie kodu, zobacz:
* [Klasa AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
* [Klasa AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* [Klasa AuthorizationSessionGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse)

Dodaj odwołanie do Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll dla klasy **WindowsFormsWebAuthenticationDialog** . 

## <a name="azure-sql-linked-service"></a>Połączona usługa Azure SQL
Można utworzyć połączoną usługę SQL i użyć jej razem z [działaniem procedury składowanej](data-factory-stored-proc-activity.md) , aby wywołać procedurę składowaną z potoku Data Factory. Aby uzyskać więcej informacji, zobacz [Łącznik usługi Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-synapse-analytics-linked-service"></a>Połączona usługa Azure Synapse Analytics
Możesz utworzyć połączoną usługę Azure Synapse Analytics i użyć jej razem z [działaniem procedury składowanej](data-factory-stored-proc-activity.md) , aby wywołać procedurę składowaną z potoku Data Factory. Aby uzyskać więcej informacji, zobacz [Łącznik usługi Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>SQL Server połączona usługa
Można utworzyć połączoną usługę SQL Server i użyć jej razem z [działaniem procedury składowanej](data-factory-stored-proc-activity.md) , aby wywołać procedurę składowaną z potoku Data Factory. Aby uzyskać więcej informacji, zobacz [SQL Server łącznik](data-factory-sqlserver-connector.md#linked-service-properties).