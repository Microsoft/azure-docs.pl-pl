---
title: Bieżąca wersja Plug and Play IoT | Microsoft Docs
description: Dowiedz się, co obejmuje bieżąca wersja Plug and Play IoT.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4bd1bb93c9ce0f491c5bf1153917491b88d55109
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043718"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>Co znajduje się w bieżącej wersji Plug and Play IoT

Ten artykuł zawiera podsumowanie narzędzi, zestawów SDK i interfejsów API, które obsługują bieżącą wersję Plug and Play IoT. Pokazane numery wersji odzwierciedlają numer wersji w czasie Plug and Play IoT jest ogólnie dostępny. Numery wersji mogą narastać po wydaniu.

## <a name="modeling-language"></a>Język modelowania

[Digital bliźniaczych reprezentacji Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Aby dowiedzieć się więcej o tym, jak urządzenia Plug and Play IoT pracują z usługą DTDL, zobacz [konwencje Plug and Play IoT](concepts-convention.md).

## <a name="tools-and-utilities"></a>Narzędzia i programy narzędziowe

- Azure IoT Explorer 0.12.0.

    Aby dowiedzieć się więcej, zobacz [Instalowanie i korzystanie z programu Azure IoT Explorer](howto-use-iot-explorer.md).

- VS Code rozszerzenia 1.0.0.

    Aby dowiedzieć się więcej, zobacz [Instalowanie i używanie narzędzi autorskich DTDL](howto-use-dtdl-authoring-tools.md).

- 1.0.0 rozszerzenia programu Visual Studio 2019.

    Aby dowiedzieć się więcej, zobacz [Instalowanie i używanie narzędzi autorskich DTDL](howto-use-dtdl-authoring-tools.md).

- 0.10.0 rozszerzenia IoT interfejsu wiersza polecenia platformy Azure.

    Rozszerzenie Azure IoT zawiera polecenia, które ułatwiają certyfikowanie urządzeń. Zobacz: `az iot product -h`.

## <a name="libraries-and-sdks"></a>Biblioteki i zestawy SDK

Aby dowiedzieć się więcej na temat bibliotek i zestawów SDK, zobacz [Microsoft SDK for IoT Plug and Play](libraries-sdks.md).

- Zestaw SDK urządzeń C [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- Osadzony zestaw SDK urządzenia C w serwisie [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) zestawu SDK urządzenia .NET
- Zestaw SDK urządzeń Java [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Zestaw SDK urządzenia w języku Python [2.3.0 PIP](https://pypi.org/project/azure-iot-device/)
- Zestaw SDK urządzeń Node.js [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET — usługa IoT Hub [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java — IoT Hub usługi [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js — IoT Hub usługi [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python-IoT Hub/Digital bliźniaczych reprezentacji Service [PIP 2.2.3](https://pypi.org/project/azure-iot-hub)
- Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)parsera modelu DTDL.

## <a name="rest-apis"></a>Interfejsy API REST

Interfejs API REST [2020-09-30](/rest/api/iothub).

Aby dowiedzieć się więcej, zobacz [Przewodnik dla deweloperów Plug and Play IoT](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

Plug and Play IoT jest obsługiwana przez IoT Hub we wszystkich regionach. Plug and Play IoT jest obsługiwana tylko przez centra IoT dla warstwy Standardowa lub bezpłatna.

## <a name="announcements"></a>Anonse

Bieżące i poprzednie anonse Plug and Play IoT można znaleźć w następujących wpisach w blogu:

- [Odświeżanie publicznej wersji zapoznawczej (opublikowane w dniu 29 sierpnia 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Przygotuj i zatwierdź swoje urządzenia dla Plug and Play IoT (ogłoszone 26 sierpnia 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [Plug and Play IoT jest teraz dostępna w wersji zapoznawczej (ogłoszona 22 sierpnia 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Kompilowanie za pomocą usługi Azure IoT Central i Plug and Play IoT (ogłoszone 7 maja 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)