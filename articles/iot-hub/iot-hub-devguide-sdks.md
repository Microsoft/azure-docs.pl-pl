---
title: Informacje o zestawach SDK usługi Azure IoT | Microsoft Docs
description: Przewodnik dla deweloperów — informacje i linki do różnych zestawów SDK urządzeń i usług Azure IoT, których można użyć do kompilowania aplikacji urządzeń i aplikacji zaplecza.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 9921ed500f831f2a002335fe83eae8f1bb47ecd4
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095342"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Omówienie i używanie zestawów SDK usługi Azure IoT Hub

Istnieją dwie kategorie zestawów SDK (Software Development Kit) do pracy z IoT Hub:

* **Zestawy SDK urządzeń IoT Hub** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT przy użyciu klienta urządzenia lub klienta modułu. Te aplikacje wysyłają dane telemetryczne do centrum IoT i opcjonalnie odbierają komunikaty, zadania, metody lub aktualizacje bliźniaczych reprezentacji z centrum IoT.  Za pomocą klienta modułu można także tworzyć [moduły](../iot-edge/iot-edge-modules.md) do [Azure IoT Edge środowiska uruchomieniowego](../iot-edge/about-iot-edge.md).

* **Zestawy SDK usługi IoT Hub** umożliwiają tworzenie aplikacji zaplecza do zarządzania centrum IoT, a także opcjonalne wysyłanie komunikatów, planowanie zadań, wywoływanie metod bezpośrednich lub wysyłanie wymaganych aktualizacji właściwości do urządzeń lub modułów IoT.

Ponadto udostępniamy zestaw SDK do pracy z [usługą Device Provisioning](../iot-dps/about-iot-dps.md).

* **Zestawy SDK urządzeń aprowizacji** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT w celu komunikowania się z usługą Device Provisioning.

* **Zestawy SDK usługi aprowizacji** umożliwiają tworzenie aplikacji zaplecza do zarządzania rejestracjami w usłudze Device Provisioning.

