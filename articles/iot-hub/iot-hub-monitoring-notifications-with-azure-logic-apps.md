---
title: Zdalne monitorowanie i powiadomienia IoT za pomocą Azure Logic Apps | Microsoft Docs
description: Użyj Azure Logic Apps monitorowania temperatury IoT w centrum IoT i automatycznie wysyłaj powiadomienia e-mail do skrzynki pocztowej w przypadku wykrycia anomalii.
author: robinsh
keywords: Monitorowanie IOT, powiadomienia iot, monitorowanie temperatury iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 74724357dea9cd6c8c89a11a9eeb3d1b2933b790
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564956"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Zdalne monitorowanie i powiadomienia IoT z Azure Logic Apps połączenia centrum IoT i skrzynki pocztowej

![Diagram end-to-end](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) może pomóc w organizowaniu przepływów pracy między usługami lokalnymi i w chmurze, co najmniej jednym przedsiębiorstwem i za pomocą różnych protokołów. Aplikacja logiki rozpoczyna się od wyzwalacza, po którym następuje co najmniej jedna akcja, którą można sekwencjonarować przy użyciu wbudowanych kontrolek, takich jak warunki i iteratory. Ta elastyczność sprawia Logic Apps idealne rozwiązanie IoT dla scenariuszy monitorowania IoT. Na przykład nadejście danych telemetrycznych z urządzenia w punkcie końcowym usługi IoT Hub może zainicjować przepływy pracy aplikacji logiki w celu przechowywania danych w obiekcie blob usługi Azure Storage, wysłać alerty e-mail z ostrzeżeniem o anomalii dotyczących danych, zaplanować wizyty technika, jeśli urządzenie zadekmentuje awarię i tak dalej.

Z tego artykułu dowiesz się, jak utworzyć aplikację logiki, która łączy centrum IoT i skrzynkę pocztową w celu monitorowania temperatury i powiadomień. Kod klienta uruchomiony na urządzeniu ustawia właściwość aplikacji dla każdego komunikatu telemetrii wysyłanego `temperatureAlert` do centrum IoT. Gdy kod klienta wykryje temperaturę powyżej 30 C, ustawia tę właściwość na wartość ; w przeciwnym razie ustawia `true` właściwość na `false` .

Komunikaty przychodzące do centrum IoT hub wyglądają podobnie do następujących: dane telemetryczne zawarte w treści i właściwość zawarte we właściwościach aplikacji (właściwości systemu nie `temperatureAlert` są wyświetlane):

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

Aby dowiedzieć się więcej na temat IoT Hub, zobacz Tworzenie i [odczytywanie komunikatów IoT Hub wiadomości.](iot-hub-devguide-messages-construct.md)

W tym temacie skonfigurujemy routing w centrum IoT w celu wysyłania komunikatów, w których właściwość jest do Service Bus `temperatureAlert` `true` końcowego. Następnie skonfiguruj aplikację logiki, która wyzwala komunikaty przychodzące do punktu końcowego Service Bus i wysyła powiadomienie e-mail.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [samouczek dotyczący symulatora online](iot-hub-raspberry-pi-web-simulator-get-started.md) urządzenia Raspberry Pi lub jeden z samouczków urządzenia. Na przykład możesz przejść do urządzenia [Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md) z node.jslub do jednego z przewodników Szybki start Wysyłanie telemetrii. [](quickstart-send-telemetry-dotnet.md) Te artykuły obejmują następujące wymagania:

  * Aktywna subskrypcja platformy Azure.
  * Centrum Azure IoT Hub w ramach Twojej subskrypcji.
  * Aplikacja kliency uruchomiona na urządzeniu, która wysyła komunikaty telemetryczne do centrum Azure IoT Hub.

## <a name="create-service-bus-namespace-and-queue"></a>Tworzenie Service Bus przestrzeni nazw i kolejki

Utwórz przestrzeń nazw i kolejkę usługi Service Bus. W dalszej części tego tematu utworzysz regułę rozsyłania w centrum IoT, aby kierować komunikaty zawierające alert temperatury do kolejki usługi Service Bus, gdzie zostaną one odebrane przez aplikację logiki i wyzwolone w celu wysłania wiadomości e-mail z powiadomieniem.

