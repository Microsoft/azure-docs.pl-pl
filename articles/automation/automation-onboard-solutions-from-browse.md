---
title: Dowiedz się, jak dołączać rozwiązania do zarządzania aktualizacjami, śledzenia zmian i zapasów dla wielu maszyn wirtualnych w usłudze Azure Automation
description: Dowiedz się, jak przywdzielić maszynę wirtualną platformy Azure za pomocą rozwiązań zarządzania aktualizacjami, śledzenia zmian i zapasów, które są częścią automatyzacji platformy Azure
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: f5f18e9365b09f06c1bd4f25a8efe909cc308dad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537019"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Włączanie rozwiązań do zarządzania aktualizacjami, śledzenia zmian i zapasów na wielu maszynach wirtualnych

Usługa Azure Automation udostępnia rozwiązania do zarządzania aktualizacjami zabezpieczeń systemu operacyjnego, śledzenia zmian i inwentaryzacji zainstalowanych na komputerach. Istnieje wiele sposobów dołączania maszyn, można dołączać rozwiązanie [z maszyny wirtualnej,](automation-onboard-solutions-from-vm.md)z [konta automatyzacji,](automation-onboard-solutions-from-automation-account.md)podczas przeglądania maszyn wirtualnych lub przez [system runbook.](automation-onboard-solutions.md) W tym artykule opisano dołączanie tych rozwiązań podczas przeglądania maszyn wirtualnych na platformie Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="enable-solutions"></a>Włączanie rozwiązań

W witrynie Azure portal przejdź do **witryny Maszyny wirtualne**.

Korzystając z pól wyboru, wybierz maszyny wirtualne, które chcesz mieć na pokładzie, korzystając z funkcji Śledzenia zmian i zarządzania aktualizacjami lub aktualizacji. Dołączanie jest dostępne dla maksymalnie trzech różnych grup zasobów naraz. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta automatyzacji.

