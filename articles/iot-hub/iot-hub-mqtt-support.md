---
title: Opis Azure IoT Hub obsługi | MQTT Microsoft Docs
description: Obsługa urządzeń łączących się z IoT Hub punktu końcowego połączonego z urządzeniem przy użyciu protokołu MQTT. Zawiera informacje na temat wbudowanej obsługi MQTT w zestawach SDK urządzeń Azure IoT.
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
ms.openlocfilehash: 077b4ea6c7d4506e29673cbb4d6d89a92657b9c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873689"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikacja z centrum IoT przy użyciu protokołu MQTT

IoT Hub urządzenia mogą komunikować się z punktami końcowymi IoT Hub przy użyciu:

* [MQTT 3.1.1 na](https://mqtt.org/) porcie 8883
* MQTT v3.1.1 przez WebSocket na porcie 443.

Usługa IoT Hub nie jest w pełni funkcjonalnym brokerem MQTT i nie obsługuje wszystkich zachowań określonych w standardzie MQTT 3.1.1. W tym artykule opisano, jak urządzenia mogą używać obsługiwanych zachowań MQTT do komunikowania się z IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Cała komunikacja urządzenia z IoT Hub musi być zabezpieczona przy użyciu protokołu TLS/SSL. W związku IoT Hub nie obsługuje niezabezpieczonych połączeń przez port 1883.

## <a name="connecting-to-iot-hub"></a>Nawiązywanie połączenia z IoT Hub

Urządzenie może używać protokołu MQTT do nawiązywania połączenia z centrum IoT przy użyciu dowolnej z poniższych opcji.

* Biblioteki w [zestawach SDK usługi Azure IoT.](https://github.com/Azure/azure-iot-sdks)
* Protokół MQTT bezpośrednio.

Port MQTT (8883) jest zablokowany w wielu firmowych i edukacyjnych środowiskach sieciowych. Jeśli nie możesz otworzyć portu 8883 w zaporze, zalecamy użycie MQTT za pośrednictwem gniazd sieci Web. MQTT za pośrednictwem gniazd sieci Web komunikuje się za pośrednictwem portu 443, który jest prawie zawsze otwarty w środowiskach sieciowych. Aby dowiedzieć się, jak określać protokoły MQTT i MQTT za pośrednictwem protokołów Web Sockets podczas korzystania z zestawów SDK usługi Azure IoT, zobacz [Używanie zestawów SDK urządzeń.](#using-the-device-sdks)

## <a name="using-the-device-sdks"></a>Korzystanie z zestawów SDK urządzeń

[Zestawy SDK urządzeń,](https://github.com/Azure/azure-iot-sdks) które obsługują protokół MQTT, są dostępne dla języków Java, Node.js, C, C# i Python. Zestawy SDK urządzeń używają standardowych IoT Hub parametrów połączenia w celu nawiązania połączenia z centrum IoT. Aby można było korzystać z protokołu MQTT, parametr protokołu klienta musi być ustawiony na **MQTT.** W parametrze protokołu klienta można również określić protokół MQTT za pośrednictwem gniazd sieci Web. Domyślnie zestawy SDK urządzeń łączą się z usługą IoT Hub z flagą **CleanSession** ustawioną na **0** i używają **QoS 1** do wymiany komunikatów z centrum IoT. Chociaż jest możliwe skonfigurowanie **QoS 0** w celu szybszej wymiany komunikatów, należy pamiętać, że dostarczanie nie jest gwarantowane ani potwierdzane. Z tego powodu **QoS 0** jest często określany jako "pożar i zapomnienie".

Gdy urządzenie jest połączone z centrum IoT Hub, zestawy SDK urządzeń zapewniają metody umożliwiające urządzeniu wymianę komunikatów z centrum IoT.

W poniższej tabeli przedstawiono linki do przykładów kodu dla każdego obsługiwanego języka i określono parametr używany do nawiązywania połączenia z usługą IoT Hub przy użyciu protokołu MQTT lub MQTT za pośrednictwem protokołu Web Sockets.

| Język | Parametr protokołu MQTT | Parametr protokołu MQTT za pośrednictwem usługi Web Sockets
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt. Mqtt | azure-iot-device-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol). MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](/dotnet/api/microsoft.azure.devices.client.transporttype). Mqtt | TransportType.Mqtt powraca do MQTT za pośrednictwem gniazd sieci Web, jeśli MQTT ulegnie awarii. Aby określić MQTT tylko za pośrednictwem gniazd sieci Web, użyj TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Domyślnie obsługuje MQTT | Dodaj `websockets=True` w wywołaniu , aby utworzyć klienta |

Poniższy fragment pokazuje, jak określić protokół MQTT za pośrednictwem protokołu Web Sockets podczas korzystania z zestawu SDK usługi Azure IoT Node.js SDK:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Poniższy fragment przedstawia sposób określania protokołu MQTT za pośrednictwem gniazd sieci Web podczas korzystania z zestawu SDK języka Python usługi Azure IoT:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Domyślny limit czasu podtrzymania aktywności

Aby upewnić się, że połączenie klienta/IoT Hub pozostaje aktywne, zarówno usługa, jak i klient regularnie wysyłają do siebie polecenie *ping* podtrzymania aktywności. Klient korzystający z zestawu SDK IoT wysyła podtrzymanie aktywności w interwale zdefiniowanym w poniższej tabeli:

|Język  |Domyślny interwał podtrzymania aktywności  |Konfigurowalne  |
|---------|---------|---------|
|Node.js     |   180 sekund      |     Nie    |
|Java     |    230 sekund     |     Nie    |
|C     | 240 sekund |  [Tak](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 sekund |  [Tak](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 sekund |  Nie   |

Zgodnie ze [specyfikacją MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)IoT Hub polecenia ping keep-alive jest 1,5 raza więcej niż wartość klienta keep-alive. Jednak usługa IoT Hub ogranicza maksymalny limit czasu po stronie serwera do 29,45 minuty (1767 sekund), ponieważ wszystkie usługi platformy Azure są powiązane z limitem czasu bezczynności protokołu TCP usługi azure load balancer, czyli 29,45 min. 

Na przykład urządzenie korzystające z zestawu JAVA SDK wysyła polecenie ping keep-alive, a następnie traci łączność sieciową. 230 sekund później urządzenie pomija polecenie ping keep-alive, ponieważ jest w trybie offline. Jednak IoT Hub nie zamyka połączenia natychmiast — czeka kolejne sekundy przed rozłączeniem urządzenia z błędem `(230 * 1.5) - 230 = 115` [404104 DeviceConnectionClosedRemotely.](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md) 

Maksymalna wartość klienta podtrzymania aktywności, które można ustawić, to `1767 / 1.5 = 1177` sekundy. Każdy ruch zresetuje podtrzymanie aktywności. Na przykład pomyślne odświeżenie tokenu sygnatury dostępu współdzielonego powoduje zresetowanie aktywności.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrowanie aplikacji urządzenia z usługi AMQP do MQTT

Jeśli używasz zestawów [SDK](https://github.com/Azure/azure-iot-sdks)urządzeń, przełączenie z używania protokołu AMQP na MQTT wymaga zmiany parametru protokołu w inicjowaniu klienta, jak wspomniano wcześniej.

W tym celu należy sprawdzić następujące elementy:

* Funkcja AMQP zwraca błędy dla wielu warunków, podczas gdy MQTT przerywa połączenie. W związku z tym logika obsługi wyjątków może wymagać pewnych zmian.

* MQTT nie obsługuje operacji *odrzucania* podczas odbierania komunikatów z chmury [do urządzenia.](iot-hub-devguide-messaging.md) Jeśli aplikacja back-end musi odbierać odpowiedź z aplikacji urządzenia, rozważ użycie [metod bezpośrednich.](iot-hub-devguide-direct-methods.md)

* Pakiet AMQP nie jest obsługiwany w zestawie SDK języka Python.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Przykład w języku C z użyciem MQTT bez zestawu Azure IoT SDK

W repozytorium przykładów [IoT MQTT](https://github.com/Azure-Samples/IoTMQTTSample)znajdziesz kilka projektów demonstracyjnych C/C++ przedstawiających sposób wysyłania komunikatów telemetrycznych i odbierania zdarzeń za pomocą centrum IoT bez użycia zestawu SDK języka C usługi Azure IoT. 

Te przykłady używają biblioteki Eclipse Mosquitto do wysyłania komunikatów do brokera MQTT zaimplementowanego w centrum IoT.

Aby dowiedzieć się, jak dostosować przykłady do korzystania z konwencji [usługi Azure IoT Plug and Play,](../iot-pnp/overview-iot-plug-and-play.md) zobacz Tutorial - Use MQTT to develop an IoT Plug and Play device client (Samouczek — tworzenie klienta urządzenia IoT Plug and Play [MQTT).](../iot-pnp/tutorial-use-mqtt.md)

To repozytorium zawiera:

**W przypadku systemu Windows:**

* TelemetryMQTTWin32: zawiera kod, który wysyła komunikat telemetrii do centrum Azure IoT Hub, sbudowaną i uruchamianą na maszynie z systemem Windows.

* SubscribeMQTTWin32: zawiera kod do subskrybowania zdarzeń danego centrum IoT na maszynie z systemem Windows.

* DeviceTwinMQTTWin32: zawiera kod do wykonywania zapytań i subskrybowania zdarzeń bliźniaczej reprezentacji urządzenia w centrum Azure IoT hub na maszynie z systemem Windows.

* PnPMQTTWin32: zawiera kod do wysyłania komunikatu telemetrii z możliwościami urządzenia IoT Plug and Play do centrum Azure IoT Hub sbudowaną i uruchamianą na maszynie z systemem Windows. Więcej informacji na [](../iot-pnp/overview-iot-plug-and-play.md) temat IoT Plug and Play

**W przypadku systemu Linux:**

* MQTTLinux: zawiera kod i skrypt kompilacji do uruchomienia w systemie Linux (do tej pory przetestowano systemy WSL, Ubuntu i Raspbian).

* LinuxConsoleVS2019: zawiera ten sam kod, ale w projekcie VS2019 przeznaczonym dla systemu WSL (pod system Windows Linux). Ten projekt umożliwia debugowanie kodu uruchomionego w systemie Linux krok po kroku z Visual Studio.

**W mosquitto_pub:**

Ten folder zawiera dwa przykładowe polecenia używane mosquitto_pub narzędziowym dostarczonym przez Mosquitto.org.

* Mosquitto_sendmessage: aby wysłać prostą wiadomość SMS do usługi Azure IoT Hub działającej jako urządzenie.

* Mosquitto_subscribe: aby zobaczyć zdarzenia występujące w centrum Azure IoT Hub.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Używanie protokołu MQTT bezpośrednio (jako urządzenie)

Jeśli urządzenie nie może używać zestawów SDK urządzenia, nadal może łączyć się z publicznymi punktami końcowymi urządzenia przy użyciu protokołu MQTT na porcie 8883. W **pakiecie CONNECT** urządzenie powinno używać następujących wartości:

* W polu **ClientId** użyj wartości **deviceId**.

* W polu **Nazwa** użytkownika użyj wartości `{iothubhostname}/{device_id}/?api-version=2018-06-30` , gdzie to pełna nazwa `{iothubhostname}` CName centrum IoT.

    Jeśli na przykład nazwa centrum IoT  jest contoso.azure-devices.net, a nazwa urządzenia to **MyDevice01,** pełne pole **Nazwa** użytkownika powinno zawierać:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

    Zdecydowanie zaleca się uwzględnianie w polu wersji interfejsu API. W przeciwnym razie może to spowodować nieoczekiwane zachowania. 
    
* W polu **Hasło** użyj tokenu SAS. Format tokenu SAS jest taki sam jak w przypadku protokołów HTTPS i AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Jeśli używasz uwierzytelniania certyfikatu X.509, hasła tokenu SAS nie są wymagane. Aby uzyskać więcej informacji, zobacz [Set up X.509 security in your Azure IoT Hub](iot-hub-security-x509-get-started.md) and follow code instructions in the [TLS/SSL configuration (Konfigurowanie protokołu TLS/SSL)](#tlsssl-configuration)i postępuj zgodnie z instrukcjami w sekcji dotyczącej konfiguracji protokołu TLS/SSL.

  Aby uzyskać więcej informacji na temat sposobu generowania tokenów SAS, zobacz sekcję urządzenia w te [IoT Hub tokenów zabezpieczających.](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

  Podczas testowania można również użyć międzyplatformowego interfejsu Azure IoT Tools for [Visual Studio Code lub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) polecenia rozszerzenia interfejsu wiersza polecenia [az iot hub generate-sas-token,](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) aby szybko wygenerować token SAS, który można skopiować i wkleić do własnego kodu.

### <a name="for-azure-iot-tools"></a>Na Azure IoT Tools

1. Rozwiń **kartę URZĄDZENIA USŁUGI AZURE IOT HUB** w lewym dolnym rogu Visual Studio Code.
  
2. Kliknij prawym przyciskiem myszy urządzenie i wybierz pozycję **Generuj token SAS dla opcji Urządzenie.**
  
3. Ustaw **czas wygaśnięcia i** naciśnij klawisz Enter.
  
4. Token SAS jest tworzony i kopiowany do schowka.

   Wygenerowany token SAS ma następującą strukturę:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Część tego tokenu do użycia jako pole **Hasło** do nawiązywania połączenia przy użyciu programu MQTT jest:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

W przypadku pakietów MQTT connect i disconnect IoT Hub zdarzenia w kanale **monitorowania** operacji. To zdarzenie zawiera dodatkowe informacje, które mogą pomóc w rozwiązywaniu problemów z łącznością.

Aplikacja urządzenia może określić komunikat **Will** w **pakiecie CONNECT.** Aplikacja urządzenia powinna używać wartości lub jako nazwy tematu Will do definiowania komunikatów Będzie przesyłanych jako `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` komunikat telemetrii.   W takim przypadku, jeśli połączenie sieciowe zostanie zamknięte, ale pakiet **DISCONNECT** nie został wcześniej  odebrany z urządzenia, program IoT Hub wysyła komunikat Will podany w pakiecie **CONNECT** do kanału telemetrii. Kanał telemetrii może być domyślnym punktem końcowym **zdarzeń** lub niestandardowym punktem końcowym zdefiniowanym przez IoT Hub routingu. Komunikat ma właściwość **iothub-MessageType** z wartością **Will** assigned to it.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Używanie protokołu MQTT bezpośrednio (jako moduł)

Nawiązywanie połączenia IoT Hub za pośrednictwem protokołu MQTT przy użyciu tożsamości modułu jest podobne do urządzenia (opisanego w sekcji dotyczącej używania protokołu [MQTT](#using-the-mqtt-protocol-directly-as-a-device)bezpośrednio jako urządzenia), ale należy użyć następujących funkcji:

* Ustaw identyfikator klienta na `{device_id}/{module_id}` wartość .

* W przypadku uwierzytelniania przy użyciu nazwy użytkownika i hasła ustaw nazwę użytkownika na i użyj tokenu SAS skojarzonego z tożsamością modułu `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` jako hasła.

* Użyj `devices/{device_id}/modules/{module_id}/messages/events/` jako tematu do publikowania danych telemetrycznych.

* Użyj `devices/{device_id}/modules/{module_id}/messages/events/` jako will tematu.

* Bliźniacze tematy GET i PATCH są identyczne dla modułów i urządzeń.

* Temat stanu bliźniaczej reprezentacji jest identyczny dla modułów i urządzeń.

## <a name="tlsssl-configuration"></a>Konfiguracja protokołu TLS/SSL

Aby bezpośrednio używać protokołu MQTT, klient *musi* nawiązać połączenie za pośrednictwem protokołu TLS/SSL. Próby pominięcia tego kroku nie powiodą się z błędami połączenia.

Aby nawiązać połączenie TLS, może być konieczne pobranie certyfikatu głównego Firmy DigiCert Baltimore i odwołanie się do tego certyfikatu. Ten certyfikat jest używany przez platformę Azure do zabezpieczania połączenia. Ten certyfikat można znaleźć w [repozytorium Azure-iot-sdk-c.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) Więcej informacji na temat tych certyfikatów można znaleźć w witrynie internetowej [firmy Digicert.](https://www.digicert.com/digicert-root-certificates.htm)

Przykład implementacji tej funkcji przy użyciu biblioteki [Paho MQTT](https://pypi.python.org/pypi/paho-mqtt) w języku Python przez program Eclipse Foundation może wyglądać następująco.

Najpierw zainstaluj bibliotekę Paho ze środowiska wiersza polecenia:

```cmd/sh
pip install paho-mqtt
```

Następnie zaim implementuj klienta w skrypcie języka Python. Zastąp symbole zastępcze w następujący sposób:

* `<local path to digicert.cer>` to ścieżka do pliku lokalnego, który zawiera certyfikat główny Firmy DigiCert Baltimore. Możesz utworzyć ten plik, kopiując informacje o certyfikacie z pliku [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) w zestawie SDK usługi Azure IoT dla języka C. Dołącz wiersze i , usuń znaczniki na początku i na końcu każdego wiersza, a następnie usuń znaki na końcu każdego `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` `"` `\r\n` wiersza.

* `<device id from device registry>` to identyfikator urządzenia dodanego do centrum IoT.

* `<generated SAS token>` to token SAS dla urządzenia utworzonego zgodnie z wcześniejszym opisem w tym artykule.

* `<iot hub name>` nazwa centrum IoT.

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

Aby uwierzytelnić się przy użyciu certyfikatu urządzenia, zaktualizuj powyższy fragment kodu przy użyciu następujących zmian (zobacz Jak uzyskać certyfikat [X.509 urzędu](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) certyfikacji, aby dowiedzieć się, jak przygotować się do uwierzytelniania opartego na certyfikatach):

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

Po pomyślnym nawiązaniu połączenia urządzenie może wysyłać komunikaty do usługi IoT Hub przy użyciu `devices/{device_id}/messages/events/` lub `devices/{device_id}/messages/events/{property_bag}` jako nazwy **tematu**. Element `{property_bag}` umożliwia urządzeniu wysyłanie komunikatów z dodatkowymi właściwościami w formacie zakodowanym w adresie URL. Na przykład:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ten `{property_bag}` element używa tego samego kodowania co ciągi zapytań w protokole HTTPS.

Poniżej znajduje się lista IoT Hub zachowania specyficzne dla implementacji:

* IoT Hub nie obsługuje komunikatów QoS 2. Jeśli aplikacja urządzenia publikuje komunikat z **QoS 2,** IoT Hub zamyka połączenie sieciowe.

* IoT Hub zachowuje komunikatów. Jeśli urządzenie wysyła komunikat z flagą **RETAIN** ustawioną na 1, IoT Hub dodaje właściwość **aplikacji mqtt-retain** do komunikatu. W takim przypadku zamiast utrwalać komunikat zachowania, IoT Hub przekazuje go do aplikacji zaplecza.

* IoT Hub obsługuje tylko jedno aktywne połączenie MQTT na urządzenie. Każde nowe połączenie MQTT w imieniu tego samego identyfikatora urządzenia powoduje, że program IoT Hub przerywa istniejące połączenie, a połączenie **400027ForcefullyClosedOnNewConnection** zostanie zarejestrowane w dziennikach IoT Hub


Aby uzyskać więcej informacji, zobacz [Przewodnik dewelopera dotyczący komunikatów.](iot-hub-devguide-messaging.md)

## <a name="receiving-cloud-to-device-messages"></a>Odbieranie komunikatów z chmury do urządzenia

Aby odbierać komunikaty z IoT Hub, urządzenie powinno subskrybować za pomocą `devices/{device_id}/messages/devicebound/#` filtru **tematu**. Wieloznaczny symbol wielopoziomowy w filtrze tematu służy tylko do zezwalania urządzeniu na odbieranie dodatkowych właściwości `#` w nazwie tematu. IoT Hub nie zezwala na używanie symboli wieloznacznych lub do `#` `?` filtrowania podtematów. Ponieważ IoT Hub nie jest brokerem komunikatów pub-sub ogólnego przeznaczenia, obsługuje tylko udokumentowane nazwy tematów i filtry tematów.

Urządzenie nie odbiera żadnych komunikatów z usługi IoT Hub, dopóki nie zasubskrybuje punktu końcowego specyficznego dla urządzenia reprezentowanego przez `devices/{device_id}/messages/devicebound/#` filtr tematu. Po nawiązyniu subskrypcji urządzenie odbiera komunikaty z chmury do urządzenia, które zostały do niego wysłane po upływie czasu subskrypcji. Jeśli urządzenie łączy się z **flagą CleanSession** ustawioną na **0,** subskrypcja jest zachowywana między różnymi sesjami. W takim przypadku przy następnym nawiąże połączenie z urządzeniem **cleanSession 0** otrzyma wszystkie zaległe komunikaty wysłane do niego po odłączeniu. Jeśli jednak urządzenie używa flagi **CleanSession** ustawionej na **wartość 1,** nie odbiera żadnych komunikatów z usługi IoT Hub dopóki nie zasubskrybuje swojego punktu końcowego urządzenia.

IoT Hub komunikaty z nazwą **tematu** `devices/{device_id}/messages/devicebound/` lub `devices/{device_id}/messages/devicebound/{property_bag}` właściwościami komunikatów. `{property_bag}` Zawiera pary klucz/wartość zakodowane w adresie URL właściwości komunikatu. Tylko właściwości aplikacji i właściwości systemowe możliwe do ustawienia przez użytkownika (takie jak **messageId** lub **correlationId)** są uwzględniane w zestawie właściwości. Nazwy właściwości systemowych mają prefiks , właściwości aplikacji **$** używają oryginalnej nazwy właściwości bez prefiksu. Aby uzyskać dodatkowe informacje o formacie worka właściwości, zobacz [Wysyłanie komunikatów z](#sending-device-to-cloud-messages)urządzenia do chmury .

W komunikatach z chmury do urządzenia wartości w torbie właściwości są reprezentowane jako w poniższej tabeli:

| Wartość właściwości | Reprezentacja | Opis |
|----|----|----|
| `null` | `key` | W pępce właściwości pojawia się tylko klucz |
| pusty ciąg | `key=` | Klucz, po którym następuje znak równości bez wartości |
| wartość niepusta, niepusta | `key=value` | Klucz, po którym następuje znak równości i wartość |

W poniższym przykładzie przedstawiono zestaw właściwości zawierający trzy właściwości aplikacji: **prop1** z wartością `null` ; **prop2**, pusty ciąg (""); i **prop3** z wartością "ciąg".

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Gdy aplikacja urządzenia subskrybuje temat za pomocą **QoS 2,** program IoT Hub udziela maksymalnego poziomu QoS 1 w **pakiecie SUBACK.** Następnie program IoT Hub komunikaty do urządzenia przy użyciu QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Pobieranie właściwości bliźniaczej reprezentacji urządzenia

Najpierw urządzenie subskrybuje usługę `$iothub/twin/res/#` , aby otrzymywać odpowiedzi operacji. Następnie wysyła pusty komunikat do tematu z wypełnioną wartością `$iothub/twin/GET/?$rid={request id}` identyfikatora **żądania**. Następnie usługa wysyła komunikat odpowiedzi zawierający dane bliźniaczej reprezentacji urządzenia w temacie przy użyciu tego samego identyfikatora żądania co `$iothub/twin/res/{status}/?$rid={request id}` żądanie. 

Identyfikator żądania może być dowolną prawidłową wartością właściwości komunikatu, zgodnie z IoT Hub przewodnika dewelopera obsługi komunikatów, [a](iot-hub-devguide-messaging.md)stan jest weryfikowany jako liczba całkowita.

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
| 429 | Zbyt wiele żądań (z ograniczeniami) zgodnie z [IoT Hub ograniczeniami](iot-hub-devguide-quotas-throttling.md) |
| 5** | Błędy serwera |

Aby uzyskać więcej informacji, zobacz przewodnik dewelopera [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Aktualizowanie zgłaszanych właściwości bliźniaczej reprezentacji urządzenia

Aby zaktualizować zgłaszane właściwości, urządzenie zgłasza żądanie IoT Hub za pośrednictwem publikacji w wyznaczonym temacie MQTT. Po przetworzeniu żądania IoT Hub stan powodzenia lub niepowodzenia operacji aktualizacji za pośrednictwem publikacji do innego tematu. Ten temat może zostać zasubskrybowany przez urządzenie w celu powiadomienia go o wyniku żądania aktualizacji bliźniaczej reprezentacji. Aby zaimplementować ten typ interakcji żądania/odpowiedzi na urządzeniu MQTT, wykorzystujemy nazwę identyfikatora żądania ( ) dostarczonego początkowo przez urządzenie w `$rid` żądaniu aktualizacji. Ten identyfikator żądania jest również uwzględniany w odpowiedzi IoT Hub, aby umożliwić urządzeniu skorelowanie odpowiedzi z określonym wcześniejszym żądaniem.

W poniższej sekwencji opisano, jak urządzenie aktualizuje zgłaszane właściwości w bliźniaczej reprezentacji urządzenia w IoT Hub:

1. Urządzenie musi najpierw subskrybować temat, aby otrzymywać odpowiedzi operacji od `$iothub/twin/res/#` IoT Hub.

2. Urządzenie wysyła komunikat zawierający aktualizację bliźniaczej reprezentacji urządzenia do `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tematu. Ten komunikat zawiera **wartość identyfikatora** żądania.

3. Następnie usługa wysyła komunikat odpowiedzi, który zawiera nową wartość tagu ETag dla kolekcji zgłaszanych właściwości w temacie `$iothub/twin/res/{status}/?$rid={request id}` . Ten komunikat odpowiedzi używa tego samego **identyfikatora żądania** co żądanie.

Treść komunikatu żądania zawiera dokument JSON zawierający nowe wartości dla zgłaszanych właściwości. Każdy członek w dokumencie JSON jest aktualizowany lub dodaje odpowiedni członek w dokumencie bliźniaczej reprezentacji urządzenia. Członek ustawiony na `null` usunięcie członka z obiektu zawierającego. Na przykład:

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
| 400 | Złe żądanie. Źle sformułowany JSON |
| 429 | Zbyt wiele żądań (ograniczanych) zgodnie z [IoT Hub ograniczeniami](iot-hub-devguide-quotas-throttling.md) |
| 5** | Błędy serwera |

Poniższy fragment kodu w języku Python przedstawia proces aktualizacji zgłoszonych właściwości bliźniaczej reprezentacji za pośrednictwem MQTT (przy użyciu klienta Paho MQTT):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Po sukcesie powyższej operacji aktualizacji zgłoszonych właściwości bliźniaczej reprezentacji komunikat z usługi IoT Hub będzie zawierał następujący temat: , gdzie to kod stanu wskazujący powodzenie, odpowiada identyfikatorowi żądania podanemu przez urządzenie w kodzie i odpowiada wersji zgłaszanej sekcji właściwości bliźniaczych reprezentacji urządzeń po `$iothub/twin/res/204/?$rid=1&$version=6` `204` `$rid=1` `$version` aktualizacji.

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Otrzymywanie powiadomień o aktualizacji żądanych właściwości

Gdy urządzenie jest połączone, IoT Hub wysyła powiadomienia do tematu , który zawiera zawartość aktualizacji wykonanej przez `$iothub/twin/PATCH/properties/desired/?$version={new version}` serwer back end rozwiązania. Na przykład:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Podobnie jak w przypadku aktualizacji właściwości, wartości oznaczają, że jest usuwany `null` członek obiektu JSON. Należy również zauważyć, `$version` że wskazuje nową wersję sekcji żądanych właściwości bliźniaczej reprezentacji.

> [!IMPORTANT]
> IoT Hub generuje powiadomienia o zmianie tylko wtedy, gdy urządzenia są połączone. Pamiętaj, aby zaimplementować [przepływ ponownego](iot-hub-devguide-device-twins.md#device-reconnection-flow) nawiązywania połączenia urządzenia, aby zapewnić synchronizację żądanych właściwości IoT Hub aplikacją urządzenia.

Aby uzyskać więcej informacji, zobacz przewodnik dewelopera [bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Odpowiadanie na metodę bezpośrednią

Najpierw urządzenie musi subskrybować usługę `$iothub/methods/POST/#` . IoT Hub metody do tematu `$iothub/methods/POST/{method name}/?$rid={request id}` , z prawidłowym kodem JSON lub pustą treścią.

Aby odpowiedzieć, urządzenie wysyła do tematu komunikat z prawidłowym kodem JSON lub pustą `$iothub/methods/res/{status}/?$rid={request id}` treścią. W tym komunikacie **identyfikator żądania** musi odpowiadać identyfikatorowi w komunikacie żądania, a **stan** musi być liczbą całkowitą.

Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera metody bezpośredniej](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Jeśli musisz dostosować zachowanie protokołu MQTT po stronie chmury, zapoznaj się z bramą protokołu [Azure IoT.](iot-hub-protocol-gateway.md) To oprogramowanie umożliwia wdrożenie bramy protokołu niestandardowego o wysokiej wydajności, która interfejsuje bezpośrednio z IoT Hub. Brama protokołu Azure IoT umożliwia dostosowanie protokołu urządzenia w celu uwzględnienia wdrożeń brownfield MQTT lub innych protokołów niestandardowych. Takie podejście wymaga jednak uruchomienia i obsługi niestandardowej bramy protokołu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat protokołu MQTT, zobacz [dokumentację MQTT](https://mqtt.org/).

Aby dowiedzieć się więcej na temat planowania IoT Hub wdrożenia, zobacz:

* [Certyfikat platformy Microsoft Azure dla IoT — wykaz urządzeń](https://devicecatalog.azure.com/)
* [Obsługa dodatkowych protokołów](iot-hub-protocol-gateway.md)
* [Porównanie z Event Hubs](iot-hub-compare-event-hubs.md)
* [Skalowanie, ha i dr](iot-hub-scaling.md)

Aby bardziej eksplorować możliwości IoT Hub, zobacz:

* [IoT Hub dla deweloperów](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/quickstart-linux.md)
