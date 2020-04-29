---
title: Rozwiązywanie problemów z alertami Azure Monitor i powiadomieniami
description: Typowe problemy związane z alertami Azure Monitor i możliwymi rozwiązaniami.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132325"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Rozwiązywanie problemów z alertami Azure Monitor 

W tym artykule omówiono typowe problemy w Azure Monitor alertów.

Alerty Azure Monitor z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników systemu. Aby uzyskać więcej informacji na temat alertów, zobacz [Omówienie alertów w Microsoft Azure](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Akcja lub powiadomienie dotyczące mojego alertu nie zadziałały zgodnie z oczekiwaniami

Jeśli w Azure Portal widzisz wyzwolony alert, ale masz problem z niektórymi akcjami lub powiadomieniami, zobacz następujące sekcje.

## <a name="did-not-receive-expected-email"></a>Nie odebrano oczekiwanego adresu e-mail

Jeśli w Azure Portal widzisz wyzwolony alert, ale nie otrzymano wiadomości e-mail skonfigurowanej na jej temat, wykonaj następujące czynności: 

1. Czy **wiadomość E-mail została pominięta przez [regułę akcji](alerts-action-rules.md)**? 

    Zapoznaj się z tematem wygenerowanego alertu w portalu i sprawdź kartę Historia dla pominiętych [grup akcji](action-groups.md): 

    ![Historia pomijania reguły akcji alertu](media/alerts-troubleshoot/history-action-rule.png)

1. **Czy typem akcji jest "Azure Resource Manager" roli "E-mail"?**

    Ta akcja sprawdza tylko Azure Resource Manager przypisań ról, które znajdują się w zakresie subskrypcji, i typu *użytkownika*.  Upewnij się, że rola została przypisana na poziomie subskrypcji, a nie na poziomie zasobu lub grupy zasobów.

1. **Czy Twój serwer poczty e-mail i Skrzynka pocztowa akceptują zewnętrzne wiadomości e-mail?**

    Sprawdź, czy wiadomości e-mail z tych trzech adresów nie są blokowane:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    W przypadku wewnętrznych list wysyłkowych lub list dystrybucyjnych są one używane do blokowania wiadomości e-mail z zewnętrznych adresów e-mail. Musisz dozwolonych powyższe adresy e-mail.  
    Aby przetestować, Dodaj zwykły służbowy adres e-mail (a nie listę wysyłkową) do grupy akcji i sprawdź, czy do tej wiadomości dotarły alerty. 

1. **Czy wiadomość e-mail została przetworzona przez reguły skrzynki odbiorczej czy filtr spamu?** 

    Sprawdź, czy nie istnieją reguły skrzynki odbiorczej, które usuwają te wiadomości e-mail lub przenoszą je do folderu bocznego. Na przykład reguły skrzynki odbiorczej mogą przechwytywać określonych nadawców lub określone słowa w temacie.

    Sprawdź również:
    
      - Ustawienia spamu klienta poczty e-mail (na przykład Outlook, Gmail)
      - ustawienia limitów nadawcy/ustawień spamu/ustawień kwarantanny serwera poczty e-mail (na przykład Exchange, Office 365, G-Suite)
      - Ustawienia urządzenia zabezpieczeń poczty e-mail (np. Barracuda, Cisco). 

1. **Czy przypadkowo Anulowano subskrypcję grupy akcji?** 

    Wiadomości e-mail dotyczące alertów zawierają link umożliwiający anulowanie subskrypcji grupy akcji. Aby sprawdzić, czy nie masz przypadkowo anulowania subskrypcji z tej grupy akcji:

    1. Otwórz grupę akcji w portalu i Sprawdź kolumnę Stan:

    ![kolumna stanu grupy akcji](media/alerts-troubleshoot/action-group-status.png)

    2. Wyszukaj potwierdzenie anulowania subskrypcji w wiadomości e-mail:

    ![Anulowano subskrypcję z grupy akcji alertu](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Aby subskrybować ponownie — Użyj linku w otrzymanej wiadomości e-mail z potwierdzeniem anulowania subskrypcji lub usuń adres e-mail z grupy akcji, a następnie ponownie go Dodaj. 
 
1. **Czy masz ograniczoną liczbę wiadomości e-mail na jeden adres e-mail?** 

    Liczba wiadomości e-mail jest [ograniczona](alerts-rate-limiting.md) do maksymalnie 100 wiadomości e-mail na każdy adres e-mail. W przypadku przekazania tego progu dodatkowe powiadomienia e-mail zostaną usunięte.  Sprawdź, czy został wyświetlony komunikat informujący o tym, że Twój adres e-mail jest tymczasowo ograniczony: 
 
   ![Szybkość wiadomości e-mail ograniczona](media/alerts-troubleshoot/email-paused.png)

   Jeśli chcesz otrzymywać dużo powiadomień bez ograniczania szybkości, rozważ użycie innej akcji, takiej jak element webhook, aplikacja logiki, funkcja platformy Azure lub elementy Runbook usługi Automation, żadna z nich nie jest ograniczona. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Nie odebrano oczekiwanego komunikatu SMS, połączenia głosowego lub powiadomienia wypychanego

Jeśli w portalu widzisz wyzwolony alert, ale nie otrzymano wiadomości SMS, połączenia głosowego ani powiadomienia wypychanego, które zostały przez Ciebie skonfigurowane, wykonaj następujące kroki: 

1. **Czy akcja została pominięta przez [regułę akcji](alerts-action-rules.md)?** 

    Zapoznaj się z tematem wygenerowanego alertu w portalu i sprawdź kartę Historia dla pominiętych [grup akcji](action-groups.md): 

    ![Historia pomijania reguły akcji alertu](media/alerts-troubleshoot/history-action-rule.png)

   W przypadku niezamierzonego zamiaru można zmodyfikować, wyłączyć lub usunąć regułę akcji.
 
1. **SMS/Voice: czy Twój numer telefonu jest poprawny?**

   Sprawdź akcję SMS pod kątem pisowni w kodzie kraju lub numerze telefonu. 
 
1. **Wiadomość SMS/głos: czy masz ograniczoną szybkość?** 

    Połączenia SMS i głosowe mają stawkę ograniczoną do nie więcej niż jedno powiadomienie co pięć minut na numer telefonu. W przypadku przekazania tego progu powiadomienia zostaną usunięte. 

      - Połączenie głosowe — Sprawdź swoją historię połączeń i zobacz, czy w ciągu ostatnich pięciu minut wystąpiło inne wywołanie z platformy Azure. 
      - SMS — Sprawdź, czy Historia programu SMS zawiera komunikat informujący o tym, że Twój numer telefonu ma ograniczoną stawkę. 

    Jeśli chcesz otrzymywać dużo powiadomień bez ograniczania szybkości, rozważ użycie innej akcji, takiej jak element webhook, aplikacja logiki, funkcja platformy Azure lub elementy Runbook usługi Automation, żadna z nich nie jest ograniczona. 
 
1. **SMS: Czy przypadkowo Anulowano subskrypcję grupy akcji?**

    Otwórz historię programu SMS i sprawdź, czy wybrano opcję dostarczania wiadomości SMS z tej konkretnej grupy akcji (przy użyciu opcji Wyłącz odpowiedź action_group_short_name) lub z wszystkich grup akcji (przy użyciu opcji Zatrzymaj odpowiedź). Aby ponownie subskrybować, Wyślij odpowiednie polecenie SMS (Włącz action_group_short_name lub Uruchom) lub Usuń akcję SMS z grupy akcji, a następnie ponownie go Dodaj.  Aby uzyskać więcej informacji, zobacz temat [zachowanie alertu programu SMS w grupach akcji](alerts-sms-behavior.md).

1. **Czy chcesz przypadkowo zablokować powiadomienia na telefonie?**

   Większość telefonów komórkowych umożliwia blokowanie wywołań lub wiadomości SMS z określonych numerów telefonów lub krótkich kodów lub blokowanie powiadomień wypychanych z określonych aplikacji (takich jak aplikacja mobilna platformy Azure). Aby sprawdzić, czy powiadomienia na telefonie zostały przypadkowo zablokowane, zapoznaj się z dokumentacją dotyczącą systemu operacyjnego telefonu i modelu lub Testuj przy użyciu innego numeru telefonu i telefonu. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Oczekiwano innego typu akcji do wyzwolenia, ale nie 

Jeśli w portalu widzisz wygenerowanego alertu, ale jego skonfigurowana akcja nie została wyzwolona, wykonaj następujące czynności: 

1. **Czy akcja została pominięta przez regułę akcji?** 

    Zapoznaj się z tematem wygenerowanego alertu w portalu i sprawdź kartę Historia dla pominiętych [grup akcji](action-groups.md): 

    ![Historia pomijania reguły akcji alertu](media/alerts-troubleshoot/history-action-rule.png)
 
    W przypadku niezamierzonego zamiaru można zmodyfikować, wyłączyć lub usunąć regułę akcji. 

1. **Czy element webhook nie został wyzwolony?**

    1. **Czy źródłowe adresy IP zostały zablokowane?**
    
       Dozwolonych [adresy IP](action-groups.md#action-specific-information) , z których wywoływana jest element webhook.

    1. **Czy punkt końcowy elementu webhook działa prawidłowo?**

       Upewnij się, że skonfigurowany punkt końcowy elementu webhook jest poprawny, a punkt końcowy działa poprawnie. Sprawdź dzienniki elementu webhook lub instrumentację kodu, aby było możliwe badanie (na przykład rejestrowanie przychodzącego ładunku). 

    1. **Czy są wywoływane zapasy czasu lub Microsoft Teams?**  
    Każdy z tych punktów końcowych oczekuje określonego formatu JSON. Postępuj zgodnie z [tymi instrukcjami](action-groups-logic-app.md) , aby zamiast tego skonfigurować akcję aplikacji logiki.

    1. **Czy element webhook stał się nieodpowiedzią lub zwrócił błędy?** 

        Nasz limit czasu dla odpowiedzi elementu webhook wynosi 10 sekund. Wywołanie elementu webhook zostanie ponowione do dwóch dodatkowych razy w przypadku zwrócenia następujących kodów stanu HTTP: 408, 429, 503, 504 lub gdy punkt końcowy HTTP nie odpowiada. Pierwsze ponowienie próby odbywa się po 10 sekundach. Drugi i ostatni proces ponawiania prób następuje po 100 sekundach. Jeśli drugie ponowienie zakończy się niepowodzeniem, punkt końcowy nie zostanie ponownie wywołany przez 30 minut dla żadnej grupy akcji.

## <a name="action-or-notification-happened-more-than-once"></a>Akcja lub powiadomienie zaszło więcej niż raz 

Jeśli otrzymasz powiadomienie o alercie (na przykład w wiadomości e-mail lub wiadomości SMS) więcej niż raz lub akcja alertu (taka jak element webhook lub funkcja platformy Azure) została wyzwolona wiele razy, wykonaj następujące czynności: 

1. **Czy jest to naprawdę ten sam alert?** 

    W niektórych przypadkach w tym samym czasie są uruchamiane wiele podobnych alertów. Z tego samego alertu może się zdarzyć, że jego akcje wywołały wiele razy. Na przykład reguła alertu dziennika aktywności może być skonfigurowana w taki sposób, aby był uruchamiany zarówno po rozpoczęciu zdarzenia, jak i po jego zakończeniu (powodzenie lub niepowodzenie), nie filtrując wartości w polu Stan zdarzenia. 

    Aby sprawdzić, czy te akcje lub powiadomienia pochodzą z różnych alertów, sprawdź szczegóły alertu, takie jak sygnatura czasowa i identyfikator alertu lub jego identyfikator korelacji. Alternatywnie Sprawdź listę wyzwolonych alertów w portalu. W takim przypadku konieczne będzie dostosowanie logiki reguły alertu lub skonfigurowanie źródła alertu. 

1. **Czy akcja powtarza się w wielu grupach akcji?** 

    Po uruchomieniu alertu każda z jego grup akcji jest przetwarzana niezależnie. Tak więc jeśli akcja (na przykład adres e-mail) pojawia się w wielu wyzwolonych grupach akcji, będzie ona wywoływana raz dla każdej grupy akcji. 

    Aby sprawdzić, które grupy akcji zostały wyzwolone, sprawdź kartę Historia alertu. Zobaczysz obie grupy akcji zdefiniowane w regule alertu i grupy akcji dodane do alertu według reguł akcji: 

    ![Powtórzona akcja w wielu grupach akcji](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Akcja lub powiadomienie ma nieoczekiwaną zawartość

Jeśli alert został odebrany, ale uważasz, że brakuje niektórych pól lub są one nieprawidłowe, wykonaj następujące kroki: 

1. **Czy wybrano poprawny format dla akcji?** 

    Każdy typ akcji (poczta e-mail, element webhook itp.) ma dwa formaty — domyślny, starszy format i [nowszy format wspólnego schematu](alerts-common-schema.md). Podczas tworzenia grupy akcji należy określić żądany format dla każdej akcji — różne akcje w grupach akcji mogą mieć różne formaty. 

    Na przykład dla akcji elementu webhook: 

    ![Akcja elementu webhook — opcja schematu](media/alerts-troubleshoot/webhook.png)

    Sprawdź, czy format określony na poziomie akcji jest oczekiwany. Można na przykład opracować kod, który reaguje na alerty (element webhook, funkcja, aplikacja logiki itp.), oczekiwano jednego formatu, ale później w akcji lub innej osobie określono inny format.  

    Sprawdź również format ładunku (JSON) dla [alertów dziennika aktywności](activity-log-alerts-webhook.md), dla alertów [wyszukiwania w dzienniku](alerts-log-webhook.md) (zarówno Application Insights jak i log Analytics), dla [alertów dotyczących metryk](alerts-metric-near-real-time.md#payload-schema), dla [typowego schematu alertu](alerts-common-schema-definitions.md)oraz dla przestarzałych [alertów metryk klasycznych](alerts-webhooks.md).

 
1. **Alerty dziennika aktywności: czy informacje są dostępne w dzienniku aktywności?** 

    [Alerty dziennika aktywności](activity-log-alerts.md) są alertami opartymi na zdarzeniach, które są zapisywane w dzienniku aktywności platformy Azure, takich jak zdarzenia dotyczące tworzenia, aktualizowania lub usuwania zasobów platformy Azure, kondycji usług i zdarzeń związanych z kondycją zasobów albo wyniki z Azure Advisor i Azure Policy. Jeśli alert został odebrany na podstawie dziennika aktywności, ale brakuje niektórych pól lub są one nieprawidłowe, najpierw sprawdź zdarzenia w dzienniku aktywności. Jeśli zasób platformy Azure nie zapisuje pól, których szukasz w swoim zdarzeniu dziennika aktywności, te pola nie zostaną uwzględnione w odpowiednim alercie. 

## <a name="action-rule-is-not-working-as-expected"></a>Reguła akcji nie działa zgodnie z oczekiwaniami 

Jeśli w portalu widzisz wyzwolony alert, ale powiązana reguła działania nie działa zgodnie z oczekiwaniami, wykonaj następujące kroki: 

1. **Czy reguła akcji jest włączona?** 

    Sprawdź kolumnę Stan reguły akcji, aby sprawdzić, czy powiązana rola akcji jest włączona. 

    ![zdjęć](media/alerts-troubleshoot/action-rule-status.png) 

    Jeśli nie jest włączona, można włączyć regułę akcji, wybierając ją i klikając pozycję Włącz. 

1. **Czy jest to alert kondycji usługi?** 

    Reguły akcji nie wpływają na alerty kondycji usługi (Monitor Service = "Service Health"). 

1. **Czy reguła akcji działała na Twoim alercie?** 

    Sprawdź, czy reguła akcji przetworzyła alert, klikając wygenerowanego alertu w portalu i przyjrzyj się karcie historia.

    Oto przykład reguły akcji pomijania wszystkich grup akcji: 
 
     ![Historia pomijania reguły akcji alertu](media/alerts-troubleshoot/history-action-rule.png)

    Oto przykład reguły akcji dodając kolejną grupę akcji:

    ![Powtórzona akcja w wielu grupach akcji](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Czy zakres reguł akcji i filtr pasuje do wygenerowanego alertu?** 

    Jeśli uważasz, że reguła akcji powinna być wyzwalana, ale nie powinna być uruchamiana, ale należy uważnie sprawdzić zakres reguł akcji i warunki filtrowania w porównaniu z właściwościami wywoływanego alertu. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Jak znaleźć identyfikator alertu dla wywoływanego alertu

Podczas otwierania sprawy dotyczącej określonego wygenerowanego alertu (takiego jak — jeśli nie otrzymano powiadomienia e-mail), należy podać identyfikator alertu. 

Aby go zlokalizować, wykonaj następujące kroki:

1. W Azure Portal przejdź do listy wyzwolonych alertów i Znajdź określony alert. Można użyć filtrów, aby ułatwić ich znalezienie. 

1. Kliknij alert, aby otworzyć Szczegóły alertu. 

1. Przewiń w dół pola alertu pierwszej karty (karta Podsumowanie) do momentu jego znalezienia i skopiowania. To pole zawiera również przycisk pomocnika kopiowania do schowka, którego można użyć.  

    ![Znajdowanie identyfikatora alertu](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problem z tworzeniem, aktualizowaniem lub usuwaniem reguł akcji w Azure Portal

Jeśli wystąpił błąd podczas próby utworzenia, zaktualizowania lub usunięcia [reguły akcji](alerts-action-rules.md), wykonaj następujące kroki: 

1. **Czy pojawił się błąd uprawnień?**  

    Należy mieć [wbudowaną rolę współautora monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)lub określone uprawnienia związane z regułami akcji i alertami.

1. **Czy zweryfikowano parametry reguły akcji?**  

    Zapoznaj się z [dokumentacją reguły akcji](alerts-action-rules.md)lub z [akcją programu PowerShell Set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) . 


## <a name="next-steps"></a>Następne kroki
- Jeśli używasz alertu dziennika, zobacz również [Rozwiązywanie problemów z alertami dzienników](alert-log-troubleshoot.md).
- Wróć do [Azure Portal](https://portal.azure.com) , aby sprawdzić, czy problem został rozwiązany z powyższymi wskazówkami 
