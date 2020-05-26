---
title: Włączanie Azure Automation Change Tracking i spisu na podstawie maszyny wirtualnej platformy Azure
description: W tym artykule opisano sposób włączania Change Tracking i spisu na maszynie wirtualnej platformy Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 5379f2c46bbeaba4ee8509603b7b739b75d08f04
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836791"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Włączanie śledzenia zmian i spisu na maszynie wirtualnej platformy Azure

W tym artykule opisano, jak można użyć maszyny wirtualnej platformy Azure w celu włączenia funkcji [Change Tracking i spisu](change-tracking.md) na innych maszynach. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Change Tracking i spisu. 

> [!NOTE]
> Podczas włączania Change Tracking i spisu tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do zarządzania maszynami.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **maszyny wirtualne** lub Wyszukaj i wybierz pozycję **maszyny wirtualne** ze strony głównej.

2. Wybierz maszynę wirtualną, dla której chcesz włączyć Change Tracking i spis. Maszyny wirtualne mogą znajdować się w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation.

3. Na stronie maszyna wirtualna wybierz opcję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

4. Musisz mieć `Microsoft.OperationalInsights/workspaces/read` uprawnienie do określenia, czy maszyna wirtualna jest włączona dla obszaru roboczego. Aby dowiedzieć się więcej o dodatkowych uprawnieniach, które są wymagane, zobacz [uprawnienia do instalacji funkcji](automation-role-based-access-control.md#feature-setup-permissions). Aby dowiedzieć się, jak jednocześnie włączyć wiele komputerów, zobacz [włączanie Change Tracking i spisu na podstawie konta usługi Automation](automation-enable-changes-from-auto-acct.md).

5. Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć Change Tracking i spis dla maszyny wirtualnej. Ukończenie instalacji może potrwać do 15 minut. 

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Sprawdź konfigurację zakresu

Change Tracking i spis używają konfiguracji zakresu w obszarze roboczym, aby określić komputery, które mają być włączone dla tej funkcji. Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania, które jest używane do ograniczania zakresu funkcji do określonych komputerów. Aby uzyskać więcej informacji, zobacz temat [współpraca z konfiguracjami zakresu dla Change Tracking i spisu](automation-scope-configurations-change-tracking.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat pracy z funkcją, zobacz [zarządzanie Change Tracking i spisem](change-tracking-file-contents.md).
* Aby uzyskać informacje na temat konfiguracji zakresów, zobacz artykuł [współpraca z konfiguracjami zakresów dla Change Tracking i spisu](automation-scope-configurations-change-tracking.md).
* Aby dowiedzieć się, jak za pomocą funkcji identyfikować oprogramowanie zainstalowane w danym środowisku, zobacz sekcję jak rozpoznać, [jakie oprogramowanie jest zainstalowane na maszynach wirtualnych](automation-tutorial-installed-software.md).
* Jeśli nie chcesz zintegrować konta usługi Automation z obszarem roboczym Log Analytics podczas włączania funkcji, zobacz [Odłącz obszar roboczy od konta usługi Automation](automation-unlink-workspace-change-tracking.md).
* Po zakończeniu wdrażania zmian na maszynach wirtualnych można je usunąć zgodnie z opisem w temacie [usuwanie maszyn wirtualnych z Change Tracking i spisu](automation-remove-vms-from-change-tracking.md).
* Rozwiązywanie ogólnych problemów z funkcją można znaleźć w temacie [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md).
