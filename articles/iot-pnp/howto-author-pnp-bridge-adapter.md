---
title: Jak utworzyć adapter dla mostka Plug and Play IoT | Microsoft Docs
description: Zidentyfikuj składniki karty mostka Plug and Play IoT. Dowiedz się, jak zwiększyć mostek, pisząc własny adapter.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746832"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>Poszerzanie mostka Plug and Play IoT
[Mostek Plug and Play IoT](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) umożliwia podłączenie istniejących urządzeń podłączonych do bramy do centrum IoT Hub. Za pomocą mostka można mapować interfejsy Plug and Play IoT na dołączone urządzenia. Interfejs Plug and Play IoT definiuje dane telemetryczne wysyłane przez urządzenie, właściwości zsynchronizowane między urządzeniem a chmurą oraz polecenia, na które odpowiada urządzenie. Możesz zainstalować i skonfigurować aplikację mostka "open source" w bramach systemu Windows lub Linux. Dodatkowo mostek można uruchomić jako moduł środowiska uruchomieniowego Azure IoT Edge.

W tym artykule szczegółowo wyjaśniono, jak:

- Rozwiń mostek Plug and Play IoT za pomocą adaptera.
- Zaimplementuj typowe wywołania zwrotne dla karty mostka.

Aby zapoznać się z prostym przykładem, który pokazuje, jak korzystać z mostka, zobacz [jak podłączyć przykład usługi IoT Plug and Play Bridge, który działa w systemie Linux lub Windows, aby IoT Hub](howto-use-iot-pnp-bridge.md).

Wskazówki i przykłady w tym artykule założono podstawową wiedzę na temat [usługi Azure Digital bliźniaczych reprezentacji](../digital-twins/overview.md) i [IoT Plug and Play](overview-iot-plug-and-play.md). Ponadto w tym artykule założono znajomość sposobu [kompilowania i wdrażania mostka Plug and Play IoT](howto-build-deploy-extend-pnp-bridge.md).

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>Przewodnik projektowy do rozbudowy mostka Plug and Play IoT za pomocą adaptera

Aby zwiększyć możliwości mostka, możesz utworzyć własne karty mostków.

Mostek używa kart sieciowych do:

- Nawiąż połączenie między urządzeniem a chmurą.
- Włącz przepływ danych między urządzeniem a chmurą.
- Włącz zarządzanie urządzeniami w chmurze.

Każda karta mostku musi:

- Utwórz interfejs Digital bliźniaczych reprezentacji.
- Użyj interfejsu, aby powiązać funkcje po stronie urządzenia z funkcjami opartymi na chmurze, takimi jak dane telemetryczne, właściwości i polecenia.
- Ustanów kontrolę i komunikację z danymi przy użyciu sprzętu lub oprogramowania układowego urządzenia.

Każda karta mostku współdziała z określonym typem urządzenia w zależności od tego, w jaki sposób adapter nawiązuje połączenie z urządzeniem i współdziała z nim. Nawet jeśli komunikacja z urządzeniem korzysta z protokołu uzgadniania, karta mostka może mieć wiele sposobów interpretacji danych z urządzenia. W tym scenariuszu karta mostka używa informacji dla karty w pliku konfiguracyjnym, aby określić *konfigurację interfejsu* , która powinna być używana przez adapter do analizy danych.

Aby można było korzystać z urządzenia, karta mostka używa protokołu komunikacyjnego obsługiwanego przez urządzenie i interfejsy API udostępniane przez podstawowy system operacyjny lub dostawcę urządzenia.

Aby można było korzystać z chmury, adapter mostku używa interfejsów API udostępnianych przez zestaw SDK języka C usługi Azure IoT do wysyłania telemetrii, tworzenia interfejsów cyfrowych sieci dwuosiowych, wysyłania aktualizacji właściwości i tworzenia funkcji wywołania zwrotnego dla aktualizacji właściwości i poleceń.

### <a name="create-a-bridge-adapter"></a>Tworzenie adaptera mostkowego

Most oczekuje adaptera mostu, aby zaimplementować interfejsy API zdefiniowane w interfejsie [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) :

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

W tym interfejsie:

- `PNPBRIDGE_ADAPTER_CREATE` tworzy kartę i konfiguruje zasoby zarządzania interfejsami. Adapter może również polegać na globalnych parametrach karty do tworzenia adaptera. Ta funkcja jest wywoływana jednokrotnie dla jednej karty.
- `PNPBRIDGE_COMPONENT_CREATE` tworzy interfejsy klienta Digital bliźniaczy i wiąże funkcje wywołania zwrotnego. Adapter inicjuje kanał komunikacyjny z urządzeniem. Karta może skonfigurować zasoby do włączania przepływu telemetrii, ale nie uruchamia zgłaszaj telemetrii do momentu `PNPBRIDGE_COMPONENT_START` wywołania. Ta funkcja jest wywoływana jednokrotnie dla każdego składnika interfejsu w pliku konfiguracji.
- `PNPBRIDGE_COMPONENT_START` jest wywoływana, aby umożliwić karcie mostka rozpoczęcie przekazywania danych telemetrycznych z urządzenia do klienta Digital bliźniaczy. Ta funkcja jest wywoływana jednokrotnie dla każdego składnika interfejsu w pliku konfiguracji.
- `PNPBRIDGE_COMPONENT_STOP` powoduje zatrzymanie przepływu telemetrii.
- `PNPBRIDGE_COMPONENT_DESTROY` niszczy klienta Digital bliźniaczy i powiązane zasoby interfejsu. Ta funkcja jest wywoływana jednokrotnie dla każdego składnika interfejsu w pliku konfiguracji, gdy mostek zostanie rozłączony lub wystąpił błąd krytyczny.
- `PNPBRIDGE_ADAPTER_DESTROY` czyści zasoby karty mostkowej.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Interakcja z mostkiem z kartami mostku

