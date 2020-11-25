---
title: Pakowanie aplikacji Service Fabric platformy Azure
description: Dowiedz się więcej o pakowaniu aplikacji Service Fabric platformy Azure i sposobach przygotowania do wdrożenia w klastrze.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 11a3fdd5dbaef53af321342952f786ed8119689c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021065"
---
# <a name="package-an-application"></a>Tworzenie pakietu aplikacji

W tym artykule opisano sposób tworzenia pakietów aplikacji Service Fabric i przygotowania ich do wdrożenia.

## <a name="package-layout"></a>Układ pakietu

Manifest aplikacji, co najmniej jeden manifest usługi i inne niezbędne pliki pakietu muszą być zorganizowane w określonym układzie do wdrożenia w klastrze Service Fabric. Przykładowe manifesty w tym artykule powinny być zorganizowane w następującej strukturze katalogów:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Nazwy folderów odpowiadają atrybutom **nazw** każdego odpowiadającego elementu. Na przykład, Jeśli manifest usługi zawiera dwa pakiety kodu **o nazwach** **MyCodeB** i, wówczas dwa foldery o tych samych nazwach będą zawierać dane binarne niezbędne dla każdego pakietu kodu.

## <a name="use-setupentrypoint"></a>Użyj SetupEntryPoint

Typowymi scenariuszami używania **SetupEntryPoint** są sytuacje, w których należy uruchomić plik wykonywalny przed uruchomieniem usługi lub trzeba wykonać operację z podniesionymi uprawnieniami. Na przykład:

* Konfigurowanie i Inicjowanie zmiennych środowiskowych wymaganych przez plik wykonywalny usługi. Nie jest ograniczony tylko do plików wykonywalnych pisanych za pośrednictwem modeli programowania Service Fabric. Na przykład npm.exe potrzebuje pewnych zmiennych środowiskowych skonfigurowanych do wdrażania aplikacji node.js.
* Konfigurowanie kontroli dostępu przez zainstalowanie certyfikatów zabezpieczeń.

