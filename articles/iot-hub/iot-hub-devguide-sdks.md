---
title: Opis zestawów SDK usługi Azure IoT | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — informacje i łącza do różnych zestawów SDK urządzeń i usług Azure IoT, których można używać do tworzenia aplikacji na urządzenia i aplikacji zaplecza.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom: mqtt
ms.openlocfilehash: 68186e777051e77deca7b6e5891eb392cac23260
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729032"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Opis zestawów SDK usługi Azure IoT Hub i korzystania z niej

Istnieją dwie kategorie zestawów do tworzenia oprogramowania (SDK) do pracy z Centrum IoT Hub:

* **SDK urządzeń usługi IoT Hub** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT przy użyciu klienta urządzenia lub klienta modułu. Te aplikacje wysyłają dane telemetryczne do centrum IoT i opcjonalnie odbierają komunikaty, zadania, metody lub aktualizacje bliźniaczych reprezentacji z centrum IoT.  Można również użyć klienta modułu do tworzenia [modułów](../iot-edge/iot-edge-modules.md) dla [środowiska uruchomieniowego usługi Azure IoT Edge](../iot-edge/about-iot-edge.md).

* **Zestawów SDK usług usługi IoT Hub** umożliwiają tworzenie aplikacji zaplecza do zarządzania centrum IoT hub i opcjonalnie wysyłać wiadomości, planować zadania, wywoływać metody bezpośrednie lub wysyłać żądane aktualizacje właściwości do urządzeń lub modułów IoT.

Ponadto udostępniamy również zestaw zestawów SDK do pracy z [usługą aprowizacji urządzeń.](../iot-dps/about-iot-dps.md)
* **SDK urządzeń inicjowania obsługi administracyjnej** umożliwiają tworzenie aplikacji uruchamianych na urządzeniach IoT w celu komunikowania się z usługą inicjowania obsługi administracyjnej urządzeń.

* **Aprowizuj usługi SDK** umożliwiają tworzenie aplikacji zaplecza do zarządzania rejestracjami w usłudze inicjowania obsługi administracyjnej urządzeń.