Poznaj [zalety programowania przy użyciu zestawów SDK usługi Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="os-platform-and-hardware-compatibility"></a>Zgodność platformy i sprzętu systemu operacyjnego

Obsługiwane platformy dla zestawów SDK można znaleźć w temacie [Obsługa Platform SDK usługi Azure IoT](iot-hub-device-sdk-platform-support.md).

Aby uzyskać więcej informacji na temat zgodności zestawu SDK z określonymi urządzeniami sprzętowymi, zobacz [katalog urządzeń certyfikatu platformy Azure dla IoT](https://catalog.azureiotsolutions.com/) lub pojedyncze repozytorium.

## <a name="azure-iot-hub-device-sdks"></a>Zestawy SDK urządzeń IoT Hub platformy Azure

Zestawy SDK urządzeń Microsoft Azure IoT zawierają kod, który ułatwia tworzenie aplikacji łączących się z usługą i zarządzanych przez usługi Azure IoT Hub.

Zestaw SDK urządzeń IoT Hub platformy Azure dla platformy .NET: 

* Pobierz z narzędzia [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Przestrzeń nazw to Microsoft. Azure. Devices. clients, która zawiera IoT Hub klientów urządzeń (DeviceClient, ModuleClient).
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp)
* [Dokumentacja interfejsu API](/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Dokumentacja modułu](/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)


Zestaw SDK urządzeń IoT Hub platformy Azure dla osadzonego języka C (ANSI C-C99):
* [Tworzenie osadzonego zestawu SDK języka C](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Kod źródłowy](https://github.com/Azure/azure-sdk-for-c)
* [Wykres rozmiaru](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart) dla urządzeń z ograniczeniami.
* [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Identity/1.0.0/api/index.html)


Azure IoT Hub Device SDK dla języka C (ANSI C-C99):

* Instalowanie z [apt-get, MBED, Arduino IDE lub iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c)
* [Kompilowanie zestawu SDK urządzenia C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Dokumentacja interfejsu API](/azure/iot-hub/iot-c-sdk-ref/)
* [Dokumentacja modułu](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Przenoszenie zestawu SDK języka C na inne platformy](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentacja dla deweloperów](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) , która zawiera informacje na temat kompilacji krzyżowej, wprowadzenie na różnych platformach itp.
* [Informacje o zużyciu zasobów usługi Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Zestaw SDK urządzeń IoT Hub platformy Azure dla języka Java:

* Dodaj do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java)
* [Dokumentacja interfejsu API](/java/api/com.microsoft.azure.sdk.iot.device)
* [Dokumentacja modułu](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Zestaw SDK urządzeń IoT Hub platformy Azure dla Node.js:

* Zainstaluj z [npm](https://www.npmjs.com/package/azure-iot-device)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node)
* [Dokumentacja interfejsu API](/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Dokumentacja modułu](/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Zestaw SDK urządzeń IoT Hub platformy Azure dla języka Python:

* Zainstaluj z [PIP](https://pypi.org/project/azure-iot-device/)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python)
* [Dokumentacja interfejsu API](/python/api/azure-iot-device)

Zestaw SDK urządzeń usługi Azure IoT Hub dla systemu iOS:

* Zainstaluj z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Samples](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Dokumentacja interfejsu API: zobacz [Dokumentacja interfejsu API języka C](/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Zestawy SDK usługi Azure IoT Hub

Zestawy SDK usługi Azure IoT zawierają kod ułatwiający Tworzenie aplikacji, które współpracują bezpośrednio z IoT Hub w celu zarządzania urządzeniami i zabezpieczeniami.

Zestaw SDK usługi Azure IoT Hub dla platformy .NET:

* Pobierz z narzędzia [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Przestrzeń nazw to Microsoft. Azure. Devices, która zawiera klientów usługi IoT Hub (Registrymanager, serviceclients).
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp)
* [Dokumentacja interfejsu API](/dotnet/api/microsoft.azure.devices)

Zestaw SDK usługi Azure IoT Hub dla języka Java:

* Dodaj do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java)
* [Dokumentacja interfejsu API](/java/api/com.microsoft.azure.sdk.iot.service)

Zestaw SDK usługi Azure IoT Hub dla Node.js:

* Pobierz z [npm](https://www.npmjs.com/package/azure-iothub)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node)
* [Dokumentacja interfejsu API](/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Zestaw SDK usługi Azure IoT Hub dla języka Python:

* Pobierz z [PIP](https://pypi.python.org/pypi/azure-iot-hub/)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python/tree/master)
* [Dokumentacja interfejsu API](/python/api/azure-iot-hub)

Zestaw SDK usługi Azure IoT Hub dla języka C:

Zestaw SDK usługi Azure IoT dla języka C nie jest już objęty aktywnym programowaniem.
Będziemy nadal rozwiązywać krytyczne usterki, takie jak awarie, uszkodzenie danych i luki w zabezpieczeniach. NIE będziemy dodawać żadnych nowych funkcji ani naprawiać usterek, które nie są krytyczne.

Obsługa zestawu SDK usługi Azure IoT jest dostępna w językach wyższego poziomu ([C#](https://github.com/Azure/azure-iot-sdk-csharp), [Java](https://github.com/Azure/azure-iot-sdk-java), [Node](https://github.com/Azure/azure-iot-sdk-node), [Python](https://github.com/Azure/azure-iot-sdk-python)).

* Pobierz z [apt-get, MBED, Arduino IDE lub NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c)

Zestaw SDK usługi Azure IoT Hub dla systemu iOS:

* Zainstaluj z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Samples](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Zapoznaj się z plikami Readme w repozytoriach usługi GitHub, aby uzyskać informacje na temat używania języka i menedżerów pakietów specyficznych dla platformy do instalowania plików binarnych i zależności na komputerze deweloperskim.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure zestawy SDK aprowizacji

**Zestawy SDK udostępniania Microsoft Azure** umożliwiają udostępnianie urządzeń IoT Hub za pomocą [usługi Device Provisioning](../iot-dps/about-iot-dps.md).

Zestawy SDK urządzeń i usług platformy Azure dla języka C#:

* Pobierz z [zestawu SDK urządzeń](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) i [zestawu SDK usługi](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) z narzędzia NuGet.
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Dokumentacja interfejsu API](/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Zestawy SDK dla urządzeń i usług platformy Azure dla języka C:

* Instalowanie z [apt-get, MBED, Arduino IDE lub iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Dokumentacja interfejsu API](/azure/iot-hub/iot-c-sdk-ref/)

Zestawy SDK urządzeń i usług platformy Azure dla języka Java:

* Dodaj do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Dokumentacja interfejsu API](/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Zestawy SDK urządzeń i usług platformy Azure dla Node.js:

* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Dokumentacja interfejsu API](/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Pobierz [zestaw SDK urządzeń](https://badge.fury.io/js/azure-iot-provisioning-device) i [zestaw SDK usługi](https://badge.fury.io/js/azure-iot-provisioning-service) z npm

Zestawy SDK urządzeń i usług dla platformy Azure dla języka Python:

* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python)
* Pobierz [zestaw SDK](https://pypi.org/project/azure-iot-device/) i [zestaw SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) urządzenia z PIP

## <a name="next-steps"></a>Następne kroki

Zestawy SDK usługi Azure IoT oferują również zestaw narzędzi, które ułatwiają programowanie:

* [iothub-Diagnostics](https://github.com/Azure/iothub-diagnostics): międzyplatformowe narzędzie wiersza polecenia pomagające zdiagnozować problemy związane z połączeniem z IoT Hub.
* [Azure-IoT-Explorer](https://github.com/Azure/azure-iot-explorer): międzyplatformowa aplikacja klasyczna umożliwiająca łączenie się z IoT Hub i Dodawanie/zarządzanie/komunikacja z urządzeniami IoT.

Odpowiednie dokumenty dotyczące programowania przy użyciu zestawów SDK usługi Azure IoT:

* Informacje o [sposobach zarządzania łącznością i niezawodną obsługą komunikatów](iot-hub-reliability-features-in-sdks.md) przy użyciu zestawów SDK IoT Hub.
* Dowiedz się więcej na temat [opracowywania platform mobilnych](iot-hub-how-to-develop-for-mobile-devices.md) , takich jak iOS i Android.
* [Obsługa platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)

Inne tematy referencyjne w tym IoT Hub przewodniku dla deweloperów obejmują:

* [Punkty końcowe centrum IoT Hub](iot-hub-devguide-endpoints.md)
* [IoT Hub język zapytań dla bliźniaczych reprezentacji urządzeń, zadań i routingu komunikatów](iot-hub-devguide-query-language.md)
* [Limity przydziału i ograniczanie wydajności](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub obsługa MQTT](iot-hub-mqtt-support.md)
* [Dokumentacja interfejsu API REST IoT Hub](/rest/api/iothub/)
