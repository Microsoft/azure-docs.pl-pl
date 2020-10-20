---
title: Włączanie Azure Automation Change Tracking i spisu z elementu Runbook
description: W tym artykule opisano sposób włączania Change Tracking i spisu z elementu Runbook.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 842b0a92ba4a2cb6b3ceb54675ef95f9c8275311
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210081"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Włączanie śledzenia zmian i spisu z poziomu elementu runbook

W tym artykule opisano, jak można użyć elementu Runbook do włączenia [Change Tracking i spisu](overview.md) maszyn wirtualnych w środowisku. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Change Tracking i spisu.

> [!NOTE]
> Podczas włączania Change Tracking i spisu tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](../how-to/region-mappings.md).

Ta metoda używa dwóch elementów Runbook:

* **Enable-MultipleSolution** — podstawowy element Runbook, który monituje o informacje o konfiguracji, wysyła zapytanie do określonej maszyny wirtualnej i wykonuje inne sprawdzenia poprawności, a następnie wywołuje element Runbook **enable-AutomationSolution** w celu skonfigurowania Change Tracking i spisu dla każdej maszyny wirtualnej w ramach określonej grupy zasobów.
* **Enable-AutomationSolution** — włącza Change Tracking i spis dla co najmniej jednej maszyny wirtualnej określonej w docelowej grupie zasobów. Sprawdzanie wymagań wstępnych jest spełnione, sprawdza, czy Log Analytics rozszerzenie maszyny wirtualnej jest zainstalowane i instalowane, jeśli nie zostanie znalezione, i dodaje maszyny wirtualne do konfiguracji zakresu w określonym Log Analytics obszarze roboczym połączonym z kontem usługi Automation.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../automation-security-overview.md) do zarządzania maszynami.
* [Log Analytics obszar roboczy](../../azure-monitor/platform/design-logs-deployment.md)
* [Maszyna wirtualna](../../virtual-machines/windows/quick-create-portal.md).
* Dwa elementy zawartości automatyzacji, które są używane przez element Runbook **enable-AutomationSolution** . Ten element Runbook, jeśli jeszcze nie istnieje na koncie usługi Automation, jest automatycznie importowany przez element Runbook **enable-MultipleSolution** podczas pierwszego uruchomienia.
    * *LASolutionSubscriptionId*: Identyfikator subskrypcji, w której znajduje się obszar roboczy log Analytics.
    * *LASolutionWorkspaceId*: identyfikator obszaru roboczego log Analyticsu połączony z kontem usługi Automation.

    Te zmienne służą do konfigurowania obszaru roboczego dołączanej maszyny wirtualnej. Jeśli nie są one określone, skrypt najpierw szuka dowolnych maszyn wirtualnych dołączanych do Change Tracking i spisu w subskrypcji, po których następuje subskrypcja konta usługi Automation, a następnie wszystkie inne subskrypcje, do których konto użytkownika ma dostęp. Jeśli nie skonfigurowano prawidłowo, może to spowodować, że Twoje maszyny dołączyją się do niektórych losowo Log Analytics obszarów roboczych.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

1. W Azure Portal przejdź do **konta usługi Automation**. Na stronie **konta usługi Automation** wybierz swoje konto z listy.

1. Na koncie usługi Automation wybierz pozycję **spis** lub **Change Tracking** w obszarze **Zarządzanie konfiguracją**.

1. Wybierz obszar roboczy Log Analytics, a następnie kliknij pozycję **Włącz**. Po włączeniu spisu lub Change Tracking jest wyświetlany transparent.

    ![Włączanie rozwiązania Change Tracking and Inventory](media/enable-from-automation-account/enable-feature.png)

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

Przy włączonej Change Tracking i spisie możesz dodać maszynę wirtualną platformy Azure do zarządzania przez funkcję.

1. Z poziomu konta usługi Automation wybierz pozycję **śledzenie zmian** lub **spis** w obszarze **Zarządzanie konfiguracją**.

