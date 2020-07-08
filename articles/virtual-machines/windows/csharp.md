---
title: 'Tworzenie maszyny wirtualnej platformy Azure i zarządzanie nią przy użyciu języka C #'
description: Użyj języka C# i Azure Resource Manager, aby wdrożyć maszynę wirtualną i wszystkie jej zasoby pomocnicze.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 4fd7ccc7b6df85397fd547f8e1e48b776f12c0df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84234515"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi na platformie Azure przy użyciu języka C # #

[Maszyna wirtualna platformy Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) wymaga kilku dodatkowych zasobów platformy Azure. W tym artykule opisano tworzenie i usuwanie zasobów maszyn wirtualnych oraz zarządzanie nimi przy użyciu języka C#. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu programu Visual Studio
> * Zainstaluj pakiet
> * Utwórz poświadczenia
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

Wykonanie tych kroków trwa około 20 minut.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Jeśli jeszcze tego nie zrobiono, zainstaluj [program Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Wybierz pozycję **Programowanie aplikacji klasycznych platformy .NET** na stronie obciążenia, a następnie kliknij przycisk **Zainstaluj**. W podsumowaniu można zobaczyć, że **.NET Framework narzędzia deweloperskie 4-4,6** są automatycznie wybierane. Jeśli masz już zainstalowany program Visual Studio, możesz dodać obciążenie .NET przy użyciu programu uruchamiania programu Visual Studio.
2. W programie Visual Studio kliknij pozycję **plik**  >  **Nowy**  >  **projekt**.
3. W obszarze **Szablony**  >  **Visual C#** wybierz pozycję **aplikacja konsoli (.NET Framework)**, wprowadź *myDotnetProject* jako nazwę projektu, wybierz lokalizację projektu, a następnie kliknij przycisk **OK**.

## <a name="install-the-package"></a>Zainstaluj pakiet

Pakiety NuGet to najprostszy sposób instalacji bibliotek, które należy wykonać, aby zakończyć te kroki. Aby uzyskać biblioteki, które są potrzebne w programie Visual Studio, wykonaj następujące czynności:

1. Kliknij kolejno pozycje **Narzędzia**  >  **Menedżer pakietów NuGet**, a następnie kliknij pozycję **konsola Menedżera pakietów**.
2. Wpisz następujące polecenie w konsoli programu:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Utwórz poświadczenia

Przed rozpoczęciem tego kroku upewnij się, że masz dostęp do jednostki [usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, które są potrzebne w późniejszym kroku.

### <a name="create-the-authorization-file"></a>Utwórz plik autoryzacji

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
4. Ustaw zmienną środowiskową w systemie Windows o nazwie AZURE_AUTH_LOCATION z pełną ścieżką do pliku autoryzacji, który został utworzony. Na przykład można użyć następującego polecenia programu PowerShell:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Tworzenie klienta zarządzania

1. Otwórz plik Program.cs dla utworzonego projektu. Następnie Dodaj te instrukcje przy użyciu instrukcji do istniejących instrukcji w górnej części pliku:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
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

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Wszystkie zasoby muszą być zawarte w [grupie zasobów](../../azure-resource-manager/management/overview.md).

Aby określić wartości dla aplikacji i utworzyć grupę zasobów, Dodaj ten kod do metody Main:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Tworzenie zestawu dostępności

[Zestawy dostępności](tutorial-availability-sets.md) ułatwiają zarządzanie maszynami wirtualnymi używanymi przez aplikację.

Aby utworzyć zestaw dostępności, Dodaj następujący kod do metody Main:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Tworzenie publicznego adresu IP

[Publiczny adres IP](../../virtual-network/public-ip-addresses.md) jest wymagany do komunikowania się z maszyną wirtualną.

Aby utworzyć publiczny adres IP dla maszyny wirtualnej, Dodaj ten kod do metody Main:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

Maszyna wirtualna musi znajdować się w podsieci [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

Aby utworzyć podsieć i sieć wirtualną, Dodaj ten kod do metody Main:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Utwórz interfejs sieciowy

Maszyna wirtualna musi mieć interfejs sieciowy, aby komunikować się z siecią wirtualną.

Aby utworzyć interfejs sieciowy, Dodaj następujący kod do metody Main:

```csharp
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Teraz, gdy wszystkie zasoby pomocnicze zostały utworzone, można utworzyć maszynę wirtualną.

Aby utworzyć maszynę wirtualną, Dodaj ten kod do metody Main:

```csharp
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Ten samouczek tworzy maszynę wirtualną z uruchomioną wersją systemu operacyjnego Windows Server. Aby dowiedzieć się więcej na temat wybierania innych obrazów, zobacz [nawigowanie i wybieranie obrazów maszyn wirtualnych platformy Azure za pomocą programu Windows PowerShell i interfejsu wiersza polecenia platformy Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Jeśli chcesz użyć istniejącego dysku zamiast obrazu z witryny Marketplace, użyj tego kodu:

```csharp
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Wykonywanie zadań zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto możesz chcieć utworzyć kod w celu zautomatyzowania powtarzalnych lub złożonych zadań.

Jeśli chcesz zrobić coś z maszyną wirtualną, musisz uzyskać jego wystąpienie:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Pobierz informacje o maszynie wirtualnej

Aby uzyskać informacje o maszynie wirtualnej, Dodaj ten kod do metody Main:

```csharp
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Można zatrzymać maszynę wirtualną i zachować wszystkie jej ustawienia, ale w dalszym ciągu korzystać z niej lub można zatrzymać maszynę wirtualną i cofnąć jej przydział. Po cofnięciu przydziału maszyny wirtualnej wszystkie skojarzone z nią zasoby są również cofane i rozliczane.

Aby zatrzymać maszynę wirtualną bez cofania jej przydziału, Dodaj ten kod do metody Main:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Jeśli chcesz cofnąć alokację maszyny wirtualnej, Zmień wywołanie wyłączenie na ten kod:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej

Aby uruchomić maszynę wirtualną, Dodaj ten kod do metody Main:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Zmiana rozmiaru maszyny wirtualnej

Podczas wybierania rozmiaru maszyny wirtualnej należy wziąć pod uwagę wiele aspektów wdrożenia. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](sizes.md).  

Aby zmienić rozmiar maszyny wirtualnej, Dodaj ten kod do metody Main:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Dodawanie dysku danych do maszyny wirtualnej

Aby dodać dysk danych do maszyny wirtualnej, Dodaj ten kod do metody Main. Ten przykład dodaje dysk danych o rozmiarze 2 GB, Han a LUN 0 i typ buforowania ReadWrite:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Usuwanie zasobów

Ze względu na to, że opłaty są naliczone za zasoby używane na platformie Azure, zawsze warto usunąć zasoby, które nie są już potrzebne. Jeśli chcesz usunąć maszyny wirtualne i wszystkie zasoby pomocnicze, wystarczy usunąć grupę zasobów.

Aby usunąć grupę zasobów, Dodaj następujący kod do metody Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Ukończenie działania tej aplikacji konsolowej od początku do końca trwa około pięciu minut. 

1. Aby uruchomić aplikację konsolową, kliknij przycisk **Uruchom**.

2. Przed naciśnięciem klawisza **Enter** w celu rozpoczęcia usuwania zasobów można sprawdzić poprawność tworzenia zasobów w Azure Portal. Kliknij stan wdrożenia, aby wyświetlić informacje o wdrożeniu.

## <a name="next-steps"></a>Następne kroki
* Skorzystaj z szablonu, aby utworzyć maszynę wirtualną przy użyciu informacji w temacie [Wdrażanie maszyny wirtualnej platformy Azure przy użyciu języka C# i szablonu Menedżer zasobów](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Dowiedz się więcej o korzystaniu z [bibliotek platformy Azure dla platformy .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
