---
title: Zdalne monitorowanie i powiadomienia w usłudze IoT przy użyciu Azure Logic Apps | Microsoft Docs
description: Użyj Azure Logic Apps do monitorowania temperatury IoT w centrum IoT i automatycznie wysyłaj powiadomienia e-mail do skrzynki pocztowej w celu wykrycia wszelkich anomalii.
author: robinsh
keywords: monitorowanie IoT, powiadomienia IoT, monitorowanie temperatury IoT
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: cd14ff0688f4230aeedac748ca4b32609bdd2938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92490326"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Zdalne monitorowanie i powiadomienia w usłudze IoT przy użyciu Azure Logic Apps łączenia Centrum IoT i skrzynki pocztowej

![Diagram kompleksowy](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) może pomóc organizować przepływy pracy w ramach usług lokalnych i w chmurze, co najmniej jednego przedsiębiorstwa i wielu różnych protokołów. Aplikacja logiki rozpoczyna się od wyzwalacza, po którym następuje jedna lub więcej akcji, które mogą być sekwencjonowane przy użyciu wbudowanych kontrolek, takich jak warunki i Iteratory. Ta elastyczność sprawia, że Logic Apps idealne rozwiązanie IoT na potrzeby scenariuszy monitorowania IoT. Na przykład po nadejściu danych telemetrycznych z urządzenia w IoT Hub punkcie końcowym można inicjować przepływy pracy aplikacji logiki w celu przechowywania danych w obiekcie blob usługi Azure Storage, wysyłać alerty e-mail w celu ostrzegania o anomaliach dotyczących danych, planować wizytę w technice, jeśli urządzenie zgłasza awarię i tak dalej.

## <a name="what-you-learn"></a>Omawiane zagadnienia

Dowiesz się, jak utworzyć aplikację logiki, która łączy centrum IoT Hub z Twoją skrzynką pocztową w celu monitorowania i powiadamiania o temperaturze.

Kod klienta uruchomiony na urządzeniu ustawia właściwość aplikacji, `temperatureAlert` na każdy komunikat telemetrii wysyłany do centrum IoT Hub. Gdy kod klienta wykrywa temperaturę powyżej 30 °C, ustawia tę właściwość na `true` ; w przeciwnym razie ustawia właściwość na `false` .

Komunikaty docierające do centrum IoT wyglądają podobnie do następujących, z danymi telemetrycznymi zawartymi w treści i właściwościami `temperatureAlert` zawartymi we właściwościach aplikacji (właściwości systemu nie są wyświetlane):

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

Aby dowiedzieć się więcej o IoT Hub formacie wiadomości, zobacz [Tworzenie i odczytywanie IoT Hub komunikatów](iot-hub-devguide-messages-construct.md).

W tym temacie opisano Konfigurowanie routingu w centrum IoT Hub w celu wysyłania komunikatów, w których `temperatureAlert` Właściwość dotyczy `true` Service Bus punktu końcowego. Następnie skonfigurujesz aplikację logiki, która wyzwala komunikaty docierające do punktu końcowego Service Bus i wyśle Ci powiadomienie e-mail.

## <a name="what-you-do"></a>Co robisz

* Utwórz przestrzeń nazw Service Bus i Dodaj do niej kolejkę Service Bus.
* Dodanie niestandardowego punktu końcowego i reguły routingu do centrum IoT Hub w celu rozesłania komunikatów zawierających alert dotyczący temperatury do kolejki Service Bus.
* Utwórz, skonfiguruj i przetestuj aplikację logiki, aby korzystać z komunikatów z kolejki Service Bus i wysyłać wiadomości e-mail z powiadomieniem do żądanego odbiorcy.

## <a name="what-you-need"></a>Potrzebne elementy

