---
title: Poprawianie systemu operacyjnego Windows w klastrze Service Fabric
description: W tym artykule omówiono sposób automatyzowania stosowania poprawek systemu operacyjnego w klastrze Service Fabric przy użyciu aplikacji aranżacji patch.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: e51b247f8c1a5a9ed8f6ec8e24363015afb2f7de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614415"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Poprawianie systemu operacyjnego Windows w klastrze Service Fabric

## <a name="automatic-os-image-upgrades"></a>Automatyczne uaktualnienia obrazu systemu operacyjnego

[Korzystanie z automatycznych uaktualnień obrazu systemu operacyjnego na Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) jest najlepszym rozwiązaniem do zachowania poprawek systemu operacyjnego na platformie Azure. Automatyczne uaktualnienia obrazu systemu operacyjnego oparte na zestawie skalowania maszyn wirtualnych są wymagane do zastosowania Silver lub wyższej trwałości w zestawie skalowania.

Wymagania dotyczące automatycznych uaktualnień obrazu systemu operacyjnego przez Virtual Machine Scale Sets
-   [Poziom trwałości](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Service Fabric to Silver lub Gold, a nie brązowy.
-   Rozszerzenie Service Fabric w definicji modelu zestawu skalowania musi mieć wartość TypeHandlerVersion 1,1 lub nowszą.
-   Poziom trwałości powinien być taki sam w przypadku klastra Service Fabric i rozszerzenia Service Fabric w definicji modelu zestawu skalowania.
- Dodatkowa sonda kondycji lub użycie rozszerzenia kondycji aplikacji dla Virtual Machine Scale Sets nie jest wymagane.

Upewnij się, że ustawienia trwałości nie są niezgodne w przypadku klastra Service Fabric i rozszerzenia Service Fabric, ponieważ niezgodność spowoduje błędy uaktualniania. Poziomy trwałości można modyfikować zgodnie z wytycznymi opisanymi na [tej stronie](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).

W przypadku trwałości Bronze automatyczne uaktualnianie obrazu systemu operacyjnego jest niedostępne. Mimo że [aplikacja aranżacji poprawek](#patch-orchestration-application ) (przeznaczona tylko dla klastrów hostowanych poza platformą Azure) *nie jest zalecana* dla poziomów trwałości Silver lub większej, jest to jedyna opcja automatyzacji aktualizacji systemu Windows w odniesieniu do domen uaktualnienia Service Fabric.

> [!IMPORTANT]
> Uaktualnienia w maszynie wirtualnej, w których "Windows Update" stosuje poprawki systemu operacyjnego bez zastępowania dysku systemu operacyjnego nie są obsługiwane w usłudze Azure Service Fabric.

Należy wykonać dwa kroki, aby włączyć funkcję z wyłączonym Windows Update w systemie operacyjnym.

1. Włączanie automatycznego uaktualniania obrazu systemu operacyjnego, wyłączanie programu Windows Updates ARM 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    Azure PowerShell
    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ``` 
    
1. Aktualizuj model zestawu skalowania po zmianie tej konfiguracji, aby zaktualizować model zestawu skalowania, konieczna jest zmiana obrazu wszystkich maszyn, tak aby zmiany zostały zastosowane.
    
    Azure PowerShell
    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ``` 
    
Aby uzyskać dalsze instrukcje, zapoznaj się z [automatycznymi uaktualnieniami obrazu systemu operacyjnego, Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) .

## <a name="patch-orchestration-application"></a>Aplikacja aranżacji poprawek

> [!IMPORTANT]
> Od 30 kwietnia 2019, aplikacja aranżacji poprawek w wersji 1,2. * nie jest już obsługiwana. Upewnij się, że uaktualniono do najnowszej wersji.

Poprawka Orchestration Application (POA) to otoka obejmująca usługę Azure Service Fabric Menedżer naprawy, która umożliwia planowanie poprawek systemu operacyjnego opartych na konfiguracji dla klastrów hostowanych poza platformą Azure. POA nie jest wymagany w przypadku klastrów hostowanych poza platformą Azure, ale planowanie instalacji poprawek przy użyciu domeny aktualizacji jest wymagane do poprawki Service Fabric hostach klastra bez ponoszenia przestojów.

POA jest aplikacją Service Fabric, która automatyzuje stosowanie poprawek systemu operacyjnego w klastrze Service Fabric bez ponoszenia przestojów.

POA oferuje następujące funkcje:

- **Automatyczna instalacja aktualizacji systemu operacyjnego**. Aktualizacje systemu operacyjnego są automatycznie pobierane i instalowane. Węzły klastra są ponownie uruchamiane w razie potrzeby bez ponoszenia przestojów klastra.

- **Stosowanie poprawek obsługujących klaster i integracja z kondycją**. Podczas gdy POA stosuje aktualizacje, monitoruje kondycję węzłów klastra. Węzły klastra są aktualizowane po jednym węźle naraz lub w jednej domenie aktualizacji jednocześnie. Jeśli kondycja klastra ulegnie awarii ze względu na proces stosowania poprawek, poprawka zostaje zatrzymana, aby uniknąć pogorszenia problemu.

## <a name="internal-details-of-poa"></a>Wewnętrzne szczegóły dotyczące POA

POA składa się z następujących podskładników:

- **Usługa koordynatora**: Ta usługa stanowa jest odpowiedzialna za:
    - Koordynowanie zadania Windows Update w całym klastrze.
    - Przechowywanie wyniku zakończonych operacji Windows Update.

- **Usługa agenta węzła**: Ta usługa bezstanowa jest uruchamiana na wszystkich Service Fabric węzłach klastra. Usługa jest odpowiedzialna za:
    - Inicjowanie programu Node Agent NTService.
    - Monitorowanie NTService agenta.

- **Node Agent NTService**: Ta usługa systemu Windows NT jest uruchamiana z uprawnieniami wyższego poziomu (system). Z kolei usługa agenta węzła i usługa koordynatora są uruchamiane z uprawnieniem niskiego poziomu (usługa sieciowa). Usługa jest odpowiedzialna za wykonywanie następujących zadań Windows Update na wszystkich węzłach klastra:
    - Wyłączanie automatycznych aktualizacji systemu Windows w węźle.
    - Pobieranie i instalowanie aktualizacji systemu Windows zgodnie z zasadami dostarczonymi przez użytkownika.
    - Ponowne uruchomienie komputera po zakończeniu instalacji aktualizacji systemu Windows.
    - Przekazywanie wyników aktualizacji systemu Windows do usługi koordynatora.
    - Raportowanie raportów kondycji w przypadku niepowodzenia operacji po wyczerpaniu wszystkich ponownych prób.

> [!NOTE]
> POA używa usługi Service Fabric Menedżer naprawy, aby wyłączyć lub włączyć węzeł i przeprowadzić kontrole kondycji. Zadanie naprawy utworzone przez POA śledzi Windows Update postęp dla każdego węzła.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Wymagana minimalna wersja .NET Framework to 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Włącz usługę Menedżer naprawy (jeśli nie jest jeszcze uruchomiona)

POA wymaga włączenia usługi Menedżer naprawy w klastrze.

#### <a name="azure-clusters"></a>Klastry platformy Azure

W klastrach platformy Azure w warstwie trwałości Silver domyślnie włączona jest usługa Menedżer naprawy. W przypadku klastrów platformy Azure w warstwie trwałości Gold może być włączona usługa Menedżer naprawy, w zależności od tego, kiedy te klastry zostały utworzone. W klastrach platformy Azure w warstwie trwałości Bronze domyślnie nie jest włączona usługa Menedżer naprawy. Jeśli usługa jest już włączona, można ją zobaczyć w sekcji usługi systemowe w Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Witryna Azure Portal
Podczas konfigurowania klastra można włączyć Menedżer naprawy z Azure Portal. Podczas konfigurowania klastra wybierz opcję **uwzględnij Menedżer naprawy** w obszarze **funkcje dodatków**.

![Obraz przedstawiający Włączanie Menedżer naprawy z Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure Resource Manager model wdrażania
Alternatywnie można użyć [Azure Resource Manager model wdrażania](./service-fabric-cluster-creation-via-arm.md) , aby włączyć usługę Menedżer naprawy w nowych i istniejących klastrach Service Fabric. Pobierz szablon klastra, który chcesz wdrożyć. Można użyć przykładowych szablonów lub utworzyć szablon niestandardowego modelu wdrażania Azure Resource Manager. 

Aby włączyć usługę Menedżer naprawy przy użyciu [szablonu modelu wdrażania Azure Resource Manager](./service-fabric-cluster-creation-via-arm.md), wykonaj następujące czynności:

1. Upewnij się, że `apiVersion` ustawiono wartość *2017-07-01-Preview* dla zasobu *Microsoft. servicefabric/klastrów* . Jeśli jest inny, należy przeprowadzić aktualizację `apiVersion` do *2017-07-01 — wersja zapoznawcza* lub nowsza:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Włącz usługę Menedżer naprawy, dodając następującą `addonFeatures` sekcję po `fabricSettings` sekcji:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Po zaktualizowaniu szablonu klastra przy użyciu tych zmian należy je zastosować i umożliwić zakończenie aktualizacji. Teraz można zobaczyć usługę Menedżer naprawy działającą w klastrze. Jest on nazywany *siecią szkieletową:/system/RepairManagerService* w sekcji usługi systemowe w Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Autonomiczne klastry lokalne

Aby włączyć usługę Menedżer naprawy w nowym lub istniejącym klastrze Service Fabric, można użyć [ustawień konfiguracji autonomicznego klastra systemu Windows](./service-fabric-cluster-manifest.md).

Aby włączyć usługę Menedżer naprawy:

1. Upewnij się, że `apiVersion` w obszarze [ogólne Konfiguracje klastra](./service-fabric-cluster-manifest.md#general-cluster-configurations) ustawiono wartość *04-2017* lub nowszą, jak pokazano poniżej:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Włącz usługę Menedżer naprawy, dodając następującą `addonFeatures` sekcję po `fabricSettings` sekcji, jak pokazano poniżej:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Zaktualizuj manifest klastra o te zmiany przy użyciu zaktualizowanego manifestu klastra [Utwórz nowy klaster](./service-fabric-cluster-creation-for-windows-server.md) lub [Uaktualnij konfigurację klastra](./service-fabric-cluster-upgrade-windows-server.md). 

   Po uruchomieniu klastra ze zaktualizowanym manifestem klastra można zobaczyć usługę Menedżer naprawy działającą w klastrze. Jest on nazywany *siecią szkieletową:/system/RepairManagerService* i znajduje się w sekcji usługi systemowe w Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Skonfiguruj aktualizacje systemu Windows dla wszystkich węzłów

Automatyczne aktualizacje systemu Windows mogą prowadzić do utraty dostępności, ponieważ w tym samym czasie mogą być ponownie uruchomione wiele węzłów klastra. Domyślnie program próbuje wyłączyć automatyczne aktualizacje systemu Windows w każdym węźle klastra. Jeśli jednak ustawienia są zarządzane przez administratora lub zasady grupy, zalecamy ustawienie zasad Windows Update na "powiadamianie przed pobraniem".

## <a name="download-the-application-package"></a>Pobierz pakiet aplikacji

Aby pobrać pakiet aplikacji, przejdź do [strony udostępnianie aplikacji aranżacji poprawek](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) w witrynie GitHub.

## <a name="configure-poa-behavior"></a>Skonfiguruj zachowanie POA

Zachowanie POA można skonfigurować w celu spełnienia Twoich potrzeb. Zastąp wartości domyślne, przekazując parametr aplikacji podczas tworzenia lub aktualizowania aplikacji. Możesz podać parametry aplikacji, określając `ApplicationParameter` `Start-ServiceFabricApplicationUpgrade` `New-ServiceFabricApplication` polecenie cmdlet lub.

| Parametr        | Typ                          | Szczegóły |
|:-|-|-|
|MaxResultsToCache    |Długo                              | Maksymalna liczba Windows Updateych wyników, które powinny być buforowane. <br><br>Wartość domyślna to 3000, przy założeniu, że: <br> &nbsp;&nbsp;-Liczba węzłów wynosi 20. <br> &nbsp;&nbsp;-Liczba aktualizacji węzła miesięcznie wynosi 5. <br> &nbsp;&nbsp;-Liczba wyników na operację może wynosić 10. <br> &nbsp;&nbsp;-Wyniki dla ostatnich trzech miesięcy powinny być przechowywane. |
|TaskApprovalPolicy   |Wyliczenie <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy wskazuje zasady, które mają być używane przez usługę koordynatora do instalowania aktualizacji systemu Windows na Service Fabric węzłach klastra.<br><br>Dozwolone wartości to: <br>*NodeWise*: aktualizacje systemu Windows są instalowane po jednym węźle w danym momencie. <br> *UpgradeDomainWise*: aktualizacje systemu Windows są instalowane w jednej domenie aktualizacji jednocześnie. (W większości, wszystkie węzły należące do domeny aktualizacji mogą przejść do usługi Windows Update).<br><br> Aby ułatwić podjęcie decyzji, które zasady najlepiej nadają się dla klastra, zobacz sekcję [często zadawanych pytań](#frequently-asked-questions) .
|LogsDiskQuotaInMB   |Długo  <br> (Domyślnie: *1024*)               | Maksymalny rozmiar dzienników aplikacji aranżacji w MB, które mogą być utrwalane lokalnie w węzłach.
| WUQuery               | ciąg<br>(Domyślnie: *IsInstalled = 0*)                | Zapytanie w celu pobrania aktualizacji systemu Windows. Aby uzyskać więcej informacji, zobacz [WuQuery.](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)
| InstallWindowsOSOnlyUpdates | *Wartość logiczna* <br> (wartość domyślna: false)                 | Użyj tej flagi, aby określić, które aktualizacje mają zostać pobrane i zainstalowane. Następujące wartości są dozwolone <br>true — instaluje tylko aktualizacje systemu operacyjnego Windows.<br>false — instaluje wszystkie dostępne aktualizacje na komputerze.          |
| WUOperationTimeOutInMinutes | int <br>(Domyślnie: *90*)                   | Określa limit czasu dla każdej operacji Windows Update (Wyszukaj lub Pobierz lub zainstaluj). Jeśli operacja nie zostanie zakończona w określonym limicie czasu, zostanie przerwana.       |
| WURescheduleCount     | int <br> (Domyślnie: *5*)                  | Maksymalna liczba przypadków, w których usługa ponownie planuje aktualizację systemu Windows, jeśli operacja nie powiedzie się.          |
| WURescheduleTimeInMinutes | int <br>(Wartość domyślna: *30*) | Interwał, w którym usługa ponownie planuje aktualizacje systemu Windows, jeśli błąd będzie nadal występował. |
| WUFrequency           | Ciąg rozdzielony przecinkami (wartość domyślna: *Weekly, środa, 7:00:00*)     | Częstotliwość instalowania aktualizacji systemu Windows. Format i możliwe wartości to: <br>-Miesięcznie, DD, gg: MM: SS (przykład: *Monthly, 5, 12:22:32*). Dozwolone wartości pola _DD_ (Day) to liczby od 1 do 28 i _ostatnie_. <br>-Tydzień, dzień, gg: MM: SS (przykład: *Weekly, wtorek, 12:22:32*)  <br>-Codziennie, gg: MM: SS (przykład: *Daily, 12:22:32*)  <br>-Tydzień, dzień, gg: MM: SS (przykład: *2, piątek, 21:00:00* wskazuje 9:00 PM UTC w piątek tygodnia każdego miesiąca) <br>- *Brak* wskazuje, że aktualizacje systemu Windows nie należy wykonywać.  <br><br> Czasy są w formacie UTC.|
| AcceptWindowsUpdateEula | Wartość logiczna <br>(Wartość domyślna: *true*) | Ustawiając tę flagę, aplikacja akceptuje umowę licencyjną End-User Windows Update w imieniu właściciela maszyny.              |

> [!TIP]
> Jeśli aktualizacje systemu Windows mają być wykonywane natychmiast, należy ustawić `WUFrequency` względem czasu wdrożenia aplikacji. Załóżmy na przykład, że masz klaster testowy z pięcioma węzłami i planujesz wdrożenie aplikacji na około 5:00 PM czasu UTC. Jeśli założono, że uaktualnienie lub wdrożenie aplikacji trwa 30 minut, ustaw WUFrequency jako *codziennie, 17:30:00*.

## <a name="deploy-poa"></a>Wdróż aplikację POA

1. Wykonaj wszystkie kroki wymagań wstępnych, aby przygotować klaster.
1. Wdróż program POA podobnie jak każda inna aplikacja Service Fabric. Aby wdrożyć go przy użyciu programu PowerShell, zobacz [wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell](./service-fabric-deploy-remove-applications.md).
1. Aby skonfigurować aplikację w czasie wdrażania, należy przekazać `ApplicationParameter` do `New-ServiceFabricApplication` polecenia cmdlet. Dla wygody użytkownika udostępniamy skrypt Deploy.ps1 wraz z aplikacją. Aby użyć skryptu:

    - Nawiązywanie połączenia z klastrem Service Fabric przy użyciu programu `Connect-ServiceFabricCluster` .
    - Wykonaj Deploy.ps1 skryptu programu PowerShell z odpowiednią `ApplicationParameter` wartością.

> [!NOTE]
> Zachowaj skrypt i folder aplikacji *PatchOrchestrationApplication* w tym samym katalogu.

## <a name="upgrade-poa"></a>Uaktualnij POA

Aby uaktualnić wersję POA przy użyciu programu PowerShell, postępuj zgodnie z instrukcjami w temacie [Service Fabric upgrade Applications using PowerShell](./service-fabric-application-upgrade-tutorial-powershell.md).

## <a name="remove-poa"></a>Usuń POA

Aby usunąć aplikację, postępuj zgodnie z instrukcjami w temacie [wdrażanie i usuwanie aplikacji przy użyciu programu PowerShell](./service-fabric-deploy-remove-applications.md).

Dla wygody użytkownika udostępniamy skrypt Undeploy.ps1 wraz z aplikacją. Aby użyć skryptu:

  - Nawiązywanie połączenia z klastrem Service Fabric przy użyciu programu ```Connect-ServiceFabricCluster``` .
  - Wykonaj Undeploy.ps1 skryptu programu PowerShell.

> [!NOTE]
> Zachowaj skrypt i folder aplikacji *PatchOrchestrationApplication* w tym samym katalogu.

## <a name="view-the-windows-update-results"></a>Wyświetl wyniki Windows Update

POA udostępnia interfejsy API REST, aby wyświetlić historyczne wyniki dla użytkowników. Oto przykład kodu JSON wyniku:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

Pola JSON są opisane w poniższej tabeli:

Pole | Wartości | Szczegóły
-- | -- | --
Klasy OperationResult | 0 — powodzenie<br> 1 — powodzenie z błędami<br> 2 — Niepowodzenie<br> 3 — przerwana<br> 4 — przerwano z limitem czasu | Wskazuje wynik operacji ogólnej, która zwykle obejmuje instalację jednej lub kilku aktualizacji.
ResultCode | Analogicznie jak klasy OperationResult | To pole wskazuje wynik operacji instalacji dla pojedynczej aktualizacji.
OperationType | 1 — Instalacja<br> 0 — wyszukiwanie i pobieranie| Domyślnie instalacja jest jedyną operacją, która jest wyświetlana w wynikach.
WindowsUpdateQuery | Wartość domyślna to "IsInstalled = 0" | Zapytanie Windows Update używane do wyszukiwania aktualizacji. Aby uzyskać więcej informacji, zobacz [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search).
RebootRequired | true — wymagany jest ponowny rozruch<br> FAŁSZ — nie jest wymagane ponowne uruchomienie komputera | Wskazuje, czy do ukończenia instalacji aktualizacji jest wymagany ponowny rozruch.
OperationStartTime | DateTime | Wskazuje czas, w którym uruchomiono operację (pobieranie/instalacja).
OperationTime | DateTime | Wskazuje czas ukończenia operacji (pobieranie/instalacja).
Wynik | 0 — powodzenie<br> inne — niepowodzenie| Wskazuje przyczynę niepowodzenia usługi Windows Update z updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Jeśli nie zaplanowano jeszcze żadnej aktualizacji, wynikowy kod JSON jest pusty.

Zaloguj się do klastra w celu zbadania Windows Update wyników. Sprawdź adres IP repliki dla podstawowego adresu usługi koordynatora i Otwórz następujący adres URL w przeglądarce: http:// &lt; Replica-IP &gt; : &lt; ApplicationPort &gt; /PatchOrchestrationApplication/V1/GetWindowsUpdateResults.

Punkt końcowy REST usługi koordynatora ma port dynamiczny. Aby sprawdzić dokładny adres URL, zapoznaj się z tematem Service Fabric Explorer. Na przykład wyniki są dostępne pod adresem *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* .

![Obraz punktu końcowego REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Jeśli zwrotny serwer proxy jest włączony w klastrze, można uzyskać dostęp do adresu URL spoza klastra.

Wymagany punkt końcowy to *http:// &lt; SERVERURL &gt; : &lt; REVERSEPROXYPORT &gt; /PatchOrchestrationApplication/CoordinatorService/V1/GetWindowsUpdateResults*.

Aby włączyć zwrotny serwer proxy w klastrze, postępuj zgodnie z instrukcjami w [odwrotnym serwerze proxy na platformie Azure Service Fabric](./service-fabric-reverseproxy.md). 

> 
> [!WARNING]
> Po skonfigurowaniu zwrotnego serwera proxy wszystkie mikrousługi w klastrze, które uwidaczniają punkt końcowy HTTP, są adresowane spoza klastra.

## <a name="diagnostics-and-health-events"></a>Zdarzenia diagnostyki i kondycji

W tej sekcji omówiono debugowanie lub diagnozowanie problemów z aktualizacjami poprawek za pomocą programu POA w klastrach Service Fabric.

> [!NOTE]
> Aby uzyskać wiele z następujących wywoływanych ulepszeń samoobsługowych, należy mieć zainstalowany program POA w wersji 1.4.0 lub nowszej.

Agent Node NTService tworzy [zadania naprawcze](/dotnet/api/system.fabric.repair.repairtask) służące do instalowania aktualizacji w węzłach. Każde zadanie jest następnie przygotowywane przez usługę koordynatora zgodnie z zasadami zatwierdzania zadań. Na koniec przygotowane zadania są zatwierdzane przez Menedżer naprawy, które nie zatwierdzają żadnych zadań, Jeśli klaster jest w złej kondycji. 

Aby ułatwić zrozumienie, jak aktualizacje są realizowane w węźle, przejdźmy krok po kroku:

1. NodeAgentNTService, uruchomione w każdym węźle, wyszukuje dostępne aktualizacje systemu Windows w zaplanowanym czasie. Jeśli aktualizacje są dostępne, pobiera je w węźle.

1. Po pobraniu aktualizacji Agent węzła NTService tworzy odpowiednie zadanie naprawy dla węzła o nazwie *POS___ \<unique_id>*. Te zadania naprawy można wyświetlić za pomocą polecenia cmdlet [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) lub za pomocą SFX w sekcji Szczegóły węzła. Po utworzeniu zadania naprawy szybko przechodzi do [stanu *zatwierdzono*](/dotnet/api/system.fabric.repair.repairtaskstate).

1. Usługa koordynatora okresowo szuka zadań naprawy w stanie *zatwierdzono* , a następnie aktualizuje je w celu *przygotowania* stanu na podstawie TaskApprovalPolicy. Jeśli TaskApprovalPolicy jest skonfigurowany jako NodeWise, zadanie naprawy odnoszące się do węzła jest przygotowywane tylko wtedy, gdy żadne inne zadanie naprawy nie jest obecnie *przygotowywane*, *zatwierdzane*, *wykonywane* lub *przywracane* . 

   Podobnie w przypadku UpgradeWise TaskApprovalPolicy istnieją zadania w powyższych Stanach tylko dla węzłów należących do tej samej domeny aktualizacji. Po przeniesieniu zadania naprawy do stanu *przygotowywania* odpowiedni węzeł Service Fabric jest [wyłączony](/powershell/module/servicefabric/disable-servicefabricnode) z zamiarem ustawionym na *ponowne uruchomienie*.

   Wersje POA 1.4.0 i nowsze zdarzenia wpisu z właściwością ClusterPatchingStatus na CoordinatorService w celu wyświetlenia poprawek, które są używane. Aktualizacje są instalowane na _poanode_0, jak pokazano na poniższej ilustracji:

    [![Obraz stanu poprawek klastra](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. Po wyłączeniu węzła zadanie naprawy jest przenoszone do stanu *wykonywania* . 
   
   > [!NOTE]
   > Węzeł, który jest zablokowany w stanie *wyłączone* , może blokować nowe zadanie naprawy, które wstrzymuje operację stosowania poprawek w klastrze.

1. Gdy zadanie naprawy jest w stanie *wykonywania* , rozpocznie się instalacja poprawek w tym węźle. Po zainstalowaniu poprawki węzeł może lub nie może zostać ponownie uruchomiony, w zależności od poprawki. Następnie zadanie naprawy jest przenoszone do stanu *przywracania* , co powoduje ponowną obsługę węzła. Zadanie naprawy jest następnie oznaczane jako ukończone.

   W programie POA w wersji 1.4.0 i nowszych można znaleźć stan aktualizacji, wyświetlając zdarzenia kondycji na NodeAgentService przy użyciu WUOperationStatus- \<NodeName> Property. Wyróżnione sekcje na poniższych ilustracjach pokazują stan aktualizacji systemu Windows w węzłach *poanode_0* i *poanode_2*:

   [![Zrzut ekranu przedstawia okno konsoli Windows Update stan operacji z wyróżnioną poanode_0.](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Zrzut ekranu przedstawia okno konsoli Windows Update stan operacji z wyróżnioną poanode_1.](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Możesz również uzyskać szczegółowe informacje za pomocą programu PowerShell. W tym celu należy połączyć się z klastrem i pobrać stan zadania naprawy przy użyciu polecenia [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask). 
   
   W poniższym przykładzie zadanie "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" jest w stanie *DownloadComplete* . Oznacza to, że aktualizacje zostały pobrane w węźle *poanode_2* i zostanie podjęta próba instalacji, gdy zadanie przejdzie do stanu *wykonywania* .

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Jeśli chcesz znaleźć więcej problemów, zaloguj się do maszyny wirtualnej lub maszyn wirtualnych i uzyskaj informacje o nich przy użyciu dzienników zdarzeń systemu Windows. Wyżej wymienione zadanie naprawy może istnieć tylko w następujących podstanach wykonywania:

      ExecutorSubState | Opis
    -- | -- 
      Brak = 1 |  Oznacza, że nie było trwającą operacją w węźle. Stan może być w fazie przejścia.
      DownloadCompleted = 2 | Oznacza, że operacja pobierania została ukończona z sukcesem, częściowym uszkodzeniem lub błędem.
      InstallationApproved = 3 | Oznacza, że operacja pobierania została zakończona wcześniej, a Menedżer naprawy zatwierdziła instalację.
      InstallationInProgress = 4 | Odnosi się do stanu wykonania zadania naprawy.
      InstallationCompleted = 5 | Oznacza, że instalacja została ukończona z sukcesem, częściowym sukcesem lub niepowodzeniem.
      RestartRequested = 6 | Oznacza, że instalacja poprawki została ukończona i istnieje oczekująca akcja ponownego uruchomienia w węźle.
      RestartNotNeeded = 7 |  Oznacza, że ponowne uruchomienie nie było wymagane po zakończeniu instalacji poprawki.
      RestartCompleted = 8 | Oznacza, że ponowne uruchomienie zostało zakończone pomyślnie.
      OperationCompleted = 9 | Operacja Windows Update została ukończona pomyślnie.
      OperationAborted = 10 | Oznacza, że operacja Windows Update została przerwana.

1. W programie POA w wersji 1.4.0 i nowszych po zakończeniu próby aktualizacji węzła zdarzenie z właściwością "WUOperationStatus-[nodename]" jest ogłaszane w usłudze NodeAgentService, aby powiadomić użytkownika, gdy zostanie rozpoczęta następna próba pobrania i zainstalowania aktualizacji systemu Windows. Ta wartość jest wyświetlana na poniższym obrazie:

     [![Zrzut ekranu przedstawia okno konsoli Windows Update stanu operacji z NodeAgentService.](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Dzienniki diagnostyczne

Dzienniki aplikacji aranżacji poprawek są zbierane w ramach dzienników środowiska uruchomieniowego Service Fabric.

Dzienniki można przechwycić za pomocą wybranego narzędzia diagnostycznego lub potoku. POA używa następujących stałych identyfikatorów dostawcy do rejestrowania zdarzeń za pośrednictwem [źródła zdarzeń](/dotnet/api/system.diagnostics.tracing.eventsource):

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Raporty dotyczące kondycji

POA publikuje także raporty o kondycji dotyczące usługi agenta węzłów lub usługi koordynatora w następujących scenariuszach:

* NTService agenta węzła nie działa

   Jeśli Agent węzła NTService nie działa w węźle, generowany jest raport o kondycji na poziomie ostrzeżenia dla usługi agenta węzła.

* Usługa Menedżer naprawy nie jest włączona

   Jeśli usługa Menedżer naprawy nie zostanie znaleziona w klastrze, generowany jest raport o kondycji na poziomie ostrzeżenia dla usługi koordynatora.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P: Dlaczego widzę mój klaster w stanie błędu, gdy jest uruchomiony program POA?**

Odp.: w trakcie instalacji, POA wyłącza lub uruchamia ponownie węzły, co może spowodować tymczasowy klaster w złej kondycji.

W zależności od zasad aplikacji jeden węzeł może zostać wyłączony podczas stosowania poprawek *lub* cała domena aktualizacji może przejść w dół.

Po zakończeniu instalacji aktualizacji systemu Windows węzły są ponownie włączone po ponownym uruchomieniu.

W poniższym przykładzie klaster przeszedł do stanu błędu tymczasowo, ponieważ dwa węzły były wyłączone i zasady MaxPercentageUnhealthyNodes zostały naruszone. Błąd jest tymczasowy do momentu rozpoczęcia operacji poprawki.

![Obraz przedstawiający klaster w złej kondycji](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Jeśli problem będzie się powtarzać, zapoznaj się z sekcją rozwiązywania problemów.

**P: co mogę zrobić, jeśli wartość POA jest w stanie ostrzegawczym?**

Odp.: Sprawdź, czy raport kondycji opublikowany dla aplikacji wskazuje główną przyczynę. Zwykle ostrzeżenie zawiera szczegółowe informacje o problemie. Jeśli problem jest przejściowy, oczekiwanie na automatyczne odzyskanie aplikacji.

**P: co mogę zrobić, jeśli mój klaster jest w złej kondycji i muszę przeprowadzić pilną aktualizację systemu operacyjnego?**

Odp.: POA nie instaluje aktualizacji, gdy klaster jest w złej kondycji. Spróbuj przełączyć klaster do stanu dobrej kondycji, aby odblokować przepływ pracy POA.

**P: czy należy ustawić TaskApprovalPolicy jako "NodeWise" lub "UpgradeDomainWise" dla mojego klastra?**

Odp.: ustawienie "UpgradeDomainWise" przyspiesza ogólne naprawianie klastra przez stosowanie poprawek równolegle do wszystkich węzłów należących do domeny aktualizacji. W trakcie procesu węzły należące do całej domeny aktualizacji są niedostępne (w [stanie *wyłączone*](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)).

W przeciwieństwie do ustawienia "NodeWise" są poprawiane tylko jeden węzeł, co oznacza, że ogólna poprawka klastra może trwać dłużej. Jednak tylko jeden węzeł nie będzie dostępny (w stanie *wyłączone* ) podczas procesu stosowania poprawek.

Jeśli klaster może tolerować działanie na N-1 Liczba domenach aktualizacji w cyklu stosowania poprawek (gdzie N to całkowita liczba domen aktualizacji w klastrze), zasady można ustawić jako "UpgradeDomainWise". W przeciwnym razie ustaw dla niego wartość "NodeWise".

**P: ile czasu zajmuje się poprawką węzła?**

Odp.: Poprawka węzła może trwać od kilku minut (na przykład [Aktualizacje definicji usługi Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions)) do godzin (na przykład [aktualizacji zbiorczych systemu Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Czas wymagany do zastosowania poprawek węzła zależy głównie od: 
 - Rozmiar aktualizacji.
 - Liczba aktualizacji, które należy zastosować w oknie poprawki.
 - Czas potrzebny na zainstalowanie aktualizacji, ponowne uruchomienie węzła (jeśli jest to wymagane) i zakończenie kroków instalacji po ponownym uruchomieniu.
 - Wydajność maszyny wirtualnej lub maszyny oraz warunki sieciowe.

**P: jak długo trwa poprawka całego klastra?**

Odp.: czas wymagany do zastosowania poprawki całego klastra zależy od następujących:

- Czas wymagany do zastosowania poprawek do węzła.

- Zasady usługi koordynatora. Zasady domyślne, "NodeWise", w wyniku stosowania poprawek tylko jeden węzeł w danym momencie, są wolniejsze niż użycie "UpgradeDomainWise". 

   Na przykład: Jeśli w węźle trwa wartość 1 godzina, należy zastosować poprawkę do 20 węzłów (tego samego typu) klastra z 5 domenami aktualizacji, z których każda zawiera 4 węzły, wymagane:
    - Dla "NodeWise": ~ 20 godzin.
    - Dla "UpgradeDomainWise": ~ 5 godzin.

- Obciążenie klastra. Każda operacja Patch wymaga przelokalizowania obciążenia klienta do innych dostępnych węzłów w klastrze. W tym czasie nastąpi [ *wyłączenie*](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling) poprawki w węźle. Jeśli klaster działa blisko szczytowego obciążenia, proces wyłączania zajmie więcej czasu. W związku z tym ogólna procedura stosowania poprawek może wydawać się niska w takich warunkach.

- Błędy kondycji klastra podczas stosowania poprawek. Wszelkie [obniżenie](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error) [kondycji klastra](./service-fabric-health-introduction.md) spowodowałoby przerwanie procesu stosowania poprawek. Ten problem zostanie dodany do całkowitego czasu wymaganego do zastosowania poprawki całego klastra.

**P: Dlaczego widzę niektóre aktualizacje w Windows Update wyników uzyskanych za pośrednictwem interfejsu API REST, ale nie w ramach historii Windows Update na maszynie?**

Odp.: niektóre aktualizacje produktów są wyświetlane tylko we własnej historii aktualizacji lub poprawek. Na przykład aktualizacje usługi Windows Defender mogą lub nie są wyświetlane w historii Windows Update w systemie Windows Server 2016.

**P: Czy można użyć POA do poprawki mojego klastra deweloperskiego (klaster z jednym węzłem)?**

Odp.: nie, nie można użyć POA do poprawki klastra z jednym węzłem. To ograniczenie jest zaprojektowane, ponieważ [usługi systemu Service Fabric](./service-fabric-technical-overview.md#system-services) lub inne aplikacje klienta spowodują Przestój. W związku z tym zadania naprawy poprawki nigdy nie zostaną zatwierdzone przez Menedżer naprawy.

**P: Jak mogę zastosować poprawki do węzłów klastra w systemie Linux?**

Odp.: Aby dowiedzieć się więcej o aranżacji aktualizacji w systemie Linux, zobacz [zestaw skalowania maszyn wirtualnych platformy Azure automatyczne uaktualnienia obrazu systemu operacyjnego](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

**P: Dlaczego cykl aktualizacji trwa tak długo?**

Odp.: zapytanie dotyczące wynikowego kodu JSON, wprowadź cykl aktualizacji dla wszystkich węzłów, a następnie spróbuj sprawdzić czas potrzebny przez aktualizację instalacji na każdym węźle przy użyciu OperationStartTime i OperationTime (OperationCompletionTime). 

W przypadku dużego przedziału czasu, w którym nie ma aktualizacji, klaster może być w stanie błędu i w związku z tym Menedżer naprawy nie może zatwierdzić żadnych zadań naprawy POA. Jeśli instalacja aktualizacji trwa długo na dowolnym węźle, ten węzeł mógł nie zostać zaktualizowany przez pewien czas. Duża liczba aktualizacji może być w trakcie instalacji, co może spowodować opóźnienia. 

Może być również możliwe, że stosowanie poprawek węzłów zostało zablokowane, ponieważ jest ono zablokowane  . Zwykle dzieje się tak, ponieważ wyłączenie węzła może prowadzić do wystąpienia kworum lub utraty danych.

**P: Dlaczego węzeł musi być wyłączony, gdy jest w nim poprawka POA?**

Odp.: POA wyłącza węzeł z zamiarą *ponownego uruchomienia* , która powoduje zatrzymanie lub ponowne przydzielenie wszystkich usług Service Fabric uruchomionych w węźle. POA robi to, aby upewnić się, że aplikacje nie zakończą się przy użyciu kombinacji nowych i starych bibliotek DLL, dlatego nie zaleca się stosowania poprawek do węzła bez wyłączania go.

**P: Jaka jest maksymalna liczba węzłów, które można zaktualizować za pomocą POA?**

Odp.: POA używa Service Fabric Menedżer naprawy do tworzenia zadań naprawczych dla węzłów do aktualizacji. Jednak nie może istnieć więcej niż 250 zadań naprawy w tym samym czasie. Obecnie, POA tworzy zadania naprawcze dla każdego węzła w tym samym czasie, więc POA może zaktualizować nie więcej niż 250 węzłów w klastrze. 

## <a name="disclaimers"></a>Zastrzeżenia

- POA akceptuje End-User umowę licencyjną Windows Update w imieniu użytkownika. Opcjonalnie ustawienie to może być wyłączone w konfiguracji aplikacji.

- POA zbiera dane telemetryczne służące do śledzenia użycia i wydajności. Dane telemetryczne aplikacji są zgodne z ustawieniem ustawienia telemetrii środowiska uruchomieniowego Service Fabric, które jest domyślnie włączone.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ta sekcja zawiera możliwe rozwiązania rozwiązywania problemów z poprawkami węzłów.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Węzeł nie wraca do stanu up

* Węzeł może być zablokowany w stanie *wyłączania* , ponieważ:

  - Sprawdzanie bezpieczeństwa jest w stanie oczekiwania. Aby rozwiązać ten problem, upewnij się, że wystarczająca liczba węzłów jest w dobrej kondycji.

* Węzeł może być zablokowany w stanie *wyłączenia* , ponieważ:

  - Zostało wyłączone ręcznie.
  - Zostało wyłączone z powodu trwającego zadania infrastruktury platformy Azure.
  - Plik został tymczasowo wyłączony przez POA, aby zastosować poprawkę do węzła.

* Węzeł może być zablokowany w stanie down, ponieważ:

  - Został on umieszczony w stanie down ręcznie.
  - Powoduje to ponowne uruchomienie (które może zostać wyzwolone przez POA).
  - Ma ona wadliwą maszynę wirtualną lub maszynę lub ma problemy z łącznością sieciową.

### <a name="updates-were-skipped-on-some-nodes"></a>Aktualizacje zostały pominięte na niektórych węzłach

POA próbuje zainstalować aktualizację systemu Windows zgodnie z zasadami ponownego planowania. Usługa próbuje odzyskać węzeł i pominąć aktualizację zgodnie z zasadami aplikacji.

W takim przypadku Raport kondycji na poziomie ostrzeżenia jest generowany w odniesieniu do usługi agenta węzła. Wynik Windows Update również zawiera możliwą przyczynę niepowodzenia.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>Kondycja klastra przechodzi do błędu podczas instalowania aktualizacji

Wadliwa Aktualizacja systemu Windows może spowodować, że kondycja aplikacji lub klastra znajduje się w określonym węźle lub domenie aktualizacji. POA nie kontynuuje żadnej kolejnej Windows Update operacji, dopóki klaster nie będzie w dobrej kondycji.

Administrator musi interweniować i określić przyczynę złej kondycji aplikacji lub klastra z powodu Windows Update.

## <a name="poa-release-notes"></a>Informacje o wersji POA

>[!NOTE]
> W przypadku wersji POA 1.4.0 i nowszych można znaleźć informacje o wersji i wydania na [stronie wersji aplikacji aranżacji poprawek](https://github.com/microsoft/Service-Fabric-POA/releases/) w witrynie GitHub.

### <a name="version-110"></a>1.1.0 wersja
- Wydanie publiczne

### <a name="version-111"></a>Wersja 1.1.1
- Naprawiono usterkę w SetupEntryPoint NodeAgentService, która uniemożliwiła instalację NodeAgentNTService.

### <a name="version-120"></a>1.2.0 wersja

- Poprawki błędów wokół przepływu pracy ponownego uruchomienia systemu.
- Poprawka błędu podczas tworzenia zadań Menedżera zasobów, z powodu tego, że sprawdzanie kondycji podczas przygotowywania zadań naprawy nie było wykonywane zgodnie z oczekiwaniami.
- Zmieniono tryb uruchamiania usługi Windows POANodeSvc z automatycznie na opóźniony — automatycznie.

### <a name="version-121"></a>Wersja 1.2.1

- Poprawka błędu w przepływie pracy skalowania w dół klastra. Wprowadzono logikę wyrzucania elementów bezużytecznych dla zadań naprawy POA należących do nieistniejących węzłów.

### <a name="version-122"></a>Wersja 1.2.2

- Różne poprawki błędów.
- Pliki binarne są teraz podpisane.
- Dodano link sfpkg dla aplikacji.

### <a name="version-130"></a>1.3.0 wersja

- Ustawienie InstallWindowsOSOnlyUpdates na false spowoduje zainstalowanie wszystkich dostępnych aktualizacji.
- Zmieniono logikę wyłączania aktualizacji automatycznych. Eliminuje to usterkę, w której aktualizacje automatyczne nie zostały wyłączone na serwerze 2016 i nowszych.
- Sparametryzowane ograniczenie położenia dla mikrousług POA dla zaawansowanych przypadków użycia.

### <a name="version-131"></a>Wersja 1.3.1
- Naprawianie regresji, gdzie 1.3.0 POA nie będzie działała w systemie Windows Server 2012 R2 lub starszym z powodu błędu wyłączenia aktualizacji automatycznych. 
- Naprawianie usterki, w której konfiguracja InstallWindowsOSOnlyUpdates jest zawsze wybierana jako true.
- Zmiana wartości domyślnej InstallWindowsOSOnlyUpdates na false.

### <a name="version-132"></a>Wersja 1.3.2
- Rozwiązanie problemu wpływającego na cykl życia poprawek w węźle, jeśli istnieją węzły o nazwie, która jest podzbiorem bieżącej nazwy węzła. W przypadku takich węzłów istnieje możliwość, że poprawki zostały pominięte lub oczekiwanie na ponowne uruchomienie.
