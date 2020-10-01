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
ms.openlocfilehash: 75ad3cef85809caee06a206ece0538c2b4f13299
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613377"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Zestawy Microsoft SDK dla IoT Plug and Play

Biblioteki Plug and Play IoT i zestawy SDK umożliwiają deweloperom tworzenie rozwiązań IoT przy użyciu różnych języków programowania na wielu platformach. Poniższa tabela zawiera linki do przykładów i przewodników Szybki Start, które ułatwiają rozpoczęcie pracy:

## <a name="device-sdks"></a>Zestawy SDK urządzeń

| Język | Pakiet | Repozytorium kodu | Samples | Szybki start | Dokumentacja |
|---|---|---|---|---|---|
| C-urządzenie | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Samples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Łączenie z usługą IoT Hub](quickstart-connect-device-c.md) | [Odwołanie](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET — urządzenie | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Łączenie z usługą IoT Hub](quickstart-connect-device-csharp.md) | [Odwołanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet&preserve-view=true) |
| Java — urządzenie | [Maven 1.25.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Łączenie z usługą IoT Hub](quickstart-connect-device-java.md) | [Odwołanie](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable&preserve-view=true) |
| Python — urządzenie | [2.3.0 PIP](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Łączenie z usługą IoT Hub](quickstart-connect-device-python.md) | [Odwołanie](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python&preserve-view=true) |
| Węzeł-urządzenie | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Łączenie z usługą IoT Hub](quickstart-connect-device-node.md) | [Odwołanie](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest&preserve-view=true) |
| Embedded C-Device | Nie dotyczy | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Samples](howto-use-embedded-c.md#samples) | [Jak używać osadzonego języka C](howto-use-embedded-c.md) | Nie dotyczy

## <a name="service-sdks"></a>Zestawy SDK usług

| Platforma  | Pakiet | Repozytorium kodu | Samples | Szybki start | Dokumentacja |
|---|---|---|---|---|---|
| .NET — usługa IoT Hub | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | Nie dotyczy | [Odwołanie](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet&preserve-view=true) |
| Java — usługa IoT Hub | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | Nie dotyczy | [Odwołanie](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable&preserve-view=true) |
| Usługa IoT Hub węzła | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | Nie dotyczy | [Odwołanie](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest&preserve-view=true) |
| Python — usługa Digital bliźniaczych reprezentacji | [PIP 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Posługiwanie się IoT Hub interfejsem API Digital bliźniaczych reprezentacji](quickstart-service-python.md) | Nie dotyczy |
| Node-Digital bliźniaczych reprezentacji Service | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Posługiwanie się IoT Hub interfejsem API Digital bliźniaczych reprezentacji](quickstart-service-node.md) | Nie dotyczy |

## <a name="next-steps"></a>Następne kroki

Aby wypróbować zestawy SDK i biblioteki, zapoznaj się z  [przewodnikiem dla deweloperów](concepts-developer-guide-device-csharp.md) oraz [Szybki Start](quickstart-connect-device-c.md) i [przewodniki po usłudze](quickstart-service-node.md).
