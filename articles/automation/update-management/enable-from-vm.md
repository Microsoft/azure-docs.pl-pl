---
title: Włączanie Update Management Azure Automation z poziomu maszyny wirtualnej platformy Azure
description: W tym artykule opisano sposób włączania Update Management z maszyny wirtualnej platformy Azure.
services: automation
ms.subservice: update-management
ms.date: 11/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 39b5b1f988a118e609015f19a086fda434797356
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050266"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Włączanie rozwiązania Update Management z poziomu maszyny wirtualnej platformy Azure

W tym artykule opisano, jak włączyć funkcję [Update Management](overview.md) na co najmniej jednej maszynie wirtualnej platformy Azure (VM). Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, musisz włączyć istniejącą maszynę wirtualną platformy Azure przy użyciu Update Management.

> [!NOTE]
> Podczas włączania Update Management tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../automation-security-overview.md) do zarządzania maszynami.
* [Maszyna wirtualna](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-the-feature-for-deployment"></a>Włącz funkcję wdrożenia

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **maszyny wirtualne** lub Wyszukaj i wybierz pozycję **maszyny wirtualne** ze strony głównej.

2. Wybierz maszynę wirtualną, dla której chcesz włączyć Update Management. Maszyny wirtualne mogą znajdować się w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation. Ty

3. Na stronie maszyna wirtualna w obszarze **operacje** wybierz pozycję **gość + aktualizacje hosta**.

    ![Wybierz pozycję Gość + aktualizacje hosta z okienka po lewej stronie](media/enable-from-vm/select-guest-and-os-updates.png)

4. Musisz mieć `Microsoft.OperationalInsights/workspaces/read` uprawnienie do określenia, czy maszyna wirtualna jest włączona dla obszaru roboczego. Aby dowiedzieć się więcej o dodatkowych uprawnieniach, które są wymagane, zobacz [uprawnienia wymagane do włączania maszyn](../automation-role-based-access-control.md#feature-setup-permissions). Aby dowiedzieć się, jak jednocześnie włączyć wiele komputerów, zobacz [włączanie Update Management z konta usługi Automation](./enable-from-automation-account.md).

5. Na stronie Włączanie Update Management wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć Update Management. Po włączeniu Update Management może upłynąć około 15 minut, zanim będzie możliwe wyświetlenie oceny aktualizacji z maszyny wirtualnej.

    ![Włączanie rozwiązania Update Management](media/enable-from-vm/enable-update-management.png)

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](manage-updates-for-vm.md).

* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](../troubleshoot/update-management.md).