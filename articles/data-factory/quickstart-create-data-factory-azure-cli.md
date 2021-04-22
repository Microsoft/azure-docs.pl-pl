---
title: 'Szybki start: tworzenie aplikacji przy użyciu Azure Data Factory wiersza polecenia platformy Azure'
description: Ten przewodnik Szybki start tworzy Azure Data Factory, w tym usługę połączona, zestawy danych i potok. Możesz uruchomić potok, aby wykonać akcję kopiowania plików.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: b40407f4c4fb81bbf76bd0b552f3c9f2c827232a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871528"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Szybki start: tworzenie aplikacji przy użyciu Azure Data Factory wiersza polecenia platformy Azure

W tym przewodniku Szybki start opisano, jak utworzyć interfejs wiersza polecenia platformy Azure Azure Data Factory. Potok, który tworzysz w tej fabryce danych, kopiuje dane z jednego folderu do innego folderu w Azure Blob Storage. Aby uzyskać informacje na temat przekształcania danych przy użyciu Azure Data Factory, zobacz [Przekształcanie danych w Azure Data Factory](transform-data.md).

Aby zapoznać się z wprowadzeniem do usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli współautora lub właściciela albo administratorem subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Role platformy Azure.](quickstart-create-data-factory-powershell.md#azure-roles)

## <a name="prepare-a-container-and-test-file"></a>Przygotowywanie kontenera i pliku testowego

W tym przewodniku Szybki start jest używane konto usługi Azure Storage, które zawiera kontener z plikiem.

1. Aby utworzyć grupę zasobów o nazwie `ADFQuickStartRG` , użyj polecenia az group [create:](/cli/azure/group#az_group_create)

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Utwórz konto magazynu za pomocą [polecenia az storage account create:](/cli/azure/storage/container#az_storage_container_create)

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Utwórz kontener o nazwie `adftutorial` za pomocą polecenia az storage container [create:](/cli/azure/storage/container#az_storage_container_create)

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. W katalogu lokalnym utwórz plik o nazwie do `emp.txt` przekazania. Jeśli pracujesz w usłudze Azure Cloud Shell, możesz znaleźć bieżący katalog roboczy przy użyciu `echo $PWD` polecenia powłoki Bash. Aby utworzyć plik, możesz użyć standardowych poleceń powłoki Bash, `cat` takich jak , :

   ```console
   cat > emp.txt
   This is text.
   ```

   Użyj **klawiszy Ctrl+D,** aby zapisać nowy plik.

1. Aby przekazać nowy plik do kontenera usługi Azure Storage, użyj [polecenia az storage blob upload:](/cli/azure/storage/blob#az_storage_blob_upload)

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   To polecenie zostanie przesłane do nowego folderu o nazwie `input` .

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć fabrykę danych Azure, uruchom [polecenie az datafactory factory create:](/cli/azure/datafactory/factory#az_datafactory_factory_create)

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Zastąp `ADFTutorialFactory` nazwą globalnie unikatowej fabryki danych, na przykład ADFTutorialFactorySP1127.

Fabrykę danych utworzoną za pomocą polecenia [az datafactory factory show](/cli/azure/datafactory/factory#az_datafactory_factory_show) można wyświetlić:

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Tworzenie połączonej usługi i zestawów danych

Następnie utwórz usługę połączona i dwa zestawy danych.

1. Pobierz ciąg połączenia dla konta magazynu za pomocą polecenia [az storage account show-connection-string:](/cli/azure/datafactory/factory#az_datafactory_factory_show)

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. W katalogu roboczy utwórz plik JSON z tą zawartością, która zawiera twoje własne ciągi połączenia z poprzedniego kroku. Nadaj plikowi nazwę `AzureStorageLinkedService.json` :

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. Utwórz połączona usługę o nazwie `AzureStorageLinkedService` za pomocą polecenia az [datafactory linked-service create:](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create)

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. W katalogu roboczy utwórz plik JSON o tej zawartości o nazwie `InputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Utwórz wejściowy zestaw danych o `InputDataset` nazwie za pomocą polecenia az [datafactory dataset create:](/cli/azure/datafactory/dataset#az_datafactory_dataset_create)

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. W katalogu roboczy utwórz plik JSON o tej zawartości o nazwie `OutputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Utwórz wyjściowy zestaw danych `OutputDataset` o nazwie za pomocą polecenia az [datafactory dataset create:](/cli/azure/datafactory/dataset#az_datafactory_dataset_create)

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Tworzenie i uruchamianie potoku

Na koniec utwórz i uruchom potok.

1. W katalogu roboczy utwórz plik JSON o tej zawartości o nazwie `Adfv2QuickStartPipeline.json` :

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. Utwórz potok o nazwie `Adfv2QuickStartPipeline` za pomocą polecenia az [datafactory pipeline create:](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create)

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Uruchom potok przy użyciu polecenia [az datafactory pipeline create-run:](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run)

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   To polecenie zwraca identyfikator uruchomienia. Skopiuj go do użycia w następnym poleceniu.

1. Sprawdź, czy uruchomienie potoku zakończyło się pomyślnie, używając [polecenia az datafactory pipeline-run show:](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show)

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Możesz również sprawdzić, czy potok został uruchomienia zgodnie z oczekiwaniami, używając [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Review deployed resources (Przegląd wdrożonych zasobów).](quickstart-create-data-factory-powershell.md#review-deployed-resources)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wszystkie zasoby w tym przewodniku Szybki start są częścią tej samej grupy zasobów. Aby usunąć wszystkie te dane, użyj [polecenia az group delete:](/cli/azure/group#az_group_delete)

```azurecli
az group delete --name ADFQuickStartRG
```

Jeśli używasz tej grupy zasobów do czegokolwiek innego, usuń poszczególne zasoby. Aby na przykład usunąć połączona usługę, użyj [polecenia az datafactory linked-service delete.](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete)

W tym przewodniku Szybki start utworzono następujące pliki JSON:

- AzureStorageLinkedService.jswł.
- InputDataset.jswł.
- OutputDataset.jswł.
- Adfv2QuickStartPipeline.jswł.

Usuń je za pomocą standardowych poleceń powłoki Bash.

## <a name="next-steps"></a>Następne kroki

- [Potoki i działania w usłudze Azure Data Factory](concepts-pipelines-activities.md)
- [Połączone usługi w usłudze Azure Data Factory](concepts-linked-services.md)
- [Zestawy danych w usłudze Azure Data Factory](concepts-datasets-linked-services.md)
- [Przekształcanie danych w usłudze Azure Data Factory](transform-data.md)