### <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

1. Na stronie [Azure Portal](https://portal.azure.com/)pozycję **+ Utwórz zasób**  >  **Service Bus.**  >  

1. W **okienku Tworzenie przestrzeni** nazw podaj następujące informacje:

   **Nazwa:** nazwa przestrzeni nazw usługi Service Bus. Przestrzeń nazw musi być unikatowa na platformie Azure.

   **Warstwa cenowa:** **wybierz pozycję Podstawowa** z listy rozwijanej. Warstwa Podstawowa jest wystarczająca w tym samouczku.

   **Grupa zasobów:** użyj tej samej grupy zasobów, która jest używana przez centrum IoT Hub.

   **Lokalizacja:** użyj tej samej lokalizacji, która jest używana przez centrum IoT Hub.

   ![Tworzenie przestrzeni nazw usługi Service Bus w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Wybierz przycisk **Utwórz**. Przed przejściem do następnego kroku poczekaj na ukończenie wdrożenia.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Dodawanie kolejki Service Bus do przestrzeni nazw

1. Otwórz Service Bus nazw. Najprostszym sposobem uzyskania do przestrzeni nazw Service Bus  jest wybranie w okienku zasobów grupy zasobów, wybranie grupy zasobów, a następnie wybranie przestrzeni nazw Service Bus z listy zasobów.

1. W **okienku Service Bus Przestrzeń nazw** wybierz pozycję **+ Kolejka**.

1. Wprowadź nazwę kolejki, a następnie wybierz pozycję **Utwórz**. Po pomyślnym utworzeniu kolejki okienko **Tworzenie kolejki** jest zamykane.

   ![Dodawanie kolejki usługi Service Bus w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Po powrocie do **Service Bus Przestrzeń nazw** w obszarze **Jednostki** wybierz **pozycję Kolejki.** Otwórz kolejkę Service Bus z listy, a następnie wybierz pozycję Zasady dostępu **współdzielonych**  >  **+ Dodaj**.

1. Wprowadź nazwę zasad, zaznacz pozycję **Zarządzaj**, a następnie wybierz pozycję **Utwórz.**

   ![Dodawanie zasad kolejki usługi Service Bus w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Dodawanie niestandardowego punktu końcowego i reguły routingu do centrum IoT

Dodaj niestandardowy punkt końcowy kolejki usługi Service Bus do centrum IoT i utwórz regułę routingu komunikatów, aby kierować komunikaty zawierające alert temperatury do tego punktu końcowego, gdzie zostaną one odebrane przez aplikację logiki. Reguła routingu używa zapytania routingu, , do przekazywania komunikatów na podstawie wartości właściwości aplikacji ustawionej przez kod klienta `temperatureAlert = "true"` `temperatureAlert` uruchomiony na urządzeniu. Aby dowiedzieć się więcej, zobacz [Message routing query based on message properties (Zapytanie dotyczące routingu komunikatów na podstawie właściwości komunikatu).](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties)

### <a name="add-a-custom-endpoint"></a>Dodawanie niestandardowego punktu końcowego

1. Otwórz centrum IoT Hub. Najprostszym sposobem uzyskania do centrum IoT  Hub jest wybranie opcji Grupy zasobów w okienku zasobów, wybranie grupy zasobów, a następnie wybranie centrum IoT z listy zasobów.

1. W **obszarze Obsługa** komunikatów wybierz pozycję Routing **komunikatów.** W **okienku Kierowanie komunikatów** wybierz **kartę Niestandardowe punkty końcowe,** a następnie wybierz **pozycję + Dodaj**. Z listy rozwijanej wybierz pozycję **Kolejka usługi Service Bus.**

   ![Zrzut ekranu przedstawiający opcję kolejki usługi Service Bus.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. W **okienku Dodawanie punktu końcowego usługi Service Bus** wprowadź następujące informacje:

   **Nazwa punktu końcowego:** nazwa punktu końcowego.

   **Przestrzeń nazw usługi Service Bus:** wybierz utworzoną przestrzeń nazw.

   **Kolejka usługi Service Bus:** wybierz utworzoną kolejkę.

   ![Dodawanie punktu końcowego do centrum IoT w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Wybierz przycisk **Utwórz**. Po pomyślnym utworzeniu punktu końcowego przejdź do następnego kroku.

### <a name="add-a-routing-rule"></a>Dodawanie reguły rozsyłania

1. Po powrocie do **okienka Kierowanie komunikatów** wybierz **kartę Trasy,** a następnie wybierz **pozycję + Dodaj**.

1. W **okienku Dodawanie trasy** wprowadź następujące informacje:

   **Nazwa:** nazwa reguły rozsyłania.

   **Punkt** końcowy: wybierz utworzony punkt końcowy.

   **Źródło danych:** wybierz **pozycję Komunikaty telemetrii urządzenia.**

   **Zapytanie dotyczące routingu:** wprowadź `temperatureAlert = "true"` .

   ![Dodawanie reguły rozsyłania w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Wybierz pozycję **Zapisz**. Możesz zamknąć okienko **Routing komunikatów.**

## <a name="create-and-configure-a-logic-app"></a>Tworzenie i konfigurowanie aplikacji logiki

W poprzedniej sekcji skonfigurujemy centrum IoT w celu przekierowania komunikatów zawierających alert temperatury do Service Bus kolejki. Teraz skonfiguruj aplikację logiki do monitorowania kolejki Service Bus i wysyłania powiadomienia e-mail za każdym razem, gdy komunikat zostanie dodany do kolejki.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

1. Wybierz **pozycję Utwórz zasób Aplikacja**  >    >  **logiki integracji.**

1. Wprowadź następujące informacje:

   **Nazwa:** nazwa aplikacji logiki.

   **Grupa zasobów:** użyj tej samej grupy zasobów, która jest używana przez centrum IoT Hub.

   **Lokalizacja:** użyj tej samej lokalizacji, z których korzysta centrum IoT Hub.

   ![Tworzenie aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Wybierz przycisk **Utwórz**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurowanie wyzwalacza aplikacji logiki

1. Otwórz aplikację logiki. Najprostszym sposobem na dostęp do aplikacji  logiki jest wybranie grupy zasobów w okienku zasobów, wybranie grupy zasobów, a następnie wybranie aplikacji logiki z listy zasobów. Po wybraniu aplikacji logiki zostanie otwarty Logic Apps Designer.

1. W projektancie Logic Apps przewiń w dół do **opcji Szablony** i wybierz pozycję Pusta **aplikacja logiki.**

   ![Rozpocznij od pustej aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Wybierz **kartę Wszystkie,** a następnie wybierz **pozycję Service Bus**.

   ![Wybierz Service Bus, aby rozpocząć tworzenie aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. W **obszarze Wyzwalacze** wybierz pozycję Po dotareniu co najmniej jednego komunikatu do kolejki **(autouzupełnieniu).**

   ![Wybierz wyzwalacz aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Utwórz połączenie usługi Service Bus.
   1. Wprowadź nazwę połączenia i wybierz swoją Service Bus nazw z listy. Zostanie otwarty następny ekran.

      ![Zrzut ekranu przedstawiający opcję Po dotarcie co najmniej jednego komunikatu do kolejki (autouzupełnienia).](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Wybierz zasady usługi Service Bus (RootManageSharedAccessKey). Następnie wybierz pozycję  **Utwórz**.

      ![Tworzenie połączenia usługi Service Bus dla aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na ostatnim ekranie w przypadku **nazwy kolejki** wybierz utworzoną kolejkę z listy rozwijanej. Wprowadź `175` wartość w wartości Maksymalna liczba **komunikatów.**

      ![Określanie maksymalnej liczby komunikatów dla połączenia usługi Service Bus w aplikacji logiki](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Wybierz **pozycję** Zapisz w menu w górnej części Logic Apps Designer, aby zapisać zmiany.

### <a name="configure-the-logic-app-action"></a>Konfigurowanie akcji aplikacji logiki

1. Utwórz połączenie usługi SMTP.

   1. Wybierz pozycję **Nowy krok**. W **obszarze Wybierz akcję** wybierz **kartę** Wszystkie.

   1. Wpisz `smtp` w polu wyszukiwania, wybierz **usługę SMTP** w wynikach wyszukiwania, a następnie wybierz pozycję **Wyślij wiadomość e-mail.**

      ![Tworzenie połączenia SMTP w aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Wprowadź informacje SMTP dla skrzynki pocztowej, a następnie wybierz pozycję **Utwórz**.

      ![Wprowadź informacje o połączeniu SMTP w aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Pobierz informacje SMTP dla [usług Hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970) [Gmail](https://support.google.com/a/answer/176600?hl=en)i [Yahoo Mail.](https://help.yahoo.com/kb/SLN4075.html)

      > [!NOTE]
      > Aby nawiązać połączenie, może być konieczne wyłączenie protokołu TLS/SSL. Jeśli tak jest i chcesz ponownie włączyć obsługę TLS po nawiązaniu połączenia, zobacz opcjonalny krok na końcu tej sekcji.

   1. Z listy **rozwijanej Dodaj nowy** parametr w kroku Wyślij wiadomość **e-mail** wybierz pozycję **Od**, **Do**, **Temat** i **Treść.** Kliknij lub naciśnij dowolne miejsce na ekranie, aby zamknąć pole wyboru.

      ![Wybieranie pól poczty e-mail połączenia SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Wprowadź adres e-mail dla **adresów Od** i **Do** oraz w `High temperature detected` przypadku **tematu** i **treści**. Jeśli zostanie **otwarte okno dialogowe Dodawanie zawartości dynamicznej z aplikacji i** łączników używanych w tym przepływie, wybierz pozycję **Ukryj,** aby ją zamknąć. W tym samouczku nie używasz zawartości dynamicznej.

      ![Wypełnij pola poczty e-mail połączenia SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Wybierz **pozycję Zapisz,** aby zapisać połączenie SMTP.

1. (Opcjonalnie) Jeśli konieczne było wyłączenie usługi TLS w celu nawiązania połączenia z dostawcą poczty e-mail i ponowne włączenie go, wykonaj następujące kroki:

   1. W **okienku Aplikacja logiki** w obszarze **Narzędzia programowe** wybierz pozycję **Połączenia interfejsu API.**

   1. Z listy połączeń interfejsu API wybierz połączenie SMTP.

   1. W **okienku Połączenie interfejsu API SMTP** w **obszarze Ogólne** wybierz pozycję Edytuj **połączenie interfejsu API**.

   1. W **okienku Edytowanie połączenia interfejsu API** wybierz pozycję **Włącz protokół SSL?,** wprowadź ponownie hasło do konta e-mail, a następnie wybierz pozycję **Zapisz.**

      ![Edytowanie połączenia interfejsu API SMTP w aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Aplikacja logiki jest teraz gotowa do przetwarzania alertów dotyczących temperatury z Service Bus i wysyłania powiadomień na konto e-mail.

## <a name="test-the-logic-app"></a>Testowanie aplikacji logiki

1. Uruchom aplikację kliencyjną na urządzeniu.

1. Jeśli używasz urządzenia fizycznego, dokładnie przywęź źródło ciepła w pobliżu czujnika cieplnego, dopóki temperatura nie przekroczy 30 stopni C. Jeśli używasz symulatora online, kod klienta będzie losowo wysyłać komunikaty telemetryczne, które przekraczają 30 C.

1. Należy rozpocząć otrzymywanie powiadomień e-mail wysyłanych przez aplikację logiki.

   > [!NOTE]
   > Dostawca usługi poczty e-mail może wymagać zweryfikowania tożsamości nadawcy, aby upewnić się, że to Ty wysyłasz wiadomość e-mail.

## <a name="next-steps"></a>Następne kroki

Pomyślnie utworzono aplikację logiki, która łączy centrum IoT i skrzynkę pocztową w celu monitorowania temperatury i powiadomień.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]