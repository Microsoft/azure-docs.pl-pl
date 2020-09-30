---
title: Bieżąca wersja Plug and Play IoT | Microsoft Docs
description: Dowiedz się, co obejmuje bieżąca wersja Plug and Play IoT.
author: dominicbetts
ms.author: dobett
ms.date: 09/19/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cfc9ca14a4b344423a27d9683114de5275fb20b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583558"
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

    Aby dowiedzieć się więcej, zobacz [Instalowanie i używanie rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure](howto-use-iot-pnp-cli.md).

    > [!TIP]
    > Rozszerzenie Azure IoT zawiera polecenia, które ułatwiają certyfikowanie urządzeń. Zobacz: `az iot product -h`.



## <a name="libraries-and-sdks"></a>Biblioteki i zestawy SDK

Aby dowiedzieć się więcej na temat bibliotek i zestawów SDK, zobacz [Microsoft SDK for IoT Plug and Play](libraries-sdks.md).

- Zestaw SDK urządzeń C [VcPkg 2020-07-19](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)
- Zestaw SDK urządzenia .NET [NuGet Microsoft. Azure. Devices. Client 1.30.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
- Java Device SDK [Maven IoT-Device-Client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Zestaw SDK urządzenia w języku Python [PIP Azure-IoT-Device v 2.2.0](https://pypi.org/project/azure-iot-device/)
- Node.js zestawu SDK urządzeń [npm Azure-IoT-Device 1.17.1](https://www.npmjs.com/package/azure-iot-device)
- .NET — usługa IoT Hub [pakiet NuGet Microsoft. Azure. Devices 1.22.0](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
- Java-IoT Hub Service [Maven IoT-Service-Client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-service-client)
- Node.js-IoT Hub Service [npm Azure-iothub 1.12.5](https://www.npmjs.com/package/azure-iothub)
- Python-IoT Hub/Digital bliźniaczych reprezentacji Service [PIP Azure-IoT-Hub 2.2.2](https://pypi.org/project/azure-iot-hub/)
- Pakiet [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)parsera modelu DTDL.

### <a name="preview"></a>Wersja zapoznawcza

- Zestaw Azure SDK dla programu Embedded [1.0.0 — wersja zapoznawcza 5](https://github.com/Azure/azure-sdk-for-c/releases/tag/1.0.0-preview.5)
- Azure RTO IoT — oprogramowanie pośredniczące [V6.0_beta3](https://github.com/azure-rtos/azure-iot-preview/releases/tag/v6.0_beta3)

## <a name="rest-apis"></a>Interfejsy API REST

Interfejs API REST [2020-09-30](https://docs.microsoft.com/rest/api/iothub).

Aby dowiedzieć się więcej, zobacz [Przewodnik dla deweloperów Plug and Play IoT](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

Plug and Play IoT jest obsługiwana przez IoT Hub we wszystkich regionach. Plug and Play IoT jest obsługiwana tylko przez centra IoT dla warstwy Standardowa lub bezpłatna.

## <a name="announcements"></a>Anonse

Bieżące i poprzednie anonse Plug and Play IoT można znaleźć w następujących wpisach w blogu:

- [Odświeżanie publicznej wersji zapoznawczej (opublikowane w dniu 29 sierpnia 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Przygotuj i zatwierdź swoje urządzenia dla Plug and Play IoT (ogłoszone 26 sierpnia 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [Plug and Play IoT jest teraz dostępna w wersji zapoznawczej (ogłoszona 22 sierpnia 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Kompilowanie za pomocą usługi Azure IoT Central i Plug and Play IoT (ogłoszone 7 maja 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

