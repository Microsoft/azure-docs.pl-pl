---
title: Azure Automation przegląd hybrydowego procesu roboczego elementu Runbook
description: Ten artykuł zawiera omówienie hybrydowego procesu roboczego elementu Runbook, którego można użyć do uruchamiania elementów Runbook na maszynach w lokalnym centrum danych lub dostawcy chmury.
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: 7bd9560399a2b2b377cb543a4f829883bcbdf7c8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183774"
---
# <a name="hybrid-runbook-worker-overview"></a>Omówienie hybrydowych procesów roboczych elementów Runbook

Elementy Runbook w programie Azure Automation mogą nie mieć dostępu do zasobów w innych chmurach lub w środowisku lokalnym, ponieważ działają na platformie Azure w chmurze. Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Elementy Runbook są przechowywane i zarządzane w Azure Automation a następnie dostarczane do co najmniej jednej przypisanej maszyny.

Na poniższej ilustracji przedstawiono tę funkcję:

![Omówienie hybrydowych procesów roboczych elementów Runbook](media/automation-hybrid-runbook-worker/automation.png)

Istnieją dwa typy procesów roboczych elementów Runbook — system i użytkownik. W poniższej tabeli opisano różnice między nimi.

|Typ | Opis |
|-----|-------------|
|**System** |Obsługuje zestaw ukrytych elementów Runbook używanych przez funkcję Update Management, które są przeznaczone do instalowania aktualizacji określonych przez użytkownika na maszynach z systemem Windows i Linux.<br> Ten typ hybrydowego procesu roboczego elementu Runbook nie jest członkiem grupy hybrydowych procesów roboczych elementu Runbook, dlatego nie uruchamia elementów Runbook przeznaczonych dla grupy procesów roboczych elementu Runbook. |
|**Użytkownik** |Program obsługuje zdefiniowane przez użytkownika elementy Runbook przeznaczone do uruchomienia bezpośrednio na komputerze z systemem Windows i Linux, które są członkami co najmniej jednej grupy procesów roboczych elementów Runbook. |

Hybrydowy proces roboczy elementu Runbook można uruchomić w systemie operacyjnym Windows lub Linux, a ta rola opiera się na [log Analytics raportowania agenta](../azure-monitor/platform/log-analytics-agent.md) do Azure monitor [obszaru roboczego log Analytics](../azure-monitor/platform/design-logs-deployment.md). Obszar roboczy nie tylko monitoruje maszynę pod kątem obsługiwanego systemu operacyjnego, ale również pobiera składniki wymagane do zainstalowania hybrydowego procesu roboczego elementu Runbook.

Po włączeniu [Update Management](./update-management/overview.md) Azure Automation, każda maszyna połączona z obszarem roboczym log Analytics zostanie automatycznie skonfigurowana jako hybrydowy proces roboczy elementu Runbook systemu.

Każdy użytkownik hybrydowy proces roboczy elementu Runbook jest członkiem grupy hybrydowych procesów roboczych elementu Runbook, która została określona podczas instalacji procesu roboczego. Grupa może obejmować pojedynczy proces roboczy, ale w celu zapewnienia wysokiej dostępności można dołączyć wielu pracowników do grupy. Każdy komputer może obsługiwać jedno hybrydowe raportowanie procesu roboczego elementu Runbook na jednym koncie usługi Automation; nie można zarejestrować hybrydowego procesu roboczego na wielu kontach usługi Automation. Wynika to z faktu, że hybrydowy proces roboczy może nasłuchiwać tylko zadań z jednego konta usługi Automation. W przypadku maszyn obsługujących systemowy hybrydowy proces roboczy elementu Runbook zarządzany przez Update Management można je dodać do grupy hybrydowych procesów roboczych elementu Runbook. Należy jednak użyć tego samego konta usługi Automation zarówno dla Update Management, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook.

Po uruchomieniu elementu Runbook w hybrydowym procesie roboczym elementu Runbook użytkownika należy określić grupę, w której ma ona działać. Każdy proces roboczy w grupie sonduje Azure Automation, aby sprawdzić, czy jakieś zadania są dostępne. Jeśli zadanie jest dostępne, pierwszy proces roboczy, aby pobrać to zadanie. Czas przetwarzania kolejki zadań zależy od profilu sprzętu hybrydowego procesu roboczego i obciążenia. Nie można określić określonego pracownika. Hybrydowy proces roboczy działa w mechanizmie sondowania (co 30 sekund) i jest zgodny z kolejnością pierwszego użycia. W zależności od tego, kiedy zadanie zostało wypchnięte, niezależnie od tego, czy proces roboczy jest wysyłany przez program, usługa Automation wybiera zadanie. Pojedynczy hybrydowy proces roboczy może ogólnie pobrać cztery zadania na polecenie ping (to znaczy co 30 sekund). Jeśli szybkość wypychania zadań jest większa niż cztery na 30 sekund, istnieje możliwość, że inny hybrydowy proces roboczy w grupie hybrydowych procesów roboczych elementu Runbook wybiorę zadanie.

