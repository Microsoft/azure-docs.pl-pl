---
title: Informacje o pomocy technicznej dotyczącej usługi Azure IoT Hub MQTT | Microsoft Docs
description: Obsługa urządzeń łączących się z IoT Hub punktem końcowym dostępnym na urządzeniu przy użyciu protokołu MQTT. Zawiera informacje o wbudowanej obsłudze MQTT w zestawach SDK urządzeń Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
- contperf-fy21q1
- fasttrack-edit
- iot
ms.openlocfilehash: 9678648b6417138e216ba2dce3a3605bb4c1bce4
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169236"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikacja z Centrum IoT Hub przy użyciu protokołu MQTT

IoT Hub umożliwia urządzeniom komunikowanie się z punktami końcowymi IoT Hub Device przy użyciu:

* [MQTT v 3.1.1](https://mqtt.org/) na porcie 8883
* MQTT v 3.1.1 przy użyciu protokołu WebSocket na porcie 443.

Usługa IoT Hub nie jest w pełni funkcjonalnym brokerem MQTT i nie obsługuje wszystkich zachowań określonych w standardzie MQTT 3.1.1. W tym artykule opisano sposób, w jaki urządzenia mogą używać obsługiwanych zachowań MQTT do komunikowania się z IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Cała komunikacja urządzeń z IoT Hub musi być zabezpieczona przy użyciu protokołu TLS/SSL. W związku z tym IoT Hub nie obsługuje połączeń niezabezpieczonych przez port 1883.

## <a name="connecting-to-iot-hub"></a>Nawiązywanie połączenia z IoT Hub

Urządzenie może korzystać z protokołu MQTT do nawiązywania połączenia z usługą IoT Hub przy użyciu dowolnej z poniższych opcji.

* Biblioteki w zestawach [SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdks).
* Protokół MQTT bezpośrednio.

Port MQTT (8883) jest blokowany w wielu firmowych środowiskach sieciowych i edukacyjnych. Jeśli nie możesz otworzyć portu 8883 w zaporze, zalecamy użycie MQTT w sieci Web Sockets. MQTT przez sieć Web Sockets komunikuje się przez port 443, który jest niemal zawsze otwarty w środowiskach sieciowych. Aby dowiedzieć się, jak określić MQTT i MQTT za pośrednictwem protokołów sieci Web Sockets w przypadku korzystania z zestawów SDK usługi Azure IoT, zobacz [Korzystanie z zestawów SDK urządzeń](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Korzystanie z zestawów SDK urządzeń

[Zestawy SDK urządzeń](https://github.com/Azure/azure-iot-sdks) obsługujące protokół MQTT są dostępne dla języków Java, Node.js, C, C# i Python. Zestawy SDK urządzeń używają standardowego ciągu połączenia IoT Hub, aby nawiązać połączenie z usługą IoT Hub. Aby można było korzystać z protokołu MQTT, parametr protokołu klienta musi być ustawiony na **MQTT**. W parametrze protokołu klienta można także określić MQTT przez gniazda sieci Web. Domyślnie zestawy SDK urządzenia łączą się z IoT Hubą z flagą **CleanSession** ustawioną na **0** i używają **usługi QoS 1** na potrzeby wymiany komunikatów z usługą IoT Hub. Chociaż istnieje możliwość skonfigurowania **usługi QoS 0** w celu przyspieszenia wymiany komunikatów, należy pamiętać, że dostarczanie nie jest gwarantowane ani potwierdzone. Z tego powodu jakość usług ( **QoS) 0** jest często określana jako "Fire i zapomnij".

Gdy urządzenie jest połączone z usługą IoT Hub, zestawy SDK urządzeń udostępniają metody, które umożliwiają urządzeniu wymianę komunikatów z Centrum IoT Hub.

Poniższa tabela zawiera linki do przykładów kodu dla każdego obsługiwanego języka i określa parametr używany do nawiązywania połączenia do IoT Hub przy użyciu MQTT lub MQTT za pośrednictwem protokołu Web Sockets.

| Język | Parametr protokołu MQTT | MQTT przez parametr protokołu sieci Web Sockets
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | Azure-IoT-Device-MQTT. MQTT | Azure-IoT-Device-MQTT. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol). MQTT | IotHubClientProtocol.MQTT_WS |
| [S](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](/dotnet/api/microsoft.azure.devices.client.transporttype). MQTT | TransportType. MQTT powraca do MQTT za pośrednictwem gniazd sieci Web, jeśli MQTT się nie powiedzie. Aby określić MQTT tylko dla gniazd sieci Web, użyj TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Domyślnie obsługuje MQTT | Dodaj `websockets=True` w wywołaniu, aby utworzyć klienta |

Poniższy fragment przedstawia sposób określania MQTT przez protokół Web Sockets w przypadku korzystania z zestawu SDK usługi Azure IoT Node.js:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Poniższy fragment przedstawia sposób określania MQTT przez protokół Web Sockets w przypadku korzystania z zestawu SDK języka Python usługi Azure IoT:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Domyślny limit czasu utrzymywania aktywności

W celu zapewnienia, że połączenie klienta/IoT Hub będzie aktywne, zarówno usługa, jak i klient regularnie wysyłają polecenie ping *Keep-Alive* do siebie nawzajem. Klient korzystający z zestawu IoT SDK wysyła wartość Keep-Alive w interwale zdefiniowanym w poniższej tabeli:

|Język  |Domyślny interwał utrzymywania aktywności  |Konfigurowalne  |
|---------|---------|---------|
|Node.js     |   180 sekund      |     Nie    |
|Java     |    230 sekund     |     Nie    |
|C     | 240 sekund |  [Tak](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 sekund |  [Tak](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 sekund |  Nie   |

Zgodnie ze [specyfikacją MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)interwał polecenia ping IoT Hub jest 1,5 razy wartość Keep-Alive klienta. IoT Hub ograniczenie maksymalnego limitu czasu po stronie serwera do 29,45 minut (1767 sekund), ponieważ wszystkie usługi platformy Azure są powiązane z limitem czasu bezczynności protokołu TCP modułu równoważenia obciążenia platformy Azure, który jest 29,45 minuty. 

Na przykład urządzenie korzystające z zestawu SDK języka Java wysyła polecenie ping Keep-Alive, a następnie utraci łączność sieciową. 230 s później, urządzenie trafi polecenie ping Keep-Alive, ponieważ jest w trybie offline. Niemniej jednak IoT Hub nie zamyka natychmiast połączenia — czeka na inne `(230 * 1.5) - 230 = 115` sekundy przed rozłączeniem urządzenia z błędem [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

Maksymalna wartość utrzymywania aktywności klienta, którą można ustawić, to `1767 / 1.5 = 1177` s. Każdy ruch zostanie zresetowany do utrzymania aktywności. Na przykład pomyślne odświeżenie tokenu SAS resetuje wartość Keep-Alive.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrowanie aplikacji urządzenia z usługi AMQP do usługi MQTT

W przypadku korzystania z [zestawów SDK urządzeń](https://github.com/Azure/azure-iot-sdks)przełączanie z używania AMQP do MQTT wymaga zmiany parametru protokołu w inicjacji klienta, jak wspomniano wcześniej.

W tym celu należy sprawdzić następujące elementy:

* AMQP zwraca błędy dla wielu warunków, podczas gdy MQTT przerywa połączenie. W efekcie logika obsługi wyjątków może wymagać pewnych zmian.

* MQTT nie obsługuje operacji *odrzucania* podczas otrzymywania [komunikatów z chmury do urządzenia](iot-hub-devguide-messaging.md). Jeśli Twoja aplikacja zaplecza musi odebrać odpowiedź z aplikacji urządzenia, należy rozważyć użycie [metod bezpośrednich](iot-hub-devguide-direct-methods.md).

* AMQP nie jest obsługiwana w zestawie SDK języka Python.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Przykład w języku C przy użyciu MQTT bez zestawu SDK usługi Azure IoT

W [przykładowym repozytorium IoT MQTT](https://github.com/Azure-Samples/IoTMQTTSample)znajdziesz kilka projektów demonstracyjnych C/C++, które pokazują, jak wysyłać komunikaty telemetryczne i odbierać zdarzenia z Centrum IoT bez używania zestawu Azure IoT C SDK. 

Te przykłady umożliwiają wysyłanie komunikatów do brokera MQTT zaimplementowane w usłudze IoT Hub przy użyciu biblioteki Mosquitto.

Aby dowiedzieć się, jak dostosować przykłady do korzystania z Konwencji [usługi Azure IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) , zobacz [samouczek — Użyj MQTT do opracowania klienta urządzenia IoT Plug and Play](../iot-pnp/tutorial-use-mqtt.md).

To repozytorium zawiera:

**Dla systemu Windows:**

* TelemetryMQTTWin32: zawiera kod służący do wysyłania komunikatów telemetrycznych do usługi Azure IoT Hub, zbudowanych i uruchamianych na komputerze z systemem Windows.

* SubscribeMQTTWin32: zawiera kod, który subskrybuje zdarzenia danego centrum IoT Hub na komputerze z systemem Windows.

* DeviceTwinMQTTWin32: zawiera kod do zapytania i subskrybuje zdarzenia dotyczące sznurka urządzenia urządzenia w usłudze Azure IoT Hub na komputerze z systemem Windows.

* PnPMQTTWin32: zawiera kod służący do wysyłania komunikatów telemetrycznych z możliwościami urządzenia Plug and Play IoT do usługi Azure IoT Hub, zbudowanych i uruchamianych na komputerze z systemem Windows. Więcej informacji można znaleźć na [Plug and Play IoT](../iot-pnp/overview-iot-plug-and-play.md)

**W przypadku systemu Linux:**

* MQTTLinux: zawiera kod i skrypt kompilacji do uruchomienia w systemie Linux (WSL, Ubuntu i raspbian zostały dotąd przetestowane).

* LinuxConsoleVS2019: zawiera ten sam kod, ale w projekcie VS2019 przeznaczonym dla WSL (podsystem systemu Windows Linux). Ten projekt umożliwia debugowanie kodu uruchomionego w systemie Linux krok po kroku z programu Visual Studio.

**Dla mosquitto_pub:**

Ten folder zawiera dwa przykłady poleceń używanych z narzędziem narzędzi mosquitto_pub udostępnionym przez Mosquitto.org.

* Mosquitto_sendmessage: aby wysłać prostą wiadomość tekstową do usługi Azure IoT Hub działającej jako urządzenie.

* Mosquitto_subscribe: Aby wyświetlić zdarzenia występujące w usłudze Azure IoT Hub.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Bezpośrednie używanie protokołu MQTT (jako urządzenia)

Jeśli urządzenie nie może użyć zestawów SDK urządzeń, nadal może nawiązać połączenie z punktami końcowymi urządzeń publicznych przy użyciu protokołu MQTT na porcie 8883. W pakiecie **Connect** urządzenie powinno używać następujących wartości:

* Dla pola **ClientId** Użyj elementu **deviceId**.

* W polu **username (nazwa użytkownika** ) Użyj elementu `{iothubhostname}/{device_id}/?api-version=2018-06-30` , gdzie `{iothubhostname}` to pełny rekord CNAME Centrum IoT Hub.

    Jeśli na przykład nazwa Twojego centrum IoT jest **contoso.Azure-Devices.NET** i jeśli nazwa urządzenia to **MyDevice01**, pełne pole **nazwy użytkownika** powinno zawierać:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

    Zdecydowanie zaleca się dołączenie w polu interfejsu API-Version. W przeciwnym razie może to spowodować nieoczekiwane zachowanie. 
    
* W polu **hasło** Użyj tokenu SAS. Format tokenu sygnatury dostępu współdzielonego jest taki sam jak dla protokołów HTTPS i AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Jeśli używasz uwierzytelniania certyfikatu X. 509, hasła tokenu sygnatury dostępu współdzielonego nie są wymagane. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](iot-hub-security-x509-get-started.md) i postępuj zgodnie z instrukcjami kodu w [sekcji Konfiguracja protokołu TLS/SSL](#tlsssl-configuration).

  Więcej informacji o sposobach generowania tokenów SAS znajduje się w sekcji Device of [Using IoT Hub tokeny zabezpieczające](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Podczas testowania można także użyć wieloplatformowych [narzędzi Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) lub rozszerzenia interfejsu wiersza polecenia [AZ IoT Hub Generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token) , aby szybko wygenerować token sygnatury dostępu współdzielonego, który można skopiować i wkleić do własnego kodu.

### <a name="for-azure-iot-tools"></a>Dla narzędzi Azure IoT Tools

1. Rozwiń kartę **urządzenia usługi Azure IoT Hub** w lewym dolnym rogu Visual Studio Code.
  
2. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Generuj token SAS dla urządzenia**.
  
3. Ustaw **czas wygaśnięcia** i naciśnij klawisz ENTER.
  
4. Token sygnatury dostępu współdzielonego został utworzony i skopiowany do Schowka.

   Wygenerowany token sygnatury dostępu współdzielonego ma następującą strukturę:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Częścią tego tokenu używaną jako pole **hasła** do nawiązywania połączenia przy użyciu MQTT jest:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

W przypadku pakietów MQTT Connect i Disconnect IoT Hub wystawić zdarzenie w kanale **monitorowania operacji** . To zdarzenie zawiera dodatkowe informacje, które mogą pomóc w rozwiązywaniu problemów z łącznością.

Aplikacja urządzenia może **określić komunikat w** pakiecie **Connect** . Aplikacja urządzenia powinna używać `devices/{device_id}/messages/events/` lub `devices/{device_id}/messages/events/{property_bag}` jako nazwa tematu  , aby określić, czy  komunikaty będą przekazywane jako komunikat telemetrii. W takim przypadku, jeśli połączenie sieciowe zostało zamknięte, ale pakiet **rozłączenia** nie został wcześniej odebrany z urządzenia, IoT Hub wyśle komunikat **o podanej** w pakiecie **Connect** do kanału telemetrii. Kanał telemetrii może być domyślnym punktem końcowym **zdarzeń** lub niestandardowym punktem końcowym zdefiniowanym przez IoT Hub Routing. Komunikat ma właściwość **iothub-MessageType** o wartości, która **zostanie** do niej przypisana.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Bezpośrednie używanie protokołu MQTT (jako modułu)

Nawiązywanie połączenia z IoT Hub za pośrednictwem usługi MQTT za pomocą tożsamości modułu jest podobne do urządzenia (opisane [w sekcji dotyczącej używania protokołu MQTT bezpośrednio jako urządzenia](#using-the-mqtt-protocol-directly-as-a-device)), ale należy użyć następujących elementów:

* Ustaw identyfikator klienta na `{device_id}/{module_id}` .

* W przypadku uwierzytelniania przy użyciu nazwy użytkownika i hasła Ustaw nazwę użytkownika na `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` i użyj tokenu sygnatury dostępu współdzielonego skojarzonego z tożsamością modułu jako hasłem.

* Użyj `devices/{device_id}/modules/{module_id}/messages/events/` jako tematu do publikowania danych telemetrycznych.

* Użycie `devices/{device_id}/modules/{module_id}/messages/events/` jako będzie tematem.

* Tematy dotyczące pobierania i stosowania przędzy są identyczne w przypadku modułów i urządzeń.

* Temat o stanie przędzy jest identyczny w przypadku modułów i urządzeń.

## <a name="tlsssl-configuration"></a>Konfiguracja protokołu TLS/SSL

Aby bezpośrednio korzystać z protokołu MQTT, klient *musi* nawiązać połączenie za pośrednictwem protokołu TLS/SSL. Próba pominięcia tego kroku kończy się niepowodzeniem z błędami połączenia.

Aby można było nawiązać połączenie TLS, może być konieczne pobranie i odwołanie certyfikatu głównego DigiCert Baltimore. Ten certyfikat jest wykorzystywany przez platformę Azure do zabezpieczenia połączenia. Ten certyfikat można znaleźć w repozytorium [Azure-IoT-SDK-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) . Więcej informacji o tych certyfikatach można znaleźć w [witrynie sieci Web DigiCert](https://www.digicert.com/digicert-root-certificates.htm).

Przykładem sposobu implementacji tego przy użyciu wersji języka Python [biblioteki PAHO MQTT](https://pypi.python.org/pypi/paho-mqtt) przez przeszukiwanie podstawy może wyglądać podobnie do poniższego.

Najpierw zainstaluj bibliotekę PAHO z poziomu środowiska wiersza polecenia:

```cmd/sh
pip install paho-mqtt
```

Następnie Zaimplementuj klienta w skrypcie języka Python. Zastąp symbole zastępcze w następujący sposób:

* `<local path to digicert.cer>` jest ścieżką do pliku lokalnego, który zawiera certyfikat główny DigiCert Baltimore. Ten plik można utworzyć, kopiując informacje o certyfikacie z [certyfikatów. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) w zestawie SDK usługi Azure IoT dla języka c. Dołącz wiersze `-----BEGIN CERTIFICATE-----` i `-----END CERTIFICATE-----` , Usuń `"` znaczniki na początku i końcu każdego wiersza, a następnie usuń `\r\n` znaki na końcu każdego wiersza.

* `<device id from device registry>` to identyfikator urządzenia dodanego do centrum IoT Hub.

* `<generated SAS token>` jest tokenem sygnatury dostępu współdzielonego dla urządzenia utworzonego zgodnie z opisem w tym artykule.

* `<iot hub name>` Nazwa Centrum IoT.

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

Aby uwierzytelnić się przy użyciu certyfikatu urządzenia, zaktualizuj Powyższy fragment kodu z następującymi zmianami (Zobacz artykuł [jak uzyskać certyfikat certyfikatu X. 509 urzędu certyfikacji](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) , aby przygotować się do uwierzytelniania opartego na certyfikatach):

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

Po pomyślnym nawiązaniu połączenia urządzenie może wysyłać komunikaty do IoT Hub przy użyciu `devices/{device_id}/messages/events/` lub `devices/{device_id}/messages/events/{property_bag}` jako **nazwy tematu**. `{property_bag}`Element umożliwia urządzeniu wysyłanie komunikatów z dodatkowymi właściwościami w formacie zakodowanym w adresie URL. Na przykład:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ten `{property_bag}` element używa tego samego kodowania co ciągi zapytań w protokole https.

Poniżej znajduje się lista zachowań IoT Hub specyficznych dla implementacji:

* IoT Hub nie obsługuje komunikatów QoS 2. Jeśli aplikacja urządzenia opublikuje komunikat z opcją **QoS 2**, IoT Hub zamyka połączenie sieciowe.

* IoT Hub nie utrwala zachowywanych komunikatów. Jeśli urządzenie wysyła komunikat z flagą **Zachowaj** ustawioną na 1, IoT Hub dodaje do wiadomości Właściwość **MQTT-utrzymują** aplikację. W takim przypadku zamiast utrwalania komunikatu o zatrzymaniu IoT Hub przekazuje go do aplikacji zaplecza.

* IoT Hub obsługuje tylko jedno aktywne połączenie MQTT dla każdego urządzenia. Każde nowe połączenie MQTT w imieniu tego samego identyfikatora urządzenia powoduje, że IoT Hub porzucić istniejące połączenie, a **400027 ConnectionForcefullyClosedOnNewConnection** zostanie zalogowany do dzienników IoT Hub


Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera obsługi komunikatów](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Otrzymywanie komunikatów z chmury do urządzenia

Aby odbierać komunikaty z IoT Hub, urządzenie powinno subskrybować korzystanie z programu `devices/{device_id}/messages/devicebound/#` jako **filtru tematu**. Wielopoziomowy symbol wielowymiarowy `#` w filtrze tematu służy tylko do zezwalania urządzeniu na odbieranie dodatkowych właściwości w nazwie tematu. IoT Hub nie zezwala na użycie `#` `?` symboli wieloznacznych lub do filtrowania tematów podrzędnych. Ponieważ IoT Hub nie jest brokerem wysyłania komunikatów ogólnego przeznaczenia, obsługuje tylko udokumentowane nazwy tematów i filtry tematów.

Urządzenie nie odbiera żadnych komunikatów z IoT Hub do momentu pomyślnego zasubskrybowania jego punktu końcowego określonego dla urządzenia reprezentowanego przez `devices/{device_id}/messages/devicebound/#` Filtr tematu. Po nawiązaniu subskrypcji urządzenie odbiera wysłane do niego komunikaty z chmury do urządzenia po upływie czasu subskrypcji. Jeśli urządzenie połączy się z flagą **CleanSession** o wartości **0**, subskrypcja jest utrwalana w różnych sesjach. W takim przypadku następnym razem, gdy urządzenie połączy się z **CleanSession 0** odbierze wszelkie zaległe komunikaty wysyłane do niego, gdy zostanie odłączony. Jeśli urządzenie używa flagi **CleanSession** ustawionej na **1** , nie odbiera żadnych komunikatów z IoT Hub, dopóki nie zasubskrybuje on swojego punktu końcowego urządzenia.

IoT Hub dostarcza wiadomości z **nazwą tematu** `devices/{device_id}/messages/devicebound/` lub w `devices/{device_id}/messages/devicebound/{property_bag}` przypadku, gdy są właściwości komunikatów. `{property_bag}` zawiera pary kluczy/wartości kodowane w adresie URL właściwości komunikatów. W zbiorze właściwości są uwzględniane tylko właściwości aplikacji i właściwości systemu użytkownika (takie jak **MessageID** lub **Identyfikator korelacji**). Nazwy właściwości systemu mają prefiks **$** , właściwości aplikacji używają oryginalnej nazwy właściwości bez prefiksu. Aby uzyskać dodatkowe informacje na temat formatu zbioru właściwości, zobacz [wysyłanie komunikatów z urządzenia do chmury](#sending-device-to-cloud-messages).

W komunikatach z chmury do urządzenia wartości w zbiorze właściwości są reprezentowane jak w poniższej tabeli:

| Wartość właściwości | Reprezentowana | Opis |
|----|----|----|
| `null` | `key` | Tylko klucz pojawia się w zbiorze właściwości |
| pusty ciąg | `key=` | Klucz, po którym następuje znak równości bez wartości |
| wartość inna niż null, niepusta | `key=value` | Klucz, po którym następuje znak równości i wartość |

Poniższy przykład przedstawia zbiór właściwości, który zawiera trzy właściwości aplikacji: **Prop1** z wartością `null` ; **prop2**, pusty ciąg (""); i **prop3** z wartością "ciągu".

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Gdy aplikacja urządzenia subskrybuje temat z zasadą **QoS 2**, IoT Hub przyznaje maksymalny poziom jakości usług (QoS) 1 w pakiecie **SUBACK** . Następnie IoT Hub dostarcza komunikaty do urządzenia przy użyciu usługi QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Pobieranie właściwości sznurka urządzenia

Najpierw urządzenie subskrybuje `$iothub/twin/res/#` , aby odebrać odpowiedzi operacji. Następnie wysyła pusty komunikat do tematu `$iothub/twin/GET/?$rid={request id}` z wypełnioną wartością dla **identyfikatora żądania**. Następnie usługa wysyła komunikat odpowiedzi zawierający dane dotyczące sznurka urządzenia w temacie `$iothub/twin/res/{status}/?$rid={request id}` przy użyciu tego samego **identyfikatora żądania** co żądanie.

Identyfikator żądania może być dowolną prawidłową wartością dla wartości właściwości wiadomości, zgodnie z opisem w [przewodniku dewelopera obsługi komunikatów IoT Hub](iot-hub-devguide-messaging.md), a stan jest sprawdzony jako liczba całkowita.

Treść odpowiedzi zawiera sekcję właściwości sznurka urządzenia, jak pokazano w poniższym przykładzie odpowiedzi:

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
| 429 | Zbyt wiele żądań (z ograniczeniami), zgodnie z [ograniczeniami IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5 * * | Błędy serwera |

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Aktualizowanie raportowanych właściwości przędzy urządzenia

Aby zaktualizować raportowane właściwości, urządzenie wysyła żądanie IoT Hub za pośrednictwem publikacji na wyznaczoną MQTT tematu. Po przetworzeniu żądania IoT Hub odpowiada na stan powodzenia lub niepowodzenia operacji aktualizacji za pośrednictwem publikacji w innym temacie. Ten temat może być subskrybowany przez urządzenie w celu powiadomienia go o wyniku żądania aktualizacji z przędzą. W celu zaimplementowania tego typu interakcji żądania/odpowiedzi w programie MQTT wykorzystujemy pojęcie identyfikatora żądania ( `$rid` ) dostarczonego początkowo przez urządzenie w jego żądaniu aktualizacji. Ten identyfikator żądania jest również uwzględniony w odpowiedzi z IoT Hub, aby umożliwić urządzeniu skorelowanie odpowiedzi z konkretnym wcześniejszym żądaniem.

Poniższa sekwencja zawiera opis sposobu aktualizowania przez urządzenie raportowanych właściwości w postaci sznurka urządzenia w IoT Hub:

1. Urządzenie musi najpierw subskrybować `$iothub/twin/res/#` temat, aby otrzymywać odpowiedzi operacji z IoT Hub.

2. Urządzenie wysyła komunikat z aktualizacją przędzy urządzenia do `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tematu. Ten komunikat zawiera wartość **identyfikatora żądania** .

3. Następnie usługa wysyła komunikat odpowiedzi zawierający nową wartość ETag dla raportowanych kolekcji właściwości w temacie `$iothub/twin/res/{status}/?$rid={request id}` . Ten komunikat odpowiedzi używa tego samego **identyfikatora żądania** co żądanie.

Treść komunikatu żądania zawiera dokument JSON, który zawiera nowe wartości raportowanych właściwości. Każdy element członkowski w dokumencie JSON aktualizuje lub dodaje odpowiadający mu element członkowski w dokumencie przędzy urządzenia. Zestaw elementów członkowskich, aby `null` usunąć element członkowski z zawierającego go obiektu. Na przykład:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Możliwe kody stanu to:

|Stan | Opis |
| ----- | ----------- |
| 204 | Powodzenie (nie jest zwracana żadna zawartość) |
| 400 | Nieprawidłowe żądanie. Źle sformułowany kod JSON |
| 429 | Zbyt wiele żądań (z ograniczeniami), zgodnie z [ograniczeniami IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5 * * | Błędy serwera |

Poniższy fragment kodu w języku Python pokazuje, że raport o raportowanych właściwościach jest przetwarzany przez MQTT (przy użyciu PAHO MQTT Client):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Po pomyślnym przeprowadzeniu operacji aktualizacji właściwości w postaci dwuosiowej komunikat dotyczący publikacji z IoT Hub będzie miał następujący temat: `$iothub/twin/res/204/?$rid=1&$version=6` , gdzie `204` jest kodem stanu wskazującym powodzenie, `$rid=1` odpowiada identyfikatorowi żądania dostarczonemu przez urządzenie w kodzie i `$version` odpowiada wersji zgłaszanej właściwości sekcji bliźniaczych reprezentacji urządzenia po aktualizacji.

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Otrzymywanie powiadomień o aktualizacji żądanych właściwości

Gdy urządzenie jest połączone, IoT Hub wysyła powiadomienia do tematu `$iothub/twin/PATCH/properties/desired/?$version={new version}` , który zawiera zawartość aktualizacji wykonywanej przez zaplecze rozwiązania. Na przykład:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Podobnie jak w przypadku aktualizacji właściwości, `null` wartość oznacza, że element członkowski obiektu JSON jest usuwany. Należy również pamiętać, że `$version` wskazuje nową wersję odpowiedniej sekcji Właściwości sznurka.

> [!IMPORTANT]
> IoT Hub generuje powiadomienia o zmianach tylko wtedy, gdy urządzenia są połączone. Upewnij się, że wdrożono [przepływ ponownego połączenia urządzenia](iot-hub-devguide-device-twins.md#device-reconnection-flow) , aby zachować odpowiednie właściwości zsynchronizowane między IoT Hub a aplikacją urządzenia.

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Odpowiedz na metodę bezpośrednią

Najpierw należy zasubskrybować urządzenie `$iothub/methods/POST/#` . IoT Hub wysyła żądania metod do tematu `$iothub/methods/POST/{method name}/?$rid={request id}` z prawidłowym elementem JSON lub pustą treścią.

Aby można było odpowiedzieć, urządzenie wysyła do tematu komunikat z prawidłowym kodem JSON lub pustą treścią `$iothub/methods/res/{status}/?$rid={request id}` . W tym komunikacie **Identyfikator żądania** musi być zgodny z jednym z komunikatów żądania, a **stan** musi być liczbą całkowitą.

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera metody bezpośredniej](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

W związku z tym jeśli trzeba będzie dostosować zachowanie protokołu MQTT po stronie chmury, należy zapoznać się z [bramą protokołu Azure IoT](iot-hub-protocol-gateway.md). To oprogramowanie umożliwia wdrożenie niestandardowej bramy protokołu o wysokiej wydajności, która jest bezpośrednio oparta na IoT Hub. Brama protokołu Azure IoT umożliwia dostosowanie protokołu urządzenia w celu uwzględnienia wdrożeń brownfield MQTT lub innych niestandardowych protokołów. To podejście wymaga jednak uruchomienia i obsługi niestandardowej bramy protokołu.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat protokołu MQTT można znaleźć w [dokumentacji MQTT](https://mqtt.org/).

Aby dowiedzieć się więcej o planowaniu wdrożenia IoT Hub, zobacz:

* [Certyfikat platformy Microsoft Azure dla IoT — wykaz urządzeń](https://devicecatalog.azure.com/)
* [Obsługa dodatkowych protokołów](iot-hub-protocol-gateway.md)
* [Porównaj z Event Hubs](iot-hub-compare-event-hubs.md)
* [Skalowanie, HA i DR](iot-hub-scaling.md)

Aby dowiedzieć się więcej o możliwościach IoT Hub, zobacz:

* [Przewodnik dla deweloperów IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)
