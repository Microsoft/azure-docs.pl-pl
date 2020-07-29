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
ms.openlocfilehash: 3331a0a9a8d3fb5d028d801d334daf2dbfa25235
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337334"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Biblioteki i zestawy SDK Plug and Play IoT

Biblioteki Plug and Play IoT i zestawy SDK umożliwiają deweloperom tworzenie rozwiązań IoT przy użyciu różnych języków programowania na wielu platformach. Poniższa tabela zawiera linki do przykładów i przewodników Szybki Start, które ułatwiają rozpoczęcie pracy:

## <a name="microsoft-sdks-for-iot-plug-and-play"></a>Zestawy Microsoft SDK dla IoT Plug and Play

**Zestawy SDK urządzeń**

| Język | Pakiet | Repozytorium kodu | Samples | Szybki start | Dokumentacja |
|---|---|---|---|---|---|
| C-urządzenie | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Samples](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Łączenie z usługą IoT Hub](quickstart-connect-device-c.md) | [Odwołanie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET — urządzenie | [1.27.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Łączenie z usługą IoT Hub](quickstart-connect-device-csharp.md) | [Odwołanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java — urządzenie | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Łączenie z usługą IoT Hub](quickstart-connect-device-java.md) | [Odwołanie](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python — urządzenie | [PIP 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Łączenie z usługą IoT Hub](quickstart-connect-device-python.md) | [Odwołanie](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Węzeł-urządzenie | [npm 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Łączenie z usługą IoT Hub](quickstart-connect-device-node.md) | [Odwołanie](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

**Zestawy SDK usługi (wersja zapoznawcza)**

| Język | Pakiet | Repozytorium kodu | Samples | Szybki start | Dokumentacja |
|---|---|---|---|---|---|
| .NET — wersja zapoznawcza usługi IoT Hub | [NuGet 1.27.1 — wersja zapoznawcza — 002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Samples](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | nie dotyczy | [Odwołanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet) |
| Java — wersja zapoznawcza usługi IoT Hub | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | nie dotyczy | [Odwołanie](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable) |
| Python — IoT Hub/wersja zapoznawcza usługi Digital bliźniaczych reprezentacji | [PIP 2.2.1 RC1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Posługiwanie się IoT Hub interfejsem API Digital bliźniaczych reprezentacji](quickstart-service-python.md) | [Odwołanie](https://docs.microsoft.com/python/api/azure-iot-hub/?view=azure-python) |
| Node-IoT Hub/Digital bliźniaczych reprezentacji Service Preview | [npm 1.0.0-PnP-Refresh. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Posługiwanie się IoT Hub interfejsem API Digital bliźniaczych reprezentacji](quickstart-service-node.md) | [Odwołanie](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest) |

## <a name="next-steps"></a>Następne kroki

Aby wypróbować zestawy SDK i biblioteki, zapoznaj się z [przewodnikiem dla deweloperów](concepts-developer-guide.md) oraz [Szybki Start](quickstart-connect-device-c.md) i [przewodniki po usłudze](quickstart-service-node.md).
