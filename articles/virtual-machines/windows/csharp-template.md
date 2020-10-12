---
title: Wdrażanie maszyny wirtualnej przy użyciu języka C# i szablonu Menedżer zasobów
description: Dowiedz się, jak używać języka C# i szablonu Menedżer zasobów, aby wdrożyć maszynę wirtualną platformy Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 07/14/2017
ms.author: cynthn
ms.custom: devx-track-csharp
ms.openlocfilehash: 779a09532790ea272d8c95ac28f8c152216efc5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002967"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Wdróż maszynę wirtualną platformy Azure przy użyciu języka C# i szablonu Menedżer zasobów

W tym artykule opisano sposób wdrażania szablonu Azure Resource Manager przy użyciu języka C#. Tworzony szablon wdraża pojedynczą maszynę wirtualną z systemem Windows Server w nowej sieci wirtualnej z jedną podsiecią.

Aby uzyskać szczegółowy opis zasobu maszyny wirtualnej, zobacz [maszyny wirtualne w szablonie Azure Resource Manager](template-description.md). Aby uzyskać więcej informacji na temat wszystkich zasobów w szablonie, zobacz [Przewodnik po szablonach Azure Resource Manager](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

Wykonanie tych kroków trwa około 10 minut.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

W tym kroku upewnij się, że program Visual Studio jest zainstalowany i utworzysz aplikację konsolową służącą do wdrażania szablonu.

1. Jeśli jeszcze tego nie zrobiono, zainstaluj [program Visual Studio](/visualstudio/install/install-visual-studio). Wybierz pozycję **Programowanie aplikacji klasycznych platformy .NET** na stronie obciążenia, a następnie kliknij przycisk **Zainstaluj**. W podsumowaniu można zobaczyć, że **.NET Framework narzędzia deweloperskie 4-4,6** są automatycznie wybierane. Jeśli masz już zainstalowany program Visual Studio, możesz dodać obciążenie .NET przy użyciu programu uruchamiania programu Visual Studio.
2. W programie Visual Studio kliknij pozycję **plik**  >  **Nowy**  >  **projekt**.
3. W obszarze **Szablony**  >  **Visual C#** wybierz pozycję **aplikacja konsoli (.NET Framework)**, wprowadź *myDotnetProject* jako nazwę projektu, wybierz lokalizację projektu, a następnie kliknij przycisk **OK**.

## <a name="install-the-packages"></a>Zainstaluj pakiety

Pakiety NuGet to najprostszy sposób instalacji bibliotek, które należy wykonać, aby zakończyć te kroki. Aby uzyskać biblioteki, które są potrzebne w programie Visual Studio, wykonaj następujące czynności:

1. Kliknij kolejno pozycje **Narzędzia**  >  **Menedżer pakietów NuGet**, a następnie kliknij pozycję **konsola Menedżera pakietów**.
2. Wpisz następujące polecenia w konsoli programu:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Tworzenie plików

W tym kroku utworzysz plik szablonu służący do wdrażania zasobów i pliku parametrów, które dostarczają wartości parametrów do szablonu. Tworzony jest również plik autoryzacji służący do wykonywania Azure Resource Manager operacji.

### <a name="create-the-template-file"></a>Utwórz plik szablonu

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy pozycję *myDotnetProject*  >  **Dodaj**  >  **nowy element**, a następnie wybierz polecenie **plik tekstowy** w *elementach języka Visual C#*. Nazwij plik *CreateVMTemplate.jsna*, a następnie kliknij przycisk **Dodaj**.
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

3. Zapisz CreateVMTemplate.jspliku.

### <a name="create-the-parameters-file"></a>Utwórz plik parametrów

Aby określić wartości parametrów zasobów w szablonie, należy utworzyć plik parametrów zawierający wartości.

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy pozycję *myDotnetProject*  >  **Dodaj**  >  **nowy element**, a następnie wybierz polecenie **plik tekstowy** w *elementach języka Visual C#*. Nazwij plik *Parameters.jsna*, a następnie kliknij przycisk **Dodaj**.
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

4. Zapisz Parameters.jspliku.

### <a name="create-the-authorization-file"></a>Utwórz plik autoryzacji

Przed wdrożeniem szablonu upewnij się, że masz dostęp do jednostki [usługi Active Directory](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Z jednostki usługi uzyskuje się token do uwierzytelniania żądań do Azure Resource Manager. Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, które są potrzebne w pliku autoryzacji.

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy pozycję *myDotnetProject*  >  **Dodaj**  >  **nowy element**, a następnie wybierz polecenie **plik tekstowy** w *elementach języka Visual C#*. Nazwij plik *azureauth. Properties*, a następnie kliknij przycisk **Dodaj**.
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

    Zastąp ** &lt; Identyfikator Subscription &gt; -ID** identyfikatorem subskrypcji, ** &lt; identyfikatorem &gt; aplikacji** z identyfikatorem aplikacji Active Directory, ** &lt; &gt; kluczem uwierzytelniania** i kluczem aplikacji oraz ** &lt; identyfikatorem &gt; dzierżawy** .

3. Zapisz plik azureauth. Properties.
4. Ustaw zmienną środowiskową w systemie Windows o nazwie AZURE_AUTH_LOCATION z pełną ścieżką do pliku autoryzacji, który został utworzony, na przykład możesz użyć następującego polecenia programu PowerShell:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Tworzenie klienta zarządzania

1. Otwórz plik Program.cs dla utworzonego projektu. Następnie Dodaj te instrukcje przy użyciu instrukcji do istniejących instrukcji w górnej części pliku:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Aby utworzyć klienta zarządzania programu, Dodaj następujący kod do metody Main:

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

Aby określić wartości dla aplikacji, Dodaj kod do metody Main:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Szablon i parametry są wdrażane na podstawie konta magazynu na platformie Azure. W tym kroku utworzysz konto i przekażesz pliki. 

Aby utworzyć konto, Dodaj następujący kod do metody Main:

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

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Wdróż szablon i parametry z utworzonego konta magazynu. 

Aby wdrożyć szablon, Dodaj następujący kod do metody Main:

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

Ze względu na to, że opłaty są naliczone za zasoby używane na platformie Azure, zawsze warto usunąć zasoby, które nie są już potrzebne. Nie trzeba usuwać poszczególnych zasobów oddzielnie z grupy zasobów. Usunięcie grupy zasobów i wszystkich jej zasobów zostanie automatycznie usunięte. 

Aby usunąć grupę zasobów, Dodaj następujący kod do metody Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Ukończenie działania tej aplikacji konsolowej od początku do końca trwa około pięciu minut. 

1. Aby uruchomić aplikację konsolową, kliknij przycisk **Uruchom**.

2. Przed naciśnięciem klawisza **Enter** w celu rozpoczęcia usuwania zasobów można sprawdzić poprawność tworzenia zasobów w Azure Portal. Kliknij stan wdrożenia, aby wyświetlić informacje o wdrożeniu.

## <a name="next-steps"></a>Następne kroki

* Jeśli wystąpiły problemy ze wdrożeniem, następnym krokiem jest zapoznaj się z tematem [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md).
* Dowiedz się, jak wdrożyć maszynę wirtualną i jej zasoby pomocnicze, przeglądając artykuł [Wdrażanie maszyny wirtualnej platformy Azure przy użyciu języka C#](../../azure-resource-manager/templates/deploy-rest.md).