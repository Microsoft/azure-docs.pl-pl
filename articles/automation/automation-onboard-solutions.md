---
title: Dołączanie rozwiązań aktualizacji, śledzenia zmian i spisu do Azure Automation
description: Dowiedz się, jak dołączać rozwiązania aktualizacji i śledzenia zmian w usłudze Azure Automation.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81457624"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Dołączanie rozwiązań aktualizacji, śledzenia zmian i spisu do Azure Automation

W ramach tego samouczka nauczysz się automatycznie dołączać rozwiązania aktualizacji, śledzenia zmian i spisu dla maszyn wirtualnych do usługi Azure Automation:

> [!div class="checklist"]
> * Dołączanie maszyny wirtualnej platformy Azure
> * Włączanie rozwiązań
> * Instalowanie i aktualizowanie modułów
> * Importowanie elementu runbook dołączania
> * Uruchamianie elementu runbook

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do zarządzania maszynami.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) do dołączenia.

## <a name="onboard-an-azure-vm"></a>Dołączanie maszyny wirtualnej platformy Azure

Istnieje wiele sposobów dołączania maszyn. Rozwiązanie można dołączyć [z maszyny wirtualnej](automation-onboard-solutions-from-vm.md), [z wyników przeglądania wielu maszyn](automation-onboard-solutions-from-browse.md) [z konta usługi Automation](automation-onboard-solutions-from-automation-account.md) lub przy użyciu elementu runbook. W tym samouczki przedstawiono procedurę włączania rozwiązania Update Management przy użyciu elementu runbook. Aby można było dołączać maszyny wirtualne platformy Azure na dużą skalę, do istniejącej maszyny wirtualnej należy dołączyć rozwiązanie Change tracking lub Update management. W tym kroku dołączysz maszynę wirtualną z rozwiązaniami Update management i Change tracking.

### <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

Rozwiązania Change Tracking i spis umożliwiają [śledzenie zmian](automation-vm-change-tracking.md) i [spisu](automation-vm-inventory.md) na maszynach wirtualnych. W tym kroku włączasz rozwiązania na maszynie wirtualnej.

