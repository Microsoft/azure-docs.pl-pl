---
title: 'Tworzenie maszyny wirtualnej platformy Azure i zarządzanie nią przy użyciu języka C #'
description: Użyj języka C# i usługi Azure Resource Manager, aby wdrożyć maszynę wirtualną i wszystkie jej zasoby pomocnicze.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 07c66b2955f3df1ffae1a0cb0c2b0888bdc790e9
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082887"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi na platformie Azure przy użyciu języka C # #

[Maszyna wirtualna platformy Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potrzebuje kilku zasobów platformy Azure. W tym artykule opisano tworzenie zasobów maszyn wirtualnych, zarządzanie nimi i usuwanie ich przy użyciu języka C#. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu programu Visual Studio
> * Zainstaluj pakiet
> * Tworzenie poświadczeń
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

To trwa około 20 minut, aby wykonać te kroki.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Jeśli jeszcze tego nie zrobiłeś, zainstaluj [program Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Wybierz pozycję **.NET desktop development** na stronie Obciążenia, a następnie kliknij pozycję **Zainstaluj**. W podsumowaniu widać, że **narzędzia programistyczne .NET Framework 4 - 4.6** są automatycznie wybierane. Jeśli program Visual Studio został już zainstalowany, można dodać obciążenie platformy .NET przy użyciu programu Visual Studio Launcher.
2. W programie Visual Studio kliknij pozycję **Plik** > **nowego** > **projektu**.
3. W **obszarze Szablony** > **Visual C#** wybierz pozycję **Aplikacja konsoli (.NET Framework)** wprowadź *myDotnetProject* dla nazwy projektu, wybierz lokalizację projektu, a następnie kliknij przycisk **OK**.

## <a name="install-the-package"></a>Zainstaluj pakiet

Pakiety NuGet są najprostszym sposobem zainstalowania bibliotek, które należy wykonać te kroki. Aby uzyskać biblioteki, które są potrzebne w programie Visual Studio, wykonaj następujące kroki:

1. Kliknij pozycję **Tools** > **Nuget Package Manager**, a następnie kliknij pozycję **Konsola Menedżera pakietów**.
2. Wpisz to polecenie w konsoli:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Tworzenie poświadczeń

Przed rozpoczęciem tego kroku upewnij się, że masz dostęp do [jednostki usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, które są potrzebne w późniejszym kroku.

### <a name="create-the-authorization-file"></a>Tworzenie pliku autoryzacji

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
4. Ustaw zmienną środowiskową w systemie Windows o nazwie AZURE_AUTH_LOCATION z pełną ścieżką do utworzonego pliku autoryzacji. Można na przykład użyć następującego polecenia programu PowerShell:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Tworzenie klienta zarządzania

1. Otwórz plik Program.cs dla utworzonego projektu. Następnie dodaj je przy użyciu instrukcji do istniejących instrukcji w górnej części pliku:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
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

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Wszystkie zasoby muszą znajdować się w [grupie zasobów](../../azure-resource-manager/management/overview.md).

Aby określić wartości dla aplikacji i utworzyć grupę zasobów, dodaj ten kod do metody Main:

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

[Zestawy dostępności](tutorial-availability-sets.md) ułatwiają utrzymanie maszyn wirtualnych używanych przez aplikację.

Aby utworzyć zestaw dostępności, dodaj ten kod do Main metody:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Tworzenie publicznego adresu IP

[Publiczny adres IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) jest potrzebny do komunikowania się z maszyną wirtualną.

Aby utworzyć publiczny adres IP dla maszyny wirtualnej, dodaj ten kod do metody Main:
   
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

Aby utworzyć podsieć i sieć wirtualną, dodaj ten kod do metody Main:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Tworzenie interfejsu sieciowego

Maszyna wirtualna potrzebuje interfejsu sieciowego do komunikowania się w sieci wirtualnej.

Aby utworzyć interfejs sieciowy, dodaj ten kod do metody Main:

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

Teraz, gdy utworzono wszystkie zasoby pomocnicze, można utworzyć maszynę wirtualną.

Aby utworzyć maszynę wirtualną, dodaj ten kod do metody Main:

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
> W tym samouczku utworzy się maszynę wirtualną z uruchomieniem wersji systemu operacyjnego Windows Server. Aby dowiedzieć się więcej na temat wybierania innych obrazów, zobacz [Nawigowanie i wybieranie obrazów maszyn wirtualnych platformy Azure za pomocą programu Windows PowerShell i interfejsu wiersza polecenia platformy Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Jeśli chcesz użyć istniejącego dysku zamiast obrazu w portalu marketplace, użyj tego kodu:

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

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można utworzyć kod do automatyzacji powtarzających się lub złożonych zadań.

Gdy musisz coś zrobić z maszyną wirtualną, musisz uzyskać jej wystąpienie:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Uzyskaj informacje o maszynie wirtualnej

Aby uzyskać informacje o maszynie wirtualnej, dodaj ten kod do Main metody:

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

Można zatrzymać maszynę wirtualną i zachować wszystkie jej ustawienia, ale nadal być naliczane za nią lub można zatrzymać maszynę wirtualną i przydzielić ją. Gdy maszyna wirtualna jest cofnięta alokacja, wszystkie zasoby skojarzone z nią są również cofnięte i kończy się rozliczenia dla niego.

Aby zatrzymać maszynę wirtualną bez rozdzielania jej, dodaj ten kod do metody Main:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Jeśli chcesz zmienić alokację maszyny wirtualnej, zmień wywołanie usługi PowerOff na ten kod:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej

Aby uruchomić maszynę wirtualną, dodaj ten kod do main metody:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Ponowne rozmiary maszyny Wirtualnej

Wiele aspektów wdrażania należy wziąć pod uwagę przy podejmowaniu decyzji o rozmiarze maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych](sizes.md).  

Aby zmienić rozmiar maszyny wirtualnej, dodaj ten kod do Metody głównej:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Dodawanie dysku danych do maszyny wirtualnej

Aby dodać dysk danych do maszyny wirtualnej, dodaj ten kod do metody Main. W tym przykładzie dodaje dysk danych o rozmiarze 2 GB, han jednostkę LUN 0 i typ buforowania ReadWrite:

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Usuwanie zasobów

Ponieważ są naliczane opłaty za zasoby używane na platformie Azure, zawsze jest dobrą praktyką, aby usunąć zasoby, które nie są już potrzebne. Jeśli chcesz usunąć maszyny wirtualne i wszystkie zasoby pomocnicze, wszystko, co musisz zrobić, to usunąć grupę zasobów.

Aby usunąć grupę zasobów, dodaj ten kod do metody Main:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Powinno upłynąć około pięciu minut, aby ta aplikacja konsoli działała całkowicie od początku do końca. 

1. Aby uruchomić aplikację konsoli, kliknij przycisk **Start**.

2. Przed naciśnięciem **klawisza Enter,** aby rozpocząć usuwanie zasobów, może upłynąć kilka minut, aby zweryfikować tworzenie zasobów w witrynie Azure portal. Kliknij stan wdrożenia, aby wyświetlić informacje o wdrożeniu.

## <a name="next-steps"></a>Następne kroki
* Skorzystaj z używania szablonu do tworzenia maszyny wirtualnej przy użyciu informacji zawartych w [usłudze Wdrażanie maszyny wirtualnej platformy Azure przy użyciu języka C# i szablonu Menedżera zasobów.](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Dowiedz się więcej o korzystaniu z [bibliotek platformy Azure dla platformy .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).
