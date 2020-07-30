---
title: Biblioteki i zestawy SDK Plug and Play IoT
description: Informacje o bibliotekach urządzeń i usług dostępne do opracowania rozwiązań z obsługą Plug and Play IoT.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407799"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Zestawy Microsoft SDK dla IoT Plug and Play

Biblioteki Plug and Play IoT i zestawy SDK umożliwiają deweloperom tworzenie rozwiązań IoT przy użyciu różnych języków programowania na wielu platformach. Poniższa tabela zawiera linki do przykładów i przewodników Szybki Start, które ułatwiają rozpoczęcie pracy:

## <a name="device-sdks-ga"></a>Zestawy SDK urządzeń (GA)

| Język | Pakiet | Repozytorium kodu | Samples | Szybki start | Tematy pomocy |
|---|---|---|---|---|---|
| C-urządzenie | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Samples](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Łączenie z usługą IoT Hub](quickstart-connect-device-c.md) | [Odwołanie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET — urządzenie | [1.27.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Łączenie z usługą IoT Hub](quickstart-connect-device-csharp.md) | [Odwołanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java — urządzenie | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Łączenie z usługą IoT Hub](quickstart-connect-device-java.md) | [Odwołanie](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python — urządzenie | [PIP 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Łączenie z usługą IoT Hub](quickstart-connect-device-python.md) | [Odwołanie](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Węzeł-urządzenie | [npm 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Łączenie z usługą IoT Hub](quickstart-connect-device-node.md) | [Odwołanie](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>Zestawy SDK urządzeń (wersja zapoznawcza)

| Język | Repozytorium/przykłady kodu |
|---|---|
|Zestaw Azure SDK dla programu Embedded| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Azure RTO IoT — oprogramowanie pośredniczące| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Przewodniki dotyczące rozpoczynania pracy z usługą Azure RTO | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>Zestawy SDK usługi (wersja zapoznawcza)

| Język | Pakiet | Repozytorium kodu | Samples | Szybki start | Tematy pomocy |
|---|---|---|---|---|---|
| .NET — wersja zapoznawcza usługi IoT Hub | [NuGet 1.27.1 — wersja zapoznawcza — 002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Samples](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | Brak | Brak |
| Java — wersja zapoznawcza usługi IoT Hub | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | Brak | Brak |
| Wersja zapoznawcza usługi Node-IoT Hub | [npm 1.12.4-PnP-Refresh. 4](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | Brak | Brak |
| Python — IoT Hub/wersja zapoznawcza usługi Digital bliźniaczych reprezentacji | [PIP 2.2.1 RC1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Posługiwanie się IoT Hub interfejsem API Digital bliźniaczych reprezentacji](quickstart-service-python.md) | Brak |
| Node-Digital bliźniaczych reprezentacji Service w wersji zapoznawczej | [npm 1.0.0-PnP-Refresh. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Posługiwanie się IoT Hub interfejsem API Digital bliźniaczych reprezentacji](quickstart-service-node.md) | Brak |

## <a name="next-steps"></a>Następne kroki

Aby wypróbować zestawy SDK i biblioteki, zapoznaj się z [przewodnikiem dla deweloperów](concepts-developer-guide.md) oraz [Szybki Start](quickstart-connect-device-c.md) i [przewodniki po usłudze](quickstart-service-node.md).
