---
title: Przygotowanie wdrożenia klastra autonomicznego
description: Dokumentacja dotycząca przygotowania środowiska i tworzenia konfiguracji klastra do uwzględnienia przed wdrożeniem klastra przeznaczonego do obsługi obciążeń produkcyjnych.
ms.topic: conceptual
ms.date: 9/11/2018
ms.openlocfilehash: 9e5ad37d803b2042fd57b0a325570e69d7b73038
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842958"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planowanie i przygotowywanie Service Fabric wdrożenia klastra autonomicznego

<a id="preparemachines"></a>Przed utworzeniem klastra wykonaj poniższe czynności.

## <a name="plan-your-cluster-infrastructure"></a>Planowanie infrastruktury klastra
Zamierzasz utworzyć klaster Service Fabric na maszynach, które są "własne", dzięki czemu możesz zdecydować, jakiego rodzaju awarie klaster ma przeżyły. Czy na przykład chcesz mieć osobne linie zasilające lub połączenia internetowe dostarczane do tych maszyn? Ponadto należy wziąć pod uwagę fizyczne zabezpieczenia tych maszyn. Gdzie znajdują się maszyny i które wymagają dostępu do nich? Po wprowadzeniu tych decyzji można logicznie mapować maszyny do różnych domen błędów (Zobacz następny krok). Planowanie infrastruktury dla klastrów produkcyjnych jest bardziej związane niż w przypadku klastrów testowych.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Określanie liczby domen błędów i domen uaktualnienia
[ *Domena błędów* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) jest fizyczną jednostką awarii i jest bezpośrednio związana z infrastrukturą fizyczną w centrach danych. Domena błędów składa się ze składników sprzętowych (komputerów, przełączników, sieci i innych), które współużytkują single point of failure. Chociaż nie istnieje 1:1 mapowanie między domenami błędów i stojakami, luźno mówiąc, każdy Stojak może być traktowany jako domena błędów.

Po określeniu domenami błędów w ClusterConfig.jsna, można wybrać nazwę dla każdego FD. Service Fabric obsługuje hierarchiczne domenami błędów, dzięki czemu można odzwierciedlić topologię infrastruktury.  Na przykład następujące domenami błędów są prawidłowe:

* "faultDomain": "FD:/Room1/Szafa1/Maszyna1"
* "faultDomain": "FD:/FD1"
* "faultDomain": "FD:/Room1/Szafa1/PDU1/M1"

*Domena uaktualnienia* (UD) jest jednostką logiczną węzłów. Podczas Service Fabricch uaktualnień w ramach aranżacji (Uaktualnienie aplikacji lub uaktualnienie klastra) wszystkie węzły w UD są przełączane w celu przeprowadzenia uaktualnienia, podczas gdy węzły w innych, są nadal dostępne do obsłużenia żądań. Uaktualnienia oprogramowania układowego wykonywanego na maszynach nie są honorowane, więc należy wykonać je pojedynczo.

Najprostszym sposobem na podejście do tego koncepcji jest rozważenie domenami błędów jako jednostki nieplanowanego błędu i odnoszącego się do jednostki planowanej konserwacji.

Jeśli określisz wartość w ClusterConfig.jsna, możesz wybrać nazwę dla każdego UDu. Na przykład następujące nazwy są prawidłowe:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "niebieska"

