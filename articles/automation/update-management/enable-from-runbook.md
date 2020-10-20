---
title: Włącz Update Management Azure Automation z elementu Runbook
description: W tym artykule opisano sposób włączania Update Management z elementu Runbook.
services: automation
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: ec102015355e3312f5dc15fa526fa543da75e0de
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222667"
---
# <a name="enable-update-management-from-a-runbook"></a>Włączanie rozwiązania Update Management z poziomu elementu runbook

W tym artykule opisano, jak można użyć elementu Runbook, aby włączyć funkcję [Update Management](overview.md) dla maszyn wirtualnych w środowisku. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, musisz włączyć istniejącą maszynę wirtualną przy użyciu Update Management.

> [!NOTE]
> Podczas włączania Update Management tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](../how-to/region-mappings.md).

Ta metoda używa dwóch elementów Runbook:

* **Enable-MultipleSolution** — podstawowy element Runbook, który monituje o informacje o konfiguracji, wysyła zapytanie do określonej maszyny wirtualnej i wykonuje inne sprawdzenia poprawności, a następnie wywołuje element Runbook **enable-AutomationSolution** w celu skonfigurowania Update Management dla każdej maszyny wirtualnej w ramach określonej grupy zasobów.
* **Enable-AutomationSolution** — włącza Update Management dla co najmniej jednej maszyny wirtualnej określonej w docelowej grupie zasobów. Sprawdzanie wymagań wstępnych jest spełnione, sprawdza, czy Log Analytics rozszerzenie maszyny wirtualnej jest zainstalowane i instalowane, jeśli nie zostanie znalezione, i dodaje maszyny wirtualne do konfiguracji zakresu w określonym Log Analytics obszarze roboczym połączonym z kontem usługi Automation.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../automation-security-overview.md) do zarządzania maszynami.
* [Log Analytics obszar roboczy](../../azure-monitor/platform/design-logs-deployment.md)
* [Maszyna wirtualna](../../virtual-machines/windows/quick-create-portal.md).
* Dwa elementy zawartości automatyzacji, które są używane przez element Runbook **enable-AutomationSolution** . Ten element Runbook, jeśli jeszcze nie istnieje na koncie usługi Automation, jest automatycznie importowany przez element Runbook **enable-MultipleSolution** podczas pierwszego uruchomienia.
    * *LASolutionSubscriptionId*: Identyfikator subskrypcji, w której znajduje się obszar roboczy log Analytics.
    * *LASolutionWorkspaceId*: identyfikator obszaru roboczego log Analyticsu połączony z kontem usługi Automation.

    Te zmienne służą do konfigurowania obszaru roboczego dołączanej maszyny wirtualnej. Jeśli nie są one określone, skrypt najpierw szuka dowolnych maszyn wirtualnych dołączanych do Update Management w jej subskrypcji, po których następuje subskrypcja konta usługi Automation, a następnie wszystkie inne subskrypcje, do których konto użytkownika ma dostęp. Jeśli nie skonfigurowano prawidłowo, może to spowodować, że Twoje maszyny dołączyją się do niektórych losowo Log Analytics obszarów roboczych.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

1. W Azure Portal przejdź do **konta usługi Automation**. Na stronie **konta usługi Automation** wybierz swoje konto z listy.

2. Na koncie usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

