---
title: Tworzenie alertów Resource Health przy użyciu witryny Azure Portal
description: Utwórz alert przy użyciu Azure Portal, który powiadomi Cię, gdy zasoby platformy Azure staną się niedostępne.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: 127aad1691f7bb6b6c64332eefde734a809ab75f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540680"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Konfigurowanie alertów dotyczących kondycji zasobów przy użyciu witryny Azure Portal

W tym artykule opisano sposób konfigurowania alertów dziennika aktywności dla powiadomień o kondycji zasobów przy użyciu Azure Portal.

Azure Resource Health informuje o aktualnym i historycznym stanie kondycji zasobów platformy Azure. Alerty Azure Resource Health mogą powiadamiać niemal w czasie rzeczywistym o zmianie stanu kondycji tych zasobów. Tworzenie alertów Resource Health programowo pozwala użytkownikom na tworzenie i dostosowywanie alertów zbiorczo.

Powiadomienia o kondycji zasobów są przechowywane w [dzienniku aktywności platformy Azure](../azure-monitor/platform/platform-logs-overview.md) z uwzględnieniem prawdopodobnie dużej ilości informacji przechowywanych w dzienniku aktywności, istnieje oddzielny interfejs użytkownika, który ułatwia wyświetlanie i Konfigurowanie alertów dotyczących powiadomień o kondycji zasobów.
Możesz otrzymać Alert, gdy zasób platformy Azure wyśle powiadomienia o kondycji zasobów do subskrypcji platformy Azure. Alert można skonfigurować na podstawie:

* Objęta subskrypcją.
* Uwzględnione typy zasobów:.
* Uwzględnione grupy zasobów.
* Uwzględnione zasoby.
* Liczba Stanów zdarzeń, których dotyczy ten zasób.
* Stany zasobów, których dotyczą.
* Typy przyczyn, dla których zastąpiły te zasoby.

Możesz również skonfigurować osobę, do której ma być wysyłany Alert:

* Wybierz istniejącą grupę akcji.
* Utwórz nową grupę akcji (która może być używana w przyszłych alertach).

Aby dowiedzieć się więcej na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/platform/action-groups.md).

Informacje o sposobie konfigurowania alertów dotyczących kondycji zasobów przy użyciu szablonów Azure Resource Manager można znaleźć w temacie [Menedżer zasobów templates](./resource-health-alert-arm-template-guide.md).
Resource Health alertu przy użyciu Azure Portal

## <a name="resource-health-alert-using-azure-portal"></a>Resource Health alertu przy użyciu witryny Azure Portal

1. W witrynie Azure [Portal](https://portal.azure.com/)wybierz pozycję **Service Health**.

    ![Service Health wybór](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. W sekcji **Resource Health** wybierz pozycję **Service Health**.
3. Wybierz pozycję **Dodaj alert kondycji zasobu** i wypełnij pola.
4. W obszarze cel alertu wybierz **subskrypcję**, **typy zasobów**, **grupy zasobów** i **zasób** , dla których chcesz otrzymywać alerty.

    ![Wybór wyboru elementu docelowego](./media/resource-health-alert-monitor-guide/alert-target.png)

5. W obszarze warunek alertu wybierz:
    1. **Stan zdarzenia** , dla którego chcesz otrzymywać alerty. Poziom ważności zdarzenia: aktywny, rozwiązany, w toku, zaktualizowany
    2. **Stan zasobu** , dla którego chcesz otrzymywać alerty. Stan zasobu zdarzenia: dostępne, niedostępne, nieznane lub obniżone
    3. **Typ przyczyny** , dla którego chcesz otrzymywać alerty. Przyczyna zdarzenia: zainicjowano platformę, ![ wybór warunku alertu zainicjowanego przez użytkownika](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. W obszarze Zdefiniuj szczegóły alertu podaj następujące informacje:
    1. **Nazwa reguły alertu**: Nazwa nowej reguły alertu.
    2. **Opis**: Opis nowej reguły alertu.
    3. **Zapisz alert w grupie zasobów**: Wybierz grupę zasobów, w której chcesz zapisać nową regułę.
7. W obszarze **Grupa akcji**z menu rozwijanego wybierz grupę akcji, która ma zostać przypisana do nowej reguły alertu. Lub [Utwórz nową grupę akcji](../azure-monitor/platform/action-groups.md) i przypisz ją do nowej reguły. Aby utworzyć nową grupę, wybierz pozycję + **Nowa grupa**.
8. Aby włączyć reguły po ich utworzeniu, wybierz opcję **tak** dla opcji **Włącz regułę przy tworzeniu** .
9. Wybierz pozycję **Utwórz regułę alertu**.

Zostanie utworzona Nowa reguła alertu dla dziennika aktywności, a w prawym górnym rogu okna pojawi się komunikat z potwierdzeniem.
Możesz włączyć, wyłączyć, edytować lub usunąć regułę. Dowiedz się więcej o [sposobach zarządzania zasadami dziennika aktywności](../azure-monitor/platform/alerts-activity-log.md#view-and-manage-in-the-azure-portal).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Resource Health:

* [Przegląd Azure Resource Health](Resource-health-overview.md)
* [Typy zasobów i kontrole kondycji dostępne za Azure Resource Health](resource-health-checks-resource-types.md)

Utwórz alerty Service Health:

* [Konfigurowanie alertów dla Service Health](./alerts-activity-log-service-notifications-portal.md) 
* [Schemat zdarzeń dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log-schema.md)
* [Konfigurowanie alertów dotyczących kondycji zasobów przy użyciu szablonów usługi Resource Manager](./resource-health-alert-arm-template-guide.md)
