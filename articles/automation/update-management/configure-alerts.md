---
title: Jak utworzyć alerty dla Azure Automation Update Management
description: W tym artykule opisano sposób konfigurowania alertów platformy Azure w celu powiadamiania o stanie ocen lub wdrożeń aktualizacji.
services: automation
ms.subservice: update-management
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 224a7b5457a099fd763ac657349fc5497824ab76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601433"
---
# <a name="how-to-create-alerts-for-update-management"></a>Jak utworzyć alerty dla Update Management

Alerty w usłudze Azure proaktywnie powiadamiają o wynikach z zadań elementów Runbook, problemów z kondycją usługi lub innymi scenariuszami związanymi z Twoim kontem usługi Automation. Azure Automation nie obejmuje wstępnie skonfigurowanych reguł alertów, ale można utworzyć własne na podstawie generowanych przez siebie danych. Ten artykuł zawiera wskazówki dotyczące tworzenia reguł alertów przy użyciu metryk uwzględnionych w Update Management.

## <a name="available-metrics"></a>Dostępne metryki

Azure Automation tworzy dwie odrębne metryki platformy dotyczące Update Management zbieranych i przesyłanych dalej do Azure Monitor. Ta Metryka jest dostępna do analizy przy użyciu [Eksplorator metryk](../../azure-monitor/essentials/metrics-charts.md) i dla alertów przy użyciu [reguły alertu metryki](../../azure-monitor/alerts/alerts-metric.md).

Emitowane są dwie metryki:

* Łączna liczba przebiegów wdrażania aktualizacji maszyny
* Łączna liczba przebiegów wdrażania aktualizacji

W przypadku korzystania z alertów obydwie metryki obsługują wymiary, które zawierają dodatkowe informacje ułatwiające Określanie zakresu alertu do określonego wdrożenia aktualizacji. W poniższej tabeli przedstawiono szczegółowe informacje o metryki i wymiarach dostępnych podczas konfigurowania alertu.

|Nazwa sygnału|Wymiary|Opis
|---|---|---|
|`Total Update Deployment Runs`|— Nazwa wdrażania aktualizacji<br>— Stan | Alerty dotyczące ogólnego stanu wdrożenia aktualizacji.|
|`Total Update Deployment Machine Runs`|— Nazwa wdrażania aktualizacji</br>— Stan</br>— Komputer docelowy</br>-Aktualizacja identyfikatora uruchomienia wdrożenia    |Alerty dotyczące stanu wdrożenia aktualizacji, które są określone na określonych maszynach.|

## <a name="create-alert"></a>Tworzenie alertu

Wykonaj poniższe kroki, aby skonfigurować alerty w celu poinformowania o stanie wdrożenia aktualizacji. Jeśli jesteś nowym tematem alertów platformy Azure, zobacz [Omówienie usługi Azure Alerts](../../azure-monitor/alerts/alerts-overview.md).

1. Na koncie usługi Automation wybierz pozycję **alerty** w obszarze **monitorowanie**, a następnie wybierz pozycję **Nowa reguła alertu**.

1. Na stronie **Tworzenie reguły alertu** konto usługi Automation zostało już wybrane jako zasób. Jeśli chcesz ją zmienić, wybierz pozycję **Edytuj zasób**.

1. Na stronie wybierz zasób wybierz pozycję **konta usługi Automation** z listy rozwijanej **Filtruj według typu zasobu** .

1. Wybierz konto usługi Automation, którego chcesz użyć, a następnie wybierz pozycję **gotowe**.

1. Wybierz pozycję **Dodaj warunek** , aby wybrać sygnał, który jest odpowiedni dla danego wymagania.

1. Dla wymiaru Wybierz prawidłową wartość z listy. Jeśli potrzebna wartość nie znajduje się na liście, wybierz pozycję **\+** obok wymiaru i wpisz nazwę niestandardową. Następnie wybierz wartość do wyszukania. Jeśli chcesz wybrać wszystkie wartości dla wymiaru, wybierz przycisk **Wybierz \*** . Jeśli nie wybierzesz wartości wymiaru, Update Management zignoruje ten wymiar.

    ![Konfigurowanie logiki sygnału](./media/manage-updates-for-vm/signal-logic.png)

1. W obszarze **logika alertu** wprowadź wartości w polach **agregacja czasu** i **próg** , a następnie wybierz pozycję **gotowe**.

1. Na następnej stronie Wprowadź nazwę i opis alertu.

1. Ustaw wartość pola **ważność** na **informacyjną (ważność 2)** dla pomyślnego uruchomienia lub **informacyjnego (ważność 1)** dla nieudanego uruchomienia.

    ![Zrzut ekranu przedstawia sekcję Definiowanie szczegółów alertu z wyróżnioną nazwą reguły alertu, opisem i polami ważności.](./media/manage-updates-for-vm/define-alert-details.png)

1. Wybierz pozycję **tak** , aby włączyć regułę alertu.

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurowanie grup akcji dla alertów

Po skonfigurowaniu alertów można skonfigurować grupę akcji, która jest grupą akcji do użycia w ramach wielu alertów. Akcje mogą obejmować powiadomienia e-mail, elementy Runbook, webhook i wiele innych. Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../../azure-monitor/alerts/action-groups.md).

1. Wybierz Alert, a następnie wybierz pozycję **Dodaj grupy akcji** w obszarze **Akcje**. Spowoduje to wyświetlenie **grupy wybierz grupę akcji do dołączenia do tego okienka reguły alertu** .

   :::image type="content" source="./media/manage-updates-for-vm/select-an-action-group.png" alt-text="Użycie i szacowane koszty.":::

1. Zaznacz pole wyboru grupy akcji do dołączenia i naciśnij przycisk Wybierz.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach w Azure monitor](../../azure-monitor/alerts/alerts-overview.md).

* Dowiedz się więcej na temat [zapytań dzienników](../../azure-monitor/logs/log-query-overview.md) dotyczących pobierania i analizowania danych z obszaru roboczego log Analytics.

* Zarządzanie [użyciem i kosztami za pomocą dzienników Azure monitor](../../azure-monitor/logs/manage-cost-storage.md) opisuje sposób kontrolowania kosztów przez zmianę okresu przechowywania danych oraz analizowanie i zgłaszanie alertów dotyczących użycia danych.