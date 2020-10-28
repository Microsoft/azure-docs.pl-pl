---
title: Włącz Azure Automation Change Tracking i spisu na podstawie konta usługi Automation
description: W tym artykule opisano sposób włączania Change Tracking i spisu na koncie usługi Automation.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 50188ad5fea0ee34a6896f0045e3bbcbfb553aaa
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677297"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Włączanie śledzenia zmian i spisu na koncie usługi Automation

W tym artykule opisano, jak można używać konta usługi Automation do włączania [Change Tracking i spisu](overview.md) maszyn wirtualnych w środowisku. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Change Tracking i spisu.

> [!NOTE]
> Podczas włączania Change Tracking i spisu tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../index.yml) do zarządzania maszynami.
* [Maszyna wirtualna](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

1. Przejdź do konta usługi Automation i wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją** .

2. Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć Change Tracking i spis. Ukończenie instalacji może potrwać do 15 minut.

    ![Włączanie rozwiązania Change Tracking and Inventory](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Włączanie maszyn wirtualnych platformy Azure

1. Z poziomu konta usługi Automation wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją** .

2. Kliknij pozycję **+ Dodaj maszyny wirtualne platformy Azure** i wybierz co najmniej jedną maszynę wirtualną z listy. Maszyny wirtualne, których nie można włączyć, są wyszarzone i nie można ich wybrać. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation. 

3. Kliknij pozycję **Włącz** , aby dodać wybrane maszyny wirtualne do grupy komputerów zapisane wyszukiwanie funkcji. Aby uzyskać więcej informacji, zobacz [ograniczanie Change Tracking i zakresu wdrożenia spisu](manage-scope-configurations.md).

      [![Włączanie maszyn wirtualnych platformy Azure](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Włączanie maszyn wirtualnych spoza platformy Azure

Nie trzeba ręcznie dodawać maszyn, które nie są na platformie Azure. Zaleca się zainstalowanie agenta Log Analytics dla systemu Windows lub Linux, najpierw łącząc maszynę z [usługą Azure ARC z włączonymi serwerami](../../azure-arc/servers/overview.md), a następnie używając Azure Policy do przypisywania programu [wdrożenia log Analytics agenta do systemu *Linux* lub *Windows* Azure Arc Machine](../../governance/policy/samples/built-in-policies.md#monitoring) Policy. Jeśli planujesz również monitorować komputery przy użyciu Azure Monitor dla maszyn wirtualnych, zamiast tego użyj inicjatywy [Enable Azure monitor dla maszyn wirtualnych](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

1. Z poziomu konta usługi Automation wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją** .

2. Kliknij pozycję **Dodaj maszynę spoza platformy Azure** . Ta akcja powoduje otwarcie nowego okna przeglądarki z [instrukcjami dotyczącymi instalowania i konfigurowania agenta log Analytics dla systemu Windows](../../azure-monitor/platform/log-analytics-agent.md) , tak aby komputer mógł rozpocząć raportowanie operacji Change Tracking i spisu. W przypadku włączenia komputera, który jest obecnie zarządzany przez Operations Manager, nowy Agent nie jest wymagany, a informacje o obszarze roboczym zostaną wprowadzone do istniejącego agenta.

## <a name="enable-machines-in-the-workspace"></a>Włączanie maszyn w obszarze roboczym

Ręcznie zainstalowane maszyny lub maszyny, które są już raportowane do obszaru roboczego, muszą zostać dodane do Azure Automation, aby można było włączyć Change Tracking i spis.

1. Z poziomu konta usługi Automation wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją** .

2. Wybierz pozycję **Zarządzaj komputerami** . Opcja **Zarządzaj maszynami** może być wyszarzona, jeśli wcześniej wybrano opcję **Włącz na wszystkich dostępnych i przyszłych maszynach**

    ![Zapisane wyszukiwania](media/enable-from-automation-account/manage-machines.png)

3. Aby włączyć Change Tracking i spis dla wszystkich dostępnych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych maszynach** na stronie **Zarządzanie komputerami** . Ta akcja wyłącza kontrolkę, aby dodać maszyny indywidualnie i dodaje wszystkie maszyny, które są raportowane do obszaru roboczego do zapisanego zapytania wyszukiwania grupy komputerów. Po wybraniu tej akcji program wyłącza opcję **Zarządzaj maszynami** .

4. Aby włączyć funkcję dla wszystkich dostępnych maszyn i przyszłych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych i przyszłych maszynach** . Ta opcja powoduje usunięcie zapisanej konfiguracji wyszukiwania i zakresu z obszaru roboczego i otwarcie tej funkcji dla wszystkich maszyn platformy Azure i spoza platformy Azure, które są raportowane do obszaru roboczego. Po wybraniu tej akcji program trwale wyłącza opcję **Zarządzaj maszynami** , ponieważ nie pozostała konfiguracja zakresu.

    > [!NOTE]
    > Ponieważ ta opcja powoduje usunięcie zapisanej konfiguracji wyszukiwania i zakresu w ramach Log Analytics, należy usunąć wszystkie blokady usuwania w obszarze roboczym Log Analytics przed wybraniem tej opcji. W przeciwnym razie opcja zakończy się niepowodzeniem, aby usunąć konfiguracje i należy usunąć je ręcznie.

5. W razie potrzeby można dodać konfigurację zakresu ponownie, dodając początkowe zapisane wyszukiwanie. Aby uzyskać więcej informacji, zobacz [ograniczanie Change Tracking i zakresu wdrożenia spisu](manage-scope-configurations.md).

6. Aby włączyć tę funkcję dla jednej lub wielu maszyn, wybierz pozycję **Włącz na wybranych maszynach** i kliknij przycisk **Dodaj** obok każdej maszyny, aby włączyć tę funkcję. To zadanie dodaje wybrane nazwy maszyn do grupy komputerów zapisane zapytanie wyszukiwania dla tej funkcji.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje na temat pracy z funkcją [Manage Change Tracking](manage-change-tracking.md) , zobacz Zarządzanie [zapasami Change Tracking i zarządzanie nimi](manage-inventory-vms.md).

* Rozwiązywanie ogólnych problemów z funkcją można znaleźć w temacie [Rozwiązywanie problemów dotyczących Change Tracking i spisu](../troubleshoot/change-tracking.md).