* Ukończ samouczek [gry online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) lub jedno z samouczków dotyczących urządzeń; na przykład [Raspberry Pi z node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Obejmują one następujące wymagania:

  * Aktywna subskrypcja platformy Azure.
  * Usługa Azure IoT Hub w ramach Twojej subskrypcji.
  * Aplikacja kliencka uruchomiona na urządzeniu, która wysyła komunikaty telemetryczne do usługi Azure IoT Hub.

## <a name="create-service-bus-namespace-and-queue"></a>Tworzenie Service Bus przestrzeni nazw i kolejki

Utwórz przestrzeń nazw i kolejkę usługi Service Bus. W dalszej części tego tematu utworzysz regułę routingu w centrum IoT Hub, aby skierować komunikaty zawierające alert dotyczący temperatury do kolejki Service Bus, w której zostaną one pobrane przez aplikację logiki, i Wyzwól ją w celu wysłania wiadomości e-mail z powiadomieniem.

### <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus

1. Na [Azure Portal](https://portal.azure.com/)wybierz pozycję **+ Utwórz zasób**  >  **integracja**  >  **Service Bus**.

1. W okienku **Tworzenie przestrzeni nazw** podaj następujące informacje:

   **Name**: nazwa przestrzeni nazw usługi Service Bus. Przestrzeń nazw musi być unikatowa na platformie Azure.

   **Warstwa cenowa**: wybierz pozycję **podstawowa** z listy rozwijanej. Warstwa Podstawowa jest wystarczająca dla tego samouczka.

   **Grupa zasobów**: Użyj tej samej grupy zasobów, która jest używana przez Centrum IoT Hub.

   **Lokalizacja**: Użyj tej samej lokalizacji, z której korzysta Centrum IoT Hub.

   ![Utwórz przestrzeń nazw usługi Service Bus w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Wybierz przycisk **Utwórz**. Przed przejściem do następnego kroku poczekaj na zakończenie wdrożenia.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Dodawanie kolejki Service Bus do przestrzeni nazw

1. Otwórz przestrzeń nazw Service Bus. Najprostszym sposobem, aby uzyskać dostęp do przestrzeni nazw Service Bus, jest wybranie **grupy zasobów** z okienka zasobów, wybranie grupy zasobów, a następnie wybranie przestrzeni nazw Service Bus z listy zasobów.

1. W okienku **przestrzeni nazw Service Bus** wybierz pozycję **+ Kolejka**.

1. Wprowadź nazwę kolejki, a następnie wybierz pozycję **Utwórz**. Po pomyślnym utworzeniu kolejki okienko **tworzenia kolejki** zostanie zamknięte.

   ![Dodaj kolejkę usługi Service Bus do Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Wróć do okienka **Service Bus przestrzeń nazw** , w obszarze **jednostki** wybierz **kolejno pozycje kolejki**. Otwórz kolejkę Service Bus z listy, a następnie wybierz pozycję **zasady dostępu współdzielonego**  >  **+ Dodaj**.

1. Wprowadź nazwę zasad, sprawdź pozycję **Zarządzaj**, a następnie wybierz pozycję **Utwórz**.

   ![Dodaj zasady kolejki usługi Service Bus do Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Dodawanie niestandardowego punktu końcowego i reguły routingu do centrum IoT Hub

Dodaj niestandardowy punkt końcowy dla kolejki Service Bus do centrum IoT Hub i Utwórz regułę routingu wiadomości, aby skierować komunikaty zawierające alert dotyczący temperatury do tego punktu końcowego, gdzie zostaną one pobrane przez aplikację logiki. Reguła routingu używa zapytania routingu, `temperatureAlert = "true"` do przesyłania dalej komunikatów na podstawie wartości `temperatureAlert` właściwości aplikacji ustawionej przez kod klienta uruchomiony na urządzeniu. Aby dowiedzieć się więcej, zobacz [zapytanie routingu komunikatów na podstawie właściwości wiadomości](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Dodawanie niestandardowego punktu końcowego

1. Otwórz Centrum IoT Hub. Najprostszym sposobem, aby uzyskać dostęp do usługi IoT Hub, jest wybranie **grupy zasobów** z okienka zasobów, wybranie grupy zasobów, a następnie wybranie Centrum IoT Hub z listy zasobów.

1. W obszarze **Obsługa komunikatów** wybierz pozycję **routing wiadomości**. W okienku **routing wiadomości** wybierz kartę **niestandardowe punkty końcowe** , a następnie wybierz pozycję **+ Dodaj**. Z listy rozwijanej wybierz **kolejno pozycje Usługa Service Bus**.

   ![Zrzut ekranu, który podświetla opcję kolejki usługi Service Bus.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. W okienku **Dodaj punkt końcowy usługi Service Bus** wprowadź następujące informacje:

   **Nazwa punktu końcowego**: nazwa punktu końcowego.

   **Przestrzeń nazw usługi Service Bus**: Wybierz utworzoną przestrzeń nazw.

   **Kolejka usługi Service Bus**: Wybierz utworzoną kolejkę.

   ![Dodaj punkt końcowy do centrum IoT Hub w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Wybierz przycisk **Utwórz**. Po pomyślnym utworzeniu punktu końcowego przejdź do następnego kroku.

### <a name="add-a-routing-rule"></a>Dodawanie reguły rozsyłania

1. W okienku **routing wiadomości** wybierz kartę **trasy** , a następnie wybierz pozycję **+ Dodaj**.

1. W okienku **Dodawanie trasy** wprowadź następujące informacje:

   **Name**: Nazwa reguły routingu.

   **Punkt końcowy**: wybierz utworzony punkt końcowy.

   **Źródło danych**: wybierz pozycję **komunikaty telemetryczne urządzenia**.

   **Zapytanie routingu**: ENTER `temperatureAlert = "true"` .

   ![Dodawanie reguły routingu w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Wybierz pozycję **Zapisz**. Okienko **routing wiadomości** można zamknąć.

## <a name="create-and-configure-a-logic-app"></a>Tworzenie i Konfigurowanie aplikacji logiki

W poprzedniej sekcji skonfigurujesz Centrum IoT Hub do przesyłania komunikatów zawierających alert dotyczący temperatury do kolejki Service Bus. Teraz można skonfigurować aplikację logiki do monitorowania kolejki Service Bus i wysyłania powiadomień e-mail za każdym razem, gdy komunikat zostanie dodany do kolejki.

### <a name="create-a-logic-app"></a>Tworzenie aplikacji logiki

1. Wybierz pozycję **Utwórz zasób**  >    >  **aplikacja logiki**.

1. Wprowadź następujące informacje:

   **Name**: Nazwa aplikacji logiki.

   **Grupa zasobów**: Użyj tej samej grupy zasobów, która jest używana przez Centrum IoT Hub.

   **Lokalizacja**: Użyj tej samej lokalizacji, z której korzysta Centrum IoT Hub.

   ![Tworzenie aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Wybierz przycisk **Utwórz**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurowanie wyzwalacza aplikacji logiki

1. Otwórz aplikację logiki. Najprostszym sposobem, aby uzyskać dostęp do aplikacji logiki, jest wybranie **grupy zasobów** z okienka zasobów, wybranie grupy zasobów, a następnie wybranie aplikacji logiki z listy zasobów. Po wybraniu aplikacji logiki zostanie otwarty projektant Logic Apps.

1. W projektancie Logic Apps przewiń w dół do opcji **Szablony** i wybierz pozycję **pusta aplikacja logiki**.

   ![Zacznij od pustej aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Wybierz kartę **wszystkie** , a następnie wybierz pozycję **Service Bus**.

   ![Wybierz Service Bus, aby rozpocząć tworzenie aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. W obszarze **wyzwalacze** wybierz, **kiedy co najmniej jeden komunikat dociera do kolejki (Autouzupełnianie)**.

   ![Wybierz wyzwalacz dla aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Utwórz połączenie z usługą Service Bus.
   1. Wprowadź nazwę połączenia i wybierz z listy Service Bus przestrzeń nazw. Zostanie otwarty następny ekran.

      ![Zrzut ekranu, który wyróżnia opcję gdy co najmniej jeden komunikat dociera do kolejki (Autouzupełnianie).](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Wybierz zasady usługi Service Bus (RootManageSharedAccessKey). Następnie wybierz pozycję  **Utwórz**.

      ![Utwórz połączenie usługi Service Bus dla aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Na ostatnim ekranie, w polu **Nazwa kolejki**, wybierz kolejkę utworzoną z listy rozwijanej. Wprowadź, `175` Aby określić **maksymalną liczbę komunikatów**.

      ![Określ maksymalną liczbę komunikatów dla połączenia usługi Service Bus w aplikacji logiki](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Wybierz pozycję **Zapisz** w menu w górnej części projektanta Logic Apps, aby zapisać zmiany.

### <a name="configure-the-logic-app-action"></a>Konfigurowanie akcji aplikacji logiki

1. Utwórz połączenie usługi SMTP.

   1. Wybierz pozycję **Nowy krok**. W obszarze **Wybierz akcję** wybierz kartę **wszystkie** .

   1. Wpisz `smtp` w polu wyszukiwania, w wynikach wyszukiwania wybierz usługę **SMTP** , a następnie wybierz pozycję **Wyślij wiadomość e-mail**.

      ![Utwórz połączenie SMTP w aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Wprowadź informacje SMTP dla swojej skrzynki pocztowej, a następnie wybierz pozycję **Utwórz**.

      ![Wprowadź informacje o połączeniu SMTP w aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Pobierz informacje SMTP dla usługi [Hotmail/Outlook. com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en)i [Yahoo poczty e-mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Aby nawiązać połączenie, może być konieczne wyłączenie protokołu TLS/SSL. W takim przypadku należy ponownie włączyć protokół TLS po nawiązaniu połączenia, zapoznaj się z opcjonalnym krokiem na końcu tej sekcji.

   1. Z listy rozwijanej **Dodaj nowy parametr** w kroku **Wyślij wiadomość E-mail** wybierz pozycję **od**, **do**, **temat** i **treść**. Kliknij lub naciśnij dowolne miejsce na ekranie, aby zamknąć pole wyboru.

      ![Wybieranie pól e-mail połączenia SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Wprowadź adres e-mail **od** i **do**, a na przykład `High temperature detected` **temat** i **treść**. Jeśli zostanie otwarte okno dialogowe **Dodaj zawartość dynamiczną z aplikacji i łączników używanych w tym przepływie** , wybierz pozycję **Ukryj** , aby je zamknąć. W tym samouczku nie jest używana zawartość dynamiczna.

      ![Wypełnij pola e-mail połączenia SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Wybierz pozycję **Zapisz** , aby zapisać połączenie SMTP.

1. Obowiązkowe Jeśli trzeba będzie wyłączyć protokół TLS w celu nawiązania połączenia z dostawcą poczty e-mail i ponownie włączyć go, wykonaj następujące kroki:

   1. W okienku **aplikacja logiki** w obszarze **Narzędzia programistyczne** wybierz pozycję **połączenia interfejsu API**.

   1. Z listy połączeń interfejsu API wybierz połączenie SMTP.

   1. W okienku **połączenie interfejsu API SMTP** w obszarze **Ogólne** wybierz pozycję **Edytuj połączenie z interfejsem API**.

   1. W okienku **Edytowanie połączenia interfejsu API** wybierz pozycję **Włącz protokół SSL?**, wprowadź ponownie hasło do konta E-mail i wybierz pozycję **Zapisz**.

      ![Edytowanie połączenia interfejsu API SMTP w aplikacji logiki w Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Aplikacja logiki jest teraz gotowa do przetwarzania alertów dotyczących temperatury z kolejki Service Bus i wysyłania powiadomień do konta e-mail.

## <a name="test-the-logic-app"></a>Testowanie aplikacji logiki

1. Uruchom aplikację kliencką na urządzeniu.

1. W przypadku korzystania z urządzenia fizycznego należy uważnie wprowadzić źródło ciepła blisko czujnika ciepła, dopóki temperatura przekroczy 30 stopni C. Jeśli używasz symulatora online, kod klienta losowo będzie wyprowadzać komunikaty telemetryczne, które przekraczają 30 C.

1. Należy zacząć otrzymywać powiadomienia e-mail wysyłane przez aplikację logiki.

   > [!NOTE]
   > Twój dostawca usług poczty e-mail może potrzebować zweryfikować tożsamość nadawcy, aby upewnić się, że wysyła wiadomość e-mail.

## <a name="next-steps"></a>Następne kroki

Pomyślnie utworzono aplikację logiki, która łączy centrum IoT Hub z Twoją skrzynką pocztową na potrzeby monitorowania i powiadomień o temperaturze.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]