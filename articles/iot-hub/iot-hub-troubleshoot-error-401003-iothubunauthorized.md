---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 401003 IoTHubUnauthorized
description: Dowiedz się, jak naprawić błąd 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4979405c7675b5eff9f6940cd34e0c974ebad217
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538276"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

W tym artykule opisano przyczyny i rozwiązania **401003 błędów IoTHubUnauthorized** .

## <a name="symptoms"></a>Objawy

### <a name="symptom-1"></a>Objaw 1

W dziennikach widoczny jest wzorzec urządzeń, które odłączają się od **401003 IoTHubUnauthorized** , a następnie **404104 DeviceConnectionClosedRemotely** , a następnie pomyślnie nawiązywać połączenie wkrótce.

### <a name="symptom-2"></a>Objaw 2

Żądania do IoT Hub kończą się niepowodzeniem z jednym z następujących komunikatów o błędach:

* Brak nagłówka autoryzacji
* IotHub " \* " nie zawiera określonego urządzenia " \* "
* Reguła autoryzacji ' \* ' nie zezwala na dostęp dla ' \* '
* Uwierzytelnianie na tym urządzeniu nie powiodło się, Odnów token lub certyfikat i ponownie nawiąż połączenie
* Odcisk palca nie jest zgodny z konfiguracją: odcisk palca: SHA1Hash = \* , SHA2Hash = \* ; Konfiguracja: PrimaryThumbprint = \* , SecondaryThumbprint =\*

## <a name="cause"></a>Przyczyna

### <a name="cause-1"></a>Przyczyna 1

W przypadku MQTT niektóre zestawy SDK są zależne od IoT Hub, aby wystawić rozłączenie, gdy token sygnatury dostępu współdzielonego wygaśnie, aby wiedzieć, kiedy Zatem 

1. Token sygnatury dostępu współdzielonego wygasa
1. IoT Hub Zwróć uwagę na wygaśnięcie i rozłącza urządzenie z **401003 IoTHubUnauthorized**
1. Urządzenie kończy połączenie z **404104 DeviceConnectionClosedRemotely**
1. Zestaw IoT SDK generuje nowy token sygnatury dostępu współdzielonego
1. Urządzenie ponownie nawiązuje połączenie z usługą IoT Hub

### <a name="cause-2"></a>Przyczyna 2

IoT Hub nie może uwierzytelnić nagłówka, reguły lub klucza uwierzytelniania.

## <a name="solution"></a>Rozwiązanie

### <a name="solution-1"></a>Rozwiązanie 1

Nie jest wymagana żadna akcja w przypadku korzystania z zestawu IoT SDK na potrzeby połączenia przy użyciu parametrów połączenia urządzenia. Zestaw IoT SDK ponownie generuje nowy token, aby ponownie połączyć się z wygaśnięciem tokenu sygnatury dostępu współdzielonego. 

Jeśli ilość błędów jest istotna, przełącz się do zestawu C SDK, który odnawia token sygnatury dostępu współdzielonego przed wygaśnięciem. Ponadto w przypadku AMQP token sygnatury dostępu współdzielonego można odświeżyć bez rozłączenia.

### <a name="solution-2"></a>Rozwiązanie 2

Ogólnie rzecz biorąc przedstawiony komunikat o błędzie powinien wyjaśnić, jak naprawić błąd. Jeśli z jakiegoś powodu nie masz dostępu do szczegółów komunikatu o błędzie, upewnij się, że:

- Użycie sygnatury dostępu współdzielonego lub innego tokenu zabezpieczającego nie wygasło. 
- Poświadczenie autoryzacji jest poprawnie sformułowane dla używanego protokołu. Aby dowiedzieć się więcej, zobacz [IoT Hub kontroli dostępu](iot-hub-devguide-security.md).
- Używana reguła autoryzacji ma uprawnienie do żądania operacji.

## <a name="next-steps"></a>Następne kroki

Aby ułatwić uwierzytelnianie IoT Hub, zalecamy korzystanie z [zestawów SDK usługi Azure IoT](iot-hub-devguide-sdks.md).