---
title: Włącz Update Management Azure Automation z elementu Runbook
description: W tym artykule opisano sposób włączania Update Management z elementu Runbook.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: a5b6fe5cf83eaa18b34a00767e14e75737aa055e
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450669"
---
# <a name="enable-update-management-from-a-runbook"></a>Włączanie rozwiązania Update Management z poziomu elementu runbook

W tym artykule opisano, jak można użyć elementu Runbook, aby włączyć funkcję [Update Management](update-mgmt-overview.md) dla maszyn wirtualnych w środowisku. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Update Management. 

> [!NOTE]
> Podczas włączania Update Management tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../index.yml) do zarządzania maszynami.
* [Maszyna wirtualna](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

1. Na koncie usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz obszar roboczy Log Analytics, a następnie kliknij pozycję **Włącz**. Gdy Update Management jest włączona, wyświetlany jest niebieski baner.

    ![Włączanie rozwiązania Update Management](media/update-mgmt-enable-runbook/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Wybierz maszynę wirtualną platformy Azure do zarządzania

Po włączeniu Update Management można dodać maszynę wirtualną platformy Azure, aby otrzymywać aktualizacje.

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz pozycję **Dodaj maszyny wirtualne platformy Azure** , aby dodać maszynę wirtualną.

3. Wybierz maszynę wirtualną z listy i kliknij przycisk **Włącz** , aby skonfigurować maszynę wirtualną pod kątem aktualizacji.

   ![Włącz Update Management dla maszyny wirtualnej](media/update-mgmt-enable-runbook/enable-update.png)

    > [!NOTE]
    > Jeśli spróbujesz włączyć inną funkcję przed ukończeniem instalacji Update Management, zostanie wyświetlony następujący komunikat:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalowanie i aktualizowanie modułów

Wymagane jest zaktualizowanie do najnowszych modułów platformy Azure i zaimportowanie modułu [AZ. OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) w celu pomyślnego włączenia Update Management dla maszyn wirtualnych.

1. Na koncie usługi Automation wybierz pozycję **moduły** w obszarze **zasoby udostępnione**.
2. Wybierz pozycję **Aktualizuj moduły platformy Azure**, aby zaktualizować moduły platformy Azure do najnowszej wersji.
3. Kliknij przycisk **tak** , aby zaktualizować wszystkie istniejące moduły platformy Azure do najnowszej wersji.

    ![Aktualizowanie modułów](media/update-mgmt-enable-runbook/update-modules.png)

4. Wróć do **modułów** w obszarze **udostępnione zasoby**.
5. Wybierz pozycję **Przeglądaj Galerię** , aby otworzyć galerię modułów.
6. Wyszukaj `Az.OperationalInsights` i zaimportuj ten moduł do konta usługi Automation.

    ![Importowanie modułu OperationalInsights](media/update-mgmt-enable-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Zaimportuj element Runbook, aby włączyć Update Management

1. Na koncie usługi Automation wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
2. Wybierz pozycję **Przeglądaj galerię**.
3. Wyszukaj ciąg `update and change tracking`.
4. Wybierz element Runbook, a następnie kliknij pozycję **Importuj** na stronie Wyświetl źródło.
5. Kliknij przycisk **OK** , aby zaimportować element Runbook do konta usługi Automation.

   ![Importuj element Runbook dla Instalatora](media/update-mgmt-enable-runbook/import-from-gallery.png)

6. Na stronie elementu Runbook kliknij przycisk **Edytuj**, a następnie wybierz pozycję **Publikuj**.
7. W okienku publikowanie elementu Runbook kliknij przycisk **tak** , aby opublikować element Runbook.

## <a name="start-the-runbook"></a>Uruchamianie elementu runbook

Aby można było uruchomić ten element Runbook, należy włączyć Update Management dla maszyny wirtualnej platformy Azure. Wymaga istniejącej maszyny wirtualnej i grupy zasobów z włączoną funkcją dla parametrów.

1. Otwórz element Runbook **enable-MultipleSolution** .

   ![Wiele elementów Runbook rozwiązania](media/update-mgmt-enable-runbook/runbook-overview.png)

2. Kliknij przycisk Start i wprowadź wartości parametrów w następujących polach:

   * **VMNAME** — Nazwa istniejącej maszyny wirtualnej, która ma zostać dodana do Update Management. Pozostaw to pole puste, aby dodać wszystkie maszyny wirtualne w grupie zasobów.
   * **VMRESOURCEGROUP** — nazwa grupy zasobów dla maszyn wirtualnych do włączenia.
   * **Subskrypcji** — Identyfikator subskrypcji nowej maszyny wirtualnej do włączenia. Pozostaw to pole puste, aby użyć subskrypcji obszaru roboczego. Jeśli używasz innego identyfikatora subskrypcji, Dodaj konto Uruchom jako dla konta usługi Automation jako współautor dla subskrypcji.
   * **ALREADYONBOARDEDVM** — Nazwa maszyny wirtualnej, która została już ręcznie włączona dla aktualizacji.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** — nazwa grupy zasobów, do której należy maszyna wirtualna.
   * **Solutiontype** — wprowadź **aktualizacje**.

   ![Parametry elementu runbook Enable-MultipleSolution](media/update-mgmt-enable-runbook/runbook-parameters.png)

3. Wybierz przycisk **OK**, aby uruchomić zadanie elementu runbook.

4. Monitoruj postęp zadania elementu Runbook i wszelkie błędy na stronie **zadań** .

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych](update-mgmt-manage-updates-for-vm.md).

* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](../troubleshoot/update-management.md).
