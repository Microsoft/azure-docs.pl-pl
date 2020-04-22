---
title: Opis wiadomości z chmury do urządzenia usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: W tym przewodniku dla deweloperów omówiono sposób korzystania z obsługi wiadomości między chmurami a urządzeniami w centrum IoT Hub. Zawiera informacje o cyklu życia wiadomości i opcje konfiguracji.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt
ms.openlocfilehash: 307ab47c1f7498f71e61108a616d35ef1d4f61c9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730010"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Wysyłanie komunikatów z chmury do urządzenia z centrum IoT

Aby wysyłać powiadomienia jednokierunkowe do aplikacji urządzenia z zaplecza rozwiązania, wyślij do urządzenia komunikaty z chmury do urządzenia. Aby zapoznać się z innymi opcjami chmury do urządzenia obsługiwanymi przez usługę Azure IoT Hub, zobacz [Wskazówki dotyczące komunikacji między chmurami a urządzeniami.](iot-hub-devguide-c2d-guidance.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Wiadomości z chmury do urządzenia są wysyłane za pośrednictwem punktu końcowego wychodzącego z usługą , */messages/devicebound*. Urządzenie odbiera wiadomości za pośrednictwem punktu końcowego specyficznego dla urządzenia, */devices/{deviceId}/messages/devicebound*.

Aby kierować każdą wiadomość z chmury do urządzenia na jednym urządzeniu, centrum IoT ustawia właściwość **na** */devices/{deviceId}/messages/devicebound*.

Każda kolejka urządzeń zawiera co najwyżej 50 komunikatów z chmury do urządzenia. Aby spróbować wysłać więcej wiadomości do tego samego urządzenia powoduje błąd.

## <a name="the-cloud-to-device-message-life-cycle"></a>Cykl życia wiadomości z chmury do urządzenia

Aby zagwarantować co najmniej raz dostarczanie wiadomości, centrum IoT nadal komunikaty z chmury do urządzenia w kolejkach na urządzenie. Aby centrum IoT hub usunęło wiadomości z kolejki, urządzenia muszą jawnie potwierdzić *zakończenie.* Takie podejście gwarantuje odporność na łączność i awarie urządzeń.

Wykres stanu cyklu życia jest wyświetlany na poniższym diagramie:

![Cykl życia wiadomości z chmury do urządzenia](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Gdy usługa centrum IoT wysyła wiadomość do urządzenia, usługa ustawia stan wiadomości w *kolejce*. Gdy urządzenie chce *odbierać* wiadomość, centrum IoT *blokuje* komunikat, ustawiając stan *na Niewidoczny*. Ten stan umożliwia innym wątkom na urządzeniu rozpoczęcie odbierania innych wiadomości. Gdy wątek urządzenia zakończy przetwarzanie wiadomości, powiadamia centrum IoT hub, *wypełniając* komunikat. Następnie centrum IoT ustawia stan na *Zakończone*.

Urządzenie może również:

* *Odrzuć* wiadomość, co powoduje, że centrum IoT, aby ustawić go na martwym stanie *literami.* Urządzenia łączące się za pomocą protokołu MQTT (Message Queuing Telemetry Transport) nie mogą odrzucać komunikatów z chmury do urządzenia.

* *Porzuć* wiadomość, co powoduje, że centrum IoT hub umieścić wiadomość z powrotem w kolejce, ze stanem ustawionym na *Enqueued*. Urządzenia łączące się za pomocą protokołu MQTT nie mogą porzucić komunikatów z chmury do urządzenia.

Wątek może nie przetwarzać wiadomości bez powiadamiania centrum IoT hub. W takim przypadku wiadomości automatycznie przejście ze stanu *Niewidoczne* z powrotem do *enqueued* stanu po przekroczenie czasu *widoczności* (lub *blokady* limit czasu). Wartość tego limitu czasu wynosi jedną minutę i nie można jej zmienić.

Właściwość **maksymalna liczba dostarczania** w centrum IoT hub określa maksymalną liczbę razy, kiedy wiadomość może przechodzić między stanami *w kolejce* i *Niewidoczne.* Po tej liczbie przejść centrum IoT ustawia stan wiadomości na *Martwe litery*. Podobnie IoT Hub ustawia stan wiadomości *dead literami* po jego upływie czasu wygaśnięcia. Aby uzyskać więcej informacji, zobacz [Czas życia](#message-expiration-time-to-live).

Jak [wysyłać wiadomości z chmury do urządzenia z Centrum IoT Hub](iot-hub-csharp-csharp-c2d.md) pokazuje, jak wysyłać wiadomości z chmury do urządzenia z chmury i odbierać je na urządzeniu.

Urządzenie zwykle kończy komunikat chmury do urządzenia, gdy utrata wiadomości nie wpływa na logikę aplikacji. Przykładem tego może być, gdy urządzenie utrwalił zawartość wiadomości lokalnie lub pomyślnie wykonał operację. Komunikat może również przenosić informacje przejściowe, których utrata nie wpłynie na funkcjonalność aplikacji. Czasami, w przypadku długotrwałych zadań, można:

* Wykonaj komunikat chmury do urządzenia po urządzeniu utrwalił opis zadania w magazynie lokalnym.

* Powiadom zaplecza rozwiązania z jednego lub więcej komunikatów z urządzenia do chmury na różnych etapach postępu zadania.

## <a name="message-expiration-time-to-live"></a>Wygaśnięcie wiadomości (czas wygaśnięcia)

Każdy komunikat chmury do urządzenia ma czas wygaśnięcia. Ten czas jest ustawiany przez jedną z następujących czynności:

* **Właściwość ExpiryTimeUtc** w usłudze
* Centrum IoT hub, przy użyciu domyślnego *czasu do przeżywania,* który jest określony jako właściwość centrum IoT

Zobacz [Opcje konfiguracji z chmury do urządzenia](#cloud-to-device-configuration-options).

Typowym sposobem, aby skorzystać z wygaśnięcia wiadomości i uniknąć wysyłania wiadomości do rozłączonych urządzeń jest ustawienie *krótkiego czasu na* wartości na żywo. Takie podejście osiąga taki sam wynik jak utrzymanie stanu połączenia urządzenia, ale jest bardziej wydajne. Podczas żądania potwierdzenia wiadomości centrum IoT hub powiadamia, które urządzenia są:

* Możliwość odbierania wiadomości.
* Nie są online lub nie powiodło się.

## <a name="message-feedback"></a>Opinie o wiadomościach

Podczas wysyłania wiadomości z chmury do urządzenia, usługa może zażądać dostarczenia opinii na wiadomość o ostatecznym stanie tej wiadomości. Można to zrobić, ustawiając właściwość aplikacji **iothub-ack** w komunikacie z chmury do urządzenia, który jest wysyłany do jednej z następujących czterech wartości:

| Wartość właściwości Ack | Zachowanie |
| ------------ | -------- |
| brak     | Centrum IoT hub nie generuje komunikatu zwrotnego (zachowanie domyślne). |
| pozytywna | Jeśli komunikat chmury do urządzenia osiągnie stan *Ukończone,* Centrum IoT generuje komunikat zwrotny. |
| negative | Jeśli komunikat chmury do urządzenia osiągnie stan *martwej litery,* Centrum IoT generuje komunikat zwrotny. |
| Pełne     | Centrum IoT generuje komunikat zwrotny w obu przypadkach. |

Jeśli wartość **Ack** jest *pełna*i nie otrzymasz wiadomości zwrotnej, oznacza to, że wiadomość zwrotna wygasła. Usługa nie może wiedzieć, co się stało z oryginalną wiadomością. W praktyce usługa powinna zapewnić, że może przetwarzać opinie przed jej wygaśnięciem. Maksymalny czas wygaśnięcia wynosi dwa dni, co pozostawia czas, aby ponownie uruchomić usługę, jeśli wystąpi błąd.

Jak wyjaśniono w [punktach końcowych,](iot-hub-devguide-endpoints.md)centrum IoT dostarcza informacje zwrotne za pośrednictwem punktu końcowego skierowanego do usługi, */messages/servicebound/feedback*, jako wiadomości. Semantyka do odbierania opinii są takie same jak w przypadku komunikatów z chmury do urządzenia. Jeśli to możliwe, opinie o wiadomości są wsadowe w jednej wiadomości, w następującym formacie:

| Właściwość     | Opis |
| ------------ | ----------- |
| Czas w kolejce | Sygnatura czasowa wskazująca, kiedy centrum odebrało wiadomość zwrotną |
| UserId       | `{iot hub name}` |
| Contenttype  | `application/vnd.microsoft.iothub.feedback.json` |

Treść jest szeregowaną tablicą rekordów json, z których każda ma następujące właściwości:

| Właściwość           | Opis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Sygnatura czasowa wskazująca, kiedy wystąpił wynik wiadomości (na przykład centrum odebrało wiadomość zwrotną lub oryginalna wiadomość wygasła) |
| OriginalMessageId (ida)  | *MessageId* komunikatu z chmury do urządzenia, do którego odnoszą się te informacje zwrotne |
| Statuscode         | Wymagany ciąg używany w komunikatach zwrotnych generowanych przez centrum IoT hub: <br/> *Powodzenie* <br/> *Wygasłe* <br/> *DeliveryCountExceeded* <br/> *Odrzucone* <br/> *Usunięte* |
| Opis        | Wartości ciągu dla *kodu stanu* |
| DeviceId           | *Identyfikator urządzenia* docelowego komunikatu z chmury do urządzenia, do którego odnosi się ta informacja zwrotna |
| DeviceGenerationId (Identyfikator pouczania urządzeń) | *DeviceGenerationId* urządzenia docelowego komunikatu chmury do urządzenia, do którego odnosi się ta informacja zwrotna |

Aby komunikat z chmury do urządzenia skorelować jego opinie z oryginalną wiadomością, usługa musi określić *MessageId*.

Treść wiadomości zwrotnej jest wyświetlana w następującym kodzie:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**Oczekująca opinia na usunięte urządzenia**

Po usunięciu urządzenia wszelkie oczekujące opinie są również usuwane. Opinie o urządzeniu są wysyłane w partiach. Jeśli urządzenie zostanie usunięte w wąskim oknie (często mniej niż 1 sekunda) między podaniem przez urządzenie otrzymania wiadomości a przygotowaniem następnej partii opinii, opinie nie wystąpią.

Możesz rozwiązać to zachowanie, czekając przez pewien czas na oczekujące opinie, aby uzyskać przed usunięciem urządzenia. Pokrewne opinie wiadomości powinny zostać utracone po usunięciu urządzenia.

## <a name="cloud-to-device-configuration-options"></a>Opcje konfiguracji między chmurami a urządzeniem

Każdy centrum IoT udostępnia następujące opcje konfiguracji dla chmury do obsługi wiadomości z urządzenia:

| Właściwość                  | Opis | Zakres i wartość domyślna |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Domyślny czas wygaśnięcia dla komunikatów z chmury do urządzenia | ISO_8601 odstęp do 2 dni (minimum 1 minuta); domyślnie: 1 godzina |
| maxDeliveryCount          | Maksymalna liczba dostaw dla kolejek między chmurami a urządzeniem na urządzenie | od 1 do 100; domyślnie: 10 |
| feedback.ttlAsIso8601     | Przechowywanie wiadomości zwrotnych związanych z usługą | ISO_8601 odstęp do 2 dni (minimum 1 minuta); domyślnie: 1 godzina |
| feedback.maxDeliveryCount | Maksymalna liczba dostarczenia dla kolejki opinii | od 1 do 100; domyślnie: 10 |
| feedback.lockDurationAsIso8601 | Maksymalna liczba dostarczenia dla kolejki opinii | ISO_8601 interwał od 5 do 300 sekund (minimum 5 sekund); domyślnie: 60 sekund. |

Opcje konfiguracji można ustawić w jeden z następujących sposobów:

* **Portal Azure:** W obszarze **Ustawienia** w centrum IoT wybierz pozycję **Wbudowane punkty końcowe** i rozwiń **chmurę na urządzeniach.** (Ustawianie **właściwości feedback.maxDeliveryCount** i **feedback.lockDurationAsIso8601** nie jest obecnie obsługiwane w witrynie Azure portal).

    ![Ustawianie opcji konfiguracji wiadomości z chmury do urządzenia w portalu](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Narzędzie CLI platformy Azure**: użyj polecenia [aktualizacji centrum az iot:](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update)

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o zestawach SDK, których można używać do odbierania komunikatów z chmury do urządzenia, zobacz [Zestawy SDK IoT platformy Azure](iot-hub-devguide-sdks.md).

Aby wypróbować odbieranie komunikatów z chmury do urządzenia, zobacz samouczek [Wysyłanie chmury do urządzenia.](iot-hub-csharp-csharp-c2d.md)
