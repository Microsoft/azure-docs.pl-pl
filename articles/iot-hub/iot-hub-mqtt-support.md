---
title: Poznaj obsługę usługi Azure IoT Hub MQTT | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — obsługa urządzeń łączących się z punktem końcowym przeznaczonym dla urządzeń Usługi IoT hub przy użyciu protokołu MQTT. Zawiera informacje o wbudowanej obsłudze MQTT w zestawach SDK urządzeń IoT platformy Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 86fc5d4845e746604c1ba69f661d1b9ea9d8dca4
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732314"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikowanie się z koncentratorem IoT za pomocą protokołu MQTT

Centrum IoT hub umożliwia urządzeniom komunikowanie się z punktami końcowymi urządzeń Usługi IoT Hub przy użyciu:

* [MQTT v3.1.1](https://mqtt.org/) na porcie 8883
* MQTT v3.1.1 przez WebSocket na porcie 443.

Usługa IoT Hub nie jest w pełni funkcjonalnym brokerem MQTT i nie obsługuje wszystkich zachowań określonych w standardzie MQTT 3.1.1. W tym artykule opisano, jak urządzenia mogą używać obsługiwanych zachowań MQTT do komunikowania się z Centrum IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Cała komunikacja urządzenia z IoT Hub musi być zabezpieczona za pomocą protokołu TLS/SSL. W związku z tym IoT Hub nie obsługuje niezabezpieczonych połączeń przez port 1883.

## <a name="connecting-to-iot-hub"></a>Łączenie się z Centrum IoT

Urządzenie może używać protokołu MQTT do łączenia się z centrum IoT przy użyciu dowolnej z następujących opcji.

* Biblioteki w zestawach [SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks).
* Protokół MQTT bezpośrednio.

Port MQTT (8883) jest zablokowany w wielu środowiskach sieci firmowych i edukacyjnych. Jeśli nie możesz otworzyć portu 8883 w zaporze, zalecamy użycie programu MQTT przez gniazda sieci Web. MQTT over Web Sockets komunikuje się przez port 443, który jest prawie zawsze otwarty w środowiskach sieciowych. Aby dowiedzieć się, jak określić protokoły MQTT i MQTT za pośrednictwem gniazd sieci Web podczas korzystania z zestawów SDK usługi Azure IoT, zobacz [Używanie zestawów SDK urządzeń](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Korzystanie z sdk urządzeń

[SDK urządzeń obsługujące](https://github.com/Azure/azure-iot-sdks) protokół MQTT są dostępne dla języków Java, Node.js, C, C#i Python. SDK urządzenia używają standardowego ciągu połączenia usługi IoT Hub do nawiązania połączenia z centrum IoT hub. Aby użyć protokołu MQTT, parametr protokołu klienta musi być ustawiony na **MQTT**. Można również określić MQTT za pośrednictwem gniazd sieci Web w parametrze protokołu klienta. Domyślnie zestaw SDK urządzenia łączy się z centrum IoT Hub z flagą **CleanSession** ustawioną na **0** i używa **QoS 1** do wymiany wiadomości z centrum IoT hub.

Gdy urządzenie jest podłączone do centrum IoT hub, sdk urządzeń zapewniają metody, które umożliwiają urządzeniu do wymiany wiadomości z centrum IoT hub.

Poniższa tabela zawiera łącza do przykładów kodu dla każdego obsługiwanego języka i określa parametr, który ma być używany do nawiązywanie połączenia z centrum IoT hub przy użyciu protokołu MQTT lub MQTT over Web Sockets.

| Język | Parametr protokołu MQTT | MQTT over Web Sockets parametr protokołu
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt. Mqtt ( Mqtt ) | azure-iot-device-mqtt. MqttWs (włady mqttws) |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable). MQTT ( MQTT ) | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C #](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet). Mqtt ( Mqtt ) | Jeśli mqtt nie powiedzie się, transportType.Mqtt powraca do MQTT za pośrednictwem gniazd sieci Web. Aby określić mqtt tylko za pośrednictwem gniazd sieci Web, należy użyć pliku TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Domyślnie obsługuje MQTT | Dodaj `websockets=True` wywołanie, aby utworzyć klienta |

Poniższy fragment pokazuje, jak określić protokół MQTT za pośrednictwem gniazd sieci Web podczas korzystania z sdk węzła Azure IoT.js:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Poniższy fragment pokazuje, jak określić protokół MQTT za pośrednictwem gniazd sieci Web podczas korzystania z sdk języka Azure IoT Python:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Domyślny limit czasu utrzymywana przy życiu

Aby zapewnić, że połączenie klienta/usługi IoT Hub pozostaje aktywne, zarówno usługa, jak i klient regularnie wysyłają do siebie *żądania ping przytrzymywania.* Klient korzystający z SDK IoT wysyła keep-alive w przedziale zdefiniowanym w poniższej tabeli:

|Język  |Domyślny interwał utrzymywanie aktywności przy życiu  |Możliwość konfiguracji  |
|---------|---------|---------|
|Node.js     |   180 sekund      |     Nie    |
|Java     |    230 sekund     |     Nie    |
|C     | 240 sekund |  [Tak](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 sekund |  [Tak](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python (V2)   | 60 sekund |  Nie   |

Zgodnie [ze specyfikacją MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)interwał pingu keep-alive usługi IoT Hub jest 1,5 razy większa od wartości utrzymywania przy życiu klienta. Jednak Usługa IoT Hub ogranicza maksymalny limit czasu po stronie serwera do 29,45 minuty (1767 sekund), ponieważ wszystkie usługi platformy Azure są powiązane z limitem czasu bezczynnego modułu równoważenia obciążenia platformy Azure TCP, który wynosi 29,45 minuty. 

Na przykład urządzenie korzystające z java SDK wysyła zachować przy życiu ping następnie traci łączność sieciową. 230 sekund później urządzenie nie będzie w stanie utrzymać przy życiu pingu, ponieważ jest w trybie offline. Jednak IoT Hub nie zamyka połączenia natychmiast - `(230 * 1.5) - 230 = 115` czeka kolejne sekundy przed odłączeniem urządzenia z błędem [404104 DeviceConnectionClosed.](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md) 

Maksymalna wartość utrzymania przy życiu `1767 / 1.5 = 1177` klienta, którą można ustawić, to sekundy. Każdy ruch zresetuje utrzymanie przy życiu. Na przykład pomyślne odświeżanie tokenu sygnatury dostępu Współdzielonego resetuje keep-alive.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrowanie aplikacji urządzenia z usługi AMQP do mqtt

Jeśli używasz [sdk urządzeń,](https://github.com/Azure/azure-iot-sdks)przejście z korzystania z protokołu AMQP na MQTT wymaga zmiany parametru protokołu w inicjowaniu klienta, jak wspomniano wcześniej.

W ten sposób należy sprawdzić następujące elementy:

* Funkcja AMQP zwraca błędy w wielu warunkach, podczas gdy MQTT kończy połączenie. W rezultacie logiki obsługi wyjątków może wymagać pewnych zmian.

* MQTT nie obsługuje operacji *odrzucenia* podczas odbierania [komunikatów z chmury do urządzenia](iot-hub-devguide-messaging.md). Jeśli aplikacja zaplecza musi otrzymać odpowiedź z aplikacji urządzenia, należy rozważyć użycie [metod bezpośrednich](iot-hub-devguide-direct-methods.md).

* Opcja AMQP nie jest obsługiwana w module SDK języka Python

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Bezpośrednie korzystanie z protokołu MQTT (jako urządzenia)

Jeśli urządzenie nie może używać sdk urządzeń, nadal może łączyć się z punktami końcowymi urządzenia publicznego przy użyciu protokołu MQTT na porcie 8883. W pakiecie **CONNECT** urządzenie powinno używać następujących wartości:

* W polu **Identyfikator klienta** użyj **identyfikatora deviceId**.

* W polu **Nazwa** `{iothubhostname}/{device_id}/?api-version=2018-06-30`użytkownika `{iothubhostname}` użyj , gdzie jest pełna nazwa C centrum IoT hub.

    Jeśli na przykład nazwa centrum IoT hub jest **contoso.azure-devices.net** i jeśli nazwą urządzenia jest **MyDevice01,** pełne pole **Nazwa użytkownika** powinno zawierać:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* W polu **Hasło** użyj tokenu Sygnatury dostępu Współdzielonego. Format tokenu sygnatury dostępu Współdzielonego jest taki sam, jak w przypadku protokołów HTTPS i AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Jeśli używasz uwierzytelniania certyfikatu X.509, hasła tokenów sygnatury dostępu Współdzielonego nie są wymagane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zabezpieczeń X.509 w centrum Azure IoT Hub](iot-hub-security-x509-get-started.md) i postępuj zgodnie z [poniższymi](#tlsssl-configuration)instrukcjami dotyczącymi kodu.

  Aby uzyskać więcej informacji na temat generowania tokenów sygnatury dostępu Współdzielonego, zobacz sekcję urządzenia [przy użyciu tokenów zabezpieczających Usługi IoT Hub](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Podczas testowania można również użyć wieloplatformowych [narzędzi Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub polecenia rozszerzenia interfejsu wiersza polecenia [az iot hub generate-sas-token,](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token) aby szybko wygenerować token sygnatury dostępu Współdzielonego, który można skopiować i wkleić do własnego kodu:

### <a name="for-azure-iot-tools"></a>Dla narzędzi IoT platformy Azure

1. Rozwiń kartę **URZĄDZENIA USŁUGI AZURE IOT HUB** w lewym dolnym rogu programu Visual Studio Code.
  
2. Kliknij prawym przyciskiem myszy urządzenie i wybierz pozycję **Generuj token SAS dla urządzenia**.
  
3. Ustaw **czas wygaśnięcia** i naciśnij przycisk "Enter".
  
4. Token sygnatury dostępu Współdzielonego jest tworzony i kopiowany do schowka.

   Wygenerowany token sygnatury dostępu Współdzielonego ma następującą strukturę:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Częścią tego tokenu, która ma być używana jako pole **Hasło** do łączenia się przy użyciu MQTT, jest:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

W przypadku pakietów łączących i rozłączanych MQTT usługa IoT Hub wystawia zdarzenie w kanale **Monitorowania operacji.** To zdarzenie ma dodatkowe informacje, które mogą pomóc w rozwiązywaniu problemów z łącznością.

Aplikacja urządzenia może określić komunikat **Will** w pakiecie **CONNECT.** Aplikacja urządzenia należy `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` użyć lub jako **will** nazwa tematu do definiowania **będzie** wiadomości, które mają być przekazywane jako komunikat telemetryczny. W takim przypadku jeśli połączenie sieciowe jest zamknięte, ale pakiet **DISCONNECT** nie został wcześniej odebrany z urządzenia, centrum IoT Hub wysyła komunikat **Will** dostarczony w pakiecie **CONNECT** do kanału telemetryczne. Kanał telemetrii może być domyślnym punktem końcowym **zdarzeń** lub niestandardowym punktem końcowym zdefiniowanym przez routing usługi IoT Hub. Wiadomość ma **właściwość iothub-MessageType** o wartości **will** przypisane do niego.

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>Przykład kodu C przy użyciu MQTT bez sdk C usługi Azure IoT
W tym [repozytorium](https://github.com/Azure-Samples/IoTMQTTSample)znajdziesz kilka projektów demonstracyjnych języka C/C++, pokazujących sposób wysyłania wiadomości telemetrycznych, odbieranie zdarzeń za pomocą centrum IoT bez użycia zestawu SDK C usługi Azure IoT. 

Te przykłady używają biblioteki Eclipse Mosquitto do wysyłania wiadomości do brokera MQTT zaimplementowanego w centrum IoT hub.

To repozytorium zawiera:

**Dla systemu Windows:**

* TelemetryMQTTWin32: zawiera kod do wysyłania wiadomości telemetrii do centrum Usługi Azure IoT hub, zbudowany i uruchomiony na komputerze z systemem Windows.

* Subskrybuj usługęMQTTWin32: zawiera kod do subskrybowania zdarzeń danego centrum IoT na komputerze z systemem Windows.

* DeviceTwinMQTTWin32: zawiera kod do kwerendy i subskrybować zdarzenia bliźniaczej reprezentacji urządzenia urządzenia w centrum Azure IoT hub na komputerze z systemem Windows.

* PnPMQTTWin32: zawiera kod do wysyłania wiadomości telemetrii z funkcjami urządzenia ioT Plug & Play do centrum Azure IoT hub, zbudowany i uruchomiony na komputerze z systemem Windows. Więcej o IoT Plug & Play [tutaj](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**Dla Linuksa:**

* MQTTLinux: zawiera kod i skrypt kompilacji do uruchomienia na Linuksie (WSL, Ubuntu i Raspbian zostały przetestowane do tej pory).

* LinuxConsoleVS2019: zawiera ten sam kod, ale w projekcie VS2019 kierowania WSL (Windows Linux podsystemu). Ten projekt umożliwia debugowanie kodu uruchomionego w systemie Linux krok po kroku z programu Visual Studio.

**Dla mosquitto_pub:**

Ten folder zawiera dwa przykładowe polecenia używane z mosquitto_pub narzędziem narzędziowym dostarczonym przez Mosquitto.org.

* Mosquitto_sendmessage: aby wysłać prostą wiadomość tekstową do centrum Usługi Azure IoT działając jako urządzenie.

* Mosquitto_subscribe: aby wyświetlić zdarzenia występujące w centrum Usługi Azure IoT hub.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Bezpośrednie korzystanie z protokołu MQTT (jako modułu)

Łączenie się z IoT Hub przez MQTT przy użyciu tożsamości modułu jest podobne do urządzenia (opisane [powyżej),](#using-the-mqtt-protocol-directly-as-a-device)ale należy użyć następujących czynności:

* Ustaw identyfikator klienta `{device_id}/{module_id}`na .

* Jeśli uwierzytelnianie przy użyciu nazwy użytkownika `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` i hasła, ustaw nazwę użytkownika i użyj tokenu Sygnatury dostępu Współdzielonego skojarzonego z tożsamością modułu jako hasła.

* Użyj `devices/{device_id}/modules/{module_id}/messages/events/` jako temat do publikowania danych telemetrycznych.

* Użyj `devices/{device_id}/modules/{module_id}/messages/events/` jako tematu WILL.

* Bliźniacze tematy GET i PATCH są identyczne dla modułów i urządzeń.

* Temat stanu bliźniaczej reprezentacji jest identyczny dla modułów i urządzeń.

## <a name="tlsssl-configuration"></a>Konfiguracja protokołu TLS/SSL

Aby bezpośrednio korzystać z protokołu MQTT, klient *musi połączyć* się za pomocą protokołu TLS/SSL. Próby pominięcia tego kroku nie powiodą się z błędami połączenia.

Aby ustanowić połączenie TLS, może być konieczne pobranie certyfikatu głównego DigiCert Baltimore i odwoływania się do tego. Ten certyfikat jest tym, którego platforma Azure używa do zabezpieczania połączenia. Ten certyfikat można znaleźć w repozytorium [Azure-iot-sdk-c.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) Więcej informacji na temat tych certyfikatów można znaleźć na [stronie internetowej firmy Digicert.](https://www.digicert.com/digicert-root-certificates.htm)

Przykład sposobu zaimplementowania tego przy użyciu wersji Python [biblioteki Paho MQTT](https://pypi.python.org/pypi/paho-mqtt) przez Eclipse Foundation może wyglądać następująco.

Najpierw zainstaluj bibliotekę Paho ze środowiska wiersza polecenia:

```cmd/sh
pip install paho-mqtt
```

Następnie zaimplementuj klienta w skrypcie języka Python. Zastąp symbole zastępcze w następujący sposób:

* `<local path to digicert.cer>`to ścieżka do pliku lokalnego zawierającego certyfikat główny DigiCert Baltimore. Można utworzyć ten plik, kopiując informacje o certyfikacie z [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) w `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----`usłudze `"` Azure IoT SDK dla języka `\r\n` C. Uwzględnij wiersze i , usuń znaczniki na początku i na końcu każdego wiersza i usuń znaki na końcu każdego wiersza.

* `<device id from device registry>`to identyfikator urządzenia dodanego do centrum IoT Hub.

* `<generated SAS token>`jest tokenem sygnatury dostępu Współdzielonego dla urządzenia utworzonego zgodnie z opisem opisanym wcześniej w tym artykule.

* `<iot hub name>`nazwę centrum IoT.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Aby uwierzytelnić się przy użyciu certyfikatu urządzenia, zaktualizuj powyższy fragment kodu za pomocą następujących zmian (zobacz [Jak uzyskać certyfikat urzędu certyfikacji X.509,](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) jak przygotować się do uwierzytelniania opartego na certyfikatach):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Wysyłanie komunikatów z urządzenia do chmury

Po nawiązanie pomyślnego połączenia urządzenie może wysyłać `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` wiadomości do Centrum IoT za pomocą lub jako **nazwa tematu**. Element `{property_bag}` umożliwia urządzeniu wysyłanie wiadomości z dodatkowymi właściwościami w formacie zakodowanym w adresie URL. Przykład:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ten `{property_bag}` element używa tego samego kodowania jako ciągów zapytań w protokole HTTPS.

Poniżej znajduje się lista zachowań specyficznych dla implementacji usługi IoT Hub:

* Usługa IoT Hub nie obsługuje komunikatów QoS 2. Jeśli aplikacja urządzenia publikuje komunikat z **QoS 2**, Usługa IoT Hub zamyka połączenie sieciowe.

* Centrum IoT hub nie utrzymuje zachować wiadomości. Jeśli urządzenie wysyła komunikat z flagą **RETAIN** ustawioną na 1, usługa IoT Hub dodaje do wiadomości właściwość aplikacji **x-opt-retain.** W takim przypadku zamiast utrwalania zachowywać komunikat, Usługa IoT Hub przekazuje go do aplikacji wewnętrznej bazy danych.

* Usługa IoT Hub obsługuje tylko jedno aktywne połączenie MQTT na urządzenie. Każde nowe połączenie MQTT w imieniu tego samego identyfikatora urządzenia powoduje, że centrum IoT Hub porzuca istniejące połączenie.

Aby uzyskać więcej informacji, zobacz [Przewodnik dla deweloperów wiadomości](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Odbieranie komunikatów z chmury do urządzenia

Aby odbierać wiadomości z Usługi IoT Hub, urządzenie powinno subskrybować jako `devices/{device_id}/messages/devicebound/#` filtr **tematu**. Wielopoziomowy `#` symbol wieloznaczny w filtrze tematu jest używany tylko w celu umożliwienia urządzeniu otrzymywania dodatkowych właściwości w nazwie tematu. Centrum IoT hub nie zezwala `#` `?` na używanie symboli wieloznacznych do filtrowania podtematów. Ponieważ Usługa IoT Hub nie jest brokerem obsługi wiadomości typu pub-sub ogólnego przeznaczenia, obsługuje tylko udokumentowane nazwy tematów i filtry tematów.

Urządzenie nie odbiera żadnych komunikatów z usługi IoT Hub, dopóki nie pomyślnie zasubskrybuje swój punkt końcowy specyficzny dla urządzenia, reprezentowany przez filtr tematu. `devices/{device_id}/messages/devicebound/#` Po ustanowieniu subskrypcji urządzenie odbiera komunikaty z chmury do urządzenia, które zostały do niego wysłane po zakończeniu subskrypcji. Jeśli urządzenie łączy się z **CleanSession** flagi ustawionej na **0,** subskrypcja jest zachowywana w różnych sesjach. W takim przypadku następnym razem, gdy urządzenie połączy się z **CleanSession 0,** otrzyma wszelkie zaległe wiadomości wysłane do niego po rozłączeniu. Jeśli urządzenie używa **CleanSession** flagi ustawionej na **1** jednak, nie odbiera żadnych komunikatów z Usługi IoT Hub, dopóki nie subskrybuje jego punktu końcowego urządzenia.

Usługa IoT Hub dostarcza wiadomości z `devices/{device_id}/messages/devicebound/{property_bag}` nazwą `devices/{device_id}/messages/devicebound/` **tematu** lub gdy istnieją właściwości wiadomości. `{property_bag}`zawiera pary klucza/wartości zakodowane w adresie URL. Tylko właściwości aplikacji i właściwości systemu settable użytkownika (takie jak **messageId** lub **correlationId**) są zawarte w torbie właściwości. Nazwy właściwości systemowych **$** mają prefiks, właściwości aplikacji używają oryginalnej nazwy właściwości bez prefiksu.

Gdy aplikacja urządzenia subskrybuje temat z **QoS 2,** Usługa IoT Hub przyznaje maksymalny poziom QoS 1 w pakiecie **SUBACK.** Następnie Usługa IoT Hub dostarcza wiadomości do urządzenia przy użyciu usługi QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Pobieranie właściwości bliźniaczej reprezentacji urządzenia

Po pierwsze, urządzenie subskrybuje `$iothub/twin/res/#`, aby otrzymać odpowiedzi operacji. Następnie wysyła pustą wiadomość `$iothub/twin/GET/?$rid={request id}`do tematu , z wypełnioną wartością **identyfikatora żądania**. Następnie usługa wysyła komunikat odpowiedzi zawierający dane `$iothub/twin/res/{status}/?$rid={request id}`bliźniaczej reprezentacji urządzenia na temat, przy użyciu tego samego **identyfikatora żądania** jako żądania.

Identyfikator żądania może być dowolną prawidłową wartością dla wartości właściwości wiadomości, zgodnie z [przewodnikiem dewelopera obsługi komunikatów IoT Hub,](iot-hub-devguide-messaging.md)a stan jest sprawdzany jako całkowita.

Treść odpowiedzi zawiera sekcję właściwości bliźniaczej reprezentacji urządzenia, jak pokazano w poniższym przykładzie odpowiedzi:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Możliwe kody stanu to:

|Stan | Opis |
| ----- | ----------- |
| 200 | Powodzenie |
| 429 | Zbyt wiele żądań (ograniczona), zgodnie z [ograniczaniem przepustowości usługi IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Błędy serwera |

Aby uzyskać więcej informacji, zobacz [Przewodnik dla deweloperów bliźniąt urządzeń](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Aktualizowanie zgłoszonych właściwości bliźniaczej reprezentacji urządzenia

Aby zaktualizować zgłoszone właściwości, urządzenie wystawia żądanie do usługi IoT Hub za pośrednictwem publikacji w wyznaczonym temacie MQTT. Po przetworzeniu żądania Usługa IoT Hub odpowiada na stan powodzenia lub niepowodzenia operacji aktualizacji za pośrednictwem publikacji na inny temat. Ten temat można subskrybować przez urządzenie w celu powiadomienia go o wyniku jego żądania aktualizacji bliźniaczej. Aby zaimplementować ten typ interakcji żądanie/odpowiedź w MQTT, używamy pojęcia identyfikatora żądania (`$rid`) dostarczonego początkowo przez urządzenie w żądaniu aktualizacji. Ten identyfikator żądania jest również uwzględniony w odpowiedzi z usługi IoT Hub, aby umożliwić urządzeniu skorelowanie odpowiedzi na jego określone wcześniejsze żądanie.

W poniższej sekwencji opisano, jak urządzenie aktualizuje zgłoszone właściwości w bliźniaczej reprezentacji urządzenia w UIT Hub:

1. Urządzenie musi najpierw subskrybować temat, `$iothub/twin/res/#` aby odbierać odpowiedzi operacji z usługi IoT Hub.

2. Urządzenie wysyła komunikat zawierający aktualizację bliźniaczej reprezentacji urządzenia do tematu. `$iothub/twin/PATCH/properties/reported/?$rid={request id}` Ten komunikat zawiera wartość **identyfikatora żądania.**

3. Następnie usługa wysyła komunikat odpowiedzi zawierający nową wartość ETag dla `$iothub/twin/res/{status}/?$rid={request id}`kolekcji zgłoszonych właściwości na temat . Ten komunikat odpowiedzi używa tego samego **identyfikatora żądania** co żądanie.

Treść komunikatu żądania zawiera dokument JSON, który zawiera nowe wartości dla zgłoszonych właściwości. Każdy element członkowski w dokumencie JSON aktualizuje lub dodaj odpowiedni element członkowski w dokumencie bliźniaczej reprezentacji urządzenia. Element członkowski `null`ustawiony na , usuwa element członkowski z obiektu zawierającego. Przykład:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Możliwe kody stanu to:

|Stan | Opis |
| ----- | ----------- |
| 204 | Sukces (nie jest zwracana zawartość) |
| 400 | Zła prośba. Zniekształcony JSON |
| 429 | Zbyt wiele żądań (ograniczona), zgodnie z [ograniczaniem przepustowości usługi IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Błędy serwera |

Fragment kodu języka Python poniżej, pokazuje dwa bliźniacze zgłaszane właściwości procesu aktualizacji za pośrednictwem MQTT (przy użyciu klienta Paho MQTT):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Po powodowaniu operacji aktualizacji bliźniaczej zgłoszonej właściwości powyżej komunikat o publikacji z usługi IoT Hub będzie miał następujący temat: `$iothub/twin/res/204/?$rid=1&$version=6`, gdzie `204` znajduje się kod stanu wskazujący sukces, `$rid=1` odpowiada identyfikatorowi żądania podanemu przez urządzenie w kodzie i `$version` odpowiada wersji zgłoszonej sekcji właściwości bliźniaczych reprezentacji urządzenia po aktualizacji.

Aby uzyskać więcej informacji, zobacz [Przewodnik dla deweloperów bliźniąt urządzeń](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Odbieranie powiadomień o aktualizacji żądanych właściwości

Gdy urządzenie jest podłączone, Usługa IoT `$iothub/twin/PATCH/properties/desired/?$version={new version}`Hub wysyła powiadomienia do tematu , które zawierają zawartość aktualizacji wykonywanej przez zaplecze rozwiązania. Przykład:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Jeśli chodzi o `null` aktualizacje właściwości, wartości oznaczają, że element członkowski obiektu JSON jest usuwany. Należy również `$version` zauważyć, że wskazuje nową wersję żądanej sekcji właściwości bliźniaczej reprezentacji.

> [!IMPORTANT]
> Usługa IoT Hub generuje powiadomienia o zmianach tylko wtedy, gdy urządzenia są podłączone. Upewnij się, że zaimplementowanie [przepływu ponownego połączenia urządzenia,](iot-hub-devguide-device-twins.md#device-reconnection-flow) aby zachować żądane właściwości zsynchronizowane między Centrum IoT i aplikacji urządzenia.

Aby uzyskać więcej informacji, zobacz [Przewodnik dla deweloperów bliźniąt urządzeń](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Odpowiadanie na metodę bezpośrednią

Po pierwsze, urządzenie musi subskrybować `$iothub/methods/POST/#`. Centrum IoT hub wysyła `$iothub/methods/POST/{method name}/?$rid={request id}`żądania metody do tematu, z prawidłowym JSON lub pustą treścią.

Aby odpowiedzieć, urządzenie wysyła do tematu `$iothub/methods/res/{status}/?$rid={request id}`wiadomość z prawidłowym jsonem lub pustą treścią . W tym komunikacie **identyfikator żądania** musi być zgodny z identyfikatorem w komunikacie żądania, a **stan** musi być całkowitej liczby.

Aby uzyskać więcej informacji, zobacz [Przewodnik dla deweloperów metod bezpośrednich](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

W ostatecznej kwestii, jeśli chcesz dostosować zachowanie protokołu MQTT po stronie chmury, należy przejrzeć [bramę protokołu IoT platformy Azure](iot-hub-protocol-gateway.md). To oprogramowanie umożliwia wdrożenie wysokiej wydajności bramy protokołu niestandardowego, który interfejsy bezpośrednio z IoT Hub. Brama protokołu IoT platformy Azure umożliwia dostosowanie protokołu urządzenia do potrzeb wdrożeń MQTT brownfield lub innych protokołów niestandardowych. Takie podejście wymaga jednak uruchomienia i obsługi bramy protokołu niestandardowego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o protokole MQTT, zapoznaj się z [dokumentacją MQTT](https://mqtt.org/documentation).

Aby dowiedzieć się więcej o planowaniu wdrożenia usługi IoT Hub, zobacz:

* [Certyfikat platformy Microsoft Azure dla IoT — wykaz urządzeń](https://catalog.azureiotsolutions.com/)
* [Obsługa dodatkowych protokołów](iot-hub-protocol-gateway.md)
* [Porównaj z Centrami zdarzeń](iot-hub-compare-event-hubs.md)
* [Skalowanie, wysokiej ha i odzyskiwania po awarii](iot-hub-scaling.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
