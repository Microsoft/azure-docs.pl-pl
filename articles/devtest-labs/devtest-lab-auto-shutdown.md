---
title: Zarządzanie zasadami Autozamykania w Azure DevTest Labs i obliczeniowych maszynach wirtualnych | Microsoft Docs
description: Dowiedz się, jak ustawić zasady Autozamykania dla laboratorium, aby maszyny wirtualne były automatycznie zamykane, gdy nie są używane.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cd7974580ea30c9d0591c88380a4e626711bad1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93318970"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Konfigurowanie automatycznego zamykania dla maszyn wirtualnych w środowisku laboratoryjnym i obliczeniowym w Azure DevTest Labs

W tym artykule wyjaśniono, jak skonfigurować ustawienia automatycznego zamykania dla maszyn wirtualnych laboratorium w DevTest Labs i maszynach wirtualnych obliczeniowych.

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Konfigurowanie automatycznego zamykania dla maszyn wirtualnych laboratorium (DevTest Labs)

Azure DevTest Labs pozwala kontrolować koszt i zminimalizować liczbę odpadów w laboratoriach przez Zarządzanie zasadami (ustawieniami) dla każdego laboratorium. W tym artykule opisano sposób konfigurowania zasad Autozamykania dla laboratorium.  Przedstawiono w nim również sposób konfigurowania ustawień Autozamykania dla maszyny wirtualnej laboratorium. Aby wyświetlić sposób ustawiania każdej zasady laboratorium, zobacz [Definiowanie zasad laboratorium w Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-autoshutdown-policy-for-a-lab"></a>Ustawianie zasad automatycznego zamykania dla laboratorium

Jako właściciel laboratorium możesz skonfigurować harmonogram zamykania dla wszystkich maszyn wirtualnych w laboratorium. Dzięki temu można zaoszczędzić koszty z uruchomionych maszyn, które nie są używane (bezczynny). Zasady zamykania można wymusić na wszystkich maszynach wirtualnych laboratorium centralnie, a także zaoszczędzić użytkownikom laboratorium nakład pracy z konfigurowania harmonogramu dla poszczególnych maszyn. Ta funkcja umożliwia ustawienie zasad w harmonogramie laboratorium, od umożliwienia użytkownikom laboratorium posiadania pełnej kontroli nad harmonogramem zamykania maszyny wirtualnej bez kontroli nad zamknięciem maszyny wirtualnej. Jako właściciel laboratorium możesz skonfigurować te zasady, wykonując następujące czynności:

1. Na stronie głównej laboratorium wybierz pozycję **Konfiguracja i zasady**.
2. Wybierz pozycję **zasady automatycznego zamykania** w sekcji **harmonogramy** menu po lewej stronie.
3. Wybierz jedną z opcji. Poniższe sekcje zawierają więcej informacji na temat tych opcji:

    ![Automatycznie Zamknij opcje zasad](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

> [!IMPORTANT]
> Zmiany zasad zamykania dotyczą tylko nowych maszyn wirtualnych utworzonych w laboratorium, a nie do istniejących maszyn wirtualnych.

### <a name="configure-autoshutdown-settings"></a>Konfigurowanie ustawień Autozamykania

Zasady Autozamykania ułatwiają minimalizowanie odpadów laboratoryjnych przez umożliwienie określenia czasu, w którym maszyny wirtualne tego laboratorium są zamykane.

Aby wyświetlić lub zmienić zasady dla laboratorium, wykonaj następujące kroki:

1. Na stronie głównej laboratorium wybierz pozycję **Konfiguracja i zasady**.
2. Wybierz pozycję **automatyczne zamykanie** w sekcji **harmonogramy** menu po lewej stronie.
3. Wybierz **opcję Włącz,** aby włączyć tę zasadę i **wyłączyć** ją.
     ![Szczegóły automatycznego zamykania](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
4. Jeśli włączysz tę zasadę, określ czas (i strefę czasową), aby zamknąć wszystkie maszyny wirtualne w bieżącym laboratorium.
5. Wybierz opcję **tak** lub **nie** , aby można było wysłać powiadomienie 30 minut przed upływem określonego czasu automatycznego zamknięcia. Jeśli wybierzesz opcję **tak**, wprowadź punkt końcowy URL elementu webhook lub adres e-mail określający, gdzie ma być ogłaszane lub wysyłane. Użytkownik otrzymuje powiadomienie i otrzymuje opcję opóźnienia zamknięcia. Aby uzyskać więcej informacji, zobacz sekcję [powiadomienia](#notifications) .
6. Wybierz pozycję **Zapisz**.

    Domyślnie po włączeniu te zasady mają zastosowanie do wszystkich maszyn wirtualnych w bieżącym laboratorium. Aby usunąć to ustawienie z określonej maszyny wirtualnej, Otwórz okienko zarządzania maszyną wirtualną i zmień jego ustawienie **automatycznego zamykania** .

> [!NOTE]
> W przypadku zaktualizowania harmonogramu automatycznego zamykania dla laboratorium lub określonej maszyny wirtualnej laboratorium w ciągu 30 minut od bieżącego zaplanowanego czasu, zaktualizowany czas zamknięcia będzie stosowany do harmonogramu następnego dnia.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Użytkownik ustawia harmonogram i może zrezygnować

Jeśli ustawisz laboratorium na te zasady, użytkownicy laboratorium mogą przesłonić lub zrezygnować z harmonogramu laboratorium. Ta opcja przyznaje użytkownikom laboratorium pełną kontrolę nad harmonogramem automatycznego zamykania maszyn wirtualnych. Użytkownicy laboratorium nie zmieniają żadnej zmiany na stronie harmonogramu automatycznego zamykania maszyny wirtualnej.

![Opcja automatycznie Zamknij zasady-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Użytkownik ustawia harmonogram i nie może zrezygnować

Jeśli ustawisz laboratorium do tych zasad, użytkownicy laboratorium mogą przesłonić harmonogram laboratorium. Nie mogą jednak zrezygnować z zasad Autozamykania. Ta opcja zapewnia, że każdy komputer w laboratorium jest objęty harmonogramem automatycznego zamykania. Użytkownicy laboratorium mogą zaktualizować harmonogram automatycznego zamykania maszyn wirtualnych i skonfigurować powiadomienia o zamknięciu.

![Opcja automatycznie Zamknij zasady-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Użytkownik nie ma kontroli nad harmonogramem ustawionym przez administratora laboratorium

Jeśli ustawisz laboratorium na te zasady, użytkownicy laboratorium nie mogą przesłonić ani zrezygnować z harmonogramu laboratorium. Ta opcja zapewnia administratorowi laboratorium pełną kontrolę nad harmonogramem dla każdej maszyny w laboratorium. Użytkownicy laboratorium mogą konfigurować tylko powiadomienia o automatycznym zamknięciu dla swoich maszyn wirtualnych.

![Opcja automatycznie Zamknij zasady-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Konfigurowanie automatycznego zamykania dla maszyn wirtualnych obliczeniowych

1. Na stronie **maszyna wirtualna** wybierz pozycję **automatyczne zamykanie** w menu po lewej stronie w sekcji **operacje** .
2. Na stronie **automatyczne zamykanie** **Wybierz opcję włączone,** aby włączyć tę zasadę i **wyłączyć** ją.
3. Jeśli włączysz tę zasadę, określ **czas** (i **strefę czasową**), o której należy wyłączyć maszynę wirtualną.
4. Wybierz opcję **tak** lub **nie** , aby wysłać powiadomienie 30 minut przed upływem określonego czasu automatycznego zamknięcia. Jeśli wybierzesz opcję **tak**, wprowadź punkt końcowy URL elementu webhook lub adres e-mail określający, gdzie ma być ogłaszane lub wysyłane. Użytkownik otrzymuje powiadomienie i otrzymuje opcję opóźnienia zamknięcia. Aby uzyskać więcej informacji, zobacz sekcję [powiadomienia](#notifications) .
5. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie automatycznego zamykania maszyny wirtualnej obliczeniowej](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Wyświetlanie dzienników aktywności dla aktualizacji automatycznego zamykania

Po zaktualizowaniu ustawienia Autozamykania zobaczysz działanie zarejestrowane w dzienniku aktywności dla maszyny wirtualnej.

1. W [Azure Portal](https://portal.azure.com)przejdź do strony głównej dla maszyny wirtualnej.
2. Z menu po lewej stronie wybierz pozycję **Dziennik aktywności** .
3. Usuń **zasób: mycomputevm** z filtrów.
4. Upewnij się, że w dzienniku aktywności są widoczne operacje **dodawania i modyfikowania harmonogramów** . Jeśli nie widzisz go, poczekaj na jakiś czas i Odśwież dziennik aktywności.

    ![Wpis dziennika aktywności](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
5. Wybierz operację **Dodaj lub Modyfikuj harmonogramy** , aby wyświetlić na stronie **Podsumowanie** następujące informacje:

    - Nazwa operacji (Dodawanie lub modyfikowanie harmonogramów)
    - Data i godzina aktualizacji ustawienia automatycznego zamykania.
    - Adres e-mail użytkownika, który zaktualizował ustawienie.

        ![Podsumowanie wpisu dziennika aktywności](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
6. Przejdź do karty **historia zmian** na stronie **Dodawanie lub modyfikowanie harmonogramów** zostanie wyświetlona historia zmian dla tego ustawienia. W poniższym przykładzie czas zamykania został zmieniony z 7 PM na 6 PM w dniu 10 kwietnia 2020 na 15:18:47 EST. Ustawienie zostało wyłączone o 15:25:09 EST.

    ![Dziennik aktywności — historia zmian](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
7. Aby wyświetlić więcej szczegółów na temat operacji, przejdź na kartę **JSON** na stronie **Dodawanie lub modyfikowanie harmonogramów** .

## <a name="notifications"></a>Powiadomienia

Po skonfigurowaniu automatycznego zamykania powiadomienia będą wysyłane do użytkowników laboratorium 30 minut przed wyzwoleniem automatycznego wyłączania, jeśli będzie to miało zastosowanie do dowolnej z ich maszyn wirtualnych. Ta opcja daje użytkownikom laboratorium szansę na zapisanie pracy przed zamknięciem. Powiadomienie zawiera również linki dla każdej maszyny wirtualnej dla następujących akcji w przypadku, gdy ktoś musi kontynuować pracę nad maszyną wirtualną.

- Pomiń automatyczne zamykanie w tym czasie
- Odłożyć Automatyczne zamknięcie przez godzinę
- Odłożyć Automatyczne zamknięcie przez 2 godziny

Powiadomienie jest wysyłane do adresu URL elementu webhook, jeśli określono element webhook.  Jeśli adres e-mail został określony w ustawieniach Autozamykania, wiadomość e-mail zostanie wysłana na ten adres e-mail. Elementy webhook umożliwiają kompilowanie lub Konfigurowanie integracji subskrybowanych przez pewne zdarzenia. Gdy jedno z tych zdarzeń zostanie wyzwolone, DevTest Labs wyśle ładunek HTTP POST do skonfigurowanego adresu URL elementu webhook. Aby uzyskać więcej informacji na temat reagowania na elementy webhook, zobacz [Azure Functions wyzwalacze protokołu HTTP i powiązania — Omówienie](../azure-functions/functions-bindings-http-webhook.md) lub [Dodawanie wyzwalacza http dla Azure Logic Apps](../connectors/connectors-native-http.md#add-an-http-trigger).

Zalecamy używanie elementów webhook, ponieważ są one szeroko obsługiwane przez różne aplikacje, takie jak Azure Logic Apps i zapasowy.  Elementy webhook umożliwiają wdrożenie własnego sposobu wysyłania powiadomień. Na przykład w tym artykule opisano sposób konfigurowania powiadomienia o autozamknięciu w celu wysłania wiadomości e-mail do właściciela maszyny wirtualnej przy użyciu Azure Logic Apps. Najpierw przejdź do sekcji podstawowe kroki, aby włączyć powiadomienia o automatycznym zamknięciu w laboratorium.

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Tworzenie aplikacji logiki, która odbiera powiadomienia e-mail

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) udostępnia wiele łączników, które ułatwiają integrację usługi z innymi klientami, takimi jak Office 365 i Twitter. Na wysokim poziomie kroki konfigurowania aplikacji logiki na potrzeby powiadomień e-mail można podzielić na cztery etapy:

- Utwórz aplikację logiki.
- Skonfiguruj wbudowany szablon.
- Integracja z klientem poczty e-mail
- Pobierz adres URL elementu webhook.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

Aby rozpocząć, Utwórz aplikację logiki w ramach subskrypcji platformy Azure, wykonując następujące czynności:

1. Wybierz pozycję **+ Utwórz zasób** w menu po lewej stronie, wybierz pozycję **integracja** i wybierz pozycję **aplikacja logiki**.

    ![Nowe menu aplikacji logiki](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Na stronie **aplikacja logiki — tworzenie** wykonaj następujące kroki:
    1. Wprowadź **nazwę** aplikacji logiki.
    2. Wybierz swoją **subskrypcję** platformy Azure.
    3. Utwórz nową **grupę zasobów** lub wybierz istniejącą grupę zasobów.
    4. Wybierz **lokalizację** aplikacji logiki.

        ![Nowa aplikacja logiki — ustawienia](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. W obszarze **powiadomienia** wybierz pozycję **Przejdź do zasobu** w powiadomieniu.

    ![Przechodzenie do zasobu](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Wybierz pozycję **Projektant aplikacji logiki** w kategorii **narzędzia wdrażania** .

    ![Wybieranie żądania HTTP/odpowiedzi](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Na stronie **żądanie HTTP-odpowiedź** wybierz pozycję **Użyj tego szablonu**.

    ![Wybierz opcję Użyj tego szablonu](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Skopiuj następujący kod JSON do sekcji **schematu JSON treści żądania** :

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
        ],
        "type": "object"
    }
    ```

    ![Zrzut ekranu przedstawiający "schemat JSON treści żądania".](./media/devtest-lab-auto-shutdown/request-json.png)
7. Wybierz pozycję **+ nowy krok** w projektancie, a następnie wykonaj następujące kroki:
    1. Wyszukaj **pakiet Office 365 Outlook — Wyślij wiadomość e-mail**.
    2. Wybierz opcję **Wyślij wiadomość e-mail** z **akcji**.

        ![Opcja wysyłania wiadomości e-mail](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Wybierz pozycję **Zaloguj** się, aby zalogować się na konto e-mail.
    4. Zaznacz **pole wyboru i wybierz pozycję** właściciel.
    5. Wybierz pozycję **temat** i wprowadź temat powiadomienia e-mail. Na przykład: "Shutdown of Machine vmName for Lab: labName".
    6. Wybierz pozycję **treść** i zdefiniuj treść wiadomości e-mail z powiadomieniem. Na przykład: "vmName jest zaplanowana do zamknięcia w ciągu 15 minut. Pomiń to zamknięcie, klikając pozycję: URL. Opóźnione zamykanie przez godzinę: delayUrl60. Opóźnione zamykanie przez 2 godziny: delayUrl120. "

        ![Schemat JSON treści żądania](./media/devtest-lab-auto-shutdown/email-options.png)
8. Wybierz pozycję **Zapisz** na pasku narzędzi. Teraz można skopiować **adres URL post protokołu HTTP**. Wybierz przycisk Kopiuj, aby skopiować adres URL do Schowka.

    ![Adres URL elementu webhook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak ustawić wszystkie zasady, zobacz [Definiowanie zasad laboratorium w Azure DevTest Labs](devtest-lab-set-lab-policy.md).
