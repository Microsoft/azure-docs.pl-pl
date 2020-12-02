---
title: Tworzenie pierwszej fabryki danych (Visual Studio)
description: Ten samouczek zawiera instrukcje tworzenia przykładowego potoku dla usługi Azure Data Factory przy użyciu programu Visual Studio.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: vs-azure
ms.date: 01/22/2018
ms.openlocfilehash: b0532e75ac581bad3b5dd8669a591186dc320a86
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496572"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>Samouczek: Tworzenie fabryki danych za pomocą programu Visual Studio
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [Przegląd i wymagania wstępne](data-factory-build-your-first-pipeline.md)
> * [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [Program PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Szablon Menedżer zasobów](data-factory-build-your-first-pipeline-using-arm.md)
> * [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Szybki start: tworzenie fabryki danych przy użyciu usługi Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

W tym samouczku przedstawiono sposób tworzenia Azure Data Factory przy użyciu programu Visual Studio. Utworzysz projekt programu Visual Studio przy użyciu szablonu projektu Data Factory, zdefiniujesz jednostki usługi Data Factory (połączone usługi, zestawy danych i potok) w formacie JSON, a następnie opublikujesz lub wdrożysz te jednostki w chmurze. 

Potok w tym samouczku zawiera jedno działanie: **działanie Hive usługi HDInsight**. To działanie uruchamia skrypt Hive w klastrze Azure HDInsight, który przekształca dane wejściowe, aby wygenerować dane wyjściowe. Uruchamianie potoku zaplanowano raz w miesiącu między określonym czasem rozpoczęcia i zakończenia. 

> [!NOTE]
> W tym samouczku nie pokazano, jak skopiować dane za pomocą usługi Azure Data Factory. Aby zapoznać się z samouczkiem dotyczącym kopiowania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: Kopiowanie danych z usługi Blob Storage do usługi SQL Database).
> 
> Potok może obejmować więcej niż jedno działanie. Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Więcej informacji znajduje się w artykule dotyczącym [planowania i wykonywania w usłudze Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>Przewodnik: Tworzenie i publikowanie jednostek usługi Data Factory
Poniżej przedstawiono kroki do wykonania w ramach tego przewodnika:

1. Utwórz dwie połączone usługi: **AzureStorageLinkedService1** i **HDInsightOnDemandLinkedService1**. 
   
    W tym samouczku zarówno dane wejściowe, jak i wyjściowe dla działania Hive znajdują się w tej samej usłudze Azure Blob Storage. Do przetwarzania istniejących danych wejściowych w celu wygenerowania danych wyjściowych jest używany klaster HDInsight na żądanie. Klaster HDInsight na żądanie jest automatycznie tworzony przez usługę Azure Data Factory w czasie wykonywania, kiedy dane wejściowe są gotowe do przetworzenia. Musisz połączyć magazyny danych lub zasoby obliczeniowe z fabryką danych, aby usługa Data Factory mogła połączyć się z nimi w czasie wykonywania. Dlatego połącz swoje konto usługi Azure Storage z fabryką danych przy użyciu usługi AzureStorageLinkedService1 i połącz klaster HDInsight na żądanie przy użyciu usługi HDInsightOnDemandLinkedService1. Podczas publikowania określ nazwę fabryki danych do utworzenia lub istniejącej fabryki danych.  
2. Utwórz dwa zestawy danych — **InputDataset** i **OutputDataset** — reprezentujące dane wejściowe i wyjściowe przechowywane w magazynie obiektów blob Azure Blob Storage. 
   
    Te definicje zestawów danych odwołują się do połączonej usługi Azure Storage utworzonej w poprzednim kroku. Dla zestawu danych InputDataset należy określić kontener obiektów blob (adfgetstarted) i folder (inputdata), który zawiera obiekt blob z danymi wejściowymi. Dla zestawu danych OutputDataset należy określić kontener obiektów blob (adfgetstarted) i folder (partitioneddata), który przechowuje dane wyjściowe. Należy określić również inne właściwości, takie jak struktura, dostępność i zasady.
3. Utwórz potok o nazwie **MyFirstPipeline**. 
  
    W tym przewodniku potok zawiera tylko jedno działanie: **działanie Hive usługi HDInsight**. To działanie przekształca dane wejściowe w celu wygenerowania danych wyjściowych przez uruchomienie skryptu Hive na klastrze HDInsight na żądanie. Aby dowiedzieć się więcej na temat działania Hive, zobacz [Hive Activity](data-factory-hive-activity.md) (Działanie Hive) 
4. Utwórz fabrykę danych o nazwie **DataFactoryUsingVS**. Wdróż fabrykę danych i wszystkie obiekty usługi Data Factory (połączone usługi, tabele i potok).
5. Po opublikowaniu będziesz monitorować potok za pomocą bloków witryny Azure Portal i aplikacji do monitorowania i zarządzania. 
  
### <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Przeczytanie artykułu [Omówienie samouczka](data-factory-build-your-first-pipeline.md) oraz wykonanie kroków **wymagań wstępnych**. Aby przejść do artykułu, możesz również wybrać opcję **Przegląd i wymagania wstępne** z listy rozwijanej w górnej części strony. Po spełnieniu wymagań wstępnych przejdź z powrotem do tego artykułu, wybierając opcję **Visual Studio** z listy rozwijanej.
2. Aby utworzyć wystąpienia usługi Data Factory, użytkownik musi być członkiem roli [współautora usługi Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.  
3. Na komputerze muszą być zainstalowane następujące elementy:
   * Visual Studio 2013 lub Visual Studio 2015
   * Pobierz zestaw Azure SDK dla programu Visual Studio 2013 lub Visual Studio 2015. Przejdź do [strony plików do pobrania Azure](https://azure.microsoft.com/downloads/) i kliknij pozycję **VS 2013** lub **VS 2015** w sekcji **.NET**.
   * Pobierz najnowszą wtyczkę usługi Azure Data Factory dla programu Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) lub [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Możesz także zaktualizować wtyczkę, wykonując następujące czynności: w menu kliknij kolejno opcje **Narzędzia**  ->  **rozszerzenia i aktualizacje**  ->  **online**  ->  **Galeria Visual Studio**  ->  **Microsoft Azure Data Factory Tools for Visual Studio**  ->  **Update**.

Teraz Użyjmy programu Visual Studio, aby utworzyć Azure Data Factory.

### <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio
1. Uruchom program **Visual Studio 2013** lub **Visual Studio 2015**. Kliknij pozycję **Plik**, wskaż polecenie **Nowy** i kliknij pozycję **Projekt**. Powinno zostać wyświetlone okno dialogowe **Nowy projekt**.  
2. W oknie dialogowym **Nowy projekt** wybierz szablon **DataFactory** i kliknij pozycję **Pusty projekt usługi Fabryka danych**.   

    ![Okno dialogowe Nowy projekt](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Wprowadź **nazwę** dla projektu, **lokalizację** oraz nazwę **rozwiązania** i kliknij przycisk **OK**.

    ![Eksplorator rozwiązań](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku utworzysz dwie połączone usługi: **Azure Storage** i **HDInsight na żądanie**. 

Połączona usługa Azure Storage łączy Twoje konto usługi Azure Storage z fabryką danych przez udostępnienie informacji o połączeniu. Usługa Data Factory używa parametrów połączenia z ustawienia połączonej usługi, aby nawiązać połączenie z usługą Azure Storage w czasie wykonywania. Ten magazyn przechowuje dane wejściowe i wyjściowe dla potoku oraz plik skryptu Hive używany przez działanie Hive. 

Za pomocą połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest automatycznie tworzony w czasie wykonywania, kiedy dane wejściowe są gotowe do przetworzenia. Klaster jest usuwany po zakończeniu przetwarzania i określonym czasie bezczynności. 

> [!NOTE]
> Fabrykę danych możesz utworzyć, określając jej nazwę i ustawienia w czasie publikowania rozwiązania usługi Data Factory.

#### <a name="create-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage
1. Kliknij prawym przyciskiem myszy pozycję **Połączone usługi** w Eksploratorze rozwiązań, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.      
2. W oknie dialogowym **Dodawanie nowego elementu** wybierz z listy pozycję **Połączona usługa Azure Storage** i kliknij przycisk **Dodaj**.
    ![Zrzut ekranu, który wyróżnia połączoną usługę Azure Storage na liście.](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. Zastąp wartości `<accountname>` i `<accountkey>` nazwą konta usługi Azure Storage oraz jego kluczem. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
    ![Połączona usługa Azure Storage](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Zapisz plik **AzureStorageLinkedService1.json**.

#### <a name="create-azure-hdinsight-linked-service"></a>Tworzenie połączonej usługi Azure HDInsight
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Połączone usługi**, wskaż polecenie **Dodaj** i kliknij opcję **Nowy element**.
2. Wybierz pozycję **Połączona usługa HDInsight na żądanie** i kliknij przycisk **Dodaj**.
3. Zastąp kod **JSON** następującym kodem JSON:

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
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:

    Właściwość | Opis
    -------- | ----------- 
    ClusterSize | Określa rozmiar klastra usługi HDInsight na platformie Hadoop.
    TimeToLive | Określa czas bezczynności, po którym klaster usługi HDInsight zostanie usunięty.
    linkedServiceName | Określa konto magazynu używane do przechowywania dzienników generowanych przez klaster usługi HDInsight na platformie Hadoop. 

    > [!IMPORTANT]
    > Klaster usługi HDInsight tworzy **kontener domyślny** w magazynie obiektów blob określonym w kodzie JSON (linkedServiceName). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. W przypadku połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest tworzony przy każdym przetwarzaniu wycinka — o ile w tym momencie nie istnieje aktywny klaster (timeToLive). Klaster jest automatycznie usuwany po zakończeniu przetwarzania.
    > 
    > Po przetworzeniu większej liczby wycinków w usłudze Azure Blob Storage będzie widocznych wiele kontenerów. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp`. Użyj narzędzi, takich jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/) , aby usunąć kontenery w usłudze Azure Blob Storage.

    Więcej informacji na temat właściwości kodu JSON znajduje się w artykule [Compute linked services](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Połączone usługi na potrzeby obliczeń). 
4. Zapisz plik **HDInsightOnDemandLinkedService1.json**.

### <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku opisano tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych na potrzeby przetwarzania przy użyciu programu Hive. Te zestawy danych dotyczą elementu **AzureStorageLinkedService1** utworzonego wcześniej w ramach tego samouczka. Połączona usługa wskazuje konto usługi Azure Storage, a zestawy danych określają kontener, folder i nazwę pliku w magazynie, w którym przechowywane są dane wejściowe i wyjściowe.   

#### <a name="create-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Tabele**, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.
2. Wybierz pozycję **Obiekt blob platformy Azure** z listy, zmień nazwę pliku na **InputDataSet.json** i kliknij przycisk **Dodaj**.
3. Zastąp kod **JSON** w edytorze następującym fragmentem kodu JSON:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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
    Ten fragment kodu JSON definiuje zestaw danych o nazwie **AzureBlobInput**, który reprezentuje dane wejściowe dla działania Hive w potoku. Określasz, że dane wejściowe znajdują się w kontenerze obiektów blob o nazwie `adfgetstarted` oraz folderze o nazwie `inputdata`.

    Poniższa tabela zawiera opis właściwości kodu JSON użytych w tym fragmencie kodu:

    Właściwość | Opis |
    -------- | ----------- |
    typ |Właściwość type jest ustawiona na wartość **AzureBlob**, ponieważ dane znajdują się w usłudze Azure Blob Storage.
    linkedServiceName | Odnosi się do utworzonej wcześniej usługi AzureStorageLinkedService1.
    fileName |Ta właściwość jest opcjonalna. Jeśli tę właściwość pominiesz, zostaną wybrane wszystkie pliki z folderu folderPath. W tym przypadku zostanie przetworzony tylko plik input.log.
    typ | Pliki dziennika są w formacie tekstowym, więc używana jest wartość TextFormat. |
    columnDelimiter | Kolumny w plikach dziennika są rozdzielane przecinkami (`,`)
    frequency/interval | Właściwość frequency (częstotliwość) jest ustawiona na wartość Month (Miesiąc), a wartość interwału wynosi 1, co oznacza, że wycinki wejściowe są dostępne co miesiąc.
    external | Ta właściwość ma wartość true, jeśli dane wejściowe dla tego działania nie są generowane przez potok. Ta właściwość jest określana tylko w przypadku wejściowych zestawów danych. Dla wejściowego zestawu danych pierwszego działania zawsze ustaw ją na wartość true.
4. Zapisz plik **InputDataset.json**.

#### <a name="create-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Teraz utworzysz wyjściowy zestaw danych do reprezentowania danych wyjściowych przechowywanych w usłudze Azure Blob Storage.

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **tabele**, wskaż polecenie **Dodaj**, a następnie kliknij pozycję **nowy element**.
2. Wybierz pozycję **Obiekt blob platformy Azure** z listy, zmień nazwę pliku na **OutputDataset.json** i kliknij przycisk **Dodaj**.
3. Zastąp kod **JSON** w edytorze następującym kodem JSON:
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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
    Fragment kodu JSON definiuje zestaw danych o nazwie **AzureBlobOutput**, który reprezentuje dane wyjściowe generowane przez działanie Hive w potoku. Określasz, że dane wyjściowe tworzone przez działanie Hive są umieszczane w kontenerze obiektów blob o nazwie `adfgetstarted` oraz folderze o nazwie `partitioneddata`. 
    
    W sekcji **availability** (dostępność) określono, że wyjściowy zestaw danych jest generowany co miesiąc. Wyjściowy zestaw danych kieruje harmonogramem potoku. Potok jest uruchamiany co miesiąc między czasem rozpoczęcia i zakończenia. 

    Opisy tych właściwości znajdują się w części **Tworzenie wejściowego zestawu danych**. Dla wyjściowego zestawu danych nie ustawia się właściwości external, ponieważ zestaw danych jest generowany przez potok.
4. Zapisz plik **OutputDataset.json**.

### <a name="create-pipeline"></a>Tworzenie potoku
Do tej pory utworzono połączoną usługę Azure Storage oraz wejściowy i wyjściowy zestaw danych. Teraz utworzysz potok z działaniem **HDInsightHive**. **Dane wejściowe** dla działania Hive mają wartość **AzureBlobInput**, a **dane wyjściowe** — **AzureBlobOutput**. Wycinek wejściowego zestawu danych jest dostępny co miesiąc (frequency: Month, interval: 1). Wycinek wyjściowy również jest generowany co miesiąc. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Potoki**, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.
2. Wybierz pozycję **Potok przekształcenia programu Hive** z listy i kliknij przycisk **Dodaj**.
3. Zastąp kod **JSON** następującym fragmentem kodu:

    > [!IMPORTANT]
    > Zastąp wartość `<storageaccountname>` nazwą konta magazynu.

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
                        "scriptLinkedService": "AzureStorageLinkedService1",
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
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > Zastąp wartość `<storageaccountname>` nazwą konta magazynu.

    Fragment kodu JSON definiuje potok, który składa się z jednego działania (działanie Hive). To działanie uruchamia skrypt Hive w celu przetworzenia danych wejściowych na klastrze usługi HDInsight na żądanie, aby wygenerować dane wyjściowe. W sekcji działań kodu JSON potoku w tablicy jest widoczne tylko jedno działanie o typie **HDInsightHive**. 

    We właściwościach typu specyficznych dla działania Hive usługi HDInsight należy określić połączoną usługę Azure Storage, która zawiera plik skryptu Hive, ścieżkę do tego pliku skryptu i jego parametry. 

    Plik skryptu Hive **partitionweblogs.hql** jest przechowywany na koncie usługi Azure Storage (określonym za pomocą właściwości scriptLinkedService) i w folderze `script` w kontenerze `adfgetstarted`.

    Sekcja `defines` służy do określania ustawień środowiska uruchomieniowego, które są przekazywane do skryptu Hive jako wartości konfiguracyjne magazynu Hive (np. `${hiveconf:inputtable}` i `${hiveconf:partitionedtable})`.

    Właściwości **start** i **end** potoku określają aktywny okres potoku. Skonfigurowano comiesięczne generowanie zestawu danych, dlatego potok wygeneruje tylko jeden wycinek (ponieważ miesiąc jest taki sam w dacie rozpoczęcia i zakończenia).

    W kodzie JSON dotyczącym działania określasz, że skrypt programu Hive jest uruchamiany w usłudze obliczeniowej określonej przez właściwość **linkedServiceName** — **HDInsightOnDemandLinkedService**.
4. Zapisz plik **HiveActivity1.json**.

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Dodawanie plików partitionweblogs.hql i input.log jako zależności
1. Kliknij prawym przyciskiem myszy pozycję **Zależności** w oknie **Eksplorator rozwiązań**, wskaż polecenie **Dodaj** i kliknij pozycję **Istniejący element**.  
2. Przejdź do folderu **C:\ADFGettingStarted** i wybierz pliki **partitionweblogs.hql****input.log**, a następnie kliknij przycisk **Dodaj**. Te dwa pliki zostały utworzone w ramach wymagań wstępnych z części [Omówienie samouczka](data-factory-build-your-first-pipeline.md).

Podczas publikowania rozwiązania w następnym kroku plik **partitionweblogs.hql** zostanie przekazany do folderu **script** w kontenerze obiektów blob `adfgetstarted`.   

### <a name="publishdeploy-data-factory-entities"></a>Publikowanie/wdrażanie jednostek usługi Fabryka danych
W tym kroku opublikujesz jednostki usługi Data Factory (połączone usługi, zestawy danych i potok) w swoim projekcie w usłudze Azure Data Factory. Podczas publikowania określisz nazwę fabryki danych. 

1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, a następnie kliknij polecenie **Publikuj**.
2. Jeśli zostanie wyświetlone okno dialogowe **Logowanie na koncie Microsoft** wprowadź poświadczenia dla konta z subskrypcją Azure i kliknij przycisk **Zaloguj**.
3. Powinno zostać wyświetlone następujące okno dialogowe:

   ![Okno dialogowe publikowania](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. Na stronie **Konfigurowanie fabryki danych** wykonaj następujące czynności:

    ![Publikowanie — ustawienia nowej fabryki danych](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. Zaznacz opcję **Utwórz nową fabrykę danych**.
   2. Wprowadź unikatową **nazwę** fabryki danych. Na przykład: **DataFactoryUsingVS09152016**. Nazwa musi być unikatowa w skali globalnej.
   3. Wybierz odpowiednią subskrypcję w polu **Subskrypcja**. 
        > [!IMPORTANT]
        > Jeśli nie jest widoczna żadna subskrypcja, upewnij się, że do logowania zostało użyte konto o uprawnieniach administratora lub współadministratora subskrypcji.
   4. Wybierz **grupę zasobów** dla fabryki danych, która ma być utworzona.
   5. Wybierz **region** dla fabryki danych.
   6. Kliknij przycisk **Dalej**, aby przejść na stronę **Publikowanie elementów**. (Naciśnij przycisk **TAB**, aby wyjść z pola nazwy, jeśli przycisk **Dalej** jest wyłączony).

      > [!IMPORTANT]
      > Jeśli podczas publikowania wystąpi błąd **Nazwa fabryki danych „DataFactoryUsingVS” jest niedostępna**, zmień nazwę (np. TwojaNazwaDataFactoryUsingVS). Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.   
1. Na stronie **Publikowanie elementów** upewnij się, że wszystkie jednostki usługi Fabryka danych zostały wybrane, i kliknij przycisk **Dalej**, aby przejść na stronę **Podsumowanie**.

    ![Strona publikowania elementów](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. Przejrzyj podsumowanie i kliknij przycisk **Dalej**, aby rozpocząć proces wdrożenia oraz wyświetlić stronę **Stan wdrożenia**.

    ![Strona podsumowania](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. Na stronie **Stan wdrożenia** powinien zostać wyświetlony stan procesu wdrożenia. Po zakończeniu wdrożenia kliknij przycisk Zakończ.

Ważne rzeczy, na które należy zwrócić uwagę:

- Jeśli wystąpi błąd: **Ta subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw Microsoft. DataFactory**, wykonaj jedną z następujących czynności i spróbuj opublikować ponownie:
    - W programie Azure PowerShell uruchom następujące polecenie, aby zarejestrować dostawcę usługi Fabryka danych.
        ```powershell   
        Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        Można uruchomić następujące polecenie, aby potwierdzić, że dostawca usługi Data Factory jest zarejestrowany.

        ```powershell
        Get-AzResourceProvider
        ```
    - Zaloguj się przy użyciu subskrypcji Azure w witrynie [Azure Portal](https://portal.azure.com) i przejdź do bloku Data Factory lub utwórz fabrykę danych w witrynie Azure Portal. Ta akcja powoduje automatyczne zarejestrowanie dostawcy.
- W przyszłości nazwa fabryki danych może zostać zarejestrowana jako nazwa DNS, a wówczas stanie się widoczna publicznie.
- Aby tworzyć wystąpienia Fabryki danych, musisz być administratorem lub współadministratorem subskrypcji platformy Azure

### <a name="monitor-pipeline"></a>Monitorowanie potoku
W tym kroku będziesz monitorować potok przy użyciu widoku diagramu fabryki danych. 

#### <a name="monitor-pipeline-using-diagram-view"></a>Monitorowanie potoku przy użyciu widoku diagramu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) i wykonaj następujące kroki:
   1. Kliknij kolejno pozycje **Więcej usług** i **Fabryki danych**.
       
        ![Przeglądanie fabryk danych](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Wybierz nazwę fabryki danych (na przykład: **DataFactoryUsingVS09152016**) z listy fabryk danych.
   
       ![Wybór fabryki danych](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. Na stronie głównej fabryki danych kliknij przycisk **Diagram**.

    ![Kafelek Diagram](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. Na stronie Widok diagramu zostanie wyświetlony przegląd potoków i zestawów danych używanych w tym samouczku.

    ![Widok diagramu](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. Aby wyświetlić wszystkie działania w potoku, kliknij prawym przyciskiem myszy potok w diagramie i kliknij polecenie Otwórz potok.

    ![Menu Otwórz potok](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Upewnij się, że działanie HDInsightHive jest widoczne w potoku.

    ![Widok Otwórz potok](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Aby przejść z powrotem do poprzedniego widoku, kliknij pozycję **Fabryka danych** w menu linków do stron nadrzędnych u góry.
6. Na stronie **Widok diagramu** kliknij dwukrotnie zestaw danych **AzureBlobInput**. Sprawdź, czy wycinek jest w stanie **Gotowe**. Może potrwać kilka minut, zanim wycinek zostanie wyświetlony ze stanem Gotowe. Jeśli poczekasz jakiś czas i tak się nie stanie, sprawdź, czy plik wejściowy (input.log) znajduje się w odpowiednim kontenerze (`adfgetstarted`) i folderze (`inputdata`). Upewnij się także, że właściwość **external** w wejściowym zestawie danych ma wartość **true**. 

   ![Wycinek danych wejściowych w stanie gotowości](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Kliknij przycisk **X**, aby zamknąć blok **AzureBlobInput**.
8. Na stronie **Widok diagramu** kliknij dwukrotnie zestaw danych **AzureBlobOutput**. Zostanie wyświetlony wycinek, który jest obecnie przetwarzany.

   ![Zrzut ekranu, który podświetla zestaw danych AzureBlobOutput.](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Po zakończeniu przetwarzania wycinek będzie mieć stan **Gotowe**.

   > [!IMPORTANT]
   > Tworzenie klastra usługi HDInsight na żądanie zwykle trwa trochę czasu (około 20 minut). Dlatego należy oczekiwać, że przetworzenie wycinka przez potok zajmie **około 30 minut**.  
   
    ![Zestaw danych](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. Gdy wycinek będzie w stanie **Gotowe**, sprawdź folder `partitioneddata` w kontenerze `adfgetstarted` w magazynie obiektów blob pod kątem danych wyjściowych.  

    ![Dane wyjściowe](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Kliknij wycinek, aby wyświetlić szczegółowe informacje na jego temat w bloku **Wycinek danych**.

    ![Szczegóły wycinka danych](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Kliknij uruchomienie działania na **liście uruchomień działań**, aby zobaczyć szczegóły dotyczące uruchamiania działania (w tym scenariuszu działanie Hive) w oknie **Szczegóły uruchamiania działania**. 
  
    ![Szczegóły uruchamiania działania](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    W plikach dziennika zobaczysz zapytanie Hive, które zostało wykonane, oraz informacje o stanie. Dzienniki te są przydatne w przypadku rozwiązywania różnych problemów.  

Zobacz artykuł [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) (Monitorowanie zestawów danych i potoku), aby uzyskać instrukcje dotyczące korzystania z witryny Azure Portal do monitorowania potoku i zestawów danych utworzonych przez siebie w ramach tego samouczka.

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorowanie potoku przy użyciu aplikacji Monitorowanie i zarządzanie
Do monitorowania potoków danych możesz też użyć aplikacji Monitorowanie i zarządzanie. Szczegółowe informacje dotyczące korzystania z aplikacji znajdują się w artykule [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App](data-factory-monitor-manage-app.md) (Monitorowanie potoków usługi Azure Data Factory oraz zarządzanie nimi za pomocą aplikacji Monitorowanie i zarządzanie).

1. Kliknij kafelek Monitorowanie i zarządzanie.

    ![Kafelek Monitorowanie i zarządzanie](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. Powinna zostać wyświetlona aplikacja Monitorowanie i zarządzanie. Zmień **godzinę rozpoczęcia** i **godzinę zakończenia** na godzinę rozpoczęcia (04-01-2016 12:00:00) i godzinę zakończenia (2016-04-02 12:00:00) potoku i kliknij przycisk **Zastosuj**.

    ![Aplikacja Monitorowanie i zarządzanie](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Wybierz okno działania z listy **Okna działania**, aby zobaczyć szczegółowe informacje na jego temat.
    ![Szczegóły okna działania](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> Po pomyślnym przetworzeniu wycinka plik wejściowy zostanie usunięty. Tak więc, jeśli chcesz ponownie uruchomić wycinek lub ponownie wykonać instrukcje z tego samouczka, przekaż plik wejściowy (input.log) do folderu `inputdata` kontenera `adfgetstarted`.

### <a name="additional-notes"></a>Uwagi dodatkowe
- Fabryka danych może obejmować jeden lub wiele potoków. Potok może obejmować jedno lub wiele działań. Na przykład działanie kopiowania może służyć do skopiowania danych ze źródła do docelowego magazynu danych, a działanie programu Hive w usłudze HDInsight do uruchomienia skryptu programu Hive, który przekształci dane wejściowe. Artykuł [supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Obsługiwane magazyny danych) zawiera listę wszystkich źródeł i ujść obsługiwanych przez działanie kopiowania. Artykuł [compute linked services](data-factory-compute-linked-services.md) (Obliczanie połączonych usług) zawiera listę usług obliczeniowych obsługiwanych przez usługę Data Factory.
- Połączone usługi łączą magazyny danych lub usługi obliczeniowe z Azure Data Factory. Artykuł [supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Obsługiwane magazyny danych) zawiera listę wszystkich źródeł i ujść obsługiwanych przez działanie kopiowania. Artykuł [Compute linked services](data-factory-compute-linked-services.md) (Połączone usługi na potrzeby obliczeń) zawiera listę usług obliczeniowych obsługiwanych przez usługę Data Factory i listę [działań przekształcania](data-factory-data-transformation-activities.md), które mogą być w nich wykonywane.
- Zobacz [Move data from/to Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) (Przenoszenie danych z/do obiektów blob Azure), aby uzyskać szczegółowe informacje na temat właściwości JSON używanych w definicji połączonej usługi Azure Storage.
- Możesz użyć własnego klastra usługi HDInsight zamiast klastra usługi HDInsight na żądanie. Szczegółowe informacje znajdują się w artykule [Compute Linked Services](data-factory-compute-linked-services.md) (Połączone usługi obliczeniowe).
-  Usługa Data Factory tworzy klaster usługi HDInsight **oparty na systemie Linux** za pomocą powyższego kodu JSON. Szczegółowe informacje znajdują się w artykule [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Połączona usługa HDInsight na żądanie).
- Klaster usługi HDInsight tworzy **kontener domyślny** w magazynie obiektów blob określonym w kodzie JSON (linkedServiceName). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. W przypadku połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest tworzony przy każdym przetwarzaniu wycinka — o ile w tym momencie nie istnieje aktywny klaster (timeToLive). Klaster jest automatycznie usuwany po zakończeniu przetwarzania.
    
    Po przetworzeniu większej liczby wycinków w usłudze Azure Blob Storage będzie widocznych wiele kontenerów. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne z następującym wzorcem: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Użyj narzędzi, takich jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/) , aby usunąć kontenery w usłudze Azure Blob Storage.
- W tym przypadku wyjściowy zestaw danych jest elementem wpływającym na ustawienia harmonogramu, więc musisz utworzyć wyjściowy zestaw danych nawet wtedy, gdy działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. 
- W tym samouczku nie pokazano, jak skopiować dane za pomocą usługi Azure Data Factory. Aby zapoznać się z samouczkiem dotyczącym kopiowania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: Kopiowanie danych z usługi Blob Storage do usługi SQL Database).


## <a name="use-server-explorer-to-view-data-factories"></a>Korzystanie z Eksploratora serwera w celu wyświetlania fabryk danych
1. W programie **Visual Studio** kliknij w menu pozycję **Widok**, a następnie kliknij pozycję **Eksplorator serwera**.
2. W oknie Eksploratora serwera rozwiń węzeł **Azure**, a następnie węzeł **Fabryka danych**. Jeśli zostanie wyświetlony monit **Zaloguj się do programu Visual Studio**, wprowadź **konto** skojarzone z subskrypcją Azure i kliknij przycisk **Kontynuuj**. Wprowadź **hasło** i kliknij przycisk **Zaloguj**. Program Visual Studio podejmie próbę uzyskania informacji na temat wszystkich fabryk danych Azure w ramach danej subskrypcji. Stan tej operacji zostanie wyświetlony w oknie **Lista zadań usługi Data Factory**.

    ![Eksplorator serwera](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Kliknij prawym przyciskiem myszy fabrykę danych i wybierz opcję **Eksportuj fabrykę danych do nowego projektu**, aby utworzyć projekt w programie Visual Studio na podstawie istniejącej fabryki danych.

    ![Eksportowanie fabryki danych](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Aktualizacja narzędzi usługi Fabryka danych dla programu Visual Studio
Aby zaktualizować narzędzia usługi Fabryka danych Azure dla programu Visual Studio, wykonaj następujące czynności:

1. W menu kliknij pozycję **Narzędzia** i wybierz pozycję **Rozszerzenia i aktualizacje**.
2. Wybierz pozycję **Aktualizacje** w lewym okienku, a następnie wybierz pozycję **Galeria Visual Studio**.
3. Wybierz pozycję **Narzędzia usługi Fabryka danych Azure dla programu Visual Studio** i kliknij przycisk **Aktualizuj**. Jeśli ta pozycja nie jest wyświetlana, masz już najnowszą wersję narzędzi.

## <a name="use-configuration-files"></a>Korzystanie z plików konfiguracji
Plików konfiguracji w programie Visual Studio można użyć do konfigurowania właściwości połączonych usług/tabel/potoków w różny sposób dla poszczególnych środowisk.

Weź pod uwagę poniższą definicję kodu JSON dotyczącą połączonej usługi Azure Storage. Chcesz określić parametr **connectionString** za pomocą różnych wartości elementów accountname i accountkey dla różnych środowisk (tworzenia/testowania/produkcji), w których wdrażasz jednostki usługi Fabryka danych. Możesz uzyskać takie zachowanie, stosując oddzielny plik konfiguracji dla każdego środowiska.

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

### <a name="add-a-configuration-file"></a>Dodawanie pliku konfiguracji
Dodaj plik konfiguracji dla każdego środowiska, wykonując następujące czynności:   

1. Kliknij prawym przyciskiem myszy projekt usługi Fabryka danych w rozwiązaniu Visual Studio, wskaż polecenie **Dodaj** i kliknij pozycję **Nowy element**.
2. Wybierz pozycję **Konfiguracja** z listy zainstalowanych szablonów po lewej stronie, wybierz opcję **Plik konfiguracji**, wprowadź **nazwę** pliku konfiguracji, a następnie kliknij przycisk **Dodaj**.

    ![Dodawanie pliku konfiguracji](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Dodaj parametry konfiguracji oraz ich wartości w poniższym formacie:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    W tym przykładzie opisano konfigurację właściwości connectionString połączonej usługi Azure Storage oraz połączonej usługi SQL Azure. Zwróć uwagę, że do określania nazwy jest używana składnia [JsonPath](https://goessner.net/articles/JsonPath/).   

    Jeśli kod JSON zawiera właściwość obejmującą tablicę wartości, jak pokazano poniżej:  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Skonfiguruj właściwości, jak pokazano w następującym pliku konfiguracji (użyj indeksowania rozpoczynającego się od zera):

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Nazwy właściwości zawierające spacje
Jeśli nazwa właściwości zawiera spacje, użyj nawiasów kwadratowych, jak pokazano w poniższym przykładzie (Database server name):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Wdrożenie rozwiązania przy użyciu konfiguracji
Podczas publikowania jednostek usługi Fabryka danych Azure w programie VS możesz określić konfigurację, której chcesz użyć dla tej operacji publikowania.

Aby opublikować jednostki w projekcie usługi Azure Data Factory przy użyciu pliku konfiguracji:   

1. Kliknij prawym przyciskiem myszy projekt usługi Fabryka danych i kliknij polecenie **Publikuj**, aby wyświetlić okno dialogowe **Publikowanie elementów**.
2. Wybierz istniejącą fabrykę danych lub określ wartości do tworzenia fabryki danych na stronie **Konfigurowanie fabryki danych** i kliknij przycisk **Dalej**.   
3. Na stronie **Publikowanie elementów** dla pola **Wybierz konfigurację wdrożenia** zostanie wyświetlona lista rozwijana z dostępnymi konfiguracjami.

    ![Wybieranie pliku konfiguracji](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Wybierz **plik konfiguracji**, którego chcesz użyć, i kliknij przycisk **Dalej**.
5. Upewnij się, że nazwa pliku JSON jest wyświetlana na stronie **Podsumowanie**, i kliknij przycisk **Dalej**.
6. Kliknij przycisk **Zakończ** po zakończeniu operacji wdrożenia.

Podczas wdrażania wartości z pliku konfiguracji służą do ustawiania wartości właściwości w plikach JSON przed wdrożeniem jednostek w usłudze Azure Data Factory.   

## <a name="use-azure-key-vault"></a>Korzystanie z rozwiązania Azure Key Vault
Przekazywanie poufnych danych (na przykład parametrów połączeń) do repozytorium kodu jest niezalecane, a często nawet sprzeczne z zasadami zabezpieczeń. Zobacz przykład [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFSecurePublish) w witrynie GitHub, aby dowiedzieć się więcej o przechowywaniu poufnych informacji w usłudze Azure Key Vault i korzystaniu z nich podczas publikowania jednostek usługi Data Factory. Rozszerzenie Secure Publish dla programu Visual Studio umożliwia przechowywanie kluczy tajnych w usłudze Key Vault, a w połączonych usługach/konfiguracjach wdrażania są określane tylko odwołania do tych kluczy. Te odwołania są rozpoznawane podczas publikowania jednostek usługi Data Factory na platformie Azure. Te pliki można następnie przekazać do repozytorium źródłowego bez ujawniania jakichkolwiek kluczy tajnych.

## <a name="summary"></a>Podsumowanie
W tym samouczku utworzono Azure Data Factory, aby przetwarzać dane przez uruchomienie skryptu programu Hive w klastrze usługi HDInsight Hadoop. Użyto Edytora fabryki danych w witrynie Azure Portal, aby:  

1. Tworzenie **fabryki danych** Azure.
2. Utworzyć dwie **połączone usługi**:
   1. Połączoną usługę **Azure Storage** w celu połączenia magazynu obiektów blob Azure, w którym przechowywane są pliki wejściowe/wyjściowe, z fabryką danych.
   2. Połączoną usługę **Azure HDInsight** na żądanie w celu połączenia klastra platformy Hadoop w usłudze HDInsight na żądanie z fabryką danych. Usługa Fabryka danych Azure tworzy klaster just in time platformy Hadoop w usłudze HDInsight, aby przetwarzać dane wejściowe i generować dane wyjściowe.
3. Utworzyć dwa **zestawy danych** zawierające dane wejściowe i wyjściowe dla działania programu Hive w usłudze HDInsight w potoku.
4. Utworzyć **potok** za pomocą działania **programu Hive w usłudze HDInsight**.  

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie potoku za pomocą działania przekształcenia (działanie usługi HDInsight), które uruchamia skrypt programu Hive w klastrze usługi HDInsight na żądanie. Aby dowiedzieć się, jak używać działania kopiowania do kopiowania danych z obiektu blob platformy Azure do usługi Azure SQL, zobacz [Samouczek: kopiowanie danych z obiektu blob platformy Azure do usługi Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Szczegółowe informacje znajdują się w artykule [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Planowanie i wykonywanie w usłudze Data Factory). 


## <a name="see-also"></a>Zobacz też

| Temat | Opis |
|:--- |:--- |
| [Potoki](data-factory-create-pipelines.md) |Ten artykuł ułatwia zapoznanie się z potokami i działaniami w usłudze Azure Data Factory oraz ze sposobem konstruowania za ich pomocą przepływów pracy opartych na danych na potrzeby scenariusza lub firmy. |
| [Zestawy danych](data-factory-create-datasets.md) |Ten artykuł ułatwia zapoznanie się z zestawami danych w usłudze Azure Data Factory. |
| [Działania przekształcania danych](data-factory-data-transformation-activities.md) |Ten artykuł zawiera listę działań przekształcania danych (takich jak przekształcenie programu Hive w usłudze HDInsight używane w tym samouczku) obsługiwanych w usłudze Fabryka danych Azure. |
| [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md) |W tym artykule wyjaśniono aspekty planowania i wykonywania modelu aplikacji usługi Fabryka danych Azure. |
| [Monitorowanie potoków i zarządzanie nimi za pomocą aplikacji do monitorowania](data-factory-monitor-manage-app.md) |Ten artykuł zawiera instrukcje dotyczące monitorowania i debugowania potoków oraz zarządzania nimi przy użyciu aplikacji do monitorowania i zarządzania. |
