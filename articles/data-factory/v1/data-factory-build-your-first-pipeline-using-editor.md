---
title: Tworzenie pierwszej fabryki danych (Azure Portal)
description: W tym samouczku przedstawiono instrukcje tworzenia przykładowego potoku usługi Azure Data Factory przy użyciu Edytora fabryki danych w witrynie Azure Portal.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: e948962a73137f78ec26a01c1d2f54d883e76783
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637449"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Samouczek: tworzenie pierwszej fabryki danych przy użyciu witryny Azure Portal
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-build-your-first-pipeline.md)
> * [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Szablon usługi Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory, która jest ogólnie dostępna. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Szybki start: tworzenie fabryki danych przy użyciu usługi Data Factory](../quickstart-create-data-factory-dot-net.md).

> [!WARNING]
> Edytor JSON w witrynie Azure Portal na potrzeby tworzenia & wdrażania potoków w wersji 1 systemu APD zostanie wyłączony z 31 lipca 2019. Po 31 lipca 2019 można nadal korzystać z [poleceń cmdlet programu PowerShell](/powershell/module/az.datafactory/?view=azps-2.4.0&viewFallbackFrom=azps-2.3.2)w usłudze ADF V1, [zestawu SDK programu .NET](/dotnet/api/microsoft.azure.management.datafactories.models?view=azure-dotnet)ADF V1, a [także do tworzenia](/rest/api/datafactory/) & wdrażania potoków w wersji 1.

