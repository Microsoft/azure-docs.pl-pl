---
title: Często zadawane pytania dotyczące usługi Azure Automation | Dokumenty firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405960"
---
# <a name="azure-automation-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi Azure Automation

To często zadawane pytania firmy Microsoft to lista często zadawanych pytań dotyczących usługi Azure Automation. Jeśli masz jakieś dodatkowe pytania dotyczące jego możliwości, wejdź na forum dyskusyjne i opublikuj swoje pytania. Gdy pytanie jest często zadawane, dodajemy je do tego artykułu, aby można je było szybko i łatwo znaleźć.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="update-management-solution"></a>Update Management solution (Rozwiązanie Update Management)

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Czy mogę zapobiec nieoczekiwanym uaktualnieniom na poziomie systemu operacyjnego?

W przypadku niektórych wariantów systemu Linux, takich jak Red Hat Enterprise Linux, uaktualnienia na poziomie systemu operacyjnego mogą występować za pośrednictwem pakietów. Może to prowadzić do zarządzania aktualizacjami, w którym zmienia się numer wersji systemu operacyjnego. Ponieważ usługa Zarządzanie aktualizacjami używa tych samych metod do aktualizowania pakietów używanych lokalnie przez administratora na komputerze z systemem Linux, to zachowanie jest zamierzone.

Aby uniknąć aktualizowania wersji systemu operacyjnego za pośrednictwem wdrożeń zarządzania aktualizacjami, użyj funkcji **wykluczeń.**

W red hat enterprise linux, nazwa `redhat-release-server.x86_64`pakietu do wykluczenia jest .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Dlaczego nie są stosowane krytyczne/aktualizacje zabezpieczeń?

Podczas wdrażania aktualizacji na komputerze z systemem Linux można wybrać klasyfikacje aktualizacji. Ta opcja filtruje aktualizacje, które spełniają określone kryteria. Ten filtr jest stosowany lokalnie na komputerze po wdrożeniu aktualizacji.

Ponieważ usługa Zarządzanie aktualizacjami wykonuje wzbogacanie aktualizacji w chmurze, niektóre aktualizacje w bezpieczeństwie aktualizacji można oznaczyć jako mające wpływ na zabezpieczenia, nawet jeśli komputer lokalny nie ma tych informacji. Jeśli zastosujesz aktualizacje krytyczne do komputera z systemem Linux, mogą istnieć aktualizacje, które nie są oznaczone jako mające wpływ na bezpieczeństwo na tym komputerze i dlatego nie są stosowane. Jednak zarządzanie aktualizacjami może nadal zgłaszać ten komputer jako niezgodny, ponieważ zawiera dodatkowe informacje o odpowiedniej aktualizacji.

Wdrażanie aktualizacji według klasyfikacji aktualizacji nie działa w wersjach RTM centos. Aby poprawnie wdrożyć aktualizacje dla systemu CentOS, wybierz wszystkie klasyfikacje, aby upewnić się, że aktualizacje są stosowane. W przypadku funkcji SUSE wybranie opcji TYLKO **Inne aktualizacje** jako klasyfikacja może spowodować zainstalowanie dodatkowych aktualizacji zabezpieczeń, jeśli najpierw wymagane są aktualizacje zabezpieczeń związane z zamkiem błyskawicznym (menedżerem pakietów) lub jego zależnościami. To zachowanie jest ograniczeniem zypper. W niektórych przypadkach może być wymagane ponowne uruchomienie wdrożenia aktualizacji, a następnie zweryfikować wdrożenie za pomocą dziennika aktualizacji.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Czy mogę wdrażać aktualizacje w dzierżawach platformy Azure?

Jeśli masz maszyny, które wymagają poprawek w innym raportowania dzierżawy platformy Azure do zarządzania aktualizacjami, należy użyć następującego obejścia, aby je zaplanować. Można użyć polecenia cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) z parametrem `ForUpdateConfiguration` określonym do utworzenia harmonogramu. Można użyć polecenia cmdlet [konfiguracji New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) i przekazać `NonAzureComputer` maszyny w innej dzierżawie do parametru. W przykładzie poniżej pokazano, jak to zrobić.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Następne kroki

Jeśli na twoje pytanie nie ma odpowiedzi, możesz zapoznać się z poniższymi źródłami, aby uzyskać dodatkowe pytania i odpowiedzi.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Forum z opiniami](https://feedback.azure.com/forums/905242-update-management)