Dowiedz się więcej o [korzyściach płynących z tworzenia przy użyciu zestawów SDK usługi Azure IoT.](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Kompatybilność platform systemu operacyjnego i sprzętu

Obsługiwane platformy dla zestawów SDK można znaleźć w [pomocy technicznej platformy Azure IoT SDKs.](iot-hub-device-sdk-platform-support.md)

Aby uzyskać więcej informacji na temat zgodności zestawu SDK z określonymi urządzeniami sprzętowymi, zobacz [katalog urządzeń certyfikatu platformy Azure dla IoT](https://catalog.azureiotsolutions.com/) lub indywidualne repozytorium.

## <a name="azure-iot-hub-device-sdks"></a>Zestaw SDK urządzeń usługi Azure IoT Hub

ZestawY SDK urządzeń IoT platformy Microsoft Azure zawierają kod ułatwiający tworzenie aplikacji, które łączą się z usługami Usługi Azure IoT Hub i są zarządzane przez usługi Usługi Azure IoT Hub.

Zestaw SDK urządzenia usługi Azure IoT Hub dla platformy .NET: 

* Pobierz z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Obszar nazw to Microsoft.Azure.Devices.Clients, który zawiera klientów urządzeń centrum IoT (DeviceClient, ModuleClient).
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp)
* [Odwołanie API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Odwołanie do modułu](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Zestaw SDK urządzenia usługi Azure IoT Hub dla języka C (ANSI C - C99):

* Zainstaluj z [apt-get, MBED, Arduino IDE lub iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c)
* [Kompilowanie zestawu SDK urządzenia C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Odwołanie API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Odwołanie do modułu](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Przenoszenie SDK C na inne platformy](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentacja dewelopera,](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) aby uzyskać informacje na temat cross-compiling, pierwsze kroki na różnych platformach, itp.
* [Informacje o zużyciu zasobów SDK usługi Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Zestaw SDK urządzenia usługi Azure IoT Hub dla środowiska Java: 

* Dodaj do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java)
* [Odwołanie API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Odwołanie do modułu](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Zestaw SDK urządzenia usługi Azure IoT Hub dla pliku Node.js: 

* Zainstaluj z [npm](https://www.npmjs.com/package/azure-iot-device)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node)
* [Odwołanie API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Odwołanie do modułu](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Zestaw SDK urządzenia usługi Azure IoT Hub dla języka Python: 

* Zainstaluj z [pip](https://pypi.org/project/azure-iot-device/)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python)
* [Odwołanie API](https://docs.microsoft.com/python/api/azure-iot-device)

Zestaw SDK urządzenia usługi Azure IoT Hub dla systemu iOS: 

* Zainstaluj z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Samples](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Odwołanie do interfejsu API: zobacz [odwołanie do interfejsu API języka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Zestaw SDK usługi Azure IoT Hub

ZestawY SDK usługi Azure IoT zawierają kod ułatwiający tworzenie aplikacji, które współdziałają bezpośrednio z Centrum IoT hub w celu zarządzania urządzeniami i zabezpieczeniami.

Zestaw SDK usługi Azure IoT Hub dla platformy .NET:

* Pobierz z [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Obszar nazw jest Microsoft.Azure.Devices, który zawiera klientów usługi Centrum IoT (RegistryManager, ServiceClients).
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp)
* [Odwołanie API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Zestaw SDK usługi Azure IoT Hub dla języka Java: 

* Dodaj do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java)
* [Odwołanie API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Zestaw SDK usługi Azure IoT Hub dla pliku Node.js: 

* Pobierz z [npm](https://www.npmjs.com/package/azure-iothub)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node)
* [Odwołanie API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Zestaw SDK usługi Azure IoT Hub dla języka Python: 

* Pobierz z [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python/tree/master)

Zestaw SDK usługi Azure IoT Hub dla języka C: 

* Pobierz z [apt-get, MBED, Arduino IDE lub NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c)

Zestaw SDK usługi Azure IoT Hub dla systemu iOS: 

* Zainstaluj z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Samples](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Zobacz pliki readme w repozytoriach GitHub, aby uzyskać informacje na temat używania menedżerów pakietów specyficznych dla języka i platformy do instalowania plików binarnych i zależności na komputerze deweloperskim.

## <a name="microsoft-azure-provisioning-sdks"></a>Zestaw SDK inicjowania obsługi administracyjnej platformy Microsoft Azure

Zestaw **SDK inicjowania obsługi administracyjnej platformy Microsoft Azure** umożliwia aprowizowanie urządzeń do centrum IoT Hub przy użyciu usługi [inicjowania obsługi administracyjnej urządzeń.](../iot-dps/about-iot-dps.md)

Zestaw SDK obsługi administracyjnej platformy Azure dla języka C#:

* Pobierz z [sdk urządzenia](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) i [sdk usługi](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) z NuGet.
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Odwołanie API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Zestaw SDK obsługi administracyjnej platformy Azure dla języka C:

* Zainstaluj z [apt-get, MBED, Arduino IDE lub iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Odwołanie API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Zestaw SDK obsługi administracyjnej platformy Azure dla środowiska Java:

* Dodaj do projektu [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Odwołanie API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Zestaw SDK obsługi administracyjnej platformy Azure dla pliku Node.js:

* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Odwołanie API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Pobierz [sdk i](https://badge.fury.io/js/azure-iot-provisioning-device) [sdk usługi](https://badge.fury.io/js/azure-iot-provisioning-service) z npm

Zestaw SDK obsługi administracyjnej platformy Azure dla języka Python:

* [Kod źródłowy](https://github.com/Azure/azure-iot-sdk-python)
* Pobierz [sdk urządzenia](https://pypi.org/project/azure-iot-device/) i [sdk usługi](https://pypi.org/project/azure-iothub-provisioningserviceclient/) z pip

## <a name="next-steps"></a>Następne kroki

ZestawY SDK usługi Azure IoT zawierają również zestaw narzędzi ułatwiających tworzenie:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): wieloplatformowe narzędzie wiersza polecenia ułatwiające diagnozowanie problemów związanych z połączeniem z usługą IoT Hub.
* [azure-iot-explorer](https://github.com/Azure/azure-iot-explorer): wieloplatformowa aplikacja komputerowa do łączenia się z centrum IoT Hub i dodawania urządzeń IoT/manage/komunikuje się z nimi.

Odpowiednie dokumenty związane z programem za pomocą zestawów SDK usługi IoT platformy Azure:
* Dowiedz [się, jak zarządzać łącznością i niezawodnymi wiadomościami](iot-hub-reliability-features-in-sdks.md) przy użyciu sdk usługi IoT Hub.
* Dowiedz się, jak [tworzyć platformy mobilne,](iot-hub-how-to-develop-for-mobile-devices.md) takie jak iOS i Android.
* [Obsługa platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)


Inne tematy referencyjne w tym przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe centrum IoT Hub](iot-hub-devguide-endpoints.md)
* [Język zapytań usługi IoT Hub dla bliźniaczych bliźniąt urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md)
* [Limity przydziału i ograniczanie wydajności](iot-hub-devguide-quotas-throttling.md)
* [Obsługa usługi IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Odwołanie do interfejsu API REST usługi IoT Hub](/rest/api/iothub/)
