---
title: Łączność urządzeń i dane telemetryczne — usługa Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się, jak łączyć, dołączać i wysyłać dane telemetryczne z urządzenia IoT w usłudze Azure Digital bliźniaczych reprezentacji.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75862479"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Ruch przychodzący danych telemetrycznych i łączność urządzeń

Dane telemetryczne wysyłane przez urządzenia i czujniki tworzą szkielet dowolnego rozwiązania IoT. Sposób reprezentowania tych różnych zasobów i zarządzania nimi w kontekście lokalizacji to główny problem dotyczący tworzenia aplikacji IoT. Usługa Azure Digital bliźniaczych reprezentacji upraszcza proces opracowywania rozwiązań IoT przez łączenie urządzeń i czujników przy użyciu grafu analizy przestrzennej.

Aby rozpocząć, utwórz zasób usługi Azure IoT Hub w katalogu głównym wykresu przestrzennego. Zasób IoT Hub umożliwia wszystkim urządzeniom znajdującym się poniżej przestrzeni głównej wysyłanie komunikatów. Po utworzeniu IoT Hub Zarejestruj urządzenia, korzystając z czujników w ramach wystąpienia Digital bliźniaczych reprezentacji. Urządzenia mogą wysyłać dane do usługi Digital bliźniaczych reprezentacji za pośrednictwem [zestawu SDK urządzeń Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Aby zapoznać się z przewodnikiem krok po kroku dotyczącym sposobu przenoszenia urządzeń, Przeczytaj [Samouczek dotyczący wdrażania i konfigurowania cyfrowego bliźniaczych reprezentacji](tutorial-facilities-setup.md). Na pierwszy rzut oka są następujące czynności:

- Wdróż wystąpienie bliźniaczych reprezentacji Digital na podstawie [Azure Portal](https://portal.azure.com).
- Utwórz spacje na wykresie.
- Utwórz zasób IoT Hub i przypisz go do przestrzeni na wykresie.
- Utwórz urządzenia i czujniki w grafie i przypisz je do miejsc utworzonych w poprzednich krokach.
- Utwórz dopasowanie do filtrowania komunikatów telemetrycznych na podstawie warunków.
- Utwórz [funkcję zdefiniowaną przez użytkownika](concepts-user-defined-functions.md)i przypisz ją do miejsca na grafie na potrzeby niestandardowego przetwarzania komunikatów telemetrycznych.
- Przypisz rolę, aby zezwolić funkcji zdefiniowanej przez użytkownika na dostęp do danych grafu.
- Pobierz parametry połączenia urządzenia IoT Hub z interfejsów API zarządzania cyfrowego bliźniaczych reprezentacji.
- Skonfiguruj parametry połączenia urządzenia na urządzeniu za pomocą zestawu SDK urządzenia usługi Azure IoT.

W poniższych sekcjach dowiesz się, jak pobrać parametry połączenia urządzenia IoT Hub z interfejsu API zarządzania bliźniaczych reprezentacji Digital. Dowiesz się również, jak wysyłać dane telemetryczne oparte na czujnikach przy użyciu IoT Hub formatu komunikatów telemetrycznych. Cyfrowy bliźniaczych reprezentacji wymaga, aby każda część telemetrii, która się odbierze, była skojarzona z czujnikiem w grafie przestrzennym. To wymaganie zapewnia, że dane są przetwarzane i kierowane w odpowiednim kontekście przestrzennym.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Pobierz parametry połączenia urządzenia IoT Hub z interfejsu API zarządzania

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Wykonaj wywołanie GET w interfejsie API urządzenia z `includes=ConnectionString` parametrem, aby uzyskać parametry połączenia z urządzeniem IoT Hub. Odfiltruj według identyfikatora GUID urządzenia lub identyfikatora sprzętu, aby znaleźć określone urządzenie.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_DEVICE_GUID* | Identyfikator urządzenia |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Wartość parametru | Zamień na |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Identyfikator sprzętu urządzenia |

W ładunku odpowiedzi Skopiuj Właściwość **ConnectionString** urządzenia. Jest ona używana po wywołaniu zestawu SDK urządzenia usługi Azure IoT w celu wysłania danych do cyfrowego bliźniaczych reprezentacji.

## <a name="device-to-cloud-message"></a>Komunikat z urządzenia do chmury

Można dostosować Format komunikatów i ładunek urządzenia, aby odpowiadały potrzebom Twojego rozwiązania. Użyj dowolnego kontraktu danych, który może zostać Zserializowany do tablicy lub strumienia bajtów, który jest obsługiwany przez [klasę komunikatów klienta urządzenia Azure IoT, komunikat (Byte [] ByteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Ten komunikat może być niestandardowym, tak długo, jak dekodowanie kontraktu danych w odpowiedniej funkcji zdefiniowanej przez użytkownika. Istnieje tylko jedno wymaganie dotyczące komunikatu z urządzenia do chmury. Zachowaj zestaw właściwości, aby upewnić się, że wiadomość jest odpowiednio kierowana do aparatu przetwarzania.

### <a name="telemetry-properties"></a>Właściwości telemetrii

 Zawartość ładunku **komunikatu** może zawierać dowolne dane o rozmiarze do 256 KB. Dla właściwości [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) typu oczekiwano kilku wymagań. W tabeli przedstawiono właściwości wymagane i opcjonalne obsługiwane przez system.

| Nazwa właściwości | Wartość | Wymagany | Opis |
|---|---|---|---|
| **DigitalTwins — Telemetria** | 1.0 | Tak | Stała wartość, która identyfikuje komunikat w systemie. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Tak | Unikatowy identyfikator czujnika, który wysyła **komunikat**. Ta wartość musi być zgodna z właściwością **HardwareId** obiektu dla systemu, aby przetworzyć go. Na przykład `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nie | Ciąg daty w formacie [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) , który identyfikuje czas próbkowania ładunku. Na przykład `2018-09-20T07:35:00.8587882-07:00`. |
| **Korelacj** | `string` | Nie | Identyfikator UUID używany do śledzenia zdarzeń w systemie. Na przykład `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Wyślij wiadomość do bliźniaczych reprezentacji Digital

Użyj wywołania DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) lub [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) , aby wysłać wiadomość do Digital bliźniaczych reprezentacji.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat możliwości przetwarzania danych Digital bliźniaczych reprezentacji i funkcji zdefiniowanych przez użytkownika, należy przeczytać artykuł [Azure Digital bliźniaczych reprezentacji Data Processing i funkcje zdefiniowane przez użytkownika](concepts-user-defined-functions.md).