Poniższa lista zawiera informacje o tym, co się stanie po uruchomieniu mostka:

1. Po uruchomieniu mostka Menedżer adapterów mostka przegląda każdy składnik interfejsu zdefiniowany w pliku konfiguracji i wywołuje `PNPBRIDGE_ADAPTER_CREATE` odpowiednią kartę. Adapter może używać parametrów konfiguracji karty globalnej do konfigurowania zasobów do obsługi różnych *konfiguracji interfejsu*.
1. Dla każdego urządzenia w pliku konfiguracji Menedżer mostów inicjuje Tworzenie interfejsu przez wywołanie `PNPBRIDGE_COMPONENT_CREATE` w odpowiedniej karcie mostka.
1. Adapter odbiera wszystkie opcjonalne ustawienia konfiguracji dla składnika interfejsu i używa tych informacji do konfigurowania połączeń z urządzeniem.
1. Karta tworzy interfejsy klienta Digital 3945ABG i wiąże funkcje wywołania zwrotnego dla aktualizacji właściwości i poleceń. Ustanowienie połączeń urządzeń nie powinno blokować powrotu wywołania zwrotnego po pomyślnym utworzeniu interfejsu sieci dwuosiowej. Aktywne połączenie z urządzeniem jest niezależne od aktywnego klienta interfejsu tworzonego przez mostek. Jeśli połączenie nie powiedzie się, karta założono, że urządzenie jest nieaktywne. Adapter mostka może spróbować ponowić próbę nawiązania połączenia.
1. Po utworzeniu przez Menedżera adapterów mostów wszystkich składników interfejsu określonych w pliku konfiguracji rejestruje wszystkie interfejsy za pomocą usługi Azure IoT Hub. Rejestracja jest blokowanym wywołaniem asynchronicznym. Po zakończeniu wywołania wyzwala wywołanie zwrotne na karcie mostka, która może następnie obsługiwać wywołania zwrotne właściwości i poleceń z chmury.
1. Menedżer adapterów mostka następnie wywołuje `PNPBRIDGE_INTERFACE_START` każdy składnik, a adapter mostka uruchamia raportowanie danych telemetrycznych do klienta Digital bliźniaczy.

### <a name="design-guidelines"></a>Wytyczne dotyczące projektowania

Postępuj zgodnie z poniższymi wskazówkami podczas tworzenia nowej karty mostkowej:

- Określ, które możliwości urządzenia są obsługiwane i jakie definicje interfejsów składników używających tej karty wyglądają.
- Określanie interfejsu i parametrów globalnych wymaganych przez kartę w pliku konfiguracji.
- Zidentyfikuj komunikację urządzenia niskiego poziomu wymaganą do obsługi właściwości i poleceń składnika.
- Określ, jak karta powinna analizować dane pierwotne z urządzenia i konwertować je na typy telemetryczne, które określa definicja interfejsu Plug and Play IoT.
- Zaimplementuj opisany wcześniej interfejs karty mostka.
- Dodaj nową kartę do manifestu karty i skompiluj mostek.

### <a name="enable-a-new-bridge-adapter"></a>Włącz nową kartę mostka

Aby włączyć karty w mostku, dodając odwołanie w [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Wywołania zwrotne adaptera mostkowego są wywoływane sekwencyjnie. Adapter nie powinien blokować wywołania zwrotnego, ponieważ uniemożliwia to wykonywanie przez program Bridge rdzenia.

### <a name="sample-camera-adapter"></a>Przykładowa karta aparatu

[Plik Readme karty aparatu fotograficznego](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) opisuje przykładową kartę kamery, którą można włączyć.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Przykłady kodu dla typowych scenariuszy i wywołań zwrotnych kart

W poniższej sekcji przedstawiono szczegółowe informacje na temat tego, jak karta dla mostka będzie implementować wywołania zwrotne dla wielu typowych scenariuszy i użycia w tej sekcji opisano następujące wywołania zwrotne:
- [Odbierz aktualizację właściwości (chmura do urządzenia)](#receive-property-update-cloud-to-device)
- [Zgłoś aktualizację właściwości (urządzenie do chmury)](#report-a-property-update-device-to-cloud)
- [Wyślij dane telemetryczne (urządzenie do chmury)](#send-telemetry-device-to-cloud)
- [Odbierz polecenie wywołania zwrotnego aktualizacji z chmury i przetwórz je po stronie urządzenia (chmura do urządzenia)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Odpowiedz na aktualizację polecenia po stronie urządzenia (urządzenie do chmury)](#respond-to-command-update-on-the-device-side-device-to-cloud)

Poniższe przykłady są oparte na [przykładowej karcie czujnika środowiska](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor).

### <a name="receive-property-update-cloud-to-device"></a>Odbierz aktualizację właściwości (chmura do urządzenia)
Pierwszym krokiem jest zarejestrowanie funkcji wywołania zwrotnego:

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
Następnym krokiem jest zaimplementowanie funkcji wywołania zwrotnego w celu odczytania aktualizacji właściwości na urządzeniu:

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Zgłoś aktualizację właściwości (urządzenie do chmury)
W dowolnym momencie po utworzeniu składnika urządzenie może raportować właściwości w chmurze o stanie: 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Wyślij dane telemetryczne (urządzenie do chmury)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Odbierz polecenie wywołania zwrotnego aktualizacji z chmury i przetwórz je po stronie urządzenia (chmura do urządzenia)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Odpowiedz na aktualizację polecenia po stronie urządzenia (urządzenie do chmury)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi IoT Plug and Play Bridge, odwiedź repozytorium usługi [iot Plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) w witrynie GitHub.
