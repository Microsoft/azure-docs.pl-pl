---
title: Azure Automation przegląd hybrydowego procesu roboczego elementu Runbook
description: Ten artykuł zawiera omówienie hybrydowego procesu roboczego elementu Runbook, którego można użyć do uruchamiania elementów Runbook na maszynach w lokalnym centrum danych lub dostawcy chmury.
services: automation
ms.subservice: process-automation
ms.date: 06/22/2020
ms.topic: conceptual
ms.openlocfilehash: bad64d030f3a5fd6c32ab82702ecd861fe4049a4
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206839"
---
# <a name="hybrid-runbook-worker-overview"></a>Omówienie hybrydowych procesów roboczych elementów Runbook

Elementy Runbook w programie Azure Automation mogą nie mieć dostępu do zasobów w innych chmurach lub w środowisku lokalnym, ponieważ działają na platformie Azure w chmurze. Za pomocą funkcji hybrydowego procesu roboczego elementu Runbook programu Azure Automation można uruchamiać elementy Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Elementy Runbook są przechowywane i zarządzane w Azure Automation a następnie dostarczane do co najmniej jednego przypisanego komputera.

Na poniższej ilustracji przedstawiono tę funkcję:

![Omówienie hybrydowych procesów roboczych elementów Runbook](media/automation-hybrid-runbook-worker/automation.png)

