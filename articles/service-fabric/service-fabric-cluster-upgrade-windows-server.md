---
title: Uaktualnianie wersji klastra autonomicznego
description: Uaktualnij kod Service Fabric platformy Azure z uruchomionym autonomicznym klastrem Service Fabric.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 5831fcffe718e4966c3444a29d35c5bbd55e5f57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842941"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Uaktualnij wersję Service Fabric działającą w klastrze 

W przypadku każdego nowoczesnego systemu możliwość uaktualnienia jest kluczem do długoterminowej sukcesu produktu. Klaster Service Fabric platformy Azure to zasób, którego jesteś członkiem. W tym artykule opisano sposób uaktualniania wersji Service Fabric uruchomionej w klastrze autonomicznym.

> [!NOTE]
> Upewnij się, że w klastrze jest zawsze uruchomiona obsługiwana wersja Service Fabric. Gdy firma Microsoft ogłasza wydanie nowej wersji Service Fabric, poprzednia wersja zostanie oznaczona do końca wsparcia po upływie co najmniej 60 dni od daty powiadomienia. Nowe wersje są ogłaszane [na blogu zespołu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). Nowa wersja jest dostępna do wyboru w tym momencie.
>
>

Klaster można uaktualnić do nowej wersji tylko wtedy, gdy jest używana konfiguracja węzła w stylu produkcyjnym, w którym każdy węzeł Service Fabric jest przypisywany na oddzielnej maszynie fizycznej lub wirtualnej. Jeśli masz klaster projektowy, w którym więcej niż jeden węzeł Service Fabric znajduje się na jednej maszynie fizycznej lub wirtualnej, należy ponownie utworzyć klaster przy użyciu nowej wersji.

