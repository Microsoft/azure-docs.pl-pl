---
title: Tworzenie pierwszej fabryki danych (PowerShell)
description: W tym samouczku przedstawiono tworzenie przykładowego potoku usługi Azure Data Factory przy użyciu programu Azure PowerShell.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: bbeb87c6e96c75e62fe97db031ae926ce30b6a19
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496351"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>Samouczek: tworzenie pierwszej fabryki danych platformy Azure przy użyciu programu Azure PowerShell
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-build-your-first-pipeline.md)
> * [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [Program PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Szablon Menedżer zasobów](data-factory-build-your-first-pipeline-using-arm.md)
> * [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Szybki start: tworzenie fabryki danych przy użyciu usługi Azure Data Factory](../quickstart-create-data-factory-powershell.md).

Ten artykuł opisuje korzystanie z programu Azure PowerShell w celu utworzenia pierwszej fabryki danych platformy Azure. Aby wykonać instrukcje z tego samouczka przy użyciu innych narzędzi/zestawów SDK, wybierz jedną z opcji z listy rozwijanej.

Potok w tym samouczku zawiera jedno działanie: **działanie Hive usługi HDInsight**. To działanie uruchamia skrypt Hive w klastrze Azure HDInsight, który przekształca dane wejściowe, aby wygenerować dane wyjściowe. Uruchamianie potoku zaplanowano raz w miesiącu między określonym czasem rozpoczęcia i zakończenia. 

> [!NOTE]
> Potok danych przedstawiony w tym samouczku przekształca dane wejściowe w celu wygenerowania danych wyjściowych. Nie kopiuje on danych ze źródłowego do docelowego magazynu danych. Aby zapoznać się z samouczkiem dotyczącym kopiowania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: Kopiowanie danych z usługi Blob Storage do usługi SQL Database).
> 
> Potok może obejmować więcej niż jedno działanie. Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Więcej informacji znajduje się w artykule dotyczącym [planowania i wykonywania w usłudze Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Przeczytanie artykułu [Omówienie samouczka](data-factory-build-your-first-pipeline.md) oraz wykonanie kroków **wymagań wstępnych**.
* Postępuj zgodnie z instrukcjami w artykule [How to install and configure Azure PowerShell](/powershell/azure/) (Instalowanie i konfigurowanie programu Azure PowerShell), aby zainstalować najnowszą wersję programu Azure PowerShell na komputerze.
* (opcjonalnie) Ten artykuł nie obejmuje wszystkich poleceń cmdlet dla usługi Fabryka danych. Pełna dokumentacja dotycząca poleceń cmdlet dla usługi Fabryka danych znajduje się w artykule [Data Factory Cmdlet Reference](/powershell/module/az.datafactory) (Dokumentacja dotycząca poleceń cmdlet dla usługi Fabryka danych).

## <a name="create-data-factory"></a>Tworzenie fabryki danych
W tym kroku opisano użycie programu Azure PowerShell do utworzenia fabryki danych Azure o nazwie **FirstDataFactoryPSH**. Fabryka danych może obejmować jeden lub wiele potoków. Potok może obejmować jedno lub wiele działań. Na przykład działanie kopiowania może służyć do skopiowania danych ze źródła do docelowego magazynu danych, a działanie programu Hive w usłudze HDInsight do uruchomienia skryptu programu Hive, który przekształci dane wejściowe. Zacznijmy tworzenie fabryki danych w tym kroku.

1. Uruchom program Azure PowerShell i uruchom następujące polecenie. Nie zamykaj programu Azure PowerShell, zanim nie wykonasz wszystkich instrukcji z tego samouczka. Jeśli go zamkniesz i otworzysz ponownie, musisz uruchomić te polecenia jeszcze raz.
   * Uruchom poniższe polecenie i wprowadź nazwę użytkownika oraz hasło, których używasz do logowania się w witrynie Azure Portal.
     ```PowerShell
     Connect-AzAccount
     ```    
   * Uruchom poniższe polecenie, aby wyświetlić wszystkie subskrypcje dla tego konta.
     ```PowerShell
     Get-AzSubscription  
     ```
   * Uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować. Ta subskrypcja powinna być taka sama jak używana w witrynie Azure Portal.
     ```PowerShell
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```     
2. Utwórz grupę zasobów platformy Azure o nazwie **ADFTutorialResourceGroup** przez uruchomienie następującego polecenia:
    
    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    W niektórych krokach w tym samouczku zakłada się, że używana jest grupa zasobów o nazwie ADFTutorialResourceGroup. Jeśli używasz innej grupy zasobów, podczas wykonywania instrukcji w tym samouczku trzeba będzie wstawić jej nazwę zamiast nazwy ADFTutorialResourceGroup.
3. Uruchom polecenie cmdlet **New-AzDataFactory** , które tworzy fabrykę danych o nazwie **FirstDataFactoryPSH**.

    ```PowerShell
    New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
   Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli wystąpi błąd **Nazwa fabryki danych „FirstDataFactoryPSH” jest niedostępna**, zmień nazwę (np. TwojaNazwaFirstDataFactoryPSH). Użyj tej nazwy zamiast ADFTutorialFactoryPSH podczas wykonywania kroków w tym samouczku. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.
* Aby tworzyć wystąpienia usługi Fabryka danych, musisz być współautorem/administratorem subskrypcji Azure
* W przyszłości nazwa fabryki danych może zostać zarejestrowana jako nazwa DNS, a wówczas stanie się widoczna publicznie.
* Jeśli zostanie wyświetlony komunikat o błędzie: „**Subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw Microsoft.DataFactory**”, wykonaj jedną z następujących czynności i spróbuj opublikować ponownie:

  * W programie Azure PowerShell uruchom następujące polecenie, aby zarejestrować dostawcę usługi Data Factory:

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Możesz uruchomić następujące polecenie, aby potwierdzić, że dostawca usługi Fabryka danych jest zarejestrowany:

    ```PowerShell
    Get-AzResourceProvider
    ```
  * Zaloguj się przy użyciu subskrypcji Azure do [portalu Azure](https://portal.azure.com) i przejdź do bloku Fabryka danych lub utwórz fabrykę danych w portalu Azure. Ta akcja powoduje automatyczne zarejestrowanie dostawcy.

Przed utworzeniem potoku musisz utworzyć kilka jednostek usługi Fabryka danych. Najpierw utwórz połączone usługi, aby połączyć usługi magazynu danych/usługi obliczeniowe ze swoim magazynem danych, zdefiniuj wejściowe i wyjściowe zestawy danych do reprezentowania danych wejściowych/wyjściowych w połączonych magazynach danych, a następnie utwórz potok zawierający działanie, które korzysta z tych zestawów danych.

## <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku opisano połączenie konta usługi Azure Storage oraz klastra Azure HDInsight na żądanie z fabryką danych. Konto usługi Azure Storage będzie przechowywać dane wejściowe i wyjściowe dla potoku w tym przykładzie. Połączona usługa HDInsight służy do uruchamiania skryptu programu Hive określonego w działaniu potoku w tym przykładzie. Zidentyfikuj usługi magazynu danych/usługi obliczeniowe, które są używane w tym scenariuszu, oraz połącz te usługi z fabryką danych przez utworzenie połączonych usług.

### <a name="create-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
W tym kroku opisano łączenie konta usługi Azure Storage z fabryką danych. Do przechowywania danych wejściowych/wyjściowych oraz pliku skryptu HQL używa się tego samego konta usługi Azure Storage.

1. W folderze C:\ADFGetStarted utwórz plik JSON o nazwie StorageLinkedService.json o następującej zawartości. Utwórz folder ADFGetStarted, jeśli jeszcze nie istnieje.

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }
    ```
    Zastąp wartość **nazwa konta** nazwą konta usługi Azure Storage i **kluczem konta** kluczem dostępu do konta usługi Azure Storage. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
2. W programie PowerShell Azure przejdź do folderu ADFGetStarted.
3. Można użyć polecenia cmdlet **New-AzDataFactoryLinkedService** , które tworzy połączoną usługę. To polecenie cmdlet i inne polecenia cmdlet Data Factory, które są używane w tym samouczku, wymagają przekazania wartości parametrów *ResourceGroupName* i *datafactoryname* . Alternatywnie można użyć polecenia **Get-AzDataFactory** , aby uzyskać obiekt **DataFactory** i przekazać obiekt bez wpisywania *ResourceGroupName* i *datafactoryname* przy każdym uruchomieniu poleceń cmdlet. Uruchom następujące polecenie, aby przypisać dane wyjściowe polecenia cmdlet **Get-AzDataFactory** do zmiennej **$DF** .

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. Teraz uruchom polecenie cmdlet **New-AzDataFactoryLinkedService** , które tworzy połączoną usługę **StorageLinkedService** .

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    Jeśli nie uruchomić polecenie cmdlet **Get-AzDataFactory** i przypiszesz dane wyjściowe do zmiennej **$DF** , trzeba będzie określić wartości parametrów *ResourceGroupName* i *datafactoryname* w następujący sposób.

    ```PowerShell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    Jeśli zamkniesz Azure PowerShell w środku samouczka, musisz uruchomić polecenie cmdlet **Get-AzDataFactory** przy następnym uruchomieniu Azure PowerShell, aby ukończyć samouczek.

### <a name="create-azure-hdinsight-linked-service"></a>Tworzenie połączonej usługi Azure HDInsight
W tym kroku przedstawiono łączenie klastra usługi HDInsight na żądanie z fabryką danych. Klaster usługi HDInsight jest automatycznie tworzony w czasie wykonywania oraz usuwany po zakończeniu przetwarzania i określonym czasie bezczynności. Możesz użyć własnego klastra usługi HDInsight zamiast klastra usługi HDInsight na żądanie. Szczegółowe informacje znajdują się w artykule [Compute Linked Services](data-factory-compute-linked-services.md) (Połączone usługi obliczeniowe).

1. W folderze **C:\ADFGetStarted** utwórz plik JSON o nazwie **HDInsightOnDemandLinkedService**.json o następującej zawartości.

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:

   | Właściwość | Opis |
   |:--- |:--- |
   | clusterSize |Określa rozmiar klastra usługi HDInsight. |
   | timeToLive |Określa czas bezczynności, po którym klaster usługi HDInsight zostanie usunięty. |
   | linkedServiceName |Określa konto magazynu używane do przechowywania dzienników generowanych w usłudze HDInsight. |

    Pamiętaj o następujących kwestiach:

   * Usługa Data Factory tworzy klaster usługi HDInsight **oparty na systemie Linux** za pomocą kodu JSON. Szczegółowe informacje znajdują się w artykule [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Połączona usługa HDInsight na żądanie).
   * Możesz użyć **własnego klastra usługi HDInsight** zamiast korzystać z klastra usługi HDInsight na żądanie. Szczegółowe informacje znajdują się w artykule [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) (Połączona usługa HDInsight).
   * Klaster usługi HDInsight tworzy **kontener domyślny** w magazynie obiektów BLOB określonym w kodzie JSON (**linkedServiceName**). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. Dzięki połączonej usłudze HDInsight na żądanie klaster usługi HDInsight jest tworzony przy każdym przetwarzaniu wycinka, chyba że istnieje aktywny klaster (**TimeToLive**). Klaster jest automatycznie usuwany po zakończeniu przetwarzania.

       Po przetworzeniu większej liczby wycinków w usłudze Azure Blob Storage będzie widocznych wiele kontenerów. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne ze wzorcem: "ADF **twojanazwafabrykidanych nazwapołączonejusługi** - **linkedservicename**-znacznikdatygodziny". Użyj narzędzi, takich jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/) , aby usunąć kontenery w usłudze Azure Blob Storage.

     Szczegółowe informacje znajdują się w artykule [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Połączona usługa HDInsight na żądanie).
2. Uruchom polecenie cmdlet **New-AzDataFactoryLinkedService** , które tworzy połączoną usługę o nazwie HDInsightOnDemandLinkedService.
    
    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku opisano tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych na potrzeby przetwarzania przy użyciu programu Hive. Te zestawy danych dotyczą elementu **StorageLinkedService** utworzonego wcześniej w ramach tego samouczka. Połączona usługa wskazuje konto usługi Azure Storage, a zestawy danych określają kontener, folder i nazwę pliku w magazynie, w którym przechowywane są dane wejściowe i wyjściowe.

### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W folderze **C:\ADFGetStarted** twórz plik JSON o nazwie **InputTable.json** o następującej zawartości:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Ten kod JSON definiuje zestaw danych o nazwie **AzureBlobInput**, który reprezentuje dane wejściowe dla działania w potoku. Ponadto określa, że dane wejściowe znajdują się w kontenerze obiektów blob o nazwie **adfgetstarted** oraz w folderze o nazwie **inputdata**.

    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:

   | Właściwość | Opis |
   |:--- |:--- |
   | typ |Właściwość type jest ustawiona na wartość AzureBlob, ponieważ dane znajdują się w magazynie obiektów blob Azure. |
   | linkedServiceName |Odnosi się do elementu StorageLinkedService utworzonego wcześniej. |
   | fileName |Ta właściwość jest opcjonalna. Jeśli tę właściwość pominiesz, zostaną wybrane wszystkie pliki z folderu folderPath. W tym przypadku zostanie przetworzony tylko plik input.log. |
   | typ |Pliki dziennika są w formacie tekstowym, więc używana jest wartość TextFormat. |
   | columnDelimiter |Kolumny w plikach dziennika są rozdzielane przecinkami (,). |
   | frequency/interval |Właściwość frequency (częstotliwość) jest ustawiona na wartość Month (Miesiąc), a wartość interwału wynosi 1, co oznacza, że wycinki wejściowe są dostępne co miesiąc. |
   | external |Ta właściwość ma wartość true (prawda), jeśli dane wejściowe nie są generowane przez usługę Fabryka danych. |
2. Uruchom następujące polecenie w programie Azure PowerShell, aby utworzyć zestaw danych usługi Data Factory:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Teraz utworzysz wyjściowy zestaw danych do reprezentowania danych wyjściowych przechowywanych w usłudze Azure Blob Storage.

1. W folderze **C:\ADFGetStarted** twórz plik JSON o nazwie **OutputTable.json** o następującej zawartości:

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Ten kod JSON definiuje zestaw danych o nazwie **AzureBlobOutput**, który reprezentuje dane wyjściowe dla działania w potoku. Ponadto określa, że wyniki są przechowywane w kontenerze obiektów blob o nazwie **adfgetstarted** oraz folderze o nazwie **partitioneddata**. W sekcji **availability** (dostępność) określono, że wyjściowy zestaw danych jest generowany co miesiąc.
2. Uruchom następujące polecenie w programie Azure PowerShell, aby utworzyć zestaw danych usługi Data Factory:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>Tworzenie potoku
W tym kroku opisano tworzenie pierwszego potoku za pomocą działania **HDInsightHive**. Wycinek danych wejściowych jest dostępny co miesiąc (frequency: Month, interval: 1), wycinek danych wyjściowych jest generowany co miesiąc, a właściwość scheduler dla działania jest również ustawiona na wartość miesięczną. Ustawienia dla wyjściowego zestawu danych i harmonogramu działania muszą być zgodne. W tym przypadku wyjściowy zestaw danych jest elementem wpływającym na ustawienia harmonogramu, więc musisz utworzyć wyjściowy zestaw danych nawet wtedy, gdy działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. Właściwości użyte w poniższym fragmencie kodu JSON zostały wyjaśnione na końcu tej sekcji.

1. W folderze C:\ADFGetStarted utwórz plik JSON o nazwie MyFirstPipelinePSH.json o następującej zawartości:

   > [!IMPORTANT]
   > Zastąp wartość **storageaccountname** w kodzie JSON nazwą swojego konta magazynu.
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```
    Ten fragment kodu JSON służy do utworzenia potoku obejmującego jedno działanie, które korzysta z programu Hive do przetwarzania danych w klastrze usługi HDInsight.

    Plik skryptu Hive, **partitionweblogs. HQL**, jest przechowywany na koncie usługi Azure Storage (określonym przez elementu scriptlinkedservice, o nazwie **StorageLinkedService**) i w folderze **skryptów** w kontenerze **adfgetstarted**.

    Sekcja **defines** służy do określenia ustawień środowiska uruchomieniowego, które zostaną przekazane do skryptu programu Hive w formie wartości konfiguracyjnych programu Hive (np. ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    Właściwości **start** i **end** potoku określają aktywny okres potoku.

    W kodzie JSON dotyczącym działania określasz, że skrypt programu Hive jest uruchamiany w usłudze obliczeniowej określonej przez właściwość **linkedServiceName** — **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Aby uzyskać szczegółowe informacje na temat właściwości kodu JSON używanych w przykładzie, zobacz sekcję „Pipeline JSON” (Kod JSON potoku) w temacie [Pipelines and activities in Azure Data Factory](data-factory-create-pipelines.md) (Potoki i działania w usłudze Azure Data Factory).

2. Upewnij się, że plik **input.log** jest wyświetlany w folderze **adfgetstarted/inputdata** w magazynie obiektów blob Azure, i uruchom następujące polecenie, aby wdrożyć potok. Ponieważ właściwości **start** i **end** są ustawione na wartość w przeszłości i właściwość **isPaused** została ustawiona na wartość „false”, potok (działanie w potoku) jest uruchamiany natychmiast po wdrożeniu.

    ```PowerShell
    New-AzDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. Gratulacje! Udało Ci się utworzyć pierwszy potok przy użyciu programu Azure PowerShell!

## <a name="monitor-pipeline"></a>Monitorowanie potoku
W tym kroku opisano użycie programu Azure PowerShell do monitorowania tego, co dzieje się w fabryce danych platformy Azure.

1. Uruchom polecenia **Get-AzDataFactory** i przypisz dane wyjściowe do zmiennej **$DF** .

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. Uruchom polecenie **Get-AzDataFactorySlice** , aby uzyskać szczegółowe informacje na temat wszystkich wycinków **EmpSQLTable**, który jest tabelą wyjściową potoku.

    ```PowerShell
    Get-AzDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    Zauważ, że określana tutaj właściwość StartDateTime oznacza taki sam czas rozpoczęcia jak określony w potoku JSON. Oto przykładowe dane wyjściowe:

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. Uruchom **Get-AzDataFactoryRun** , aby uzyskać szczegółowe informacje o uruchomieniach działania dla określonego wycinka.

    ```PowerShell
    Get-AzDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    Oto przykładowe dane wyjściowe: 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    Możesz kontynuować uruchamianie tego polecenia cmdlet do momentu, gdy wycinek będzie widoczny w stanie **Gotowe** lub **Niepowodzenie**. Gdy wycinek będzie w stanie Gotowe, sprawdź folder **partitioneddata** w kontenerze **adfgetstarted** w magazynie obiektów blob pod kątem danych wyjściowych.  Tworzenie klastra usługi HDInsight na żądanie zwykle zajmuje trochę czasu.

    ![Dane wyjściowe](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> Tworzenie klastra usługi HDInsight na żądanie zwykle trwa trochę czasu (około 20 minut). Dlatego należy oczekiwać, że przetworzenie wycinka przez potok zajmie **około 30 minut**.
>
> Po pomyślnym przetworzeniu wycinka plik wejściowy zostanie usunięty. Tak więc, jeśli chcesz ponownie uruchomić wycinek lub ponownie wykonać instrukcje z tego samouczka, przekaż plik wejściowy (input.log) do folderu inputdata kontenera adfgetstarted.
>
>

## <a name="summary"></a>Podsumowanie
W tym samouczku opisano tworzenie fabryki danych Azure do przetwarzania danych przez uruchomienie skryptu programu Hive w klastrze platformy Hadoop w usłudze HDInsight. Użyto Edytora fabryki danych w witrynie Azure Portal, aby:

1. Tworzenie **fabryki danych** Azure.
2. Utworzyć dwie **połączone usługi**:
   1. Połączoną usługę **Azure Storage** w celu połączenia magazynu obiektów blob Azure, w którym przechowywane są pliki wejściowe/wyjściowe, z fabryką danych.
   2. Połączoną usługę **Azure HDInsight** na żądanie w celu połączenia klastra platformy Hadoop w usłudze HDInsight na żądanie z fabryką danych. Usługa Fabryka danych Azure tworzy klaster just in time platformy Hadoop w usłudze HDInsight, aby przetwarzać dane wejściowe i generować dane wyjściowe.
3. Utworzyć dwa **zestawy danych** zawierające dane wejściowe i wyjściowe dla działania programu Hive w usłudze HDInsight w potoku.
4. Utworzyć **potok** za pomocą działania **programu Hive w usłudze HDInsight**.

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie potoku za pomocą działania przekształcania (działanie usługi HDInsight), które uruchamia skrypt programu Hive w klastrze usługi HDInsight platformy Azure na żądanie. Instrukcje dotyczące korzystania z działania kopiowania w celu kopiowania danych z magazynu obiektów blob Azure do usług SQL Azure znajdują się w artykule [Tutorial: Copy data from an Azure Blob to Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: kopiowanie danych z magazynu obiektów blob Azure do usług SQL Azure).

## <a name="see-also"></a>Zobacz też

| Temat | Opis |
|:--- |:--- |
| [Dokumentacja dotycząca poleceń cmdlet usługi Data Factory](/powershell/module/az.datafactory) |Zobacz pełną dokumentację dotyczącą poleceń cmdlet w usłudze Fabryka danych. |
| [Potoki](data-factory-create-pipelines.md) |Ten artykuł ułatwia zapoznanie się z potokami i działaniami w usłudze Azure Data Factory oraz ze sposobem konstruowania za ich pomocą przepływów pracy typu end-to-end opartych na danych na potrzeby scenariusza lub firmy. |
| [Zestawy danych](data-factory-create-datasets.md) |Ten artykuł ułatwia zapoznanie się z zestawami danych w usłudze Azure Data Factory. |
| [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md) |W tym artykule wyjaśniono aspekty planowania i wykonywania modelu aplikacji usługi Fabryka danych Azure. |
| [Monitorowanie potoków i zarządzanie nimi za pomocą aplikacji do monitorowania](data-factory-monitor-manage-app.md) |Ten artykuł zawiera instrukcje dotyczące monitorowania i debugowania potoków oraz zarządzania nimi przy użyciu aplikacji do monitorowania i zarządzania. |
