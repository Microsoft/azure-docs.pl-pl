---
title: Opracowywanie modułów dla Azure IoT Edge | Microsoft Docs
description: Opracowywanie modułów niestandardowych dla Azure IoT Edge, które mogą komunikować się ze środowiskiem uruchomieniowym i IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a30b4b056d56e096f80b9494ab80a585fff76e66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489933"
---
# <a name="develop-your-own-iot-edge-modules"></a>Opracowywanie własnych modułów IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Moduły Azure IoT Edge mogą łączyć się z innymi usługami platformy Azure i współtworzyć w większym potoku danych w chmurze. W tym artykule opisano sposób opracowywania modułów w celu komunikowania się z IoT Edge środowiska uruchomieniowego i IoT Hub, a w związku z tym w pozostałej części chmury platformy Azure.

## <a name="iot-edge-runtime-environment"></a>Środowisko uruchomieniowe IoT Edge

Środowisko uruchomieniowe IoT Edge zapewnia infrastrukturę do integrowania funkcji wielu modułów IoT Edge i wdrażania ich na urządzeniach IoT Edge. Każdy program może być spakowany jako moduł IoT Edge. Aby w pełni wykorzystać możliwości IoT Edge funkcji komunikacji i zarządzania programu, program uruchomiony w module może użyć zestawu SDK urządzenia usługi Azure IoT do nawiązania połączenia z lokalnym centrum IoT Edge.
::: moniker range=">=iotedge-2020-11"
Moduły mogą również używać dowolnego klienta MQTT do nawiązywania połączenia z lokalnym brokerem centrum MQTT IoT Edge.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>Pakowanie programu jako modułu IoT Edge

