---
title: Wdrożenie Service Fabric platformy Azure z usługą FabricClient
description: Użyj interfejsów API FabricClient, aby wdrażać i usuwać aplikacje w Service Fabric.
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 343a37c983b1d64a4b1986913d9d6fd648a113fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785553"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Wdrażanie i usuwanie aplikacji przy użyciu FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Program PowerShell](service-fabric-deploy-remove-applications.md)
> * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [Interfejsy API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Gdy [Typ aplikacji zostanie spakowany][10], jest gotowy do wdrożenia w klastrze Service Fabric platformy Azure. Wdrożenie obejmuje następujące trzy kroki:

1. Przekaż pakiet aplikacji do magazynu obrazów
2. Rejestrowanie typu aplikacji
3. Usuń pakiet aplikacji z magazynu obrazów
4. Tworzenie wystąpienia aplikacji

Po wdrożeniu aplikacji i uruchomieniu wystąpienia w klastrze można usunąć wystąpienie aplikacji i jego typ aplikacji. Aby całkowicie usunąć aplikację z klastra, wykonaj następujące czynności:

1. Usuwanie (lub usuwanie) uruchomionego wystąpienia aplikacji
2. Wyrejestruj typ aplikacji, jeśli nie jest już potrzebny

Jeśli używasz programu Visual Studio do wdrażania i debugowania aplikacji w lokalnym klastrze programistycznym, wszystkie powyższe kroki są obsługiwane automatycznie za pomocą skryptu programu PowerShell.  Ten skrypt znajduje się w folderze *skryptów* projektu aplikacji. Ten artykuł zawiera informacje o tym, co robi skrypt, aby wykonać te same operacje poza programem Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Łączenie z klastrem
Połącz się z klastrem, tworząc wystąpienie [FabricClient](/dotnet/api/system.fabric.fabricclient) przed uruchomieniem dowolnego z przykładów kodu w tym artykule. Aby zapoznać się z przykładami łączenia się z lokalnym klastrem projektowym lub klastrem zdalnym lub klastrem zabezpieczonym przy użyciu Azure Active Directory, certyfikatów x509 lub Active Directory Windows, zobacz [nawiązywanie połączenia z zabezpieczonym klastrem](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Aby nawiązać połączenie z lokalnym klastrem programistycznym, uruchom następujący przykład:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Przekaż pakiet aplikacji
Załóżmy, że tworzysz aplikację i pakujesz ją *w programie* Visual Studio. Domyślnie nazwa typu aplikacji wymieniona w ApplicationManifest.xml to "noapplicationtype".  Pakiet aplikacji, który zawiera wymagany manifest aplikacji, manifesty usług i kod/pakiety danych, znajduje się w lokalizacji *C:\Users \& lt; username &gt; \Documents\Visual Studio 2019 \ Projects\MyApplication\MyApplication\pkg\Debug*.

Przekazywanie pakietu aplikacji umieszcza go w lokalizacji dostępnej dla wewnętrznych składników Service Fabric. Service Fabric sprawdza pakiet aplikacji podczas rejestracji pakietu aplikacji. Jeśli jednak chcesz zweryfikować pakiet aplikacji lokalnie (czyli przed przekazaniem), użyj polecenia cmdlet [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .

Interfejs API [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) przekazuje pakiet aplikacji do magazynu obrazów klastra. 

Jeśli pakiet aplikacji jest duży i/lub zawiera wiele plików, można [go skompresować](service-fabric-package-apps.md#compress-a-package) i skopiować do magazynu obrazów przy użyciu programu PowerShell. Kompresja zmniejsza rozmiar i liczbę plików.

Zobacz [opis parametrów połączenia magazynu obrazu](service-fabric-image-store-connection-string.md) , aby uzyskać dodatkowe informacje na temat parametrów połączenia magazynu obrazów i magazynu obrazów.

## <a name="register-the-application-package"></a>Rejestrowanie pakietu aplikacji
Typ i wersja aplikacji zadeklarowanych w manifeście aplikacji stają się dostępne do użycia podczas rejestrowania pakietu aplikacji. System odczytuje pakiet przekazany w poprzednim kroku, weryfikuje pakiet, przetwarza zawartość pakietu i kopiuje przetworzony pakiet do wewnętrznej lokalizacji systemowej.  

Interfejs API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) rejestruje typ aplikacji w klastrze i udostępnia go do wdrożenia.

Interfejs API [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) zawiera informacje o wszystkich pomyślnie zarejestrowanych typach aplikacji. Za pomocą tego interfejsu API można określić, kiedy ma zostać wykonana Rejestracja.

## <a name="remove-an-application-package-from-the-image-store"></a>Usuwanie pakietu aplikacji z magazynu obrazów
Zaleca się usunięcie pakietu aplikacji po pomyślnym zarejestrowaniu aplikacji.  Usuwanie pakietów aplikacji z magazynu obrazów zwalnia zasoby systemowe.  Utrzymywanie nieużywanych pakietów aplikacji zużywa magazyn dyskowy i prowadzi do problemów z wydajnością aplikacji. Usuń pakiet aplikacji z magazynu obrazów przy użyciu interfejsu API [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) .

## <a name="create-an-application-instance"></a>Tworzenie wystąpienia aplikacji
Można utworzyć wystąpienie aplikacji z dowolnego typu aplikacji, który został pomyślnie zarejestrowany przy użyciu interfejsu API [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) . Nazwa każdej aplikacji musi zaczynać się od schematu *"Fabric:"* i musi być unikatowa dla każdego wystąpienia aplikacji (w klastrze). Tworzone są również wszystkie domyślne usługi zdefiniowane w manifeście aplikacji typu aplikacji docelowej.

Dla każdej używanej wersji zarejestrowanego typu aplikacji można utworzyć wiele wystąpień aplikacji. Każde wystąpienie aplikacji działa w izolacji z własnym katalogiem roboczym i zestawem procesów.

Aby sprawdzić, które nazwane aplikacje i usługi są uruchomione w klastrze, uruchom interfejsy API [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) i [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) .

## <a name="create-a-service-instance"></a>Tworzenie wystąpienia usługi
Można utworzyć wystąpienie usługi z typu usługi przy użyciu interfejsu API [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) .  Jeśli usługa jest zadeklarowana jako usługa domyślna w manifeście aplikacji, usługa zostanie utworzona przy tworzeniu wystąpienia aplikacji.  Wywołanie interfejsu API [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) dla usługi, która została już utworzona, zwróci wyjątek typu fabricexception. Wyjątek będzie zawierać kod błędu o wartości FabricErrorCode. ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Usuwanie wystąpienia usługi
Gdy wystąpienie usługi nie jest już potrzebne, można je usunąć z działającego wystąpienia aplikacji, wywołując interfejs API [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) .  

> [!WARNING]
> Tej operacji nie można cofnąć i nie można odzyskać stanu usługi.

## <a name="remove-an-application-instance"></a>Usuwanie wystąpienia aplikacji
Gdy wystąpienie aplikacji nie jest już potrzebne, można trwale usunąć je za pomocą nazwy przy użyciu interfejsu API [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) . [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automatycznie usuwa wszystkie usługi należące do aplikacji, a także trwale usuwa wszystkie Stany usługi.

> [!WARNING]
> Tej operacji nie można odwrócić i nie można odzyskać stanu aplikacji.

## <a name="unregister-an-application-type"></a>Wyrejestrowywanie typu aplikacji
Jeśli określona wersja typu aplikacji nie jest już wymagana, należy wyrejestrować tę konkretną wersję typu aplikacji przy użyciu interfejsu API [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) . Wyrejestrowywanie nieużywanych wersji typów aplikacji zwalnia miejsce w magazynie używane przez magazyn obrazów. Wersję typu aplikacji można wyrejestrować, o ile nie są tworzone żadne aplikacje dla tej wersji typu aplikacji. Ponadto typ aplikacji nie może mieć oczekujących uaktualnień aplikacji, które odwołują się do tej wersji typu aplikacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage prosi o ImageStoreConnectionString
Środowisko zestawu SDK Service Fabric powinno mieć już skonfigurowane poprawne ustawienia domyślne. Ale w razie potrzeby ImageStoreConnectionString dla wszystkich poleceń powinna być zgodna z wartością używaną przez klaster Service Fabric. ImageStoreConnectionString można znaleźć w manifeście klastra pobranym przy użyciu poleceń [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) i Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Polecenie cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , które jest częścią modułu Service Fabric zestawu SDK programu PowerShell, służy do pobierania parametrów połączenia magazynu obrazu.  Aby zaimportować moduł SDK, uruchom polecenie:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


ImageStoreConnectionString znajduje się w manifeście klastra:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Zobacz [opis parametrów połączenia magazynu obrazu](service-fabric-image-store-connection-string.md) , aby uzyskać dodatkowe informacje na temat parametrów połączenia magazynu obrazów i magazynu obrazów.

### <a name="deploy-large-application-package"></a>Wdróż pakiet dużej aplikacji
Problem: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API limit czasu dla dużego pakietu aplikacji (kolejność GB).
Spróbuj:
- Określ większy limit czasu dla metody [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) z `timeout` parametrem. Domyślnie limit czasu to 30 minut.
- Sprawdź połączenie sieciowe między maszyną źródłową a klastrem. Jeśli połączenie jest powolne, rozważ użycie komputera z lepszym połączeniem sieciowym.
Jeśli komputer kliencki znajduje się w innym regionie niż klaster, rozważ użycie komputera klienckiego w tym samym lub tym samym regionie co klaster.
- Sprawdź, czy używasz ograniczania zewnętrznego. Na przykład jeśli magazyn obrazów jest skonfigurowany do korzystania z usługi Azure Storage, przekazywanie może być ograniczone.

Problem: przekazywanie pakietu zakończyło się pomyślnie, ale [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API limit czasu. Spróbował
- [Kompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów.
Kompresja zmniejsza rozmiar i liczbę plików, co z kolei zmniejsza ilość ruchu i pracy, które Service Fabric muszą zostać wykonane. Operacje przekazywania mogą być wolniejsze (zwłaszcza w przypadku dołączenia czasu kompresji), ale rejestrowanie i Wyrejestrowywanie typu aplikacji jest szybsze.
- Określ większy limit czasu dla interfejsu API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) z `timeout` parametrem.

### <a name="deploy-application-package-with-many-files"></a>Wdróż pakiet aplikacji z wieloma plikami
Problem: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) limit czasu dla pakietu aplikacji z wieloma plikami (kolejność tysięcy).
Spróbuj:
- [Kompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów. Kompresja zmniejsza liczbę plików.
- Określ większy limit czasu dla [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) z `timeout` parametrem.

## <a name="code-example"></a>Przykładowy kod
Poniższy przykład kopiuje pakiet aplikacji do magazynu obrazów i Inicjuje obsługę administracyjną typu aplikacji. Następnie przykład tworzy wystąpienie aplikacji i tworzy wystąpienie usługi. Na koniec przykład usuwa wystąpienie aplikacji, anuluje wymagania typu aplikacji i usuwa pakiet aplikacji z magazynu obrazów.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Następne kroki
[Service Fabric uaktualniania aplikacji](service-fabric-application-upgrade.md)

[Service Fabric wprowadzenie do kondycji](service-fabric-health-introduction.md)

[Diagnozowanie i rozwiązywanie problemów z usługą Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelowanie aplikacji w Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