Aby uzyskać więcej informacji na temat konfigurowania **SetupEntryPoint**, zobacz [Konfigurowanie zasad dla punktu wejścia Instalatora usługi](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurowanie

### <a name="build-a-package-by-using-visual-studio"></a>Tworzenie pakietu przy użyciu programu Visual Studio

Jeśli używasz programu Visual Studio do tworzenia aplikacji, możesz użyć polecenia *Package* , aby automatycznie utworzyć pakiet pasujący do opisanego powyżej układu.

Aby utworzyć pakiet, kliknij prawym przyciskiem myszy projekt aplikacji w *Eksplorator rozwiązań* i wybierz polecenie **pakiet** :

![Pakowanie aplikacji za pomocą programu Visual Studio][vs-package-command]

Po zakończeniu tworzenia pakietów można znaleźć lokalizację pakietu w oknie **danych wyjściowych** . Krok pakowanie odbywa się automatycznie podczas wdrażania lub debugowania aplikacji w programie Visual Studio.

### <a name="build-a-package-by-command-line"></a>Kompiluj pakiet w wierszu polecenia

Możliwe jest również programowo pakowanie aplikacji przy użyciu programu `msbuild.exe` . Pod okapem program Visual Studio działa tak, aby dane wyjściowe były takie same.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testowanie pakietu

Strukturę pakietu można sprawdzić lokalnie za pomocą programu PowerShell za pomocą polecenia [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) .
To polecenie sprawdza, czy występują problemy z analizowaniem manifestu i weryfikują wszystkie odwołania. To polecenie weryfikuje tylko prawidłowość strukturalną katalogów i plików w pakiecie.
Nie sprawdza ona żadnego kodu ani zawartości pakietu danych poza sprawdzeniem, czy istnieją wszystkie niezbędne pliki.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Ten błąd wskazuje, że w pakiecie kodu brakuje pliku *MySetup.bat* , do którego odwołuje się **SetupEntryPoint** manifestu usługi. Po dodaniu brakującego pliku weryfikacja aplikacji kończy się powodzeniem:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Jeśli aplikacja ma zdefiniowane [Parametry aplikacji](service-fabric-manage-multiple-environment-app-configuration.md) , można przekazać je w [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) w celu sprawdzenia poprawności.

Jeśli znasz klaster, w którym aplikacja zostanie wdrożona, zalecamy przekazanie `ImageStoreConnectionString` parametru. W takim przypadku pakiet jest również sprawdzany pod kątem wcześniejszych wersji aplikacji, która jest już uruchomiona w klastrze. Na przykład sprawdzanie poprawności może wykryć, czy pakiet z tą samą wersją, ale z inną zawartością, został już wdrożony.  

Po poprawnym spakowaniu aplikacji i przeprowadzeniu walidacji należy rozważyć kompresję pakietu w celu przyspieszenia operacji wdrażania.

## <a name="compress-a-package"></a>Kompresuj pakiet

Gdy pakiet jest duży lub zawiera wiele plików, można go skompresować do szybszego wdrożenia. Kompresja zmniejsza liczbę plików i rozmiar pakietu.
W przypadku skompresowanego pakietu aplikacji [przekazywanie pakietu aplikacji](service-fabric-deploy-remove-applications.md#upload-the-application-package) może trwać dłużej niż w porównaniu do przekazywania nieskompresowanego pakietu, zwłaszcza jeśli kompresja jest wykonywana w ramach kopiowania. Dzięki kompresji [rejestracja](service-fabric-deploy-remove-applications.md#register-the-application-package) i [Wyrejestrowanie typu aplikacji](service-fabric-deploy-remove-applications.md#unregister-an-application-type) jest szybsza.

Mechanizm wdrażania jest taki sam dla skompresowanych i nieskompresowanych pakietów. Jeśli pakiet jest skompresowany, jest przechowywany w postaci, w której znajduje się w magazynie obrazów klastra, i nie jest kompresowany w węźle przed uruchomieniem aplikacji.
Kompresja zastępuje prawidłowy pakiet Service Fabric ze skompresowaną wersją. Folder musi zezwalać na uprawnienia do zapisu. Wykonywanie kompresji na już skompresowanym pakiecie nie powoduje żadnych zmian.

Pakiet można skompresować, uruchamiając polecenie PowerShell [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) z `CompressPackage` przełącznikiem. Możesz zdekompresować pakiet za pomocą tego samego polecenia, używając `UncompressPackage` przełącznika.

Następujące polecenie kompresuje pakiet bez kopiowania go do magazynu obrazów. Możesz skopiować skompresowany pakiet do jednego lub kilku klastrów Service Fabric, w razie potrzeby, za pomocą polecenia [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) bez `SkipCopy` flagi.
Pakiet zawiera teraz pliki spakowane dla `code` `config` pakietów, i `data` . Manifest aplikacji i manifesty usługi nie są spakowane, ponieważ są one zbędne dla wielu operacji wewnętrznych. Na przykład udostępnianie pakietów, nazwa typu aplikacji i wyodrębnianie wersji dla niektórych walidacji muszą mieć dostęp do manifestów. Zapakowywanie manifestów spowodowałoby niewydajne wykonywanie tych operacji.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Alternatywnie można skompresować i skopiować pakiet za pomocą [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) w jednym kroku.
Jeśli pakiet jest duży, podaj wystarczająco dużo czasu, aby zezwolić na czas na kompresję pakietu i przekazywanie do klastra.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Wewnętrznie, Service Fabric oblicza sumy kontrolne dla pakietów aplikacji na potrzeby walidacji. W przypadku korzystania z kompresji sumy kontrolne są obliczane w spakowanych wersjach każdego pakietu. Generowanie nowego pliku zip z tego samego pakietu aplikacji tworzy różne sumy kontrolne. Aby zapobiec błędom walidacji, należy użyć [udostępniania różnicowego](service-fabric-application-upgrade-advanced.md). W przypadku tej opcji nie należy uwzględniać pakietów niezmienionych w nowej wersji. Zamiast tego należy odwołać się do nich bezpośrednio z nowego manifestu usługi.

Jeśli obsługa różnic nie jest opcją i musisz dołączyć pakiety, wygeneruj nowe wersje dla `code` `config` pakietów, i, `data` Aby uniknąć niezgodności sumy kontrolnej. Generowanie nowych wersji dla niezmienionych pakietów jest konieczne, gdy jest używany skompresowany pakiet, niezależnie od tego, czy Poprzednia wersja używa kompresji, czy nie.

Pakiet jest teraz spakowany prawidłowo, sprawdzony i kompresowany (w razie potrzeby), więc jest gotowy do [wdrożenia](service-fabric-deploy-remove-applications.md) co najmniej jednego klastra Service Fabric.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Kompresuj pakiety podczas wdrażania przy użyciu programu Visual Studio

Można nakazać programowi Visual Studio kompresowanie pakietów w ramach wdrożenia, dodając `CopyPackageParameters` element do profilu publikowania i ustawiając `CompressPackage` atrybut na `true` .

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Utwórz sfpkg

Począwszy od wersji 6,1, Service Fabric umożliwia inicjowanie obsługi administracyjnej ze sklepu zewnętrznego.
W przypadku tej opcji pakiet aplikacji nie musi być kopiowany do magazynu obrazów. Zamiast tego można utworzyć `sfpkg` i przekazać go do magazynu zewnętrznego, a następnie podać identyfikator URI pobierania, aby Service Fabric podczas aprowizacji. Ten sam pakiet można zainicjować w wielu klastrach. Inicjowanie obsługi administracyjnej w sklepie zewnętrznym pozwala zaoszczędzić czas wymagany do skopiowania pakietu do każdego klastra.

`sfpkg`Plik jest plikiem ZIP, który zawiera początkowy pakiet aplikacji i ma rozszerzenie ". sfpkg".
W pliku zip pakiet aplikacji może być skompresowany lub nieskompresowany. Kompresja pakietu aplikacji wewnątrz pliku zip odbywa się przy użyciu kodu, konfiguracji i poziomów pakietów danych, jak [wspomniano wcześniej](service-fabric-package-apps.md#compress-a-package).

Aby utworzyć `sfpkg` , należy rozpocząć od folderu zawierającego oryginalny pakiet aplikacji, skompresowany lub nie. Następnie użyj dowolnego narzędzia do ZIP folderu z rozszerzeniem ". sfpkg". Na przykład użyj [zipfile. CreateFromDirectory](/dotnet/api/system.io.compression.zipfile.createfromdirectory?view=netcore-3.1#System_IO_Compression_ZipFile_CreateFromDirectory_System_String_System_String_System_IO_Compression_CompressionLevel_System_Boolean_).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

`sfpkg`Należy przekazać do zewnętrznego magazynu poza pasmem, poza Service Fabric. Magazyn zewnętrzny może być dowolnym magazynem, który uwidacznia punkt końcowy HTTP lub https. Podczas aprowizacji Service Fabric wykonuje operację pobierania, aby pobrać `sfpkg` pakiet aplikacji, więc magazyn musi zezwalać na dostęp do odczytu pakietu.

Aby zainicjować obsługę administracyjną pakietu, Użyj zewnętrznej obsługi administracyjnej, który wymaga identyfikatora URI pobierania i informacji o typie aplikacji.

>[!NOTE]
> Inicjowanie obsługi oparte na ścieżce względnej magazynu obrazów nie obsługuje obecnie `sfpkg` plików. W związku z tym `sfpkg` nie należy kopiować do magazynu obrazów.

## <a name="next-steps"></a>Następne kroki

[Wdrażanie i usuwanie aplikacji][10] — opis sposobu użycia programu PowerShell do zarządzania wystąpieniami aplikacji

[Zarządzanie parametrami aplikacji dla wielu środowisk][11] opisuje sposób konfigurowania parametrów i zmiennych środowiskowych dla różnych wystąpień aplikacji.

[Konfigurowanie zasad zabezpieczeń dla aplikacji][12] zawiera opis sposobu uruchamiania usług w ramach zasad zabezpieczeń w celu ograniczenia dostępu.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
