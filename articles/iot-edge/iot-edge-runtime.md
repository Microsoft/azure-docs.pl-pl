---
title: Dowiedz się, jak środowisko uruchomieniowe zarządza urządzeniami — Azure IoT Edge | Microsoft Docs
description: Dowiedz się, jak środowisko uruchomieniowe IoT Edge zarządza modułami, zabezpieczeniami, komunikacją i raportowaniem na urządzeniach
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 74cfe4ba3c92d8d96dd196ef6f612b9ed7c0da9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496256"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Poznaj środowisko uruchomieniowe Azure IoT Edge i jego architekturę

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Środowisko uruchomieniowe usługi IoT Edge to kolekcja programów, które zmieniają urządzenie w urządzenie usługi IoT Edge. Zbiorowo składniki środowiska uruchomieniowego IoT Edge umożliwiają IoT Edge urządzeniom otrzymywanie kodu do uruchomienia na brzegu i przekazywanie wyników.

Środowisko uruchomieniowe IoT Edge jest odpowiedzialne za następujące funkcje na urządzeniach IoT Edge:

* Instaluje i aktualizuje pakiety robocze na urządzeniu.

* Utrzymuje standardy zabezpieczeń usługi Azure IoT Edge na urządzeniu.

* Upewnij się, że [moduły IoT Edge](iot-edge-modules.md) są zawsze uruchomione.

* Przesyła raporty o kondycji modułów do chmury na potrzeby zdalnego monitorowania.

* Zarządzanie komunikacją między urządzeniami podrzędnymi a urządzeniami IoT Edge.

* Zarządzanie komunikacją między modułami na urządzeniu IoT Edge.

* Zarządzanie komunikacją między urządzeniem IoT Edge i chmurą.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* Zarządzanie komunikacją między urządzeniami IoT Edge.
::: moniker-end

