---
title: Zdalne monitorowanie i powiadomienia i powiadomienia IoT za pomocą usługi Azure Logic Apps | Dokumenty firmy Microsoft
description: Użyj usługi Azure Logic Apps do monitorowania temperatury IoT w centrum IoT hub i automatycznie wysyłaj powiadomienia e-mail do skrzynki pocztowej w celu wykrycia anomalii.
author: robinsh
keywords: monitorowanie iot, powiadomienia iot, monitorowanie temperatury iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 2720f9acfa308294b30f9203ba80e3f9b426e1e9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680719"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Zdalne monitorowanie i powiadomienia IoT za pomocą usługi Azure Logic Apps łączącej centrum IoT i skrzynkę pocztową

![Diagram end-to-end](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Usługa Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) może pomóc w organizowaniu przepływów pracy w usługach lokalnych i w chmurze, w co najmniej jednym przedsiębiorstwie oraz w różnych protokołach. Aplikacja logiki zaczyna się od wyzwalacza, po którym następuje jedna lub więcej akcji, które można sekwencjonować przy użyciu wbudowanych formantów, takich jak warunki i iteratory. Ta elastyczność sprawia, że aplikacje logiki idealnym rozwiązaniem IoT dla scenariuszy monitorowania IoT. Na przykład pojawienie się danych telemetrycznych z urządzenia w punkcie końcowym usługi IoT Hub może inicjować przepływy pracy aplikacji logiki do magazynowania danych w obiekcie blob usługi Azure Storage, wysyłać alerty e-mail, aby ostrzec o anomaliach danych, zaplanować wizytę technika, jeśli urządzenie zgłasza błąd i tak dalej.

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak utworzyć aplikację logiki, która łączy centrum IoT hub i skrzynki pocztowej do monitorowania temperatury i powiadomień.

Kod klienta uruchomiony na urządzeniu ustawia `temperatureAlert`właściwość aplikacji, na każdy komunikat telemetrii wysyła do centrum IoT hub. Gdy kod klienta wykryje temperaturę powyżej 30 `true`C, ustawia tę właściwość na ; w przeciwnym razie ustawia `false`właściwość na .

Komunikaty przychodzące do centrum IoT wyglądają podobnie do następujących, z danymi `temperatureAlert` telemetrycznymi zawartymi w treści i właściwości zawartej we właściwościach aplikacji (właściwości systemu nie są wyświetlane):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Aby dowiedzieć się więcej o formacie komunikatów Usługi IoT Hub, zobacz [Tworzenie i odczytywanie komunikatów Usługi IoT Hub](iot-hub-devguide-messages-construct.md).

W tym temacie skonfigurować routingu w centrum IoT `temperatureAlert` hub `true` do wysyłania wiadomości, w których właściwość jest do punktu końcowego usługi Service Bus. Następnie skonfigurować aplikację logiki, która wyzwala wiadomości docierających do punktu końcowego usługi Service Bus i wysyła powiadomienie e-mail.

## <a name="what-you-do"></a>Co robisz

* Utwórz obszar nazw usługi Service Bus i dodaj do niej kolejkę usługi Service Bus.
* Dodaj niestandardowy punkt końcowy i regułę routingu do centrum IoT hub, aby rozsyłać komunikaty zawierające alert temperatury do kolejki usługi Service Bus.
* Tworzenie, konfigurowanie i testowanie aplikacji logiki w celu korzystania z wiadomości z kolejki usługi Service Bus i wysyłania wiadomości e-mail z powiadomieniami do żądanego adresata.

## <a name="what-you-need"></a>Co jest potrzebne

