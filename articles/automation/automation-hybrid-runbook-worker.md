---
title: Azure Automation przegląd hybrydowego procesu roboczego elementu Runbook
description: Ten artykuł zawiera omówienie hybrydowego procesu roboczego elementu Runbook, którego można użyć do uruchamiania elementów Runbook na maszynach w lokalnym centrum danych lub dostawcy chmury.
services: automation
ms.subservice: process-automation
ms.date: 09/14/2020
ms.topic: conceptual
ms.openlocfilehash: f5dc9305df8ce0e26e13738d605849fa75cc53a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087896"
---
# <a name="hybrid-runbook-worker-overview"></a>Omówienie hybrydowych procesów roboczych elementów Runbook

Elementy Runbook w programie Azure Automation mogą nie mieć dostępu do zasobów w innych chmurach lub w środowisku lokalnym, ponieważ działają na platformie Azure w chmurze. Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Elementy Runbook są przechowywane i zarządzane w Azure Automation a następnie dostarczane do co najmniej jednej przypisanej maszyny.

Na poniższej ilustracji przedstawiono tę funkcję:

![Omówienie hybrydowych procesów roboczych elementów Runbook](media/automation-hybrid-runbook-worker/automation.png)

Hybrydowy proces roboczy elementu Runbook można uruchomić w systemie operacyjnym Windows lub Linux. Jest to zależne od [log Analytics raportowania agenta](../azure-monitor/platform/log-analytics-agent.md) do [obszaru roboczego Azure Monitor Log Analytics](../azure-monitor/platform/design-logs-deployment.md). Obszar roboczy nie tylko monitoruje maszynę pod kątem obsługiwanego systemu operacyjnego, ale również pobiera składniki wymagane dla hybrydowego procesu roboczego elementu Runbook.

Każdy hybrydowy proces roboczy elementu Runbook jest członkiem grupy hybrydowych procesów roboczych elementu Runbook, która została określona podczas instalacji agenta. Grupa może zawierać jednego agenta, ale w celu zapewnienia wysokiej dostępności można zainstalować wielu agentów w grupie. Każdy komputer może obsługiwać jedno zgłoszenie hybrydowego procesu roboczego na jedno konto usługi Automation.

Po uruchomieniu elementu Runbook w hybrydowym procesie roboczym elementu Runbook należy określić grupę, w której ma ona działać. Każdy proces roboczy w grupie sonduje Azure Automation, aby sprawdzić, czy jakieś zadania są dostępne. Jeśli zadanie jest dostępne, pierwszy proces roboczy, aby pobrać to zadanie. Czas przetwarzania kolejki zadań zależy od profilu sprzętu hybrydowego procesu roboczego i obciążenia. Nie można określić określonego pracownika.

