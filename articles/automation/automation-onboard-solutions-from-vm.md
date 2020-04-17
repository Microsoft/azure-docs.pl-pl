---
title: Wbudowane rozwiązania zarządzania aktualizacjami, śledzenia zmian i zapasów z maszyny wirtualnej platformy Azure
description: Dowiedz się, jak przywdzielić maszynę wirtualną platformy Azure za pomocą rozwiązań do zarządzania aktualizacjami, śledzenia zmian i zapasów, które są częścią usługi Azure Automation.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f33f829b6cb86cb01c848e5fc48e1618a3e00a2c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537036"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Wbudowane rozwiązania zarządzania aktualizacjami, śledzenia zmian i zapasów z maszyny wirtualnej platformy Azure

Usługa Azure Automation udostępnia rozwiązania ułatwiające zarządzanie aktualizacjami zabezpieczeń systemu operacyjnego, śledzenie zmian i inwentaryzację zainstalowanych na komputerach. Istnieje wiele sposobów na wbudowane maszyny. Rozwiązanie można za pomocą maszyny wirtualnej, [z konta automatyzacji,](automation-onboard-solutions-from-automation-account.md) [przeglądać wiele maszyn](automation-onboard-solutions-from-browse.md)lub za pomocą systemu [runbook.](automation-onboard-solutions.md) W tym artykule opisano dołączanie tych rozwiązań z maszyny wirtualnej platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="enable-the-solutions"></a>Włączanie rozwiązań

Najpierw włącz jedno lub wszystkie trzy rozwiązania na maszynie Wirtualnej:

