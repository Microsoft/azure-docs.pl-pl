---
title: Service Fabric uaktualniania aplikacji przy użyciu programu PowerShell
description: W tym artykule omówiono środowisko wdrażania aplikacji Service Fabric, zmiany kodu i wdrażania uaktualnienia przy użyciu programu PowerShell.
ms.topic: conceptual
ms.date: 8/5/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b038f4a41ee02960a02c4445b65eb2360a75761
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792035"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Service Fabric uaktualniania aplikacji przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Program PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Najczęściej używane i zalecane podejście do uaktualniania to monitorowane uaktualnienie stopniowe.  Usługa Azure Service Fabric monitoruje kondycję uaktualnianej aplikacji na podstawie zestawu zasad dotyczących kondycji. Po uaktualnieniu domeny aktualizacji (UD) program Service Fabric szacuje kondycję aplikacji, a następnie przechodzi do następnej domeny aktualizacji lub nie przejdzie do uaktualnienia w zależności od zasad dotyczących kondycji.

Uaktualnienie monitorowanej aplikacji można wykonać przy użyciu zarządzanych lub natywnych interfejsów API, programu PowerShell, interfejsu wiersza polecenia platformy Azure, środowiska Java lub REST. Aby uzyskać instrukcje dotyczące przeprowadzania uaktualnienia przy użyciu programu Visual Studio, zobacz [Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md).

Przy Service Fabric monitorowanych uaktualnień stopniowych administrator aplikacji może skonfigurować zasady oceny kondycji, których Service Fabric używa do określenia, czy aplikacja jest w dobrej kondycji. Ponadto administrator może skonfigurować akcję, która ma zostać podjęta w przypadku niepowodzenia oceny kondycji (na przykład podczas automatycznego wycofywania). W tej sekcji przedstawiono monitorowane uaktualnienie dla jednego z przykładów zestawu SDK korzystającego z programu PowerShell. 

> [!NOTE]
> [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s nie są zachowywane podczas uaktualniania aplikacji. Aby zachować bieżące parametry aplikacji, użytkownik powinien najpierw pobrać parametry i przekazać je do wywołania interfejsu API uaktualniania, takiego jak poniżej:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Krok 1. Kompilowanie i wdrażanie przykładu obiektów wizualnych
Skompiluj i Opublikuj aplikację, klikając prawym przyciskiem myszy projekt aplikacji, **VisualObjectsApplication** i wybierając polecenie **Publikuj** .  Aby uzyskać więcej informacji, zobacz [Samouczek dotyczący uaktualniania aplikacji Service Fabric](service-fabric-application-upgrade-tutorial.md).  Alternatywnie możesz użyć programu PowerShell do wdrożenia aplikacji.

> [!NOTE]
> Przed użyciem dowolnego polecenia Service Fabric w programie PowerShell należy najpierw nawiązać połączenie z klastrem przy użyciu `Connect-ServiceFabricCluster` polecenia cmdlet. Podobnie zakłada się, że klaster został już skonfigurowany na komputerze lokalnym. Zapoznaj się z artykułem dotyczącym [konfigurowania Service Fabric środowiska deweloperskiego](service-fabric-get-started.md).
> 
> 

Po skompilowaniu projektu w programie Visual Studio można użyć polecenia programu PowerShell [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) , aby skopiować pakiet aplikacji do magazynu ImageStore. Jeśli chcesz zweryfikować pakiet aplikacji lokalnie, użyj polecenia cmdlet [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) . Następnym krokiem jest zarejestrowanie aplikacji w środowisku uruchomieniowym Service Fabric przy użyciu polecenia cmdlet [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) . Poniższy krok polega na uruchomieniu wystąpienia aplikacji za pomocą polecenia cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication) .  Te trzy kroki są analogiczne do użycia elementu menu **Wdróż** w programie Visual Studio.  Po zakończeniu aprowizacji należy wyczyścić skopiowany pakiet aplikacji z magazynu obrazów, aby zmniejszyć ilość używanych zasobów.  Jeśli typ aplikacji nie jest już wymagany, powinien zostać wyrejestrowany z tego samego powodu. Aby uzyskać więcej informacji [, zobacz Wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) .

