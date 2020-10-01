---
title: Przewodnik dla deweloperów usług — IoT Plug and Play | Microsoft Docs
description: Opis Plug and Play IoT dla deweloperów usług
author: dominicbetts
ms.author: dobett
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f082e4d4c6c71e460842f80a5aa17130b6a41279
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614227"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Przewodnik dewelopera usługi IoT Plug and Play

Usługa IoT Plug and Play umożliwia tworzenie inteligentnych urządzeń, które anonsują swoje możliwości w aplikacjach Azure IoT. Urządzenia IoT Plug and Play nie wymagają ręcznej konfiguracji, gdy klient łączy je z aplikacjami obsługującymi Plug and Play IoT.

Plug and Play IoT umożliwia korzystanie z urządzeń, które ogłosiły swój identyfikator modelu w usłudze IoT Hub. Można na przykład uzyskać dostęp do właściwości i poleceń urządzenia bezpośrednio.

Aby użyć urządzenia Plug and Play IoT, które jest połączone z Centrum IoT, użyj jednego z zestawów SDK usługi IoT lub interfejsu API REST IoT Hub:

## <a name="service-sdks"></a>Zestawy SDK usług

Użyj zestawów SDK usługi Azure IoT w rozwiązaniu, aby móc korzystać z urządzeń i modułów. Można na przykład użyć zestawów SDK usługi do odczytu i aktualizacji właściwości przędzy i poleceń Invoke. Obsługiwane języki to C#, Java, Node.js i Python.

Zestawy SDK usług umożliwiają dostęp do informacji o urządzeniach, takich jak aplikacje klasyczne lub sieci Web. Zestawy SDK usługi obejmują dwie przestrzenie nazw i modele obiektów, których można użyć do pobrania identyfikatora modelu:

- Klient usługi IoT Hub. Ta usługa ujawnia Identyfikator modelu jako właściwość przędzy urządzenia.

- Klient usługi Digital bliźniaczych reprezentacji. Nowy interfejs API Digital bliźniaczych reprezentacji działa na konstrukcjach wysokiego poziomu, takich jak składniki, właściwości i polecenia, które są zdefiniowane w modelu języka Digital bliźniaczych reprezentacji Definition. Interfejsy API cyfrowego przędzy ułatwiają konstruktorom rozwiązań tworzenie rozwiązań Plug and Play IoT.

| Platforma | Klient usługi IoT Hub | Klient usługi Digital bliźniaczych reprezentacji |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [Dokumentacja](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twin.modelid?view=azure-dotnet#Microsoft_Azure_Devices_Shared_Twin_ModelId&preserve-view=true) <br/> [Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Dokumentacja](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Samples](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Dokumentacja](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Przykład](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Dokumentacja](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [Przykład](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [Dokumentacja](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Przykład](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Dokumentacja](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [Przykład](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="rest-api"></a>Interfejs API REST

Poniższe przykłady używają IoT Hub interfejsu API REST do współpracy z podłączonym urządzeniem IoT Plug and Play. Bieżąca wersja interfejsu API to `2020-09-30` . Dołącz `?api-version=2020-09-30` do wywołań pi Rest.

> [!NOTE]
> Moduł bliźniaczych reprezentacji nie jest obecnie obsługiwany przez `digitalTwins` interfejs API.

Jeśli urządzenie z termostatem zostanie wywołane `t-123` , uzyskasz wszystkie właściwości wszystkich interfejsów zaimplementowanych przez urządzenie przy użyciu interfejsu API REST Get:

```REST
GET /digitalTwins/t-123
```

To wywołanie będzie zawierać właściwość JSON `$metadata.$model` z identyfikatorem modelu ogłoszonym przez urządzenie.

Wszystkie właściwości wszystkich interfejsów są dostępne z `GET /DigitalTwin/{device-id}` szablonem interfejsu API REST, gdzie `{device-id}` jest identyfikatorem urządzenia:

```REST
GET /digitalTwins/{device-id}
```

Polecenia IoT Plug and Play Device można wywołać bezpośrednio. Jeśli `Thermostat` składnik na `t-123` urządzeniu ma `restart` polecenie, można wywołać go przy użyciu wywołania interfejsu API REST:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Bardziej ogólnie rzecz biorąc, polecenia można wywołać za poorednictwem szablonu interfejsu API REST:

- `device-id`: Identyfikator urządzenia.
- `component-name`: nazwa interfejsu z sekcji Implements w modelu możliwości urządzenia.
- `command-name`: nazwa polecenia.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak modelowanie urządzeń, Oto kilka dodatkowych zasobów:

- [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (zestaw SDK urządzenia)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [Interfejs API REST usługi IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Składniki modelu](./concepts-components.md)
- [Instalowanie narzędzi autorskich DTDL i korzystanie z nich](howto-use-dtdl-authoring-tools.md)
