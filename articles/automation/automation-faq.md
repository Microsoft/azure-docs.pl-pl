---
title: Azure Automation — często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ab4ce9b8691f27fe392d2e3099677d45900d2e3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834232"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation często zadawanych pytań

To często zadawane pytania dotyczące firmy Microsoft to lista często zadawanych pytań dotyczących Azure Automation. Jeśli masz inne pytania dotyczące jego możliwości, przejdź do forum dyskusyjnego i opublikuj swoje pytania. Gdy pytanie jest często zadawane, dodajemy je do tego artykułu, aby można je było znaleźć szybko i łatwo.

## <a name="update-management"></a>Zarządzanie aktualizacjami

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Czy mogę zapobiec nieoczekiwanym uaktualnień na poziomie systemu operacyjnego?

W niektórych wariantach systemu Linux, takich Red Hat Enterprise Linux, uaktualnienia na poziomie systemu operacyjnego mogą być dostępne za pośrednictwem pakietów. Może to prowadzić do Update Management, w których numer wersji systemu operacyjnego się zmienia. Ponieważ Update Management używa tych samych metod do aktualizowania pakietów, których administrator używa lokalnie na maszynie z systemem Linux, to zachowanie jest zamierzone.

Aby uniknąć aktualizowania wersji systemu operacyjnego za Update Management wdrożeń, użyj **funkcji wykluczania.**

W Red Hat Enterprise Linux nazwa pakietu do wykluczenia to `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Dlaczego nie zastosowano aktualizacji krytycznych/zabezpieczeń?

Podczas wdrażania aktualizacji na maszynie z systemem Linux można wybrać klasyfikacje aktualizacji. Ta opcja filtruje aktualizacje spełniające określone kryteria. Ten filtr jest stosowany lokalnie na maszynie podczas wdrażania aktualizacji.

Ponieważ Update Management wzbogacenia aktualizacji w chmurze, niektóre aktualizacje w usłudze Update Management można oznaczać jako mające wpływ na zabezpieczenia, nawet jeśli komputer lokalny nie ma tych informacji. Jeśli zastosujemy aktualizacje krytyczne do maszyny z systemem Linux, mogą wystąpić aktualizacje, które nie są oznaczone jako mające wpływ na zabezpieczenia na tej maszynie i w związku z tym nie są stosowane. Jednak Update Management nadal zgłaszać tę maszynę jako niezgodne, ponieważ zawiera ona dodatkowe informacje o odpowiedniej aktualizacji.

Wdrażanie aktualizacji według klasyfikacji aktualizacji nie działa w wersjach RTM systemu CentOS. Aby prawidłowo wdrożyć aktualizacje systemu CentOS, wybierz wszystkie klasyfikacje, aby upewnić się, że aktualizacje zostały zastosowane. W przypadku oprogramowania  SUSE wybranie klasyfikacji TYLKO inne aktualizacje może zainstalować inne aktualizacje zabezpieczeń, jeśli są one związane z programem zypper (menedżerem pakietów) lub jego zależności są wymagane jako pierwsze. To zachowanie jest ograniczeniem zypper. W niektórych przypadkach może być wymagane ponowne wdrożenie aktualizacji, a następnie zweryfikowanie wdrożenia za pomocą dziennika aktualizacji.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Czy mogę wdrażać aktualizacje w dzierżawach platformy Azure?

Jeśli masz maszyny, które wymagają zastosowania poprawek w innym raportowaniu dzierżawy platformy Azure do usługi Update Management, musisz użyć następującego obejścia, aby zaplanować je. Możesz użyć polecenia cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) z parametrem `ForUpdateConfiguration` określonym w celu utworzenia harmonogramu. Możesz użyć polecenia cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) i przekazać maszyny w innej dzierżawie do `NonAzureComputer` parametru . W przykładzie poniżej pokazano, jak to zrobić.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Automatyzacja procesów — python runbook

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Która wersja języka Python 3 jest obsługiwana w Azure Automation?

W przypadku zadań w chmurze obsługiwany jest język Python 3.8. Skrypty i pakiety z dowolnej wersji 3.x mogą działać, jeśli kod jest zgodny w różnych wersjach.

W przypadku zadań hybrydowych dla hybrydowych dni pracy runbook systemu Windows można zainstalować dowolną wersję 3.x, której chcesz użyć. W przypadku zadań hybrydowych w hybrydowych procesach roboczych runbook systemu Linux polegamy na wersji 3 języka Python zainstalowanej na maszynie w celu uruchomienia konfiguracji DSC OMSConfig i hybrydowego procesu roboczego systemu Linux. Zalecamy zainstalowanie wersji 3.6; Jednak różne wersje powinny również działać, jeśli nie ma żadnych zmian w podpisach metod lub kontraktach między wersjami języka Python 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Czy można uruchamiać środowiska Runbook języka Python 2 i Python 3 na tym samym koncie automatyzacji?

Tak, nie ma żadnych ograniczeń w zakresie używania środowisk Runbook języka Python 2 i Python 3 na tym samym koncie automatyzacji.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Jaki jest plan migracji istniejących pakietów i runbook języka Python 2 do środowiska Python 3?

Azure Automation nie planuje migracji pakietów i runbook języka Python 2 do środowiska Python 3. Tę migrację należy przeprowadzić samodzielnie. Istniejące i nowe pakiety runbook i pakiety języka Python 2 będą nadal działać.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Jakie pakiety są obsługiwane domyślnie w środowisku Python 3?

Pakiet platformy Azure 4.0.0 jest instalowany domyślnie w środowisku automatyzacji języka Python 3. Możesz ręcznie zaimportować wyższą wersję pakietu platformy Azure, aby zastąpić wersję domyślną.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Co zrobić, jeśli uruchamiam runbook języka Python 3, który odwołuje się do pakietu języka Python 2 lub odwrotnie?

Środowiska Python 2 i Python 3 mają różne środowiska wykonywania. Gdy jest uruchomiony podręcznik runbook języka Python 2, można importować tylko pakiety języka Python 2 i podobne dla języka Python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Jak mogę między pakietami i pakietami runbook języka Python 2 i Python 3?

Python 3 to nowa definicja runbook, która rozróżnia między językami Python 2 i Python 3. Podobnie wprowadzono inny rodzaj pakietu dla pakietów języka Python 3.

## <a name="next-steps"></a>Następne kroki

Jeśli w tym miejscu nie ma odpowiedzi na Twoje pytanie, możesz odwołać się do następujących źródeł, aby uzyskać więcej pytań i odpowiedzi.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Forum z opiniami](https://feedback.azure.com/forums/905242-update-management)
