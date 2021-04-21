---
title: Azure Automation omówienie hybrydowego procesu roboczego runbook
description: Ten artykuł zawiera omówienie hybrydowego procesu roboczego runbook, za pomocą którego można uruchamiać element Runbook na maszynach w lokalnym centrum danych lub dostawcy usług w chmurze.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2bb178302d399805eb84b233060d5717e2dba8b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830560"
---
# <a name="hybrid-runbook-worker-overview"></a>Omówienie hybrydowych procesów roboczych elementów Runbook

Zasoby runbook w Azure Automation mogą nie mieć dostępu do zasobów w innych chmurach lub w środowisku lokalnym, ponieważ działają na platformie Azure w chmurze. Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook w programie Azure Automation można uruchamiać element runbook bezpośrednio na komputerze hostującym rolę i na zasobach w środowisku w celu zarządzania tymi zasobami lokalnymi. W programie są przechowywane i zarządzane Azure Automation, a następnie dostarczane do co najmniej jednej przypisanej maszyny.

## <a name="runbook-worker-types"></a>Typy procesów roboczych elementów Runbook

Istnieją dwa typy pracowników runbook — system i użytkownik. W poniższej tabeli opisano różnicę między nimi.

|Typ | Opis |
|-----|-------------|
|**System** |Obsługuje zestaw ukrytych elementach Runbook używanych przez funkcję Update Management, które są przeznaczone do instalowania aktualizacji określonych przez użytkownika na maszynach z systemami Windows i Linux.<br> Ten typ hybrydowego procesu roboczego runbook nie jest elementem członkowskim grupy hybrydowych procesów roboczych runbook i dlatego nie uruchamia elementów Runbook, które są kierowane do grupy procesów roboczych runbook. |
|**Użytkownik** |Obsługuje zdefiniowane przez użytkownika elementy Runbook przeznaczone do uruchamiania bezpośrednio na maszynie z systemami Windows i Linux, które są członkami co najmniej jednej grupy procesów roboczych elementów Runbook. |

Hybrydowy proces roboczy runbook może działać w systemie operacyjnym Windows lub Linux, a ta rola opiera się na raportowaniu agenta usługi [Log Analytics](../azure-monitor/agents/log-analytics-agent.md) do Azure Monitor [usługi Log Analytics.](../azure-monitor/logs/design-logs-deployment.md) Obszar roboczy jest nie tylko do monitorowania maszyny dla obsługiwanego systemu operacyjnego, ale także do pobierania składników wymaganych do zainstalowania hybrydowego procesu roboczego elementów Runbook.

Gdy Azure Automation [Update Management,](./update-management/overview.md) każda maszyna połączona z obszarem roboczym usługi Log Analytics zostanie automatycznie skonfigurowana jako systemowy hybrydowy proces roboczy runbook. Aby skonfigurować go jako hybrydowy proces roboczy runbook systemu Windows dla użytkownika, zobacz [Deploy a Windows Hybrid Runbook Worker](automation-windows-hrw-install.md) (Wdrażanie hybrydowego procesu roboczego runbook systemu Windows) i Deploy a Linux Hybrid Runbook Worker (Wdrażanie hybrydowego procesu [roboczego runbook systemu Linux).](automation-linux-hrw-install.md)

## <a name="how-does-it-work"></a>Jak to działa?

![Omówienie hybrydowych procesów roboczych elementów Runbook](media/automation-hybrid-runbook-worker/automation.png)

Każdy użytkownik hybrydowego procesu roboczego runbook jest członkiem grupy hybrydowych procesów roboczych runbook poszczególnych użytkowników, która jest określana podczas instalowania procesu roboczego. Grupa może zawierać jednego procesu roboczego, ale można dołączyć do grupy wiele procesów roboczych, aby uzyskać wysoką dostępność. Każda maszyna może hostować jeden hybrydowy proces roboczy runbook raportowania na jednym koncie usługi Automation. Nie można zarejestrować hybrydowego procesu roboczego na wielu kontach usługi Automation. Hybrydowy proces roboczy może nasłuchiwać zadań tylko z jednego konta usługi Automation. W przypadku maszyn hostujących systemowy hybrydowy proces roboczy runbook zarządzany przez Update Management można dodać je do grupy hybrydowych procesów roboczych runbook. Należy jednak użyć tego samego konta usługi Automation zarówno dla Update Management, jak i członkostwa w grupie hybrydowych procesów roboczych runbook.