Aby wdrożyć program na urządzeniu IoT Edge, należy najpierw go umieścić w kontenerze i uruchomić go za pomocą aparatu zgodnego z platformą Docker. IoT Edge używa [Moby](https://github.com/moby/moby), projektu Open Source za platformą Docker, jako aparatu zgodnego z platformą Docker. Te same parametry, które są używane z platformą Docker, można przesłać do modułów IoT Edge. Aby uzyskać więcej informacji, zobacz [jak skonfigurować opcje tworzenia kontenerów dla modułów IoT Edge](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>Korzystanie z Centrum IoT Edge

Centrum IoT Edge oferuje dwie główne funkcje: proxy do IoT Hub i komunikacji lokalnej.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Nawiązywanie połączenia z Centrum IoT Edge z poziomu modułu

Połączenie z lokalnym centrum IoT Edge z modułu obejmuje te same kroki połączenia, jak dla wszystkich klientów. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z centrum IoT Edge](iot-edge-runtime.md#connecting-to-the-iot-edge-hub).

Aby użyć usługi IoT Edge Routing za pośrednictwem AMQP lub MQTT, możesz użyć ModuleClient z zestawu Azure IoT SDK. Utwórz wystąpienie ModuleClient, aby połączyć moduł z Centrum IoT Edge uruchomionego na urządzeniu, podobnie jak wystąpienia DeviceClient połączą urządzenia IoT z IoT Hub. Aby uzyskać więcej informacji na temat klasy ModuleClient i jej metod komunikacji, zobacz Dokumentacja interfejsu API dla preferowanego języka SDK: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)lub [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Aby korzystać z IoT Edge brokera MQTT, musisz dysponować własnym klientem MQTT i samodzielnie inicjować połączenie z informacjami pobranymi z interfejsu API obciążeń IoT Edge demona. <!--Need to add details here-->

Aby uzyskać więcej informacji na temat wybierania tras routingu lub publikowania/subskrybowania z brokerem MQTT, zobacz [komunikacja lokalna](iot-edge-runtime.md#local-communication).

### <a name="mqtt-broker-primitives"></a>Elementy podstawowe brokera MQTT

#### <a name="send-a-message-on-a-user-defined-topic"></a>Wyślij wiadomość w temacie zdefiniowanym przez użytkownika

Za pomocą IoT Edge brokera MQTT można publikować komunikaty we wszystkich tematach zdefiniowanych przez użytkownika. W tym celu Autoryzuj moduł do publikowania w określonych tematach, a następnie uzyskaj token z interfejsu API obciążenia, który będzie używany jako hasło podczas nawiązywania połączenia z brokerem usługi MQTT, a następnie Publikuj komunikaty w autoryzowanych tematach z wybranym przez klienta MQTT.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Odbieranie komunikatów w temacie zdefiniowanym przez użytkownika

W przypadku IoT Edge brokera MQTT komunikaty są podobne. Najpierw upewnij się, że moduł jest autoryzowany do subskrybowania określonych tematów, a następnie uzyskaj token z interfejsu API obciążenia, który będzie używany jako hasło podczas nawiązywania połączenia z brokerem usługi MQTT, i wreszcie Subskrybuj wiadomości w autoryzowanych tematach z wybranym przez klienta MQTT.

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub elementy podstawowe

IoT Hub widzi wystąpienie modułu analogicznie do urządzenia, w tym sensie:

* może wysyłać [komunikaty z urządzenia do chmury](../iot-hub/iot-hub-devguide-messaging.md);
* może otrzymywać [bezpośrednie metody](../iot-hub/iot-hub-devguide-direct-methods.md) ukierunkowane na jego tożsamość.
* ma dwuosiowy moduł, który jest odrębny i odizolowany od [sznurka urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) i innego modułu bliźniaczych reprezentacji tego urządzenia;

Obecnie moduły nie mogą odbierać komunikatów z chmury do urządzenia ani używać funkcji przekazywania plików.

Podczas pisania modułu można nawiązać połączenie z Centrum IoT Edge i używać IoT Hub podstawowych, tak jak w przypadku używania IoT Hub z aplikacją urządzenia. Jedyną różnicą między modułami IoT Edge i aplikacjami urządzeń IoT jest konieczność odwoływania się do tożsamości modułu zamiast tożsamości urządzenia.

#### <a name="device-to-cloud-messages"></a>Komunikaty z urządzenia do chmury

Moduł IoT Edge może wysyłać komunikaty do chmury za pośrednictwem Centrum IoT Edge, które działa jako Broker lokalny i propaguje komunikaty do chmury. Aby włączyć złożone przetwarzanie komunikatów z urządzenia do chmury, moduł IoT Edge może również przechwycić i przetworzyć komunikaty wysyłane przez inne moduły lub urządzenia do swojego lokalnego centrum IoT Edge i wysyłać nowe komunikaty z przetworzonymi danymi. W tym celu można utworzyć łańcuchy modułów IoT Edge, aby tworzyć potoki przetwarzania lokalnego.

Aby wysyłać komunikaty telemetryczne z urządzenia do chmury przy użyciu routingu, użyj ModuleClient zestawu SDK usługi Azure IoT. W zestawie SDK usługi Azure IoT każdy moduł ma koncepcję punktów końcowych *danych wejściowych* i *wyjściowych* modułu, które są mapowane na specjalne tematy MQTT. Użyj metody, która wyśle `ModuleClient.sendMessageAsync` komunikaty w wyjściowym punkcie końcowym modułu. Następnie skonfiguruj trasę w edgeHub, aby wysłać ten wyjściowy punkt końcowy do IoT Hub.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Wysyłanie komunikatów telemetrycznych z urządzenia do chmury przy użyciu brokera usługi MQTT jest podobne do publikowania komunikatów w tematach zdefiniowanych przez użytkownika, ale przy użyciu następującego IoT Hub specjalnego tematu dla modułu: `devices/<device_name>/<module_name>/messages/events` . Autoryzacje muszą być odpowiednio skonfigurowane. Mostek MQTT musi być również skonfigurowany do przesyłania dalej komunikatów z tego tematu do chmury.

::: moniker-end

Aby przetwarzać komunikaty przy użyciu routingu, należy najpierw skonfigurować trasę do wysyłania komunikatów pochodzących z innego punktu końcowego (modułu lub urządzenia) do wejściowego punktu końcowego modułu, a następnie nasłuchiwać komunikatów w wejściowym punkcie końcowym modułu. Za każdym razem, gdy nowa wiadomość wraca do tyłu, funkcja wywołania zwrotnego jest wyzwalana przez zestaw SDK usługi Azure IoT. Przetwarzaj wiadomości za pomocą tej funkcji wywołania zwrotnego i opcjonalnie wysyłaj nowe wiadomości w kolejce punktów końcowych modułu.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Przetwarzanie komunikatów przy użyciu brokera MQTT jest podobne do subskrybowania komunikatów w tematach zdefiniowanych przez użytkownika, ale przy użyciu IoT Edge specjalnych tematów w kolejce wyjściowej modułu: `devices/<device_name>/<module_name>/messages/events` . Autoryzacje muszą być odpowiednio skonfigurowane. Opcjonalnie możesz wysyłać nowe wiadomości w wybranych tematach.

::: moniker-end

#### <a name="twins"></a>Bliźniaczych reprezentacji

Bliźniaczych reprezentacji jest jednym z elementów podstawowych dostarczonych przez IoT Hub. Istnieją dokumenty JSON, które przechowują informacje o stanie, w tym metadane, konfiguracje i warunki. Każdy moduł lub urządzenie ma swój własny sznurek.

Aby uzyskać splot modułu z zestawem SDK usługi Azure IoT, wywołaj `ModuleClient.getTwin` metodę.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Aby uzyskać sznurki modułowe z dowolnym klientem MQTT, jest uwzględniana nieco większa ilość pracy, ponieważ uzyskanie sznurka nie jest typowym wzorcem MQTT. Moduł musi najpierw subskrybować IoT Hub specjalny temat `$iothub/twin/res/#` . Ta nazwa tematu jest dziedziczona po IoT Hub, a wszystkie urządzenia/moduły muszą subskrybować ten sam temat. Nie oznacza to, że urządzenia odbierają sznury siebie nawzajem. IoT Hub i edgeHub wiedzą, które sznurki powinny być dostarczone, nawet jeśli wszystkie urządzenia nasłuchują tej samej nazwy tematu. Po dokonaniu subskrypcji moduł musi poprosić o dwuosiową publikację, publikując wiadomość w następującym IoT Hub specjalnym temacie z IDENTYFIKATORem żądania `$iothub/twin/GET/?$rid=1234` . Ten identyfikator żądania jest dowolnym IDENTYFIKATORem (czyli identyfikatorem GUID), który zostanie wysłany z powrotem przez IoT Hub wraz z żądanymi danymi. Jest to sposób, w jaki klient może sparować swoje żądania z odpowiedziami. Kod wyniku jest kodem stanu podobnym do HTTP, gdzie pomyślne kodowanie jest 200.

::: moniker-end

Aby otrzymać poprawkę w module z zestawem SDK usługi Azure IoT, zaimplementuj funkcję wywołania zwrotnego i zarejestruj ją za pomocą `ModuleClient.moduleTwinCallback` metody z zestawu SDK usługi Azure IoT, aby funkcja wywołania zwrotnego była wyzwalana za każdym razem, gdy zostanie wyświetlona poprawka przędzy.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Aby otrzymać poprawkę MQTT modułu z dowolnego klienta programu, proces jest bardzo podobny do otrzymywania pełnego bliźniaczych reprezentacjiu: Klient musi subskrybować specjalny temat IoT Hub `$iothub/twin/PATCH/properties/desired/#` . Po dokonaniu subskrypcji, gdy IoT Hub wysyła zmianę odpowiedniej sekcji z sznurka, klient otrzymuje tę wiadomość.

::: moniker-end

#### <a name="receive-direct-methods"></a>Otrzymywanie metod bezpośrednich

Aby otrzymać metodę bezpośrednią z zestawem SDK usługi Azure IoT, zaimplementuj funkcję wywołania zwrotnego i zarejestruj ją za pomocą `ModuleClient.methodCallback` metody z zestawu SDK usługi Azure IoT, aby funkcja wywołania zwrotnego była wywoływana za każdym razem, gdy zostanie wyświetlona bezpośrednia Metoda.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Aby otrzymać metodę bezpośrednią z dowolnego klienta MQTT, proces jest bardzo podobny do odbierania poprawek bliźniaczych. Klient musi potwierdzić, że odebrał wywołanie i może wysłać kilka informacji w tym samym czasie. Specjalny IoT Hub tematu subskrybowania usługi `$iothub/methods/POST/#` .

::: moniker-end

## <a name="language-and-architecture-support"></a>Obsługa języków i architektury

IoT Edge obsługuje wiele systemów operacyjnych, architektury urządzeń i języki programowania, dzięki czemu można stworzyć scenariusz, który odpowiada Twoim potrzebom. Ta sekcja służy do poznania opcji tworzenia niestandardowych modułów IoT Edge. Więcej informacji o obsłudze narzędzi i wymaganiach dla każdego języka można znaleźć w temacie [Przygotowywanie środowiska deweloperskiego i testowego do IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

W przypadku wszystkich języków w poniższej tabeli IoT Edge obsługuje programowanie dla urządzeń z systemem AMD64 i ARM32 Linux.

| Język programowania | Narzędzia programistyczne |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Program Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Program Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Obsługa programowania i debugowania dla urządzeń z systemem ARM64 Linux jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać więcej informacji, zobacz [programowanie i debugowanie modułów IoT Edge arm64 w Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

W przypadku wszystkich języków w poniższej tabeli IoT Edge obsługuje programowanie dla urządzeń z systemem AMD64 Windows.

| Język programowania | Narzędzia programistyczne |
| -------------------- | ----------------- |
| C | Program Visual Studio 2017/2019 |
| C# | Visual Studio Code (bez możliwości debugowania)<br>Program Visual Studio 2017/2019 |

## <a name="next-steps"></a>Następne kroki

[Przygotuj środowisko deweloperskie i testowe dla IoT Edge](development-environment.md)

[Korzystanie z programu Visual Studio do opracowywania modułów w języku C# dla IoT Edge](how-to-visual-studio-develop-module.md)

[Użyj Visual Studio Code do opracowania modułów dla IoT Edge](how-to-vs-code-develop-module.md)

[Omówienie i używanie zestawów SDK usługi Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md)