Teraz można użyć Service Fabric Explorer, [Aby wyświetlić klaster i aplikację](service-fabric-visualizing-your-cluster.md). Aplikacja ma usługę sieci Web, do której można przejść w programie Internet Explorer, wpisując `http://localhost:8081/visualobjects` na pasku adresu.  Na ekranie powinny być widoczne przepływające obiekty wizualne.  Ponadto można użyć polecenie [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) , aby sprawdzić stan aplikacji.

## <a name="step-2-update-the-visual-objects-sample"></a>Krok 2. Aktualizacja przykładu obiektów wizualnych
Można zauważyć, że w wersji, która została wdrożona w kroku 1, obiekty wizualne nie są obracane. Uaktualnimy tę aplikację do jednego miejsca, w którym obiekty wizualne również są obracane.

Wybierz projekt VisualObjects. ActorService w ramach rozwiązania VisualObjects i Otwórz plik StatefulVisualObjectActor. cs. W tym pliku przejdź do metody `MoveObject` , Skomentuj `this.State.Move()` i Usuń komentarz `this.State.Move(true)` . Ta zmiana powoduje obrócenie obiektów po uaktualnieniu usługi.

Należy również zaktualizować plik *ServiceManifest.xml* (w obszarze PackageRoot) projektu **VisualObjects. ActorService**. Zaktualizuj *CodePackage* i wersję usługi do 2,0 oraz odpowiadające im wiersze w pliku *ServiceManifest.xml* .
Możesz użyć opcji *Edytuj pliki manifestu* programu Visual Studio po kliknięciu prawym przyciskiem myszy rozwiązania, aby zmienić plik manifestu.

Po wprowadzeniu zmian manifest powinien wyglądać podobnie do następującego (wyróżnione fragmenty pokazują zmiany):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Teraz plik *ApplicationManifest.xml* (znaleziony w ramach projektu **VisualObjects** w ramach rozwiązania **VisualObjects** ) został zaktualizowany do wersji 2,0 projektu **VisualObjects. ActorService** . Ponadto wersja aplikacji jest aktualizowana pod kątem 2.0.0.0 z 1.0.0.0. *ApplicationManifest.xml* powinien wyglądać podobnie do następującego fragmentu kodu:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Teraz Skompiluj projekt, wybierając tylko projekt **ActorService** , a następnie klikając prawym przyciskiem myszy i wybierając opcję **kompilacji** w programie Visual Studio. W przypadku wybrania opcji **Kompiluj ponownie wszystkie** należy zaktualizować wersje dla wszystkich projektów, ponieważ kod zostałby zmieniony. Następnie Spakuj zaktualizowaną aplikację, klikając prawym przyciskiem myszy **_VisualObjectsApplication_*_, wybierając menu Service Fabric i wybierając _* pakiet**. Ta akcja powoduje utworzenie pakietu aplikacji, który można wdrożyć.  Zaktualizowana aplikacja jest gotowa do wdrożenia.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Krok 3. podjęcie decyzji dotyczącej zasad dotyczących kondycji i parametrów uaktualnienia
Zapoznaj się z [parametrami uaktualniania aplikacji](service-fabric-application-upgrade-parameters.md) i [procesem uaktualniania](service-fabric-application-upgrade.md) , aby uzyskać dobrą wiedzę na temat różnych parametrów uaktualniania, przekroczeń czasu i stosowanego kryterium kondycji. W tym instruktażu kryterium oceny kondycji usługi jest ustawione na wartość domyślną (i zalecaną), co oznacza, że wszystkie usługi i wystąpienia powinny być w *dobrej kondycji* po uaktualnieniu.  

Należy jednak zwiększyć *HealthCheckStableDuration* do 180 sekund (aby zapewnić kondycję usług przez co najmniej 120 sekund, zanim uaktualnienie przejdzie do następnej domeny aktualizacji).  Można również ustawić *UpgradeDomainTimeout* na 1200 sekund i *UpgradeTimeout* na 3000 sekund.

Na koniec Przypuśćmy również ustawienie *UpgradeFailureAction* do wycofania. Ta opcja wymaga Service Fabric wycofania aplikacji do poprzedniej wersji, jeśli wystąpią problemy podczas uaktualniania. W tym celu podczas uruchamiania uaktualnienia (w kroku 4) są określone następujące parametry:

FailureAction = wycofywanie

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Krok 4. Przygotowywanie aplikacji do uaktualnienia
Teraz aplikacja została skompilowana i gotowa do uaktualnienia. Jeśli otworzysz okno programu PowerShell jako administrator i wpisz polecenie [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication), powinieneś poinformować, że jest to typ aplikacji 1.0.0.0 of **VisualObjects** , który został wdrożony.  

