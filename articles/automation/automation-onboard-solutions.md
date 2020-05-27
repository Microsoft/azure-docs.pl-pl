---
title: Włącz Update Management Azure Automation z elementu Runbook
description: W tym artykule opisano sposób włączania Update Management z elementu Runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 39ebdb6937b03d72365e9d3785af9571ebb66186
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836060"
---
# <a name="enable-update-management-from-a-runbook"></a>Włączanie rozwiązania Update Management z poziomu elementu runbook

W tym artykule opisano, jak można użyć elementu Runbook, aby włączyć funkcję [Update Management](automation-update-management.md) dla maszyn wirtualnych w środowisku. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Update Management. 

> [!NOTE]
> Podczas włączania Update Management tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do zarządzania maszynami.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

1. Na koncie usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz obszar roboczy Log Analytics, a następnie kliknij pozycję **Włącz**. Gdy Update Management jest włączona, wyświetlany jest niebieski baner. 

    ![Włączanie rozwiązania Update Management](media/automation-onboard-solutions/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Wybierz maszynę wirtualną platformy Azure do zarządzania

Po włączeniu Update Management można dodać maszynę wirtualną platformy Azure, aby otrzymywać aktualizacje.

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz pozycję **Dodaj maszyny wirtualne platformy Azure** , aby dodać maszynę wirtualną.

3. Wybierz maszynę wirtualną z listy i kliknij przycisk **Włącz** , aby skonfigurować maszynę wirtualną pod kątem aktualizacji. 

   ![Włącz Update Management dla maszyny wirtualnej](media/automation-onboard-solutions/enable-update.png)

    > [!NOTE]
    > Jeśli spróbujesz włączyć inną funkcję przed ukończeniem instalacji Update Management, zostanie wyświetlony następujący komunikat:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalowanie i aktualizowanie modułów

Wymagane jest zaktualizowanie do najnowszych modułów platformy Azure i zaimportowanie modułu [AZ. OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) w celu pomyślnego włączenia Update Management dla maszyn wirtualnych.

1. Na koncie usługi Automation wybierz pozycję **moduły** w obszarze **zasoby udostępnione**. 
2. Wybierz pozycję **Aktualizuj moduły platformy Azure**, aby zaktualizować moduły platformy Azure do najnowszej wersji. 
3. Kliknij przycisk **tak** , aby zaktualizować wszystkie istniejące moduły platformy Azure do najnowszej wersji.

    ![Aktualizowanie modułów](media/automation-onboard-solutions/update-modules.png)

4. Wróć do **modułów** w obszarze **udostępnione zasoby**. 
5. Wybierz pozycję **Przeglądaj Galerię** , aby otworzyć galerię modułów. 
6. Wyszukaj `Az.OperationalInsights` i zaimportuj ten moduł do konta usługi Automation.

    ![Importowanie modułu OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Zaimportuj element Runbook, aby włączyć Update Management

1. Na koncie usługi Automation wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
2. Wybierz pozycję **Przeglądaj galerię**.
3. Wyszukaj ciąg `update and change tracking`.
4. Wybierz element Runbook, a następnie kliknij pozycję **Importuj** na stronie Wyświetl źródło. 
5. Kliknij przycisk **OK** , aby zaimportować element Runbook do konta usługi Automation.

   ![Importuj element Runbook dla Instalatora](media/automation-onboard-solutions/import-from-gallery.png)

6. Na stronie elementu Runbook kliknij przycisk **Edytuj**, a następnie wybierz pozycję **Publikuj**. 
7. W okienku publikowanie elementu Runbook kliknij przycisk **tak** , aby opublikować element Runbook.

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Aby można było uruchomić ten element Runbook, należy włączyć Update Management dla maszyny wirtualnej platformy Azure. Wymaga istniejącej maszyny wirtualnej i grupy zasobów z włączoną funkcją dla parametrów.

1. Otwórz element Runbook **enable-MultipleSolution** .

   ![Wiele elementów Runbook rozwiązania](media/automation-onboard-solutions/runbook-overview.png)

2. Kliknij przycisk Start i wprowadź wartości parametrów w następujących polach:

   * **VMNAME** — Nazwa istniejącej maszyny wirtualnej, która ma zostać dodana do Update Management. Pozostaw to pole puste, aby dodać wszystkie maszyny wirtualne w grupie zasobów.
   * **VMRESOURCEGROUP** — nazwa grupy zasobów dla maszyn wirtualnych do włączenia.
   * **Subskrypcji** — Identyfikator subskrypcji nowej maszyny wirtualnej do włączenia. Pozostaw to pole puste, aby użyć subskrypcji obszaru roboczego. Jeśli używasz innego identyfikatora subskrypcji, Dodaj konto Uruchom jako dla konta usługi Automation jako współautor dla subskrypcji.
   * **ALREADYONBOARDEDVM** — Nazwa maszyny wirtualnej, która została już ręcznie włączona dla aktualizacji.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** — nazwa grupy zasobów, do której należy maszyna wirtualna.
   * **Solutiontype** — wprowadź **aktualizacje**.

   ![Parametry elementu runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

3. Wybierz przycisk **OK**, aby uruchomić zadanie elementu runbook.
4. Monitoruj postęp oraz wszelkie błędy na stronie zadania elementu runbook.

## <a name="next-steps"></a>Następne kroki

* Aby zaplanować element Runbook, zobacz [Zarządzanie harmonogramami w Azure Automation](shared-resources/schedules.md).
* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
* Aby poznać konfiguracje zakresów, zobacz [Working with Scope Configurations](automation-scope-configurations-update-management.md).
* Jeśli obszar roboczy Log Analytics nie jest już potrzebny, zobacz instrukcje w polu [Odłącz obszar roboczy z konta usługi Automation dla Update Management](automation-unlink-workspace-update-management.md).
* Aby usunąć maszyny wirtualne z Update Management, zobacz [usuwanie maszyn wirtualnych z Update Management](automation-remove-vms-from-update-management.md).
* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](troubleshoot/update-management.md).
* Aby rozwiązać problemy z usługą Windows Update Agent, zobacz [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Aby rozwiązać problemy z agentem aktualizacji systemu Linux, zobacz[Rozwiązywanie problemów z agentem aktualizacji systemu Linux](troubleshoot/update-agent-issues-linux.md).