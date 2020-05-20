---
title: Monitorowanie elementów Runbook Azure Automation przy użyciu alertów metryk
description: W tym artykule opisano sposób monitorowania elementów Runbook w oparciu o metryki.
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: e0d30e3d18ecb279e26731346bca7bb7c12943cb
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685414"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Monitorowanie elementów runbook za pomocą alertów metryki

W tym artykule dowiesz się, jak tworzyć alerty na podstawie stanu ukończenia elementów Runbook.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="create-alert"></a>Tworzenie alertu

Alerty umożliwiają zdefiniowanie warunku do monitorowania oraz akcję podejmowaną po spełnieniu warunku.

W Azure Portal przejdź do konta usługi Automation. W obszarze **monitorowanie**wybierz pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertów**. Zakres dla elementu docelowego jest już zdefiniowany dla konta usługi Automation.

### <a name="configure-alert-criteria"></a>Konfigurowanie kryteriów alertów

1. Kliknij pozycję **+ Dodaj kryteria**. Wybierz pozycję **metryki** dla **typu sygnału**i wybierz pozycję **całkowita liczba zadań** z tabeli.

2. Na stronie **Konfiguruj logikę sygnału** jest definiowana logika, która wyzwala alert. W obszarze Graf historyczny prezentowane są dwa wymiary, **Nazwa elementu Runbook** i **stan**. Wymiary są różnymi właściwościami metryki, których można użyć do filtrowania wyników. W polu **Nazwa elementu Runbook**wybierz element Runbook, na którym chcesz utworzyć alert, lub pozostaw pustą wartość, aby otrzymywać alerty dotyczące wszystkich elementów Runbook. W polu **stan**wybierz pozycję stan z listy rozwijanej, która ma być monitorowana. Wartości Nazwa elementu Runbook i stan, które pojawiają się na liście rozwijanej, są przeznaczone tylko dla zadań uruchomionych w ciągu ostatniego tygodnia.

   Jeśli chcesz otrzymywać alerty dotyczące stanu lub elementu Runbook, który nie jest wyświetlany na liście rozwijanej, kliknij **\+** obok wymiaru. Ta akcja powoduje otwarcie okna dialogowego, w którym można wprowadzić wartość niestandardową, która nie jest ostatnio emitowana dla tego wymiaru. Jeśli wprowadzisz wartość, która nie istnieje dla właściwości, alert nie zostanie wyzwolony.

   > [!NOTE]
   > Jeśli nie zastosowano nazwy dla wymiaru **runbookname** , jeśli istnieją jakiekolwiek elementy Runbook spełniające kryteria stanu, w tym ukryte elementy Runbook systemu, zostanie wyświetlony alert.

3. W obszarze **logika alertu**Zdefiniuj warunek i próg alertu. Poniżej przedstawiono Podgląd zdefiniowanego warunku.

4. W obszarze **oceniane na podstawie**wybierz pozycję przedział czasu dla zapytania i częstotliwość, z jaką ma ona być uruchomiona. Na przykład w przypadku wybrania **w ciągu ostatnich 5 minut** **okresu** i **co 1 minuty** dla **częstotliwości**, alert szuka liczby elementów Runbook, które spełniają kryteria w ciągu ostatnich 5 minut. To zapytanie jest uruchamiane co minutę, a gdy zdefiniowane kryteria alertu nie będą już znajdować się w oknie 5-minutowym, alert zostanie automatycznie rozwiązany. Na koniec kliknij przycisk **Gotowe**.

   ![Wybierz zasób dla alertu](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definiowanie szczegółów alertu

1. W obszarze **2. Zdefiniuj szczegóły alertu**, nadaj Alertowi przyjazną nazwę i opis. Ustaw **ważność** na zgodną z warunkiem alertu. Istnieje pięć serwerów z zakresu od 0 do 5. Alerty są traktowane niezależnie od ważności, można dopasować ważność do dopasowania do logiki biznesowej.

1. W dolnej części sekcji jest przycisk, który umożliwia włączenie reguły po zakończeniu. Reguły domyślne są włączane podczas tworzenia. W przypadku wybrania opcji nie można utworzyć alertu, który zostanie utworzony w stanie **wyłączonym** . Na stronie **reguły** w Azure monitor możesz ją zaznaczyć, a następnie kliknij pozycję **Włącz** , aby włączyć alert, gdy wszystko będzie gotowe.

### <a name="define-the-action-to-take"></a>Zdefiniuj akcję do wykonania

1. W obszarze **3. Zdefiniuj grupę akcji**, kliknij pozycję **+ Nowa grupa akcji**. Grupa akcji to grupa akcji, których można użyć w więcej niż jednym alercie. Mogą one obejmować, ale nie są ograniczone do powiadomień e-mail, elementów Runbook, elementy webhook i wiele innych. Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie i zarządzanie grupami akcji](../azure-monitor/platform/action-groups.md)

1. W polu **Nazwa grupy akcji** wpisz przyjazną nazwę i nazwę krótką. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

1. W sekcji **Akcje** w obszarze **Typ akcji**wybierz pozycję **poczta e-mail/SMS/wypychanie/głos**.

1. Na stronie **E-mail/SMS/Push/Głos** nadaj nazwę. Zaznacz pole wyboru **E-mail** i wpisz prawidłowy adres e-mail, który ma być używany.

   ![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-alert-activity-log/add-action-group.png)

1. Kliknij przycisk **OK** na stronie **E-mail/SMS/Push/Głos**, aby ją zamknąć, a następnie kliknij przycisk **OK**, aby zamknąć stronę **Dodawanie grupy akcji**. Nazwa określona na tej stronie jest zapisywana jako **Nazwa akcji**.

1. Po zakończeniu kliknij przycisk **Zapisz**. Ta akcja tworzy regułę, która powiadamia użytkownika, gdy element Runbook został ukończony z określonym stanem.

> [!NOTE]
> Po dodaniu adresu e-mail do grupy akcji zostanie wysłana wiadomość e-mail z powiadomieniem informującą o tym, że adres został dodany do grupy akcji.

## <a name="receive-notification"></a>Odbierz powiadomienie

Gdy spełnione są kryteria alertów, Grupa akcji uruchamia zdefiniowaną akcję. W tym artykule jest wysyłana wiadomość e-mail. Na poniższej ilustracji przedstawiono przykład wiadomości e-mail otrzymanej po wyzwoleniu alertu:

![Alert e-mail](./media/automation-alert-activity-log/alert-email.png)

Gdy Metryka nie będzie już poza zdefiniowanym progiem, alert zostanie zdezaktywowany, a grupa akcji uruchamia zdefiniowaną akcję. W przypadku wybrania typu akcji e-mail zostanie wysłana wiadomość e-mail z informacją o rozwiązaniu.

## <a name="next-steps"></a>Następne kroki

Przejdź do poniższego artykułu, aby dowiedzieć się więcej na temat innych sposobów integrowania alertów z kontem usługi Automation.

> [!div class="nextstepaction"]
> [Użyj alertu, aby wyzwolić Azure Automation element Runbook](automation-create-alert-triggered-runbook.md)
