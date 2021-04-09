---
title: 'Szybki Start: Tworzenie Azure Data Factory przy użyciu interfejsu wiersza polecenia platformy Azure'
description: Ten przewodnik Szybki Start tworzy Azure Data Factory, w tym połączonej usługi, zestawów danych i potoku. Można uruchomić potok, aby wykonać akcję kopiowania plików.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: 9af5f276e49e9eb2756dc544db353c75c99bc5a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105938064"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Szybki Start: Tworzenie Azure Data Factory przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start opisano, jak utworzyć Azure Data Factory przy użyciu interfejsu wiersza polecenia platformy Azure. Potok tworzony w tej fabryce danych kopiuje dane z jednego folderu do innego folderu w usłudze Azure Blob Storage. Aby uzyskać informacje na temat przekształcania danych przy użyciu Azure Data Factory, zobacz [Przekształć dane w Azure Data Factory](transform-data.md).

Aby zapoznać się z wprowadzeniem do usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli współautora lub właściciela albo administratorem subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [role platformy Azure](quickstart-create-data-factory-powershell.md#azure-roles).

## <a name="prepare-a-container-and-test-file"></a>Przygotowywanie kontenera i pliku testowego

Ten przewodnik Szybki Start używa konta usługi Azure Storage, które zawiera kontener z plikiem.

1. Aby utworzyć grupę zasobów o nazwie `ADFQuickStartRG` , użyj polecenia [AZ Group Create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Utwórz konto magazynu przy użyciu polecenia [AZ Storage account Create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Utwórz kontener o nazwie przy `adftutorial` użyciu polecenia [AZ Storage Container Create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. W katalogu lokalnym Utwórz plik o nazwie `emp.txt` do przekazania. Jeśli pracujesz w Azure Cloud Shell, możesz znaleźć bieżący katalog roboczy przy użyciu `echo $PWD` polecenia bash. Aby utworzyć plik, można użyć standardowych poleceń bash, takich jak `cat` :

   ```console
   cat > emp.txt
   This is text.
   ```

   Aby zapisać nowy plik, użyj **kombinacji klawiszy CTRL + D** .

1. Aby przekazać nowy plik do kontenera usługi Azure Storage, użyj polecenia [AZ Storage BLOB upload](/cli/azure/storage/blob#az_storage_blob_upload) :

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   To polecenie przekazuje do nowego folderu o nazwie `input` .

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć fabrykę danych Azure, uruchom polecenie [AZ DataFactory Factory Create](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_create) :

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Zamień na `ADFTutorialFactory` globalnie unikatową nazwę fabryki danych, na przykład ADFTutorialFactorySP1127.

Można wyświetlić fabrykę danych utworzoną za pomocą polecenia [AZ DataFactory Factory show](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) :

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Tworzenie połączonej usługi i zestawów danych

Następnie Utwórz połączoną usługę i dwa zestawy danych.

1. Pobierz parametry połączenia dla konta magazynu za pomocą polecenia [AZ Storage account show-Connection-String](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) :

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. W katalogu roboczym Utwórz plik JSON z tą zawartością, który zawiera własne parametry połączenia z poprzedniego kroku. Nazwij plik `AzureStorageLinkedService.json` :

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

1. Utwórz połączoną usługę o nazwie przy `AzureStorageLinkedService` użyciu polecenia [AZ DataFactory Linked-Service Create](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_create) :

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. W katalogu roboczym Utwórz plik JSON o nazwie `InputDataset.json` :

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

1. Utwórz wejściowy zestaw danych o nazwie przy `InputDataset` użyciu polecenia [AZ DataFactory DataSet Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. W katalogu roboczym Utwórz plik JSON o nazwie `OutputDataset.json` :

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

1. Utwórz wyjściowy zestaw danych o nazwie przy `OutputDataset` użyciu polecenia [AZ DataFactory DataSet Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Tworzenie i uruchamianie potoku

Na koniec Utwórz i uruchom potok.

1. W katalogu roboczym Utwórz plik JSON o nazwie `Adfv2QuickStartPipeline.json` :

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

1. Utwórz potok o nazwie `Adfv2QuickStartPipeline` przy użyciu polecenia [AZ DataFactory Pipeline Create](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create) :

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Uruchom potok za pomocą polecenia [AZ DataFactory potoku Create-Run](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create_run) :

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   To polecenie zwraca identyfikator uruchomienia. Skopiuj go do użycia w następnym poleceniu.

1. Sprawdź, czy uruchomienie potoku zakończyło się pomyślnie za pomocą polecenia [AZ DataFactory Pipeline-Run show](/cli/azure/ext/datafactory/datafactory/pipeline-run#ext_datafactory_az_datafactory_pipeline_run_show) :

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Możesz również sprawdzić, czy potok działał zgodnie z oczekiwaniami przy użyciu [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Przegląd wdrożonych zasobów](quickstart-create-data-factory-powershell.md#review-deployed-resources).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wszystkie zasoby w tym przewodniku Szybki Start są częścią tej samej grupy zasobów. Aby je usunąć, użyj polecenia [AZ Group Delete](/cli/azure/group#az_group_delete) :

```azurecli
az group delete --name ADFQuickStartRG
```

W przypadku korzystania z tej grupy zasobów dla wszystkich innych elementów, należy usunąć poszczególne zasoby. Na przykład aby usunąć połączoną usługę, użyj polecenia [AZ DataFactory Linked-Service Delete](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_delete) .

W tym przewodniku szybki start utworzono następujące pliki JSON:

- AzureStorageLinkedService.jsna
- InputDataset.jsna
- OutputDataset.jsna
- Adfv2QuickStartPipeline.jsna

Usuń je za pomocą standardowych poleceń bash.

## <a name="next-steps"></a>Następne kroki

- [Potoki i działania w usłudze Azure Data Factory](concepts-pipelines-activities.md)
- [Połączone usługi w usłudze Azure Data Factory](concepts-linked-services.md)
- [Zestawy danych w usłudze Azure Data Factory](concepts-datasets-linked-services.md)
- [Przekształcanie danych w usłudze Azure Data Factory](transform-data.md)