1. W [portalu Azure](https://portal.azure.com)wybierz **maszyny wirtualne** lub wyszukaj i wybierz **maszyny wirtualne** ze strony głównej.
2. Wybierz maszynę wirtualną, dla której chcesz włączyć rozwiązanie.
3. Na stronie Maszyny Wirtualnej w obszarze **Operacje**wybierz pozycję **Zarządzanie aktualizacjami,** **Zapasy**lub **Śledzenie zmian**. Maszyna wirtualna może istnieć w dowolnym regionie, bez względu na lokalizację konta automatyzacji. Podczas dołączania rozwiązania z maszyny Wirtualnej, należy `Microsoft.OperationalInsights/workspaces/read` mieć uprawnienia do określenia, czy maszyna wirtualna jest dołączana do obszaru roboczego. Aby dowiedzieć się więcej o dodatkowych uprawnieniach, które są wymagane, zobacz [uprawnienia potrzebne do komputerów wbudowanych](automation-role-based-access-control.md#onboarding-permissions). Aby dowiedzieć się, jak dołączać wiele komputerów jednocześnie, zobacz [Wbudowane rozwiązania zarządzania aktualizacjami, śledzenia zmian i zapasów.](automation-onboard-solutions-from-automation-account.md)

4. Wybierz obszar roboczy usługi Azure Log Analytics i konto automatyzacji, a następnie kliknij przycisk **Włącz,** aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

![Dołączanie rozwiązania do zarządzania aktualizacjami](media/automation-tutorial-update-management/manageupdates-update-enable.png)

5. Przejdź do innych rozwiązań, a następnie wybierz pozycję **Włącz**. Obszar roboczy usługi Log Analytics i listy rozwijane kont automatyzacji są wyłączone, ponieważ te rozwiązania używają tego samego obszaru roboczego i konta automatyzacji, co wcześniej włączone rozwiązanie.

> [!NOTE]
> Śledzenie zmian i zapasów używają tego samego rozwiązania. Gdy jedno z tych rozwiązań jest włączone, drugie jest również włączone.

## <a name="scope-configuration"></a>Konfiguracja zakresu

Każde rozwiązanie używa konfiguracji zakresu w obszarze roboczym do kierowania na komputery, które otrzymują rozwiązanie. Konfiguracja zakresu to grupa jednego lub więcej zapisanych wyszukiwań, które są używane do ograniczenia zakresu rozwiązania do określonych komputerów. Aby uzyskać dostęp do konfiguracji zakresu:

1. Na koncie automatyzacji w obszarze **Powiązane zasoby**wybierz pozycję **Obszar roboczy**. 
2. W obszarze roboczym w obszarze **Źródła danych obszaru roboczego**wybierz pozycję **Konfiguracje zakresu**.
3. Jeśli wybrany obszar roboczy nie ma jeszcze rozwiązania Zarządzanie aktualizacjami lub Śledzenie zmian, tworzone są następujące konfiguracje zakresu:

    * `MicrosoftDefaultScopeConfig-ChangeTracking`
    * `MicrosoftDefaultScopeConfig-Updates`

    Jeśli wybrany obszar roboczy ma już rozwiązanie, rozwiązanie nie jest ponownie rozmieszczane i konfiguracja zakresu nie jest dodawana.

4. Zaznacz elipsy (**...**) w dowolnej konfiguracji, a następnie kliknij przycisk **Edytuj**. 
5. W okienku **konfiguracji Edytowanie zakresu** wybierz pozycję **Wybierz grupy komputerów**. Okienko **Grupy komputerów** zawiera zapisane wyszukiwania używane do tworzenia konfiguracji zakresu.

## <a name="saved-searches"></a>Zapisane wyszukiwania

Po dodaniu komputera do rozwiązania Zarządzanie aktualizacjami, Śledzenie zmian lub Spis, komputer jest dodawany do jednego z dwóch zapisanych wyszukiwań w obszarze roboczym. Zapisane wyszukiwania to zapytania zawierające komputery, które są przeznaczone dla tych rozwiązań.

Przejdź do swojego obszaru roboczego. W obszarze **Ogólne**wybierz pozycję **Zapisane wyszukiwania**. Dwa zapisane wyszukiwania używane przez te rozwiązania są pokazane w poniższej tabeli:

|Nazwa     |Kategoria  |Alias  |
|---------|---------|---------|
|Grupa MicrosoftDefaultComputerGroup     |  Changetracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|Grupa MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

Wybierz jeden z zapisanych wyszukiwań, aby wyświetlić kwerendę używaną do wypełniania grupy. Na poniższej ilustracji przedstawiono kwerendę i jej wyniki:

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Unlink workspace (Odłączanie obszaru roboczego)

Następujące rozwiązania są zależne od obszaru roboczego usługi Log Analytics:

* [Zarządzanie aktualizacjami](automation-update-management.md)
* [Śledzenie zmian](automation-change-tracking.md)
* [Uruchamianie/zatrzymywania maszyn wirtualnych poza godzinami pracy](automation-solution-vm-management.md)

Jeśli zdecydujesz, że nie chcesz już integrować konta automatyzacji z obszarem roboczym usługi Log Analytics, możesz odłączyć swoje konto bezpośrednio z witryny Azure portal.  Przed kontynuowaniem należy najpierw usunąć rozwiązania wymienione wcześniej, w przeciwnym razie proces ten nie będzie mógł kontynuować. Zapoznaj się z artykułem dla określonego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do jego usunięcia.

Po usunięciu tych rozwiązań można wykonać następujące kroki, aby odłączyć konto automatyzacji.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania SQL platformy Azure, mogły utworzyć zasoby automatyzacji i mogą również wymagać usunięcia przed odłączeniem obszaru roboczego.

1. W witrynie Azure portal otwórz konto automatyzacji i wybierz **połączony obszar roboczy** w sekcji **Powiązane zasoby** po lewej stronie.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**.

   ![Odłącz stronę obszaru roboczego](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Zostanie wyświetlony monit sprawdzający, czy chcesz kontynuować.

3. Podczas gdy usługa Azure Automation próbuje odłączyć konto obszaru roboczego usługi Log Analytics, można śledzić postęp w obszarze **Powiadomienia** z menu.

Jeśli użyto rozwiązania do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Harmonogramy aktualizacji — każda z nich będzie miała nazwy zgodne z utworzonymi wdrożeniami aktualizacji.

* Hybrydowe grupy robocze utworzone dla rozwiązania — każda będzie nazywana podobnie jak machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Jeśli używano start/stop maszyn wirtualnych podczas poza godzinami pracy rozwiązania, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywania harmonogramów ujeżdnych maszyn wirtualnych
* Uruchamianie i zatrzymywania obrażeń ekwształtów maszyn wirtualnych
* Zmienne

Alternatywnie można również odłączyć obszar roboczy od konta automatyzacji z obszaru roboczego usługi Log Analytics. W obszarze roboczym wybierz pozycję **Konto automatyzacji** w obszarze **Zasoby pokrewne**. Na stronie Konto automatyzacji wybierz pozycję **Odłącz konto**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć maszynę wirtualną z zarządzania aktualizacjami:

* W obszarze roboczym usługi Log Analytics usuń maszynę wirtualną z zapisanego wyszukiwania konfiguracji `MicrosoftDefaultScopeConfig-Updates`zakresu . Zapisane wyszukiwania można znaleźć w obszarze **Ogólne** w obszarze roboczym.
* Usuń [agenta analizy dzienników dla systemu Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta analizy dzienników dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Przejdź do samouczków dotyczących rozwiązań, aby dowiedzieć się, jak z nich korzystać:

* [Samouczek - Zarządzanie aktualizacjami dla maszyny Wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikowanie oprogramowania na maszynie Wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — rozwiązywanie problemów ze zmianami na maszynie Wirtualnej](automation-tutorial-troubleshoot-changes.md)
