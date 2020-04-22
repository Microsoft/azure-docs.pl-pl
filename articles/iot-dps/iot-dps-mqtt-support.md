---
title: Poznaj obsługę usługi inicjowania obsługi urządzeń MQTT platformy Azure | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — obsługa urządzeń łączących się z punktem końcowym obsługującym urządzenia usługi Azure IoT (DPS) przy użyciu protokołu MQTT.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 213fc3412a2dfad77946e52a355a30774d6860c7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680689"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Komunikowanie się z dps za pomocą protokołu MQTT

Dps umożliwia urządzeniom komunikowanie się z punktem końcowym urządzenia DPS za pomocą:

* [MQTT v3.1.1](https://mqtt.org/) na porcie 8883
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) przez WebSocket na porcie 443.

DPS nie jest w pełni funkcjonalnym brokerem MQTT i nie obsługuje wszystkich zachowań określonych w standardzie MQTT v3.1.1. W tym artykule opisano, jak urządzenia mogą używać obsługiwanych zachowań MQTT do komunikowania się z dps.

Cała komunikacja urządzenia z DPS musi być zabezpieczona za pomocą protokołu TLS/SSL. W związku z tym DPS nie obsługuje niezabezpieczonych połączeń przez port 1883.

 > [!NOTE] 
 > DPS obecnie nie obsługuje urządzeń korzystających z [mechanizmu zaświadczania](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) TPM za pośrednictwem protokołu MQTT.

## <a name="connecting-to-dps"></a>Łączenie się z DPS

Urządzenie może używać protokołu MQTT do łączenia się z dps przy użyciu dowolnej z następujących opcji.

* Biblioteki w zestawach [SDK inicjowania obsługi administracyjnej usługi Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks).
* Protokół MQTT bezpośrednio.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Bezpośrednie korzystanie z protokołu MQTT (jako urządzenia)

Jeśli urządzenie nie może używać sdk urządzeń, nadal może łączyć się z punktami końcowymi urządzenia publicznego przy użyciu protokołu MQTT na porcie 8883. W pakiecie **CONNECT** urządzenie powinno używać następujących wartości:

* W polu **Identyfikator klienta** należy użyć **identyfikatora rejestracji**.

* W polu **Nazwa** `{idScope}/registrations/{registration_id}/api-version=2019-03-31`użytkownika `{idScope}` należy użyć , gdzie znajduje [się idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) dps.

* W polu **Hasło** użyj tokenu Sygnatury dostępu Współdzielonego. Format tokenu sygnatury dostępu Współdzielonego jest taki sam, jak w przypadku protokołów HTTPS i AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`Identyfikator RESOURCEURI powinien być `{idScope}/registrations/{registration_id}`w formacie . Nazwa zasad powinna `registration`być .

  > [!NOTE]
  > Jeśli używasz uwierzytelniania certyfikatu X.509, hasła tokenów sygnatury dostępu Współdzielonego nie są wymagane.

  Aby uzyskać więcej informacji na temat generowania tokenów sygnatury dostępu współdzielonego, zobacz sekcję tokeny zabezpieczające w sekcji [Kontrola dostępu do DPS](how-to-control-access.md#security-tokens).

Poniżej znajduje się lista zachowań specyficznych dla implementacji DPS:

 * DPS nie obsługuje funkcji **CleanSession** flaga jest ustawiona na **0**.

 * Gdy aplikacja urządzenia subskrybuje temat z **QoS 2,** DPS udziela maksymalnego poziomu QoS 1 w pakiecie **SUBACK.** Następnie DPS dostarcza wiadomości do urządzenia za pomocą QoS 1.

## <a name="tlsssl-configuration"></a>Konfiguracja protokołu TLS/SSL

Aby bezpośrednio korzystać z protokołu MQTT, klient *musi połączyć* się za pomocą protokołu TLS 1.2. Próby pominięcia tego kroku nie powiodą się z błędami połączenia.


## <a name="registering-a-device"></a>Rejestrowanie urządzenia

Aby zarejestrować urządzenie za pośrednictwem dps, `$dps/registrations/res/#` urządzenie powinno subskrybować przy użyciu jako **filtr tematu**. Wielopoziomowy `#` symbol wieloznaczny w filtrze tematu jest używany tylko w celu umożliwienia urządzeniu otrzymywania dodatkowych właściwości w nazwie tematu. DPS nie zezwala na `#` używanie `?` symboli wieloznacznych do filtrowania podtematów. Ponieważ DPS nie jest brokerem wiadomości typu pub-sub ogólnego przeznaczenia, obsługuje tylko udokumentowane nazwy tematów i filtry tematów.

Urządzenie powinno opublikować komunikat rejestru `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` do DPS przy użyciu jako **nazwa tematu**. Ładunek powinien zawierać obiekt [rejestracji urządzenia](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) w formacie JSON.
W pomyślnym scenariuszu urządzenie otrzyma odpowiedź `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` na nazwę tematu, gdzie x jest wartością ponawiania ponawiania w sekundach. Ładunek odpowiedzi będzie zawierać [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) obiektu w formacie JSON.

## <a name="polling-for-registration-operation-status"></a>Sondowanie stanu operacji rejestracji

Urządzenie musi okresowo sondować usługę, aby otrzymać wynik operacji rejestracji urządzenia. Zakładając, że urządzenie już subskrybowane do tematu, `$dps/registrations/res/#` jak wskazano powyżej, `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` może opublikować komunikat get operationstatus do nazwy tematu. Identyfikator operacji w tym komunikacie powinien być wartością otrzymaną w komunikacie odpowiedzi RegistrationOperationStatus w poprzednim kroku. W przypadku pomyślnego usługi odpowie `$dps/registrations/res/200/?$rid={request_id}` na ten temat. Ładunek odpowiedzi będzie zawierać RegistrationOperationStatus obiektu. Urządzenie powinno utrzymywać sondowanie usługi, jeśli kod odpowiedzi jest 202 po opóźnieniu równym okresowi ponawiania. Operacja rejestracji urządzenia zakończy się pomyślnie, jeśli usługa zwraca kod stanu 200.

## <a name="connecting-over-websocket"></a>Łączenie się przez Websocket
Podczas łączenia się przez Websocket, `mqtt`określ subprotocol jako . Śledź [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o protokole MQTT, zapoznaj się z [dokumentacją MQTT](https://mqtt.org/documentation).

Aby dokładniej zbadać możliwości dps, zobacz:

* [Informacje o IoT DPS](about-iot-dps.md)
