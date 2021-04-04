---
title: Azure Automation często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724276"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation często zadawane pytania

Ta firma Microsoft — często zadawane pytania dotyczące Azure Automation. Jeśli masz jakieś pytania dotyczące jego możliwości, przejdź na forum dyskusyjne i Opublikuj swoje pytania. Gdy pytanie jest często zadawane, dodamy je do tego artykułu, aby szybko i łatwo je znaleźć.

## <a name="update-management"></a>Zarządzanie aktualizacjami

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Czy mogę uniknąć nieoczekiwanych uaktualnień na poziomie systemu operacyjnego?

W przypadku niektórych odmian systemu Linux, takich jak Red Hat Enterprise Linux, uaktualnienia na poziomie systemu operacyjnego mogą odbywać się za poorednictwem pakietów. Może to prowadzić do Update Management uruchomień, w których zostanie zmieniony numer wersji systemu operacyjnego. Ponieważ Update Management używa tych samych metod do aktualizacji pakietów, których administrator używa lokalnie na komputerze z systemem Linux, to zachowanie jest zamierzone.

Aby uniknąć aktualizacji wersji systemu operacyjnego za pomocą wdrożeń Update Management, użyj funkcji **wykluczania** .

W Red Hat Enterprise Linux nazwa pakietu do wykluczenia to `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Dlaczego aktualizacje krytyczne/zabezpieczenia są stosowane?

Podczas wdrażania aktualizacji na komputerze z systemem Linux można wybrać klasyfikacje aktualizacji. Ta opcja umożliwia filtrowanie aktualizacji spełniających określone kryteria. Ten filtr jest stosowany lokalnie na komputerze podczas wdrażania aktualizacji.

Ponieważ Update Management wykonuje wzbogacanie aktualizacji w chmurze, można oflagować niektóre aktualizacje w Update Management jako mające wpływ na bezpieczeństwo, nawet jeśli maszyna lokalna nie ma tych informacji. W przypadku zastosowania aktualizacji krytycznych do maszyny z systemem Linux mogą wystąpić aktualizacje, które nie są oznaczone jako mające wpływ na zabezpieczenia na tym komputerze i w związku z tym nie są stosowane. Jednak Update Management może nadal zgłosić ten komputer jako niezgodny, ponieważ zawiera on dodatkowe informacje na temat odpowiedniej aktualizacji.

Wdrażanie aktualizacji według klasyfikacji aktualizacji nie działa w wersjach RTM CentOS. Aby poprawnie wdrożyć aktualizacje dla programu CentOS, wybierz pozycję Wszystkie klasyfikacje, aby upewnić się, że aktualizacje są stosowane. W przypadku opcji SUSE wybierz tylko **inne aktualizacje** , ponieważ klasyfikacja może instalować niektóre inne aktualizacje zabezpieczeń, jeśli są one powiązane z użyciu narzędzia zypper (Menedżer pakietów) lub jej zależności są wymagane jako pierwsze. To zachowanie jest ograniczeniem użyciu narzędzia zypper. W niektórych przypadkach może być konieczne ponowne uruchomienie wdrożenia aktualizacji, a następnie zweryfikowanie wdrożenia za pomocą dziennika aktualizacji.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Czy można wdrażać aktualizacje w ramach dzierżawców platformy Azure?

Jeśli masz maszyny wymagające zastosowania poprawek w innej usłudze raportowania dzierżawy platformy Azure do Update Management, musisz użyć następującego obejścia, aby je zaplanować. Można użyć polecenia cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) z `ForUpdateConfiguration` parametrem określonym do utworzenia harmonogramu. Można użyć polecenia cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) i przekazać maszyny w innej dzierżawie do `NonAzureComputer` parametru. W przykładzie poniżej pokazano, jak to zrobić.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Automatyzacja procesów — elementy Runbook w języku Python

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Która wersja języka Python 3 jest obsługiwana w Azure Automation?

W przypadku zadań w chmurze jest obsługiwany język Python 3,8. Skrypty i pakiety z dowolnej wersji 3. x mogą funkcjonować, jeśli kod jest zgodny w różnych wersjach.

W przypadku zadań hybrydowych dla hybrydowych procesów roboczych elementów Runbook systemu Windows można zainstalować dowolną wersję 3. x, której chcesz użyć. W przypadku zadań hybrydowych dla hybrydowych procesów roboczych systemu Linux na maszynie zainstalowano system Python 3 w celu uruchomienia OMSConfig DSC i hybrydowego procesu roboczego systemu Linux. Zalecamy zainstalowanie wersji 3,6; jednak różne wersje powinny również funkcjonować w przypadku braku istotnych zmian w sygnaturach metod lub kontraktach między wersjami języka Python 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Czy elementy Runbook języka Python 2 i Python 3 są uruchomione na tym samym koncie usługi Automation?

Tak. nie ma ograniczeń dotyczących używania elementów Runbook języka Python 2 i Python 3 w tym samym koncie usługi Automation.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Jaki jest plan migracji istniejących elementów Runbook i pakietów języka Python 2 do języka Python 3?

Azure Automation nie planuje migracji elementów Runbook i pakietów języka Python 2 do języka Python 3. Trzeba będzie wykonać tę migrację samodzielnie. Istniejące i nowe elementy Runbook i pakiety języka Python 2 będą nadal działały.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Jakie pakiety są obsługiwane domyślnie w środowisku Python 3?

Pakiet Azure Package 4.0.0 jest instalowany domyślnie w środowisku automatyzacji języka Python 3. Możesz ręcznie zaimportować nowszą wersję pakietu platformy Azure, aby zastąpić wersję domyślną.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Co zrobić, jeśli uruchamiam element Runbook języka Python 3, który odwołuje się do pakietu Python 2 lub vice versa?

Języki Python 2 i Python 3 mają różne środowiska wykonawcze. Gdy element Runbook języka Python 2 jest uruchomiony, można importować tylko pakiety Python 2 i podobne do języka Python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Jak mogę rozróżniania elementów Runbook i pakietów języka Python 2 i Python 3?

Python 3 to nowa definicja elementu Runbook, która odróżnia elementy Runbook w języku Python 2 i Python 3. Podobnie dla pakietów języka Python 3 wprowadzono inny rodzaj pakietu.

## <a name="next-steps"></a>Następne kroki

Jeśli na pytanie nie ma odpowiedzi, możesz zapoznać się z poniższymi źródłami, aby uzyskać więcej pytań i odpowiedzi.

- [Azure Automation](/answers/topics/azure-automation.html)
- [Forum z opiniami](https://feedback.azure.com/forums/905242-update-management)
