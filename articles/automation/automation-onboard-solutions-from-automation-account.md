---
title: Włącz Update Management Azure Automation z konta usługi Automation
description: W tym artykule opisano sposób włączania Update Management na koncie usługi Automation.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: e38e9a66db9e775023ff5c7940fd892eb0926824
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170736"
---
# <a name="enable-update-management-from-an-automation-account"></a>Włączanie rozwiązania Update Management z poziomu konta usługi Automation

W tym artykule opisano, jak za pomocą konta usługi Automation włączyć funkcję [Update Management](automation-update-management.md) dla maszyn wirtualnych w środowisku. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Update Management. 

> [!NOTE]
> Podczas włączania Update Management tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do zarządzania maszynami.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

## <a name="enable-update-management"></a>Włączanie rozwiązania Update Management

1. Na koncie usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć Update Management. Ukończenie instalacji może potrwać do 15 minut.

    ![Włączanie rozwiązania Update Management](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Włączanie maszyn wirtualnych platformy Azure

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Kliknij pozycję **+ Dodaj maszyny wirtualne platformy Azure** i wybierz co najmniej jedną maszynę wirtualną z listy. Maszyny wirtualne, których nie można włączyć, są wyszarzone i nie można ich wybrać. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation. 

3. Kliknij pozycję **Włącz** , aby dodać wybrane maszyny wirtualne do grupy komputerów zapisane wyszukiwanie funkcji.

    ![Włączanie maszyn wirtualnych platformy Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Włączanie maszyn wirtualnych spoza platformy Azure

Nie trzeba ręcznie dodawać maszyn, które nie są na platformie Azure. 

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Kliknij pozycję **Dodaj maszynę spoza platformy Azure**. Ta akcja powoduje otwarcie nowego okna przeglądarki z [instrukcjami dotyczącymi instalowania i konfigurowania agenta log Analytics dla systemu Windows](../azure-monitor/platform/log-analytics-agent.md) , tak aby maszyna mogła rozpocząć raportowanie Update Management operacji. W przypadku włączenia komputera, który jest obecnie zarządzany przez Operations Manager, nowy Agent nie jest wymagany, a informacje o obszarze roboczym zostaną wprowadzone do istniejącego agenta.

## <a name="enable-machines-in-the-workspace"></a>Włączanie maszyn w obszarze roboczym

Ręcznie zainstalowane maszyny lub maszyny, które są już raportowane do obszaru roboczego, muszą zostać dodane do Azure Automation, aby Update Management do włączenia. 

1. Z poziomu konta usługi Automation wybierz pozycję **Update Management** w obszarze **Update Management**.

2. Wybierz pozycję **Zarządzaj komputerami**. Przycisk **Zarządzaj maszynami** może być wyszarzony, jeśli wcześniej wybrano opcję **Włącz na wszystkich dostępnych i przyszłych maszynach**

    ![Zapisane wyszukiwania](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Aby włączyć Update Management dla wszystkich dostępnych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych maszynach** na stronie Zarządzanie komputerami. Ta akcja powoduje wyłączenie kontrolki do dodawania maszyn osobno. To zadanie dodaje wszystkie nazwy maszyn, które są raportowane do obszaru roboczego do zapisanego zapytania wyszukiwania grupy komputerów. Po wybraniu tej akcji program wyłącza przycisk **Zarządzaj maszynami** .

5. Aby włączyć funkcję dla wszystkich dostępnych maszyn i przyszłych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych i przyszłych maszynach**. Ta opcja usuwa zapisane wyszukiwania i konfiguracje zakresów z obszaru roboczego i otwiera funkcję dla wszystkich maszyn platformy Azure i spoza platformy Azure, które są raportowane do obszaru roboczego. Po wybraniu tej akcji przycisk **Zarządzaj maszynami** zostaje trwale wyłączony, ponieważ nie pozostała konfiguracja zakresu.

6. W razie potrzeby można dodać konfiguracje zakresów ponownie, dodając początkowe zapisane wyszukiwania. Aby uzyskać więcej informacji, zobacz [Ograniczanie zakresu wdrożenia Update Management](automation-scope-configurations-update-management.md).

7. Aby włączyć tę funkcję dla jednej lub wielu maszyn, wybierz pozycję **Włącz na wybranych maszynach** i kliknij przycisk **Dodaj** obok każdej maszyny, aby włączyć tę funkcję. To zadanie dodaje wybrane nazwy maszyn do grupy komputerów zapisane zapytanie wyszukiwania dla tej funkcji.

## <a name="next-steps"></a>Następne kroki

* Aby używać Update Management dla maszyn wirtualnych, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
* Aby rozwiązać ogólne błędy Update Management, zobacz [Rozwiązywanie problemów z Update Management](troubleshoot/update-management.md).
* Aby rozwiązać problemy z usługą Windows Update Agent, zobacz [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Aby rozwiązać problemy z agentem aktualizacji systemu Linux, zobacz[Rozwiązywanie problemów z agentem aktualizacji systemu Linux](troubleshoot/update-agent-issues-linux.md).