* Ukończ samouczek [symulatora online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jeden z samouczków urządzenia; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:

  * Aktywna subskrypcja platformy Azure.
  * Centrum Usługi Azure IoT w ramach subskrypcji.
  * Aplikacja kliencka uruchomiona na urządzeniu, która wysyła komunikaty telemetryczne do centrum Usługi Azure IoT Hub.

## <a name="create-service-bus-namespace-and-queue"></a>Tworzenie obszaru nazw i kolejki usługi Service Bus

Utwórz przestrzeń nazw i kolejkę usługi Service Bus. W dalszej części tego tematu utworzysz regułę routingu w centrum IoT hub, aby kierować wiadomości zawierające alert temperatury do kolejki usługi Service Bus, gdzie zostaną pobrane przez aplikację logiki i wyzwoli ją, aby wysłać wiadomość e-mail z powiadomieniem.

### <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

1. W [portalu Azure](https://portal.azure.com/)wybierz **pozycję + Tworzenie usługi** > **integracji** > zasobów**Bus**.

1. W okienku **Tworzenie obszaru nazw** podaj następujące informacje:

   **Nazwa**: Nazwa obszaru nazw magistrali usług. Obszar nazw musi być unikatowy na platformie Azure.

   **Warstwa cenowa:** Wybierz **opcję Podstawowe** z listy rozwijanej. Warstwa Podstawowa jest wystarczająca dla tego samouczka.

   **Grupa zasobów:** Użyj tej samej grupy zasobów, której używa centrum IoT Hub.

   **Lokalizacja:** Użyj tej samej lokalizacji, której używa centrum IoT Hub.

   ![Tworzenie obszaru nazw magistrali usług w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Wybierz pozycję **Utwórz**. Poczekaj na zakończenie wdrożenia przed przejściem do następnego kroku.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Dodawanie kolejki usługi Service Bus do obszaru nazw

1. Otwórz obszar nazw usługi Service Bus. Najprostszym sposobem dostania się do obszaru nazw usługi Service Bus jest wybranie **grup zasobów** z okienka zasobów, wybranie grupy zasobów, a następnie wybranie obszaru nazw usługi Service Bus z listy zasobów.

1. W okienku **Obszar nazw magistrali usług** wybierz pozycję + **Kolejka**.

1. Wprowadź nazwę kolejki, a następnie wybierz pozycję **Utwórz**. Po pomyślnym utworzeniu kolejki okienko **Utwórz kolejkę** zostanie zamknięte.

   ![Dodawanie kolejki magistrali usług w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Powrót do okienka **Obszar nazw magistrali usługowej** w obszarze **Jednostki**wybierz pozycję **Kolejki**. Otwórz kolejkę usługi Service Bus z listy, a następnie wybierz pozycję **Zasady** > dostępu udostępnionego +**Dodaj**.

1. Wprowadź nazwę zasad, zaznacz pole wyboru **Zarządzaj**, a następnie wybierz pozycję **Utwórz**.

   ![Dodawanie zasad kolejki magistrali usług w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Dodawanie niestandardowego punktu końcowego i reguły routingu do centrum IoT

Dodaj niestandardowy punkt końcowy dla kolejki usługi Service Bus do centrum IoT hub i utwórz regułę routingu wiadomości do wiadomości bezpośrednich zawierających alert temperatury do tego punktu końcowego, gdzie zostaną pobrane przez aplikację logiki. Reguła routingu używa kwerendy `temperatureAlert = "true"`routingu, aby przesłać `temperatureAlert` dalej wiadomości na podstawie wartości właściwości aplikacji ustawionej przez kod klienta uruchomiony na urządzeniu. Aby dowiedzieć się więcej, zobacz [Kwerenda routingu wiadomości na podstawie właściwości wiadomości](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Dodawanie niestandardowego punktu końcowego

1. Otwórz centrum IoT. Najprostszym sposobem dostania się do centrum IoT hub jest wybranie **grup zasobów** z okienka zasobów, wybierz grupę zasobów, a następnie wybierz centrum IoT z listy zasobów.

1. W obszarze **Wiadomości**wybierz pozycję **Routing wiadomości**. W okienku **Routing wiadomości** wybierz kartę **Niestandardowe punkty końcowe,** a następnie wybierz pozycję **+ Dodaj**. Z listy rozwijanej wybierz **pozycję Kolejka magistrali usług**.

   ![Dodawanie punktu końcowego do centrum IoT hub w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. W okienku **Dodawanie punktu końcowego magistrali usług** wprowadź następujące informacje:

   **Nazwa punktu końcowego**: Nazwa punktu końcowego.

   **Obszar nazw magistrali usług:** Wybierz utworzony obszar nazw.

   **Kolejka magistrali usług:** wybierz utworzoną kolejkę.

   ![Dodawanie punktu końcowego do centrum IoT hub w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Wybierz pozycję **Utwórz**. Po pomyślnym utworzeniu punktu końcowego przejdź do następnego kroku.

### <a name="add-a-routing-rule"></a>Dodawanie reguły rozsyłania

1. Wróć do **okienka Routing wiadomości,** wybierz kartę **Trasy,** a następnie wybierz pozycję **+ Dodaj**.

1. W **okienku Dodawanie trasy** wprowadź następujące informacje:

   **Nazwa**: Nazwa reguły routingu.

   **Punkt końcowy**: Wybierz utworzony punkt końcowy.

   **Źródło danych**: Wybierz **komunikaty telemetryczne urządzenia**.

   **Kwerenda routingu**: Enter `temperatureAlert = "true"`.

   ![Dodawanie reguły routingu w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Wybierz pozycję **Zapisz**. Okienko **Routing wiadomości** można zamknąć.

## <a name="create-and-configure-a-logic-app"></a>Tworzenie i konfigurowanie aplikacji logiki

W poprzedniej sekcji skonfigurować centrum IoT hub do kierowania komunikatów zawierających alert temperatury do kolejki usługi Service Bus. Teraz można skonfigurować aplikację logiki do monitorowania kolejki usługi Service Bus i wysyłania powiadomień e-mail za każdym razem, gdy wiadomość zostanie dodana do kolejki.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

1. Wybierz **pozycję Utwórz aplikację** > **logiki integracji** > **Logic App**zasobów .

1. Wprowadź następujące informacje:

   **Nazwa**: Nazwa aplikacji logiki.

   **Grupa zasobów:** Użyj tej samej grupy zasobów, której używa centrum IoT Hub.

   **Lokalizacja:** Użyj tej samej lokalizacji, której używa centrum IoT Hub.

   ![Tworzenie aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Wybierz pozycję **Utwórz**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurowanie wyzwalacza aplikacji logiki

1. Otwórz aplikację logiki. Najprostszym sposobem, aby uzyskać do aplikacji logiki jest wybranie **grup zasobów** z okienka zasobów, wybierz grupę zasobów, a następnie wybierz aplikację logiki z listy zasobów. Po wybraniu aplikacji logiki zostanie otwarty projektant aplikacji logiki.

1. W Projektancie aplikacji logiki przewiń w dół do **szablonów** i wybierz pozycję **Pusta aplikacja logiki**.

   ![Zacznij od pustej aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Wybierz kartę **Wszystkie,** a następnie wybierz pozycję **Magistrala usług**.

   ![Wybierz usługę Service Bus, aby rozpocząć tworzenie aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. W obszarze **Wyzwalacze**wybierz pozycję **Po dotarciu jednej lub kilku wiadomości do kolejki (autouzupełnianie).**

   ![Wybierz wyzwalacz aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Utwórz połączenie magistrali usług.
   1. Wprowadź nazwę połączenia i wybierz obszar nazw usługi Service Bus z listy. Zostanie otwarty następny ekran.

      ![Tworzenie połączenia magistrali usług dla aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Wybierz zasady magistrali usług (RootManageSharedAccessKey). Następnie wybierz pozycję **Utwórz**.

      ![Tworzenie połączenia magistrali usług dla aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na ekranie końcowym w przypadku **nazwy kolejki**wybierz kolejkę utworzoną z listy rozwijanej. Wprowadź `175` **maksymalną liczbę wiadomości**.

      ![Określ maksymalną liczbę komunikatów dla połączenia magistrali usług w aplikacji logiki](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Wybierz **pozycję Zapisz** w menu u góry projektanta aplikacji logiki, aby zapisać zmiany.

### <a name="configure-the-logic-app-action"></a>Konfigurowanie akcji aplikacji logiki

1. Utwórz połączenie usługi SMTP.

   1. Wybierz pozycję **Nowy krok**. W **obszarze Wybieranie akcji**wybierz kartę **Wszystkie.**

   1. Wpisz `smtp` pole wyszukiwania, zaznacz usługę **SMTP** w wynikach wyszukiwania, a następnie wybierz pozycję **Wyślij wiadomość e-mail**.

      ![Tworzenie połączenia SMTP w aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Wprowadź informacje SMTP dla skrzynki pocztowej, a następnie wybierz pozycję **Utwórz**.

      ![Wprowadzanie informacji o połączeniu SMTP w aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Pobierz informacje o SMTP dla [hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970) [Gmail](https://support.google.com/a/answer/176600?hl=en)i [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > W celu nawiązania połączenia może być konieczne wyłączenie protokołu TLS/SSL. Jeśli tak jest i chcesz ponownie włączyć TLS po nawiązaniu połączenia, zobacz opcjonalny krok na końcu tej sekcji.

   1. Z listy rozwijanej **Dodaj nowy parametr** w kroku Wyślij wiadomość **e-mail** wybierz pozycję **Od**, **Do** **tematu** i **treści**. Kliknij lub naciśnij dowolne miejsce na ekranie, aby zamknąć pole wyboru.

      ![Wybieranie pól poczty e-mail połączenia SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Wprowadź swój adres e-mail `High temperature detected` dla **od** i **do**, a także dla **tematu** i **treści**. Jeśli zostanie otwarta opcja **Dodaj zawartość dynamiczną z aplikacji i łączników używanych w tym** oknie dialogowym przepływu, wybierz pozycję **Ukryj,** aby ją zamknąć. Nie używasz zawartości dynamicznej w tym samouczku.

      ![Pola poczty e-mail połączenia SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Wybierz **pozycję Zapisz,** aby zapisać połączenie SMTP.

1. (Opcjonalnie) Jeśli trzeba było wyłączyć usługę TLS w celu nawiązania połączenia z dostawcą poczty e-mail i chcesz go ponownie włączyć, wykonaj następujące czynności:

   1. W okienku **aplikacji Logika** w obszarze **Narzędzia programistyczne**wybierz pozycję **Połączenia interfejsu API**.

   1. Z listy połączeń interfejsu API wybierz połączenie SMTP.

   1. W okienku **połączenie interfejsu API smtp** w obszarze **Ogólne**wybierz pozycję Edytuj połączenie **interfejsu API**.

   1. W okienku **Edytowanie połączenia interfejsu API** wybierz pozycję **Włącz protokół SSL?**, wprowadź ponownie hasło do konta e-mail i wybierz pozycję **Zapisz**.

      ![Edytowanie połączenia interfejsu API SMTP w aplikacji logiki w witrynie Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Aplikacja logiki jest teraz gotowa do przetwarzania alertów o temperaturze z kolejki usługi Service Bus i wysyłania powiadomień na konto e-mail.

## <a name="test-the-logic-app"></a>Testowanie aplikacji logiki

1. Uruchom aplikację kliencką na urządzeniu.

1. Jeśli używasz urządzenia fizycznego, ostrożnie przynieś źródło ciepła w pobliżu czujnika ciepła, aż temperatura przekroczy 30 stopni C. Jeśli używasz symulatora online, kod klienta losowo wyprowadza komunikaty telemetryczne, które przekraczają 30 C.

1. Należy rozpocząć otrzymywanie powiadomień e-mail wysyłanych przez aplikację logiki.

   > [!NOTE]
   > Dostawca usług poczty e-mail może wymagać zweryfikowania tożsamości nadawcy, aby upewnić się, że to ty wysyłasz wiadomość e-mail.

## <a name="next-steps"></a>Następne kroki

Pomyślnie utworzono aplikację logiki, która łączy centrum IoT hub i skrzynki pocztowej do monitorowania temperatury i powiadomień.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
