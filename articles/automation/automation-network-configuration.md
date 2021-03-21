---
title: Szczegóły konfiguracji sieci Azure Automation
description: Ten artykuł zawiera szczegółowe informacje dotyczące sieci wymaganych przez Azure Automation konfigurację stanu, Azure Automation hybrydowego procesu roboczego elementu Runbook, Update Management i Change Tracking oraz spisu
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 36331e9c07926d4d3ffff136aefa2f9a77d47cb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708887"
---
# <a name="azure-automation-network-configuration-details"></a>Szczegóły konfiguracji sieci Azure Automation

Ta strona zawiera szczegółowe informacje dotyczące sieci, które są wymagane dla [hybrydowego procesu roboczego elementu Runbook i konfiguracji stanu](#hybrid-runbook-worker-and-state-configuration), a także dla [Update Management i Change Tracking i spisu](#update-management-and-change-tracking-and-inventory).

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Hybrydowy proces roboczy elementu Runbook i konfiguracja stanu

Następujący port i adresy URL są wymagane dla hybrydowego procesu roboczego elementu Runbook i dla [konfiguracji stanu automatyzacji](automation-dsc-overview.md) do komunikowania się z Azure Automation.

* Port: tylko 443 wymagany do wychodzącego dostępu do Internetu
* Globalny adres URL: `*.azure-automation.net`
* Globalny adres URL US Gov Wirginia: `*.azure-automation.us`
* Usługa agenta: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Planowanie sieci dla hybrydowego procesu roboczego elementu Runbook

Dla systemu lub użytkownika hybrydowego procesu roboczego elementu Runbook w celu nawiązania połączenia i zarejestrowania się w Azure Automation musi on mieć dostęp do numeru portu i adresów URL opisanych w tej sekcji. Proces roboczy musi mieć również dostęp do [portów i adresów URL wymaganych przez agenta log Analytics](../azure-monitor/agents/agent-windows.md) do łączenia się z obszarem roboczym Azure Monitor Log Analytics.

Jeśli masz konto usługi Automation zdefiniowane dla określonego regionu, możesz ograniczyć komunikację hybrydowego procesu roboczego elementu Runbook do tego regionalnego centrum danych. Przejrzyj [rekordy DNS używane przez Azure Automation](how-to/automation-region-dns-records.md) dla wymaganych rekordów DNS.

### <a name="configuration-of-private-networks-for-state-configuration"></a>Konfiguracja sieci prywatnych na potrzeby konfiguracji stanu

Jeśli węzły znajdują się w sieci prywatnej, wymagane są porty i adresy URL zdefiniowane powyżej. Te zasoby zapewniają łączność sieciową z zarządzanym węzłem i umożliwiają komunikację DSC z Azure Automation.

Jeśli używasz zasobów DSC komunikujących się między węzłami, takimi jak [WAITFOR * Resources](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), musisz również zezwolić na ruch między węzłami. Zapoznaj się z dokumentacją poszczególnych zasobów DSC, aby poznać te wymagania sieciowe.

Aby zrozumieć wymagania klienta dotyczące protokołu TLS 1,2, zobacz [Wymuszanie protokołu tls 1,2 dla Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Update Management i Change Tracking i spis

Adresy w tej tabeli są wymagane zarówno dla Update Management, jak i dla Change Tracking i spisu. Akapit następujący po tabeli dotyczy także obu tych metod.

Komunikacja z tymi adresami odbywa się przy użyciu **portu 443**.

|Azure — publiczna  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*. ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*. oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*. azure-automation.us|

Podczas tworzenia reguł zabezpieczeń grupy sieciowej lub konfigurowania zapory platformy Azure w celu zezwalania na ruch do usługi Automation i obszaru roboczego Log Analytics Użyj [tagów usług](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** i **AzureMonitor**. Upraszcza to ciągłe zarządzanie regułami zabezpieczeń sieci. Aby bezpiecznie i prywatnie połączyć się z usługą Automation z maszyn wirtualnych platformy Azure, zapoznaj się z tematem [Korzystanie z prywatnego linku platformy Azure](./how-to/private-link-security.md). Aby uzyskać bieżący tag usługi i informacje o zakresie do uwzględnienia w ramach konfiguracji lokalnych zapór, zobacz [pliki JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o usłudze [Automation Update Management Omówienie](update-management\overview.md).
* Dowiedz się więcej o [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md).
* Dowiedz się więcej na temat [Change Tracking i spisu](change-tracking\overview.md).
* Dowiedz się więcej o [konfiguracji stanu automatyzacji](automation-dsc-overview.md).