![Lista maszyn wirtualnych](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Użyj formantów filtru, aby zmodyfikować listę maszyn wirtualnych, a następnie kliknij górne pole wyboru, aby zaznaczyć wszystkie maszyny wirtualne na liście.

Na pasku poleceń kliknij pozycję **Usługi** i wybierz pozycję **Zmień śledzenie,** **Zapasy**lub **Zarządzanie aktualizacjami**.

> [!NOTE]
> Śledzenie zmian i zapasy używają tego samego rozwiązania. Gdy jeden jest włączony, drugi jest włączony, jak również.

Poniższa ilustracja dotyczy zarządzania aktualizacjami. Śledzenie zmian i zasoby reklamowe mają ten sam układ i zachowanie.

Lista maszyn wirtualnych jest filtrowana, aby wyświetlić tylko maszyny wirtualne, które znajdują się w tej samej subskrypcji i lokalizacji. Jeśli maszyny wirtualne znajdują się w więcej niż trzech grupach zasobów, wybrane są pierwsze trzy grupy zasobów.

### <a name="onboarding-limitations"></a><a name="resource-group-limit"></a>Ograniczenia dołączania

Liczba grup zasobów, których można używać do dołączania, jest ograniczona [przez limity wdrażania Menedżera zasobów](../azure-resource-manager/templates/cross-resource-group-deployment.md). Wdrożenia Menedżera zasobów, których nie należy mylić z wdrożeniami aktualizacji, są ograniczone do 5 grup zasobów na wdrożenie. Aby zapewnić integralność dołączania, 2 z tych grup zasobów są zarezerwowane do konfigurowania obszaru roboczego usługi Log Analytics, konta automatyzacji i powiązanych zasobów. Spowoduje to, że masz 3 grupy zasobów do wybrania do wdrożenia. Ten limit dotyczy tylko jednoczesnego dołączania, a nie liczby grup zasobów, które mogą być zarządzane przez rozwiązanie automatyzacji.

Można również użyć systemu runbook do dołączania, aby uzyskać więcej informacji, zobacz [Wbudowane rozwiązania do aktualizacji aktualizacji i zmian w usłudze Azure Automation.](automation-onboard-solutions.md)

Użyj formantów filtru, aby wybrać maszyny wirtualne z różnych subskrypcji, lokalizacji i grup zasobów.

![Wbudowane rozwiązanie do zarządzania aktualizacjami](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Przejrzyj opcje dla obszaru roboczego usługi Log Analytics i konta automatyzacji. Domyślnie wybierany jest istniejący obszar roboczy i konto automatyzacji. Jeśli chcesz użyć innego obszaru roboczego usługi Log Analytics i konta automatyzacji, kliknij przycisk **NIESTANDARDOWY,** aby wybrać je ze strony **Konfiguracja niestandardowa.** Po wybraniu obszaru roboczego usługi Log Analytics zostanie sprawdzone w celu ustalenia, czy jest on połączony z kontem automatyzacji. Jeśli zostanie znalezione połączone konto automatyzacji, zostanie wyświetlony następujący ekran. Po zakończeniu kliknij przycisk **OK**.

![Wybieranie obszaru roboczego i konta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Jeśli wybrany obszar roboczy nie jest połączony z kontem automatyzacji, zostanie wyświetlony następujący ekran. Wybierz konto automatyzacji i kliknij przycisk **OK** po zakończeniu.

![Brak obszaru roboczego](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
>
> Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionu dla obszaru roboczego Konto automatyzacji i Usługa Analizy dzienników](how-to/region-mappings.md).

Usuń zaznaczenie pola wyboru obok dowolnej maszyny wirtualnej, której nie chcesz włączyć. Maszyny wirtualne, których nie można włączyć, są już odznaczone.

Kliknij **przycisk Włącz,** aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

## <a name="unlink-workspace"></a>Unlink workspace (Odłączanie obszaru roboczego)

Następujące rozwiązania są zależne od obszaru roboczego usługi Log Analytics:

* [Zarządzanie aktualizacjami](automation-update-management.md)
* [Śledzenie zmian](automation-change-tracking.md)
* [Uruchamianie/zatrzymywania maszyn wirtualnych poza godzinami pracy](automation-solution-vm-management.md)

Jeśli zdecydujesz, że nie chcesz już integrować konta automatyzacji z obszarem roboczym usługi Log Analytics, możesz odłączyć swoje konto bezpośrednio z witryny Azure portal. Przed kontynuowaniem należy najpierw usunąć rozwiązania wymienione wcześniej, w przeciwnym razie proces ten nie będzie mógł kontynuować. Zapoznaj się z artykułem dla określonego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do jego usunięcia.

Po usunięciu tych rozwiązań można wykonać następujące kroki, aby odłączyć konto automatyzacji.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania SQL platformy Azure, mogły utworzyć zasoby automatyzacji i mogą również wymagać usunięcia przed odłączeniem obszaru roboczego.

1. W witrynie Azure portal otwórz konto automatyzacji, a na stronie konta automatyzacji wybierz **pozycję Połączony obszar roboczy** w sekcji **Powiązane zasoby** po lewej stronie.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**.

   ![Odłącz stronę obszaru roboczego](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Zostanie wyświetlony monit sprawdzający, czy chcesz kontynuować.

3. Podczas gdy usługa Azure Automation próbuje odłączyć konto obszaru roboczego usługi Log Analytics, można śledzić postęp w obszarze **Powiadomienia** z menu.

Jeśli użyto rozwiązania do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Harmonogramy aktualizacji — każda z nich będzie miała nazwy zgodne z utworzonymi wdrożeniami aktualizacji)

* Hybrydowe grupy robocze utworzone dla rozwiązania — każda będzie nazywana podobnie jak machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Jeśli używano start/stop maszyn wirtualnych podczas poza godzinami pracy rozwiązania, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywania harmonogramów ujeżdnych maszyn wirtualnych
* Uruchamianie i zatrzymywania obrażeń ekwształtów maszyn wirtualnych
* Zmienne

Alternatywnie można również odłączyć obszar roboczy od konta automatyzacji z obszaru roboczego usługi Log Analytics. W obszarze roboczym wybierz pozycję **Konto automatyzacji** w obszarze **Zasoby pokrewne**. Na stronie Konto automatyzacji wybierz pozycję **Odłącz konto**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas dołączania do wielu maszyn mogą istnieć maszyny, które są wyświetlane jako `Cannot enable`. Istnieją różne powody, dla których niektóre maszyny mogą nie być włączone. W poniższych sekcjach przedstawiono możliwe przyczyny `Cannot enable` stanu na maszynie Wirtualnej podczas próby wbudowanego.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Maszyna wirtualna zgłasza się do\<innego obszaru\>roboczego: ' workspaceName '.  Zmienianie konfiguracji w celu jej używania w celu włączenia

**Przyczyna:** Ten błąd pokazuje, że maszyna wirtualna, która próbujesz dołączać, zgłasza do innego obszaru roboczego.

**Rozwiązanie:** Kliknij opcję **Użyj jako konfiguracji,** aby zmienić docelowe konto automatyzacji i obszar roboczy usługi Log Analytics.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Maszyna wirtualna zgłasza się do obszaru roboczego, który nie jest dostępny w tej subskrypcji

**Przyczyna:** Obszar roboczy, do który maszyna wirtualna zgłasza:

* Jest w innej subskrypcji lub
* już nie istnieje, lub
* Znajduje się w grupie zasobów, do której nie masz uprawnień dostępu

**Rozwiązanie:** Znajdź konto automatyzacji skojarzone z obszarem roboczym, który maszyna wirtualna zgłasza i dołącza na maszynie wirtualnej, zmieniając konfigurację zakresu.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Wersja lub dystrybucja systemu operacyjnego maszyny Wirtualnej nie jest obsługiwana

**Przyczyna:** Rozwiązanie nie jest obsługiwane dla wszystkich dystrybucji linuksa lub wszystkich wersji systemu Windows.

**Rozwiązanie:** Zapoznaj się [z listą obsługiwanych klientów](automation-update-management.md#clients) dla rozwiązania.

### <a name="classic-vms-cannot-be-enabled"></a>Nie można włączyć klasycznych maszyn wirtualnych

**Przyczyna:** Maszyny wirtualne korzystające z klasycznego modelu wdrażania nie są obsługiwane.

**Rozwiązanie:** Migrowanie maszyny wirtualnej do modelu wdrażania Menedżera zasobów. Aby dowiedzieć się, jak to zrobić, zobacz [Migrowanie zasobów modelu wdrażania klasycznego](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Maszyna wirtualna jest zatrzymana. (cofnięto przydział)

**Przyczyna:** Maszyna wirtualna w stanie **niew** stanie uruchomionym.

**Rozwiązanie:** Aby wbudowana maszyna wirtualna do rozwiązania maszyna wirtualna musi być uruchomiona. Kliknij łącze **początkowe maszyny Wirtualnej,** aby uruchomić maszynę wirtualną bez nawigowania ze strony.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć maszynę wirtualną z zarządzania aktualizacjami:

* W obszarze roboczym usługi Log Analytics usuń maszynę wirtualną z zapisanego wyszukiwania konfiguracji `MicrosoftDefaultScopeConfig-Updates`zakresu . Zapisane wyszukiwania można znaleźć w obszarze **Ogólne** w obszarze roboczym.
* Usuń [agenta analizy dzienników dla systemu Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta analizy dzienników dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy rozwiązanie jest włączone dla maszyn wirtualnych, odwiedź omówienie zarządzania aktualizacjami, aby dowiedzieć się, jak utworzyć **wdrożenie aktualizacji** dla komputerów.

> [!div class="nextstepaction"]
> [Zarządzanie aktualizacjami — zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](./automation-tutorial-update-management.md)

Dodawanie tutoriale na temat rozwiązań i jak z nich korzystać:

* [Samouczek - Zarządzanie aktualizacjami dla maszyny Wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikowanie oprogramowania na maszynie Wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — rozwiązywanie problemów ze zmianami na maszynie Wirtualnej](automation-tutorial-troubleshoot-changes.md)
