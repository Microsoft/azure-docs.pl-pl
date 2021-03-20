---
title: Pakiet autonomiczny platformy Azure Service Fabric dla systemu Windows Server
description: Opis i zawartość pakietu autonomicznego Service Fabric platformy Azure dla systemu Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: fb9a52510788a275bdeea67cd5c1fdd2e894738d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86261019"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Zawartość pakietu autonomicznego Service Fabric dla systemu Windows Server
W [pobraniu](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric pakiet autonomiczny zostaną odnalezione następujące pliki:

| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Skrypt programu PowerShell, który tworzy klaster przy użyciu ustawień w ClusterConfig.jsna. |
| RemoveServiceFabricCluster.ps1 |Skrypt programu PowerShell, który usuwa klaster przy użyciu ustawień w ClusterConfig.jsna. |
| AddNode.ps1 |Skrypt programu PowerShell służący do dodawania węzła do istniejącego wdrożonego klastra na bieżącym komputerze. |
| RemoveNode.ps1 |Skrypt programu PowerShell służący do usuwania węzła z istniejącego wdrożonego klastra z bieżącej maszyny. |
| CleanFabric.ps1 |Skrypt programu PowerShell służący do czyszczenia instalacji autonomicznej Service Fabric poza bieżącą maszyną. Poprzednie instalacje MSI należy usunąć przy użyciu własnych skojarzonych z nimi odinstalowywania. |
| TestConfiguration.ps1 |Skrypt programu PowerShell służący do analizowania infrastruktury, jak określono w Cluster.jsna. |
| DownloadServiceFabricRuntimePackage.ps1 |Skrypt programu PowerShell służący do pobierania najnowszego pakietu środowiska uruchomieniowego poza pasmem, w przypadku scenariuszy, w których maszyna wdrożenia nie jest połączona z Internetem. |
| DeploymentComponentsAutoextractor.exe |Samodzielne wyodrębnianie archiwum zawierającego składniki wdrożenia używane przez skrypty pakietu autonomicznego. |
| EULA_ENU.txt |Postanowienia licencyjne dotyczące korzystania z Microsoft Azure Service Fabric autonomicznego pakietu systemu Windows Server. [Kopię umowy EULA można pobrać](https://go.microsoft.com/fwlink/?LinkID=733084) teraz. |
| Readme.txt |Link do informacji o wersji i podstawowych instrukcji instalacji. Jest to podzestaw instrukcji przedstawionych w tym dokumencie. |
| ThirdPartyNotice. rtf |Powiadomienie dotyczące oprogramowania innych firm, które znajduje się w pakiecie. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe uruchamiana na żądanie w celu zbierania i przekazywania dzienników śledzenia do firmy Microsoft w celu uzyskania pomocy technicznej. |
| Tools\ServiceFabricUpdateService.zip |Narzędzie służące do włączania autouzupełniania kodu dla klastrów, które nie mają dostępu do Internetu. Więcej informacji można znaleźć [tutaj](service-fabric-cluster-upgrade-windows-server.md)|

**Szablony** 

| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.jsna |Przykładowy plik konfiguracji klastra, który zawiera ustawienia dla klastra projektowego niezabezpieczonego, z trzema węzłami (lub maszyn wirtualnych), w tym informacje dla każdego węzła w klastrze. |
| ClusterConfig.Unsecure.MultiMachine.jsna |Przykładowy plik konfiguracji klastra, który zawiera ustawienia niezabezpieczonego klastra wielomaszynowego (lub maszyny wirtualnej), w tym informacje dotyczące poszczególnych maszyn w klastrze. |
| ClusterConfig.Windows.DevCluster.jsna |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla klastra deweloperskiego z trzema węzłami (lub maszyną wirtualną), w tym informacje dotyczące każdego węzła w klastrze. Klaster jest zabezpieczony przy użyciu [tożsamości systemu Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)). |
| ClusterConfig.Windows.MultiMachine.jsna |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla bezpiecznego klastra wielomaszynowego (lub maszyny wirtualnej) korzystającego z zabezpieczeń systemu Windows, w tym informacje dotyczące poszczególnych maszyn znajdujących się w bezpiecznym klastrze. Klaster jest zabezpieczony przy użyciu [tożsamości systemu Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)). |
| ClusterConfig.x509.DevCluster.jsna |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla klastra deweloperskiego z trzema węzłami (lub maszyną wirtualną), w tym informacje dotyczące każdego węzła w klastrze. Klaster jest zabezpieczony przy użyciu certyfikatów x509. |
| ClusterConfig.x509.MultiMachine.jsna |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla klastra bezpiecznego, wielomaszynowego (lub maszyny wirtualnej), w tym informacje o każdym węźle w zabezpieczonym klastrze. Klaster jest zabezpieczony przy użyciu certyfikatów x509. |
| ClusterConfig.gMSA.Windows.MultiMachine.jsna |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla klastra bezpiecznego, wielomaszynowego (lub maszyny wirtualnej), w tym informacje o każdym węźle w zabezpieczonym klastrze. Klaster jest zabezpieczony przy użyciu [kont usług zarządzanych przez grupę](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)). |

## <a name="cluster-configuration-samples"></a>Przykłady konfiguracji klastra
Najnowsze wersje szablonów konfiguracji klastra można znaleźć na stronie usługi GitHub: [przykłady konfiguracji klastra samodzielnego](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Niezależny pakiet środowiska uruchomieniowego
Najnowszy pakiet środowiska uruchomieniowego jest automatycznie pobierany podczas wdrażania klastra z [linku pobierania — Service Fabric środowiska uruchomieniowego — Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Powiązane
* [Tworzenie autonomicznego klastra usługi Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Scenariusze zabezpieczeń klastra Service Fabric](service-fabric-windows-cluster-windows-security.md)