3. Wybierz obszar roboczy Log Analytics, a następnie kliknij pozycję **Włącz**. Gdy Update Management jest włączona, transparent jest pokazywany.

    ![Włączanie rozwiązania Update Management](media/enable-from-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>Instalowanie i aktualizowanie modułów

Wymagane jest zaktualizowanie do najnowszych modułów platformy Azure i zaimportowanie modułu [AZ. OperationalInsights](/powershell/module/az.operationalinsights) w celu pomyślnego włączenia Update Management dla maszyn wirtualnych za pomocą elementu Runbook.

1. Na koncie usługi Automation wybierz pozycję **moduły** w obszarze **zasoby udostępnione**.

2. Wybierz pozycję **Aktualizuj moduły platformy Azure**, aby zaktualizować moduły platformy Azure do najnowszej wersji.

3. Kliknij przycisk **tak** , aby zaktualizować wszystkie istniejące moduły platformy Azure do najnowszej wersji.

    ![Aktualizowanie modułów](media/enable-from-runbook/update-modules.png)

4. Wróć do **modułów** w obszarze **udostępnione zasoby**.

5. Wybierz pozycję **Przeglądaj Galerię** , aby otworzyć galerię modułów.

6. Wyszukaj `Az.OperationalInsights` i zaimportuj ten moduł do konta usługi Automation.

    ![Importowanie modułu OperationalInsights](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Wybierz maszynę wirtualną platformy Azure do zarządzania

Po włączeniu Update Management można dodać maszynę wirtualną platformy Azure, aby otrzymywać aktualizacje.

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w sekcji **Zarządzanie aktualizacjami**.

2. Wybierz pozycję **Dodaj maszyny wirtualne platformy Azure** , aby dodać maszynę wirtualną.

3. Z listy wybierz maszynę wirtualną, a następnie kliknij pozycję **Włącz** , aby skonfigurować maszynę wirtualną do zarządzania.

   ![Włącz Update Management dla maszyny wirtualnej](media/enable-from-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > Jeśli spróbujesz włączyć inną funkcję przed ukończeniem instalacji Update Management, zostanie wyświetlony następujący komunikat: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-update-management"></a>Zaimportuj element Runbook, aby włączyć Update Management

1. Na koncie usługi Automation wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.

2. Wybierz pozycję **Przeglądaj galerię**.

3. Wyszukaj **aktualizację i śledzenie zmian**.

4. Wybierz element Runbook, a następnie kliknij pozycję **Importuj** na stronie **Wyświetl źródło** .

5. Kliknij przycisk **OK** , aby zaimportować element Runbook do konta usługi Automation.

   ![Importuj element Runbook dla Instalatora](media/enable-from-runbook/import-from-gallery.png)

6. Na stronie **element Runbook** wybierz element Runbook **enable-MultipleSolution** , a następnie kliknij przycisk **Edytuj**. W edytorze tekstu wybierz pozycję  **Publikuj**.

7. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak** , aby opublikować element Runbook.

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Aby można było uruchomić ten element Runbook, należy włączyć Update Management dla maszyny wirtualnej platformy Azure. Wymaga istniejącej maszyny wirtualnej i grupy zasobów z włączoną funkcją, aby skonfigurować co najmniej jedną maszynę wirtualną w docelowej grupie zasobów.

1. Otwórz element Runbook **enable-MultipleSolution** .

   ![Wiele elementów Runbook rozwiązania](media/enable-from-runbook/runbook-overview.png)

2. Kliknij przycisk Start i wprowadź wartości parametrów w następujących polach:

   * **VMNAME** — Nazwa istniejącej maszyny wirtualnej, która ma zostać dodana do Update Management. Pozostaw to pole puste, aby dodać wszystkie maszyny wirtualne w grupie zasobów.
   * **VMRESOURCEGROUP** — nazwa grupy zasobów dla maszyn wirtualnych do włączenia.
   * **Subskrypcji** — Identyfikator subskrypcji nowej maszyny wirtualnej do włączenia. Pozostaw to pole puste, aby użyć subskrypcji obszaru roboczego. Jeśli używasz innego identyfikatora subskrypcji, Dodaj konto Uruchom jako dla konta usługi Automation jako współautor dla subskrypcji.
   * **ALREADYONBOARDEDVM** — Nazwa maszyny wirtualnej, która została już ręcznie włączona dla aktualizacji.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** — nazwa grupy zasobów, do której należy maszyna wirtualna.
   * **Solutiontype** — wprowadź **aktualizacje**.

   ![Parametry elementu runbook Enable-MultipleSolution](media/enable-from-runbook/runbook-parameters.png)

3. Wybierz przycisk **OK**, aby uruchomić zadanie elementu runbook.

4. Monitoruj postęp zadania elementu Runbook i wszelkie błędy na stronie **zadań** .

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych](manage-updates-for-vm.md).

* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](../troubleshoot/update-management.md).
