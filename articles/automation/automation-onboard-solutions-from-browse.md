---
title: Dowiedz się, jak dołączyć rozwiązania Update Management, Change Tracking i spisu dla wielu maszyn wirtualnych w Azure Automation
description: Dowiedz się, jak dołączyć maszynę wirtualną platformy Azure przy użyciu rozwiązań Update Management, Change Tracking i spisu, które są częścią Azure Automation
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: f5f18e9365b09f06c1bd4f25a8efe909cc308dad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537019"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Włączanie rozwiązań Update Management, Change Tracking i spisu na wielu maszynach wirtualnych

Azure Automation udostępnia rozwiązania do zarządzania aktualizacjami zabezpieczeń systemu operacyjnego, śledzenia zmian i spisu, co jest zainstalowane na komputerach. Istnieje wiele sposobów, aby dołączać maszyny, można dołączyć rozwiązanie [z maszyny wirtualnej](automation-onboard-solutions-from-vm.md)do [konta usługi Automation](automation-onboard-solutions-from-automation-account.md)podczas przeglądania maszyn wirtualnych lub przez [element Runbook](automation-onboard-solutions.md). W tym artykule opisano dołączanie tych rozwiązań podczas przeglądania maszyn wirtualnych na platformie Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="enable-solutions"></a>Włączanie rozwiązań

W Azure Portal przejdź do **maszyn wirtualnych**.

Korzystając z pól wyboru, wybierz maszyny wirtualne, które chcesz dołączyć do Change Tracking i spisu lub Update Management. Dołączanie jest dostępne dla maksymalnie trzech różnych grup zasobów jednocześnie. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation.