Podczas uruchamiania runbook w hybrydowym procesu roboczego runbook użytkownika należy określić grupę, w których jest uruchamiany. Każdy proces roboczy w grupie sonduje Azure Automation, czy są dostępne jakiekolwiek zadania. Jeśli zadanie jest dostępne, pierwszy proces roboczy, który je pobierze, je przejmuje. Czas przetwarzania kolejki zadań zależy od profilu sprzętu hybrydowego procesu roboczego i obciążenia. Nie można określić określonego procesu roboczego. Hybrydowy proces roboczy działa na mechanizmie sondowania (co 30 sek.) i jest zgodny z kolejnością " pierwszy, pierwszy- i pierwszy" . W zależności od tego, kiedy zadanie zostało wypchne, niezależnie od tego, który hybrydowy proces roboczy wysyła polecenie ping, usługa Automation przejmuje zadanie. Pojedynczy hybrydowy proces roboczy zazwyczaj może odbierać cztery zadania na polecenie ping (czyli co 30 sekund). Jeśli szybkość wypychania zadań jest większa niż cztery na 30 sekund, istnieje duża możliwość, że zadanie zostanie odebrane przez innego hybrydowego procesu roboczego w grupie hybrydowych procesów roboczych runbook.

Hybrydowy proces roboczy runbook nie ma wielu [](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) limitów zasobów piaskownicy platformy [Azure](automation-runbook-execution.md#runbook-execution-environment) dla miejsca na dysku, pamięci lub gniazd sieciowych. Limity hybrydowego procesu roboczego są związane tylko z jego własnymi zasobami [](automation-runbook-execution.md#fair-share) i nie są ograniczone przez limit czasu sprawiedliwego udostępniania, który mają piaskownice platformy Azure.

Aby kontrolować dystrybucję podręczników runbook w hybrydowych procesach roboczych runbook oraz czas lub sposób wyzwalania zadań, możesz zarejestrować hybrydowy proces roboczy w różnych grupach hybrydowych procesów roboczych runbook w ramach konta usługi Automation. Przekieruj zadania do określonej grupy lub grup w celu obsługi układu wykonywania.

## <a name="hybrid-runbook-worker-installation"></a>Instalacja hybrydowego procesu roboczego runbook

Proces instalowania hybrydowego procesu roboczego runbook użytkownika zależy od systemu operacyjnego. W poniższej tabeli zdefiniowano typy wdrożeń.

|System operacyjny  |Typy wdrożeń  |
|---------|---------|
|Windows     | [Automatyczny](automation-windows-hrw-install.md#automated-deployment)<br>[Ręczne](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Ręczne](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Zalecaną metodą instalacji maszyny z systemem Windows jest użycie Azure Automation Runbook w celu całkowitego zautomatyzowania procesu jego konfigurowania. Jeśli nie jest to możliwe, możesz wykonać procedurę krok po kroku, aby ręcznie zainstalować i skonfigurować rolę. W przypadku maszyn z systemem Linux należy uruchomić skrypt języka Python, aby zainstalować agenta na maszynie.

## <a name="network-planning"></a><a name="network-planning"></a>Planowanie sieci

Sprawdź [Azure Automation sieci,](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) aby uzyskać szczegółowe informacje na temat portów, adresów URL i innych szczegółów sieci wymaganych dla hybrydowego procesu roboczego runbook.

### <a name="proxy-server-use"></a>Użycie serwera proxy

Jeśli używasz serwera proxy do komunikacji między Azure Automation i maszynami z uruchomionym agentem usługi Log Analytics, upewnij się, że odpowiednie zasoby są dostępne. Limit czasu dla żądań z hybrydowego procesu roboczego runbook i usług Automation wynosi 30 sekund. Po trzech próbach żądanie kończy się niepowodzeniem.

### <a name="firewall-use"></a>Użycie zapory

Jeśli używasz zapory do ograniczania dostępu do Internetu, musisz skonfigurować zaporę tak, aby zezwalała na dostęp. Jeśli używasz bramy usługi Log Analytics jako serwera proxy, upewnij się, że jest ona skonfigurowana dla hybrydowych śledzenia runbook. Zobacz [Konfigurowanie bramy usługi Log Analytics dla hybrydowych procesów pracy runbook usługi Automation.](../azure-monitor/agents/gateway.md)

### <a name="service-tags"></a>Tagi usługi

Azure Automation obsługuje tagi usługi sieci wirtualnej platformy Azure, począwszy od tagu usługi [GuestAndHybridManagement.](../virtual-network/service-tags-overview.md) Tagi usług mogą być używane do definiowania kontroli dostępu do sieci w [sieciowych](../virtual-network/network-security-groups-overview.md#security-rules) grupach zabezpieczeń [lub Azure Firewall](../firewall/service-tags.md). Tagi usługi mogą być używane w miejsce określonych adresów IP podczas tworzenia reguł zabezpieczeń. Określając nazwę tagu usługi **GuestAndHybridManagement**  w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla usługi Automation lub go odrzucić. Ten tag usługi nie obsługuje zezwalania na bardziej szczegółową kontrolę przez ograniczenie zakresów adresów IP do określonego regionu.

Tag usługi dla usługi Azure Automation udostępnia tylko ip używane w następujących scenariuszach:

* Wyzwalanie webhook z poziomu sieci wirtualnej
* Zezwalaj hybrydowym procesom State Configuration runbook lub agentom w sieci wirtualnej na komunikowanie się z usługą Automation

>[!NOTE]
>Tag usługi **GuestAndHybridManagement** obecnie nie obsługuje wykonywania zadania runbook w piaskownicy platformy Azure, tylko bezpośrednio w hybrydowym procesu roboczego runbook.

## <a name="support-for-impact-level-5-il5"></a>Obsługa poziomu wpływu 5 (IL5)

Azure Automation hybrydowy proces roboczy runbook może być używany w programie Azure Government do obsługi obciążeń Impact Level 5 w jednej z następujących dwóch konfiguracji:

* [Izolowana maszyna wirtualna.](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines) Po wdrożeniu zużywają one cały host fizyczny dla tej maszyny, zapewniając wymagany poziom izolacji wymagany do obsługi obciążeń IL5.

* [Dedykowane hosty platformy Azure](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), które zapewniają serwery fizyczne, które mogą hostować co najmniej jedną maszynę wirtualną dedykowaną dla jednej subskrypcji platformy Azure.

>[!NOTE]
>Izolacja obliczeniowa za pośrednictwem roli hybrydowego procesu roboczego elementu Runbook jest dostępna dla chmur platformy Azure w wersji komercyjnej i dla instytucji rządowych USA.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Update Management adresów dla hybrydowego procesu roboczego runbook

Oprócz standardowych adresów i portów wymaganych dla hybrydowego procesu roboczego runbook program Update Management ma inne wymagania dotyczące konfiguracji sieci opisane w [sekcji dotyczącej planowania](./update-management/overview.md#ports) sieci.

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation State Configuration hybrydowego procesu roboczego runbook

Możesz uruchomić [Azure Automation State Configuration](automation-dsc-overview.md) hybrydowym procesem roboczym runbook. Aby zarządzać konfiguracją serwerów, które obsługują hybrydowy proces roboczy runbook, należy dodać serwery jako węzły DSC. Zobacz [Enable machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Limity procesów roboczych runbook

Maksymalna liczba grup hybrydowych procesów roboczych na konto usługi Automation wynosi 4000 i ma zastosowanie do obu grup procesów roboczych & hybrydowych użytkowników. Jeśli masz więcej niż 4000 maszyn do zarządzania, zalecamy utworzenie kolejnego konta usługi Automation.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbook w hybrydowym procesu roboczego runbook

Mogą to być zasoby, które zarządzają zasobami na komputerze lokalnym lub uruchamiane względem zasobów w środowisku lokalnym, w którym wdrożono hybrydowy proces roboczy runbook użytkownika. W takim przypadku można uruchomić swoje element runbook w hybrydowym procesie roboczy, a nie na koncie usługi Automation. Funkcje Runbook uruchamiane w hybrydowym procesie roboczy runbook są identyczne ze strukturą, jak te uruchamiane na koncie usługi Automation. Zobacz Runbook on a Hybrid Runbook Worker (Uruchamianie [runbook w hybrydowym procesu roboczego runbook).](automation-hrw-run-runbooks.md)

### <a name="hybrid-runbook-worker-jobs"></a>Zadania hybrydowego procesu roboczego runbook

Zadania hybrydowego procesu roboczego runbook są uruchamiane na lokalnym koncie **systemowym** w systemie Windows lub [na koncie nxautomation w](automation-runbook-execution.md#log-analytics-agent-for-linux) systemie Linux. Azure Automation zadania dotyczące hybrydowych jść runbook inaczej niż zadania uruchamiane w piaskownicach platformy Azure. Zobacz [Środowisko wykonywania runbook](automation-runbook-execution.md#runbook-execution-environment).

Jeśli maszyna hosta hybrydowego procesu roboczego runbook zostanie ponownie uruchomiona, wszystkie uruchomione zadania runbook będą uruchamiane ponownie od początku lub od ostatniego punktu kontrolnego dla podręczników przepływu pracy programu PowerShell. Po ponad trzykrotnym ponownym uruchomieniu zadania runbook zostaje ono wstrzymane.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Uprawnienia dla hybrydowego procesu roboczego runbook

Ponieważ mają dostęp do zasobów spoza platformy Azure, w przypadku hybrydowych procesów roboczych runbook użytkownika nie można używać mechanizmu uwierzytelniania zwykle używanego przez uwierzytelnianie w zasobach platformy Azure przez te zasoby. Runbook zapewnia własne uwierzytelnianie do zasobów lokalnych lub konfiguruje uwierzytelnianie przy użyciu tożsamości [zarządzanych dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) Można również określić konto Uruchom jako, aby udostępnić kontekst użytkownika dla wszystkich runbook.

## <a name="view-system-hybrid-runbook-workers"></a>Wyświetlanie systemowych hybrydowych pracowników runbook

Po włączeniu Update Management na maszynach z systemem Windows lub Linux można utworzyć spis grupy hybrydowych pracowników elementu Runbook systemu w Azure Portal. W portalu można wyświetlić maksymalnie 2000 procesów pracy, wybierając kartę System **hybrid workers group (Systemowa** grupa hybrydowych procesów pracy) z opcji Hybrid workers **group** (Grupa hybrydowych procesów pracy) w okienku po lewej stronie dla wybranego konta usługi Automation.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Strona grup hybrydowych procesów roboczych systemu konta usługi Automation" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Jeśli masz więcej niż 2000 hybrydowych pracowników, aby uzyskać listę wszystkich z nich, możesz uruchomić następujący skrypt programu PowerShell:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować swoje runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz Runbook on a Hybrid Runbook Worker (Uruchamianie runbook w hybrydowym procesu [roboczego runbook).](automation-hrw-run-runbooks.md)

* Aby dowiedzieć się, jak rozwiązywać problemy z hybrydowymi procesami roboczymi runbook, zobacz Rozwiązywanie problemów z hybrydowymi [procesami roboczymi runbook.](troubleshoot/hybrid-runbook-worker.md#general)
