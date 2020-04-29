---
title: Przewodnik dla deweloperów — IoT Plug and Play Preview | Microsoft Docs
description: Opis modelowania urządzeń dla deweloperów Plug and Play IoT
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77605223"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Przewodnik dla deweloperów modelu IoT Plug and Play w wersji zapoznawczej

Usługa IoT Plug and Play w wersji zapoznawczej umożliwia tworzenie urządzeń, które anonsują swoje możliwości w aplikacjach Azure IoT. Urządzenia IoT Plug and Play nie wymagają ręcznej konfiguracji, gdy klient łączy je z aplikacjami obsługującymi Plug and Play IoT. IoT Central to przykład aplikacji z obsługą Plug and Play IoT.

Aby skompilować urządzenie IoT Plug and Play, należy utworzyć opis urządzenia. Opis jest wykonywany przy użyciu prostego języka definicji o nazwie Digital bliźniaczych reprezentacji Definition Language (DTDL).

## <a name="device-capability-model"></a>Model możliwości urządzenia

Za pomocą DTDL można utworzyć _model możliwości urządzenia_ do opisywania części urządzenia. Typowe urządzenie IoT składa się z:

- Części niestandardowe, które są elementami, które sprawiają, że urządzenie jest unikatowe.
- Standardowe części, które są wspólne dla wszystkich urządzeń.

Te części nazywają się _interfejsami_ w modelu możliwości urządzeń. Interfejsy definiują szczegóły każdej części implementującej Twoje urządzenie.

Poniższy przykład przedstawia model możliwości urządzenia dla termostatu:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Model możliwości ma niektóre wymagane pola:

- `@id`: unikatowy identyfikator w postaci prostej jednolitej nazwy zasobu.
- `@type`: deklaruje, że ten obiekt jest modelem możliwości.
- `@context`: określa wersję DTDL używaną dla modelu możliwości.
- `implements`: Wyświetla listę interfejsów, które implementuje urządzenie.

Każdy wpis na liście interfejsów w sekcji Implements ma:

- `name`: Nazwa programowania interfejsu.
- `schema`: interfejs implementujący model możliwości.

Istnieją dodatkowe pola opcjonalne, za pomocą których można dodać więcej szczegółów do modelu możliwości, takich jak wyświetlana nazwa i opis. Interfejsy, które są zadeklarowane w ramach modelu możliwości, można traktować jako składniki urządzenia. W publicznej wersji zapoznawczej lista interfejsów może zawierać tylko jeden wpis na schemat.

## <a name="interface"></a>Interface

Dzięki DTDL można opisać możliwości urządzenia przy użyciu interfejsów. Interfejsy opisują _Właściwości_, dane _telemetryczne_i _polecenia_ , które są implementowane przez część urządzenia:

- `Properties`. Właściwości to pola danych, które reprezentują stan urządzenia. Użyj właściwości, aby reprezentować stan trwały urządzenia, taki jak stan włączony pompy chłodzącej. Właściwości mogą również reprezentować podstawowe właściwości urządzenia, takie jak wersja oprogramowania układowego urządzenia. Można zadeklarować właściwości jako tylko do odczytu lub do zapisu.
- `Telemetry`. Pola telemetrii przedstawiają pomiary z czujników. Za każdym razem, gdy urządzenie przyjmuje pomiar czujnika, należy wysłać zdarzenie telemetrii zawierające dane czujnika.
- `Commands`. Polecenia reprezentują metody, które użytkownicy urządzenia mogą wykonywać na urządzeniu. Na przykład polecenie Reset lub polecenie umożliwiające włączenie lub wyłączenie wentylatora.

Poniższy przykład pokazuje interfejs dla urządzenia z termostatem:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Interfejs ma niektóre wymagane pola:

