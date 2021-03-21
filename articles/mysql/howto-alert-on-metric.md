---
title: Konfigurowanie alertów metryk — Azure Portal-Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do alertów metryk dla Azure Database for MySQL z Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5b9e7938f404a37672fe8d13725b1e64d665bf18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576267"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Użyj Azure Portal, aby skonfigurować alerty dotyczące metryk dla Azure Database for MySQL 

W tym artykule opisano sposób konfigurowania alertów Azure Database for MySQL przy użyciu Azure Portal. Możesz otrzymywać alerty na podstawie metryk monitorowania dla usług platformy Azure.

Alert jest wyzwalany, gdy wartość określonej metryki przekroczy przypisany próg. Alert jest wyzwalany podczas pierwszego spełnienia warunku, a następnie gdy ten warunek nie jest już spełniany. 

Można skonfigurować alert, aby wykonał następujące akcje po jego wyzwoleniu:
* Wysyłanie powiadomień e-mail do administratora usługi i współadministratorów
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail, które określisz.
* Wywołanie elementu webhook

Można skonfigurować i uzyskać informacje o regułach alertów przy użyciu:
* [Witryna Azure Portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Interfejs wiersza polecenia platformy Azure](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Interfejs API REST usługi Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Tworzenie reguły alertu dotyczącej metryki z poziomu witryny Azure Portal
1. W [Azure Portal](https://portal.azure.com/)wybierz serwer Azure Database for MySQL, który chcesz monitorować.

2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **alerty** , jak pokazano:

   :::image type="content" source="./media/howto-alert-on-metric/2-alert-rules.png" alt-text="Wybierz reguły alertów":::

3. Wybierz pozycję **Dodaj alert dotyczący metryki** (+ ikona).

4. Zostanie otwarta strona **Tworzenie reguły** , jak pokazano poniżej. Wypełnij wymagane informacje:

   :::image type="content" source="./media/howto-alert-on-metric/4-add-rule-form.png" alt-text="Formularz dodawania alertu metryki":::

5. W sekcji **warunek** wybierz pozycję **Dodaj warunek**.

6. Wybierz metrykę z listy sygnałów, dla których chcesz otrzymywać alerty. W tym przykładzie wybierz pozycję "procent magazynu".
   
   :::image type="content" source="./media/howto-alert-on-metric/6-configure-signal-logic.png" alt-text="Wybierz metrykę":::

7. Skonfiguruj logikę alertów, łącznie z **warunkiem** (np. "Większe niż"), **próg** (np. 85 procent), **agregacja czasu**, **okres** czasu reguły metryki musi być spełniony przed wyzwalaczami alertu (np. "W ciągu ostatnich 30 minut") i **częstotliwość**.
   
   Wybierz pozycję **gotowe** po zakończeniu.

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Wybierz metrykę 2":::

8. W sekcji **grupy akcji** wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę, która będzie otrzymywać powiadomienia dotyczące alertu.

9. Wypełnij formularz "Dodawanie grupy akcji" z nazwą, krótką nazwą, subskrypcją i grupą zasobów.

10. Skonfiguruj typ akcji **poczty e-mail/SMS/wypychania/głosu** .
    
    Wybierz pozycję "Azure Resource Manager rolę E-mail", aby wybrać właścicieli subskrypcji, współautorów i czytelników, którzy mają otrzymywać powiadomienia.
   
    Opcjonalnie Podaj prawidłowy identyfikator URI w polu **elementu webhook** , jeśli ma on być wywoływany po uruchomieniu alertu.

    Po zakończeniu wybierz **przycisk OK** .

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Grupa akcji":::

11. Określ nazwę, opis i ważność reguły alertu.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Grupa akcji 2"::: 

12. Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć alert.

    W ciągu kilku minut alert jest aktywny i wyzwalacze opisane wcześniej.

## <a name="manage-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu możesz go zaznaczyć i wykonać następujące czynności:

* Wyświetl wykres pokazujący próg metryki i rzeczywiste wartości z poprzedniego dnia odpowiednie dla tego alertu.
* **Edytuj** lub **Usuń** regułę alertów.
* **Wyłączenie** lub **włączenie** alertu, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../azure-monitor/alerts/alerts-webhooks.md).
* Zapoznaj się z [omówieniem kolekcji metryk](../azure-monitor/data-platform.md) , aby upewnić się, że usługa jest dostępna i odpowiada.