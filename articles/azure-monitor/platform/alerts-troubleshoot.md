---
title: Rozwiązywanie problemów z alertami Azure Monitor i powiadomieniami
description: Typowe problemy związane z alertami Azure Monitor i możliwymi rozwiązaniami.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: 856aeb4dabc64edc2ade2869fddc3d14cb21a81b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942161"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Rozwiązywanie problemów z alertami Azure Monitor

W tym artykule omówiono typowe problemy w Azure Monitor alerty i powiadomienia.

Alerty Azure Monitor z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów przed zapisaniem ich przez użytkowników systemu. Aby uzyskać więcej informacji na temat alertów, zobacz [Omówienie alertów w Microsoft Azure](alerts-overview.md).

Jeśli masz problem z uruchamianiem alertu lub nie jest on uruchamiany w oczekiwany sposób, zapoznaj się z artykułami poniżej. W Azure Portal można zobaczyć "uruchomienia" alertów.

- [Rozwiązywanie problemów z alertami Azure Monitor w Microsoft Azure](alerts-troubleshoot-metric.md)  
- [Rozwiązywanie problemów z alertami rejestrowania Azure Monitor w Microsoft Azure](alerts-troubleshoot-metric.md)

Jeśli alert jest uruchamiany zgodnie z Azure Portal, ale nie występują odpowiednie powiadomienia, Skorzystaj z informacji zawartych w dalszej części tego artykułu, aby rozwiązać ten problem.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Akcja lub powiadomienie dotyczące mojego alertu nie zadziałały zgodnie z oczekiwaniami

Jeśli w Azure Portal widzisz wyzwolony alert, ale masz problem z niektórymi akcjami lub powiadomieniami, zobacz następujące sekcje.

## <a name="did-not-receive-expected-email"></a>Nie odebrano oczekiwanego adresu e-mail

Jeśli w witrynie Azure Portal widzisz wyzwolony alert, ale skonfigurowana dla niego akcja, taka jak wiadomość e-mail, nie została wywołana, wykonaj następujące czynności:

1. Czy **wiadomość E-mail została pominięta przez [regułę akcji](alerts-action-rules.md)**?

    Klikając wyzwolony alert w portalu i przeglądając kartę historii, sprawdź, czy istnieją pominięte [grupy akcji](action-groups.md):

    ![Historia pomijania reguły akcji alertu](media/alerts-troubleshoot/history-action-rule.png)

1. **Czy typem akcji jest "Azure Resource Manager" roli "E-mail"?**

    Ta akcja sprawdza tylko Azure Resource Manager przypisań ról, które znajdują się w zakresie subskrypcji, i typu *użytkownika*.  Upewnij się, że rola została przypisana na poziomie subskrypcji, a nie na poziomie zasobu lub grupy zasobów.

1. **Czy Twój serwer poczty e-mail i Skrzynka pocztowa akceptują zewnętrzne wiadomości e-mail?**

    Sprawdź, czy nie są blokowane wiadomości e-mail z tych trzech adresów:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Zdarza się często, że wiadomości e-mail z zewnętrznych adresów e-mail są blokowane w wewnętrznych listach wysyłkowych lub listach dystrybucyjnych. Musisz zezwolić na pocztę e-mail przy użyciu podanych powyżej adresów.  
    Aby to przetestować, dodaj do grupy akcji zwykły służbowy adres e-mail (nie listę wysyłkową) i sprawdź, czy do tej skrzynki pocztowej trafiają alerty.

1. **Czy wiadomość e-mail została przetworzona przez reguły skrzynki odbiorczej czy filtr spamu?**

    Sprawdź, czy nie ma reguł skrzynki odbiorczej, które powodują usunięcie tych wiadomości e-mail lub ich przeniesienie do innego folderu. Na przykład reguły skrzynki odbiorczej mogą przechwytywać określonych nadawców lub określone słowa w temacie.

    Sprawdź również następujące elementy:

   - Ustawienia spamu w kliencie poczty e-mail (takim jak Outlook lub Gmail)
      - ustawienia limitów nadawcy/ustawień spamu/ustawień kwarantanny serwera poczty e-mail (na przykład Exchange, Microsoft 365, G-Suite)
      - Ustawienia urządzenia zabezpieczeń poczty e-mail (np. Barracuda, Cisco).

