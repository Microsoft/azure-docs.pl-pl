---
title: Włączanie Update Management Azure Automation z poziomu maszyny wirtualnej platformy Azure
description: W tym artykule opisano sposób włączania Update Management z maszyny wirtualnej platformy Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cab114e50852f293a3d1caf5bdc9a341f75f2557
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743967"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Włączanie Update Management z maszyny wirtualnej platformy Azure

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

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Sprawdź konfigurację zakresu

Update Management używa konfiguracji zakresu w obszarze roboczym, aby określić komputery, które mają być włączone dla tej funkcji. Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania, które jest używane do ograniczania zakresu funkcji do określonych komputerów. Aby uzyskać więcej informacji, zobacz [Working with Scope Configurations for Update Management](automation-scope-configurations-update-management.md).

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
* Aby poznać konfiguracje zakresów, zobacz [Working with Scope Configurations for Update Management](automation-scope-configurations-update-management.md).
* Jeśli obszar roboczy Log Analytics nie jest już potrzebny, zobacz instrukcje w polu [Odłącz obszar roboczy z konta usługi Automation dla Update Management](automation-unlink-workspace-update-management.md).
* Aby usunąć maszyny wirtualne z Update Management, zobacz [usuwanie maszyn wirtualnych z Update Management](automation-remove-vms-from-update-management.md).
* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](troubleshoot/update-management.md).
* Aby rozwiązać problemy z usługą Windows Update Agent, zobacz [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Aby rozwiązać problemy z agentem aktualizacji systemu Linux, zobacz[Rozwiązywanie problemów z agentem aktualizacji systemu Linux](troubleshoot/update-agent-issues-linux.md).