Hybrydowy proces roboczy elementu Runbook może uruchamiać system operacyjny Windows lub Linux. Do monitorowania wymaga użycia Azure Monitor i agenta Log Analytics dla obsługiwanego systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Azure monitor](automation-runbook-execution.md#azure-monitor).

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

Zalecaną metodą instalacji jest użycie Azure Automation elementu Runbook w celu całkowitego zautomatyzowania procesu konfigurowania komputera z systemem Windows. Druga metoda polega na przekroczeniu procedury krok po kroku, aby ręcznie zainstalować i skonfigurować rolę. W przypadku maszyn z systemem Linux należy uruchomić skrypt języka Python w celu zainstalowania agenta na komputerze.

## <a name="network-planning"></a><a name="network-planning"></a>Planowanie sieci

Aby hybrydowy proces roboczy elementu Runbook mógł nawiązać połączenie i zarejestrować się w Azure Automation, musi mieć dostęp do numeru portu i adresów URL opisanych w tej sekcji. Proces roboczy musi mieć również dostęp do [portów i adresów URL wymaganych przez agenta log Analytics](../azure-monitor/platform/agent-windows.md) , aby połączyć się z obszarem roboczym Azure Monitor Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Następujący port i adresy URL są wymagane dla hybrydowego procesu roboczego elementu Runbook:

* Port: tylko protokół TCP 443 wymagany do wychodzącego dostępu do Internetu
* Globalny adres URL:`*.azure-automation.net`
* Globalny adres URL US Gov Wirginia:`*.azure-automation.us`
* Usługa agenta:`https://<workspaceId>.agentsvc.azure-automation.net`

Jeśli masz konto usługi Automation zdefiniowane dla określonego regionu, możesz ograniczyć komunikację hybrydowego procesu roboczego elementu Runbook do tego regionalnego centrum danych. Przejrzyj [rekordy DNS używane przez Azure Automation](how-to/automation-region-dns-records.md) dla wymaganych rekordów DNS.

### <a name="proxy-server-use"></a>Użycie serwera proxy

Jeśli serwer proxy jest używany do komunikacji między Azure Automation i komputerami z agentem Log Analytics, należy się upewnić, że dostępne są odpowiednie zasoby. Limit czasu dla żądań od hybrydowego procesu roboczego elementu Runbook i usług Automation wynosi 30 sekund. Po trzech próbach żądanie zakończy się niepowodzeniem.

### <a name="firewall-use"></a>Użycie zapory

W przypadku używania zapory w celu ograniczenia dostępu do Internetu należy skonfigurować zaporę tak, aby zezwalała na dostęp. Jeśli jest używana brama Log Analytics jako serwer proxy, upewnij się, że jest ona skonfigurowana dla hybrydowych procesów roboczych elementów Runbook. Zobacz [Konfigurowanie bramy log Analytics dla hybrydowych procesów roboczych usługi Automation](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Update Management w hybrydowym procesie roboczym elementu Runbook

Po włączeniu [Update Management](automation-update-management.md) Azure Automation, każdy komputer połączony z obszarem roboczym log Analytics zostanie automatycznie skonfigurowany jako hybrydowy proces roboczy elementu Runbook. Każdy proces roboczy może obsługiwać elementy Runbook, których dotyczą usługi zarządzania aktualizacjami.

Komputer skonfigurowany w ten sposób nie jest zarejestrowany w przypadku żadnych grup hybrydowych procesów roboczych elementów Runbook zdefiniowanych już na koncie usługi Automation. Możesz dodać komputer do grupy hybrydowych procesów roboczych elementu Runbook, ale musisz użyć tego samego konta zarówno dla Update Management, jak i dla członkostwa w grupie hybrydowych procesów roboczych elementu Runbook. Ta funkcja została dodana do wersji 7.2.12024.0 hybrydowego procesu roboczego elementu Runbook.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Adresy Update Management dla hybrydowego procesu roboczego elementu Runbook

Na początku standardowych adresów i portów wymaganych przez hybrydowy proces roboczy elementu Runbook, Update Management potrzebują adresów w następnej tabeli. Komunikacja z tymi adresami odbywa się przy użyciu portu 443.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`     | `*.ods.opinsights.azure.us`         |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation konfiguracja stanu w hybrydowym procesie roboczym elementu Runbook

[Konfigurację stanu Azure Automation](automation-dsc-overview.md) można uruchomić w hybrydowym procesie roboczym elementu Runbook. Aby zarządzać konfiguracją serwerów, które obsługują hybrydowy proces roboczy elementu Runbook, należy dodać serwery jako węzły DSC. Zobacz temat [Włączanie maszyn do zarządzania przez Azure Automation konfigurację stanu](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Elementy Runbook w hybrydowym procesie roboczym elementu Runbook

Mogą istnieć elementy Runbook, które zarządzają zasobami na komputerze lokalnym lub działają względem zasobów w środowisku lokalnym, w którym wdrożono hybrydowy proces roboczy elementu Runbook. W takim przypadku można wybrać uruchamianie elementów Runbook w hybrydowym procesie roboczym, a nie na koncie usługi Automation. Elementy Runbook działające w hybrydowym procesie roboczym elementu Runbook są identyczne w strukturze z tymi, które są uruchamiane na koncie usługi Automation. Zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Zadania hybrydowego procesu roboczego elementu Runbook

Zadania hybrydowego procesu roboczego elementu Runbook są uruchamiane w ramach lokalnego konta **systemowego** w systemie Windows lub [konta Nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) w systemie Linux. Azure Automation obsługuje zadania dla hybrydowych procesów roboczych elementu Runbook nieco inaczej niż zadania uruchamiane w piaskownicach platformy Azure. Zobacz [środowisko wykonywania elementu Runbook](automation-runbook-execution.md#runbook-execution-environment).

Jeśli maszyna hosta hybrydowego procesu roboczego elementu Runbook zostanie ponownie uruchomiona, wszystkie uruchomione zadania elementu Runbook są uruchamiane od początku lub z ostatniego punktu kontrolnego dla elementów Runbook przepływu pracy programu PowerShell. Po ponownym uruchomieniu zadania elementu Runbook więcej niż trzy razy zostanie ono zawieszone.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Uprawnienia elementu Runbook dla hybrydowego procesu roboczego elementu Runbook

Ponieważ uzyskują dostęp do zasobów nienależących do platformy Azure, elementy Runbook działające w hybrydowym procesie roboczym elementu Runbook nie mogą korzystać z mechanizmu uwierzytelniania zwykle używanego przez elementy Runbook uwierzytelniane w zasobach platformy Azure. Element Runbook udostępnia własne uwierzytelnianie do zasobów lokalnych lub konfiguruje uwierzytelnianie przy użyciu [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Możesz również określić konto Uruchom jako, aby zapewnić kontekst użytkownika dla wszystkich elementów Runbook.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak skonfigurować elementy Runbook do automatyzowania procesów w lokalnym centrum danych lub w innym środowisku chmury, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md).
* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów z hybrydowym procesem roboczym](troubleshoot/hybrid-runbook-worker.md#general)