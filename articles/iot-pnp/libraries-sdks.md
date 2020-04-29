---
title: Biblioteki i zestawy SDK Plug and Play IoT
description: Informacje o bibliotekach urządzeń i usług dostępne do opracowania rozwiązań z obsługą Plug and Play IoT.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064325"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Biblioteki i zestawy SDK Plug and Play IoT

Biblioteki Plug and Play IoT i zestawy SDK umożliwiają deweloperom tworzenie rozwiązań IoT przy użyciu różnych języków programowania na wielu platformach. Poniższa tabela zawiera linki do przykładów i przewodników Szybki Start, które ułatwiają rozpoczęcie pracy:

## <a name="microsoft-supported-libraries-and-sdks"></a>Biblioteki i zestawy SDK obsługiwane przez firmę Microsoft

| Platforma | Biblioteka/pakiet | Kod źródłowy | Przykład | Szybki start | Dokumentacja |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Zestaw SDK urządzeń w systemie apt — get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Przykłady klienta Digital bliźniaczy](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-c-linux.md) | [Dokumentacja](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Zestaw SDK urządzeń w systemie Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Przykłady klienta Digital bliźniaczy](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-c-windows.md) | [Dokumentacja](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Zestaw SDK urządzeń przy OSADZAniu](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Przykłady klienta Digital bliźniaczy](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Dokumentacja](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Zestaw SDK urządzeń w środowisku IDE Arduino](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Przykłady klienta Digital bliźniaczy](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Dokumentacja](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Zestaw SDK urządzeń w systemie CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Przykłady klienta Digital bliźniaczy](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Dokumentacja](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Cyfrowe próbki dwuosiowe](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-csharp.md) | [Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Cyfrowe próbki dwuosiowe](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-java.md) | [Dokumentacja](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Cyfrowe próbki dwuosiowe](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Łączenie z usługą IoT Hub](./quickstart-connect-pnp-device-node.md) | [Dokumentacja](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Obsługa IoT Hub

Możliwości urządzenia IoT Plug and Play są obsługiwane tylko przez [centra IoT i warstwa standardowa warstwy Bezpłatna](../iot-hub/iot-hub-scaling.md).

## <a name="next-steps"></a>Następne kroki

Oprócz zestawów SDK i bibliotek urządzeń można używać interfejsów API REST do korzystania z repozytoriów modelu.