- `@id`: unikatowy identyfikator w postaci prostej jednolitej nazwy zasobu.
- `@type`: deklaruje, że ten obiekt jest interfejsem.
- `@context`: określa wersję DTDL użytą dla interfejsu.
- `contents`: Wyświetla właściwości, dane telemetryczne i polecenia wchodzące w skład Twojego urządzenia.

W tym prostym przykładzie istnieje tylko jedno pole telemetrii. Minimalny opis pola ma:

- `@type`: określa typ możliwości: `Telemetry`, `Property`, lub. `Command`
- `name`: zawiera nazwę wartości telemetrii.
- `schema`: określa typ danych telemetrii. Ta wartość może być typem pierwotnym, takim jak Double, Integer, Boolean lub String. Obsługiwane są również złożone typy obiektów, tablice i mapy.

Inne opcjonalne pola, takie jak nazwa wyświetlana i opis, umożliwiają dodanie więcej szczegółów do interfejsu i możliwości.

### <a name="properties"></a>Właściwości

Domyślnie właściwości są tylko do odczytu. Właściwości tylko do odczytu oznaczają, że urządzenie zgłasza aktualizacje wartości właściwości do centrum IoT Hub. Centrum IoT Hub nie może ustawić wartości właściwości tylko do odczytu.

Można także oznaczyć właściwość jako zapisywalną w interfejsie. Urządzenie może odebrać aktualizację właściwości z możliwością zapisu z Centrum IoT, a także aktualizować aktualizacje wartości właściwości do centrum.

Urządzenia nie muszą być połączone, aby ustawić wartości właściwości. Zaktualizowane wartości są transferowane, gdy urządzenie następnym nawiąże połączenie z centrum. To zachowanie dotyczy zarówno właściwości tylko do odczytu, jak i do zapisu.

Nie używaj właściwości, aby wysyłać dane telemetryczne z urządzenia. Na przykład właściwość ReadOnly, taka jak `temperatureSetting=80` powinna oznaczać, że temperatura urządzenia została ustawiona na 80, a urządzenie próbuje uzyskać dostęp do tej temperatury lub pozostać w tej temperaturze.

W przypadku właściwości zapisywalnych aplikacja urządzenia zwraca kod stanu żądanego stanu, wersję i opis, aby wskazać, czy został on odebrany i zastosował wartość właściwości.

### <a name="telemetry"></a>Telemetria