Aby kontrolować dystrybucję elementów Runbook w hybrydowych procesach roboczych elementu Runbook i kiedy lub jak wyzwalane są zadania, można zarejestrować hybrydowy proces roboczy dla różnych grup hybrydowych procesów roboczych elementu Runbook w ramach konta usługi Automation. Kierowanie zadań do określonych grup lub grup w celu obsługi rozmieszczenia.

Użyj hybrydowego procesu roboczego elementu Runbook zamiast [piaskownicy platformy Azure](automation-runbook-execution.md#runbook-execution-environment) , ponieważ nie ma wiele [limitów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) piaskownicy dotyczących miejsca na dysku, pamięci lub gniazd sieciowych. Limity dla hybrydowego procesu roboczego są powiązane tylko z zasobami pracownika.

> [!NOTE]
> Hybrydowe procesy robocze elementów Runbook nie są ograniczone przez ograniczony czas [udostępniania](automation-runbook-execution.md#fair-share) dla piaskownic platformy Azure.

## <a name="hybrid-runbook-worker-installation"></a>Instalacja hybrydowego procesu roboczego elementu Runbook

Proces instalacji hybrydowego procesu roboczego elementu Runbook użytkownika zależy od systemu operacyjnego. W poniższej tabeli zdefiniowano typy wdrożeń.

|System operacyjny  |Typy wdrożeń  |
|---------|---------|
|Windows     | [Automatyczny](automation-windows-hrw-install.md#automated-deployment)<br>[Ręczne](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Ręczne](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Zalecana metoda instalacji dla komputera z systemem Windows polega na użyciu Azure Automation elementu Runbook w celu całkowitego zautomatyzowania procesu konfigurowania go. Jeśli to nie jest możliwe, można wykonać procedurę krok po kroku, aby ręcznie zainstalować i skonfigurować rolę. W przypadku maszyn z systemem Linux należy uruchomić skrypt języka Python w celu zainstalowania agenta na komputerze.

## <a name="network-planning"></a><a name="network-planning"></a>Planowanie sieci

Dla systemu i użytkownika hybrydowego procesu roboczego elementu Runbook w celu nawiązania połączenia i zarejestrowania się w Azure Automation musi on mieć dostęp do numeru portu i adresów URL opisanych w tej sekcji. Proces roboczy musi mieć również dostęp do [portów i adresów URL wymaganych przez agenta log Analytics](../azure-monitor/platform/agent-windows.md) , aby połączyć się z obszarem roboczym Azure Monitor Log Analytics.

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

Azure Automation obsługuje Tagi usługi sieci wirtualnej platformy Azure, rozpoczynając od [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)znacznika usługi. Za pomocą tagów usługi można definiować kontrolę dostępu do sieci w [grupach zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md#security-rules) lub w [zaporze platformy Azure](../firewall/service-tags.md). Podczas tworzenia reguł zabezpieczeń można używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi **GuestAndHybridManagement**  w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla usługi Automation lub go odrzucić. Ten tag usługi nie obsługuje bardziej szczegółowej kontroli przez ograniczenie zakresów adresów IP do określonego regionu.

Tag usługi dla usługi Azure Automation zawiera tylko adresy IP używane w następujących scenariuszach:

* Wyzwalanie elementów webhook z poziomu sieci wirtualnej
* Zezwalaj na komunikację hybrydową procesów roboczych elementów Runbook lub agentów konfiguracji stanu w sieci wirtualnej, aby komunikować się z usługą Automation

>[!NOTE]
>**GuestAndHybridManagement** tag usługi aktualnie nie obsługuje wykonywania zadań elementu Runbook w piaskownicy platformy Azure, tylko bezpośrednio w hybrydowym procesie roboczym elementu Runbook.

## <a name="support-for-impact-level-5-il5"></a>Obsługa poziomu wpływu 5 (IL5)

Azure Automation hybrydowego procesu roboczego elementu Runbook można użyć w Azure Government do obsługi obciążeń poziomu 5 w jednej z następujących dwóch konfiguracji:

* [Izolowana maszyna wirtualna](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Po wdrożeniu korzystają one z całego hosta fizycznego dla tej maszyny dostarczającej wymagany poziom izolacji wymagane do obsługi obciążeń IL5.

* [Dedykowane hosty platformy Azure](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts), które udostępniają serwery fizyczne, które mogą hostować co najmniej jedną maszynę wirtualną, która jest przeznaczona dla jednej subskrypcji platformy Azure.

>[!NOTE]
>Izolacja obliczeniowa za pomocą roli hybrydowego procesu roboczego elementu Runbook jest dostępna dla chmur dla instytucji rządowych platformy Azure. 

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Adresy Update Management dla hybrydowego procesu roboczego elementu Runbook

Oprócz standardowych adresów i portów wymaganych dla hybrydowego procesu roboczego elementu Runbook, Update Management ma dodatkowe wymagania dotyczące konfiguracji sieciowej opisane w sekcji [Planowanie sieci](./update-management/overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation konfiguracja stanu w hybrydowym procesie roboczym elementu Runbook

[Konfigurację stanu Azure Automation](automation-dsc-overview.md) można uruchomić w hybrydowym procesie roboczym elementu Runbook. Aby zarządzać konfiguracją serwerów, które obsługują hybrydowy proces roboczy elementu Runbook, należy dodać serwery jako węzły DSC. Zobacz temat [Włączanie maszyn do zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Limity procesów roboczych elementu Runbook

Maksymalna liczba grup hybrydowych procesów roboczych na konto usługi Automation wynosi 4000 i ma zastosowanie do obu systemów & użytkownika hybrydowego. Jeśli masz więcej niż 4 000 maszyn do zarządzania, zalecamy utworzenie dodatkowych kont usługi Automation.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Elementy Runbook w hybrydowym procesie roboczym elementu Runbook

Mogą istnieć elementy Runbook, które zarządzają zasobami na komputerze lokalnym lub działają względem zasobów w środowisku lokalnym, w którym wdrożono hybrydowy proces roboczy elementu Runbook użytkownika. W takim przypadku można wybrać uruchamianie elementów Runbook w hybrydowym procesie roboczym, a nie na koncie usługi Automation. Elementy Runbook działające w hybrydowym procesie roboczym elementu Runbook są identyczne w strukturze z tymi, które są uruchamiane na koncie usługi Automation. Zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Zadania hybrydowego procesu roboczego elementu Runbook

Zadania hybrydowego procesu roboczego elementu Runbook są uruchamiane w ramach lokalnego konta **systemowego** w systemie Windows lub [konta Nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) w systemie Linux. Azure Automation obsługuje zadania dla hybrydowych procesów roboczych elementu Runbook inaczej niż zadania uruchamiane w piaskownicach platformy Azure. Zobacz [środowisko wykonywania elementu Runbook](automation-runbook-execution.md#runbook-execution-environment).

Jeśli maszyna hosta hybrydowego procesu roboczego elementu Runbook zostanie ponownie uruchomiona, wszystkie uruchomione zadania elementu Runbook są uruchamiane od początku lub z ostatniego punktu kontrolnego dla elementów Runbook przepływu pracy programu PowerShell. Po ponownym uruchomieniu zadania elementu Runbook więcej niż trzy razy zostanie ono zawieszone.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Uprawnienia elementu Runbook dla hybrydowego procesu roboczego elementu Runbook

Ponieważ uzyskują dostęp do zasobów nienależących do platformy Azure, elementy Runbook działające w hybrydowym procesie roboczym elementu Runbook nie mogą korzystać z mechanizmu uwierzytelniania zwykle używanego przez elementy Runbook uwierzytelniane w zasobach platformy Azure. Element Runbook udostępnia własne uwierzytelnianie do zasobów lokalnych lub konfiguruje uwierzytelnianie przy użyciu [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Możesz również określić konto Uruchom jako, aby zapewnić kontekst użytkownika dla wszystkich elementów Runbook.

## <a name="view-system-hybrid-runbook-workers"></a>Wyświetl hybrydowe procesy robocze elementów Runbook systemu

Po włączeniu funkcji Update Management na komputerach z systemem Windows lub Linux można sporządzić spis listy grup hybrydowych procesów roboczych elementów Runbook w Azure Portal. Możesz wyświetlić do 2 000 procesów roboczych w portalu, wybierając **grupę hybrydowych procesów roboczych grupy** z opcji **Grupa hybrydowych procesów roboczych** z okienka po lewej stronie dla wybranego konta usługi Automation.

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