---
title: Azure Automation często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 76c8d09ef2ef0130ddac856a1f37f8b68d977494
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186235"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation często zadawane pytania

Ta firma Microsoft — często zadawane pytania dotyczące Azure Automation. Jeśli masz dodatkowe pytania dotyczące jego możliwości, przejdź na forum dyskusyjne i Opublikuj swoje pytania. Gdy pytanie jest często zadawane, dodamy je do tego artykułu, aby można je było szybko i łatwo znaleźć.

## <a name="update-management"></a>Zarządzanie aktualizacjami

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Czy mogę uniknąć nieoczekiwanych uaktualnień na poziomie systemu operacyjnego?

W przypadku niektórych odmian systemu Linux, takich jak Red Hat Enterprise Linux, uaktualnienia na poziomie systemu operacyjnego mogą odbywać się za poorednictwem pakietów. Może to prowadzić do Update Management uruchomień, w których zostanie zmieniony numer wersji systemu operacyjnego. Ponieważ Update Management używa tych samych metod do aktualizacji pakietów, których administrator używa lokalnie na komputerze z systemem Linux, to zachowanie jest zamierzone.

Aby uniknąć aktualizacji wersji systemu operacyjnego za pomocą wdrożeń Update Management, użyj funkcji **wykluczania** .

W Red Hat Enterprise Linux nazwa pakietu do wykluczenia to `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Dlaczego aktualizacje krytyczne/zabezpieczenia są stosowane?

Podczas wdrażania aktualizacji na komputerze z systemem Linux można wybrać klasyfikacje aktualizacji. Ta opcja umożliwia filtrowanie aktualizacji spełniających określone kryteria. Ten filtr jest stosowany lokalnie na komputerze podczas wdrażania aktualizacji.

Ponieważ Update Management wykonuje wzbogacanie aktualizacji w chmurze, można oflagować niektóre aktualizacje w Update Management jako mające wpływ na bezpieczeństwo, nawet jeśli maszyna lokalna nie ma tych informacji. W przypadku zastosowania aktualizacji krytycznych do maszyny z systemem Linux mogą wystąpić aktualizacje, które nie są oznaczone jako mające wpływ na zabezpieczenia na tym komputerze i w związku z tym nie są stosowane. Jednak Update Management może nadal zgłosić ten komputer jako niezgodny, ponieważ zawiera on dodatkowe informacje na temat odpowiedniej aktualizacji.

Wdrażanie aktualizacji według klasyfikacji aktualizacji nie działa w wersjach RTM CentOS. Aby poprawnie wdrożyć aktualizacje dla programu CentOS, wybierz pozycję Wszystkie klasyfikacje, aby upewnić się, że aktualizacje są stosowane. W przypadku opcji SUSE wybranie **innych aktualizacji** jako klasyfikacji może spowodować zainstalowanie pewnych dodatkowych aktualizacji zabezpieczeń, jeśli najpierw wymagane są aktualizacje zabezpieczeń powiązane z użyciu narzędzia zypper (Menedżer pakietów) lub jej zależności. To zachowanie jest ograniczeniem użyciu narzędzia zypper. W niektórych przypadkach może być konieczne ponowne uruchomienie wdrożenia aktualizacji, a następnie zweryfikowanie wdrożenia za pomocą dziennika aktualizacji.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Czy można wdrażać aktualizacje w ramach dzierżawców platformy Azure?

Jeśli masz maszyny wymagające zastosowania poprawek w innej usłudze raportowania dzierżawy platformy Azure do Update Management, musisz użyć następującego obejścia, aby je zaplanować. Można użyć polecenia cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) z `ForUpdateConfiguration` parametrem określonym do utworzenia harmonogramu. Można użyć polecenia cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) i przekazać maszyny w innej dzierżawie do `NonAzureComputer` parametru. W przykładzie poniżej pokazano, jak to zrobić.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Następne kroki

Jeśli na pytanie nie ma odpowiedzi, możesz zapoznać się z poniższymi źródłami w celu uzyskania dodatkowych pytań i odpowiedzi.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Forum z opiniami](https://feedback.azure.com/forums/905242-update-management)
