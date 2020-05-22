---
title: Włącz Azure Automation Change Tracking i spisu na podstawie konta usługi Automation
description: W tym artykule opisano sposób włączania Change Tracking i spisu na koncie usługi Automation.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 7aacf88315c19dc6e1016bf518343165492f9188
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749095"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Włączanie Change Tracking i spisu na podstawie konta usługi Automation

W tym artykule opisano sposób korzystania z konta usługi Automation w celu włączenia funkcji [Change Tracking i spisu](change-tracking.md) dla maszyn wirtualnych w środowisku. Aby włączyć maszyny wirtualne platformy Azure na dużą skalę, należy włączyć istniejącą maszynę wirtualną przy użyciu Change Tracking i spisu. 

> [!NOTE]
> Podczas włączania Change Tracking i spisu tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation. Aby uzyskać listę obsługiwanych par mapowania, zobacz [Mapowanie regionów dla konta usługi Automation i obszaru roboczego log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do zarządzania maszynami.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Włączanie rozwiązania Change Tracking and Inventory

1. Przejdź do konta usługi Automation i wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

2. Wybierz obszar roboczy Log Analytics i konto usługi Automation, a następnie kliknij pozycję **Włącz** , aby włączyć Change Tracking i spis. Ukończenie instalacji może potrwać do 15 minut.

    ![Włączanie rozwiązania Change Tracking and Inventory](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Sprawdź konfigurację zakresu

Change Tracking i spis używają konfiguracji zakresu w obszarze roboczym, aby określić, że komputery mają otrzymywać zmiany. Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania, które jest używane do ograniczania zakresu funkcji do określonych komputerów. Aby uzyskać więcej informacji, zobacz temat [współpraca z konfiguracjami zakresu dla Change Tracking i spisu](automation-scope-configurations-change-tracking.md).

## <a name="enable-azure-vms"></a>Włączanie maszyn wirtualnych platformy Azure

1. Z poziomu konta usługi Automation wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

2. Kliknij pozycję **+ Dodaj maszyny wirtualne platformy Azure** i wybierz co najmniej jedną maszynę wirtualną z listy. Maszyny wirtualne, których nie można włączyć, są wyszarzone i nie można ich wybrać. Maszyny wirtualne platformy Azure mogą istnieć w dowolnym regionie, niezależnie od lokalizacji konta usługi Automation. 

3. Kliknij pozycję **Włącz** , aby dodać wybrane maszyny wirtualne do grupy komputerów zapisane wyszukiwanie funkcji. Aby uzyskać więcej informacji, zobacz temat [współpraca z konfiguracjami zakresu dla Change Tracking i spisu](automation-scope-configurations-change-tracking.md).

    ![Włączanie maszyn wirtualnych platformy Azure](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Włączanie maszyn wirtualnych spoza platformy Azure

Nie trzeba ręcznie dodawać maszyn, które nie są na platformie Azure. 

1. Z poziomu konta usługi Automation wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

2. Kliknij pozycję **Dodaj maszynę spoza platformy Azure**. Ta akcja powoduje otwarcie nowego okna przeglądarki z [instrukcjami dotyczącymi instalowania i konfigurowania agenta log Analytics dla systemu Windows](../azure-monitor/platform/log-analytics-agent.md) , tak aby komputer mógł rozpocząć raportowanie operacji Change Tracking i spisu. W przypadku włączenia komputera, który jest obecnie zarządzany przez Operations Manager, nowy Agent nie jest wymagany, a informacje o obszarze roboczym zostaną wprowadzone do istniejącego agenta.

## <a name="enable-machines-in-the-workspace"></a>Włączanie maszyn w obszarze roboczym

Ręcznie zainstalowane maszyny lub maszyny, które są już raportowane do obszaru roboczego, muszą zostać dodane do Azure Automation, aby można było włączyć Change Tracking i spis. 

1. Z poziomu konta usługi Automation wybierz pozycję **spis** lub **śledzenie zmian** w obszarze **Zarządzanie konfiguracją**.

2. Wybierz pozycję **Zarządzaj komputerami**. Przycisk **Zarządzaj maszynami** może być wyszarzony, jeśli wcześniej wybrano opcję **Włącz na wszystkich dostępnych i przyszłych maszynach**

    ![Zapisane wyszukiwania](media/automation-enable-changes-from-auto-acct/managemachines.png)

4. Aby włączyć Change Tracking i spis dla wszystkich dostępnych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych maszynach** na stronie Zarządzanie komputerami. Ta akcja powoduje wyłączenie kontrolki do dodawania maszyn osobno. To zadanie dodaje wszystkie nazwy maszyn, które są raportowane do obszaru roboczego do zapisanego zapytania wyszukiwania grupy komputerów. Po wybraniu tej akcji program wyłącza przycisk **Zarządzaj maszynami** .

5. Aby włączyć funkcję dla wszystkich dostępnych maszyn i przyszłych maszyn, wybierz pozycję **Włącz na wszystkich dostępnych i przyszłych maszynach**. Ta opcja usuwa zapisane wyszukiwania i konfiguracje zakresów z obszaru roboczego i otwiera funkcję dla wszystkich maszyn platformy Azure i spoza platformy Azure, które są raportowane do obszaru roboczego. Po wybraniu tej akcji przycisk **Zarządzaj maszynami** zostaje trwale wyłączony, ponieważ nie pozostała konfiguracja zakresu.

6. W razie potrzeby można dodać konfiguracje zakresów ponownie, dodając początkowe zapisane wyszukiwania. Aby uzyskać więcej informacji, zobacz temat [współpraca z konfiguracjami zakresu dla Change Tracking i spisu](automation-scope-configurations-change-tracking.md).

7. Aby włączyć tę funkcję dla jednej lub wielu maszyn, wybierz pozycję **Włącz na wybranych maszynach** i kliknij przycisk **Dodaj** obok każdej maszyny, aby włączyć tę funkcję. To zadanie dodaje wybrane nazwy maszyn do grupy komputerów zapisane zapytanie wyszukiwania dla tej funkcji.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie Change Tracking i spisem](change-tracking-file-contents.md)
* [Pracuj z konfiguracjami zakresu dla Change Tracking i spisu](automation-scope-configurations-change-tracking.md)
* [Identyfikowanie oprogramowania na maszynie wirtualnej](automation-tutorial-installed-software.md)
* [Odłączanie obszaru roboczego od konta usługi Automation dla Change Tracking i spisu](automation-unlink-workspace-change-tracking.md)
* [Usuwanie maszyn wirtualnych z Change Tracking i spisu](automation-remove-vms-from-change-tracking.md)
* [Rozwiązywanie problemów dotyczących zmian na maszynie wirtualnej platformy Azure](automation-tutorial-troubleshoot-changes.md)
* [Rozwiązywanie problemów dotyczących Change Tracking i spisu](troubleshoot/change-tracking.md)