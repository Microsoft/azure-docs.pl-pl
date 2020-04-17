---
title: Wdrażanie maszyny Wirtualnej przy użyciu języka C# i szablonu Menedżera zasobów
description: Dowiedz się, jak wdrożyć maszynę wirtualną platformy Azure za pomocą języka C# i szablonu Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: f636af911b3c6eb7d40a5ce5052bf17ae5d10f01
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452200"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Wdrażanie maszyny wirtualnej platformy Azure przy użyciu języka C# i szablonu Menedżera zasobów

W tym artykule pokazano, jak wdrożyć szablon usługi Azure Resource Manager przy użyciu języka C#. Utworzony szablon wdraża pojedynczą maszynę wirtualną z systemem Windows Server w nowej sieci wirtualnej z pojedynczą podsiecią.

Aby uzyskać szczegółowy opis zasobu maszyny wirtualnej, zobacz [Maszyny wirtualne w szablonie Usługi Azure Resource Manager](template-description.md). Aby uzyskać więcej informacji na temat wszystkich zasobów w szablonie, zobacz Przewodnik [po szablonie usługi Azure Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

To trwa około 10 minut, aby wykonać te kroki.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

W tym kroku upewnij się, że program Visual Studio jest zainstalowany i utworzyć aplikację konsoli używane do wdrażania szablonu.

1. Jeśli jeszcze tego nie zrobiłeś, zainstaluj [program Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Wybierz pozycję **.NET desktop development** na stronie Obciążenia, a następnie kliknij pozycję **Zainstaluj**. W podsumowaniu widać, że **narzędzia programistyczne .NET Framework 4 - 4.6** są automatycznie wybierane. Jeśli program Visual Studio został już zainstalowany, można dodać obciążenie platformy .NET przy użyciu programu Visual Studio Launcher.
2. W programie Visual Studio kliknij pozycję **Plik** > **nowego** > **projektu**.
3. W **obszarze Szablony** > **Visual C#** wybierz pozycję **Aplikacja konsoli (.NET Framework)** wprowadź *myDotnetProject* dla nazwy projektu, wybierz lokalizację projektu, a następnie kliknij przycisk **OK**.

## <a name="install-the-packages"></a>Zainstaluj pakiety

Pakiety NuGet są najprostszym sposobem zainstalowania bibliotek, które należy wykonać te kroki. Aby uzyskać biblioteki, które są potrzebne w programie Visual Studio, wykonaj następujące kroki:

1. Kliknij pozycję **Tools** > **Nuget Package Manager**, a następnie kliknij pozycję **Konsola Menedżera pakietów**.
2. Wpisz następujące polecenia w konsoli:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Tworzenie plików

W tym kroku należy utworzyć plik szablonu, który wdraża zasoby i plik parametrów, który dostarcza wartości parametrów do szablonu. Należy również utworzyć plik autoryzacji, który jest używany do wykonywania operacji usługi Azure Resource Manager.

### <a name="create-the-template-file"></a>Tworzenie pliku szablonu

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy *myDotnetProject* > **Dodaj** > **nowy element,** a następnie wybierz polecenie Plik **tekstowy** w *elementów programu Visual C#*. Nazwij plik *CreateVMTemplate.json*, a następnie kliknij przycisk **Dodaj**.
2. Dodaj ten kod JSON do utworzonego pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Zapisz plik CreateVMTemplate.json.

### <a name="create-the-parameters-file"></a>Tworzenie pliku parametrów

Aby określić wartości parametrów zasobów w szablonie, należy utworzyć plik parametrów zawierający wartości.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy *myDotnetProject* > **Dodaj** > **nowy element,** a następnie wybierz polecenie Plik **tekstowy** w *elementów programu Visual C#*. Nazwij plik *Parameters.json*, a następnie kliknij przycisk **Dodaj**.
2. Dodaj ten kod JSON do utworzonego pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Zapisz plik Parameters.json.

### <a name="create-the-authorization-file"></a>Tworzenie pliku autoryzacji

Przed wdrożeniem szablonu upewnij się, że masz dostęp do [jednostki usługi Active Directory](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Od jednostki usługi można uzyskać token do uwierzytelniania żądań do usługi Azure Resource Manager. Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, który jest potrzebny w pliku autoryzacji.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy *myDotnetProject* > **Dodaj** > **nowy element,** a następnie wybierz polecenie Plik **tekstowy** w *elementów programu Visual C#*. Nazwij plik *azureauth.properties,* a następnie kliknij przycisk **Dodaj**.
2. Dodaj następujące właściwości autoryzacji:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Zamień ** &lt;identyfikator&gt; subskrypcji** identyfikatorem subskrypcji, ** &lt;identyfikatorem&gt; aplikacji** identyfikatorem usługi Active Directory na identyfikator aplikacji usługi Active Directory, ** &lt;kluczem&gt; uwierzytelniania** kluczem aplikacji i ** &lt;identyfikatorem dzierżawy&gt; ** identyfikatorem dzierżawy.

3. Zapisz plik azureauth.properties.
4. Ustaw zmienną środowiskową w systemie Windows o nazwie AZURE_AUTH_LOCATION z pełną ścieżką do utworzonego pliku autoryzacji, na przykład można użyć następującego polecenia programu PowerShell:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Tworzenie klienta zarządzania

1. Otwórz plik Program.cs dla utworzonego projektu. Następnie dodaj je przy użyciu instrukcji do istniejących instrukcji w górnej części pliku:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Aby utworzyć klienta zarządzania, dodaj ten kod do Main metody:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby określić wartości dla aplikacji, dodaj kod do Main metody:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Szablon i parametry są wdrażane z konta magazynu na platformie Azure. W tym kroku należy utworzyć konto i przekazać pliki. 

Aby utworzyć konto, dodaj ten kod do Metody głównej:

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdrażanie szablonu i parametrów z utworzonego konta magazynu. 

Aby wdrożyć szablon, dodaj ten kod do Main metody:

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Usuwanie zasobów

Ponieważ są naliczane opłaty za zasoby używane na platformie Azure, zawsze jest dobrą praktyką, aby usunąć zasoby, które nie są już potrzebne. Nie trzeba usuwać każdego zasobu oddzielnie z grupy zasobów. Usuń grupę zasobów i wszystkie jej zasoby są automatycznie usuwane. 

Aby usunąć grupę zasobów, dodaj ten kod do metody Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Powinno upłynąć około pięciu minut, aby ta aplikacja konsoli działała całkowicie od początku do końca. 

1. Aby uruchomić aplikację konsoli, kliknij przycisk **Start**.

2. Przed naciśnięciem **klawisza Enter,** aby rozpocząć usuwanie zasobów, może upłynąć kilka minut, aby zweryfikować tworzenie zasobów w witrynie Azure portal. Kliknij stan wdrożenia, aby wyświetlić informacje o wdrożeniu.

## <a name="next-steps"></a>Następne kroki

* Jeśli wystąpiły problemy z wdrożeniem, następnym krokiem będzie przyjrzenie się [rozwiązywaniu typowych błędów wdrażania platformy Azure za pomocą usługi Azure Resource Manager.](../../resource-manager-common-deployment-errors.md)
* Dowiedz się, jak wdrożyć maszynę wirtualną i jej zasoby pomocnicze, przeglądając [wdrażanie maszyny wirtualnej platformy Azure przy użyciu języka C#](csharp.md).