![Środowisko uruchomieniowe komunikuje się ze szczegółowymi informacjami i kondycją modułu, aby IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Obowiązki środowiska uruchomieniowego IoT Edge dzielą się na dwie kategorie: komunikacja i zarządzanie modułem. Te dwie role są wykonywane przez dwa składniki, które są częścią środowiska uruchomieniowego IoT Edge. *Agent IoT Edge* wdraża i monitoruje moduły, a *Centrum IoT Edge* jest odpowiedzialne za komunikację.

Zarówno Agent IoT Edge, jak i centrum IoT Edge są modułami, podobnie jak każdy inny moduł uruchomiony na IoT Edge urządzeniu. Są one czasami określane jako *moduły środowiska uruchomieniowego*.

## <a name="iot-edge-agent"></a>Agent IoT Edge

Agent IoT Edge jest jednym z dwóch modułów, które tworzą Azure IoT Edge środowiska uruchomieniowego. Jest on odpowiedzialny za tworzenie wystąpień modułów, zapewnienie, że nadal działają, i raportowanie stanu modułów z powrotem do IoT Hub. Te dane konfiguracyjne są zapisywane jako właściwości sznurka modułu agenta IoT Edge.

[Demon zabezpieczenia IoT Edge](iot-edge-security-manager.md) uruchamia agenta IoT Edge przy uruchamianiu urządzenia. Agent pobiera sznurek modułu z IoT Hub i sprawdza manifest wdrożenia. Manifest wdrożenia to plik JSON, który deklaruje moduły, które muszą zostać uruchomione.

Każdy element w manifeście wdrożenia zawiera określone informacje o module i jest używany przez agenta IoT Edge do kontrolowania cyklu życia modułu. Aby uzyskać więcej informacji na temat wszystkich właściwości używanych przez agenta IoT Edge do kontrolowania modułów, Przeczytaj o [właściwościach agenta IoT Edge i IoT Edge module centrum bliźniaczych reprezentacji](module-edgeagent-edgehub.md).

Agent IoT Edge wysyła odpowiedź środowiska uruchomieniowego do IoT Hub. Poniżej znajduje się lista możliwych odpowiedzi:
  
* 200 — OK
* 400 — konfiguracja wdrożenia jest źle sformułowana lub nieprawidłowa.
* 417 — urządzenie nie ma ustawionej konfiguracji wdrożenia.
* 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
* 406 — urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
* 500 — Wystąpił błąd w czasie wykonywania IoT Edge.

Więcej informacji o tworzeniu manifestów wdrożenia znajduje [się w temacie Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

### <a name="security"></a>Zabezpieczenia

Agent IoT Edge odgrywa kluczową rolę w zabezpieczeniach IoT Edge urządzeniu. Na przykład wykonuje akcje takie jak sprawdzenie obrazu modułu przed jego uruchomieniem.

Aby uzyskać więcej informacji na temat środowiska zabezpieczeń Azure IoT Edge, Przeczytaj o programie [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="iot-edge-hub"></a>IoT Edge Hub

Centrum IoT Edge jest innym modułem, który tworzy Azure IoT Edge środowiska uruchomieniowego. Działa jako lokalny serwer proxy dla IoT Hub, uwidaczniając te same punkty końcowe protokołu co IoT Hub. Ta spójność oznacza, że klienci mogą łączyć się z IoT Edge środowiska uruchomieniowego w taki sam sposób jak IoT Hub.

Centrum IoT Edge nie jest pełną wersją IoT Hub uruchomioną lokalnie. IoT Edge Hub dyskretnie delegowanie niektórych zadań do IoT Hub. Na przykład IoT Edge Hub automatycznie pobiera informacje o autoryzacji z IoT Hub przy pierwszym połączeniu, aby umożliwić nawiązanie połączenia z urządzeniem. Po nawiązaniu pierwszego połączenia informacje o autoryzacji są buforowane lokalnie przez IoT Edge centrum. Przyszłe połączenia z tego urządzenia są autoryzowane bez konieczności ponownego pobierania informacji o autoryzacji z chmury.

### <a name="cloud-communication"></a>Komunikacja w chmurze

Aby zmniejszyć przepustowość używaną przez rozwiązanie IoT Edge, centrum IoT Edge optymalizuje, ile rzeczywistych połączeń odbywa się w chmurze. Usługa IoT Edge Hub pobiera logiczne połączenia z modułów lub urządzeń podrzędnych oraz łączy je z jednym połączeniem fizycznym z chmurą. Szczegóły tego procesu są niewidoczne dla reszty rozwiązania. Klienci uważają swoje własne połączenie z chmurą, nawet jeśli są wysyłane przez to samo połączenie. IoT Edge Hub może używać AMQP lub protokołu MQTT do komunikowania się z chmurą, niezależnie od protokołów używanych przez urządzenia podrzędne. Centrum IoT Edge obecnie obsługuje tylko łączenie koniunkcji z pojedynczym połączeniem fizycznym przy użyciu AMQP jako protokołu nadrzędnego i jego możliwości multipleksowania. AMQP jest domyślnym protokołem nadrzędnym.

![IoT Edge Hub jest bramą między urządzeniami fizycznymi i IoT Hub](./media/iot-edge-runtime/gateway-communication.png)

IoT Edge Hub może określić, czy jest on połączony z IoT Hub. W przypadku utraty połączenia IoT Edge centrum zapisuje na komputerze aktualizacje lub sznurki. Po ponownym nawiązaniu połączenia synchronizuje wszystkie dane. Lokalizacja używana dla tymczasowej pamięci podręcznej jest określana przez właściwość sznurka modułu IoT Edge Hub. Rozmiar pamięci podręcznej nie jest nieograniczony i zostanie powiększony, dopóki urządzenie ma pojemność magazynu. Aby uzyskać więcej informacji, zobacz [możliwości trybu offline](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Komunikacja modułu

IoT Edge Hub ułatwia komunikację modułu z modułem. Używanie Centrum IoT Edge jako brokera komunikatów zachowuje moduły niezależne od siebie. Moduły muszą określać dane wejściowe, na których są akceptowane wiadomości i dane wyjściowe, do których są zapisywane wiadomości. Programista rozwiązań może połączyć te dane wejściowe i dane wyjściowe, aby moduły przetwarzali danych w kolejności specyficznej dla tego rozwiązania.

![IoT Edge Hub ułatwia komunikację między modułami](./media/iot-edge-runtime/module-endpoints.png)

Aby wysłać dane do centrum IoT Edge, moduł wywołuje metodę SendEventAsync. Pierwszy argument określa, w którym wyniku chcesz wysłać wiadomość. Następujący pseudokodzie wysyła komunikat w **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Aby odebrać komunikat, zarejestruj wywołanie zwrotne, które przetwarza wiadomości przychodzące do określonych danych wejściowych. Następujący pseudokodzie rejestruje funkcję messageProcessor, która ma być używana do przetwarzania wszystkich komunikatów odebranych w **INPUT1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Aby uzyskać więcej informacji na temat klasy ModuleClient i jej metod komunikacji, zobacz Dokumentacja interfejsu API dla preferowanego języka SDK: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)lub [Node.js](/javascript/api/azure-iot-device/moduleclient).

Deweloper rozwiązania jest odpowiedzialny za określenie reguł, które określają, jak centrum IoT Edge przekazuje komunikaty między modułami. Reguły routingu są zdefiniowane w chmurze i wypychane do IoT Edge Hub w swoim sznurze modułu. Ta sama składnia dla IoT Hub Routes służy do definiowania tras między modułami w Azure IoT Edge. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

![Trasy między modułami przechodzą przez Centrum IoT Edge](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Komunikacja lokalna

IoT Edge Hub ułatwia komunikację lokalną. Umożliwia ona komunikację między modułami, modułem do modułu, komunikatami między urządzeniami przez brokera komunikatów w celu utrzymywania niezależnych od siebie urządzeń i modułów.

>[!NOTE]
> Funkcja MQTT Broker jest w publicznej wersji zapoznawczej przy użyciu IoT Edge wersja 1,2. Musi być jawnie włączona.

Centrum IoT Edge obsługuje dwa mechanizmy brokera:

1. [Funkcje routingu komunikatów obsługiwane przez IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md) i,
2. Broker MQTT ogólnego przeznaczenia, który spełnia [normy MQTT Standard v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)

#### <a name="using-routing"></a>Korzystanie z routingu

Pierwszy mechanizm tworzenia brokera wykorzystuje te same funkcje routingu co IoT Hub, aby określić sposób przekazywania komunikatów między urządzeniami lub modułami. Pierwsze urządzenia lub moduły określają dane wejściowe, na których są akceptowane wiadomości i dane wyjściowe, do których są zapisywane wiadomości. Następnie deweloper rozwiązania może kierować komunikaty między źródłem, np. wyjściem i miejscem docelowym, np. danymi wejściowymi, przy użyciu potencjalnych filtrów.

![Trasy między modułami przechodzą przez Centrum IoT Edge](./media/iot-edge-runtime/module-endpoints-routing.png)

Routing może być używany przez urządzenia lub moduły utworzone za pomocą zestawów SDK urządzeń usługi Azure IoT za pośrednictwem AMQP lub protokołu MQTT. Wszystkie wiadomości IoT Hub podstawowe, np. telemetrię, metody bezpośrednie, C2D, bliźniaczych reprezentacji, są obsługiwane, ale komunikacja ze zdefiniowanymi przez użytkownika tematami nie jest obsługiwana.

Więcej informacji o trasach znajduje [się w temacie Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>Korzystanie z brokera MQTT

Mechanizm drugiego brokera jest oparty na standardowym brokerze MQTT. MQTT to uproszczony protokół transferu komunikatów, który gwarantuje optymalne osiągi urządzeń z ograniczoną ilością zasobów i jest popularnym standardem publikowania i subskrybowania. Urządzenia lub moduły subskrybują tematy dotyczące odbierania komunikatów opublikowanych przez inne urządzenia lub moduły. IoT Edge Hub implementuje własnego brokera MQTT, który [jest zgodny ze specyfikacją MQTT w wersji 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html).

Broker MQTT umożliwia korzystanie z dwóch dodatkowych wzorców komunikacji w porównaniu do routingu: lokalnego rozgłaszania i komunikacji punkt-punkt. Emisja lokalna jest przydatna, gdy jedno urządzenie lub moduł wymaga lokalnego alertu wielu innych urządzeń lub modułów. Komunikacja punkt-punkt umożliwia dwóm IoT Edge urządzeniom lub dwóm urządzeniom IoT komunikowanie się lokalnie bez przejazdu do chmury.

![Publikowanie i subskrybowanie lokalnie za pomocą Centrum IoT Edge](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

Brokera MQTT może używać urządzeń lub modułów utworzonych przy użyciu zestawów SDK urządzeń usługi Azure IoT, które komunikują się za pośrednictwem protokołu MQTT lub wszelkich klientów MQTT ogólnego przeznaczenia. Z wyjątkiem C2D wszystkich IoT Hub komunikatów podstawowych, np. telemetrii, metody bezpośrednie, bliźniaczych reprezentacji są obsługiwane. IoT Hub Tematy specjalne używane przez IoT Hub elementy podstawowe są obsługiwane i dlatego są tematami zdefiniowanymi przez użytkownika.
Ten temat może być IoT Hub specjalnym tematem lub tematem zdefiniowanym przez użytkownika.

W przeciwieństwie do mechanizmu routingu, porządkowanie komunikatów jest tylko najlepszym rozwiązaniem i nie jest gwarantowane, a filtrowanie komunikatów nie jest obsługiwane przez brokera. Brak tych funkcji umożliwia jednak szybsze działanie brokera usługi MQTT.

Aby uzyskać więcej informacji na temat brokera usługi MQTT, zobacz [Publikowanie i subskrybowanie przy użyciu IoT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>Porównanie mechanizmów brokera

Oto funkcje dostępne dla każdego mechanizmu brokera:

|Funkcje  | Routing  | Broker MQTT  |
|---------|---------|---------|
|Telemetrię D2C    |     &#10004;    |         |
|Lokalna Telemetria     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Splot     |    &#10004;     |    &#10004;     |
|C2D dla urządzeń     |   &#10004;      |         |
|Zamawianie     |    &#10004;     |         |
|Filtrowanie     |     &#10004;    |         |
|Tematy zdefiniowane przez użytkownika     |         |    &#10004;     |
|Urządzenie-urządzenie     |         |    &#10004;     |
|Emisja lokalna     |         |    &#10004;     |
|Wydajność     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Nawiązywanie połączenia z Centrum IoT Edge

Centrum IoT Edge akceptuje połączenia od klientów urządzeń lub modułów przy użyciu protokołu MQTT lub protokołu AMQP.

>[!NOTE]
> IoT Edge Hub obsługuje klientów, którzy łączą się za pomocą MQTT lub AMQP. Nie obsługuje klientów korzystających z protokołu HTTP.

Gdy klient łączy się z Centrum IoT Edge, wystąpią następujące działania:

1. Jeśli Transport Layer Security (TLS) jest używany (zalecane), kanał TLS został utworzony w celu nawiązania zaszyfrowanej komunikacji między klientem a centrum IoT Edge.
2. Informacje o uwierzytelnianiu są wysyłane z klienta do IoT Edge Hub w celu zidentyfikowania siebie.
3. IoT Edge Hub autoryzuje lub odrzuca połączenie na podstawie jego zasad autoryzacji.

#### <a name="secure-connections-tls"></a>Bezpieczne połączenia (TLS)

Domyślnie Centrum IoT Edge akceptuje tylko połączenia zabezpieczone protokołem Transport Layer Security (TLS), np. szyfrowane połączenia, których nie można odszyfrować przez inną firmę.

Jeśli klient nawiąże połączenie z portem 8883 (MQTTS) lub 5671 (AMQPS) z Centrum IoT Edge, należy skompilować kanał TLS. Podczas uzgadniania protokołu TLS Centrum IoT Edge wysyła swój łańcuch certyfikatów, którego klient musi zweryfikować. Aby można było sprawdzić poprawność łańcucha certyfikatów, certyfikat główny Centrum IoT Edge musi być zainstalowany jako zaufany certyfikat na komputerze klienckim. Jeśli certyfikat główny nie jest zaufany, Biblioteka klienta zostanie odrzucona przez Centrum IoT Edge z błędem weryfikacji certyfikatu.

Kroki, które należy wykonać w celu zainstalowania tego certyfikatu głównego brokera na urządzeniach klienckich, są opisane w [przezroczystej bramie](how-to-create-transparent-gateway.md) i w dokumentacji dotyczącej [przygotowywania urządzenia podrzędnego](how-to-connect-downstream-device.md#prepare-a-downstream-device) . Moduły mogą używać tego samego certyfikatu głównego co centrum IoT Edge, wykorzystując interfejs API demona IoT Edge.

#### <a name="authentication"></a>Authentication

Centrum IoT Edge akceptuje tylko połączenia z urządzeń lub modułów, które mają tożsamość IoT Hub, np. zarejestrowane w IoT Hub i mają jedną z trzech metod uwierzytelniania klientów obsługiwanych przez Centrum IoT Hub w celu zapewnienia potwierdzenia tożsamości: [uwierzytelniania kluczy symetrycznych](how-to-authenticate-downstream-device.md#symmetric-key-authentication), uwierzytelniania z podpisem [własnym x. 509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), uwierzytelniania [podpisanego przez urząd certyfikacji x. 509](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Te tożsamości IoT Hub mogą być weryfikowane lokalnie przez Centrum IoT Edge, dzięki czemu nadal można nawiązać połączenia w trybie offline.

Uwagi:

* Moduły IoT Edge obsługują obecnie tylko uwierzytelnianie klucza symetrycznego.
* Klienci MQTT z tylko lokalną nazwą użytkownika i hasłami nie są zaakceptowani przez brokera programu IoT Edge Hub MQTT, muszą używać tożsamości IoT Hub.

#### <a name="authorization"></a>Autoryzacja

Po uwierzytelnieniu Centrum IoT Edge ma dwa sposoby autoryzacji połączeń klientów:

* Sprawdzając, czy klient należy do swojego zestawu zaufanych klientów zdefiniowanych w IoT Hub. Zestaw zaufanych klientów jest określany przez skonfigurowanie relacji nadrzędny/podrzędny lub urządzenia/modułu w IoT Hub. Po utworzeniu modułu w IoT Edge relacja zaufania zostanie automatycznie ustanowiona między tym modułem a jego urządzeniem IoT Edge. Jest to jedyny model autoryzacji obsługiwany przez mechanizm brokera routingu.

* Przez skonfigurowanie zasad autoryzacji. Te zasady autoryzacji to dokument zawierający listę wszystkich autoryzowanych tożsamości klientów, które mogą uzyskać dostęp do zasobów w centrum IoT Edge. Jest to podstawowy model autoryzacji używany przez brokera IoT Edge Hub MQTT, chociaż relacje nadrzędny/podrzędny i obiekt/moduł mogą być również zrozumiałe dla brokera MQTT dla tematów IoT Hub.

### <a name="remote-configuration"></a>Konfiguracja zdalna

Centrum IoT Edge jest całkowicie kontrolowane przez chmurę. Pobiera swoją konfigurację z IoT Hub za pośrednictwem [sznurka modułu](iot-edge-modules.md#module-twins). Obejmuje:

* Konfiguracja tras
* Zasady autoryzacji
* Konfiguracja mostka MQTT

Ponadto można wykonać kilka czynności konfiguracyjnych, konfigurując [zmienne środowiskowe w centrum IoT Edge](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Telemetria jakości środowiska uruchomieniowego

IoT Edge zbiera anonimowe dane telemetryczne z środowiska uruchomieniowego hosta i modułów systemowych w celu poprawienia jakości produktu. Te informacje są nazywane telemetrią jakości środowiska uruchomieniowego. Zebrane dane telemetryczne są okresowo wysyłane jako komunikaty z urządzenia do chmury w celu IoT Hub z agenta IoT Edge. Te komunikaty nie są wyświetlane w zwykłych telemetrii klienta i nie zużywają żadnego przydziału komunikatów.

Agent IoT Edge i centrum generują metryki, które można zbierać w celu zrozumienia wydajności urządzeń. Podzbiór tych metryk jest zbierany przez agenta IoT Edge w ramach telemetrii jakości środowiska uruchomieniowego. Metryki zbierane na potrzeby telemetrii jakości środowiska uruchomieniowego są oznaczone znacznikiem `ms_telemetry` . Aby uzyskać informacje o wszystkich dostępnych metrykach, zobacz [dostęp do wbudowanych metryk dostępu](how-to-access-built-in-metrics.md).

Wszelkie informacje o użytkowniku lub organizacji, takie jak nazwy urządzeń i modułów, są usuwane przed przekazywaniem, aby zapewnić anonimowy charakter danych telemetrycznych dotyczących jakości środowiska uruchomieniowego.

Agent IoT Edge zbiera dane telemetryczne co godzinę i wysyła jeden komunikat do IoT Hub co 24 godziny.

Jeśli chcesz zrezygnować z wysyłania danych telemetrycznych środowiska uruchomieniowego z urządzeń, możesz to zrobić na dwa sposoby:

* Ustaw `SendRuntimeQualityTelemetry` zmienną środowiskową na wartość `false` dla **edgeAgent** lub
* Usuń zaznaczenie opcji w Azure Portal podczas wdrażania.

## <a name="next-steps"></a>Następne kroki

* [Omówienie modułów usługi Azure IoT Edge](iot-edge-modules.md)
* [Learn how to deploy modules and establish routes in IoT Edge (Dowiedz się, jak wdrażać moduły i ustanawiać trasy w usłudze IoT Edge)](module-composition.md).
* [Dowiedz się, jak publikować i subskrybować IoT Edge](how-to-publish-subscribe.md)
* [Informacje o metrykach środowiska uruchomieniowego IoT Edge](how-to-access-built-in-metrics.md)
