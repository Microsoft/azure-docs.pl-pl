---
title: Włącz Update Management Azure Automation z konta usługi Automation
description: W tym artykule opisano sposób włączania Update Management na koncie usługi Automation.
services: automation
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: b97e1e61401697204f79004e4678e6f2286f4a98
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380542"
---
# <a name="enable-update-management-from-an-automation-account"></a>Włączanie rozwiązania Update Management z poziomu konta usługi Automation

W tym artykule opisano, jak za pomocą konta usługi Automation włączyć funkcję [Update Management](overview.md) dla maszyn wirtualnych w środowisku, w tym maszyn lub serwerów zarejestrowanych przy użyciu [usługi Azure ARC z obsługą serwerów](../../azure-arc/servers/overview.md). Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, musisz włączyć istniejącą maszynę wirtualną platformy Azure przy użyciu Update Management.

> [!NOTE]
> Podczas włączania Update Management tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](../index.yml) do zarządzania maszynami.
* [Maszyna wirtualna platformy Azure](../../virtual-machines/windows/quick-create-portal.md)lub maszyna wirtualna lub serwer zarejestrowani z serwerami z obsługą łuku. Na maszynach wirtualnych lub serwerach innych niż platformy Azure musi być zainstalowany [agent log Analytics](../../azure-monitor/platform/log-analytics-agent.md) dla systemu Windows lub Linux, a raportowanie do obszaru roboczego połączonego z kontem usługi Automation Update Management jest włączone w programie. Zalecamy zainstalowanie agenta Log Analytics dla systemu Windows lub Linux, najpierw łącząc maszynę z [włączonymi serwerami usługi Azure Arc](../../azure-arc/servers/overview.md), a następnie korzystając z Azure Policy, można przypisać do zasad wbudowane zasady [wdrażania agenta log Analytics lub *Windows* Azure *Linux*](../../governance/policy/samples/built-in-policies.md#monitoring) . Alternatywnie, jeśli planujesz monitorowanie maszyn przy użyciu Azure Monitor dla maszyn wirtualnych, zamiast tego użyj inicjatywy [Enable Azure monitor dla maszyn wirtualnych](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

1. Na koncie usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie wybierz pozycję **Włącz** , aby włączyć Update Management. Ukończenie instalacji może potrwać do 15 minut.

    ![Włączanie rozwiązania Update Management](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Włączanie maszyn wirtualnych platformy Azure

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz pozycję **+ Dodaj maszyny wirtualne platformy Azure** i wybierz co najmniej jedną maszynę wirtualną z listy. Maszyny wirtualne, których nie można włączyć, są wyszarzone i nie można ich wybrać. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation.

3. Wybierz pozycję **Włącz** , aby dodać wybrane maszyny wirtualne do grupy komputerów zapisane wyszukiwanie funkcji.

    ![Włączanie maszyn wirtualnych platformy Azure](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Włączanie maszyn wirtualnych spoza platformy Azure

W przypadku maszyn lub serwerów hostowanych poza platformą Azure, w tym tych zarejestrowanych przy użyciu serwerów z obsługą usługi Azure ARC, wykonaj następujące kroki, aby włączyć je w Update Management.  

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz pozycję **Dodaj maszynę spoza platformy Azure**. Ta akcja powoduje otwarcie nowego okna przeglądarki z [instrukcjami dotyczącymi instalowania i konfigurowania agenta log Analytics dla systemu Windows](../../azure-monitor/platform/log-analytics-agent.md) , tak aby maszyna mogła rozpocząć raportowanie do Update Management. Jeśli włączysz komputer, który jest obecnie zarządzany przez Operations Manager, nie jest wymagany nowy Agent. Informacje o obszarze roboczym zostaną dodane do konfiguracji agentów.

## <a name="enable-machines-in-the-workspace"></a>Włączanie maszyn w obszarze roboczym

Ręcznie zainstalowane maszyny lub maszyny, które są już raportowane do obszaru roboczego, muszą zostać dodane do Azure Automation, aby Update Management do włączenia.

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz pozycję **Zarządzaj komputerami**. Przycisk **Zarządzaj maszynami** może być wyszarzony, jeśli wcześniej wybrano opcję **Włącz na wszystkich dostępnych i przyszłych maszynach**

    ![Zapisane wyszukiwania](media/enable-from-automation-account/managemachines.png)

3. Aby włączyć Update Management dla wszystkich dostępnych maszyn, które są raportowane do obszaru roboczego, wybierz pozycję **Włącz na wszystkich dostępnych maszynach** na stronie Zarządzanie komputerami. Ta akcja wyłącza kontrolkę, aby dodać maszyny indywidualnie i dodaje wszystkie maszyny, które są raportowane do obszaru roboczego do zapisanego zapytania wyszukiwania grupy komputerów `MicrosoftDefaultComputerGroup` . Po wybraniu tej akcji program wyłącza opcję **Zarządzaj maszynami** .

4. Aby włączyć funkcję dla wszystkich dostępnych maszyn i przyszłych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych i przyszłych maszynach**. Ta opcja powoduje usunięcie zapisanej konfiguracji wyszukiwania i zakresu z obszaru roboczego, a także umożliwia uwzględnienie wszystkich maszyn platformy Azure i innych niż platformy Azure, które są obecnie lub w przyszłości, raport w obszarze roboczym. Po wybraniu tej akcji program trwale wyłącza opcję **Zarządzaj maszynami** , ponieważ nie jest dostępna żadna konfiguracja zakresu.

    > [!NOTE]
    > Ponieważ ta opcja powoduje usunięcie zapisanej konfiguracji wyszukiwania i zakresu w ramach Log Analytics, należy usunąć wszystkie blokady usuwania w obszarze roboczym Log Analytics przed wybraniem tej opcji. W przeciwnym razie opcja zakończy się niepowodzeniem, aby usunąć konfiguracje i należy usunąć je ręcznie.

5. W razie potrzeby można dodać konfigurację zakresu ponownie, dodając początkowe zapisane zapytanie wyszukiwania. Aby uzyskać więcej informacji, zobacz [Ograniczanie zakresu wdrożenia Update Management](scope-configuration.md).

6. Aby włączyć tę funkcję dla jednej lub wielu maszyn, wybierz pozycję **Włącz na wybranych maszynach** i wybierz pozycję **Dodaj** obok każdej maszyny. To zadanie dodaje wybrane nazwy maszyn do grupy komputerów zapisane zapytanie wyszukiwania dla tej funkcji.

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych](manage-updates-for-vm.md).

* Gdy nie musisz już zarządzać maszynami wirtualnymi ani serwerami przy użyciu Update Management, zobacz [usuwanie maszyn wirtualnych z Update Management](remove-vms.md).

* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](../troubleshoot/update-management.md).
