---
title: Dowiedz się, jak dołączać rozwiązania do zarządzania aktualizacjami, śledzenia zmian i zapasów w usłudze Azure Automation
description: Dowiedz się, jak przywdzielić maszynę wirtualną platformy Azure za pomocą rozwiązań zarządzania aktualizacjami, śledzenia zmian i zapasów, które są częścią automatyzacji platformy Azure
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 57378005bd668fa9c0f2aea70c411bbf911130db
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457658"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Wbudowane rozwiązania do zarządzania aktualizacjami, śledzenia zmian i zapasów

Usługa Azure Automation udostępnia rozwiązania do zarządzania aktualizacjami zabezpieczeń systemu operacyjnego, śledzenia zmian i inwentaryzacji zainstalowanych na komputerach. Istnieje wiele sposobów na wbudowane maszyny, można wbudowane rozwiązanie [z maszyny wirtualnej,](automation-onboard-solutions-from-vm.md) [od przeglądania wielu maszyn,](automation-onboard-solutions-from-browse.md)z konta automatyzacji, lub przez [runbook](automation-onboard-solutions.md). W tym artykule opisano dołączanie tych rozwiązań z konta automatyzacji.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się na https://portal.azure.complatformie Azure na poziomie .

## <a name="enable-solutions"></a>Włączanie rozwiązań

Przejdź do konta automatyzacji i wybierz pozycję **Śledzenie zapasów** lub **zmian** w obszarze **Zarządzanie konfiguracją**.

Wybierz obszar roboczy usługi Log Analytics i konto automatyzacji, a następnie kliknij przycisk **Włącz,** aby włączyć rozwiązanie. Włączanie rozwiązania może trwać do 15 minut.

![Wbudowane rozwiązanie inwentaryzacyjnych](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> W przypadku włączenia rozwiązań tylko w niektórych regionach jest obsługiwane łączenie obszaru roboczego usługi Log Analytics i konta usługi Automation.
>
> Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionu dla obszaru roboczego Konto automatyzacji i Usługa Analizy dzienników](how-to/region-mappings.md).

Rozwiązanie Change Tracking and Inventory umożliwia [śledzenie zmian](automation-vm-change-tracking.md) i tworzenie [spisu](automation-vm-inventory.md) na maszynach wirtualnych. W tym kroku włączysz rozwiązanie na maszynie wirtualnej.

Po zakończeniu powiadamiania o dołączaniu rozwiązania do śledzenia zmian i zapasów wybierz pozycję **Zarządzanie aktualizacjami** w obszarze **Zarządzanie aktualizacjami**.

Rozwiązanie do zarządzania aktualizacjami umożliwia zarządzanie aktualizacjami i poprawkami dla platformy Azure i hybrydowych maszyn wirtualnych. Można ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrożenia, aby sprawdzić, czy aktualizacje zostały pomyślnie zastosowane do nich.

Na stronie włącz rozwiązanie wybrany obszar roboczy usługi Log Analytics jest tym samym obszarem roboczym, który jest używany w poprzednim kroku. Kliknij **pozycję Włącz,** aby przykłować rozwiązanie do zarządzania aktualizacjami. Włączanie rozwiązania może trwać do 15 minut.

