---
title: Konfigurowanie alertów metryk — Azure Portal-Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do alertów metryk dla Azure Database for MariaDB z Azure Portal.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 1e7dafaba6b6792afdec1f1866629fac9ba5a577
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664422"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Użyj Azure Portal, aby skonfigurować alerty dotyczące metryk dla Azure Database for MariaDB

W tym artykule opisano sposób konfigurowania alertów Azure Database for MariaDB przy użyciu Azure Portal. Możesz otrzymywać alerty na podstawie metryk monitorowania dla usług platformy Azure.

Alert jest wyzwalany, gdy wartość określonej metryki przekroczy przypisany próg. Alert jest wyzwalany podczas pierwszego spełnienia warunku, a następnie gdy ten warunek nie jest już spełniany.

Można skonfigurować alert, aby wykonał następujące akcje po jego wyzwoleniu:
* Wysyłanie powiadomień e-mail do administratora usługi i współadministratorów
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail, które określisz.
* Wywołanie elementu webhook

Można skonfigurować i uzyskać informacje o regułach alertów przy użyciu:
* [Witryna Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfejs wiersza polecenia platformy Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Interfejs API REST usługi Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>Tworzenie reguły alertu dotyczącej metryki
1. W [Azure Portal](https://portal.azure.com/)wybierz serwer Azure Database for MariaDB, który chcesz monitorować.

2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **alerty** , jak pokazano:

   ![Wybierz reguły alertów](./media/howto-alert-metric/2-alert-rules.png)

3. Wybierz pozycję **Dodaj alert dotyczący metryki** (+ ikona).

4. Zostanie otwarta strona **Tworzenie reguły** , jak pokazano poniżej. Wypełnij wymagane informacje:

   ![Formularz dodawania alertu metryki](./media/howto-alert-metric/4-add-rule-form.png)

5. W sekcji **warunek** wybierz pozycję **Dodaj warunek**.

6. Wybierz metrykę z listy sygnałów, dla których chcesz otrzymywać alerty. W tym przykładzie wybierz pozycję "procent magazynu".
   
   ![Wybierz metrykę](./media/howto-alert-metric/6-configure-signal-logic.png)

7. Skonfiguruj logikę alertów, łącznie z **warunkiem** (np. "Większe niż"), **próg** (np. 85 procent), **agregacja czasu**, **okres** czasu reguły metryki musi być spełniony przed wyzwalaczami alertu (np. "W ciągu ostatnich 30 minut") i **częstotliwość**.
   
   Wybierz pozycję **gotowe** po zakończeniu.

   ![Wybierz metrykę 2](./media/howto-alert-metric/7-set-threshold-time.png)

8. W sekcji **grupy akcji** wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę, która będzie otrzymywać powiadomienia dotyczące alertu.

9. Wypełnij formularz "Dodawanie grupy akcji" z nazwą, krótką nazwą, subskrypcją i grupą zasobów.

10. Skonfiguruj typ akcji **poczty e-mail/SMS/wypychania/głosu** .
    
    Wybierz pozycję "Azure Resource Manager rolę E-mail", aby wybrać właścicieli subskrypcji, współautorów i czytelników, którzy mają otrzymywać powiadomienia.
   
    Opcjonalnie Podaj prawidłowy identyfikator URI w polu **elementu webhook** , jeśli ma on być wywoływany po uruchomieniu alertu.

    Po zakończeniu wybierz **przycisk OK** .

    ![Grupa akcji](./media/howto-alert-metric/10-action-group-type.png)

11. Określ nazwę, opis i ważność reguły alertu.

    ![Grupa akcji 2](./media/howto-alert-metric/11-name-description-severity.png) 

12. Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć alert.

    W ciągu kilku minut alert jest aktywny i wyzwalacze opisane wcześniej.

## <a name="manage-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu możesz go zaznaczyć i wykonać następujące czynności:

* Wyświetl wykres pokazujący próg metryki i rzeczywiste wartości z poprzedniego dnia odpowiednie dla tego alertu.
* **Edytuj** lub **Usuń** regułę alertów.
* **Wyłączenie** lub **włączenie** alertu, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
* Zapoznaj się z [omówieniem kolekcji metryk](../azure-monitor/platform/data-platform.md) , aby upewnić się, że usługa jest dostępna i odpowiada.