Dwa różne przepływy pracy umożliwiają uaktualnienie klastra do najnowszej wersji lub obsługiwanej wersji Service Fabric. Jeden przepływ pracy jest przeznaczony dla klastrów, które mają łączność do automatycznego pobierania najnowszej wersji. Drugi przepływ pracy jest przeznaczony dla klastrów, które nie mają łączności do pobierania najnowszej wersji Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Włącz funkcję autouaktualnienia wersji Service Fabric klastra
Aby ustawić, że klaster ma pobierać aktualizacje Service Fabric w przypadku wydania przez firmę Microsoft nowej wersji, ustaw dla `fabricClusterAutoupgradeEnabled` konfiguracji klastra *wartość true*. Aby ręcznie wybrać obsługiwaną wersję Service Fabric, do której ma być włączony klaster, ustaw `fabricClusterAutoupgradeEnabled` dla konfiguracji klastra *wartość false*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Uaktualnij klastry z łącznością, aby pobrać najnowszy kod i konfigurację
Wykonaj te kroki, aby uaktualnić klaster do obsługiwanej wersji, jeśli węzły klastra mają łączność z Internetem z [Centrum pobierania Microsoft](https://download.microsoft.com).

W przypadku klastrów mających łączność z [Centrum pobierania Microsoft](https://download.microsoft.com)firma Microsoft okresowo sprawdza dostępność nowych wersji Service Fabric.

Gdy dostępna jest nowa wersja Service Fabric, pakiet zostanie pobrany lokalnie do klastra i zainicjowany na potrzeby uaktualnienia. Dodatkowo, aby poinformować klienta o nowej wersji, system pokazuje jawne ostrzeżenie kondycji klastra podobne do następujących:

"Bieżąca wersja klastra obsługa końcowa [Date]."

Gdy w klastrze działa Najnowsza wersja, ostrzeżenie zostanie odrzucone.

Gdy widzisz ostrzeżenie kondycji klastra, Uaktualnij klaster:

1. Połącz się z klastrem z dowolnego komputera, który ma dostęp administratora do wszystkich maszyn wymienionych jako węzły w klastrze. Maszyna, na której jest uruchamiany ten skrypt, nie musi być częścią klastra.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Pobierz listę wersji Service Fabric, do których można przeprowadzić uaktualnienie.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Powinno zostać wyświetlone dane wyjściowe podobne do tego:

    ![Pobierz Service Fabric wersje][getfabversions]
3. Rozpocznij Uaktualnianie klastra do dostępnej wersji za pomocą polecenia [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade) programu Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Aby monitorować postęp uaktualniania, można użyć Service Fabric Explorer lub uruchomić następujące polecenie programu PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest wycofywane. Aby określić niestandardowe zasady dotyczące kondycji dla polecenia Start-ServiceFabricClusterUpgrade, zapoznaj się z dokumentacją programu [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po rozwiązaniu problemów, które wystąpiły w wyniku wycofania, należy ponownie zainicjować uaktualnienie, wykonując te same czynności, które zostały opisane wcześniej.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Uaktualnij klastry, które *nie mają łączności* , aby pobrać najnowszy kod i konfigurację
Wykonaj te kroki, aby uaktualnić klaster do obsługiwanej wersji, jeśli węzły klastra nie mają łączności z Internetem z [Centrum pobierania Microsoft](https://download.microsoft.com).

> [!NOTE]
> Jeśli używasz klastra, który nie jest połączony z Internetem, musisz monitorować [Blog zespołu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) , aby poznać nowe wersje. System nie wyświetla ostrzeżenia kondycji klastra w celu powiadomienia o nowych wersjach.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Obsługa autoaprowizacji a Ręczne inicjowanie obsługi
Aby włączyć automatyczne pobieranie i rejestrację dla najnowszej wersji kodu, skonfiguruj usługę aktualizacji Service Fabric. Aby uzyskać instrukcje, zobacz *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* w [pakiecie autonomicznym](service-fabric-cluster-standalone-package-contents.md).

W przypadku procesu ręcznego postępuj zgodnie z tymi instrukcjami.

Zmodyfikuj konfigurację klastra, aby ustawić dla następującej właściwości *wartość false* przed rozpoczęciem uaktualniania konfiguracji:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Aby uzyskać szczegółowe informacje na temat użycia, zobacz polecenie programu PowerShell [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) . Przed rozpoczęciem uaktualniania konfiguracji należy zaktualizować element "clusterConfigurationVersion" w formacie JSON.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Przepływ pracy uaktualniania klastra

1. Uruchom [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) z jednego z węzłów w klastrze i zanotuj *TargetCodeVersion*.

2. Uruchom następujące dane z maszyny połączonej z Internetem, aby wyświetlić wszystkie wersje zgodne z uaktualnieniem z bieżącą wersją i pobrać odpowiedni pakiet ze skojarzonych linków pobierania:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Połącz się z klastrem z dowolnego komputera, który ma dostęp administratora do wszystkich maszyn wymienionych jako węzły w klastrze. Maszyna, na której jest uruchamiany ten skrypt, nie musi być częścią klastra.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Skopiuj pobrany pakiet do magazynu obrazów klastra.

5. Zarejestruj skopiowany pakiet.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Rozpocznij Uaktualnianie klastra do dostępnej wersji.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Możesz monitorować postęp uaktualniania na Service Fabric Explorer lub uruchomić następujące polecenie programu PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Jeśli zasady kondycji klastra nie są spełnione, uaktualnienie jest wycofywane. Aby określić niestandardowe zasady dotyczące kondycji dla polecenia Start-ServiceFabricClusterUpgrade, zapoznaj się z dokumentacją programu [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Po rozwiązaniu problemów, które wystąpiły w wyniku wycofania, należy ponownie zainicjować uaktualnienie, wykonując te same czynności, które zostały opisane wcześniej.

## <a name="next-steps"></a>Następne kroki
* [Uaktualnianie konfiguracji klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md)
* Dostosuj niektóre [Service Fabric ustawienia klastra](service-fabric-cluster-fabric-settings.md).
* [Skaluj klaster do i z zewnątrz](service-fabric-cluster-scale-in-out.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