Ten artykuł zawiera instrukcje korzystania z witryny [Azure Portal](https://portal.azure.com/) w celu utworzenia pierwszej fabryki danych. Aby wykonać instrukcje z tego samouczka przy użyciu innych narzędzi/zestawów SDK, wybierz jedną z opcji z listy rozwijanej. 

Potok w tym samouczku zawiera jedno działanie: działanie Hive usługi Azure HDInsight. To działanie uruchamia skrypt Hive w klastrze usługi HDInsight, który przekształca dane wejściowe, aby wygenerować dane wyjściowe. Uruchamianie potoku zaplanowano raz w miesiącu między określonym czasem rozpoczęcia i zakończenia. 

> [!NOTE]
> Potok danych przedstawiony w tym samouczku przekształca dane wejściowe w celu wygenerowania danych wyjściowych. Aby zapoznać się z samouczkiem dotyczącym kopiowania danych przy użyciu usługi Data Factory, zobacz [Tutorial: Copy data from Azure Blob Storage to Azure SQL Database (Samouczek: kopiowanie danych z usługi Azure Blob Storage do usługi Azure SQL Database)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Potok może obejmować więcej niż jedno działanie. Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Aby uzyskać więcej informacji, zobacz [Planowanie i wykonywanie w Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Wymagania wstępne
Zapoznaj się z [Tutorial overview](data-factory-build-your-first-pipeline.md) i wykonaj kroki opisane w sekcji „Wymagania wstępne”.

Ten artykuł nie zawiera omówienia pojęć usługi Data Factory. Aby uzyskać więcej informacji o usłudze, zobacz [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
Fabryka danych może obejmować jeden lub wiele potoków. Potok może obejmować jedno lub wiele działań. Może to być na przykład działanie kopiowania, które kopiuje dane ze źródła do docelowego magazynu danych. Innym przykładem jest działanie Hive usługi HDInsight służące do uruchamiania skryptu Hive umożliwiającego przekształcenie danych wejściowych w dane wyjściowe. 

Aby utworzyć fabrykę danych, wykonaj następujące kroki:

1. Zaloguj się do [Azure portal](https://portal.azure.com/).

1. Wybierz pozycję **nowe**  >  **dane + analiza**  >  **Data Factory** .

   ![Blok tworzenia](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

1. W bloku **Nowa fabryka danych** w obszarze **Nazwa** wprowadź **GetStartedDF** .

   ![Blok Nowa fabryka danych](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > Nazwa fabryki danych musi być globalnie unikatowa. Jeśli wystąpi błąd „Nazwa fabryki danych GetStartedDF jest niedostępna”, zmień nazwę fabryki danych. Na przykład użyj nazwy TwojaNazwaGetStartedDF i utwórz ponownie fabrykę danych. Aby uzyskać więcej informacji na temat reguł nazewnictwa, zobacz [Data Factory: Naming rules (Fabryka danych: reguły nazewnictwa)](data-factory-naming-rules.md).
   >
   > W przyszłości nazwa fabryki danych może zostać zarejestrowana jako nazwa DNS, a wówczas stanie się widoczna publicznie.
   >
   >
1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w ramach której chcesz utworzyć fabrykę danych.

1. Wybierz istniejącą grupę zasobów lub utwórz grupę zasobów. Aby zapoznać się z samouczkiem, Utwórz grupę zasobów o nazwie **ADFGetStartedRG** .

1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko regiony obsługiwane przez usługę Data Factory.

1. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** .

1. Wybierz pozycję **Utwórz** .

   > [!IMPORTANT]
   > Aby utworzyć wystąpienia Data Factory, musisz być członkiem roli [współautor Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na poziomie subskrypcji/grupy zasobów.
   >
   >
1. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem **Wdrażanie fabryki danych** :    

   ![Stan Wdrażanie fabryki danych](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

1. Po utworzeniu fabryki danych zostanie wyświetlona strona **Fabryka danych** z zawartością fabryki danych.     

    ![Blok Fabryka danych](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Przed utworzeniem potoku w fabryce danych musisz utworzyć kilka jednostek fabryki danych. Najpierw utwórz połączone usługi, aby połączyć magazyny danych/obliczenia ze swoim magazynem danych. Następnie zdefiniuj wejściowy i wyjściowy zestaw danych w celu reprezentowania danych wejściowych/wyjściowych w połączonych magazynach danych. Na koniec utwórz potok z działaniem używającym tych zestawów danych.

## <a name="create-linked-services"></a>Tworzenie połączonych usług
W tym kroku opisano połączenie konta usługi Azure Storage oraz klastra usługi HDInsight na żądanie z fabryką danych. W tym przykładzie konto magazynu będzie przechowywać dane wejściowe i wyjściowe dla potoku. Połączona usługa HDInsight służy do uruchamiania skryptu programu Hive określonego w działaniu potoku w tym przykładzie. Określ, [data store](data-factory-data-movement-activities.md)które / [usługi obliczeniowe](data-factory-compute-linked-services.md) magazynu danych są używane w danym scenariuszu. Następnie połącz te usługi z fabryką danych, tworząc połączone usługi.  

### <a name="create-a-storage-linked-service"></a>Tworzenie połączonej usługi Storage
W tym kroku opisano łączenie konta magazynu z fabryką danych. Na potrzeby tego samouczka do przechowywania danych wejściowych/wyjściowych oraz pliku skryptu HQL używa się tego samego konta magazynu.

1. W bloku **Fabryka danych** dla pozycji **GetStartedDF** wybierz pozycję **Utwórz i wdróż** . Pojawi się Edytor fabryki danych.

   ![Kafelek Utwórz i wdróż](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

1. Wybierz pozycję **Nowy magazyn danych** , a następnie opcję **Azure Storage** .

   ![Blok nowego magazynu danych](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

1. Zostanie wyświetlony skrypt JSON umożliwiający utworzenie połączonej usługi Storage w edytorze.

   ![Połączona usługa Storage](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Zastąp **nazwę konta** nazwą konta magazynu. Zastąp **klucz konta** kluczem dostępu do konta magazynu. Aby dowiedzieć się, jak uzyskać klucz dostępu do magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

    ![Przycisk Wdróż](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Po pomyślnym wdrożeniu połączonej usługi okno Wersja robocza-1 zniknie. W widoku drzewa po lewej stronie pojawi się wartość **AzureStorageLinkedService** .

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>Tworzenie połączonej usługi HDInsight
W tym kroku przedstawiono łączenie klastra usługi HDInsight na żądanie z fabryką danych. Klaster usługi HDInsight jest tworzony automatycznie w czasie wykonywania. Po zakończeniu przetwarzania i określonym czasie bezczynności klaster jest usuwany.

1. W edytorze Data Factory wybierz pozycję **więcej**  >  **Nowy**  >  **klaster usługi HDInsight na żądanie** .

    ![Nowe obliczenie](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. Fragment kodu JSON opisuje właściwości, które zostaną użyte do utworzenia klastra usługi HDInsight na żądanie.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    Poniższa tabela zawiera opis właściwości JSON użytych w tym fragmencie kodu.

   | Właściwość | Opis |
   |:--- |:--- |
   | clusterSize |Określa rozmiar klastra usługi HDInsight. |
   | timeToLive | Określa czas bezczynności, po którym klaster usługi HDInsight zostanie usunięty. |
   | linkedServiceName | Określa konto magazynu używane do przechowywania dzienników generowanych w usłudze HDInsight. |

    Pamiętaj o następujących kwestiach:

     a. Usługa Data Factory tworzy klaster usługi HDInsight oparty na systemie Linux za pomocą właściwości JSON. Aby uzyskać więcej informacji, zobacz [On-demand HDInsight linked service (Połączona usługa HDInsight na żądanie)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. Zamiast klastra usługi HDInsight na żądanie możesz użyć własnego klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Połączona usługa HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. Klaster usługi HDInsight tworzy kontener domyślny w magazynie obiektów blob określonym we właściwości JSON ( **linkedServiceName** ). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. W przypadku połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest tworzony przy każdym przetwarzaniu wycinka — o ile w tym momencie nie istnieje aktywny klaster ( **timeToLive** ). Klaster jest automatycznie usuwany po zakończeniu przetwarzania.

     Po przetworzeniu większej liczby wycinków w magazynie obiektów blob będzie widocznych wiele kontenerów. Jeśli nie są one potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne ze wzorcem: "ADF **twojanazwafabrykidanych nazwapołączonejusługi** - **linkedservicename** -znacznikdatygodziny". Aby usunąć kontenery z magazynu obiektów blob, użyj takich narzędzi, jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

     Aby uzyskać więcej informacji, zobacz [On-demand HDInsight linked service (Połączona usługa HDInsight na żądanie)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć połączoną usługę.

    ![Opcja wdrażania](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

1. Upewnij się, że w widoku drzewa po lewej stronie wyświetlane są obie pozycje **AzureStorageLinkedService** oraz **HDInsightOnDemandLinkedService** .

    ![Zrzut ekranu pokazujący, że AzureStorageLinkedService i HDInsightOnDemandLinkedService są połączone ze sobą.](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym kroku opisano tworzenie zestawów danych do reprezentowania danych wejściowych i wyjściowych na potrzeby przetwarzania przy użyciu programu Hive. Te zestawy danych dotyczą elementu AzureStorageLinkedService utworzonego wcześniej w tym samouczku. Połączona usługa wskazuje konto magazynu. Zestawy danych określają kontener, folder i nazwę pliku w magazynie, który przechowuje dane wejściowe i wyjściowe.   

### <a name="create-the-input-dataset"></a>Tworzenie wejściowego zestawu danych
1. W edytorze Data Factory wybierz pozycję **więcej**  >  **Nowy zestaw danych**  >  **Azure Blob Storage** .

    ![Nowy zestaw danych](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. Za pomocą tego fragmentu kodu JSON tworzysz zestaw danych o nazwie **AzureBlobInput** , który reprezentuje dane wejściowe dla działania w potoku. Ponadto określasz, że dane wejściowe znajdują się w kontenerze obiektów blob o nazwie **adfgetstarted** oraz folderze o nazwie **inputdata** .

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
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
    Poniższa tabela zawiera opis właściwości JSON użytych w tym fragmencie kodu.

   | Właściwość | Zagnieżdżone w | Opis |
   |:--- |:--- |:--- |
   | typ | properties |Właściwość type jest ustawiona na wartość **AzureBlob** , ponieważ dane znajdują się w magazynie obiektów blob. |
   | linkedServiceName | format |Odnosi się do utworzonego wcześniej elementu AzureStorageLinkedService. |
   | folderPath | typeProperties | Określa kontener obiektów blob oraz folder, który zawiera wejściowe obiekty blob. | 
   | fileName | typeProperties |Ta właściwość jest opcjonalna. Jeśli pominiesz tę właściwość, zostaną wybrane wszystkie pliki z folderu folderPath. W tym samouczku zostanie przetworzony tylko plik input.log. |
   | typ | format |Pliki dziennika są w formacie tekstowym, więc używana jest wartość **TextFormat** . |
   | columnDelimiter | format |Kolumny w plikach dziennika są rozdzielane przecinkami (`,`). |
   | frequency/interval | availability |Właściwość frequency (częstotliwość) jest ustawiona na wartość **Miesiąc** , a wartość interwału wynosi **1** , co oznacza, że wycinki wejściowe są dostępne co miesiąc. |
   | external | properties | Ta właściwość ma wartość **true** (prawda), jeśli dane wejściowe nie są generowane przez ten potok. W tym samouczku plik input.log nie jest generowany w tym potoku, dlatego możemy ustawić właściwość na **true** . |

    Aby uzyskać więcej informacji o tych właściwościach JSON, zobacz [Łącznik obiektu blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties).

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć nowo utworzony zestaw danych. Zestaw danych zostanie wyświetlony w widoku drzewa po lewej stronie.

### <a name="create-the-output-dataset"></a>Tworzenie wyjściowego zestawu danych
Teraz utworzysz wyjściowy zestaw danych do reprezentowania danych wyjściowych przechowywanych w magazynie obiektów blob.

1. W edytorze Data Factory wybierz pozycję **więcej**  >  **Nowy zestaw danych**  >  **Azure Blob Storage** .

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1. Za pomocą tego fragmentu kodu JSON tworzysz zestaw danych o nazwie **AzureBlobOutput** i określasz strukturę danych, które są generowane przy użyciu skryptu programu Hive. Ponadto określasz, że wyniki są przechowywane w kontenerze obiektów blob o nazwie **adfgetstarted** oraz folderze o nazwie **partitioneddata** . W sekcji **availability** (dostępność) określono, że wyjściowy zestaw danych jest generowany co miesiąc.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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
    Opisy tych właściwości znajdują się w części „Tworzenie wejściowego zestawu danych”. Dla wyjściowego zestawu danych nie ustawia się właściwości external (zewnętrzne), ponieważ zestaw danych jest generowany w usłudze Data Factory.

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć nowo utworzony zestaw danych.

1. Sprawdź, czy zestaw danych został utworzony pomyślnie.

    ![Widok drzewa z połączonymi usługami](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>Tworzenie potoku
W tym kroku opisano tworzenie pierwszego potoku za pomocą działania Hive usługi HDInsight. Wycinek wejściowy jest dostępny co miesiąc (wartość częstotliwości to Miesiąc, a interwał wynosi 1). Wycinek wyjściowy jest generowany co miesiąc. Właściwość scheduler dla działania jest również ustawiona na wartość miesięczną. Ustawienia dla wyjściowego zestawu danych i harmonogramu działania muszą być zgodne. W tym przypadku wyjściowy zestaw danych jest elementem wpływającym na ustawienia harmonogramu, więc musisz utworzyć wyjściowy zestaw danych nawet wtedy, gdy działanie nie generuje żadnych danych wyjściowych. Jeśli w działaniu nie są używane żadne dane wejściowe, możesz pominąć tworzenie zestawu danych wejściowych. Właściwości użyte w poniższym fragmencie kodu JSON zostały wyjaśnione na końcu tej sekcji.

1. W edytorze Data Factory wybierz pozycję **więcej**  >  **nowych potoków** .

    ![Opcja Nowy potok](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

1. Skopiuj i wklej poniższy fragment kodu do okna Wersja robocza-1.

   > [!IMPORTANT]
   > Zastąp wartość **storageaccountname** w kodzie JSON nazwą swojego konta magazynu.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
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

    Plik skryptu programu Hive **partitionweblogs.hql** jest przechowywany na koncie magazynu określonym za pomocą elementu scriptLinkedService o nazwie **AzureStorageLinkedService1** . Można go znaleźć w folderze **script** w kontenerze **adfgetstarted** .

    Sekcja **defines** służy do określania ustawień środowiska uruchomieniowego, które są przekazywane do skryptu Hive jako wartości konfiguracyjne magazynu Hive, np. ${hiveconf:inputtable} i ${hiveconf:partitionedtable}.

    Właściwości **start** i **end** potoku określają aktywny okres potoku.

    W kodzie JSON dotyczącym działania określasz, że skrypt programu Hive jest uruchamiany w usłudze obliczeniowej określonej przez właściwość **linkedServiceName** : **HDInsightOnDemandLinkedService** .

   > [!NOTE]
   > Aby uzyskać szczegółowe informacje na temat właściwości JSON używanych w przykładzie, zobacz sekcję „Pipeline JSON” (Kod JSON potoku) w temacie [Pipelines and activities in Data Factory (Potoki i działania w usłudze Data Factory)](data-factory-create-pipelines.md).
   >
   >
1. Upewnij się, że:

   a. Plik **input.log** istnieje w folderze **inputdata** kontenera **adfgetstarted** w magazynie obiektów blob.

   b. Plik **partitionweblogs.hql** znajduje się w folderze **script** kontenera **adfgetstarted** w magazynie obiektów blob. Jeśli nie widzisz tych plików, wykonaj kroki opisane w sekcji „Wymagania wstępne” w [omówieniu samouczka](data-factory-build-your-first-pipeline.md).

   c. Element **storageaccountname** został zastąpiony nazwą konta magazynu w kodzie JSON potoku.

1. Wybierz przycisk **Wdróż** na pasku poleceń, aby wdrożyć potok. Ponieważ właściwości **start** i **end** zostały ustawione na wartości w przeszłości, a właściwość **isPaused** została ustawiona na wartość **false** , potok (działanie w potoku) jest uruchamiany natychmiast po wdrożeniu.

1. Upewnij się, że potok jest wyświetlany w widoku drzewa.

    ![Widok drzewa z potokiem](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>Monitorowanie potoku
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Monitorowanie potoku przy użyciu widoku diagramu
1. W bloku **Fabryka danych** wybierz opcję **Diagram** .

    ![Kafelek Diagram](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

1. W widoku **diagramu** zostanie wyświetlony przegląd potoków i zestawów danych używanych w tym samouczku.

    ![Widok diagramu](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

1. Aby wyświetlić wszystkie działania w potoku, kliknij prawym przyciskiem myszy potok w diagramie i wybierz polecenie **Otwórz potok** .

    ![Menu Otwórz potok](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

1. Upewnij się, że **działanie Hive** jest widoczne w potoku.

    ![Widok Otwórz potok](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Aby powrócić do poprzedniego widoku, wybierz pozycję **Fabryka danych** w menu u góry.

1. W widoku **diagramu** kliknij dwukrotnie zestaw danych **AzureBlobInput** . Sprawdź, czy wycinek jest w stanie **Gotowe** . Może upłynąć kilka minut, zanim wycinek zostanie wyświetlony ze stanem **Gotowe** . Jeśli poczekasz jakiś czas i tak się nie stanie, sprawdź, czy plik wejściowy ( **input.log** ) znajduje się w odpowiednim kontenerze ( **adfgetstarted** ) i folderze ( **inputdata** ).

   ![Wycinek danych wejściowych w stanie gotowości](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

1. Zamknij blok **AzureBlobInput** .

1. W widoku **diagramu** kliknij dwukrotnie zestaw danych **AzureBlobOutput** . Zostanie wyświetlony wycinek, który jest obecnie przetwarzany.

   ![Przetwarzanie zestawu danych w toku](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

1. Po zakończeniu przetwarzania wycinek zostanie wyświetlony ze stanem **Gotowe** .

   ![Zestaw danych ze stanem Gotowe](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > Tworzenie klastra usługi HDInsight na żądanie zwykle trwa około 20 minut. Należy oczekiwać, że przetworzenie wycinka przez potok zajmie około 30 minut.
   >
   >

1. Gdy wycinek będzie w stanie **Gotowe** , sprawdź, czy w folderze **partitioneddata** w kontenerze **adfgetstarted** w magazynie obiektów blob znajdują się dane wyjściowe.  

   ![Dane wyjściowe](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

1. Wybierz wycinek, aby wyświetlić więcej informacji o nim w bloku **Wycinek danych** .

    ![Informacje o wycinku danych](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

1. Z listy **Uruchomienia działania** wybierz uruchomienie działania, aby wyświetlić dodatkowe informacje o nim. (W tym scenariuszu jest to działanie Hive). Informacje są wyświetlane w bloku **szczegóły uruchomienia działania** .   

    ![Okno Szczegóły uruchamiania działania](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   W plikach dziennika zobaczysz zapytanie Hive, które zostało wykonane, oraz informacje o stanie. Dzienniki te są przydatne w przypadku rozwiązywania różnych problemów.
   Aby uzyskać więcej informacji, zobacz [Monitor and manage pipelines by using Azure portal blades (Monitorowanie potoków i zarządzanie nimi przy użyciu bloków w witrynie Azure Portal)](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> Po pomyślnym przetworzeniu wycinka plik wejściowy zostanie usunięty. W związku z tym, jeśli chcesz ponownie uruchomić wycinek lub wykonać samouczka, Przekaż plik wejściowy ( **Input. log** ) do folderu **inputdata** w kontenerze **adfgetstarted** .
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Monitorowanie potoku przy użyciu aplikacji Monitorowanie i zarządzanie
Do monitorowania potoków możesz też użyć aplikacji Monitorowanie i zarządzanie. Aby uzyskać więcej informacji o sposobie używania tej aplikacji, zobacz [Monitor and manage Data Factory pipelines by using the Monitor & Manage app (Monitorowanie potoków usługi Data Factory i zarządzanie nimi przy użyciu aplikacji Monitorowanie i zarządzanie)](data-factory-monitor-manage-app.md).

1. Wybierz kafelek **Monitorowanie i zarządzanie** na stronie głównej fabryki danych.

    ![Kafelek Monitorowanie i zarządzanie](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

1. W aplikacji Monitorowanie i zarządzanie zmień wartości **Czas rozpoczęcia** i **Czas zakończenia** na czas rozpoczęcia i czas zakończenia potoku. Wybierz przycisk **Zastosuj** .

    ![Aplikacja Monitorowanie i zarządzanie](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

1. Wybierz okno działania z listy **Okna działania** , aby wyświetlić informacje o tym działaniu.

    ![Lista Okna działania](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Podsumowanie
W tym samouczku opisano tworzenie fabryki danych do przetwarzania danych przez uruchomienie skryptu programu Hive w klastrze platformy Hadoop w usłudze HDInsight. Użyto Edytora fabryki danych w witrynie Azure Portal, aby:  

* Tworzenie fabryki danych.
* Utworzyć dwie połączone usługi:
   * Połączoną usługę Storage w celu połączenia magazynu obiektów blob, w którym przechowywane są pliki wejściowe/wyjściowe, z fabryką danych.
   * Połączoną usługę HDInsight na żądanie w celu połączenia klastra platformy Hadoop na żądanie w usłudze HDInsight z fabryką danych. Usługa Data Factory tworzy klaster just in time platformy Hadoop w usłudze HDInsight, aby przetwarzać dane wejściowe i generować dane wyjściowe.
* Utworzyć dwa zestawy danych zawierające dane wejściowe i wyjściowe dla działania programu Hive w usłudze HDInsight w potoku.
* Utworzyć potok za pomocą działania programu Hive w usłudze HDInsight.

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie potoku za pomocą działania przekształcenia (działanie usługi HDInsight), które uruchamia skrypt programu Hive w klastrze usługi HDInsight na żądanie. Aby dowiedzieć się, jak używać działania kopiowania do kopiowania danych z magazynu obiektów BLOB do Azure SQL Database, zobacz [Samouczek: kopiowanie danych z magazynu obiektów BLOB do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Zobacz także
| Temat | Opis |
|:--- |:--- |
| [Potoki](data-factory-create-pipelines.md) |Ten artykuł ułatwia zapoznanie się z potokami i działaniami w usłudze Data Factory oraz ze sposobem konstruowania za ich pomocą kompletnych przepływów pracy opartych na danych na potrzeby scenariusza lub firmy. |
| [Zestawy danych](data-factory-create-datasets.md) |Ten artykuł ułatwia zapoznanie się z zestawami danych w usłudze Data Factory. |
| [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md) |W tym artykule wyjaśniono aspekty planowania i wykonywania modelu aplikacji usługi Data Factory. |
| [Monitorowanie potoków i zarządzanie nimi za pomocą aplikacji Monitorowanie i zarządzanie](data-factory-monitor-manage-app.md) |Ten artykuł zawiera instrukcje dotyczące monitorowania i debugowania potoków oraz zarządzania nimi przy użyciu aplikacji Monitorowanie i zarządzanie. |