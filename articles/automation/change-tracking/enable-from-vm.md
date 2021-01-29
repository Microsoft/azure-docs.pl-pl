---
title: Włączanie Azure Automation Change Tracking i spisu na podstawie maszyny wirtualnej platformy Azure
description: W tym artykule opisano sposób włączania Change Tracking i spisu na maszynie wirtualnej platformy Azure.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 61b45d6f6414b1e8e1f48f6d46957b21b9b8c58b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052502"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Włączanie śledzenia zmian i spisu na maszynie wirtualnej platformy Azure

W tym artykule opisano, jak można użyć maszyny wirtualnej platformy Azure w celu włączenia [Change Tracking i spisu](overview.md) na innych maszynach. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Change Tracking i spisu.

> [!NOTE]
> Podczas włączania Change Tracking i spisu tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../automation-security-overview.md) do zarządzania maszynami.
* [Maszyna wirtualna](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **maszyny wirtualne** lub Wyszukaj i wybierz pozycję **maszyny wirtualne** ze strony głównej.

2. Wybierz maszynę wirtualną, dla której chcesz włączyć Change Tracking i spis. Maszyny wirtualne mogą znajdować się w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation.

3. Na stronie maszyna wirtualna wybierz opcję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

4. Musisz mieć `Microsoft.OperationalInsights/workspaces/read` uprawnienie do określenia, czy maszyna wirtualna jest włączona dla obszaru roboczego. Aby dowiedzieć się więcej o dodatkowych uprawnieniach, które są wymagane, zobacz [uprawnienia do instalacji funkcji](../automation-role-based-access-control.md#feature-setup-permissions). Aby dowiedzieć się, jak jednocześnie włączyć wiele komputerów, zobacz [włączanie Change Tracking i spisu na podstawie konta usługi Automation](enable-from-automation-account.md).

5. Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć Change Tracking i spis dla maszyny wirtualnej. Ukończenie instalacji może potrwać do 15 minut.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat pracy z funkcją [](manage-change-tracking.md) , zobacz Zarządzanie [zapasami Change Tracking i zarządzanie nimi](manage-inventory-vms.md).

* Rozwiązywanie ogólnych problemów z funkcją można znaleźć w temacie [Rozwiązywanie problemów dotyczących Change Tracking i spisu](../troubleshoot/change-tracking.md).