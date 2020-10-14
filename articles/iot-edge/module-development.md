---
title: Opracowywanie modułów dla Azure IoT Edge | Microsoft Docs
description: Opracowywanie modułów niestandardowych dla Azure IoT Edge, które mogą komunikować się ze środowiskiem uruchomieniowym i IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eae36f6b4baabdcc9831b084602d340a299a7bac
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047628"
---
# <a name="develop-your-own-iot-edge-modules"></a>Opracowywanie własnych modułów IoT Edge

Moduły Azure IoT Edge mogą łączyć się z innymi usługami platformy Azure i współtworzyć w większym potoku danych w chmurze. W tym artykule opisano sposób opracowywania modułów w celu komunikowania się z IoT Edge środowiska uruchomieniowego i IoT Hub, a w związku z tym w pozostałej części chmury platformy Azure.

## <a name="iot-edge-runtime-environment"></a>Środowisko uruchomieniowe IoT Edge

Środowisko uruchomieniowe IoT Edge zapewnia infrastrukturę do integrowania funkcji wielu modułów IoT Edge i wdrażania ich na urządzeniach IoT Edge. Każdy program może być spakowany jako moduł IoT Edge. Aby w pełni wykorzystać możliwości IoT Edge funkcji komunikacji i zarządzania programu, program uruchomiony w module może użyć zestawu SDK urządzenia usługi Azure IoT do nawiązania połączenia z lokalnym centrum IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Korzystanie z Centrum IoT Edge

Centrum IoT Edge oferuje dwie główne funkcje: proxy do IoT Hub i komunikacji lokalnej.

### <a name="iot-hub-primitives"></a>IoT Hub elementy podstawowe

IoT Hub widzi wystąpienie modułu analogicznie do urządzenia, w tym sensie:

* ma dwuosiowy moduł, który jest odrębny i odizolowany od [sznurka urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) i innego modułu bliźniaczych reprezentacji tego urządzenia;
* może wysyłać [komunikaty z urządzenia do chmury](../iot-hub/iot-hub-devguide-messaging.md);
* może otrzymywać [bezpośrednie metody](../iot-hub/iot-hub-devguide-direct-methods.md) ukierunkowane na jego tożsamość.

Obecnie moduły nie mogą odbierać komunikatów z chmury do urządzenia ani używać funkcji przekazywania plików.

Podczas pisania modułu można użyć [zestawu SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) , aby nawiązać połączenie z Centrum IoT Edge i użyć powyższych funkcji, tak jak w przypadku korzystania z IoT Hub z aplikacją urządzenia. Jedyną różnicą między modułami IoT Edge i aplikacjami urządzeń IoT jest konieczność odwoływania się do tożsamości modułu zamiast tożsamości urządzenia.

### <a name="device-to-cloud-messages"></a>Komunikaty z urządzenia do chmury

Aby włączyć złożone przetwarzanie komunikatów przesyłanych z urządzeń do chmury, usługa IoT Edge Hub zapewnia deklaratywny Routing komunikatów między modułami oraz między modułami i IoT Hub. Routing deklaratywny umożliwia modułom przechwytywanie i przetwarzanie komunikatów wysyłanych przez inne moduły i propagowanie ich do złożonych potoków. Aby uzyskać więcej informacji, zobacz [wdrażanie modułów i ustanawianie tras w IoT Edge](module-composition.md).

Moduł IoT Edge, w przeciwieństwie do normalnej aplikacji urządzenia IoT Hub, może odbierać komunikaty z urządzenia do chmury, które są używane przez jego lokalnego centrum IoT Edge, aby przetworzyć je.

IoT Edge Hub propaguje komunikaty do modułu w oparciu o trasy deklaratywne opisane w [manifeście wdrożenia](module-composition.md). Podczas tworzenia modułu IoT Edge można odbierać te komunikaty przez ustawienie programów obsługi komunikatów.

Aby uprościć tworzenie tras, IoT Edge dodaje koncepcję punktów końcowych *danych wejściowych* i *wyjściowych* modułu. Moduł może odbierać wszystkie komunikaty przesyłane z urządzenia do chmury bez określania danych wejściowych, a także wysyłać komunikaty z urządzenia do chmury bez konieczności określania danych wyjściowych. Używanie jawnych danych wejściowych i wyjść sprawia, że reguły routingu są prostsze do zrozumienia.

Na koniec komunikaty z urządzenia do chmury obsługiwane przez centrum brzegowe są znakowane przy użyciu następujących właściwości systemu:

| Właściwość | Opis |
| -------- | ----------- |
| $connectionDeviceId | Identyfikator urządzenia klienta, który wysłał komunikat |
| $connectionModuleId | Identyfikator modułu, który wysłał komunikat |
| $inputName | Dane wejściowe, które odebrały tę wiadomość. Może być pusty. |
| $outputName | Dane wyjściowe użyte do wysłania wiadomości. Może być pusty. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Nawiązywanie połączenia z Centrum IoT Edge z poziomu modułu

Podłączanie do lokalnego centrum IoT Edge z modułu obejmuje dwie czynności:

1. Utwórz wystąpienie ModuleClient w aplikacji.
2. Upewnij się, że aplikacja akceptuje certyfikat przedstawiony przez Centrum IoT Edge na tym urządzeniu.

Utwórz wystąpienie ModuleClient, aby połączyć moduł z Centrum IoT Edge uruchomionego na urządzeniu, podobnie jak wystąpienia DeviceClient połączą urządzenia IoT z IoT Hub. Aby uzyskać więcej informacji na temat klasy ModuleClient i jej metod komunikacji, zobacz Dokumentacja interfejsu API dla preferowanego języka SDK: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)lub [Node.js](/javascript/api/azure-iot-device/moduleclient).

## <a name="language-and-architecture-support"></a>Obsługa języków i architektury

IoT Edge obsługuje wiele systemów operacyjnych, architektury urządzeń i języki programowania, dzięki czemu można stworzyć scenariusz, który odpowiada Twoim potrzebom. Ta sekcja służy do poznania opcji tworzenia niestandardowych modułów IoT Edge. Więcej informacji o obsłudze narzędzi i wymaganiach dla każdego języka można znaleźć w temacie [Przygotowywanie środowiska deweloperskiego i testowego do IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

W przypadku wszystkich języków w poniższej tabeli IoT Edge obsługuje programowanie dla urządzeń z systemem AMD64 i ARM32 Linux.

| Język programowania | Narzędzia programistyczne |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Program Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Program Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Obsługa programowania i debugowania dla urządzeń z systemem ARM64 Linux jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać więcej informacji, zobacz [programowanie i debugowanie modułów IoT Edge arm64 w Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

W przypadku wszystkich języków w poniższej tabeli IoT Edge obsługuje programowanie dla urządzeń z systemem AMD64 Windows.

| Język programowania | Narzędzia programistyczne |
| -------------------- | ----------------- |
| C | Program Visual Studio 2017/2019 |
| C# | Visual Studio Code (bez możliwości debugowania)<br>Program Visual Studio 2017/2019 |

## <a name="next-steps"></a>Następne kroki

[Przygotuj środowisko deweloperskie i testowe dla IoT Edge](development-environment.md)

[Korzystanie z programu Visual Studio do opracowywania modułów w języku C# dla IoT Edge](how-to-visual-studio-develop-module.md)

[Użyj Visual Studio Code do opracowania modułów dla IoT Edge](how-to-vs-code-develop-module.md)

[Omówienie i używanie zestawów SDK usługi Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md)