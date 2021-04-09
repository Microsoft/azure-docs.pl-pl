---
title: Konfigurowanie alertów monitorowania dla zadań Azure Stream Analytics
description: W tym artykule opisano sposób konfigurowania monitorowania i alertów dla Azure Stream Analytics zadań przy użyciu Azure Portal.
author: jseb225
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 06/21/2019
ms.openlocfilehash: 7884f8baa24180fcb94f77a45c3457ba62d3f351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018143"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Konfigurowanie alertów dla zadań Azure Stream Analytics

Ważne jest, aby monitorować zadanie Azure Stream Analytics, aby upewnić się, że zadanie działa w sposób ciągły bez żadnych problemów. W tym artykule opisano sposób konfigurowania alertów dla typowych scenariuszy, które powinny być monitorowane. 

Można definiować reguły dotyczące metryk z danych dzienników operacji za pośrednictwem portalu, a także [programowo](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Konfigurowanie alertów w Azure Portal
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Otrzymywanie alertów w przypadku nieoczekiwanego zatrzymania zadania

Poniższy przykład ilustruje sposób konfigurowania alertów w przypadku przejściu zadania w stan niepowodzenia. Ten alert jest zalecany w przypadku wszystkich zadań.

1. W Azure Portal Otwórz zadanie Stream Analytics, dla którego chcesz utworzyć alert.

2. Na stronie **zadanie** przejdź do sekcji **monitorowanie** .  

3. Wybierz **metryki**, a następnie **nową regułę alertu**.

   ![Konfiguracja alertów Stream Analytics Azure Portal](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Nazwa zadania Stream Analytics powinna być automatycznie wyświetlana w obszarze **zasób**. Kliknij pozycję **Dodaj warunek**, a następnie wybierz pozycję **wszystkie operacje administracyjne** w obszarze **Konfiguruj logikę sygnału**.

   ![Wybierz nazwę sygnału dla alertu Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. W obszarze **Konfiguruj logikę sygnałów** Zmień **poziom zdarzenia** na **wszystkie** i Zmień **stan** na **Niepowodzenie**. Pozostaw **zdarzenie zainicjowane przez** puste i wybierz pozycję **gotowe**.

   ![Konfiguruj logikę sygnałów dla Stream Analytics alertu](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Wybierz istniejącą grupę akcji lub Utwórz nową grupę. W tym przykładzie została utworzona nowa grupa akcji o nazwie **TIDashboardGroupActions** z akcją **wiadomości e-mail** , która wysyła wiadomość E-mail do użytkowników z rolą **właściciela** Azure Resource Manager.

   ![Konfigurowanie alertu dla zadania usługi Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. Każdy **zasób**, **warunek** i **grupy akcji** powinny mieć wpis. Należy pamiętać, że aby alerty były wyzwalane, należy spełnić warunki zdefiniowane. Można na przykład mierzyć co 5 minut średnią wartość metryki z ostatnich 15 minut.

   ![Zrzut ekranu przedstawia okno dialogowe Tworzenie reguły z ZASOBem, WARUNKIem i grupą akcji.](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Dodaj **nazwę reguły alertu**, **Opis** i **grupę zasobów** do **szczegółów alertu** , a następnie kliknij pozycję **Utwórz regułę alertu** , aby utworzyć regułę dla zadania Stream Analytics.

   ![Zrzut ekranu przedstawia okno dialogowe Tworzenie reguły z SZCZEGÓŁami ALERTu.](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scenariusze do monitorowania

Poniższe alerty są zalecane do monitorowania wydajności zadania Stream Analytics. Te metryki powinny być oceniane co minutę w ciągu ostatnich 5 minut.

|Metric|Warunek|Agregacja czasu|Próg|Akcje naprawcze|
|-|-|-|-|-|
|Użycie SU%|Większe niż|Maksimum|80|Istnieje wiele czynników, które zwiększają wykorzystanie SU%. Możesz skalować przy użyciu zapytania przetwarzanie równoległe lub zwiększyć liczbę jednostek przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Korzystanie z przetwarzania równoległego zapytań w usłudze Azure Stream Analytics](stream-analytics-parallelization.md).|
|Błędy środowiska uruchomieniowego|Większe niż|Łącznie|0|Sprawdź dzienniki aktywności lub zasobów i wprowadź odpowiednie zmiany w danych wejściowych, kwerendzie lub wyjściowych.|
|Opóźnienie znaku wodnego|Większe niż|Maksimum|Gdy średnia wartość tej metryki w ciągu ostatnich 15 minut jest większa niż tolerancja późnego przybycia (w sekundach). Jeśli tolerancja późnego przybycia nie została zmodyfikowana, wartość domyślna to 5 sekund.|Spróbuj zwiększyć liczbę usług SUs lub przekształcają zapytania. Aby uzyskać więcej informacji na temat usługi SUs, zobacz [Omówienie i Dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Aby uzyskać więcej informacji na temat przekształcają zapytania, zobacz Korzystanie [z usługi Query przetwarzanie równoległe w Azure Stream Analytics](stream-analytics-parallelization.md).|
|Błędy deserializacji danych wejściowych|Większe niż|Łącznie|0|Sprawdź dzienniki aktywności lub zasobów i wprowadź odpowiednie zmiany w danych wejściowych. Aby uzyskać więcej informacji na temat dzienników zasobów, zobacz [Rozwiązywanie problemów Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md)|

## <a name="next-steps"></a>Następne kroki

* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)