1. **Czy przypadkowo Anulowano subskrypcję grupy akcji?**

    W wiadomościach e-mail zawierających alerty znajduje się link umożliwiający anulowanie subskrypcji grupy akcji. Aby sprawdzić, czy nastąpiło przypadkowe anulowanie subskrypcji grupy akcji, wykonaj dowolną z następujących czynności:

    1. Otwórz w portalu grupę akcji i sprawdź kolumnę Stan:

    ![kolumna stanu w grupie akcji](media/alerts-troubleshoot/action-group-status.png)

    2. Sprawdź, czy otrzymano wiadomość e-mail z potwierdzeniem anulowania subskrypcji:

    ![anulowanie subskrypcji grupy akcji alertów](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Aby ponownie zasubskrybować grupę akcji, skorzystaj z linku w otrzymanej wiadomości e-mail z potwierdzeniem anulowania subskrypcji lub usuń adres e-mail z grupy akcji, a następnie ponownie go dodaj. 
 
1. **Czy masz ograniczoną liczbę wiadomości e-mail na jeden adres e-mail?**

    [Liczba wiadomości e-mail jest ograniczona](alerts-rate-limiting.md) do maksymalnie 100 wiadomości na godzinę dla każdego adresu e-mail. Po przekroczeniu tego progu kolejne wiadomości e-mail z powiadomieniami są usuwane.  Sprawdź, czy w skrzynce odbiorczej jest wiadomość e-mail informująca o tym, że na Twój adres e-mail nałożono tymczasowe ograniczenie liczby wiadomości: 
 
   ![Ograniczenie liczby wiadomości e-mail](media/alerts-troubleshoot/email-paused.png)

   Jeśli chcesz otrzymywać dużo powiadomień bez ograniczenia częstotliwości, rozważ użycie innej akcji, takiej jak element webhook, aplikacja logiki, funkcja platformy Azure lub elementy runbook automatyzacji, które nie są ograniczane. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Nie odebrano oczekiwanego komunikatu SMS, połączenia głosowego lub powiadomienia wypychanego

Jeśli w portalu widzisz wyzwolony alert, ale skonfigurowana dla niego akcja, taka jak wiadomość SMS, połączenie głosowe lub powiadomienie push, nie została wywołana, wykonaj następujące czynności: 

1. **Czy akcja została pominięta przez [regułę akcji](alerts-action-rules.md)?**

    Klikając wyzwolony alert w portalu i przeglądając kartę historii, sprawdź, czy istnieją pominięte [grupy akcji](action-groups.md): 

    ![Historia pomijania reguły akcji alertu](media/alerts-troubleshoot/history-action-rule.png)

   Jeśli było to niezamierzone, możesz zmodyfikować, wyłączyć lub usunąć regułę akcji.
 
1. **SMS/Voice: czy Twój numer telefonu jest poprawny?**

   Sprawdź akcję wiadomości SMS pod kątem literówek w kodzie kraju lub numerze telefonu.
 
1. **Wiadomość SMS/głos: czy masz ograniczoną szybkość?**

    Wiadomości SMS i połączenia głosowe mają limity częstotliwości wynoszące nie więcej, niż jedno powiadomienie co pięć minut na numer telefonu. W przypadku przekroczenia tego progu powiadomienia nie są wysyłane.

      - Połączenie głosowe — sprawdź swoją historię połączeń i zobacz, czy w ciągu ostatnich pięciu minut miało miejsce inne połączenie z platformy Azure.
      - Wiadomości SMS — sprawdź, czy w skrzynce wiadomości SMS jest wiadomość wskazująca, że Twój numer telefonu ma ograniczoną liczbę wiadomości.

    Jeśli chcesz otrzymywać dużo powiadomień bez ograniczenia częstotliwości, rozważ użycie innej akcji, takiej jak element webhook, aplikacja logiki, funkcja platformy Azure lub elementy runbook automatyzacji, które nie są ograniczane. 
 
1. **SMS: Czy przypadkowo Anulowano subskrypcję grupy akcji?**

    Otwórz historię programu SMS i sprawdź, czy wybrano opcję dostarczania wiadomości SMS z tej konkretnej grupy akcji (przy użyciu opcji Wyłącz odpowiedź action_group_short_name) lub z wszystkich grup akcji (przy użyciu opcji Zatrzymaj odpowiedź). Aby subskrybować ponownie, wyślij odpowiednie polecenie SMS (ENABLE krótka_nazwa_grupy_akcji lub START) lub usuń akcję wiadomości SMS z grupy akcji, a następnie dodaj ją ponownie.  Aby uzyskać więcej informacji, zobacz [Zachowanie alertu SMS w grupach akcji](alerts-sms-behavior.md).

1. **Czy chcesz przypadkowo zablokować powiadomienia na telefonie?**

   Większość telefonów komórkowych umożliwia blokowanie połączeń lub wiadomości SMS z określonych numerów telefonów lub krótkich kodów oraz blokowanie powiadomień push z określonych aplikacji (takich jak aplikacja mobilna platformy Azure). Aby sprawdzić, czy przypadkowo nie zablokowano powiadomień na telefonie, zapoznaj się z dokumentacją dotyczącą systemu operacyjnego lub modelu Twojego telefonu bądź przeprowadź test przy użyciu innego telefonu i numeru telefonu.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Oczekiwano innego typu akcji do wyzwolenia, ale nie 
   
Jeśli w portalu widzisz wyzwolony alert, ale jego skonfigurowana akcja nie została wywołana, wykonaj następujące czynności:

1. **Czy akcja została pominięta przez regułę akcji?**

    Klikając wyzwolony alert w portalu i przeglądając kartę historii, sprawdź, czy istnieją pominięte [grupy akcji](action-groups.md):

    ![Historia pomijania reguły akcji alertu](media/alerts-troubleshoot/history-action-rule.png)
 
    Jeśli było to niezamierzone, możesz zmodyfikować, wyłączyć lub usunąć regułę akcji.

1. **Czy element webhook nie został wyzwolony?**

    1. **Czy źródłowe adresy IP zostały zablokowane?**
    
       Dodaj [adresy IP](action-groups.md#action-specific-information) , z których jest wywoływany element webhook, z listy dozwolonych.

    1. **Czy punkt końcowy elementu webhook działa prawidłowo?**

       Sprawdź, czy skonfigurowany punkt końcowy elementu webhook jest poprawny oraz czy punkt końcowy działa prawidłowo. Sprawdź dzienniki elementu webhook lub instrumentuj kod, aby umożliwić analizę (na przykład rejestrowanie ładunku przychodzącego).

    1. **Czy są wywoływane zapasy czasu lub Microsoft Teams?**  
    Każdy z tych punktów końcowych oczekuje określonego formatu JSON. Postępuj zgodnie z [tymi instrukcjami](action-groups-logic-app.md), aby w zamian skonfigurować akcję aplikacji logiki.

    1. **Czy element webhook stał się nieodpowiedzią lub zwrócił błędy?** 

        Limit czasu dla odpowiedzi elementu webhook wynosi 10 sekund. Wywołanie elementu webhook zostanie ponowione maksymalnie dwa razy, gdy zostaną zwrócone następujące kody stanu HTTP: 408, 429, 503, 504, lub gdy punkt końcowy HTTP nie odpowiada. Pierwsze ponowienie próby odbywa się po 10 sekundach. Drugie (ostatnie) ponowienie próby odbywa się po 100 sekundach. Jeśli drugie ponowienie zakończy się niepowodzeniem, punkt końcowy nie zostanie ponownie wywołany przez 30 minut dla żadnej grupy akcji.

## <a name="action-or-notification-happened-more-than-once"></a>Akcja lub powiadomienie zaszło więcej niż raz 

W przypadku otrzymania powiadomienia o alercie (na przykład za pomocą wiadomości e-mail lub wiadomości SMS) więcej niż raz lub wielokrotnego wyzwolenia akcji alertu (takiej jak element webhook lub funkcja platformy Azure), wykonaj następujące czynności: 

1. **Czy jest to naprawdę ten sam alert?** 

    W niektórych przypadkach wiele podobnych alertów jest uruchamianych mniej więcej w tym samym czasie. Może się więc tylko wydawać, że ten sam alert wyzwolił swoje akcje wiele razy. Na przykład reguła alertu dziennika aktywności może być skonfigurowana w taki sposób, aby była uruchamiana zarówno po rozpoczęciu zdarzenia, jak i po jego zakończeniu (pomyślnym lub niepomyślnym) bez filtrowania wartości w polu stanu zdarzenia. 

    Aby sprawdzić, czy te akcje lub powiadomienia pochodzą z różnych alertów, sprawdź szczegóły alertu, takie jak sygnatura czasowa i identyfikator alertu lub jego identyfikator korelacji. Możesz też sprawdzić listę wyzwolonych alertów w portalu. W takim przypadku konieczne będzie dostosowanie logiki reguły alertu lub skonfigurowanie źródła alertu. 

1. **Czy akcja powtarza się w wielu grupach akcji?** 

    Po uruchomieniu alertu każda z jego grup akcji jest przetwarzana niezależnie. Jeśli więc akcja (na przykład adres e-mail) pojawia się w wielu wyzwolonych grupach akcji, zostanie wywołana raz dla każdej grupy akcji. 

    Aby sprawdzić, które grupy akcji zostały wyzwolone, sprawdź kartę historii alertu. Są tam widoczne zarówno grupy akcji zdefiniowane w regule alertu, jak i grupy akcji dodane do alertu przez reguły akcji: 

    ![Powtórzona akcja w wielu grupach akcji](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Akcja lub powiadomienie ma nieoczekiwaną zawartość

Jeśli alert został odebrany, ale uważasz, że brakuje niektórych pól lub są one nieprawidłowe, wykonaj następujące kroki: 

1. **Czy wybrano poprawny format dla akcji?** 

    Każdy typ akcji (poczta e-mail, element webhook itp.) ma dwa formaty — domyślny, starszy format i [nowszy format wspólnego schematu](alerts-common-schema.md). Tworząc grupę akcji, określa się format poszczególnych akcji — dostępnych jest szereg formatów. 

    Na przykład dla akcji elementu webhook: 

    ![Akcja elementu webhook — opcja schematu](media/alerts-troubleshoot/webhook.png)

    Sprawdź, czy na poziomie akcji określono odpowiedni format. Na przykład może się zdarzyć, że w kodzie, który reaguje na alerty (element webhook, funkcja, aplikacja logiki itp.), oczekiwany jest dany format, ale w dalszej części akcji użytkownik określi inny format.  

    Sprawdź również format ładunku (JSON) dla [alertów dziennika aktywności](activity-log-alerts-webhook.md), [alertów przeszukiwania dzienników](alerts-log-webhook.md) (zarówno usługi Application Insights, jak i analizy dzienników), [alertów metryk](alerts-metric-near-real-time.md#payload-schema), [wspólnego schematu alertów](alerts-common-schema-definitions.md) oraz przestarzałych [alertów metryk klasycznych](alerts-webhooks.md).

 
1. **Alerty dziennika aktywności: czy informacje są dostępne w dzienniku aktywności?** 

    [Alerty dziennika aktywności](activity-log-alerts.md) są alertami opartymi na zdarzeniach, które są zapisywane w dzienniku aktywności platformy Azure, takich jak zdarzenia dotyczące tworzenia, aktualizowania lub usuwania zasobów platformy Azure, kondycji usług i zdarzeń związanych z kondycją zasobów albo wyniki z Azure Advisor i Azure Policy. Jeśli odebrano alert na podstawie dziennika aktywności, ale brakuje niektórych pól lub są one nieprawidłowe, najpierw sprawdź zdarzenia w dzienniku aktywności. Jeśli zasób platformy Azure nie zapisał pól, których szukasz w zdarzeniu dziennika aktywności, pola te nie będą dostępne w alercie. 

## <a name="action-rule-is-not-working-as-expected"></a>Reguła akcji nie działa zgodnie z oczekiwaniami 

Jeśli w portalu widzisz wyzwolony alert, ale powiązana reguła akcji nie zadziałała zgodnie z oczekiwaniami, wykonaj następujące czynności: 

1. **Czy reguła akcji jest włączona?** 

    Sprawdź kolumnę stanu reguły akcji, aby zweryfikować, czy powiązana rola akcji została włączona. 

    ![zdjęć](media/alerts-troubleshoot/action-rule-status.png) 

    Jeśli reguła akcji nie jest włączona, możesz ją włączyć, zaznaczając ją i klikając pozycję Włącz. 

1. **Czy jest to alert kondycji usługi?** 

    Reguły akcji nie mają wpływu na alerty dotyczące kondycji usługi (usługa monitorowania = „Service Health”). 

1. **Czy reguła akcji działa względem Twojego alertu?** 

    Sprawdź, czy reguła akcji przetworzyła alert, klikając wyzwolony alert w portalu i przeglądając kartę historii.

    Oto przykład reguły akcji pomijającej wszystkie grupy akcji: 
 
     ![Historia pomijania reguły akcji alertu](media/alerts-troubleshoot/history-action-rule.png)

    Oto przykład reguły akcji dodającej kolejną grupę akcji:

    ![Powtórzona akcja w wielu grupach akcji](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Czy zakres i filtr reguły akcji są zgodne z wygenerowanym alertem?** 

    Jeśli uważasz, że reguła akcji powinna zostać wyzwolona, ale nie została, lub że nie powinna zostać wyzwolona, a została wyzwolona, uważnie sprawdź zakres reguły akcji i warunki filtrowania w porównaniu z właściwościami wywołanego alertu. 


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

    Należy mieć [wbudowaną rolę współautora monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor)lub określone uprawnienia związane z regułami akcji i alertami.

1. **Czy zweryfikowano parametry reguły akcji?**  

    Zapoznaj się z [dokumentacją reguły akcji](alerts-action-rules.md)lub z [akcją programu PowerShell Set-AzActionRule](/powershell/module/az.alertsmanagement/set-azactionrule) . 


## <a name="next-steps"></a>Następne kroki
- Jeśli używasz alertu dziennika, zobacz również [Rozwiązywanie problemów z alertami dzienników](./alerts-troubleshoot-log.md).
- Wróć do [Azure Portal](https://portal.azure.com) , aby sprawdzić, czy problem został rozwiązany z powyższymi wskazówkami 
