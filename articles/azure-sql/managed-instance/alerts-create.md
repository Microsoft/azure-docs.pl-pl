---
title: Skonfiguruj alerty i powiadomienia dla wystąpienia zarządzanego (Azure Portal)
description: Użyj Azure Portal, aby utworzyć alerty wystąpienia zarządzanego SQL, które mogą wyzwalać powiadomienia lub automatyzację, gdy zostaną spełnione określone warunki.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: a85bf6510163c47fa39bdb28debff806e1812f58
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499275"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Tworzenie alertów dla wystąpienia zarządzanego usługi Azure SQL przy użyciu witryny Azure Portal
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule opisano sposób konfigurowania alertów dla baz danych w bazie danych wystąpienia zarządzanego Azure SQL przy użyciu Azure Portal. Alerty mogą wysłać wiadomość e-mail, wywołać element webhook, wykonać funkcję usługi Azure Functions, wykonać element runbook, wywołać zewnętrzny system obsługi biletów zgodny z narzędziem ITSM, nawiązać połączenie telefoniczne lub wysłać wiadomość tekstową, gdy dana metryka, na przykład rozmiar magazynu wystąpienia lub użycie procesora CPU, osiągnie wstępnie zdefiniowany próg. W tym artykule przedstawiono również najlepsze rozwiązania dotyczące ustawiania okresów alertów.


## <a name="overview"></a>Omówienie

Możesz otrzymywać alerty na podstawie metryk monitorowania dla usługi lub zdarzeń w usłudze Azure.

* **Wartości metryk** — alert wyzwalany, gdy wartość określonej metryki przekroczy próg przypisany w dowolnym kierunku. Oznacza to, że jest wyzwalane podczas pierwszego spełnienia warunku, a następnie gdy ten warunek nie jest już spełniony.

Można skonfigurować alert, aby wykonać następujące czynności podczas wyzwalania:

* Wysyłanie powiadomień e-mail do administratora usługi i współadministratorów
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail, które określisz.
* Wywoływanie numeru telefonu przy użyciu monitu głosowego
* Wyślij wiadomość SMS na numer telefonu
* Wywołanie elementu webhook
* Wywołaj funkcję platformy Azure
* Wywoływanie elementu Runbook platformy Azure
* Wywołaj zewnętrzny bilet narzędzia ITSM zgodny z systemem

Można skonfigurować i uzyskać informacje o regułach alertów za pomocą [Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure lub usługi](../../azure-monitor/platform/alerts-classic-portal.md) [Azure Monitor API REST](/rest/api/monitor/alertrules). 

## <a name="alerting-metrics-available-for-managed-instance"></a>Metryki alertów dostępne dla wystąpienia zarządzanego

> [!IMPORTANT]
> Metryki alertów są dostępne tylko dla wystąpienia zarządzanego. Metryki alertów dla poszczególnych baz danych w wystąpieniu zarządzanym są niedostępne. Dane telemetryczne diagnostyki baz danych są dostępne w postaci [dzienników diagnostycznych](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export). Alerty w dziennikach diagnostycznych mogą być skonfigurowane z poziomu produktu [analitycznego SQL](../../azure-monitor/insights/azure-sql.md) przy użyciu [skryptów alertów dziennika](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) dla wystąpienia zarządzanego.

Następujące metryki wystąpienia zarządzanego są dostępne dla konfiguracji alertów:

| Metryka | Opis | Wartość jednostki miary \ możliwe wartości |
| :--------- | --------------------- | ----------- |
| Średni procent procesora CPU | Średni procent użycia procesora CPU w wybranym okresie. | 0-100 (procent) |
| Odczytane bajty we/wy | Bajty we/wy odczytane w wybranym okresie. | Bajty |
| Bajty we/wy zapisywane | Bajty we/wy zapisywane w wybranym okresie. | Bajty |
| Liczba żądań we/wy | Liczba żądań we/wy w wybranym okresie. | Porządkow |
| Zarezerwowane miejsce w magazynie | Bieżąca Maksymalna ilość miejsca do magazynowania zarezerwowana dla wystąpienia zarządzanego. Zmiany przy użyciu operacji skalowania zasobów. | MB (megabajty) |
| Używane miejsce do magazynowania | Miejsce do magazynowania używane w wybranym okresie. Zmiany użycia magazynu według baz danych i wystąpienia. | MB (megabajty) |
| Liczba rdzeni wirtualnych | Rdzeni wirtualnych aprowizacji dla wystąpienia zarządzanego. Zmiany przy użyciu operacji skalowania zasobów. | 4-80 (rdzeni wirtualnych) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Tworzenie reguły alertu w ramach metryki z Azure Portal

