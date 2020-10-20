---
title: Jak utworzyć alerty dla Azure Automation Update Management
description: W tym artykule opisano sposób konfigurowania alertów platformy Azure w celu powiadamiania o stanie ocen lub wdrożeń aktualizacji.
services: automation
ms.subservice: update-management
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 150269dd2cc6a25a507f03c7d6b47544a89da12a
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222732"
---
# <a name="how-to-create-alerts-for-update-management"></a>Jak utworzyć alerty dla Update Management

Alerty w usłudze Azure proaktywnie powiadamiają o wynikach z zadań elementów Runbook, problemów z kondycją usługi lub innymi scenariuszami związanymi z Twoim kontem usługi Automation. Azure Automation nie obejmuje wstępnie skonfigurowanych reguł alertów, ale można utworzyć własne na podstawie generowanych przez siebie danych. Ten artykuł zawiera wskazówki dotyczące tworzenia reguł alertów przy użyciu metryk uwzględnionych w Update Management.

## <a name="available-metrics"></a>Dostępne metryki

Azure Automation tworzy dwie odrębne metryki platformy dotyczące Update Management zbieranych i przesyłanych dalej do Azure Monitor. Ta Metryka jest dostępna do analizy przy użyciu [Eksplorator metryk](../../azure-monitor/platform/metrics-charts.md) i dla alertów przy użyciu [reguły alertu metryki](../../azure-monitor/platform/alerts-metric.md).

Emitowane są dwie metryki:

* Łączna liczba przebiegów wdrażania aktualizacji maszyny
* Łączna liczba przebiegów wdrażania aktualizacji

W przypadku korzystania z alertów obydwie metryki obsługują wymiary, które zawierają dodatkowe informacje ułatwiające Określanie zakresu alertu do określonego wdrożenia aktualizacji. W poniższej tabeli przedstawiono szczegółowe informacje o metryki i wymiarach dostępnych podczas konfigurowania alertu.

|Nazwa sygnału|Wymiary|Opis
|---|---|---|
|`Total Update Deployment Runs`|— Nazwa wdrażania aktualizacji<br>— Stan | Alerty dotyczące ogólnego stanu wdrożenia aktualizacji.|
|`Total Update Deployment Machine Runs`|— Nazwa wdrażania aktualizacji</br>— Stan</br>— Komputer docelowy</br>-Aktualizacja identyfikatora uruchomienia wdrożenia    |Alerty dotyczące stanu wdrożenia aktualizacji, które są określone na określonych maszynach.|

## <a name="create-alert"></a>Tworzenie alertu

Wykonaj poniższe kroki, aby skonfigurować alerty w celu poinformowania o stanie wdrożenia aktualizacji. Jeśli jesteś nowym tematem alertów platformy Azure, zobacz [Omówienie usługi Azure Alerts](../../azure-monitor/platform/alerts-overview.md).

1. Na koncie usługi Automation wybierz pozycję **alerty** w obszarze **monitorowanie**, a następnie wybierz pozycję **Nowa reguła alertu**.

2. Na stronie **Tworzenie reguły alertu** konto usługi Automation zostało już wybrane jako zasób. Jeśli chcesz ją zmienić, wybierz pozycję **Edytuj zasób**.

3. Na stronie wybierz zasób wybierz pozycję **konta usługi Automation** z listy rozwijanej **Filtruj według typu zasobu** .

4. Wybierz konto usługi Automation, którego chcesz użyć, a następnie wybierz pozycję **gotowe**.

5. Wybierz pozycję **Dodaj warunek** , aby wybrać sygnał, który jest odpowiedni dla danego wymagania.

6. Dla wymiaru Wybierz prawidłową wartość z listy. Jeśli potrzebna wartość nie znajduje się na liście, wybierz pozycję **\+** obok wymiaru i wpisz nazwę niestandardową. Następnie wybierz wartość do wyszukania. Jeśli chcesz wybrać wszystkie wartości dla wymiaru, wybierz przycisk **Wybierz \* ** . Jeśli nie wybierzesz wartości wymiaru, Update Management zignoruje ten wymiar.

    ![Konfigurowanie logiki sygnału](./media/manage-updates-for-vm/signal-logic.png)

7. W obszarze **logika alertu**wprowadź wartości w polach **agregacja czasu** i **próg** , a następnie wybierz pozycję **gotowe**.

8. Na następnej stronie Wprowadź nazwę i opis alertu.

9. Ustaw wartość pola **ważność** na **informacyjną (ważność 2)** dla pomyślnego uruchomienia lub **informacyjnego (ważność 1)** dla nieudanego uruchomienia.

    ![Zrzut ekranu przedstawia sekcję Definiowanie szczegółów alertu z wyróżnioną nazwą reguły alertu, opisem i polami ważności.](./media/manage-updates-for-vm/define-alert-details.png)

10. Wybierz pozycję **tak** , aby włączyć regułę alertu.

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurowanie grup akcji dla alertów

Po skonfigurowaniu alertów można skonfigurować grupę akcji, która jest grupą akcji do użycia w ramach wielu alertów. Akcje mogą obejmować powiadomienia e-mail, elementy Runbook, webhook i wiele innych. Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../../azure-monitor/platform/action-groups.md).

1. Wybierz Alert, a następnie wybierz pozycję **Utwórz nowy** w obszarze **grupy akcji**.

2. Wprowadź pełną nazwę i krótką nazwę grupy akcji. Update Management używa krótkiej nazwy podczas wysyłania powiadomień przy użyciu określonej grupy.

3. W obszarze **Akcje**wprowadź nazwę określającą akcję, na przykład **powiadomienie e-mail**.

4. W **polu Typ akcji**wybierz odpowiedni typ, na przykład **e-mail/SMS/wypychanie/głos**.

5. Wybierz pozycję **Edytuj szczegóły**.

6. Wypełnij okienko dla danego typu akcji. Na przykład jeśli używasz **poczty e-mail/SMS/wypychania/głosu**, wprowadź nazwę akcji, zaznacz pole wyboru adres **e-mail** , wprowadź prawidłowy adres e-mail, a następnie wybierz przycisk **OK**.

    ![Konfigurowanie grupy akcji dla poczty e-mail](./media/manage-updates-for-vm/configure-email-action-group.png)

7. W okienku Dodawanie grupy akcji wybierz przycisk **OK**.

8. W przypadku wiadomości e-mail z alertami można dostosować temat wiadomości e-mail. Wybierz pozycję **Dostosuj akcje** w obszarze **Utwórz regułę**, a następnie wybierz pozycję **temat wiadomości e-mail**.

9. Po zakończeniu wybierz pozycję **Utwórz regułę alertu**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach w Azure monitor](../../azure-monitor/platform/alerts-overview.md).

* Dowiedz się więcej na temat [zapytań dzienników](../../azure-monitor/log-query/log-query-overview.md) dotyczących pobierania i analizowania danych z obszaru roboczego log Analytics.

* Zarządzanie [użyciem i kosztami za pomocą dzienników Azure monitor](../../azure-monitor/platform/manage-cost-storage.md) opisuje sposób kontrolowania kosztów przez zmianę okresu przechowywania danych oraz analizowanie i zgłaszanie alertów dotyczących użycia danych.