Użyj hybrydowego procesu roboczego elementu Runbook zamiast [piaskownicy platformy Azure](automation-runbook-execution.md#runbook-execution-environment) , ponieważ nie ma wiele [limitów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) piaskownicy dotyczących miejsca na dysku, pamięci lub gniazd sieciowych. Limity dla hybrydowego procesu roboczego są powiązane tylko z zasobami pracownika.

> [!NOTE]
> Hybrydowe procesy robocze elementów Runbook nie są ograniczone przez ograniczony czas [udostępniania](automation-runbook-execution.md#fair-share) dla piaskownic platformy Azure.

## <a name="hybrid-runbook-worker-installation"></a>Instalacja hybrydowego procesu roboczego elementu Runbook

Proces instalacji hybrydowego procesu roboczego elementu Runbook zależy od systemu operacyjnego. W poniższej tabeli zdefiniowano typy wdrożeń.

|System operacyjny  |Typy wdrożeń  |
|---------|---------|
|Windows     | [Automatyczny](automation-windows-hrw-install.md#automated-deployment)<br>[Ręczne](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Zalecaną metodą instalacji jest użycie Azure Automation elementu Runbook w celu całkowitego zautomatyzowania procesu konfigurowania maszyny z systemem Windows. Jeśli to nie jest możliwe, można wykonać procedurę krok po kroku, aby ręcznie zainstalować i skonfigurować rolę. W przypadku maszyn z systemem Linux należy uruchomić skrypt języka Python w celu zainstalowania agenta na komputerze.

## <a name="network-planning"></a><a name="network-planning"></a>Planowanie sieci

Aby hybrydowy proces roboczy elementu Runbook mógł nawiązać połączenie i zarejestrować się w Azure Automation, musi mieć dostęp do numeru portu i adresów URL opisanych w tej sekcji. Proces roboczy musi mieć również dostęp do [portów i adresów URL wymaganych przez agenta log Analytics](../azure-monitor/platform/agent-windows.md) , aby połączyć się z obszarem roboczym Azure Monitor Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Następujący port i adresy URL są wymagane dla hybrydowego procesu roboczego elementu Runbook:

* Port: tylko protokół TCP 443 wymagany do wychodzącego dostępu do Internetu
* Globalny adres URL: `*.azure-automation.net`
* Globalny adres URL US Gov Wirginia: `*.azure-automation.us`
* Usługa agenta: `https://<workspaceId>.agentsvc.azure-automation.net`

Jeśli masz konto usługi Automation zdefiniowane dla określonego regionu, możesz ograniczyć komunikację hybrydowego procesu roboczego elementu Runbook do tego regionalnego centrum danych. Przejrzyj [rekordy DNS używane przez Azure Automation](how-to/automation-region-dns-records.md) dla wymaganych rekordów DNS.

### <a name="proxy-server-use"></a>Użycie serwera proxy

Jeśli serwer proxy jest używany do komunikacji między Azure Automation i komputerami z agentem Log Analytics, należy się upewnić, że dostępne są odpowiednie zasoby. Limit czasu dla żądań od hybrydowego procesu roboczego elementu Runbook i usług Automation wynosi 30 sekund. Po trzech próbach żądanie zakończy się niepowodzeniem.

### <a name="firewall-use"></a>Użycie zapory

W przypadku używania zapory w celu ograniczenia dostępu do Internetu należy skonfigurować zaporę tak, aby zezwalała na dostęp. Jeśli jest używana brama Log Analytics jako serwer proxy, upewnij się, że jest ona skonfigurowana dla hybrydowych procesów roboczych elementów Runbook. Zobacz [Konfigurowanie bramy log Analytics dla hybrydowych procesów roboczych elementów Runbook usługi Automation](../azure-monitor/platform/gateway.md).

### <a name="service-tags"></a>Tagi usługi

Azure Automation obsługuje Tagi usługi sieci wirtualnej platformy Azure, rozpoczynając od [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)znacznika usługi. Za pomocą tagów usługi można definiować kontrolę dostępu do sieci w [grupach zabezpieczeń sieci](../virtual-network/security-overview.md#security-rules) lub w [zaporze platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń można używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi **GuestAndHybridManagement**  w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla usługi Automation lub go odrzucić. Ten tag usługi nie obsługuje bardziej szczegółowej kontroli przez ograniczenie zakresów adresów IP do określonego regionu.

Tag usługi dla usługi Azure Automation zawiera tylko adresy IP używane w następujących scenariuszach:

* Wyzwalanie elementów webhook z poziomu sieci wirtualnej
* Zezwalaj na komunikację hybrydową procesów roboczych elementów Runbook lub agentów konfiguracji stanu w sieci wirtualnej, aby komunikować się z usługą Automation

>[!NOTE]
>**GuestAndHybridManagement** tag usługi aktualnie nie obsługuje wykonywania zadań elementu Runbook w piaskownicy platformy Azure, tylko bezpośrednio w hybrydowym procesie roboczym elementu Runbook.

## <a name="support-for-impact-level-5-il5"></a>Obsługa poziomu wpływu 5 (IL5)

Azure Automation hybrydowego procesu roboczego elementu Runbook można użyć w Azure Government do obsługi obciążeń poziomu 5 w jednej z następujących dwóch konfiguracji:

* [Izolowana maszyna wirtualna](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Po wdrożeniu korzystają one z całego hosta fizycznego dla tej maszyny wirtualnej, zapewniając wymagany poziom izolacji wymagane do obsługi obciążeń IL5.

* [Dedykowane hosty platformy Azure](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts), które udostępniają serwery fizyczne, które mogą hostować co najmniej jedną maszynę wirtualną, która jest przeznaczona dla jednej subskrypcji platformy Azure.

>[!NOTE]
>Izolacja obliczeniowa za pomocą roli hybrydowego procesu roboczego elementu Runbook jest dostępna dla chmur dla instytucji rządowych platformy Azure. 

## <a name="update-management-on-hybrid-runbook-worker"></a>Update Management w hybrydowym procesie roboczym elementu Runbook

Po włączeniu [Update Management](update-management/update-mgmt-overview.md) Azure Automation, każda maszyna połączona z obszarem roboczym log Analytics zostanie automatycznie skonfigurowana jako hybrydowy proces roboczy elementu Runbook. Każdy proces roboczy może obsługiwać elementy Runbook, których dotyczą usługi zarządzania aktualizacjami.

Komputer skonfigurowany w ten sposób nie jest zarejestrowany dla żadnych grup hybrydowych procesów roboczych elementów Runbook zdefiniowanych już na koncie usługi Automation. Możesz dodać maszynę do grupy hybrydowych procesów roboczych elementu Runbook, ale musisz użyć tego samego konta Update Management dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcja została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Adresy Update Management dla hybrydowego procesu roboczego elementu Runbook

Na podstawie standardowych adresów i portów wymaganych dla hybrydowego procesu roboczego elementu Runbook Update Management ma dodatkowe wymagania dotyczące konfiguracji sieciowej opisane w sekcji [Planowanie sieci](update-management/update-mgmt-overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation konfiguracja stanu w hybrydowym procesie roboczym elementu Runbook

[Konfigurację stanu Azure Automation](automation-dsc-overview.md) można uruchomić w hybrydowym procesie roboczym elementu Runbook. Aby zarządzać konfiguracją serwerów, które obsługują hybrydowy proces roboczy elementu Runbook, należy dodać serwery jako węzły DSC. Zobacz temat [Włączanie maszyn do zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Elementy Runbook w hybrydowym procesie roboczym elementu Runbook

Mogą istnieć elementy Runbook, które zarządzają zasobami na komputerze lokalnym lub działają względem zasobów w środowisku lokalnym, w którym wdrożono hybrydowy proces roboczy elementu Runbook. W takim przypadku można wybrać uruchamianie elementów Runbook w hybrydowym procesie roboczym, a nie na koncie usługi Automation. Elementy Runbook działające w hybrydowym procesie roboczym elementu Runbook są identyczne w strukturze z tymi, które są uruchamiane na koncie usługi Automation. Zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Zadania hybrydowego procesu roboczego elementu Runbook

Zadania hybrydowego procesu roboczego elementu Runbook są uruchamiane w ramach lokalnego konta **systemowego** w systemie Windows lub [konta Nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) w systemie Linux. Azure Automation obsługuje zadania dla hybrydowych procesów roboczych elementu Runbook nieco inaczej niż zadania uruchamiane w piaskownicach platformy Azure. Zobacz [środowisko wykonywania elementu Runbook](automation-runbook-execution.md#runbook-execution-environment).

Jeśli maszyna hosta hybrydowego procesu roboczego elementu Runbook zostanie ponownie uruchomiona, wszystkie uruchomione zadania elementu Runbook są uruchamiane od początku lub z ostatniego punktu kontrolnego dla elementów Runbook przepływu pracy programu PowerShell. Po ponownym uruchomieniu zadania elementu Runbook więcej niż trzy razy zostanie ono zawieszone.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Uprawnienia elementu Runbook dla hybrydowego procesu roboczego elementu Runbook

Ponieważ uzyskują dostęp do zasobów nienależących do platformy Azure, elementy Runbook działające w hybrydowym procesie roboczym elementu Runbook nie mogą korzystać z mechanizmu uwierzytelniania zwykle używanego przez elementy Runbook uwierzytelniane w zasobach platformy Azure. Element Runbook udostępnia własne uwierzytelnianie do zasobów lokalnych lub konfiguruje uwierzytelnianie przy użyciu [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Możesz również określić konto Uruchom jako, aby zapewnić kontekst użytkownika dla wszystkich elementów Runbook.

## <a name="view-hybrid-runbook-workers"></a>Wyświetlanie hybrydowych procesów roboczych elementów Runbook

Po włączeniu funkcji Update Management na serwerach lub maszynach wirtualnych z systemem Windows można sporządzić spis listy grup hybrydowych procesów roboczych elementów Runbook w Azure Portal. Możesz wyświetlić do 2 000 procesów roboczych w portalu, wybierając **grupę hybrydowych procesów roboczych grupy** z opcji **Grupa hybrydowych procesów roboczych** z okienka po lewej stronie dla wybranego konta usługi Automation.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Strona grup hybrydowych procesów roboczych systemu konta usługi Automation" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Jeśli masz więcej niż 2 000 hybrydowych procesów roboczych, aby wyświetlić listę wszystkich z nich, możesz uruchomić następujący skrypt programu PowerShell:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).

* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów z hybrydowym procesem roboczym](troubleshoot/hybrid-runbook-worker.md#general)
