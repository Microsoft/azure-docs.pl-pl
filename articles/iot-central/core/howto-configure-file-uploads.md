---
title: Przekazywanie plików z urządzeń do usługi Azure Storage | Microsoft Docs
description: Jak skonfigurować przekazywanie plików z urządzeń do chmury. Po skonfigurowaniu przekazywania plików Zaimplementuj operacje przekazywania plików na urządzeniach.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: ed2eff4d6ccbb9f85dfaf7049fa3cc18711bae0f
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796911"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Przekazywanie plików z urządzeń do chmury

*Ten temat dotyczy administratorów i deweloperów urządzeń.*

IoT Central umożliwia przekazywanie multimediów i innych plików z połączonych urządzeń do magazynu w chmurze. Można skonfigurować możliwość przekazywania plików w aplikacji IoT Central, a następnie zaimplementować przekazywanie plików w kodzie urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować przekazywanie plików, musisz być administratorem w aplikacji IoT Central.

Do przechowywania przekazanych plików jest potrzebne konto usługi Azure Storage i kontener. Jeśli nie masz istniejącego konta magazynu i kontenera do użycia, Utwórz [nowe konto magazynu w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="configure-device-file-uploads"></a>Konfigurowanie przekazywania plików urządzeń

Aby skonfigurować przekazywanie plików urządzeń:

1. Przejdź do sekcji **Administracja** w swojej aplikacji.

1. Wybierz pozycję **przekazywanie plików urządzeń**.

1. Wybierz konto magazynu i kontener, które mają być używane. Jeśli konto magazynu znajduje się w innej subskrypcji platformy Azure z Twojej aplikacji, wprowadź parametry połączenia konta magazynu.

1. W razie potrzeby dostosuj limit czasu przekazywania, który określa, jak długo żądanie przekazania pozostanie prawidłowe. Prawidłowe wartości to od 1 do 24 godzin.

1. Wybierz pozycję **Zapisz**. Po **wyświetleniu** stanu jest gotowy do przekazywania plików z urządzeń.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Skonfiguruj przekazywanie plików w aplikacji":::

## <a name="disable-device-file-uploads"></a>Wyłącz operacje przekazywania plików z urządzeń

Jeśli chcesz wyłączyć przekazywanie plików z urządzenia do aplikacji IoT Central:

1. Przejdź do sekcji **Administracja** w swojej aplikacji.

1. Wybierz pozycję **przekazywanie plików urządzeń**.

1. Wybierz pozycję **Usuń**.

## <a name="upload-a-file-from-a-device"></a>Przekazywanie pliku z urządzenia

IoT Central używa funkcji przekazywania plików IoT Hub, aby umożliwić urządzeniom przekazywanie plików. Przykładowy kod, który pokazuje, jak przekazywać pliki z urządzenia, zapoznaj się z [przykładem IoT Central przekazywania plików](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak skonfigurować i zaimplementować przekazywanie plików urządzeń w IoT Central, sugerowanym następnym krokiem jest zapoznanie się z dodatkowymi przekazywaniem plików urządzeń:

- [Przekazywanie plików z urządzenia do chmury przy użyciu IoT Hub (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Przekazywanie plików z urządzenia do chmury przy użyciu IoT Hub (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [Przekazywanie plików z urządzenia do chmury przy użyciu IoT Hub (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [Przekazywanie plików z urządzenia do chmury przy użyciu IoT Hub (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)