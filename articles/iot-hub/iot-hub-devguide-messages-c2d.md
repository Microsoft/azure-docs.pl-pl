---
title: Opis Azure IoT Hub komunikatów z chmury do urządzenia | Microsoft Docs
description: W tym przewodniku dla deweloperów omówiono sposób używania komunikatów z chmury do urządzenia w centrum IoT. Zawiera on informacje o cyklu życia komunikatu i opcjach konfiguracji.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 7bb3ca2b31eaef5c0639f30e0f2a329a37dfe7e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761785"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Wysyłanie komunikatów z chmury do urządzenia z centrum IoT

Aby wysyłać jednokierunkowe powiadomienia do aplikacji urządzenia z zadedyku rozwiązania, wysyłaj komunikaty z chmury do urządzenia z centrum IoT do urządzenia. Aby uzyskać omówienie innych opcji chmura-urządzenie obsługiwanych przez usługę Azure IoT Hub, zobacz Wskazówki dotyczące komunikacji między [chmurą a urządzeniem.](iot-hub-devguide-c2d-guidance.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Komunikaty z chmury do urządzenia są wysyłane za pośrednictwem punktu końcowego dla usługi */messages/devicebound.* Następnie urządzenie odbiera komunikaty za pośrednictwem punktu końcowego specyficznego dla urządzenia */devices/{deviceId}/messages/devicebound.*

Aby kierować każdy komunikat z chmury do urządzenia na jednym  urządzeniu, centrum IoT hub ustawia właściwość na *wartość /devices/{deviceId}/messages/devicebound.*

Każda kolejka urządzeń zawiera co najwyżej 50 komunikatów z chmury do urządzenia. Próba wysłania większej liczby komunikatów do tego samego urządzenia powoduje błąd.

## <a name="the-cloud-to-device-message-life-cycle"></a>Cykl życia komunikatów chmura-urządzenie

Aby zagwarantować co najmniej raz dostarczenie komunikatów, centrum IoT będzie utrwalać komunikaty z chmury do urządzenia w kolejkach dla węzłów. Aby centrum IoT hub usuwało komunikaty z kolejki, urządzenia muszą jawnie potwierdzić *ukończenie.* Takie podejście gwarantuje odporność na awarie łączności i urządzeń.

Wykres stanu cyklu życia jest wyświetlany na poniższym diagramie:

![Cykl życia komunikatów chmura-urządzenie](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Gdy usługa IoT Hub wysyła komunikat do urządzenia, ustawia stan komunikatu *w kolejkach*. Gdy urządzenie chce odebrać *komunikat,* centrum IoT *blokuje* ten komunikat, ustawiając stan *Niewidoczne.* Ten stan umożliwia innym wątkom na urządzeniu rozpoczęcie odbierania innych komunikatów. Po zakończeniu przetwarzania komunikatu wątek urządzenia powiadamia centrum IoT, wypełniając *komunikat.* Następnie centrum IoT ustawia stan na *Ukończono.*

Urządzenie może również:

* *Odrzuć* komunikat, co powoduje, że centrum IoT ustawia dla niego stan *utraconych wiadomości.* Urządzenia, które łączą się za pośrednictwem protokołu MQTT (Message Queuing Telemetry Transport), nie mogą odrzucać komunikatów wysyłanych z chmury do urządzeń.

* *Porzuć* komunikat, co powoduje, że centrum IoT hub umieszcza komunikat z powrotem w kolejce ze stanem *W kolejce.* Urządzenia, które łączą się za pośrednictwem protokołu MQTT, nie mogą porzucić komunikatów z chmury do urządzenia.

Wątek może nie przetworzyć komunikatu bez powiadomienia centrum IoT. W takim przypadku komunikaty automatycznie przejdą  ze stanu *Niewidoczne* z powrotem do stanu w kolejkach po przechyłym czasie widoczności (lub zablokowaniu *czasu).*  Wartość tego czasu wynosi jedną minutę i nie można jej zmienić.

Właściwość **maksymalnej liczby dostaw** w centrum IoT określa maksymalną liczbę przejść  komunikatu między stanami w kolejkach i *niewidocznymi.* Po tej liczbie przejść centrum IoT ustawia stan komunikatu na *Utracony komunikat*. Podobnie centrum IoT ustawia stan komunikatu na *Utracone* wiadomości po upływie czasu jego wygaśnięcia. Aby uzyskać więcej informacji, zobacz [Time to live (Czas do transmisji na żywo).](#message-expiration-time-to-live)

W [artykule How to send cloud-to-device messages with IoT Hub](iot-hub-csharp-csharp-c2d.md) (Jak wysyłać komunikaty z chmury do urządzenia za pomocą usługi IoT Hub) pokazano, jak wysyłać komunikaty z chmury do urządzenia i odbierać je na urządzeniu.

Urządzenie zwykle kończy komunikat z chmury do urządzenia, gdy utrata komunikatu nie wpływa na logikę aplikacji. Przykładem może być sytuacji, gdy urządzenie utrwala zawartość komunikatu lokalnie lub pomyślnie wykonało operację. Komunikat może również przenosić informacje przejściowe, których utrata nie miałaby wpływu na funkcjonalność aplikacji. Czasami w przypadku długotrwałych zadań można:

* Ukończ komunikat z chmury do urządzenia po utrwaleniu opisu zadania w magazynie lokalnym.

* Powiadamianie za pomocą jednego lub większej liczby komunikatów z urządzenia do chmury na różnych etapach postępu zadania.

## <a name="message-expiration-time-to-live"></a>Wygaśnięcie komunikatu (czas wygaśnięcia)

Każdy komunikat z chmury do urządzenia ma czas wygaśnięcia. Ten czas jest ustawiany przez jedną z następujących czynności:

* Właściwość **ExpiryTimeUtc** w usłudze
* Centrum IoT hub, używając  domyślnego czasu, który jest określony jako właściwość centrum IoT

Zobacz [Opcje konfiguracji chmura-urządzenie.](#cloud-to-device-configuration-options)

Częstym sposobem korzystania z wygasania komunikatów i uniknięcia wysyłania komunikatów do odłączonych urządzeń jest ustawienie wartości *na żywo w krótkim* czasie. Takie podejście zapewnia taki sam wynik jak utrzymywanie stanu połączenia urządzenia, ale jest bardziej wydajne. Gdy żądasz potwierdzeń komunikatów, centrum IoT powiadomi Cię, które urządzenia są:

* Możliwość odbierania komunikatów.
* Nie są w trybie online lub zakończyły się niepowodzeniem.

## <a name="message-feedback"></a>Opinia o wiadomościach

W przypadku wysyłania komunikatu z chmury do urządzenia usługa może zażądać dostarczenia opinii na temat 1 wiadomości dotyczącej ostatecznego stanu tego komunikatu. Można to zrobić, ustawiając właściwość aplikacji **iothub-ack** w komunikacie z chmury do urządzenia, który jest wysyłany do jednej z czterech następujących wartości:

| Wartość właściwości Ack | Zachowanie |
| ------------ | -------- |
| brak     | Centrum IoT nie generuje komunikatu opinii (zachowanie domyślne). |
| positive | Jeśli komunikat z chmury do urządzenia osiągnie stan *Ukończono,* centrum IoT wygeneruje komunikat opinii. |
| negative | Jeśli komunikat z chmury do  urządzenia osiągnie stan utraconych wiadomości, centrum IoT wygeneruje komunikat opinii. |
| Pełne     | W obu przypadkach centrum IoT generuje komunikat opinii. |

Jeśli wartość **Ack** jest *pełna* i nie otrzymasz wiadomości zwrotnej, oznacza to, że komunikat opinii wygasł. Usługa nie może wiedzieć, co się stało z oryginalnym komunikatem. W praktyce usługa powinna upewnić się, że może przetworzyć opinię przed jej wygaśnięciem. Maksymalny czas wygaśnięcia wynosi dwa dni, co pozostawia czas na uruchomienie usługi ponownie w przypadku wystąpienia awarii.

Jak wyjaśniono [w punkcie końcowym,](iot-hub-devguide-endpoints.md)centrum IoT hub dostarcza informacje zwrotne za pośrednictwem punktu końcowego dla usługi */messages/servicebound/feedback* jako komunikaty. Semantyka otrzymywania opinii jest taka sama jak w przypadku komunikatów z chmury do urządzenia. Jeśli to możliwe, opinie o komunikatach są wsadowe w jednym komunikacie w następującym formacie:

| Właściwość     | Opis |
| ------------ | ----------- |
| EnqueuedTime | Znacznik czasu wskazujący, kiedy komunikat opinii został odebrany przez centrum |
| UserId       | `{iot hub name}` |
| Contenttype  | `application/vnd.microsoft.iothub.feedback.json` |

System wyśle opinię, gdy partia osiągnie 64 komunikaty, lub w ciągu 15 sekund od ostatniego wysłania, w zależności od tego, co nastąpi najpierw. 

Treść jest tablicą rekordów serializowana w pliku JSON, z których każdy ma następujące właściwości:

| Właściwość           | Opis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Sygnatura czasowa wskazująca, kiedy wynik komunikatu się wydarzył (na przykład centrum otrzymało komunikat opinii lub oryginalny komunikat wygasł) |
| OriginalMessageId  | MessageId *komunikatu* z chmury do urządzenia, do którego odnoszą się te informacje zwrotne |
| Statuscode         | Wymagany ciąg używany w komunikatach opinii generowanych przez centrum IoT: <br/> *Powodzenie* <br/> *Wygasła* <br/> *DeliveryCountExceeded* <br/> *Odrzucone* <br/> *Usunięte* |
| Opis        | Wartości ciągów dla *statusCode* |
| DeviceId           | *DeviceId* urządzenia docelowego komunikatu z chmury do urządzenia, do którego odnosi się ta opinia |
| DeviceGenerationId | *DeviceGenerationId* urządzenia docelowego komunikatu z chmury do urządzenia, do którego odnosi się ta opinia |

Aby komunikat z chmury do urządzenia skorelować swoją opinię z oryginalnym komunikatem, usługa musi określić wartość *MessageId*.

Treść komunikatu z opinią jest wyświetlana w następującym kodzie:

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

**Oczekiwanie na opinię na temat usuniętych urządzeń**

Po usunięciu urządzenia usuwane są również wszelkie oczekujące opinie. Opinie dotyczące urządzeń są wysyłane w partiach. Jeśli urządzenie zostanie usunięte w wąskim oknie (często krótszym niż 1 sekunda) między potwierdzeniem odebrania komunikatu a przygotowaniem następnej partii opinii, opinia nie nastąpi.

Możesz rozwiązać ten problem, czekając przez określony czas na oczekiwanie na opinię przed usunięciem urządzenia. Po usunięciu urządzenia należy założyć, że opinia o powiązanych komunikatach zostanie utracona.

## <a name="cloud-to-device-configuration-options"></a>Opcje konfiguracji chmura-urządzenie

Każde centrum IoT udostępnia następujące opcje konfiguracji dla komunikatów z chmury do urządzenia:

| Właściwość                  | Opis | Zakres i wartość domyślna |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Domyślny czas wygaśnięcia dla komunikatów z chmury do urządzenia | ISO_8601 interwału do 2 dni (co najmniej 1 minuta); wartość domyślna: 1 godzina |
| maxDeliveryCount          | Maksymalna liczba dostaw dla kolejek chmura-urządzenie na urządzenie | od 1 do 100; wartość domyślna: 10 |
| feedback.ttlAsIso8601     | Przechowywanie dla komunikatów opinii związanych z usługą | ISO_8601 interwału do 2 dni (minimum 1 minuta); wartość domyślna: 1 godzina |
| feedback.maxDeliveryCount | Maksymalna liczba dostaw dla kolejki opinii | od 1 do 100; wartość domyślna: 10 |
| feedback.lockDurationAsIso8601 | Maksymalna liczba dostaw dla kolejki opinii | ISO_8601 interwału od 5 do 300 sekund (co najmniej 5 sekund); wartość domyślna: 60 sekund. |

Opcje konfiguracji można ustawić na jeden z następujących sposobów:

* **Azure Portal:** w **obszarze Ustawienia** w centrum IoT hub wybierz pozycję Wbudowane punkty końcowe i **rozwiń** węzeł Komunikaty **z chmury do urządzenia.** (Ustawianie właściwości **feedback.maxDeliveryCount** i **feedback.lockDurationAsIso8601** nie jest obecnie obsługiwane w Azure Portal).

    ![Ustawianie opcji konfiguracji dla komunikatów z chmury do urządzenia w portalu](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Interfejs wiersza polecenia platformy Azure:** użyj [polecenia az iot hub update:](/cli/azure/iot/hub#az_iot_hub_update)

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

Aby uzyskać informacje o zestawach SDK, których można używać do odbierania komunikatów z chmury do urządzenia, zobacz [Zestawy SDK usługi Azure IoT.](iot-hub-devguide-sdks.md)

Aby spróbować odbierać komunikaty z chmury do urządzenia, zobacz [samouczek Wysyłanie z](iot-hub-csharp-csharp-c2d.md) chmury do urządzenia.
