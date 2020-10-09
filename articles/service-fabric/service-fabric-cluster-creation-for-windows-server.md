---
title: Tworzenie autonomicznego klastra usługi Azure Service Fabric
description: Utwórz klaster Service Fabric platformy Azure na dowolnym komputerze (fizycznym lub wirtualnym) z systemem Windows Server, niezależnie od tego, czy jest on lokalny, czy w dowolnej chmurze.
ms.topic: conceptual
ms.date: 2/21/2019
ms.openlocfilehash: 36883f2c8b09fa3f8f013e0267dafa2a8220e5d2
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843196"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Tworzenie klastra autonomicznego działającego w systemie Windows Server
Za pomocą usługi Azure Service Fabric można tworzyć klastry Service Fabric na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server. Oznacza to, że można wdrażać i uruchamiać aplikacje Service Fabric w dowolnym środowisku zawierającym zestaw połączonych komputerów z systemem Windows Server, być lokalnym lub dowolnym dostawcą chmury. Service Fabric udostępnia pakiet instalacyjny do tworzenia klastrów Service Fabric o nazwie autonomiczny pakiet systemu Windows Server. Tradycyjne Service Fabric klastrów na platformie Azure są dostępne jako usługa zarządzana, natomiast autonomiczne klastry Service Fabric są samoobsługowe. Aby uzyskać więcej informacji na temat różnic, zobacz [porównywanie klastrów na platformie Azure i autonomicznych Service Fabric](./service-fabric-deploy-anywhere.md).

W tym artykule omówiono procedurę tworzenia klastra autonomicznego Service Fabric.

