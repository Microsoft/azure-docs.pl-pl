---
title: Konfigurowanie alertów metryk — Azure Portal-Azure Database for MySQL-elastyczny serwer
description: W tym artykule opisano sposób konfigurowania i uzyskiwania dostępu do alertów metryk dla Azure Database for MySQL elastycznego serwera z Azure Portal.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 632aae766e6fd1328dc6e0135a88a942d7ad0910
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595782"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Użyj Azure Portal, aby skonfigurować alerty dotyczące metryk dla serwera Azure Database for MySQL-elastyczny 

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

W tym artykule opisano sposób konfigurowania alertów Azure Database for MySQL przy użyciu Azure Portal. Możesz otrzymywać alerty na podstawie metryk monitorowania dla usług platformy Azure.

Alert jest wyzwalany, gdy wartość określonej metryki przekroczy przypisany próg. Alert jest wyzwalany podczas pierwszego spełnienia warunku, a następnie gdy ten warunek nie jest już spełniany. Alerty metryk są stanowe, oznacza to, że wysyłają powiadomienia tylko wtedy, gdy stan zmieni się.

Można skonfigurować alert, aby wykonał następujące akcje po jego wyzwoleniu:
* Wysyłanie powiadomień e-mail do administratora usługi i współadministratorów
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail, które określisz.
* Wywołanie elementu webhook

Można skonfigurować i uzyskać informacje o regułach alertów przy użyciu:
* [Witryna Azure Portal](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Interfejs wiersza polecenia platformy Azure](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Interfejs API REST usługi Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Tworzenie reguły alertu dotyczącej metryki z poziomu witryny Azure Portal
1. W [Azure Portal](https://portal.azure.com/)wybierz Azure Database for MySQL elastyczny serwer, który chcesz monitorować.
2. W sekcji **monitorowanie** na pasku bocznym wybierz pozycję **alerty**.
3. Wybierz pozycję **+ Nowa reguła alertu**.
4. Zostanie otwarta strona **Tworzenie reguły**. Wypełnij wymagane informacje:
5. W sekcji **warunek** wybierz **pozycję Wybierz warunek**.
6. Zostanie wyświetlona lista obsługiwanych sygnałów, wybierz metrykę, dla której chcesz utworzyć alert. Na przykład wybierz pozycję "procent magazynu".
7. Zostanie wyświetlony wykres metryki dla ostatnich sześciu godzin. Za pomocą listy rozwijanej **Okres wykresu** wybierz opcję, aby wyświetlić dłuższą historię dla metryki.
8. Wybierz typ **progu** (np. "Static" lub "Dynamic"), **operator** (np. "Większe niż") i **typ agregacji** (np. średnia). Spowoduje to określenie logiki, która będzie Szacowana przez regułę alertu metryki.
    - Jeśli używasz progu **statycznego** , Kontynuuj definiowanie **wartości progowej** (np. 85 procent). Wykres metryk może pomóc w ustaleniu, co może być rozsądnym progiem.
    - Jeśli używasz progu **dynamicznego** , Kontynuuj definiowanie **czułości progowej**. Na wykresie metryki zostaną wyświetlone obliczone progi na podstawie ostatnich danych. [Dowiedz się więcej o opcjach typu i czułości wartości progów dynamicznych](../../azure-monitor/alerts/alerts-dynamic-thresholds.md).
9. Zawęź warunek, dostosowując **stopień szczegółowości agregacji (okres)** , w którym punkty danych są grupowane przy użyciu funkcji typu agregacji (np. "30 minut") i **częstotliwość** (np. co 15 minut).
10. Kliknij przycisk **Gotowe**.
11. Dodaj grupę akcji. Grupa akcji to zbiór preferencji powiadomień definiowanych przez właściciela subskrypcji platformy Azure. W sekcji **grupy akcji** wybierz **pozycję Wybierz grupę akcji** , aby wybrać istniejącą grupę akcji, która ma zostać dołączona do reguły alertu.
12. Możesz również utworzyć nową grupę akcji, aby otrzymywać powiadomienia dotyczące alertu. Aby uzyskać więcej informacji [, zobacz Tworzenie grupy akcji i zarządzanie nią](../../azure-monitor/alerts/action-groups.md) .
13. Aby utworzyć nową grupę akcji, wybierz pozycję **+ Utwórz grupę akcji**. Wypełnij formularz "Tworzenie grupy akcji" z **subskrypcją**, **grupą zasobów**, **nazwą grupy akcji** i **nazwą wyświetlaną**.
14. Skonfiguruj **powiadomienia** dla grupy akcji.
    
    W obszarze **Typ powiadomienia** wybierz pozycję "E-mail Azure Resource Manager rolę", aby wybrać właścicieli subskrypcji, współautorów i czytelników, którzy mają otrzymywać powiadomienia. Wybierz **rolę Azure Resource Manager** do wysłania wiadomości e-mail.
    Możesz również wybrać **wiadomości e-mail/SMS/wypychanie/głos** , aby wysyłać powiadomienia do określonych adresatów.

    Podaj **nazwę** dla typu powiadomienia, a następnie wybierz pozycję **Przegląd + Utwórz** po zakończeniu.

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. Wypełnij **szczegóły reguły** alertu, takie jak **Nazwa reguły alertu**, **Opis**, **Zapisz regułę alertu w grupie zasobów** i **ważności**.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć alert.
    W ciągu kilku minut alert jest aktywny i wyzwalacze opisane wcześniej.
## <a name="manage-your-alerts"></a>Zarządzanie alertami
Po utworzeniu alertu możesz go zaznaczyć i wykonać następujące czynności:

* Wyświetl wykres pokazujący próg metryki i rzeczywiste wartości z poprzedniego dnia odpowiednie dla tego alertu.
* **Edytuj** lub **Usuń** regułę alertów.
* **Wyłączenie** lub **włączenie** alertu, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej [na temat ustawiania alertów dotyczących metryk](../../azure-monitor/alerts/alerts-metric.md).
- Dowiedz się więcej o dostępnych [metrykach w Azure Database for MySQL elastycznym serwerze](./concepts-monitoring.md).
- [Omówienie działania alertów metryk w usłudze Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md)