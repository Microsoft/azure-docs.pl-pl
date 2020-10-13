---
title: Wdrażanie Service Fabric platformy Azure za pomocą programu PowerShell
description: Dowiedz się więcej o usuwaniu i wdrażaniu aplikacji na platformie Azure Service Fabric i sposobach wykonywania tych akcji w programie PowerShell.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 8bc4557c5d0d59330c7e91a4b3fdce83cfbf334c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827436"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Program PowerShell](service-fabric-deploy-remove-applications.md)
> * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [Interfejsy API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Gdy [Typ aplikacji zostanie spakowany][10], jest gotowy do wdrożenia w klastrze Service Fabric platformy Azure. Wdrożenie obejmuje następujące trzy kroki:

1. Przekaż pakiet aplikacji do magazynu obrazów.
2. Zarejestruj typ aplikacji z ścieżką względną magazynu obrazu.
3. Utwórz wystąpienie aplikacji.

Gdy wdrożona aplikacja nie jest już potrzebna, można usunąć wystąpienie aplikacji i jej typ aplikacji. Aby całkowicie usunąć aplikację z klastra, należy wykonać następujące czynności:

1. Usuń (lub Usuń) uruchomione wystąpienie aplikacji.
2. Wyrejestruj typ aplikacji, jeśli nie jest już potrzebny.
3. Usuń pakiet aplikacji z magazynu obrazów.

Jeśli używasz programu Visual Studio do wdrażania i debugowania aplikacji w lokalnym klastrze programistycznym, wszystkie powyższe kroki są obsługiwane automatycznie za pomocą skryptu programu PowerShell.  Ten skrypt znajduje się w folderze *skryptów* projektu aplikacji. Ten artykuł zawiera informacje o tym, co robi skrypt, aby można było wykonywać te same operacje poza programem Visual Studio. 

Innym sposobem wdrożenia aplikacji jest użycie zewnętrznej obsługi administracyjnej. Pakiet aplikacji można [spakować jako `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) i przekazać do magazynu zewnętrznego. W takim przypadku przekazywanie do magazynu obrazów nie jest konieczne. Wdrożenie wymaga następujących kroków:

1. Przekaż `sfpkg` do magazynu zewnętrznego. Magazyn zewnętrzny może być dowolnym magazynem, który uwidacznia punkt końcowy HTTP lub https.
2. Zarejestruj typ aplikacji przy użyciu zewnętrznego identyfikatora URI pobierania i informacji o typie aplikacji.
2. Utwórz wystąpienie aplikacji.

W obszarze Czyszczenie Usuń wystąpienia aplikacji i Wyrejestruj typ aplikacji. Ponieważ pakiet nie został skopiowany do magazynu obrazów, nie ma lokalizacji tymczasowej do oczyszczenia. Inicjowanie obsługi administracyjnej ze sklepu zewnętrznego jest dostępne począwszy od Service Fabric wersji 6,1.

>[!NOTE]
> Program Visual Studio nie obsługuje obecnie udostępniania zewnętrznego.

 

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Przed uruchomieniem jakichkolwiek poleceń programu PowerShell w tym artykule, należy zawsze uruchomić polecenie [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) , aby nawiązać połączenie z klastrem Service Fabric. Aby nawiązać połączenie z lokalnym klastrem programistycznym, uruchom następujące polecenie:

```powershell
Connect-ServiceFabricCluster
```

Aby zapoznać się z przykładami łączenia się ze zdalnym klastrem lub klastrem zabezpieczonym przy użyciu Azure Active Directory, certyfikatów x509 lub Windows Active Directory zobacz [łączenie z bezpiecznym klastrem](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Przekaż pakiet aplikacji

Przekazywanie pakietu aplikacji umieszcza go w lokalizacji dostępnej dla wewnętrznych składników Service Fabric.
Jeśli chcesz zweryfikować pakiet aplikacji lokalnie, użyj polecenia cmdlet [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) .

Polecenie [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) przekazuje pakiet aplikacji do magazynu obrazów klastra.

Załóżmy, że tworzysz aplikację i pakujesz ją *w programie* Visual Studio 2015. Domyślnie nazwa typu aplikacji wymieniona w ApplicationManifest.xml to "noapplicationtype".  Pakiet aplikacji, który zawiera wymagany manifest aplikacji, manifesty usługi i pakiety danych/config/dane, znajduje się w lokalizacji *C:\Users \<username\> \Documents\Visual Studio 2015 \ Projects\MyApplication\MyApplication\pkg\Debug*. 

Następujące polecenie wyświetla zawartość pakietu aplikacji:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Jeśli pakiet aplikacji jest duży i/lub zawiera wiele plików, można [go skompresować](service-fabric-package-apps.md#compress-a-package). Kompresja zmniejsza rozmiar i liczbę plików.
Powoduje to szybsze rejestrowanie i Wyrejestrowywanie typu aplikacji. Czas przekazywania może być obecnie wolniejszy, szczególnie w przypadku dołączenia czasu do skompresowania pakietu. 

Aby skompresować pakiet, użyj tego samego polecenia [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) . Kompresję można wykonać niezależnie od przekazywania, przy użyciu `SkipCopy` flagi lub razem z operacją przekazywania. Zastosowanie kompresji w skompresowanym pakiecie to no-op.
Aby zdekompresować skompresowany pakiet, użyj tego samego polecenia [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) z `UncompressPackage` przełącznikiem.

Poniższe polecenie cmdlet kompresuje pakiet bez kopiowania go do magazynu obrazów. Pakiet zawiera teraz pliki spakowane dla `Code` `Config` pakietów i. Aplikacje i manifesty usług nie są spakowane, ponieważ są one zbędne w przypadku wielu operacji wewnętrznych (takich jak udostępnianie pakietów, nazwa typu aplikacji i wyodrębnianie wersji dla pewnych walidacji). Zapakowywanie manifestów spowodowałoby niewydajne wykonywanie tych operacji.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

W przypadku dużych pakietów aplikacji kompresja trwa. Aby uzyskać najlepsze wyniki, Użyj szybkiego dysku SSD. Czasy kompresji i rozmiar skompresowanego pakietu również różnią się w zależności od zawartości pakietu.
Na przykład poniżej przedstawiono statystyki kompresji dla niektórych pakietów, które pokazują początkowy i skompresowany rozmiar pakietu wraz z czasem kompresji.

|Rozmiar początkowy (MB)|Liczba plików|Czas kompresji|Rozmiar skompresowanego pakietu (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Po skompresowaniu pakietu można go przekazać do jednego lub wielu klastrów Service Fabric w razie potrzeby. Mechanizm wdrażania jest taki sam dla skompresowanych i nieskompresowanych pakietów. Skompresowane pakiety są przechowywane jako takie w magazynie obrazów klastra. Pakiety są dekompresowane w węźle przed uruchomieniem aplikacji.


Poniższy przykład przekazuje pakiet do magazynu obrazów do folderu o nazwie "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Jeśli nie określisz parametru *-ApplicationPackagePathInImageStore* , pakiet aplikacji zostanie skopiowany do folderu "Debugowanie" w magazynie obrazów.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** automatycznie wykryje odpowiednie parametry połączenia magazynu obrazu, jeśli sesja programu PowerShell jest połączona z klastrem Service Fabric. W przypadku wersji Service Fabric starszych niż 5,6 argument **-ImageStoreConnectionString** musi być jawnie podany.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Polecenie cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , które jest częścią modułu Service Fabric zestawu SDK programu PowerShell, służy do pobierania parametrów połączenia magazynu obrazu.  Aby zaimportować moduł SDK, uruchom polecenie:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Zobacz [opis parametrów połączenia magazynu obrazu](service-fabric-image-store-connection-string.md) , aby uzyskać dodatkowe informacje na temat parametrów połączenia magazynu obrazów i magazynu obrazów.
>
>
>

Czas potrzebny na przekazanie pakietu różni się w zależności od wielu czynników. Niektóre z tych czynników to liczba plików w pakiecie, rozmiar pakietu i rozmiary plików. Szybkość sieci między maszyną źródłową a klastrem Service Fabric ma także wpływ na czas przekazywania. Domyślny limit czasu dla [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) to 30 minut.
W zależności od opisanych czynników może być konieczne zwiększenie limitu czasu. W przypadku kompresowania pakietu w wywołaniu kopiowania należy również wziąć pod uwagę czas kompresji.



## <a name="register-the-application-package"></a>Rejestrowanie pakietu aplikacji

Typ i wersja aplikacji zadeklarowanych w manifeście aplikacji stają się dostępne do użycia podczas rejestrowania pakietu aplikacji. System odczytuje pakiet przekazany w poprzednim kroku, weryfikuje pakiet, przetwarza zawartość pakietu i kopiuje przetworzony pakiet do wewnętrznej lokalizacji systemowej.  

Uruchom polecenie cmdlet [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) , aby zarejestrować typ aplikacji w klastrze i udostępnić go do wdrożenia:

### <a name="register-the-application-package-copied-to-image-store"></a>Zarejestruj pakiet aplikacji skopiowany do magazynu obrazów

Gdy pakiet został wcześniej skopiowany do magazynu obrazów, operacja Register określa ścieżkę względną w magazynie obrazów.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" jest folderem w magazynie obrazu, w którym znajduje się pakiet aplikacji. Typ aplikacji o nazwie "webapplicationtype" i wersji "1.0.0" (oba znajdują się w manifeście aplikacji) jest teraz zarejestrowany w klastrze.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Rejestrowanie pakietu aplikacji skopiowanego do magazynu zewnętrznego

Service Fabric począwszy od wersji 6,1, Inicjowanie obsługi administracyjnej obsługuje pobieranie pakietu z magazynu zewnętrznego. Identyfikator URI pobierania reprezentuje ścieżkę do [ `sfpkg` pakietu aplikacji](service-fabric-package-apps.md#create-an-sfpkg) , z którego można pobrać pakiet aplikacji przy użyciu protokołów HTTP lub https. Pakiet musi zostać wcześniej przekazany do tej lokalizacji zewnętrznej. Identyfikator URI musi zezwalać na dostęp do odczytu, aby Service Fabric mógł pobrać plik. `sfpkg`Plik musi mieć rozszerzenie ". sfpkg". Operacja aprowizacji powinna obejmować informacje o typie aplikacji, jak zostało to Znalezione w manifeście aplikacji.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

Polecenie [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) zwraca wartość dopiero po pomyślnym zarejestrowaniu pakietu aplikacji przez system. Jak długo trwa Rejestracja zależy od rozmiaru i zawartości pakietu aplikacji. W razie potrzeby parametr **-TimeoutSec** może służyć do zapewnienia dłuższego limitu czasu (domyślny limit czasu to 60 sekund).

Jeśli masz duży pakiet aplikacji lub występują limity czasu, użyj parametru **-Async** . Polecenie zwraca wartość, gdy klaster akceptuje polecenie register. Operacja rejestrowania jest kontynuowana zgodnie z wymaganiami.
Polecenie [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) wyświetla listę wersji typu aplikacji i ich stan rejestracji. Możesz użyć tego polecenia, aby określić, kiedy rejestracja jest gotowa.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Usuwanie pakietu aplikacji z magazynu obrazów

Jeśli pakiet został skopiowany do magazynu obrazów, należy usunąć go z lokalizacji tymczasowej po pomyślnym zarejestrowaniu aplikacji. Usuwanie pakietów aplikacji z magazynu obrazów zwalnia zasoby systemowe. Utrzymywanie nieużywanych pakietów aplikacji zużywa magazyn dyskowy i prowadzi do problemów z wydajnością aplikacji.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Tworzenie aplikacji

Można utworzyć wystąpienie aplikacji z dowolnej wersji typu aplikacji, która została pomyślnie zarejestrowana, za pomocą polecenia cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) . Nazwa każdej aplikacji musi zaczynać się od schematu *"Fabric:"* i musi być unikatowa dla każdego wystąpienia aplikacji. Tworzone są również wszystkie domyślne usługi zdefiniowane w manifeście aplikacji typu aplikacji docelowej.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Dla każdej używanej wersji zarejestrowanego typu aplikacji można utworzyć wiele wystąpień aplikacji. Każde wystąpienie aplikacji działa w izolacji z własnym katalogiem i procesem roboczym.

Aby sprawdzić, które nazwane aplikacje i usługi są uruchomione w klastrze, uruchom polecenia cmdlet [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) i [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) :

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Usuwanie aplikacji

Gdy wystąpienie aplikacji nie jest już potrzebne, można trwale usunąć je przez nazwę za pomocą polecenia cmdlet [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) . Polecenie [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automatycznie usuwa wszystkie usługi należące do aplikacji, a także trwale usuwa wszystkie Stany usługi. 

> [!WARNING]
> Tej operacji nie można odwrócić i nie można odzyskać stanu aplikacji.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Wyrejestrowywanie typu aplikacji

Jeśli określona wersja typu aplikacji nie jest już wymagana, należy wyrejestrować typ aplikacji za pomocą polecenia cmdlet [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) . Wyrejestrowywanie nieużywanych typów aplikacji zwalnia miejsce w magazynie używane przez magazyn obrazów przez usunięcie plików typu aplikacji. Wyrejestrowanie typu aplikacji nie powoduje usunięcia pakietu aplikacji skopiowanego do lokalizacji tymczasowej magazynu obrazu, jeśli użyto kopiowania do magazynu obrazów. Typ aplikacji może zostać wyrejestrowany, o ile nie są dla nich tworzone żadne aplikacje i nie odwołuje się do niego oczekujące uaktualnienia aplikacji.

Uruchom [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) , aby wyświetlić typy aplikacji aktualnie zarejestrowane w klastrze:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Uruchom [Wyrejestrowanie-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) , aby wyrejestrować określony typ aplikacji:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage prosi o ImageStoreConnectionString

Środowisko zestawu SDK Service Fabric powinno mieć już skonfigurowane poprawne ustawienia domyślne. Ale w razie potrzeby ImageStoreConnectionString dla wszystkich poleceń powinna być zgodna z wartością używaną przez klaster Service Fabric. ImageStoreConnectionString można znaleźć w manifeście klastra pobranym przy użyciu poleceń [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) i Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
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

Problem: [ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) dla dużego pakietu aplikacji (kolejność GB).
Spróbuj:
- Określ większy limit czasu dla polecenia [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) z `TimeoutSec` parametrem. Domyślnie limit czasu to 30 minut.
- Sprawdź połączenie sieciowe między maszyną źródłową a klastrem. Jeśli połączenie jest powolne, rozważ użycie komputera z lepszym połączeniem sieciowym.
Jeśli komputer kliencki znajduje się w innym regionie niż klaster, rozważ użycie komputera klienckiego w tym samym lub tym samym regionie co klaster.
- Sprawdź, czy używasz ograniczania zewnętrznego. Na przykład jeśli magazyn obrazów jest skonfigurowany do korzystania z usługi Azure Storage, przekazywanie może być ograniczone.

Problem: pakiet przekazywania został zakończony pomyślnie, ale ServiceFabricApplicationType limit czasu [rejestru](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) . Spróbował
- [Kompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów.
Kompresja zmniejsza rozmiar i liczbę plików, co z kolei zmniejsza ilość ruchu i pracy, które Service Fabric muszą zostać wykonane. Operacje przekazywania mogą być wolniejsze (zwłaszcza w przypadku dołączenia czasu kompresji), ale rejestrowanie i Wyrejestrowanie typu aplikacji jest szybsze.
- Określ większy limit czasu dla elementu [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) z `TimeoutSec` parametrem.
- Określ `Async` przełącznik dla elementu [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Polecenie zwraca wartość, gdy klaster akceptuje polecenie, a rejestracja typu aplikacji jest kontynuowana asynchronicznie. Z tego powodu nie ma potrzeby określania wyższego limitu czasu w tym przypadku. Polecenie [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) wyświetla listę wszystkich pomyślnie zarejestrowanych wersji typu aplikacji i ich stan rejestracji. Możesz użyć tego polecenia, aby określić, kiedy rejestracja jest gotowa.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Wdróż pakiet aplikacji z wieloma plikami

Problem: [ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) limit czasu dla pakietu aplikacji z wieloma plikami (kolejność tysięcy).
Spróbuj:
- [Kompresuj pakiet](service-fabric-package-apps.md#compress-a-package) przed skopiowaniem do magazynu obrazów. Kompresja zmniejsza liczbę plików.
- Określ większy limit czasu dla elementu [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) z `TimeoutSec` parametrem.
- Określ `Async` przełącznik dla elementu [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Polecenie zwraca wartość, gdy klaster akceptuje polecenie, a rejestracja typu aplikacji jest kontynuowana asynchronicznie.
Z tego powodu nie ma potrzeby określania wyższego limitu czasu w tym przypadku. Polecenie [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) wyświetla listę wszystkich pomyślnie zarejestrowanych wersji typu aplikacji i ich stan rejestracji. Możesz użyć tego polecenia, aby określić, kiedy rejestracja jest gotowa.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Następne kroki

[Tworzenie pakietu aplikacji](service-fabric-package-apps.md)

[Service Fabric uaktualniania aplikacji](service-fabric-application-upgrade.md)

[Service Fabric wprowadzenie do kondycji](service-fabric-health-introduction.md)

[Diagnozowanie i rozwiązywanie problemów z usługą Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelowanie aplikacji w Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