> [!NOTE]
> Ten autonomiczny pakiet systemu Windows Server jest dostępny komercyjnie bez opłat i może być używany do wdrożeń produkcyjnych. Ten pakiet może zawierać nowe funkcje Service Fabric, które znajdują się w wersji zapoznawczej. Przewiń w dół do "[funkcji wersji zapoznawczej zawartej w tym pakiecie](#previewfeatures_anchor)". Lista funkcji w wersji zapoznawczej. [Kopię umowy EULA można pobrać](https://go.microsoft.com/fwlink/?LinkID=733084) teraz.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Uzyskaj pomoc techniczną dotyczącą pakietu Service Fabric dla systemu Windows Server
* Zapoznaj się ze społecznością Service Fabric pakiet autonomiczny dla systemu Windows Server na [stronie pytań i odpowiedzi Microsoft&pytań i odpowiedzi na platformie Azure Service Fabric](/answers/topics/azure-service-fabric.html).
* Otwórz bilet dla [profesjonalnej pomocy technicznej dla Service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Dowiedz się więcej na temat profesjonalnej pomocy technicznej firmy Microsoft [tutaj](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Możesz również uzyskać pomoc techniczną dotyczącą tego pakietu w ramach [programu Microsoft Pomoc techniczna Premier](https://support.microsoft.com/en-us/premier).
* Aby uzyskać więcej informacji, zobacz [Opcje pomocy technicznej platformy Azure Service Fabric](./service-fabric-support.md).
* Aby zbierać dzienniki do celów pomocy technicznej, uruchom [Service Fabric autonomiczny moduł zbierający dzienniki](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Pobieranie pakietu usługi Service Fabric dla systemu Windows Server
Aby utworzyć klaster, należy użyć pakietu Service Fabric dla systemu Windows Server (Windows Server 2012 R2 i nowszego) znalezionego tutaj: <br>
[Link do pobierania — Service Fabric autonomiczny pakiet — Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Znajdź [tutaj](service-fabric-cluster-standalone-package-contents.md)szczegółowe informacje o zawartości pakietu.

Pakiet środowiska uruchomieniowego Service Fabric jest pobierany automatycznie podczas tworzenia klastra. Jeśli wdrożenie z komputera nie jest połączone z Internetem, Pobierz pakiet środowiska uruchomieniowego poza pasmem z tego miejsca: <br>
[Link do pobierania — środowisko uruchomieniowe Service Fabric — system Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Znajdź przykłady konfiguracji klastra autonomicznego w: <br>
[Przykłady konfiguracji klastra autonomicznego](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Tworzenie klastra
W ramach pakietu instalacyjnego jest instalowanych kilka przykładowych plików konfiguracji klastra. *ClusterConfig.Unsecure.DevCluster.json* to najprostsza konfiguracja klastra: niezabezpieczony klaster z trzema węzłami działającymi na pojedynczym komputerze.  Inne pliki konfiguracji zawierają opis klastrów dla jednej lub wielu maszyn, zabezpieczonych za pomocą certyfikatów X.509 lub zabezpieczeń systemu Windows.  Nie musisz modyfikować żadnych domyślnych ustawień konfiguracji w tym samouczku, ale przyjrzyj się plikowi konfiguracji i zapoznaj się z ustawieniami.  Sekcja **węzły** opisuje trzy węzły w klastrze: nazwę, adres IP, [typ węzła, domenę błędów i domenę uaktualnień](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  Sekcja **właściwości** definiuje [zabezpieczenia, poziom niezawodności, zbieranie diagnostyki i typy węzłów](service-fabric-cluster-manifest.md#cluster-properties) klastra.

Klaster utworzony w tym artykule jest niezabezpieczony.  Każda osoba może połączyć się z nim anonimowo i przeprowadzić operacje związane z zarządzaniem. Dlatego też klastry produkcyjne należy zawsze zabezpieczać przy użyciu certyfikatów X.509 lub zabezpieczeń systemu Windows.  Zabezpieczenia są konfigurowane tylko w momencie tworzenia klastra. Nie można włączyć zabezpieczeń później, gdy klaster jest już utworzony. Zaktualizuj plik konfiguracji, Włącz [zabezpieczenia certyfikatu](service-fabric-windows-cluster-x509-security.md) lub [zabezpieczenia systemu Windows](service-fabric-windows-cluster-windows-security.md). Dalsze informacje na temat zabezpieczeń klastra usługi Service Fabric można znaleźć w temacie [Secure a cluster](service-fabric-cluster-security.md) (Zabezpieczanie klastra).

### <a name="step-1-create-the-cluster"></a>Krok 1. Tworzenie klastra

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scenariusz A: Tworzenie niezabezpieczonego lokalnego klastra projektowego
Service Fabric można wdrożyć w klastrze programistycznym z jednym maszyną przy użyciu *ClusterConfig.Unsecure.DevCluster.jsna* pliku uwzględnionym w [przykładach](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Rozpakuj pakiet autonomiczny na maszynę, skopiuj przykładowy plik konfiguracji na komputer lokalny, a następnie uruchom skrypt *CreateServiceFabricCluster.ps1* za pośrednictwem sesji programu PowerShell administratora, z folderu pakietu autonomicznego.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Aby uzyskać szczegółowe informacje dotyczące rozwiązywania problemów, zobacz sekcję Konfigurowanie środowiska w temacie [Planowanie i przygotowywanie wdrożenia klastra](service-fabric-cluster-standalone-deployment-preparation.md) .

Jeśli skończysz wykonywanie scenariuszy programistycznych, możesz usunąć klaster Service Fabric z komputera, odwołując się do kroków w sekcji "[usuwanie klastra](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scenariusz B: Tworzenie klastra obejmującego wiele maszyn
Po wykonaniu kroków związanych z planowaniem i przygotowaniem w ramach [planu i przygotowania wdrożenia klastra](service-fabric-cluster-standalone-deployment-preparation.md)można utworzyć klaster produkcyjny przy użyciu pliku konfiguracji klastra.

Administrator klastra wdrażający i konfigurujący klaster musi mieć uprawnienia administratora na komputerze. Usługi Service Fabric nie można zainstalować na kontrolerze domeny.

1. Skrypt *TestConfiguration.ps1* w pakiecie autonomicznym jest używany jako analizator najlepszych rozwiązań, który weryfikuje, czy można wdrożyć klaster w danym środowisku. Listę wymagań wstępnych i wymagań dotyczących środowiska można znaleźć w temacie [Deployment preparation](service-fabric-cluster-standalone-deployment-preparation.md) (Przygotowywanie środowiska). Uruchom skrypt, aby zweryfikować, czy można utworzyć klaster programowania:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Powinny pojawić się dane wyjściowe podobne do poniższych. Jeśli ostatnie pole "Passed" jest zwracane jako "true", testy Sanity zostały zakończone, a klaster zostanie wdrożony na podstawie konfiguracji wejściowej.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Utwórz klaster: Uruchom skrypt *CreateServiceFabricCluster.ps1* , aby wdrożyć klaster Service Fabric na każdym komputerze w konfiguracji. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Ślady wdrożenia są zapisywane na maszynie lub maszynie wirtualnej, na której uruchomiono skrypt programu PowerShell CreateServiceFabricCluster.ps1. Znajdują się w podfolderze DeploymentTraces w katalogu, w którym uruchomiono skrypt. Aby sprawdzić, czy usługa Service Fabric została prawidłowo wdrożona na maszynie, poszukaj zainstalowanych plików w katalogu FabricDataRoot, zgodnie z sekcją FabricSettings w pliku konfiguracji klastra (domyślnie c:\ProgramData\SF). Ponadto w Menedżerze zadań możesz zobaczyć uruchomione procesy FabricHost.exe i Fabric.exe.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scenariusz C: Tworzenie klastra w trybie offline (odłączony internetowo)
Pakiet środowiska uruchomieniowego Service Fabric jest pobierany automatycznie podczas tworzenia klastra. W przypadku wdrażania klastra na maszynach, które nie są połączone z Internetem, należy pobrać pakiet środowiska uruchomieniowego Service Fabric oddzielnie i podać ścieżkę do niej podczas tworzenia klastra.
Pakiet środowiska uruchomieniowego można pobrać oddzielnie, z innej maszyny połączonej z Internetem, przy użyciu [linku pobierania — Service Fabric środowiska uruchomieniowego — Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Skopiuj pakiet środowiska uruchomieniowego do lokalizacji, w której jest wdrażany klaster trybu offline, i Utwórz klaster, uruchamiając `CreateServiceFabricCluster.ps1` z `-FabricRuntimePackagePath` dołączonym parametrem, jak pokazano w tym przykładzie: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* i *.\MicrosoftAzureServiceFabric.cab* są ścieżkami do konfiguracji klastra i pliku Runtime. cab odpowiednio.

### <a name="step-2-connect-to-the-cluster"></a>Krok 2. Nawiązywanie połączenia z klastrem
Połącz się z klastrem, aby sprawdzić, czy klaster działa i jest dostępny. Moduł ServiceFabric programu PowerShell został zainstalowany w środowisku uruchomieniowym.  Można nawiązać połączenie z klastrem z jednego z węzłów klastra lub z komputera zdalnego przy użyciu środowiska uruchomieniowego Service Fabric.  Polecenie cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) umożliwia ustanowienie połączenia z klastrem.

Aby nawiązać połączenie z niezabezpieczonym klastrem, uruchom następujące polecenie programu PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Na przykład:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.234:19000
```

Inne przykłady łączenia z klastrem można znaleźć w temacie [Nawiązywanie połączenia z zabezpieczonym klastrem](service-fabric-connect-to-secure-cluster.md). Po nawiązaniu połączenia z klastrem użyj polecenia cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps), aby wyświetlić listę węzłów w klastrze oraz informacje o stanie każdego węzła. Element **HealthState** powinien mieć wartość *OK* dla każdego węzła.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Krok 3. wizualizowanie klastra przy użyciu Eksploratora Service Fabric
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) to odpowiednie narzędzie do wizualizowania klastra i zarządzania aplikacjami.  Service Fabric Explorer to usługa uruchamiana w klastrze, do której można uzyskać dostęp za pomocą przeglądarki, przechodząc do programu `http://localhost:19080/Explorer` .

Pulpit nawigacyjny klastra zawiera omówienie klastra, w tym podsumowanie kondycji węzła i aplikacji. Widok węzła przedstawia fizyczny układ klastra. Dla danego węzła można sprawdzić, które aplikacje mają kod wdrożony w tym węźle.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Dodawanie i usuwanie węzłów
Możesz dodawać i usuwać węzły w klastrze autonomicznym usługi Service Fabric stosownie do zmieniających się potrzeb biznesowych. Aby uzyskać szczegółowe instrukcje, zobacz [Add or Remove nodes to a Service Fabric standalone cluster (Dodawanie i usuwanie węzłów w klastrze autonomicznym usługi Service Fabric)](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Usuwanie klastra
Aby usunąć klaster, uruchom skrypt *RemoveServiceFabricCluster.ps1* programu PowerShell z folderu pakietu i przekaż ścieżkę do pliku konfiguracji JSON. Możesz opcjonalne określić lokalizację dziennika usuwania.

Ten skrypt można uruchomić na dowolnym komputerze, który ma dostęp administratora do wszystkich maszyn wymienionych jako węzły w pliku konfiguracji klastra. Maszyna, na której jest uruchomiony ten skrypt, nie musi być częścią klastra.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Zebrane dane telemetryczne i sposoby ich wycofania
Domyślnie produkt zbiera dane telemetryczne dotyczące użycia Service Fabric, aby ulepszyć produkt. Analizator najlepszych rozwiązań, który jest uruchamiany w ramach instalacji, sprawdza połączenia z programem [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) . Jeśli ta opcja jest nieosiągalna, instalacja kończy się niepowodzeniem, chyba że zrezygnujesz z używania telemetrii.

1. Potok telemetrii próbuje przekazać następujące dane do [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) każdego dnia. Jest to najlepszy przekazanie i nie ma wpływu na funkcjonalność klastra. Dane telemetryczne są wysyłane tylko z węzła, na którym jest uruchomiony Menedżer trybu failover. Żadne inne węzły nie wysyłają danych telemetrycznych.
2. Dane telemetryczne składają się z następujących elementów:

* Liczba usług
* Liczba typów ServiceType
* Liczba aplikacji
* Liczba ApplicationUpgrades
* Liczba jednostek failoverunits
* Liczba InBuildFailoverUnits
* Liczba UnhealthyFailoverUnits
* Liczba replik
* Liczba InBuildReplicas
* Liczba StandByReplicas
* Liczba OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Liczba węzłów
* IsContextComplete: PRAWDA/FAŁSZ
* ClusterId: jest to identyfikator GUID generowany losowo dla każdego klastra
* ServiceFabricVersion
* Adres IP maszyny wirtualnej lub maszyny, z której zostało przekazane dane telemetryczne

Aby wyłączyć telemetrię, Dodaj następujące elementy do *Właściwości* w konfiguracji klastra: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funkcje w wersji zapoznawczej zawarte w tym pakiecie
Brak.


> [!NOTE]
> Począwszy od nowej [wersji platformy autonomicznej klastra dla systemu Windows Server (wersja 5.3.204. x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), można uaktualnić klaster do przyszłych wersji ręcznie lub automatycznie. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [uaktualnianie autonomicznego dokumentu wersji klastra Service Fabric](service-fabric-cluster-upgrade-windows-server.md) .
> 
> 

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md)
* [Ustawienia konfiguracji autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md)
* [Dodawanie lub usuwanie węzłów w autonomicznym klastrze Service Fabric](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Uaktualnianie autonomicznej wersji klastra Service Fabric](service-fabric-cluster-upgrade-windows-server.md)
* [Tworzenie autonomicznego klastra Service Fabric z maszynami wirtualnymi platformy Azure z systemem Windows](./service-fabric-cluster-creation-via-arm.md)
* [Zabezpieczanie klastra autonomicznego w systemie Windows przy użyciu zabezpieczeń systemu Windows](service-fabric-windows-cluster-windows-security.md)
* [Zabezpieczanie klastra autonomicznego w systemie Windows przy użyciu certyfikatów x509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
