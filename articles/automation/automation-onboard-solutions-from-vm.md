---
title: Dołączanie rozwiązań Update Management, Change Tracking i spisu z maszyny wirtualnej platformy Azure
description: Dowiedz się, jak dołączać maszynę wirtualną platformy Azure z rozwiązaniami Update Management, Change Tracking i spisu, które są częścią Azure Automation.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f33f829b6cb86cb01c848e5fc48e1618a3e00a2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537036"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Dołączanie rozwiązań Update Management, Change Tracking i spisu z maszyny wirtualnej platformy Azure

Azure Automation udostępnia rozwiązania ułatwiające zarządzanie aktualizacjami zabezpieczeń systemu operacyjnego, śledzenie zmian i spisy, które są zainstalowane na komputerach. Istnieje wiele sposobów na dołączenie maszyn. Możesz dołączyć rozwiązanie z maszyny wirtualnej, z [konta usługi Automation](automation-onboard-solutions-from-automation-account.md), [przeglądać wiele maszyn](automation-onboard-solutions-from-browse.md)lub przy użyciu [elementu Runbook](automation-onboard-solutions.md). W tym artykule opisano dołączanie tych rozwiązań z maszyny wirtualnej platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="enable-the-solutions"></a>Włącz rozwiązania

Najpierw włącz jedno lub wszystkie trzy rozwiązania na maszynie wirtualnej:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **maszyny wirtualne** lub Wyszukaj i wybierz pozycję **maszyny wirtualne** ze strony głównej.
2. Wybierz maszynę wirtualną, dla której chcesz włączyć rozwiązanie.
3. Na stronie maszyna wirtualna w obszarze **operacje**wybierz pozycję **Zarządzanie aktualizacjami**, **spis**lub **śledzenie zmian**. Maszyna wirtualna może istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation. Podczas dołączania rozwiązania z maszyny wirtualnej należy mieć `Microsoft.OperationalInsights/workspaces/read` uprawnienia do określenia, czy maszyna wirtualna została dołączona do obszaru roboczego. Aby dowiedzieć się więcej o dodatkowych uprawnieniach, które są wymagane, zobacz [uprawnienia wymagane do](automation-role-based-access-control.md#onboarding-permissions)dołączania maszyn. Aby dowiedzieć się, jak dołączyć wiele maszyn jednocześnie, zobacz Dołączanie [rozwiązań Update Management, Change Tracking i spisu](automation-onboard-solutions-from-automation-account.md).

4. Wybierz obszar roboczy platformy Azure Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

![Dołączanie rozwiązania Update Management](media/automation-tutorial-update-management/manageupdates-update-enable.png)

5. Przejdź do innych rozwiązań, a następnie wybierz pozycję **Włącz**. Lista rozwijana Log Analytics obszaru roboczego i konta usługi Automation jest wyłączona, ponieważ te rozwiązania korzystają z tego samego obszaru roboczego i konta usługi Automation jak wcześniej włączone rozwiązanie.

> [!NOTE]
> Śledzenie zmian i spis używają tego samego rozwiązania. Gdy jedno z tych rozwiązań jest włączone, drugi również jest włączony.

## <a name="scope-configuration"></a>Konfiguracja zakresu

Każde rozwiązanie korzysta z konfiguracji zakresu w obszarze roboczym, aby kierować komputery, na których jest uzyskiwane rozwiązanie. Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania, które są używane do ograniczania zakresu rozwiązania do określonych komputerów. Aby uzyskać dostęp do konfiguracji zakresu:

1. Na koncie usługi Automation w obszarze **powiązane zasoby**wybierz pozycję **obszar roboczy**. 
2. W obszarze roboczym w obszarze **źródła danych obszaru roboczego**wybierz pozycję **konfiguracje zakresów**.
3. Jeśli wybrany obszar roboczy nie ma już rozwiązania Update Management lub Change Tracking, zostaną utworzone następujące konfiguracje zakresów:

    * `MicrosoftDefaultScopeConfig-ChangeTracking`
    * `MicrosoftDefaultScopeConfig-Updates`

    Jeśli wybrany obszar roboczy ma już rozwiązanie, rozwiązanie nie zostanie ponownie wdrożone i nie zostanie dodana konfiguracja zakresu.

4. Wybierz wielokropek (**...**) we wszystkich konfiguracjach, a następnie kliknij przycisk **Edytuj**. 
5. W okienku **Edytowanie konfiguracji zakresu** wybierz pozycję **Wybierz grupy komputerów**. W okienku **grupy komputerów** zostaną wyświetlone zapisane wyszukiwania, które są używane do tworzenia konfiguracji zakresu.

## <a name="saved-searches"></a>Zapisane wyszukiwania

Gdy komputer zostanie dodany do rozwiązania Update Management, Change Tracking lub spisu, komputer zostanie dodany do jednego z dwóch zapisanych wyszukiwań w obszarze roboczym. Zapisane wyszukiwania to zapytania zawierające komputery przeznaczone dla tych rozwiązań.

Przejdź do swojego obszaru roboczego. W obszarze **Ogólne**wybierz pozycję **zapisane wyszukiwania**. Dwa zapisane wyszukiwania, które są używane w tych rozwiązaniach, przedstawiono w poniższej tabeli:

|Nazwa     |Kategoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Śledzenia zmian       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

Wybierz jedno z zapisanych wyszukiwań, aby wyświetlić zapytanie, które jest używane do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytanie i jego wyniki:

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Unlink workspace (Odłączanie obszaru roboczego)

Następujące rozwiązania zależą od obszaru roboczego Log Analytics:

* [Zarządzanie aktualizacjami](automation-update-management.md)
* [Change Tracking](automation-change-tracking.md)
* [Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy](automation-solution-vm-management.md)

Jeśli zdecydujesz, że nie chcesz już integrować konta usługi Automation z obszarem roboczym Log Analytics, możesz odłączyć swoje konto bezpośrednio od Azure Portal.  Przed kontynuowaniem należy najpierw usunąć wymienione wcześniej rozwiązania. w przeciwnym razie proces ten nie zostanie zablokowany. Zapoznaj się z artykułem dotyczącym określonego rozwiązania, które zostało zaimportowane, aby poznać kroki wymagane do jego usunięcia.

Po usunięciu tych rozwiązań możesz wykonać następujące czynności, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania Azure SQL monitoring, mogły utworzyć zasoby automatyzacji i mogą być również musiały zostać usunięte przed odłączeniem obszaru roboczego.

1. W Azure Portal Otwórz konto usługi Automation i wybierz **połączony obszar roboczy** w sekcji **Zasoby pokrewne** po lewej stronie.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**.

   ![Odłącz stronę obszaru roboczego](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Zostanie wyświetlony monit sprawdzający, czy chcesz kontynuować.

3. Mimo że Azure Automation próbuje odłączyć konto do obszaru roboczego Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

Jeśli użyto rozwiązania Update Management, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Harmonogramy aktualizacji — każda z nich będzie mieć nazwy zgodne z utworzonymi wdrożeniami aktualizacji.

* Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każda z nich będzie podobna do maszyna1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Jeśli użyto rozwiązania Start/Stop VMs during off-hours, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywanie harmonogramów elementów Runbook maszyny wirtualnej
* Uruchamianie i zatrzymywanie elementów Runbook maszyny wirtualnej
* Zmienne

Możesz również odłączyć obszar roboczy od konta usługi Automation z poziomu obszaru roboczego Log Analytics. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć maszynę wirtualną z Update Management:

* W obszarze roboczym Log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania konfiguracji `MicrosoftDefaultScopeConfig-Updates`zakresu. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.
* Usuń [agenta log Analytics dla systemu Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta log Analytics dla Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Przejdź do samouczków dotyczących rozwiązań, aby dowiedzieć się, jak z nich korzystać:

* [Samouczek — zarządzanie aktualizacjami dla maszyny wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikowanie oprogramowania na maszynie wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej](automation-tutorial-troubleshoot-changes.md)