1. W Azure Portal wybierz pozycję **konta usługi Automation**, a następnie na liście Wybierz swoje konto usługi Automation.
1. Wybierz pozycję **spis** w obszarze **Zarządzanie konfiguracją**.
1. Wybierz istniejący obszar roboczy Log Analytics lub Utwórz nowy. 
1. Kliknij przycisk **Włącz**.

    ![Dołączanie rozwiązania Update](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

Rozwiązanie Update Management umożliwia zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych z systemem Windows platformy Azure. Można ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji i przejrzeć wyniki wdrażania, aby sprawdzić, czy aktualizacje zostały zastosowane pomyślnie do maszyny wirtualnej. W tym kroku włączysz rozwiązanie dla maszyny wirtualnej.

1. Na koncie usługi Automation wybierz pozycję **Update Management** w sekcji **Update Management** .
1. Wybrany obszar roboczy usługi log Analytics jest obszarem roboczym używanym w poprzednim kroku. Kliknij przycisk **Włącz**, aby dołączyć rozwiązanie Update management. Podczas instalowania rozwiązania Update management jest wyświetlany niebieski baner. 

    ![Dołączanie rozwiązania Update](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>Wybieranie maszyny wirtualnej platformy Azure, która ma być zarządzana

Teraz, gdy rozwiązania są włączone, można dołączyć maszynę wirtualną platformy Azure do tych rozwiązań.

1. Na koncie usługi Automation wybierz pozycję **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**. 
2. Na stronie śledzenie zmian kliknij pozycję **Dodaj maszyny wirtualne platformy Azure** , aby dodać maszynę wirtualną.

3. Z listy wybierz maszynę wirtualną, a następnie kliknij pozycję **Włącz**. Ta akcja włącza rozwiązania Change Tracking i spisu dla maszyny wirtualnej.

   ![Włączanie rozwiązania Update dla maszyny wirtualnej](media/automation-onboard-solutions/enable-change-tracking.png)

4. Po zakończeniu powiadomienia o dołączeniu do maszyny wirtualnej wybierz pozycję **Update Management** w obszarze **Update Management**.

5. Wybierz pozycję **Dodaj maszyny wirtualne platformy Azure** , aby dodać maszynę wirtualną.

6. Wybierz maszynę wirtualną z listy i wybierz przycisk **Włącz**. Ta akcja włącza rozwiązanie Update Management dla maszyny wirtualnej.

   ![Włączanie rozwiązania Update dla maszyny wirtualnej](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Jeśli nie poczekasz na zakończenie innych rozwiązań, podczas włączania następnego rozwiązania zostanie wyświetlony komunikat:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalowanie i aktualizowanie modułów

Wymagane jest zaktualizowanie do najnowszych modułów platformy Azure i zaimportowanie modułu [AZ. OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) w celu pomyślnego zaautomatyzowania dołączania rozwiązania.

## <a name="update-azure-modules"></a>Aktualizowanie modułów platformy Azure

1. Na koncie usługi Automation wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. 
2. Wybierz pozycję **Aktualizuj moduły platformy Azure**, aby zaktualizować moduły platformy Azure do najnowszej wersji. 
3. Kliknij przycisk **tak** , aby zaktualizować wszystkie istniejące moduły platformy Azure do najnowszej wersji.

![Aktualizowanie modułów](media/automation-onboard-solutions/update-modules.png) A

### <a name="install-azoperationalinsights-module"></a>Zainstaluj AZ. OperationalInsights module

1. Na koncie usługi Automation wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. 
2. Wybierz pozycję **Przeglądaj Galerię** , aby otworzyć galerię modułów. 
3. Wyszukaj AZ. OperationalInsights i zaimportuj ten moduł do konta usługi Automation.

![Importowanie modułu OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importowanie elementu runbook dołączania

1. Na koncie usługi Automation wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
1. Wybierz pozycję **Przeglądaj galerię**.
1. Wyszukaj `update and change tracking`.
3. Wybierz element Runbook, a następnie kliknij pozycję **Importuj** na stronie Wyświetl źródło. 
4. Kliknij przycisk **OK** , aby zaimportować element Runbook do konta usługi Automation.

   ![Importowanie elementu runbook dołączania](media/automation-onboard-solutions/import-from-gallery.png)

6. Na stronie elementu Runbook kliknij przycisk **Edytuj**, a następnie wybierz pozycję **Publikuj**. 
7. W okienku publikowanie elementu Runbook kliknij przycisk **tak** , aby opublikować element Runbook.

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Aby można było uruchomić ten element Runbook, należy do maszyny wirtualnej platformy Azure dodać lub dodać rozwiązania do śledzenia zmian. Wymaga istniejącej maszyny wirtualnej i grupy zasobów z rozwiązaniem dołączonym dla parametrów.

1. Otwórz element Runbook **enable-MultipleSolution** .

   ![Wiele elementów runbook rozwiązań](media/automation-onboard-solutions/runbook-overview.png)

1. Kliknij przycisk Start i wprowadź następujące wartości dla parametrów.

   * **VMNAME** — pozostaw puste. Nazwa istniejącej maszyny wirtualnej do dołączenia do rozwiązania aktualizacji lub śledzenia zmian. Pozostawienie tej wartości pustej spowoduje dołączenie wszystkich maszyn wirtualnych w grupie zasobów.
   * **VMRESOURCEGROUP** — nazwa grupy zasobów dla maszyn wirtualnych, które mają zostać dołączone.
   * **SUBSCRIPTIONID** — pozostaw puste. Identyfikator subskrypcji nowej maszyny wirtualnej, która ma zostać dołączona. Jeśli to pole pozostawiono puste, używana jest subskrypcja obszaru roboczego. Jeśli podano inny identyfikator subskrypcji, konto Uruchom jako dla tego konta należy dodać jako współautora również dla tej subskrypcji.
   * **ALREADYONBOARDEDVM** — nazwa maszyny wirtualnej, która została ręcznie dołączona do rozwiązania Updates lub ChangeTracking.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** — nazwa grupy zasobów, do której należy maszyna wirtualna.
   * **Solutiontype** — wprowadź **aktualizacje** lub **śledzenia zmian**.

   ![Parametry elementu runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Wybierz przycisk **OK**, aby uruchomić zadanie elementu runbook.
1. Monitoruj postęp oraz wszelkie błędy na stronie zadania elementu runbook.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć maszynę wirtualną z Update Management:

1. W obszarze roboczym Log Analytics Usuń maszynę wirtualną z zapisanego wyszukiwania konfiguracji `MicrosoftDefaultScopeConfig-Updates`zakresu. Zapisane wyszukiwania można znaleźć **ogólnie** w obszarze roboczym.
2. Usuń [agenta log Analytics dla systemu Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) lub [agenta log Analytics dla Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Ręczne dołączanie maszyny wirtualnej platformy Azure.
> * Instalowanie i aktualizowanie wymaganych modułów platformy Azure.
> * Importowanie elementu runbook, który służy do dołączania maszyn wirtualnych platformy Azure.
> * Uruchamianie elementu runbook, który automatycznie dołącza maszyny wirtualne platformy Azure.

Skorzystaj z tego linku, aby dowiedzieć się więcej o planowaniu elementów runbook.

> [!div class="nextstepaction"]
> [Planowanie elementów runbook](automation-schedules.md).