Aby uzyskać bardziej szczegółowe informacje na temat domenami błędów i do programu, zobacz [opisywanie klastra Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

Klaster w środowisku produkcyjnym powinien obejmować co najmniej trzy domenami błędów, aby można było je obsłużyć na potrzeby środowiska produkcyjnego. Jeśli masz pełną kontrolę nad konserwacją i zarządzaniem węzłami, oznacza to, że użytkownik jest odpowiedzialny za aktualizowanie i zastępowanie maszyn. W przypadku klastrów działających w środowiskach (czyli Amazon Web Services wystąpienia maszyn wirtualnych), w których nie masz pełnej kontroli nad maszynami, w klastrze powinien znajdować się co najmniej pięć domenami błędów. Każdy FD może mieć co najmniej jeden węzeł. Ma to na celu zapobieganie problemom spowodowanym przez uaktualnienia i aktualizacje maszyn, które w zależności od ich czasu mogą zakłócać uruchamianie aplikacji i usług w klastrach.

## <a name="determine-the-initial-cluster-size"></a>Określanie początkowego rozmiaru klastra

Ogólnie rzecz biorąc, liczba węzłów w klastrze jest określana na podstawie potrzeb firmy, czyli liczby usług i kontenerów, które będą działać w klastrze i ile zasobów jest potrzebnych do utrzymania obciążeń. W przypadku klastrów produkcyjnych zalecamy używanie co najmniej pięciu węzłów w klastrze, obejmujących 5 domenami błędów. Jednak zgodnie z powyższym opisem, jeśli masz pełną kontrolę nad węzłami i może obejmować trzy domenami błędów, to zadanie jest również trzy węzły.

Klastry testowe z uruchomionymi obciążeniami stanowymi powinny mieć trzy węzły, podczas gdy klastry testowe tylko z uruchomionymi obciążeniami bezstanowymi potrzebują tylko jednego węzła. Należy również zauważyć, że w celach programistycznych można mieć więcej niż jeden węzeł na danej maszynie. W środowisku produkcyjnym Service Fabric obsługuje tylko jeden węzeł na maszynę fizyczną lub wirtualną.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Przygotuj maszyny, które będą działać jako węzły

Poniżej przedstawiono zalecane specyfikacje dotyczące maszyn w klastrze Service Fabric:

* Co najmniej 16 GB pamięci RAM
* Co najmniej 40 GB dostępnego miejsca na dysku
* Procesor 4-lub większy
* Łączność z bezpieczną siecią lub sieciami dla wszystkich maszyn
* Zainstalowany system operacyjny Windows Server (prawidłowe wersje: 2012 R2, 2016, 1709 lub 1803). Service Fabric w wersji 6.4.654.9590 i nowszych również obsługuje serwer 2019 i 1809.
* [.NET Framework 4.5.1 lub nowszy](https://www.microsoft.com/download/details.aspx?id=40773), pełna instalacja
* [Windows PowerShell 3.0](/powershell/scripting/windows-powershell/install/installing-windows-powershell?view=powershell-7)
* [Usługa RemoteRegistry](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754820(v=ws.11)) powinna być uruchomiona na wszystkich maszynach
* **Dysk instalacji Service Fabric musi mieć system plików NTFS**
* **Dzienniki wydajności usług systemu Windows, *& alerty* i *Dziennik zdarzeń systemu Windows* , muszą [być włączone](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc755249(v=ws.11))**.

> [!IMPORTANT]
> Administrator klastra wdrażający i konfigurujący klaster musi mieć [uprawnienia administratora](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) na każdej z tych maszyn. Usługi Service Fabric nie można zainstalować na kontrolerze domeny.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Pobierz pakiet autonomiczny Service Fabric dla systemu Windows Server
[Link do pobierania — Service Fabric autonomiczny pakiet — system Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) i rozpakować pakiet na maszynę wdrożenia, która nie jest częścią klastra, lub do jednej z maszyn, które będą częścią klastra.

## <a name="modify-cluster-configuration"></a>Modyfikuj konfigurację klastra
Aby utworzyć klaster autonomiczny, należy utworzyć autonomiczną konfigurację klastra ClusterConfig.jsw pliku, która opisuje specyfikację klastra. Plik konfiguracji można oprzeć na szablonach znalezionych w poniższym łączu. <br>
[Konfiguracje klastra autonomicznego](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Aby uzyskać szczegółowe informacje dotyczące sekcji w tym pliku, zobacz [Ustawienia konfiguracji autonomicznego klastra systemu Windows](service-fabric-cluster-manifest.md).

Otwórz jeden z ClusterConfig.jsplików z pobranego pakietu i zmodyfikuj następujące ustawienia:

| **Ustawienie konfiguracji** | **Opis** |
| --- | --- |
| **Elementów NodeType** |Typy węzłów umożliwiają rozdzielenie węzłów klastra do różnych grup. Klaster musi mieć co najmniej jedną NodeType. Wszystkie węzły w grupie mają następujące typowe cechy: <br> **Nazwa** — nazwa typu węzła. <br>**Porty punktów końcowych** — są to różne nazwane punkty końcowe (porty), które są skojarzone z tym typem węzła. Możesz użyć dowolnego numeru portu, o ile nie są one sprzeczne z żadnym innym w tym manifeście i nie są jeszcze używane przez żadną inną aplikację uruchomioną na komputerze/maszynie wirtualnej. <br> **Właściwości umieszczania** — te informacje opisują właściwości tego typu węzła, które są używane jako ograniczenia umieszczania dla usług systemowych lub usług. Te właściwości to zdefiniowane przez użytkownika pary klucz/wartość, które zapewniają dodatkowe metadane dla danego węzła. Przykładami właściwości węzła jest to, czy węzeł ma dysk twardy, czy kartę graficzną, liczbę jednostek na dysku twardym, rdzenie i inne właściwości fizyczne. <br> **Pojemności** — pojemności węzłów definiują nazwę i ilość określonego zasobu, który jest dostępny do użycia w określonym węźle. Na przykład węzeł może zdefiniować, że ma pojemność dla metryki o nazwie "MemoryInMb" i że ma ona domyślnie dostępne 2048 MB. Te pojemności są używane w czasie wykonywania w celu zapewnienia, że usługi wymagające określonych ilości zasobów są umieszczane w węzłach, w których te zasoby są dostępne w wymaganych ilościach.<br>**Isprimary** — Jeśli masz więcej niż jeden NodeType, upewnij się, że tylko jeden z nich jest ustawiony na wartość *true*, co oznacza, że usługi systemowe są uruchamiane. Dla wszystkich innych typów węzłów należy ustawić wartość *false* . |
| **Węzły** |Są to szczegółowe informacje dotyczące każdego z węzłów, które są częścią klastra (typu węzła, nazwy węzła, adresu IP, domeny błędów i domeny uaktualnienia węzła). Komputery, na których ma zostać utworzone klaster, muszą być wymienione w tym miejscu przy użyciu adresów IP. <br> Jeśli używasz tego samego adresu IP dla wszystkich węzłów, zostanie utworzony klaster jednokrotny, którego można użyć do celów testowych. Nie należy używać jednobox klastrów do wdrażania obciążeń produkcyjnych. |

Po skonfigurowaniu wszystkich ustawień środowiska przez konfigurację klastra można je przetestować względem środowiska klastra (krok 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Konfigurowanie środowiska

Gdy administrator klastra konfiguruje autonomiczny klaster Service Fabric, należy skonfigurować środowisko z następującymi kryteriami: <br>
1. Użytkownik tworzący klaster powinien mieć uprawnienia zabezpieczeń na poziomie administratora dla wszystkich komputerów, które są wymienione jako węzły w pliku konfiguracji klastra.
2. Maszyna, z której jest tworzony klaster, a także każda maszyna węzła klastra musi:
   * Odinstalowanie zestawu SDK Service Fabric
   * Zainstalowano środowisko uruchomieniowe Service Fabric
   * Mieć włączoną usługę Zapora systemu Windows (MpsSvc)
   * Włączono usługę Rejestr zdalny (Rejestr zdalny)
   * Włączono udostępnianie plików (SMB)
   * Mieć otwarte wymagane porty na podstawie portów konfiguracji klastra
   * Mieć otwarte wymagane porty dla systemu Windows SMB i usługi Rejestr zdalny: 135, 137, 138, 139 i 445
   * Połączenie sieciowe ze sobą
3. Żadna z maszyn węzłów klastra nie powinna być kontrolerem domeny.
4. Jeśli klaster, który ma zostać wdrożony, jest zabezpieczonym klastrem, sprawdź, czy zostały spełnione wymagania wstępne dotyczące zabezpieczeń i czy zostały prawidłowo skonfigurowane na potrzeby konfiguracji.
5. Jeśli maszyny klastra nie są dostępne w Internecie, ustaw następujące ustawienia w konfiguracji klastra:
   * Wyłącz telemetrię: w obszarze *Właściwości* Ustaw *"enableTelemetry": false*
   * Wyłącz pobieranie automatycznej wersji sieci szkieletowej & powiadomienia, że aktualna wersja klastra zbliża się do końca wsparcia: w obszarze *Właściwości* Ustaw *"fabricClusterAutoupgradeEnabled": false*
   * Alternatywnie, jeśli dostęp do sieci Internet jest ograniczony do domen z białej listy, poniższe domeny są wymagane do automatycznego uaktualniania: go.microsoft.com download.microsoft.com

6. Ustaw odpowiednie wykluczenia programu antywirusowego Service Fabric:

| **Wykluczone katalogi oprogramowania antywirusowego** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (z konfiguracji klastra) |
| FabricLogRoot (z konfiguracji klastra) |

| **Wykluczone procesy programu antywirusowego** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Weryfikuj środowisko przy użyciu skryptu TestConfiguration
Skrypt TestConfiguration.ps1 można znaleźć w pakiecie autonomicznym. Jest on używany jako Analizator najlepszych rozwiązań do sprawdzania poprawności niektórych kryteriów powyżej i powinien być używany jako Sanity, aby sprawdzić, czy klaster można wdrożyć w danym środowisku. Jeśli wystąpi awaria, zapoznaj się z listą w obszarze [Konfiguracja środowiska](service-fabric-cluster-standalone-deployment-preparation.md) w celu rozwiązywania problemów.

Ten skrypt można uruchomić na dowolnym komputerze, który ma dostęp administratora do wszystkich maszyn wymienionych jako węzły w pliku konfiguracji klastra. Maszyna, na której jest uruchomiony ten skrypt, nie musi być częścią klastra.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
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

Obecnie ten moduł testowania konfiguracji nie sprawdza poprawności konfiguracji zabezpieczeń, dlatego musi być wykonywany niezależnie.

> [!NOTE]
> Ciągle wprowadzamy udoskonalenia, aby ten moduł był bardziej niezawodny, więc jeśli wystąpi wadliwy lub brakujący przypadek, w którym sądzisz, że nie jest on obecnie przechwytywany przez TestConfiguration, daj nam znać nasze [kanały pomocy technicznej](./service-fabric-support.md).
>
>

## <a name="next-steps"></a>Następne kroki
* [Tworzenie klastra autonomicznego działającego w systemie Windows Server](service-fabric-cluster-creation-for-windows-server.md)