![Wbudowane rozwiązanie do aktualizacji](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Konfiguracja zakresu

Każde rozwiązanie używa konfiguracji zakresu w obszarze roboczym do kierowania na komputery, które otrzymują rozwiązanie. Konfiguracja zakresu to grupa jednego lub więcej zapisanych wyszukiwań, która jest używana do ograniczenia zakresu rozwiązania do określonych komputerów. Aby uzyskać dostęp do konfiguracji zakresu, na koncie automatyzacji w obszarze **Zasoby pokrewne**wybierz pozycję **Obszar roboczy**. Następnie w obszarze roboczym w obszarze **Źródła danych obszaru roboczego**wybierz opcję **Konfiguracje zakresu**.

Jeśli w wybranym obszarze roboczym nie ma jeszcze rozwiązań Zarządzanie aktualizacjami lub Śledzenie zmian, tworzone są następujące konfiguracje zakresu:

* **Śledzenie microsoftdefaultscopeconfig-changetracking**

* **Aktualizacje microsoftdefaultscopeconfig**

Jeśli wybrany obszar roboczy ma już rozwiązanie, rozwiązanie nie jest ponownie wdrażane, a konfiguracja zakresu nie jest dodawana do niego.

## <a name="saved-searches"></a>Zapisane wyszukiwania

Gdy komputer zostanie dodany do rozwiązania Zarządzanie aktualizacjami lub Śledzenie zmian i spis, są one dodawane do jednego z dwóch zapisanych wyszukiwań w obszarze roboczym. Te zapisane wyszukiwania są kwerendami, które zawierają komputery, które są przeznaczone dla tych rozwiązań.

Przejdź do obszaru roboczego usługi Log Analytics i wybierz pozycję **Zapisane wyszukiwania** w obszarze **Ogólne**. Dwa zapisane wyszukiwania używane przez te rozwiązania można zobaczyć w poniższej tabeli:

|Nazwa     |Kategoria  |Alias  |
|---------|---------|---------|
|Grupa MicrosoftDefaultComputerGroup     |  Changetracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|Grupa MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

Wybierz zapisane wyszukiwanie, aby wyświetlić kwerendę używaną do wypełniania grupy. Na poniższej ilustracji przedstawiono kwerendę i jej wyniki:

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Wbudowane maszyny wirtualne platformy Azure

Z konta Automatyzacja wybierz **pozycję Zapasy** lub **Śledzenie zmian** w obszarze **Zarządzanie konfiguracją**lub **Zarządzanie aktualizacjami** w obszarze **Zarządzanie aktualizacjami**.

Kliknij **+ Dodaj maszyny wirtualne platformy Azure**, wybierz jedną lub więcej maszyn wirtualnych z listy. Maszyny wirtualne, których nie można włączyć, są wyszarzone i nie można ich wybrać. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta automatyzacji. Na stronie **Włącz zarządzanie aktualizacjami** kliknij pozycję **Włącz**. Ta akcja dodaje wybrane maszyny wirtualne do grupy komputerów zapisane wyszukiwanie rozwiązania.

![Włączanie maszyn wirtualnych platformy Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Dołączanie maszyny spoza platformy Azure

Maszyny niena platformy Azure muszą być dodawane ręcznie. Z konta Automatyzacja wybierz **pozycję Zapasy** lub **Śledzenie zmian** w obszarze **Zarządzanie konfiguracją**lub **Zarządzanie aktualizacjami** w obszarze **Zarządzanie aktualizacjami**.

Kliknij **pozycję Dodaj komputer spoza platformy Azure**. Ta akcja otwiera nowe okno przeglądarki z [instrukcjami instalowania i konfigurowania agenta usługi Log Analytics dla systemu Windows, tak](../azure-monitor/platform/log-analytics-agent.md) aby urządzenie może rozpocząć raportowanie do rozwiązania. Jeśli dołączasz komputer, który jest obecnie zarządzany przez program System Center Operations Manager, nowy agent nie jest wymagany, a informacje o obszarze roboczym są wprowadzane do istniejącego agenta.

## <a name="onboard-machines-in-the-workspace"></a>Maszyny pokładowe w obszarze roboczym

Ręcznie zainstalowane maszyny lub maszyny już raportowania do obszaru roboczego należy dodać do usługi Azure Automation, aby rozwiązanie było włączone. Z konta Automatyzacja wybierz **pozycję Zapasy** lub **Śledzenie zmian** w obszarze **Zarządzanie konfiguracją**lub **Zarządzanie aktualizacjami** w obszarze **Zarządzanie aktualizacjami**.

Wybierz **pozycję Zarządzaj maszynami**. Ta akcja otwiera stronę **Zarządzanie maszynami.** Ta strona umożliwia włączenie rozwiązania na wybranych maszynach, wszystkich dostępnych maszynach lub włączenie rozwiązania dla wszystkich obecnych maszyn i włączenie go na wszystkich przyszłych maszynach. Przycisk **Zarządzaj maszynami** może być wyszarzony, jeśli wcześniej wybrano opcję **Włącz na wszystkich dostępnych i przyszłych komputerach.**

![Zapisane wyszukiwania](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Wszystkie dostępne maszyny

Aby włączyć rozwiązanie dla wszystkich dostępnych maszyn, wybierz **włącz na wszystkich dostępnych komputerach**. Ta akcja wyłącza formant, aby dodać maszyny indywidualnie. To zadanie dodaje wszystkie nazwy maszyn raportowania do obszaru roboczego do grupy komputerów zapisane zapytanie wyszukiwania. Po wybraniu tej opcji ta akcja wyłącza przycisk **Zarządzaj maszynami.**

### <a name="all-available-and-future-machines"></a>Wszystkie dostępne i przyszłe maszyny

Aby włączyć rozwiązanie dla wszystkich dostępnych maszyn i przyszłych maszyn, wybierz **włącz wszystkie dostępne i przyszłe maszyny.** Ta opcja powoduje usunięcie zapisanych wyszukiwań i konfiguracji zakresu z obszaru roboczego. Ta akcja otwiera rozwiązanie dla wszystkich komputerów platformy Azure i innych niż Azure, które zgłaszają się do obszaru roboczego. Po wybraniu tej opcji ta akcja trwale wyłącza przycisk **Zarządzaj maszynami,** ponieważ nie ma żadnej konfiguracji zakresu.

Konfiguracje zakresu można dodać z powrotem, dodając początkowe zapisane wyszukiwania z powrotem. Aby uzyskać więcej informacji, zobacz [Zapisane wyszukiwania](#saved-searches).

### <a name="selected-machines"></a>Wybrane maszyny

Aby włączyć rozwiązanie dla co najmniej jednej maszyny, wybierz **włącz na wybranych komputerach** i kliknij przycisk **Dodaj** obok każdej maszyny, którą chcesz dodać do rozwiązania. To zadanie dodaje wybrane nazwy komputerów do grupy komputerów zapisane zapytanie wyszukiwania dla rozwiązania.

## <a name="unlink-workspace"></a>Unlink workspace (Odłączanie obszaru roboczego)

Następujące rozwiązania są zależne od obszaru roboczego usługi Log Analytics:

* [Zarządzanie aktualizacjami](automation-update-management.md)
* [Śledzenie zmian](automation-change-tracking.md)
* [Uruchamianie/zatrzymywania maszyn wirtualnych poza godzinami pracy](automation-solution-vm-management.md)

Jeśli zdecydujesz, że nie chcesz już integrować konta automatyzacji z obszarem roboczym usługi Log Analytics, możesz odłączyć swoje konto bezpośrednio z witryny Azure portal.  Przed kontynuowaniem należy najpierw usunąć rozwiązania wymienione wcześniej, w przeciwnym razie ten proces nie będzie mógł kontynuować. Zapoznaj się z artykułem dla określonego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do jego usunięcia.

Po usunięciu tych rozwiązań można wykonać następujące kroki, aby odłączyć konto automatyzacji.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania SQL platformy Azure, mogły utworzyć zasoby automatyzacji i mogą również wymagać usunięcia przed odłączeniem obszaru roboczego.

1. W witrynie Azure portal otwórz konto automatyzacji, a na stronie konta automatyzacji wybierz **pozycję Połączony obszar roboczy** w sekcji oznaczonej **powiązanymi zasobami** po lewej stronie.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**.

   ![Odłącz stronę obszaru roboczego](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Otrzymasz monit z weryfikacją, że chcesz kontynuować.

3. Podczas usługi Azure Automation próbuje odłączyć konto obszaru roboczego usługi Log Analytics, można śledzić postęp w obszarze **Powiadomienia** z menu.

Jeśli użyto rozwiązania do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Harmonogramy aktualizacji — każda z nich będzie miała nazwy zgodne z utworzonymi wdrożeniami aktualizacji.

* Hybrydowe grupy robocze utworzone dla rozwiązania — każda będzie nazywana podobnie jak machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Jeśli używano start i stop maszyn wirtualnych podczas poza godzinami pracy rozwiązania, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywania harmonogramów ujeżdnych maszyn wirtualnych
* Uruchamianie i zatrzymywania obrażeń ekwształtów maszyn wirtualnych
* Zmienne

Alternatywnie można również odłączyć obszar roboczy od konta automatyzacji z obszaru roboczego usługi Log Analytics. W obszarze roboczym wybierz pozycję **Konto automatyzacji** w obszarze **Zasoby pokrewne**. Na stronie Konto automatyzacji wybierz pozycję **Odłącz konto**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć maszynę wirtualną z zarządzania aktualizacjami:

* W obszarze roboczym usługi Log Analytics usuń maszynę wirtualną z zapisanego wyszukiwania konfiguracji `MicrosoftDefaultScopeConfig-Updates`zakresu . Zapisane wyszukiwania można znaleźć w obszarze **Ogólne** w obszarze roboczym.
* Usuń [agenta analizy dzienników dla systemu Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta analizy dzienników dla systemu Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

Przejdź do samouczków dotyczących rozwiązań, aby dowiedzieć się, jak z nich korzystać.

* [Samouczek - Zarządzanie aktualizacjami dla maszyny Wirtualnej](automation-tutorial-update-management.md)

* [Samouczek — identyfikowanie oprogramowania na maszynie Wirtualnej](automation-tutorial-installed-software.md)

* [Samouczek — rozwiązywanie problemów ze zmianami na maszynie Wirtualnej](automation-tutorial-troubleshoot-changes.md)