Pakiet aplikacji jest przechowywany w następującej ścieżce względnej, w której został odkompresowany Service Fabric SDK- *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Folder "Package" powinien znajdować się w tym katalogu, w którym jest przechowywany pakiet aplikacji. Sprawdź sygnatury czasowe, aby upewnić się, że jest to najnowsza kompilacja (może zajść potrzeba zmodyfikowania odpowiednich ścieżek).

Teraz Skopiuj zaktualizowany pakiet aplikacji do Service Fabric magazynu ImageStore (gdzie pakiety aplikacji są przechowywane przez Service Fabric). Parametr *ApplicationPackagePathInImageStore* Service Fabric informuje, gdzie można znaleźć pakiet aplikacji. Zaktualizowana aplikacja została umieszczona w "VisualObjects \_ v2" przy użyciu następującego polecenia (konieczne może być odpowiednie zmodyfikowanie ścieżek).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Następnym krokiem jest zarejestrowanie tej aplikacji za pomocą Service Fabric, które można wykonać za pomocą polecenia [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) :

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Jeśli poprzednie polecenie nie powiedzie się, prawdopodobnie trzeba będzie ponownie skompilować wszystkie usługi. Jak wspomniano w kroku 2, konieczne może być również zaktualizowanie wersji usługi WebService.

Zaleca się usunięcie pakietu aplikacji po pomyślnym zarejestrowaniu aplikacji.  Usuwanie pakietów aplikacji z magazynu obrazów zwalnia zasoby systemowe.  Utrzymywanie nieużywanych pakietów aplikacji zużywa magazyn dyskowy i prowadzi do problemów z wydajnością aplikacji.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Krok 5. uruchomienie uaktualniania aplikacji
Teraz wszystko jest ustawione, aby rozpocząć uaktualnianie aplikacji za pomocą polecenia [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Nazwa aplikacji jest taka sama, jak została opisana w pliku *ApplicationManifest.xml* . Service Fabric używa tej nazwy do identyfikowania aplikacji, która ma zostać uaktualniona. Jeśli limit czasu jest za krótki, może wystąpić komunikat o błędzie informujący o problemie. Zapoznaj się z sekcją Rozwiązywanie problemów lub Zwiększ limit czasu.

Teraz, gdy Uaktualnianie aplikacji jest realizowane, można monitorować je za pomocą Service Fabric Explorer lub przy użyciu polecenia programu PowerShell [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) : 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

W ciągu kilku minut stan uzyskany za pomocą powyższego polecenia programu PowerShell powinien określać, że wszystkie domeny aktualizacji zostały uaktualnione (ukończono). Należy się dowiedzieć, że obiekty wizualne w oknie przeglądarki zostały rozpoczęte.

Możesz spróbować uaktualnić wersję 2 do wersji 3 lub z wersji 2 do wersji 1 jako ćwiczenie. Przejście z wersji 2 do wersji 1 jest również uznawane za uaktualnienie. Twórz aplikacje, korzystając z limitów czasu i zasad dotyczących kondycji, aby je znać. W przypadku wdrażania w klastrze platformy Azure parametry muszą być odpowiednio skonfigurowane. Dobrym sposobem jest ustawienie limitów czasu.

## <a name="next-steps"></a>Następne kroki
[Uaktualnianie aplikacji przy użyciu programu Visual Studio](service-fabric-application-upgrade-tutorial.md) przeprowadzi Cię przez proces uaktualniania aplikacji przy użyciu programu Visual Studio.

Kontroluj sposób uaktualniania aplikacji przy użyciu [parametrów uaktualnienia](service-fabric-application-upgrade-parameters.md).

Aby uaktualnić aplikacje, należy się upewnić, jak używać [serializacji danych](service-fabric-application-upgrade-data-serialization.md).

Dowiedz się, jak korzystać z zaawansowanych funkcji podczas uaktualniania aplikacji, odwołując się do [zaawansowanych tematów](service-fabric-application-upgrade-advanced.md).

Rozwiązywanie typowych problemów dotyczących uaktualnień aplikacji, w odniesieniu do kroków w [temacie Troubleshooting Upgrades Applications](service-fabric-application-upgrade-troubleshooting.md).
