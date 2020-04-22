---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 401003 IoTHubNieautoryzowane
description: Dowiedz się, jak naprawić błąd 401003 IoTHubNiewolniony
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
ms.openlocfilehash: f46d41c8287d03cbe9582ed560244cbd85cdeeaa
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759600"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

W tym artykule opisano przyczyny i rozwiązania dla **401003 IoTHubNieutoryzowany** błędy.

## <a name="symptoms"></a>Objawy

### <a name="symptom-1"></a>Objaw 1

W dziennikach diagnostycznych widać wzorzec urządzeń rozłączanych z **401003 IoTHubUnauthorized**, a następnie **404104 DeviceConnectionClosedReotely**, a następnie pomyślnie połączone wkrótce po.

### <a name="symptom-2"></a>Objaw 2

Żądania do Usługi IoT Hub nie powiodą się z jednym z następujących komunikatów o błędach:

* Brak nagłówka autoryzacji
* IotHub\*' ' nie zawiera\*określonego urządzenia '
* Reguła\*autoryzacji ' '\*nie zezwala na dostęp dla '
* Uwierzytelnianie nie powiodło się dla tego urządzenia, odnowić token lub certyfikat i ponownie nawiązać połączenie
* Odcisk palca nie pasuje do konfiguracji: Odcisk\*palca: SHA1Hash= , SHA2Hash=\*; Konfiguracja: PrimaryThumbprint=\*, SecondaryThumbprint=\*

## <a name="cause"></a>Przyczyna

### <a name="cause-1"></a>Przyczyna 1

W przypadku programu MQTT niektóre skuszki SDK polegają na centrum IoT Hub w celu rozłączenia po wygaśnięciu tokenu sygnatury dostępu Współdzielonego, aby wiedzieć, kiedy go odświeżyć. Więc 

1. Token sygnatury dostępu Współdzielonego wygasa
1. Centrum IoT hub powiadamia o wygaśnięciu i rozłącza urządzenie za pomocą **401003 IoTHubUnauthorized**
1. Urządzenie kończy rozłączanie za pomocą **urządzenia 404104 DeviceConnectionClosedReotely**
1. SDK IoT generuje nowy token Sygnatury dostępu Współdzielonego
1. Urządzenie łączy się ponownie z Centrum IoT Hub

### <a name="cause-2"></a>Przyczyna 2

Usługa IoT Hub nie może uwierzytelnić nagłówka, reguły ani klucza uwierzytelniania.

## <a name="solution"></a>Rozwiązanie

### <a name="solution-1"></a>Rozwiązanie 1

Nie jest wymagana żadna akcja, jeśli przy użyciu usługi IoT SDK do połączenia przy użyciu ciągu połączenia urządzenia. IoT SDK ponownie generuje nowy token, aby ponownie połączyć się z wygaśnięciem tokenu sygnatury dostępu Współdzielonego. 

Jeśli ilość błędów jest problemem, przełącz się do SDK C, który odnawia token sygnatury dostępu Współdzielonego przed wygaśnięciem. Ponadto dla amqp token sygnatury dostępu Współdzielonego można odświeżyć bez rozłączenia.

### <a name="solution-2"></a>Rozwiązanie 2

Ogólnie rzecz biorąc przedstawiony komunikat o błędzie powinien wyjaśniać, jak naprawić błąd. Jeśli z jakiegoś powodu nie masz dostępu do szczegółów komunikatu o błędzie, upewnij się, że:

- Sygnatury dostępu Współdzielonego lub innego tokenu zabezpieczającego, którego używasz, nie wygasł. 
- Poświadczenia autoryzacji jest dobrze sformułowany dla protokołu, którego używasz. Aby dowiedzieć się więcej, zobacz [Kontrola dostępu usługi IoT Hub](iot-hub-devguide-security.md).
- Używana reguła autoryzacji ma uprawnienia do żądanej operacji.

## <a name="next-steps"></a>Następne kroki

Aby ułatwić uwierzytelnianie w centrum IoT Hub, zalecamy korzystanie z [zestawów SDK usługi Azure IoT.](iot-hub-devguide-sdks.md)