Domyślnie usługa IoT Hub kieruje wszystkie komunikaty telemetryczne z urządzeń do [wbudowanego punktu końcowego (**komunikaty/zdarzenia**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) zgodnego z [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/).

Możesz użyć [niestandardowych punktów końcowych IoT Hub i reguł routingu](../iot-hub/iot-hub-devguide-messages-d2c.md) , aby wysyłać dane telemetryczne do innych miejsc docelowych, takich jak BLOB Storage czy innych centrów zdarzeń. Reguły routingu używają właściwości komunikatów do wybierania komunikatów.

### <a name="commands"></a>Polecenia

Polecenia są synchroniczne lub asynchroniczne. Polecenie synchroniczne musi być wykonywane w ciągu 30 sekund domyślnie, a urządzenie musi być połączone po nadejściu polecenia. Jeśli urządzenie jest w czasie odpowiedzi lub urządzenie nie jest połączone, polecenie kończy się niepowodzeniem.

Korzystaj z poleceń asynchronicznych dla długotrwałych operacji. Urządzenie wysyła informacje o postępie przy użyciu komunikatów telemetrycznych. Te komunikaty postępu mają następujące właściwości nagłówka:

- `iothub-command-name`: nazwa polecenia, na przykład `UpdateFirmware`.
- `iothub-command-request-id`: Identyfikator żądania wygenerowany po stronie serwera i wysyłany do urządzenia w wywołaniu początkowym.
- `iothub-interface-id`: Identyfikator interfejsu, na którym jest zdefiniowane to polecenie, na przykład `urn:example:AssetTracker:1`.
 `iothub-interface-name`: nazwa wystąpienia tego interfejsu, na przykład `myAssetTracker`.
- `iothub-command-statuscode`: kod stanu zwrócony z urządzenia, na przykład `202`.

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Plug and Play IoT ułatwia anonsowanie możliwości urządzenia. Gdy urządzenie Plug and Play IoT zostanie połączone z IoT Hub, należy zarejestrować model możliwości urządzenia. Rejestracja umożliwia klientom korzystanie z możliwości Plug and Play IoT na urządzeniu.

W tym przewodniku pokazano, jak zarejestrować urządzenie przy użyciu zestawu SDK urządzeń Azure IoT dla języka C.

Dla każdego interfejsu, który implementuje urządzenie, należy utworzyć interfejs i połączyć go z jego implementacją.

W przypadku interfejsu termostatu pokazanego wcześniej przy użyciu zestawu C SDK utworzysz interfejs termostatu i podłącz go do jego implementacji:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Powtórz ten kod dla każdego interfejsu, który implementuje urządzenie.

Po utworzeniu interfejsu Zarejestruj model możliwości urządzenia i interfejsy w centrum IoT:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Korzystanie z urządzenia

Plug and Play IoT umożliwia korzystanie z urządzeń zarejestrowanych w usłudze IoT Hub. Można na przykład uzyskać dostęp do właściwości i poleceń urządzenia bezpośrednio.

Aby użyć urządzenia Plug and Play IoT, które jest połączone z Centrum IoT, użyj interfejsu API REST IoT Hub lub jednego z zestawów SDK języka IoT. W poniższych przykładach użyto interfejsu API REST IoT Hub. Bieżąca wersja interfejsu API to `2019-07-01-preview`. Dołącz `?api-version=2019-07-01-preview` do wywołań pi Rest.

Aby uzyskać wartość właściwości urządzenia, taką jak wersja oprogramowania układowego (`fwVersion`) w `DeviceInformation` interfejsie termostatu, należy użyć interfejsu API REST Digital bliźniaczych reprezentacji.

Jeśli urządzenie z termostatem zostanie `t-123`wywołane, uzyskasz wszystkie właściwości wszystkich interfejsów zaimplementowanych przez urządzenie przy użyciu interfejsu API REST Get:

```REST
GET /digitalTwins/t-123/interfaces
```

Ogólnie rzecz biorąc, wszystkie właściwości wszystkich interfejsów są dostępne przy użyciu tego szablonu interfejsu API `{device-id}` REST, gdzie jest identyfikatorem urządzenia:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Jeśli znasz nazwę interfejsu, na przykład `deviceInformation`, i chcesz uzyskać właściwości dla tego konkretnego interfejsu, przenosząc żądanie do określonego interfejsu według nazwy:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

Bardziej ogólnie rzecz biorąc, można uzyskać dostęp do właściwości dla określonego interfejsu za pomocą tego szablonu `device-id` interfejsu API REST, gdzie jest identyfikatorem urządzenia i `{interface-name}` jest nazwą interfejsu:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Polecenia IoT Plug and Play Device można wywołać bezpośrednio. Jeśli `Thermostat` interfejs w `t-123` urządzeniu ma `restart` polecenie, możesz wywołać go przy użyciu wywołania interfejsu API REST:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Bardziej ogólnie rzecz biorąc, polecenia można wywołać za poorednictwem szablonu interfejsu API REST:

- `device-id`: Identyfikator urządzenia.
- `interface-name`: nazwa interfejsu z sekcji Implements w modelu możliwości urządzenia.
- `command-name`: nazwa polecenia.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak modelowanie urządzeń, Oto kilka dodatkowych zasobów:

- [Język definicji Digital bliźniaczy (DTDL)](https://aka.ms/DTDL)
- [C (zestaw SDK urządzenia)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [Interfejs API REST usługi IoT](https://docs.microsoft.com/rest/api/iothub/device)
