---
title: Informacje o pomocy technicznej MQTT usługi Azure IoT Device Provisioning Service | Microsoft Docs
description: Przewodnik dla deweloperów — obsługa urządzeń łączących się z punktem końcowym usługi Azure IoT Device Provisioning (DPS) dostępnym na urządzeniu przy użyciu protokołu MQTT.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0a7ec2f4f8fdf631a6bc5096296275291ec41751
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967129"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Komunikacja z usługą DPS przy użyciu protokołu MQTT

Usługa DPS umożliwia urządzeniom komunikowanie się z punktem końcowym urządzenia usługi DPS przy użyciu:

* [MQTT v 3.1.1](https://mqtt.org/) na porcie 8883
* [MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) przy użyciu protokołu WebSocket na porcie 443.

Usługa DPS nie jest w pełni funkcjonalnym brokerem MQTT i nie obsługuje wszystkich zachowań określonych w standardzie MQTT v 3.1.1. W tym artykule opisano sposób, w jaki urządzenia mogą używać obsługiwanych zachowań MQTT do komunikowania się z usługą DPS.

Cała komunikacja urządzeń z usługą DPS musi być zabezpieczona przy użyciu protokołu TLS/SSL. W związku z tym usługa DPS nie obsługuje połączeń niezabezpieczonych przez port 1883.

 > [!NOTE] 
 > Usługa DPS nie obsługuje obecnie urządzeń korzystających z [mechanizmu zaświadczania](./concepts-service.md#attestation-mechanism) TPM za pośrednictwem protokołu MQTT.

## <a name="connecting-to-dps"></a>Nawiązywanie połączenia z usługą DPS

Urządzenie może korzystać z protokołu MQTT do nawiązywania połączenia z usługą DPS przy użyciu dowolnej z poniższych opcji.

* Biblioteki w zestawach [SDK aprowizacji usługi Azure IoT](../iot-hub/iot-hub-devguide-sdks.md#microsoft-azure-provisioning-sdks).
* Protokół MQTT bezpośrednio.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Bezpośrednie używanie protokołu MQTT (jako urządzenia)

Jeśli urządzenie nie może użyć zestawów SDK urządzeń, nadal może nawiązać połączenie z punktami końcowymi urządzeń publicznych przy użyciu protokołu MQTT na porcie 8883. W pakiecie **Connect** urządzenie powinno używać następujących wartości:

* Dla pola **ClientId** Użyj **Identyfikator rejestracji**.

* W polu **username (nazwa użytkownika** ) Użyj `{idScope}/registrations/{registration_id}/api-version=2019-03-31` , gdzie `{idScope}` jest [idScopeą](./concepts-service.md#id-scope) usługi DPS.

* W polu **hasło** Użyj tokenu SAS. Format tokenu sygnatury dostępu współdzielonego jest taki sam jak dla protokołów HTTPS i AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` Wartość resourceURI powinna być w formacie `{idScope}/registrations/{registration_id}` . Nazwa zasad powinna być `registration` .

  > [!NOTE]
  > Jeśli używasz uwierzytelniania certyfikatu X. 509, hasła tokenu sygnatury dostępu współdzielonego nie są wymagane.

  Więcej informacji o sposobach generowania tokenów SAS znajduje się w sekcji tokeny zabezpieczające [kontroli dostępu do usługi DPS](how-to-control-access.md#security-tokens).

Poniżej znajduje się lista zachowań specyficznych dla implementacji programu DPS:

 * Usługa DPS nie obsługuje funkcji flagi **CleanSession** ustawionej na **wartość 0**.

 * Gdy aplikacja urządzenia subskrybuje temat z **usługą QoS 2, usługa** DPS przyznaje maksymalny poziom jakości usług (QoS) 1 w pakiecie **SUBACK** . Następnie usługa DPS dostarcza komunikaty do urządzenia przy użyciu funkcji QoS 1.

## <a name="tlsssl-configuration"></a>Konfiguracja protokołu TLS/SSL

Aby bezpośrednio korzystać z protokołu MQTT, klient *musi* połączyć się przez protokół TLS 1,2. Próba pominięcia tego kroku kończy się niepowodzeniem z błędami połączenia.


## <a name="registering-a-device"></a>Rejestrowanie urządzenia

Aby zarejestrować urządzenie za pośrednictwem usługi DPS, urządzenie powinno subskrybować korzystanie z programu `$dps/registrations/res/#` jako **filtru tematu**. Wielopoziomowy symbol wielowymiarowy `#` w filtrze tematu służy tylko do zezwalania urządzeniu na odbieranie dodatkowych właściwości w nazwie tematu. Usługa DPS nie zezwala na użycie `#` `?` symboli wieloznacznych ani do filtrowania tematów podrzędnych. Ponieważ usługa DPS nie jest brokerem wysyłania komunikatów ogólnego przeznaczenia, obsługuje tylko udokumentowane nazwy tematów i filtry tematów.

Urządzenie powinno opublikować komunikat rejestrowania w usłudze DPS przy użyciu `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` jako **nazwy tematu**. Ładunek powinien zawierać obiekt [rejestracji urządzenia](/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) w formacie JSON.
W przypadku pomyślnego scenariusza urządzenie otrzyma odpowiedź na `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` nazwę tematu, gdzie x jest wartością retry-After w sekundach. Ładunek odpowiedzi będzie zawierać obiekt [RegistrationOperationStatus](/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) w formacie JSON.

## <a name="polling-for-registration-operation-status"></a>Sondowanie stanu operacji rejestracji

Urządzenie musi okresowo sondować usługę, aby otrzymać wynik operacji rejestracji urządzenia. Przy założeniu, że urządzenie już subskrybuje `$dps/registrations/res/#` temat zgodnie z powyższym opisem, może opublikować komunikat Get operationstatus w `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` nazwie tematu. Identyfikator operacji w tym komunikacie powinien być wartością odebraną w komunikacie odpowiedzi RegistrationOperationStatus w poprzednim kroku. W przypadku powodzenia usługa odpowie w `$dps/registrations/res/200/?$rid={request_id}` temacie. Ładunek odpowiedzi będzie zawierać obiekt RegistrationOperationStatus. Urządzenie powinno utrzymywać sondowanie usługi, jeśli kod odpowiedzi jest 202 po opóźnieniu równym ponowieniu okresu. Operacja rejestracji urządzenia zakończyła się pomyślnie, jeśli usługa zwróci kod stanu 200.

## <a name="connecting-over-websocket"></a>Łączenie przy użyciu protokołu WebSocket
Podczas nawiązywania połączenia za pośrednictwem protokołu WebSocket Określ podprotokół jako `mqtt` . Postępuj zgodnie ze [specyfikacją RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat protokołu MQTT można znaleźć w [dokumentacji MQTT](https://mqtt.org/).

Aby dowiedzieć się więcej o możliwościach punktu dystrybucji, zobacz:

* [Informacje o IoT DPS](about-iot-dps.md)