1. W [witrynie Azure Portal](https://portal.azure.com/)Znajdź zarządzane wystąpienie, które chcesz monitorować, a następnie wybierz je.

2. Wybierz element menu **metryki** w sekcji monitorowanie.

   ![Monitorowanie](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. Z menu rozwijanego wybierz jedną z metryk, dla których chcesz skonfigurować alert (w przykładzie pokazano miejsce do magazynowania).

4. Wybierz okres agregacji — średni, minimalny lub maksymalny osiągnięty w danym przedziale czasu (średnia, minimalna lub maks.). 

5. Wybierz **nową regułę alertu**

6. W okienku utwórz regułę alertu kliknij pozycję **nazwa warunku** (w przykładzie pokazano miejsce do magazynowania)

   ![Zdefiniuj warunek](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. W okienku Konfigurowanie logiki sygnałów Zdefiniuj operatora, typ agregacji i wartość progową

   * Opcje typu operatora są większe niż, równe i mniejsze niż (wartość progowa)
   * Opcje typu agregacji są minimalne, maksymalne lub średnie (w okresie szczegółowości agregacji)
   * Wartość progu to wartość alertu, która zostanie oceniona na podstawie kryteriów agregacji i operatora
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   W przykładzie pokazanym na zrzucie ekranu jest używana wartość 1840876 MB reprezentująca wartość progową 1,8 TB. Jako że operator w przykładzie jest ustawiony na wartość większą niż, zostanie utworzony alert, jeśli użycie miejsca do magazynowania w wystąpieniu zarządzanym przekracza 1,8 TB. Należy zauważyć, że wartość progowa metryk miejsca do magazynowania musi być wyrażona w MEGABAJTach.

8. Ustaw okres próbny — stopień szczegółowości agregacji (w minutach) i częstotliwość obliczania. Częstotliwość oceny spowoduje, że system alertów będzie okresowo sprawdzać, czy warunek progu został spełniony.

9. Wybierz grupę akcji. Zostanie wyświetlone okienko grupa akcji, za pomocą którego będzie można wybrać istniejącą lub utworzyć nową akcję. Ta akcja definiuje, że nastąpi po wyzwoleniu alertu (na przykład wysłaniu wiadomości e-mail, nawiązaniu połączenia z telefonem, wykonaniu elementu webhook, funkcji platformy Azure lub elementu Runbook).

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Aby utworzyć nową grupę akcji, wybierz pozycję **+ Utwórz grupę akcji** .

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Zdefiniuj, w jaki sposób chcesz otrzymywać alerty: Wprowadź nazwę grupy akcji, krótką nazwę, nazwę akcji i wybierz typ akcji. Typ akcji definiuje, czy użytkownik będzie powiadamiany za pośrednictwem poczty e-mail, wiadomości tekstowej, rozmowy głosowej, czy też w przypadku, gdy prawdopodobnie element webhook, funkcja platformy Azure zostanie wykonana, lub zostanie utworzony bilet narzędzia ITSM w zgodnym systemie.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Wypełnij szczegóły reguły alertu dotyczącej rekordów, a następnie wybierz typ ważności.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * Ukończ Tworzenie reguły alertu, klikając przycisk **Utwórz regułę alertu** .

Nowa reguła alertu stanie się aktywna w ciągu kilku minut i zostanie wyzwolona na podstawie Twoich ustawień.

## <a name="verifying-alerts"></a>Weryfikowanie alertów

> [!NOTE]
> Aby Pomijaj alerty, zobacz [pominięcia alertów za pomocą reguł akcji](../../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts).

Po skonfigurowaniu reguły alertów Sprawdź, czy jest spełniony wyzwalacz alertów i jego częstotliwość. Aby zapoznać się z przykładem wyświetlanym na tej stronie w celu skonfigurowania alertu dotyczącego miejsca do magazynowania, jeśli opcja alertów została wysłana pocztą e-mail, możesz otrzymać wiadomość e-mail, która jest pokazana poniżej.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

W wiadomości e-mail wyświetlana jest nazwa alertu, szczegóły progu i powód, dla którego alert został wyzwolony, ułatwiający weryfikację i rozwiązywanie problemów z alertem. Aby wyświetlić alert otrzymany za pośrednictwem poczty e-mail w Azure Portal, można użyć przycisku **Pokaż w Azure Portal** . 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Wyświetlanie, wstrzymywanie, aktywowanie, modyfikowanie i usuwanie istniejących reguł alertów

> [!NOTE]
> Istniejące alerty muszą być zarządzane z poziomu menu alertów z poziomu pulpitu nawigacyjnego Azure Portal. Nie można modyfikować istniejących alertów z bloku zasobów wystąpienia zarządzanego.

Aby wyświetlać, wstrzymywać, uaktywniać, modyfikować i usuwać istniejące alerty:

1. Wyszukaj alerty za pomocą wyszukiwania Azure Portal. Kliknij pozycję alerty.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   Alternatywnie można również kliknąć pozycję alerty na pasku nawigacyjnym platformy Azure, jeśli został on skonfigurowany.

2. W okienku alerty wybierz pozycję Zarządzaj regułami alertów.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   Zostanie wyświetlona lista istniejących alertów. Wybierz pojedynczą istniejącą regułę alertu, aby ją zarządzać. Istniejące aktywne reguły można modyfikować i dostrajać do preferencji użytkownika. Aktywne reguły mogą być również zawieszone bez usuwania. 

## <a name="next-steps"></a>Następne kroki

* Informacje o systemie Azure Monitor alertów można znaleźć [w temacie Omówienie alertów w programie Microsoft Azure](../../azure-monitor/platform/alerts-overview.md)
* Dowiedz się więcej na temat alertów metryk, zobacz [Opis działania alertów metryk w Azure monitor](../../azure-monitor/platform/alerts-metric-overview.md)
* Dowiedz się więcej o konfigurowaniu elementu webhook w alertach, zobacz [wywoływanie elementu webhook przy użyciu klasycznego alertu metryki](../../azure-monitor/platform/alerts-webhooks.md)
* Informacje na temat konfigurowania alertów i zarządzania nimi przy użyciu programu PowerShell, zobacz [reguły akcji](/powershell/module/az.monitor/add-azmetricalertrulev2)
* Informacje na temat konfigurowania alertów i zarządzania nimi za pomocą interfejsu API, zobacz [Azure monitor dokumentacja interfejsu API REST](/rest/api/monitor/)