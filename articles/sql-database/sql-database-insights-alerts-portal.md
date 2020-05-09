---
title: Konfigurowanie alertów i powiadomień (Azure Portal)
description: Użyj Azure Portal, aby utworzyć alerty SQL Database, które mogą wyzwalać powiadomienia lub automatyzację, gdy zostaną spełnione określone warunki.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: 37eabd4b2f61a82388b1c8d0aaf4aef2320f008e
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801811"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Tworzenie alertów dla Azure SQL Database i baz danych usługi Azure Synapse Analytics przy użyciu Azure Portal

## <a name="overview"></a>Omówienie

W tym artykule opisano sposób konfigurowania alertów dla baz danych z pojedynczym, pulą i magazynem danych w Azure SQL Database i usługi Azure Synapse Analytics (dawniej Azure SQL Data Warehouse) przy użyciu Azure Portal. Alerty mogą wysyłać wiadomość e-mail lub wywoływać element webhook, gdy pewna Metryka (na przykład rozmiar bazy danych lub użycie procesora) osiągnie wartość progową. W tym artykule przedstawiono również najlepsze rozwiązania dotyczące ustawiania okresów alertów.

> [!NOTE]
> Aby uzyskać szczegółowe instrukcje dotyczące wystąpienia zarządzanego przez usługę SQL, zobacz temat [tworzenie alertów dotyczących wystąpienia zarządzanego Azure SQL](sql-database-managed-instance-alerts.md).

Możesz otrzymywać alerty na podstawie metryk monitorowania dla usługi lub zdarzeń w usłudze Azure.

* **Wartości metryk** — alert wyzwalany, gdy wartość określonej metryki przekroczy próg przypisany w dowolnym kierunku. Oznacza to, że jest wyzwalane podczas pierwszego spełnienia warunku, a następnie gdy ten warunek nie jest już spełniony.
* **Zdarzenia dziennika aktywności** — alert może być wyzwalany dla *każdego* zdarzenia lub tylko wtedy, gdy wystąpi pewna liczba zdarzeń.

Można skonfigurować alert, aby wykonać następujące czynności podczas wyzwalania:

* Wysyłanie powiadomień e-mail do administratora usługi i współadministratorów
* Wyślij wiadomość e-mail do dodatkowych wiadomości e-mail, które określisz.
* Wywołanie elementu webhook

Można skonfigurować i uzyskać informacje o regułach alertów za pomocą

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfejs wiersza polecenia (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfejs API REST usługi Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Tworzenie reguły alertu w ramach metryki z Azure Portal

1. W [portalu](https://portal.azure.com/)Znajdź zasób, który chcesz monitorować, i wybierz go.
2. W sekcji monitorowanie wybierz pozycję **alerty** . Tekst i ikona mogą się nieco różnić w zależności od różnych zasobów.  

   ![Monitorowanie](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Wybierz przycisk **Nowa reguła alertu** , aby otworzyć stronę **Tworzenie reguły** .
  ![Tworzenie reguły](media/sql-database-insights-alerts-portal/create-rule.png)

4. W sekcji **warunek** kliknij pozycję **Dodaj**.
  ![Zdefiniuj warunek](media/sql-database-insights-alerts-portal/create-rule.png)
5. Na stronie **Konfiguruj logikę sygnału** Wybierz sygnał.
  ![Wybierz pozycję](media/sql-database-insights-alerts-portal/select-signal.png)sygnał.
6. Po wybraniu sygnału, takiego jak **procent procesora CPU**, zostanie wyświetlona strona **Konfiguruj logikę sygnału** .
  ![Konfigurowanie logiki sygnału](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Na tej stronie Skonfiguruj typ progu, operator, typ agregacji, wartość progu, stopień szczegółowości agregacji oraz częstotliwość obliczania. Następnie kliknij przycisk **gotowe**.
8. Na stronie **Tworzenie reguły**wybierz istniejącą **grupę akcji** lub Utwórz nową. Grupa akcji umożliwia zdefiniowanie akcji, która ma zostać wykonana w przypadku wystąpienia warunku alertu.
  ![Zdefiniuj grupę akcji](media/sql-database-insights-alerts-portal/action-group.png)

9. Zdefiniuj nazwę reguły, podaj opcjonalny opis, wybierz poziom ważności reguły, wybierz, czy włączyć regułę przy tworzeniu reguły, a następnie kliknij pozycję **Utwórz alert reguły** , aby utworzyć alert reguły metryki.

W ciągu 10 minut alert jest aktywny i wyzwalacze opisane wcześniej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [konfigurowaniu elementów webhook w alertach](../azure-monitor/platform/alerts-webhooks.md).
