---
title: Włączanie Update Management Azure Automation z poziomu maszyny wirtualnej platformy Azure
description: W tym artykule opisano sposób włączania Update Management z maszyny wirtualnej platformy Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 254ff2906d2b80b6c34ba930daffbb2c65f2b22f
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117926"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Włączanie rozwiązania Update Management z poziomu maszyny wirtualnej platformy Azure

W tym artykule opisano, jak można użyć maszyny wirtualnej platformy Azure, aby włączyć funkcję [Update Management](automation-update-management.md) na innych maszynach. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Update Management. 

> [!NOTE]
> Podczas włączania Update Management tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do zarządzania maszynami.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **maszyny wirtualne** lub Wyszukaj i wybierz pozycję **maszyny wirtualne** ze strony głównej.

2. Wybierz maszynę wirtualną, dla której chcesz włączyć Update Management. Maszyny wirtualne mogą znajdować się w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation. Ty

3. Na stronie maszyna wirtualna w obszarze **operacje**wybierz pozycję **Update Management**.

4. Musisz mieć `Microsoft.OperationalInsights/workspaces/read` uprawnienie do określenia, czy maszyna wirtualna jest włączona dla obszaru roboczego. Aby dowiedzieć się więcej o dodatkowych uprawnieniach, które są wymagane, zobacz [uprawnienia wymagane do włączania maszyn](automation-role-based-access-control.md#feature-setup-permissions). Aby dowiedzieć się, jak jednocześnie włączyć wiele komputerów, zobacz [włączanie Update Management z konta usługi Automation](automation-onboard-solutions-from-automation-account.md).

5. Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć Update Management. Ukończenie instalacji może potrwać do 15 minut. 

    ![Włączanie rozwiązania Update Management](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Ograniczanie zakresu wdrożenia

Update Management używa konfiguracji zakresu w obszarze roboczym, aby określić komputery, na których mają zostać odebrane aktualizacje. Aby uzyskać więcej informacji, zobacz [Ograniczanie zakresu wdrożenia Update Management](automation-scope-configurations-update-management.md).

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
* Aby poznać konfiguracje zakresów, zobacz [ograniczanie Update Management zakresu wdrożenia](automation-scope-configurations-update-management.md).
* Jeśli obszar roboczy Log Analytics nie jest już potrzebny, zobacz instrukcje w polu [Odłącz obszar roboczy z konta usługi Automation dla Update Management](automation-unlink-workspace-update-management.md).
* Aby usunąć maszyny wirtualne z Update Management, zobacz [usuwanie maszyn wirtualnych z Update Management](automation-remove-vms-from-update-management.md).
* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](troubleshoot/update-management.md).
* Aby rozwiązać problemy z usługą Windows Update Agent, zobacz [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Aby rozwiązać problemy z agentem aktualizacji systemu Linux, zobacz[Rozwiązywanie problemów z agentem aktualizacji systemu Linux](troubleshoot/update-agent-issues-linux.md).
