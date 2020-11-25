---
title: Samouczek — łączenie ogólnej aplikacji klienckiej Java z platformą Azure IoT Central | Microsoft Docs
description: W tym samouczku pokazano, jak deweloper urządzenia łączy urządzenie z uruchomioną aplikacją kliencką Java do aplikacji IoT Central platformy Azure. Użytkownik modyfikuje automatycznie wygenerowany szablon urządzenia przez dodanie widoków, które umożliwiają operatorowi współpracujące z podłączonym urządzeniem.
author: dominicbetts
ms.author: dobett
ms.date: 11/05/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
ms.openlocfilehash: dabfbdcd8d5181096e9b9b038418140a07c97e32
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "96017507"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-java"></a>Samouczek: Tworzenie i łączenie aplikacji klienckiej z aplikacją usługi Azure IoT Central (Java)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku pokazano, jak programista urządzeń ma połączyć aplikację kliencką Java z aplikacją IoT Central platformy Azure. Aplikacja Java symuluje zachowanie urządzenia z termostatem. Gdy aplikacja nawiązuje połączenie z IoT Central, wysyła identyfikator modelu urządzenia termostatu. IoT Central korzysta z identyfikatora modelu, aby pobrać model urządzenia i utworzyć dla Ciebie szablon urządzenia. Dodano dostosowania i widoki do szablonu urządzenia, aby umożliwić operatorowi współpracujące z urządzeniem.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz i uruchom kod urządzenia Java i sprawdź, czy jest on połączony z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne wysyłane z urządzenia.
> * Dodawanie niestandardowych widoków do szablonu urządzenia.
> * Opublikuj szablon urządzenia.
> * Użyj widoku, aby zarządzać właściwościami urządzeń.
> * Wywoływanie polecenia w celu sterowania urządzeniem.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja IoT Central platformy Azure utworzona przy użyciu szablonu **aplikacji niestandardowej** . Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md). Aplikacja musi zostać utworzona w dniu lub po 14 lipca 2020.
* Komputer deweloperski z Java SE Development Kit 8. W programie [Java długoterminowa obsługa platformy Azure i Azure Stack](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)w obszarze **Obsługa długoterminowa** wybierz pozycję **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).
* Lokalna kopia repozytorium usługi [Microsoft Azure IoT SDK dla środowiska Java](https://github.com/Azure/azure-iot-sdk-java) GitHub, która zawiera przykładowy kod. Użyj tego linku, aby pobrać kopię repozytorium: [Pobierz plik zip](https://github.com/Azure/azure-iot-sdk-java/archive/master.zip). Następnie Rozpakuj plik do odpowiedniej lokalizacji na komputerze lokalnym.

## <a name="review-the-code"></a>Przeglądanie kodu

W edytorze tekstów na kopii Microsoft Azure IoT SDK dla środowiska Java pobranego wcześniej Otwórz plik *Azure-IoT-SDK-Java/Device/IoT-Device-Samples/PnP-Device-Sample/thermostat-Device-Sample/src/Main/Java/Samples/com/Microsoft/Azure/SDK/IoT/Device/termostat. Java* .

Po uruchomieniu przykładu w celu nawiązania połączenia z IoT Centralm program używa usługi Device Provisioning (DPS) do zarejestrowania urządzenia i wygenerowania parametrów połączenia. Przykład pobiera informacje o połączeniu usługi DPS wymagane z środowiska wiersza polecenia.

`main`Metoda:

* Wywołania `initializeAndProvisionDevice` dotyczące ustawiania `dtmi:com:example:Thermostat;1` identyfikatora modelu, używania DPS do inicjowania obsługi i rejestrowania urządzenia, tworzenia wystąpienia **DeviceClient** i łączenia się z aplikacją IoT Central.
* Tworzy procedurę obsługi poleceń dla `getMaxMinReport` polecenia.
* Tworzy procedurę obsługi aktualizacji właściwości dla modyfikowalnej `targetTemperature` właściwości.
* Uruchamia wątek w celu wysyłania danych telemetrycznych temperatury i aktualizowania `maxTempSinceLastReboot` właściwości co pięć sekund.

```java
async function main() {

public static void main(String[] args) throws URISyntaxException, IOException, ProvisioningDeviceClientException, InterruptedException {

    // ...

    switch (deviceSecurityType.toLowerCase())
    {
        case "dps":
        {
            if (validateArgsForDpsFlow())
            {
                initializeAndProvisionDevice();
                break;
            }
            throw new IllegalArgumentException("Required environment variables are not set for DPS flow, please recheck your environment.");
        }
        case "connectionstring":
        {
            // ...
        }
        default:
        {
            // ...
        }
    }

    deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new TargetTemperatureUpdateCallback(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback =
            Collections.singletonMap(
                    new Property("targetTemperature", null),
                    new Pair<>(new TargetTemperatureUpdateCallback(), null));
    deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);

    String methodName = "getMaxMinReport";
    deviceClient.subscribeToDeviceMethod(new GetMaxMinReportMethodCallback(), methodName, new MethodIotHubEventCallback(), methodName);

    new Thread(new Runnable() {
        @SneakyThrows({InterruptedException.class, IOException.class})
        @Override
        public void run() {
            while (true) {
                if (temperatureReset) {
                    // Generate a random value between 5.0°C and 45.0°C for the current temperature reading.
                    temperature = BigDecimal.valueOf(random.nextDouble() * 40 + 5).setScale(1, RoundingMode.HALF_UP).doubleValue();
                    temperatureReset = false;
                }

                sendTemperatureReading();
                Thread.sleep(5 * 1000);
            }
        }
    }).start();
}
```

`initializeAndProvisionDevice`Metoda pokazuje, w jaki sposób urządzenie używa DPS do rejestrowania i łączenia się z IoT Central. Ładunek zawiera identyfikator modelu:

```java
private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException {
    SecurityProviderSymmetricKey securityClientSymmetricKey = new SecurityProviderSymmetricKey(deviceSymmetricKey.getBytes(), registrationId);
    ProvisioningDeviceClient provisioningDeviceClient = null;
    ProvisioningStatus provisioningStatus = new ProvisioningStatus();

    provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityClientSymmetricKey);

    AdditionalData additionalData = new AdditionalData();
    additionalData.setProvisioningPayload(String.format("{\"modelId\": \"%s\"}", MODEL_ID));

    provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

    while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
    {
        if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
                provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
                provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
        {
            provisioningStatus.exception.printStackTrace();
            System.out.println("Registration error, bailing out");
            break;
        }
        System.out.println("Waiting for Provisioning Service to register");
        Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
    }

    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);

    if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
        // ...

        String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
        String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

        deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityClientSymmetricKey, IotHubClientProtocol.MQTT, options);
        deviceClient.open();
    }
}
```

`sendTemperatureTelemetry`Metoda pokazuje, w jaki sposób urządzenie wysyła dane telemetryczne dotyczące temperatury do IoT Central:

```java
private static void sendTemperatureTelemetry() {
    String telemetryName = "temperature";
    String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

    Message message = new Message(telemetryPayload);
    message.setContentEncoding(StandardCharsets.UTF_8.name());
    message.setContentTypeFinal("application/json");

    deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);

    temperatureReadings.put(new Date(), temperature);
}
```

`updateMaxTemperatureSinceLastReboot`Metoda wysyła `maxTempSinceLastReboot` aktualizację właściwości do IoT Central:

```java
private static void updateMaxTemperatureSinceLastReboot() throws IOException {
    String propertyName = "maxTempSinceLastReboot";
    Property reportedProperty = new Property(propertyName, maxTemperature);

    deviceClient.sendReportedProperties(Collections.singleton(reportedProperty));
}
```

`TargetTemperatureUpdateCallback`Klasa zawiera `TwinPropertyCallBack` metodę do obsługi aktualizacji właściwości z możliwością zapisu z IoT Central:

```java
String propertyName = "targetTemperature";

public void TwinPropertyCallBack(Property property, Object context) {
    if (property.getKey().equalsIgnoreCase(propertyName)) {
        double targetTemperature = ((Number)property.getValue()).doubleValue();

        EmbeddedPropertyUpdate pendingUpdate = new EmbeddedPropertyUpdate(targetTemperature, StatusCode.IN_PROGRESS.value, property.getVersion(), null);
        Property reportedPropertyPending = new Property(propertyName, pendingUpdate);
        try {
            deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyPending));
        } catch (IOException e) {
            throw new RuntimeException("IOException when sending reported property update: ", e);
        }

        // Update temperature in 2 steps
        double step = (targetTemperature - temperature) / 2;
        for (int i = 1; i <=2; i++) {
            temperature = BigDecimal.valueOf(temperature + step).setScale(1, RoundingMode.HALF_UP).doubleValue();
            Thread.sleep(5 * 1000);
        }

        EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(temperature, StatusCode.COMPLETED.value, property.getVersion(), "Successfully updated target temperature");
        Property reportedPropertyCompleted = new Property(propertyName, completedUpdate);
        deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
    } else {
        log.debug("Property: Received an unrecognized property update from service.");
    }
}
```

`GetMaxMinReportMethodCallback`Klasa zawiera `call` metodę do obsługi poleceń wywoływanych z IoT Central:

```java
String commandName = "getMaxMinReport";

public DeviceMethodData call(String methodName, Object methodData, Object context) {
    if (methodName.equalsIgnoreCase(commandName)) {

        String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);
        Date since = getCommandRequestValue(jsonRequest, Date.class);

        double runningTotal = 0;
        Map<Date, Double> filteredReadings = new HashMap<>();
        for (Map.Entry<Date, Double> entry : temperatureReadings.entrySet()) {
            if (entry.getKey().after(since)) {
                filteredReadings.put(entry.getKey(), entry.getValue());
                runningTotal += entry.getValue();
            }
        }

        if (filteredReadings.size() > 1) {
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'");
            double maxTemp = Collections.max(filteredReadings.values());
            double minTemp = Collections.min(filteredReadings.values());
            double avgTemp = runningTotal / filteredReadings.size();
            String startTime = sdf.format(Collections.min(filteredReadings.keySet()));
            String endTime = sdf.format(Collections.max(filteredReadings.keySet()));

            String responsePayload = String.format(
                    "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                    maxTemp,
                    minTemp,
                    avgTemp,
                    startTime,
                    endTime);

            return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
        }

        return new DeviceMethodData(StatusCode.NOT_FOUND.value, null);
    }

    log.error("Command: Unknown command {} invoked from service.", methodName);
    return new DeviceMethodData(StatusCode.NOT_FOUND.value, null);
}
```

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

[!INCLUDE [iot-central-connection-configuration](../../../includes/iot-central-connection-configuration.md)]

W systemie Windows przejdź do folderu głównego pobranego repozytorium Java SDK.

Uruchom następujące polecenie, aby skompilować przykładową aplikację:

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-code"></a>Uruchamianie kodu

Aby uruchomić przykładową aplikację, Otwórz środowisko wiersza polecenia i przejdź do folderu *Azure-IoT-SDK-Java/Device/IoT-Device-Samples/PnP-Device-Sample/termostat-Device-przyklad* , który zawiera przykładowy plik *termostat. Java* .

[!INCLUDE [iot-central-connection-environment](../../../includes/iot-central-connection-environment.md)]

Uruchom przykład:

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

Poniższe dane wyjściowe pokazują urządzenie, które rejestruje i nawiązuje połączenie z IoT Central. Przykład zaczyna wysyłać dane telemetryczne:

```cmd/sh
Waiting for Provisioning Service to register
Waiting for Provisioning Service to register
IotHUb Uri : iotc-...azure-devices.net
Device ID : sample-device-01
2020-11-05 14:38:27.218 DEBUG Thermostat:208 - Opening the device client.
2020-11-05 14:38:27.253 DEBUG MqttIotHubConnection:122 - Opening MQTT connection...
2020-11-05 14:38:27.405 DEBUG Mqtt:117 - Sending MQTT CONNECT packet...
2020-11-05 14:38:28.782 DEBUG Mqtt:121 - Sent MQTT CONNECT packet was acknowledged
2020-11-05 14:38:28.786 DEBUG Mqtt:291 - Sending MQTT SUBSCRIBE packet for topic devices/sample-device-01/messages/devicebound/#
2020-11-05 14:38:28.965 DEBUG Mqtt:297 - Sent MQTT SUBSCRIBE packet for topic devices/sample-device-01/messages/devicebound/# was acknowledged
2020-11-05 14:38:28.967 DEBUG MqttIotHubConnection:205 - MQTT connection opened successfully
2020-11-05 14:38:28.968 INFO  IotHubTransport:270 - The connection to the IoT Hub has been established
2020-11-05 14:38:28.970 INFO  IotHubTransport:1133 - Updating transport status to new status CONNECTED with reason CONNECTION_OK
2020-11-05 14:38:28.972 DEBUG IotHubTransport:1143 - Invoking connection status callbacks with new status details
2020-11-05 14:38:28.976 INFO  IotHubTransport:327 - Client connection opened successfully
2020-11-05 14:38:28.978 INFO  DeviceClient:398 - Device client opened successfully
2020-11-05 14:38:28.979 DEBUG Thermostat:142 - Start twin and set handler to receive "targetTemperature" updates.
2020-11-05 14:38:28.993 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [85f8f25b-924e-47a1-b7f0-e7bd227cbb9f] Message Id [df65a02b-6d75-40fe-8636-a571ad3ae970] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2020-11-05 14:38:28.994 INFO  IotHubTransport:1040 - Sending message ( Message details: Correlation Id [85f8f25b-924e-47a1-b7f0-e7bd227cbb9f] Message Id [df65a02b-6d75-40fe-8636-a571ad3ae970] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2020-11-05 14:38:29.000 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [d3692283-63d6-446a-aeab-5a4e4aa9e948] Message Id [d53139e3-acb4-458b-8c03-119d8fc04d5a] Request Id [0] Device Operation Type [DEVICE_OPERATION_TWIN_GET_REQUEST] )
2020-11-05 14:38:29.018 DEBUG Mqtt:291 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
2020-11-05 14:38:29.080 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [f8c2180d-7289-4bf8-afa5-0ad6565a27eb] Message Id [c553e8bd-3c7e-4830-a3e7-12964cf3d936] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2020-11-05 14:38:29.103 DEBUG Thermostat:150 - Set handler to receive "getMaxMinReport" command.
2020-11-05 14:38:29.127 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [c6f3c2e6-68da-4d52-b429-9e62418a2fd7] Message Id [7f17f191-7ab0-46e7-897e-a635c0519287] Device Operation Type [DEVICE_OPERATION_METHOD_SUBSCRIBE_REQUEST] )
2020-11-05 14:38:29.167 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [e1dc7b20-1783-462a-87db-a63042a583c4] Message Id [752f3b01-6c28-4f76-a314-97c75f37bc64] )
2020-11-05 14:38:29.169 DEBUG Thermostat:419 - Telemetry: Sent - {"temperature": 39.6 C} with message Id 752f3b01-6c28-4f76-a314-97c75f37bc64.
```

[!INCLUDE [iot-central-monitor-thermostat](../../../includes/iot-central-monitor-thermostat.md)]

Można sprawdzić, jak urządzenie reaguje na polecenia i aktualizacje właściwości:

```cmd/sh
2020-11-05 14:43:03.179 DEBUG Thermostat:318 - Command: Received - Generating min, max, avg temperature report since Thu Nov 05 06:30:00 GMT 2020.
2020-11-05 14:43:03.180 DEBUG Thermostat:345 - Command: MaxMinReport since Thu Nov 05 06:30:00 GMT 2020: "maxTemp": 33.1 C, "minTemp": 33.1 C, "avgTemp": 33.1 C, "startTime": 2020-11-05T14:42:28Z, "endTime": 2020-11-05T14:42:58Z

...

2020-11-05 14:49:08.182 DEBUG Thermostat:261 - Property: Received - {"targetTemperature": 56.0 C}.
2020-11-05 14:49:08.185 DEBUG Thermostat:270 - Property: Update - {"targetTemperature": 56.0 C} is IN_PROGRESS
2020-11-05 14:49:18.206 DEBUG Thermostat:282 - Property: Update - {"targetTemperature": 55.9 C} is COMPLETED
```

## <a name="view-raw-data"></a>Wyświetlanie danych pierwotnych

[!INCLUDE [iot-central-monitor-thermostat-raw-data](../../../includes/iot-central-monitor-thermostat-raw-data.md)]

## <a name="next-steps"></a>Następne kroki

Jeśli wolisz korzystać z zestawu samouczków IoT Central i dowiedzieć się więcej na temat tworzenia rozwiązania IoT Central, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie szablonu urządzenia bramy](./tutorial-define-gateway-device-type.md)

Jako deweloper urządzenia teraz znasz podstawowe informacje dotyczące sposobu tworzenia urządzenia przy użyciu języka Java, a niektóre sugerowane następne kroki to:

* Przeczytaj [co to są szablony urządzeń?](./concepts-device-templates.md) aby dowiedzieć się więcej na temat roli szablonów urządzeń podczas implementowania kodu urządzenia.
* Aby dowiedzieć się więcej o sposobach rejestrowania urządzeń w usłudze IoT Central i sposobach IoT Central zabezpieczania połączeń urządzeń, przeczytaj artykuł [wprowadzenie do usługi Azure IoT Central](./concepts-get-connected.md) .
* Odczytaj [ładunki telemetryczne, właściwości i poleceń,](concepts-telemetry-properties-commands.md) aby dowiedzieć się więcej o danych, które są wymieniane przez urządzenia z IoT Central.
