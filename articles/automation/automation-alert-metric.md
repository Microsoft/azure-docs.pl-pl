---
title: Monitorowanie elementów Runbook Azure Automation przy użyciu alertów metryk
description: W tym artykule opisano sposób konfigurowania alertu metryki na podstawie stanu ukończenia elementu Runbook.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 3a52824a7030d78647d8a664819f439d92d7296d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100593955"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Monitorowanie elementów runbook za pomocą alertów metryki

W tym artykule dowiesz się, jak utworzyć [alert dotyczący metryki](../azure-monitor/alerts/alerts-metric-overview.md) na podstawie stanu ukończenia elementu Runbook.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

## <a name="create-alert"></a>Tworzenie alertu

Alerty umożliwiają zdefiniowanie warunku do monitorowania oraz akcję podejmowaną po spełnieniu warunku.

1. Uruchom usługę Azure Automation w Azure Portal, klikając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **konta usługi Automation**.

2. Na liście kont usługi Automation wybierz konto, dla którego chcesz utworzyć alert. 

3. W obszarze **monitorowanie** wybierz pozycję **alerty** , a następnie wybierz pozycję **+ Nowa reguła alertów**. Zakres dla elementu docelowego jest już zdefiniowany i skojarzony z kontem usługi Automation.

### <a name="configure-alert-criteria"></a>Konfigurowanie kryteriów alertów

1. Kliknij pozycję **Wybierz warunek**. Wybierz pozycję **metryki** dla **typu sygnału** i wybierz pozycję **całkowita liczba zadań** z listy.

2. Na stronie **Konfiguruj logikę sygnału** jest definiowana logika, która wyzwala alert. W obszarze Graf historyczny prezentowane są dwa wymiary, **Nazwa elementu Runbook** i **stan**. Wymiary są różnymi właściwościami metryki, których można użyć do filtrowania wyników. W polu **Nazwa elementu Runbook** wybierz element Runbook, na którym chcesz utworzyć alert, lub pozostaw pustą wartość, aby otrzymywać alerty dotyczące wszystkich elementów Runbook. W polu **stan** wybierz pozycję stan z listy rozwijanej, która ma być monitorowana. Wartości Nazwa elementu Runbook i stan, które pojawiają się na liście rozwijanej, są przeznaczone tylko dla zadań uruchomionych w ciągu ostatniego tygodnia.

   Jeśli chcesz otrzymywać alerty dotyczące stanu lub elementu Runbook, który nie jest wyświetlany na liście rozwijanej, kliknij opcję **Dodaj wartość niestandardową** obok wymiaru. Ta akcja powoduje otwarcie okna dialogowego, które pozwala określić wartość niestandardową, która nie jest ostatnio emitowana dla tego wymiaru. Jeśli wprowadzisz wartość, która nie istnieje dla właściwości, alert nie zostanie wyzwolony.

   > [!NOTE]
   > Jeśli nie określisz nazwy dla wymiaru **Nazwa elementu Runbook** , jeśli istnieją jakiekolwiek elementy Runbook spełniające kryteria stanu, w tym ukryte elementy Runbook systemu, zostanie wyświetlony alert.

    Na przykład, aby otrzymywać alerty, gdy element Runbook zwróci stan _niepowodzenia_ , oprócz określania nazwy elementu Runbook, dla wymiaru **stanu** **nie można** dodać wartości wymiaru niestandardowego.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="Określ wartość wymiaru niestandardowego" border="false":::

3. W obszarze **logika alertu** Zdefiniuj warunek i próg alertu. Poniżej przedstawiono Podgląd zdefiniowanego warunku.

4. W obszarze **oceniane na podstawie** wybierz pozycję TimeSpan dla zapytania i częstotliwość uruchamiania tej kwerendy. Na przykład w przypadku wybrania **w ciągu ostatnich 5 minut** **okresu**, a **co 1 minutę** dla **częstotliwości**, alert szuka liczby elementów Runbook, które spełniają kryteria w ciągu ostatnich 5 minut. To zapytanie jest uruchamiane co minutę, a gdy zdefiniowane kryteria alertu nie będą już znajdować się w oknie 5-minutowym, alert zostanie automatycznie rozwiązany. Po zakończeniu kliknij przycisk **gotowe**.

   ![Wybierz zasób dla alertu](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>Zdefiniuj akcję do wykonania

1. W obszarze **Grupa akcji** wybierz pozycję **Określ grupę akcji**. Grupa akcji to grupa akcji, których można użyć w więcej niż jednym alercie. Mogą one obejmować, ale nie są ograniczone do powiadomień e-mail, elementów Runbook, elementy webhook i wiele innych. Aby dowiedzieć się więcej na temat grup akcji i kroków tworzenia tych, które wysyłają powiadomienia e-mail, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/alerts/action-groups.md).

### <a name="define-alert-details"></a>Definiowanie szczegółów alertu

1. W obszarze **szczegóły reguły alertu** nadaj Alertowi przyjazną nazwę i opis. Ustaw **ważność** na zgodną z warunkiem alertu. Istnieje pięć serwerów z zakresu od 0 do 5. Alerty są traktowane niezależnie od ważności, można dopasować ważność do dopasowania do logiki biznesowej.

1. Reguły domyślne są włączane podczas tworzenia, chyba **że dla opcji** **Włącz regułę alertu** zostanie wybrana opcja Wyłącz. W przypadku alertów utworzonych w stanie wyłączone, możesz je włączyć w przyszłości, gdy wszystko będzie gotowe. Wybierz pozycję **Utwórz regułę alertu** , aby zapisać zmiany.

## <a name="receive-notification"></a>Odbierz powiadomienie

Gdy spełnione są kryteria alertów, Grupa akcji uruchamia zdefiniowaną akcję. W tym artykule jest wysyłana wiadomość e-mail. Na poniższej ilustracji przedstawiono przykład wiadomości e-mail otrzymanej po wyzwoleniu alertu:

![Alert e-mail](./media/automation-alert-activity-log/alert-email.png)

Gdy Metryka nie będzie już poza zdefiniowanym progiem, alert zostanie zdezaktywowany, a grupa akcji uruchamia zdefiniowaną akcję. W przypadku wybrania typu akcji e-mail zostanie wysłana wiadomość e-mail z informacją o rozwiązaniu.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji, zobacz [Używanie alertu do wyzwalania elementu runbook Azure Automation](automation-create-alert-triggered-runbook.md).