2. Kliknij pozycję **Dodaj maszyny wirtualne platformy Azure** , aby dodać maszynę wirtualną.

3. Z listy wybierz maszynę wirtualną, a następnie kliknij pozycję **Włącz**. Ta akcja powoduje włączenie Change Tracking i spisu dla maszyny wirtualnej.

   ![Włączanie Change Tracking i spisu dla maszyny wirtualnej](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > Jeśli spróbujesz włączyć inną funkcję przed rozpoczęciem instalacji Change Tracking i zakończeniu spisu, zostanie wyświetlony następujący komunikat: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importowanie elementu Runbook w celu włączenia Change Tracking i spisu

1. Na koncie usługi Automation wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.

2. Wybierz pozycję **Przeglądaj galerię**.

3. Wyszukaj **aktualizację i śledzenie zmian**.

4. Wybierz element Runbook, a następnie kliknij pozycję **Importuj** na stronie **Wyświetl źródło** .

5. Kliknij przycisk **OK** , aby zaimportować element Runbook do konta usługi Automation.

   ![Importuj element Runbook dla Instalatora](media/enable-from-runbook/import-from-gallery.png)

6. Na stronie **element Runbook** wybierz element Runbook **enable-MultipleSolution** , a następnie kliknij przycisk **Edytuj**. W edytorze tekstu wybierz pozycję  **Publikuj**.

7. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak** , aby opublikować element Runbook.

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Aby można było uruchomić ten element Runbook, należy włączyć Change Tracking i spis dla maszyny wirtualnej platformy Azure. Wymaga istniejącej maszyny wirtualnej i grupy zasobów z włączoną funkcją, aby skonfigurować co najmniej jedną maszynę wirtualną w docelowej grupie zasobów.

1. Otwórz element Runbook **enable-MultipleSolution** .

   ![Wiele elementów Runbook rozwiązania](media/enable-from-runbook/runbook-overview.png)

2. Kliknij przycisk Start i wprowadź wartości parametrów w następujących polach:

   * **VMNAME** — Nazwa istniejącej maszyny wirtualnej, która ma zostać dodana do Change Tracking i spisu. Pozostaw to pole puste, aby dodać wszystkie maszyny wirtualne w grupie zasobów.
   * **VMRESOURCEGROUP** — nazwa grupy zasobów dla maszyn wirtualnych do włączenia.
   * **Subskrypcji** — Identyfikator subskrypcji nowej maszyny wirtualnej do włączenia. Pozostaw to pole puste, aby użyć subskrypcji obszaru roboczego. Jeśli używasz innego identyfikatora subskrypcji, Dodaj konto Uruchom jako dla konta usługi Automation jako współautor dla subskrypcji.
   * **ALREADYONBOARDEDVM** — Nazwa maszyny wirtualnej, która została już ręcznie włączona dla aktualizacji.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** — nazwa grupy zasobów, do której należy maszyna wirtualna.
   * **Solutiontype** — wprowadź **śledzenia zmian**.

   ![Parametry elementu runbook Enable-MultipleSolution](media/enable-from-runbook/runbook-parameters.png)

3. Wybierz przycisk **OK**, aby uruchomić zadanie elementu runbook.

4. Monitoruj postęp zadania elementu Runbook i wszelkie błędy na stronie **zadań** .

## <a name="next-steps"></a>Następne kroki

* Aby zaplanować element Runbook, zobacz [Zarządzanie harmonogramami w Azure Automation](../shared-resources/schedules.md).

* Aby uzyskać szczegółowe informacje na temat pracy z funkcją [Manage Change Tracking](manage-change-tracking.md) , zobacz Zarządzanie [zapasami Change Tracking i zarządzanie nimi](manage-inventory-vms.md).

* Rozwiązywanie ogólnych problemów z funkcją można znaleźć w temacie [Rozwiązywanie problemów dotyczących Change Tracking i spisu](../troubleshoot/change-tracking.md).


