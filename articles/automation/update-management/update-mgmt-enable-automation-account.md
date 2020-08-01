---
title: Włącz Update Management Azure Automation z konta usługi Automation
description: W tym artykule opisano sposób włączania Update Management na koncie usługi Automation.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 930861c61843c5963c83d8fa6dc1efdce20853f4
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450657"
---
# <a name="enable-update-management-from-an-automation-account"></a>Włączanie rozwiązania Update Management z poziomu konta usługi Automation

W tym artykule opisano, jak za pomocą konta usługi Automation włączyć funkcję [Update Management](update-mgmt-overview.md) dla maszyn wirtualnych w środowisku. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Update Management.

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

2. Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie wybierz pozycję **Włącz** , aby włączyć Update Management. Ukończenie instalacji może potrwać do 15 minut.

    ![Włączanie rozwiązania Update Management](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Włączanie maszyn wirtualnych platformy Azure

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz pozycję **+ Dodaj maszyny wirtualne platformy Azure** i wybierz co najmniej jedną maszynę wirtualną z listy. Maszyny wirtualne, których nie można włączyć, są wyszarzone i nie można ich wybrać. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation.

3. Wybierz pozycję **Włącz** , aby dodać wybrane maszyny wirtualne do grupy komputerów zapisane wyszukiwanie funkcji.

    ![Włączanie maszyn wirtualnych platformy Azure](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Włączanie maszyn wirtualnych spoza platformy Azure

Nie trzeba ręcznie dodawać maszyn, które nie są na platformie Azure.

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz pozycję **Dodaj maszynę spoza platformy Azure**. Ta akcja powoduje otwarcie nowego okna przeglądarki z [instrukcjami dotyczącymi instalowania i konfigurowania agenta log Analytics dla systemu Windows](../../azure-monitor/platform/log-analytics-agent.md) , tak aby maszyna mogła rozpocząć raportowanie do Update Management. Jeśli włączysz komputer, który jest obecnie zarządzany przez Operations Manager, nie jest wymagany nowy Agent. Informacje o obszarze roboczym zostaną dodane do konfiguracji agentów.

## <a name="enable-machines-in-the-workspace"></a>Włączanie maszyn w obszarze roboczym

Ręcznie zainstalowane maszyny lub maszyny, które są już raportowane do obszaru roboczego, muszą zostać dodane do Azure Automation, aby Update Management do włączenia.

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz pozycję **Zarządzaj komputerami**. Przycisk **Zarządzaj maszynami** może być wyszarzony, jeśli wcześniej wybrano opcję **Włącz na wszystkich dostępnych i przyszłych maszynach**

    ![Zapisane wyszukiwania](media/update-mgmt-enable-automation-account/managemachines.png)

3. Aby włączyć Update Management dla wszystkich dostępnych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych maszynach** na stronie Zarządzanie komputerami. Ta akcja powoduje wyłączenie kontrolki do dodawania maszyn osobno. To zadanie dodaje wszystkie nazwy maszyn, które są raportowane do obszaru roboczego do zapisanego zapytania wyszukiwania grupy komputerów. Po wybraniu tej akcji program wyłącza przycisk **Zarządzaj maszynami** .

4. Aby włączyć funkcję dla wszystkich dostępnych maszyn i przyszłych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych i przyszłych maszynach**. Ta opcja usuwa zapisane wyszukiwania i konfiguracje zakresów z obszaru roboczego i otwiera funkcję dla wszystkich maszyn platformy Azure i spoza platformy Azure, które są raportowane do obszaru roboczego. Po wybraniu tej akcji przycisk **Zarządzaj maszynami** zostaje trwale wyłączony, ponieważ nie pozostała konfiguracja zakresu.

5. W razie potrzeby można dodać konfiguracje zakresów ponownie, dodając początkowe zapisane wyszukiwania. Aby uzyskać więcej informacji, zobacz [Ograniczanie zakresu wdrożenia Update Management](update-mgmt-scope-configuration.md).

6. Aby włączyć tę funkcję dla jednej lub wielu maszyn, wybierz pozycję **Włącz na wybranych maszynach** i wybierz pozycję **Dodaj** obok każdej maszyny. To zadanie dodaje wybrane nazwy maszyn do grupy komputerów zapisane zapytanie wyszukiwania dla tej funkcji.

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych](update-mgmt-manage-updates-for-vm.md).

* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](../troubleshoot/update-management.md).