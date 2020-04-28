---
title: Pakiet autonomiczny platformy Azure Service Fabric dla systemu Windows Server
description: Opis i zawartość pakietu autonomicznego Service Fabric platformy Azure dla systemu Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451846"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Zawartość pakietu autonomicznego Service Fabric dla systemu Windows Server
W [pobraniu](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric pakiet autonomiczny zostaną odnalezione następujące pliki:

| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| CreateServiceFabricCluster. ps1 |Skrypt programu PowerShell, który tworzy klaster przy użyciu ustawień w ClusterConfig. JSON. |
| RemoveServiceFabricCluster. ps1 |Skrypt programu PowerShell, który usuwa klaster przy użyciu ustawień w ClusterConfig. JSON. |
| AddNode. ps1 |Skrypt programu PowerShell służący do dodawania węzła do istniejącego wdrożonego klastra na bieżącym komputerze. |
| RemoveNode. ps1 |Skrypt programu PowerShell służący do usuwania węzła z istniejącego wdrożonego klastra z bieżącej maszyny. |
| CleanFabric. ps1 |Skrypt programu PowerShell służący do czyszczenia instalacji autonomicznej Service Fabric poza bieżącą maszyną. Poprzednie instalacje MSI należy usunąć przy użyciu własnych skojarzonych z nimi odinstalowywania. |
| TestConfiguration. ps1 |Skrypt programu PowerShell służący do analizowania infrastruktury, jak określono w pliku Cluster. JSON. |
| DownloadServiceFabricRuntimePackage. ps1 |Skrypt programu PowerShell służący do pobierania najnowszego pakietu środowiska uruchomieniowego poza pasmem, w przypadku scenariuszy, w których maszyna wdrożenia nie jest połączona z Internetem. |
| DeploymentComponentsAutoextractor. exe |Samodzielne wyodrębnianie archiwum zawierającego składniki wdrożenia używane przez skrypty pakietu autonomicznego. |
| EULA_ENU. txt |Postanowienia licencyjne dotyczące korzystania z Microsoft Azure Service Fabric autonomicznego pakietu systemu Windows Server. [Kopię umowy EULA można pobrać](https://go.microsoft.com/fwlink/?LinkID=733084) teraz. |
| Plik Readme. txt |Link do informacji o wersji i podstawowych instrukcji instalacji. Jest to podzestaw instrukcji przedstawionych w tym dokumencie. |
| ThirdPartyNotice. rtf |Powiadomienie dotyczące oprogramowania innych firm, które znajduje się w pakiecie. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector. exe, który jest uruchamiany na żądanie w celu zbierania i przekazywania dzienników śledzenia do firmy Microsoft w celu uzyskania pomocy technicznej. |
| Tools\ServiceFabricUpdateService.zip |Narzędzie służące do włączania autouzupełniania kodu dla klastrów, które nie mają dostępu do Internetu. Więcej szczegółów można znaleźć [tutaj](service-fabric-cluster-upgrade-windows-server.md)|

**Przystawki** 

| **Nazwa pliku** | **Krótki opis** |
| --- | --- |
| ClusterConfig. Unsecure. DevCluster. JSON |Przykładowy plik konfiguracji klastra, który zawiera ustawienia dla klastra projektowego niezabezpieczonego, z trzema węzłami (lub maszyn wirtualnych), w tym informacje dla każdego węzła w klastrze. |
| ClusterConfig. Unsecure. xmlmachine. JSON |Przykładowy plik konfiguracji klastra, który zawiera ustawienia niezabezpieczonego klastra wielomaszynowego (lub maszyny wirtualnej), w tym informacje dotyczące poszczególnych maszyn w klastrze. |
| ClusterConfig. Windows. DevCluster. JSON |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla klastra deweloperskiego z trzema węzłami (lub maszyną wirtualną), w tym informacje dotyczące każdego węzła w klastrze. Klaster jest zabezpieczony przy użyciu [tożsamości systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig. Windows. wielomachine. JSON |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla bezpiecznego klastra wielomaszynowego (lub maszyny wirtualnej) korzystającego z zabezpieczeń systemu Windows, w tym informacje dotyczące poszczególnych maszyn znajdujących się w bezpiecznym klastrze. Klaster jest zabezpieczony przy użyciu [tożsamości systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig. x509. DevCluster. JSON |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla klastra deweloperskiego z trzema węzłami (lub maszyną wirtualną), w tym informacje dotyczące każdego węzła w klastrze. Klaster jest zabezpieczony przy użyciu certyfikatów x509. |
| ClusterConfig. x509. wielomachine. JSON |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla klastra bezpiecznego, wielomaszynowego (lub maszyny wirtualnej), w tym informacje o każdym węźle w zabezpieczonym klastrze. Klaster jest zabezpieczony przy użyciu certyfikatów x509. |
| ClusterConfig. gMSA. Windows. wielomachine. JSON |Przykładowy plik konfiguracji klastra, który zawiera wszystkie ustawienia dla klastra bezpiecznego, wielomaszynowego (lub maszyny wirtualnej), w tym informacje o każdym węźle w zabezpieczonym klastrze. Klaster jest zabezpieczony przy użyciu [kont usług zarządzanych przez grupę](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Przykłady konfiguracji klastra
Najnowsze wersje szablonów konfiguracji klastra można znaleźć na stronie usługi GitHub: [przykłady konfiguracji klastra samodzielnego](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Niezależny pakiet środowiska uruchomieniowego
Najnowszy pakiet środowiska uruchomieniowego jest automatycznie pobierany podczas wdrażania klastra z [linku pobierania — Service Fabric środowiska uruchomieniowego — Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Powiązane
* [Tworzenie autonomicznego klastra usługi Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Scenariusze zabezpieczeń klastra Service Fabric](service-fabric-windows-cluster-windows-security.md)
