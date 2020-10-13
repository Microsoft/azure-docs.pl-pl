---
title: Włączanie Azure Automation Change Tracking i spisu z elementu Runbook
description: W tym artykule opisano sposób włączania Change Tracking i spisu z elementu Runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 92149289c6c422179ddb3562274020acf779f10b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186286"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Włączanie śledzenia zmian i spisu z poziomu elementu runbook

W tym artykule opisano, jak można użyć elementu Runbook do włączenia funkcji [Change Tracking i spisu](change-tracking.md) dla maszyn wirtualnych w środowisku. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Change Tracking i spisu. 

> [!NOTE]
> Podczas włączania Change Tracking i spisu tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](./index.yml) do zarządzania maszynami.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory 

1. W Azure Portal wybierz pozycję **konta usługi Automation**, a następnie na liście Wybierz swoje konto usługi Automation.
1. Wybierz pozycję **spis** w obszarze **Zarządzanie konfiguracją**.
1. Wybierz istniejący obszar roboczy Log Analytics lub Utwórz nowy. 
1. Kliknij przycisk **Włącz**.

    ![Włączanie rozwiązania Change Tracking and Inventory](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Wybierz maszynę wirtualną platformy Azure do zarządzania

Przy włączonej Change Tracking i spisie możesz dodać maszynę wirtualną platformy Azure do zarządzania przez funkcję.

1. Z poziomu konta usługi Automation wybierz pozycję **śledzenie zmian** lub **spis** w obszarze **Zarządzanie konfiguracją**.

2. Kliknij pozycję **Dodaj maszyny wirtualne platformy Azure** , aby dodać maszynę wirtualną.

3. Z listy wybierz maszynę wirtualną, a następnie kliknij pozycję **Włącz**. Ta akcja powoduje włączenie Change Tracking i spisu dla maszyny wirtualnej.

   ![Włączanie Change Tracking i spisu dla maszyny wirtualnej](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Jeśli spróbujesz włączyć inną funkcję przed rozpoczęciem instalacji Change Tracking i zakończeniu spisu, zostanie wyświetlony następujący komunikat: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalowanie i aktualizowanie modułów

Wymagane jest zaktualizowanie do najnowszych modułów platformy Azure i zaimportowanie modułu [AZ. OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) w celu pomyślnego włączenia Change Tracking i spisu dla maszyny wirtualnej.

1. Na koncie usługi Automation wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. 
2. Wybierz pozycję **Aktualizuj moduły platformy Azure**, aby zaktualizować moduły platformy Azure do najnowszej wersji. 
3. Kliknij przycisk **tak** , aby zaktualizować wszystkie istniejące moduły platformy Azure do najnowszej wersji.

    ![Aktualizowanie modułów](media/automation-enable-changes-from-runbook/update-modules.png)

4. Wróć do **modułów** w obszarze **udostępnione zasoby**. 
5. Wybierz pozycję **Przeglądaj Galerię** , aby otworzyć galerię modułów. 
6. Wyszukaj AZ. OperationalInsights i zaimportuj ten moduł do konta usługi Automation.

    ![Importowanie modułu OperationalInsights](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importowanie elementu Runbook w celu włączenia Change Tracking i spisu

1. Na koncie usługi Automation wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
2. Wybierz pozycję **Przeglądaj galerię**.
3. Wyszukaj ciąg `update and change tracking`.
4. Wybierz element Runbook, a następnie kliknij pozycję **Importuj** na stronie Wyświetl źródło. 
5. Kliknij przycisk **OK** , aby zaimportować element Runbook do konta usługi Automation.

   ![Importuj element Runbook dla Instalatora](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Na stronie elementu Runbook kliknij przycisk **Edytuj**, a następnie wybierz pozycję **Publikuj**. 
7. W okienku publikowanie elementu Runbook kliknij przycisk **tak** , aby opublikować element Runbook.

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Aby można było uruchomić ten element Runbook, należy włączyć Change Tracking i spis dla maszyny wirtualnej platformy Azure. Wymaga istniejącej maszyny wirtualnej i grupy zasobów z włączoną funkcją dla parametrów.

1. Otwórz element Runbook **enable-MultipleSolution** .

   ![Wiele elementów runbook rozwiązań](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Kliknij przycisk Start i wprowadź wartości parametrów w następujących polach:

   * **VMNAME** — Nazwa istniejącej maszyny wirtualnej, która ma zostać dodana do Change Tracking i spisu. Pozostaw to pole puste, aby dodać wszystkie maszyny wirtualne w grupie zasobów.
   * **VMRESOURCEGROUP** — nazwa grupy zasobów dla maszyn wirtualnych do włączenia.
   * **Subskrypcji** — Identyfikator subskrypcji nowej maszyny wirtualnej do włączenia. Pozostaw to pole puste, aby użyć subskrypcji obszaru roboczego. Jeśli używasz innego identyfikatora subskrypcji, Dodaj konto Uruchom jako dla konta usługi Automation jako współautor dla subskrypcji.
   * **ALREADYONBOARDEDVM** — Nazwa maszyny wirtualnej, która została już ręcznie włączona dla zmian.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** — nazwa grupy zasobów, do której należy maszyna wirtualna.
   * **Solutiontype** — wprowadź **śledzenia zmian**.

   ![Parametry elementu runbook Enable-MultipleSolution](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Wybierz przycisk **OK**, aby uruchomić zadanie elementu runbook.
1. Monitoruj postęp oraz wszelkie błędy na stronie zadania elementu runbook.

## <a name="next-steps"></a>Następne kroki

* Aby zaplanować element Runbook, zobacz [Zarządzanie harmonogramami w Azure Automation](shared-resources/schedules.md).
* Aby uzyskać szczegółowe informacje na temat pracy z funkcją, zobacz [zarządzanie Change Tracking i spisem](change-tracking-file-contents.md).
* Rozwiązywanie ogólnych problemów z funkcją można znaleźć w temacie [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md).