![Lista maszyn wirtualnych](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Użyj kontrolek filtr, aby zmodyfikować listę maszyn wirtualnych, a następnie kliknij górne pole wyboru, aby wybrać wszystkie maszyny wirtualne na liście.

Na pasku poleceń kliknij pozycję **usługi** i wybierz opcję **śledzenie zmian**, **spis**lub **Update Management**.

> [!NOTE]
> Change Tracking i spis używają tego samego rozwiązania. Gdy jest włączona, druga jest również włączona.

Na poniższej ilustracji przedstawiono Update Management. Change Tracking i spis mają ten sam układ i zachowanie.

Lista maszyn wirtualnych jest filtrowana w celu wyświetlania tylko maszyn wirtualnych znajdujących się w tej samej subskrypcji i lokalizacji. Jeśli maszyny wirtualne znajdują się w więcej niż trzech grupach zasobów, wybierane są pierwsze trzy grupy zasobów.

### <a name="onboarding-limitations"></a><a name="resource-group-limit"></a>Ograniczenia dotyczące dołączania

Liczba grup zasobów, których można użyć do dołączenia, jest ograniczona przez [Menedżer zasobów limity wdrożenia](../azure-resource-manager/templates/cross-resource-group-deployment.md). Menedżer zasobów wdrożeń nie należy mylić z wdrożeniami aktualizacji, są ograniczone do 5 grup zasobów na wdrożenie. Aby zapewnić integralność dołączania, 2 z tych grup zasobów są zastrzeżone do konfigurowania obszaru roboczego Log Analytics, konta usługi Automation i powiązanych zasobów. Spowoduje to pozostawienie 3 grup zasobów do wybrania do wdrożenia. Ten limit dotyczy tylko jednoczesnego dołączania, a nie liczby grup zasobów, które mogą być zarządzane przez rozwiązanie automatyzacji.

Możesz również użyć elementu Runbook do dołączenia, aby uzyskać więcej informacji, zobacz Dołączanie [rozwiązań aktualizacji i śledzenia zmian do Azure Automation](automation-onboard-solutions.md).

Użyj kontrolek filtr, aby wybrać maszyny wirtualne z różnych subskrypcji, lokalizacji i grup zasobów.

![Dołączanie rozwiązania do zarządzania aktualizacjami](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Zapoznaj się z opcjami obszaru roboczego Log Analytics i konta usługi Automation. Istniejący obszar roboczy i konto usługi Automation są domyślnie zaznaczone. Jeśli chcesz użyć innego obszaru roboczego Log Analytics i konta usługi Automation, kliknij pozycję **niestandardowa** , aby wybrać je ze strony **Konfiguracja niestandardowa** . Po wybraniu obszaru roboczego Log Analytics należy sprawdzić, czy jest on połączony z kontem usługi Automation. Po znalezieniu połączonego konta usługi Automation zobaczysz następujący ekran. Po zakończeniu kliknij przycisk **OK**.

![Wybieranie obszaru roboczego i konta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Jeśli wybrany obszar roboczy nie jest połączony z kontem usługi Automation, zobaczysz następujący ekran. Wybierz konto usługi Automation, a następnie kliknij przycisk **OK** po zakończeniu.

![Brak obszaru roboczego](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
>
> Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

Usuń zaznaczenie pola wyboru obok każdej maszyny wirtualnej, która nie ma być włączona. Maszyny wirtualne, których nie można włączyć, są już odwybrane.

Kliknij pozycję **Włącz** , aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

## <a name="unlink-workspace"></a>Unlink workspace (Odłączanie obszaru roboczego)

Następujące rozwiązania zależą od obszaru roboczego Log Analytics:

* [Zarządzanie aktualizacjami](automation-update-management.md)
* [Change Tracking](automation-change-tracking.md)
* [Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy](automation-solution-vm-management.md)

Jeśli zdecydujesz, że nie chcesz już integrować konta usługi Automation z obszarem roboczym Log Analytics, możesz odłączyć swoje konto bezpośrednio od Azure Portal. Przed kontynuowaniem należy najpierw usunąć wymienione wcześniej rozwiązania. w przeciwnym razie proces ten nie zostanie zablokowany. Zapoznaj się z artykułem dotyczącym określonego rozwiązania, które zostało zaimportowane, aby poznać kroki wymagane do jego usunięcia.

Po usunięciu tych rozwiązań możesz wykonać następujące czynności, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania Azure SQL monitoring, mogły utworzyć zasoby automatyzacji i mogą być również musiały zostać usunięte przed odłączeniem obszaru roboczego.

1. W Azure Portal Otwórz konto usługi Automation, a na stronie konto usługi Automation wybierz opcję **połączony obszar roboczy** w sekcji **zasoby powiązane** z pozostałą częścią.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**.

   ![Odłącz stronę obszaru roboczego](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Zostanie wyświetlony monit sprawdzający, czy chcesz kontynuować.

3. Mimo że Azure Automation próbuje odłączyć konto do obszaru roboczego Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

Jeśli użyto rozwiązania Update Management, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Harmonogramy aktualizacji — każda z nich będzie mieć nazwy zgodne z utworzonymi wdrożeniami aktualizacji)

* Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każda z nich będzie podobna do maszyna1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Jeśli użyto rozwiązania Start/Stop VMs during off-hours, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywanie harmonogramów elementów Runbook maszyny wirtualnej
* Uruchamianie i zatrzymywanie elementów Runbook maszyny wirtualnej
* Zmienne

Możesz również odłączyć obszar roboczy od konta usługi Automation z poziomu obszaru roboczego Log Analytics. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas dołączania wielu maszyn mogą istnieć maszyny, które są wyświetlane jako `Cannot enable`. Istnieją różne przyczyny, dla których niektóre maszyny mogą nie być włączone. W poniższych sekcjach przedstawiono możliwe przyczyny `Cannot enable` stanu na maszynie wirtualnej podczas próby dołączenia.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Raporty maszyny wirtualnej do innego obszaru roboczego:\<"WorkspaceName\>".  Zmień konfigurację, aby była używana do włączania

**Przyczyna**: ten błąd pokazuje, że maszyna wirtualna, którą próbujesz dołączyć do raportów, do innego obszaru roboczego.

**Rozwiązanie**: kliknij pozycję **Użyj jako konfiguracji** , aby zmienić wybrane konto usługi Automation i obszar roboczy log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Raporty maszyn wirtualnych do obszaru roboczego, który nie jest dostępny w tej subskrypcji

**Przyczyna**: obszar roboczy, do którego jest raportowana maszyna wirtualna:

* Znajduje się w innej subskrypcji lub
* Już nie istnieje lub
* Znajduje się w grupie zasobów, do której nie masz uprawnień dostępu

**Rozwiązanie**: Znajdź konto usługi Automation skojarzone z obszarem roboczym, do którego maszyna wirtualna jest zgłaszana, i dołączaj maszynę wirtualną, zmieniając konfigurację zakresu.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Wersja lub dystrybucja systemu operacyjnego maszyny wirtualnej nie jest obsługiwana

**Przyczyna:** Rozwiązanie nie jest obsługiwane w przypadku wszystkich dystrybucji systemu Linux lub wszystkich wersji Windows.

**Rozwiązanie:** Zapoznaj się z [listą obsługiwanych klientów](automation-update-management.md#clients) rozwiązania.

### <a name="classic-vms-cannot-be-enabled"></a>Nie można włączyć klasycznych maszyn wirtualnych

**Przyczyna**: maszyny wirtualne korzystające z klasycznego modelu wdrażania nie są obsługiwane.

**Rozwiązanie**: Migruj maszynę wirtualną do modelu wdrażania Menedżer zasobów. Aby dowiedzieć się, jak to zrobić, zobacz [Migrowanie zasobów modelu wdrożenia klasycznego](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Maszyna wirtualna jest zatrzymana. bez alokacji

**Przyczyna**: maszyna wirtualna nie jest w stanie **uruchomienia** .

**Rozwiązanie**: Aby dołączyć maszynę wirtualną do rozwiązania, maszyna wirtualna musi być uruchomiona. Kliknij link **Rozpocznij maszynę wirtualną** , aby uruchomić maszynę wirtualną bez nawigowania na stronie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć maszynę wirtualną z Update Management:

* W obszarze roboczym Log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania konfiguracji `MicrosoftDefaultScopeConfig-Updates`zakresu. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.
* Usuń [agenta log Analytics dla systemu Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta log Analytics dla Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy rozwiązanie jest włączone dla maszyn wirtualnych, zapoznaj się z artykułem Omówienie Update Management, aby dowiedzieć się, jak utworzyć **wdrożenie aktualizacji** dla maszyn.

> [!div class="nextstepaction"]
> [Update Management — Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](./automation-tutorial-update-management.md)

Dodatkowe samouczki dotyczące rozwiązań i sposoby ich używania:

* [Samouczek — zarządzanie aktualizacjami dla maszyny wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikowanie oprogramowania na maszynie wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej](automation-tutorial-troubleshoot-changes.md)
