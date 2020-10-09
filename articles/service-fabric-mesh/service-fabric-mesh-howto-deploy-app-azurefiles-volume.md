---
title: Używanie woluminu opartego na Azure Files w aplikacji Service Fabric siatki
description: Dowiedz się, jak przechowywać informacje o stanie w aplikacji sieci Service Fabricej na platformie Azure, instalując wolumin oparty na Azure Files w ramach usługi przy użyciu interfejsu wiersza polecenia platformy Azure.
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 01cee3dc3f6b67aba1e6f8455ed7b538a44fc6f7
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842791"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Zainstaluj wolumin oparty na Azure Files w aplikacji Service Fabric siatkę 

W tym artykule opisano sposób instalowania woluminu opartego na Azure Files w usłudze Service Fabric aplikacji siatki.  Sterownik woluminu Azure Files to sterownik woluminu platformy Docker używany do instalowania udziału Azure Files w kontenerze, który służy do utrwalania stanu usługi. Woluminy zapewniają magazyn plików ogólnego przeznaczenia i umożliwiają odczytywanie i zapisywanie plików przy użyciu standardowych interfejsów API plików we/wy dysku.  Aby dowiedzieć się więcej o woluminach i opcjach przechowywania danych aplikacji, zobacz artykuł [przechowywanie stanu](service-fabric-mesh-storing-state.md).

Aby zainstalować wolumin w usłudze, należy utworzyć zasób woluminu w aplikacji siatki Service Fabric, a następnie odwołać się do tego woluminu w usłudze.  Deklarowanie zasobu woluminu i odwoływanie się do niego w ramach zasobu usługi można wykonać w [plikach zasobów opartych na YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) lub [szablonie wdrożenia opartym na notacji JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Przed zainstalowaniem woluminu należy najpierw utworzyć konto usługi Azure Storage i [udział plików w Azure Files](../storage/files/storage-how-to-create-file-share.md).

## <a name="prerequisites"></a>Wymagania wstępne
> [!NOTE]
> **Znany problem dotyczący wdrażania na komputerze deweloperskim RS5 systemu Windows:** Istnieje otwarta usterka z poleceniem cmdlet programu PowerShell New-SmbGlobalMapping na maszynach z systemem RS5 Windows, które uniemożliwiają zainstalowanie woluminów Azurefile. Poniżej znajduje się przykładowy błąd, który pojawia się, gdy wolumin oparty na AzureFile jest instalowany na lokalnym komputerze deweloperskim.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
Obejście problemu to 1) uruchom poniższe polecenie jako administrator programu PowerShell i 2) ponownie uruchom maszynę.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Aby ukończyć ten artykuł, można użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. 

Aby używać interfejsu wiersza polecenia platformy Azure lokalnie z tym artykułem, upewnij się, że program `az --version` zwraca co najmniej `azure-cli (2.0.43)` .  Zainstaluj (lub zaktualizuj) moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric siatka, wykonując te [instrukcje](service-fabric-mesh-howto-setup-cli.md).

Aby zalogować się do platformy Azure i ustawić subskrypcję:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Utwórz konto magazynu i udział plików (opcjonalnie)
Zainstalowanie woluminu Azure Files wymaga konta magazynu i udziału plików.  Możesz użyć istniejącego konta usługi Azure Storage i udziału plików albo utworzyć zasoby:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Pobierz nazwę i klucz konta magazynu oraz nazwę udziału plików
Nazwa konta magazynu, klucz konta magazynu i nazwa udziału plików są przywoływane jako `<storageAccountName>` , `<storageAccountKey>` i `<fileShareName>` w poniższych sekcjach. 

Utwórz listę kont magazynu i uzyskaj nazwę konta magazynu z udziałem plików, którego chcesz użyć:
```azurecli-interactive
az storage account list
```

Pobierz nazwę udziału plików:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Pobierz klucz konta magazynu ("Klucz1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Te wartości można również znaleźć w [Azure Portal](https://portal.azure.com):
* `<storageAccountName>` -W obszarze **konta magazynu**nazwa konta magazynu użytego do utworzenia udziału plików.
* `<storageAccountKey>` -Wybierz konto magazynu w obszarze **konta magazynu** , a następnie wybierz pozycję **klucze dostępu** i użyj wartości w obszarze **Klucz1**.
* `<fileShareName>` — Wybierz konto magazynu w obszarze  **konta magazynu** , a następnie wybierz pozycję **pliki**. Nazwa do użycia to nazwa utworzonego udziału plików.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarowanie zasobu woluminu i aktualizowanie zasobu usługi (JSON)

Dodaj parametry `<fileShareName>` , `<storageAccountName>` i `<storageAccountKey>` wartości, które zostały znalezione w poprzednim kroku. 

Utwórz zasób woluminu jako element równorzędny zasobu aplikacji. Określ nazwę i dostawcę ("SFAzureFile", aby użyć woluminu na podstawie Azure Files). W programie `azureFileParameters` Określ parametry `<fileShareName>` , `<storageAccountName>` i `<storageAccountKey>` wartości, które zostały znalezione w poprzednim kroku.

Aby zainstalować wolumin w usłudze, należy dodać `volumeRefs` `codePackages` element do elementu usługi.  `name` jest IDENTYFIKATORem zasobu dla woluminu (lub parametrem szablonu wdrożenia dla zasobu woluminu) i nazwą woluminu zadeklarowanego w pliku zasobów woluminu. YAML.  `destinationPath` jest katalogiem lokalnym, do którego zostanie zainstalowany wolumin.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Zadeklaruj zasób woluminu i zaktualizuj zasób usługi (YAML)

Dodaj nowy plik *Volume. YAML* w katalogu *zasobów aplikacji* dla aplikacji.  Określ nazwę i dostawcę ("SFAzureFile", aby użyć woluminu na podstawie Azure Files). `<fileShareName>`, `<storageAccountName>` , i `<storageAccountKey>` są wartościami znalezionymi w poprzednim kroku.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Zaktualizuj plik *Service. YAML* w katalogu *zasobów usługi* , aby zainstalować wolumin w usłudze.  Dodaj `volumeRefs` element do `codePackages` elementu.  `name` jest IDENTYFIKATORem zasobu dla woluminu (lub parametrem szablonu wdrożenia dla zasobu woluminu) i nazwą woluminu zadeklarowanego w pliku zasobów woluminu. YAML.  `destinationPath` jest katalogiem lokalnym, do którego zostanie zainstalowany wolumin.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Następne kroki

- Wyświetlanie przykładowej aplikacji Azure Files Volume w witrynie [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Aby dowiedzieć się więcej o modelu zasobów usługi Service Fabric, zobacz [Model zasobów usługi Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
