---
title: Informacje o rejestrze tożsamości usługi Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — Opis rejestru tożsamości IoT Hub i sposobu korzystania z niego do zarządzania urządzeniami. Zawiera informacje na temat importowania i eksportowania tożsamości urządzeń zbiorczo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 2f811c504f8871b06805d5578ed2d70c651be25d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90029838"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Informacje o rejestrze tożsamości w centrum IoT Hub

Każde Centrum IoT Hub ma rejestr tożsamości, który przechowuje informacje o urządzeniach i modułach, które mogą nawiązywać połączenia z usługą IoT Hub. Aby urządzenie lub moduł mogły połączyć się z usługą IoT Hub, musi istnieć wpis dla tego urządzenia lub modułu w rejestrze tożsamości Centrum IoT. Urządzenie lub moduł musi także uwierzytelniać się w usłudze IoT Hub na podstawie poświadczeń przechowywanych w rejestrze tożsamości.

Dla urządzenia lub identyfikatora modułu przechowywanego w rejestrze tożsamości jest rozróżniana wielkość liter.

Na wysokim poziomie rejestr tożsamości to kolekcja zasobów urządzeń lub modułów z obsługą protokołu REST. Po dodaniu wpisu w rejestrze tożsamości IoT Hub tworzy zestaw zasobów dla poszczególnych urządzeń, takich jak kolejka, która zawiera komunikaty przesyłane z chmury do urządzenia.

Użyj rejestru tożsamości, gdy zachodzi taka potrzeba:

* Zainicjuj obsługę urządzeń lub modułów łączących się z Centrum IoT Hub.
* Kontroluj dostęp dla poszczególnych urządzeń/poszczególnych modułów do urządzeń lub punktów końcowych dostępnych w module centrum.

> [!NOTE]
> * Rejestr tożsamości nie zawiera żadnych metadanych specyficznych dla aplikacji.
> * Tożsamość modułu i sznurki modułu są w publicznej wersji zapoznawczej. Funkcja poniżej będzie obsługiwana w przypadku tożsamości modułu, gdy jest ona ogólnie dostępna.
>

## <a name="identity-registry-operations"></a>Operacje rejestru tożsamości

Rejestr IoT Hub Identity uwidacznia następujące operacje:

* Utwórz tożsamość urządzenia lub modułu
* Aktualizowanie tożsamości urządzenia lub modułu
* Pobierz tożsamość urządzenia lub modułu według identyfikatora
* Usuwanie tożsamości urządzenia lub modułu
* Wyświetl listę tożsamości do 1000
* Eksportowanie tożsamości urządzeń do usługi Azure Blob Storage
* Importowanie tożsamości urządzeń z usługi Azure Blob Storage

Wszystkie te operacje mogą używać optymistycznej współbieżności, jak określono w [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> Jedynym sposobem pobrania wszystkich tożsamości w rejestrze tożsamości Centrum IoT jest użycie funkcji [eksportowania](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) .

Rejestr tożsamości IoT Hub:

* Nie zawiera żadnych metadanych aplikacji.
* Można uzyskać dostęp do słownika przy użyciu elementu **deviceId** lub **moduleId** jako klucza.
* Nie obsługuje zapytań ekspresowych.

Rozwiązanie IoT zazwyczaj ma oddzielny magazyn specyficzny dla rozwiązań, który zawiera metadane specyficzne dla aplikacji. Na przykład magazyn specyficzny dla rozwiązania w inteligentnym rozwiązaniu do kompilowania będzie rejestrował pomieszczenie, w którym jest wdrożony czujnik temperatury.

> [!IMPORTANT]
> Tylko rejestr tożsamości służy do zarządzania urządzeniami i wykonywania operacji aprowizacji. Operacje o wysokiej przepływności w czasie wykonywania nie powinny być zależne od wykonywania operacji w rejestrze tożsamości. Na przykład sprawdzanie stanu połączenia urządzenia przed wysłaniem polecenia nie jest obsługiwanym wzorcem. Upewnij się, że są sprawdzane [szybkości ograniczania](iot-hub-devguide-quotas-throttling.md) dla rejestru tożsamości oraz wzorzec [pulsu urządzenia](iot-hub-devguide-identity-registry.md#device-heartbeat) .

## <a name="disable-devices"></a>Wyłączanie urządzeń

Urządzenia można wyłączyć, aktualizując Właściwość **stan** tożsamości w rejestrze tożsamości. Zazwyczaj ta właściwość jest używana w dwóch scenariuszach:

* Podczas procesu aranżacji aprowizacji. Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi urządzeń](iot-hub-devguide-identity-registry.md#device-provisioning).

* Jeśli z jakiegoś powodu podejrzewasz, że urządzenie zostało naruszone lub nie ma autoryzacji.

Ta funkcja jest niedostępna dla modułów.

## <a name="import-and-export-device-identities"></a>Importowanie i eksportowanie tożsamości urządzeń

Użyj operacji asynchronicznych w [punkcie końcowym dostawcy zasobów IoT Hub](iot-hub-devguide-endpoints.md) , aby wyeksportować tożsamości urządzeń zbiorczo z rejestru tożsamości Centrum IoT. Eksporty to długotrwałe zadania, które używają kontenera obiektów BLOB dostarczonego przez klienta w celu zapisywania danych tożsamości urządzenia odczytywanych z rejestru tożsamości.

Użyj operacji asynchronicznych w [punkcie końcowym dostawcy zasobów IoT Hub](iot-hub-devguide-endpoints.md) , aby zaimportować tożsamość urządzeń zbiorczo do rejestru tożsamości Centrum IoT. Importy to długotrwałe zadania, które wykorzystują dane w kontenerze obiektów BLOB dostarczonym przez klienta w celu zapisania danych tożsamości urządzenia w rejestrze tożsamości.

Aby uzyskać więcej informacji na temat interfejsów API importowania i eksportowania, zobacz [IoT Hub interfejsów API REST dostawcy zasobów](/rest/api/iothub/iothubresource). Aby dowiedzieć się więcej o uruchamianiu zadań importowania i eksportowania, zobacz [zbiorcze Zarządzanie tożsamościami urządzeń IoT Hub](iot-hub-bulk-identity-mgmt.md).

Tożsamości urządzeń można również eksportować i importować z IoT Hub za pośrednictwem interfejsu API usługi za pośrednictwem interfejsu [API REST](/rest/api/iothub/service/jobs/createimportexportjob) lub jednego z [zestawów SDK usługi](/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-service-sdks)IoT Hub.

## <a name="device-provisioning"></a>Inicjowanie obsługi urządzeń

Dane urządzenia, które są przechowywane w ramach danego rozwiązania IoT, zależą od konkretnych wymagań tego rozwiązania. Jednak jako minimum, rozwiązanie musi przechowywać tożsamości urządzeń i klucze uwierzytelniania. Usługa Azure IoT Hub zawiera rejestr tożsamości, w którym można przechowywać wartości dla każdego urządzenia, takie jak identyfikatory, klucze uwierzytelniania i kody stanu. Rozwiązanie może korzystać z innych usług platformy Azure, takich jak Table Storage, BLOB Storage lub Cosmos DB, do przechowywania dodatkowych danych urządzeń.

*Inicjowanie obsługi administracyjnej urządzeń* polega na dodaniu początkowych danych urządzenia do sklepów w rozwiązaniu. Aby umożliwić nowym urządzeniu łączenie się z centrum, należy dodać identyfikator i klucze urządzenia do rejestru tożsamości IoT Hub. W ramach procesu aprowizacji może być konieczne zainicjowanie danych specyficznych dla urządzenia w innych magazynach rozwiązań. Możesz również użyć IoT Hub Device Provisioning Service platformy Azure, aby włączyć funkcję bezobsługowego udostępniania w czasie just-in-Time do jednego lub kilku centrów IoT, bez konieczności interwencji człowieka. Aby dowiedzieć się więcej, zobacz [dokumentację usługi aprowizacji](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Puls urządzenia

Rejestr tożsamości IoT Hub zawiera pole o nazwie **connectionState**. Podczas tworzenia i debugowania Używaj tylko pola **connectionState** . Rozwiązania IoT nie powinny wysyłać zapytań do pola w czasie wykonywania. Na przykład nie należy wysyłać zapytania do pola **connectionState** , aby sprawdzić, czy urządzenie jest połączone przed wysłaniem komunikatu z chmury do urządzenia lub wiadomości SMS. Zalecamy subskrybowanie [zdarzeń **odłączenia urządzenia** ](iot-hub-event-grid.md#event-types) w Event Grid w celu uzyskania alertów i monitorowania stanu połączenia urządzenia. Skorzystaj z tego [samouczka](iot-hub-how-to-order-connection-state-events.md) , aby dowiedzieć się, jak zintegrować urządzenia połączone i odłączone urządzenia z IoT Hub w rozwiązaniu IoT.

Jeśli Twoje rozwiązanie IoT musi wiedzieć, czy urządzenie jest połączone, można zaimplementować *wzorzec pulsu*.
W przypadku wzorca pulsu urządzenie wysyła komunikaty z urządzenia do chmury co najmniej raz na ustalony czas (na przykład co najmniej raz na godzinę). W związku z tym nawet jeśli urządzenie nie ma żadnych danych do wysłania, nadal wysyła pustą wiadomość z urządzenia do chmury (zazwyczaj z właściwością identyfikującą ją jako puls). Po stronie usługi rozwiązanie zachowuje mapę z ostatnim pulsem odebranym dla każdego urządzenia. Jeśli rozwiązanie nie odbiera komunikatu pulsu w oczekiwanym czasie z urządzenia, zakłada się, że występuje problem z urządzeniem.

Bardziej złożona implementacja może obejmować informacje z [Azure monitor](../azure-monitor/index.yml) i [Azure Resource Health](../service-health/resource-health-overview.md) do identyfikowania urządzeń próbujących nawiązać połączenie lub komunikację, ale kończy się niepowodzeniem, sprawdź monitor z przewodnikiem [diagnostycznym](iot-hub-monitor-resource-health.md) . Podczas implementowania wzorca pulsu upewnij się, że zaznaczono [IoT Hub przydziały i ograniczenia](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Jeśli rozwiązanie IoT używa stanu połączenia wyłącznie do określenia, czy wysyłać komunikaty z chmury do urządzenia, a komunikaty nie są emitowane do dużych zestawów urządzeń, należy rozważyć użycie *krótkiego wzorca czasu wygaśnięcia* . Ten wzorzec osiąga ten sam wynik jak utrzymywanie rejestru stanu połączenia urządzenia przy użyciu wzorca pulsu, a jednocześnie jest wydajniejszy. W przypadku żądania potwierdzeń komunikatów IoT Hub może powiadomić o tym, które urządzenia mogą odbierać komunikaty, które nie są.

## <a name="device-and-module-lifecycle-notifications"></a>Powiadomienia dotyczące cyklu życia urządzenia i modułu

IoT Hub może powiadomić rozwiązanie IoT o utworzeniu lub usunięciu tożsamości, wysyłając powiadomienia o cyklu życia. W tym celu Twoje rozwiązanie IoT musi utworzyć trasę i ustawić źródło danych równe *DeviceLifecycleEvents* lub *ModuleLifecycleEvents*. Domyślnie żadne powiadomienia o cyklu życia nie są wysyłane, oznacza to, że nie istnieją takie trasy. Komunikat powiadomienia zawiera właściwości i treść.

Właściwości: właściwości systemu komunikatów są poprzedzone `$` symbolem.

Komunikat powiadomienia dla urządzenia:

| Nazwa | Wartość |
| --- | --- |
|Typ $content | application/json |
|$iothub — enqueuedtime |  Godzina wysłania powiadomienia |
|$iothub-komunikat-Źródło | deviceLifecycleEvents |
|$content — kodowanie | UTF-8 |
|opType | **createDeviceIdentity** lub **deleteDeviceIdentity** |
|hubName | Nazwa IoT Hub |
|deviceId | Identyfikator urządzenia |
|operationTimestamp | ISO8601 sygnatura czasowa operacji |
|iothub — komunikat-schemat | deviceLifecycleNotification |

Treść: Ta sekcja jest w formacie JSON i reprezentuje sznurek utworzonej tożsamości urządzenia. Przykład:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Komunikat powiadomienia dla modułu:

| Nazwa | Wartość |
| --- | --- |
Typ $content | application/json |
$iothub — enqueuedtime |  Godzina wysłania powiadomienia |
$iothub-komunikat-Źródło | moduleLifecycleEvents |
$content — kodowanie | UTF-8 |
opType | **createModuleIdentity** lub **deleteModuleIdentity** |
hubName | Nazwa IoT Hub |
moduleId | Identyfikator modułu |
operationTimestamp | ISO8601 sygnatura czasowa operacji |
iothub — komunikat-schemat | moduleLifecycleNotification |

Treść: Ta sekcja jest w formacie JSON i reprezentuje dwuosiową tożsamość utworzonego modułu. Przykład:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Właściwości tożsamości urządzenia

Tożsamości urządzeń są reprezentowane jako dokumenty JSON o następujących właściwościach:

| Właściwość | Opcje | Opis |
| --- | --- | --- |
| deviceId |wymagane, tylko do odczytu w aktualizacjach |Ciąg z rozróżnianą wielkością liter (do 128 znaków) ASCII 7-bitowe znaki alfanumeryczne i niektóre znaki specjalne: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |wymagane, tylko do odczytu |Ciąg z rozróżnianą wielkością liter, który jest generowany przez Centrum IoT, do 128 znaków. Ta wartość jest używana do rozróżniania urządzeń z tym samym identyfikatorem **deviceId**, gdy zostały usunięte i ponownie utworzone. |
| element ETag |wymagane, tylko do odczytu |Ciąg reprezentujący słaby element ETag dla tożsamości urządzenia, zgodnie z [RFC7232](https://tools.ietf.org/html/rfc7232). |
| uwierzytelniania |optional |Obiekt złożony zawierający informacje o uwierzytelnianiu i materiały zabezpieczające. |
| auth. symkey |optional |Obiekt złożony zawierający klucz podstawowy i pomocniczy, przechowywany w formacie base64. |
| status |wymagane |Wskaźnik dostępu. Można **włączyć** lub **wyłączyć**. Jeśli ta **Funkcja jest włączona**, urządzenie może nawiązać połączenie. Jeśli ta **możliwość jest wyłączona**, urządzenie nie może uzyskać dostępu do żadnego punktu końcowego mającego dostęp do urządzenia. |
| statusReason |optional |Ciąg o długości 128 znaków, który przechowuje przyczynę stanu tożsamości urządzenia. Dozwolone są wszystkie znaki UTF-8. |
| statusUpdateTime |tylko do odczytu |Wskaźnik czasowy przedstawiający datę i godzinę ostatniej aktualizacji stanu. |
| connectionState |tylko do odczytu |Pole wskazujące stan połączenia: **połączone** lub **rozłączone**. To pole reprezentuje widok IoT Hub stanu połączenia z urządzeniem. **Ważne**: to pole powinno być używane tylko na potrzeby tworzenia i debugowania. Stan połączenia jest aktualizowany tylko dla urządzeń korzystających z MQTT lub AMQP. Ponadto jest oparty na poleceniach ping na poziomie protokołu (MQTT Pings lub pinges AMQP) i może mieć maksymalnie 5 minut. Z tego względu można mieć fałszywe pozytywne, takie jak urządzenia zgłoszone jako połączone, ale które są rozłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Wskaźnik danych czasowych, przedstawiający datę i godzinę ostatniego zaktualizowania stanu połączenia. |
| lastActivityTime |tylko do odczytu |Wskaźnik danych czasowych, przedstawiający datę i godzinę ostatniego połączenia urządzenia, odebrania lub wysłania wiadomości. |

> [!NOTE]
> Stan połączenia może reprezentować tylko widok IoT Hub stanu połączenia. Aktualizacje tego stanu mogą być opóźnione, w zależności od warunków i konfiguracji sieci.

> [!NOTE]
> Obecnie zestawy SDK urządzeń nie obsługują używania `+` znaków i w identyfikatorze `#` **deviceId**.

## <a name="module-identity-properties"></a>Właściwości tożsamości modułu

Tożsamości modułów są reprezentowane jako dokumenty JSON o następujących właściwościach:

| Właściwość | Opcje | Opis |
| --- | --- | --- |
| deviceId |wymagane, tylko do odczytu w aktualizacjach |Ciąg z rozróżnianą wielkością liter (do 128 znaków) ASCII 7-bitowe znaki alfanumeryczne i niektóre znaki specjalne: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| moduleId |wymagane, tylko do odczytu w aktualizacjach |Ciąg z rozróżnianą wielkością liter (do 128 znaków) ASCII 7-bitowe znaki alfanumeryczne i niektóre znaki specjalne: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |wymagane, tylko do odczytu |Ciąg z rozróżnianą wielkością liter, który jest generowany przez Centrum IoT, do 128 znaków. Ta wartość jest używana do rozróżniania urządzeń z tym samym identyfikatorem **deviceId**, gdy zostały usunięte i ponownie utworzone. |
| element ETag |wymagane, tylko do odczytu |Ciąg reprezentujący słaby element ETag dla tożsamości urządzenia, zgodnie z [RFC7232](https://tools.ietf.org/html/rfc7232). |
| uwierzytelniania |optional |Obiekt złożony zawierający informacje o uwierzytelnianiu i materiały zabezpieczające. |
| auth. symkey |optional |Obiekt złożony zawierający klucz podstawowy i pomocniczy, przechowywany w formacie base64. |
| status |wymagane |Wskaźnik dostępu. Można **włączyć** lub **wyłączyć**. Jeśli ta **Funkcja jest włączona**, urządzenie może nawiązać połączenie. Jeśli ta **możliwość jest wyłączona**, urządzenie nie może uzyskać dostępu do żadnego punktu końcowego mającego dostęp do urządzenia. |
| statusReason |optional |Ciąg o długości 128 znaków, który przechowuje przyczynę stanu tożsamości urządzenia. Dozwolone są wszystkie znaki UTF-8. |
| statusUpdateTime |tylko do odczytu |Wskaźnik czasowy przedstawiający datę i godzinę ostatniej aktualizacji stanu. |
| connectionState |tylko do odczytu |Pole wskazujące stan połączenia: **połączone** lub **rozłączone**. To pole reprezentuje widok IoT Hub stanu połączenia z urządzeniem. **Ważne**: to pole powinno być używane tylko na potrzeby tworzenia i debugowania. Stan połączenia jest aktualizowany tylko dla urządzeń korzystających z MQTT lub AMQP. Ponadto jest oparty na poleceniach ping na poziomie protokołu (MQTT Pings lub pinges AMQP) i może mieć maksymalnie 5 minut. Z tego względu można mieć fałszywe pozytywne, takie jak urządzenia zgłoszone jako połączone, ale które są rozłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Wskaźnik danych czasowych, przedstawiający datę i godzinę ostatniego zaktualizowania stanu połączenia. |
| lastActivityTime |tylko do odczytu |Wskaźnik danych czasowych, przedstawiający datę i godzinę ostatniego połączenia urządzenia, odebrania lub wysłania wiadomości. |

> [!NOTE]
> Obecnie zestawy SDK urządzeń nie obsługują używania `+` znaków i w identyfikatorach `#` **deviceId** i **moduleId**.

## <a name="additional-reference-material"></a>Dodatkowe materiały referencyjne

Inne tematy referencyjne w przewodniku dewelopera IoT Hub obejmują:

* [IoT Hub punkty końcowe](iot-hub-devguide-endpoints.md) opisują różne punkty końcowe, które są uwidaczniane przez każde Centrum IoT Hub na potrzeby operacji w czasie wykonywania i zarządzania.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisują zachowania przydziałów i ograniczania przepustowości, które mają zastosowanie do usługi IoT Hub.

* [Zestawy SDK urządzeń i usług Azure IoT](iot-hub-devguide-sdks.md) wymieniają różne zestawy SDK języka, których można używać podczas tworzenia aplikacji dla urządzeń i usług, które współpracują z usługą IoT Hub.

* [Język zapytań IoT Hub](iot-hub-devguide-query-language.md) zawiera opis języka zapytań, za pomocą którego można pobrać informacje z IoT Hub dotyczące bliźniaczych reprezentacji i zadań urządzenia.

* [IoT Hub obsługa MQTT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi IoT Hub protokołu MQTT.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy wiesz już, jak korzystać z rejestru tożsamości IoT Hub, możesz zainteresować następujące tematy IoT Hub Przewodnik dla deweloperów:

* [Kontrola dostępu do usługi IoT Hub](iot-hub-devguide-security.md)

* [Synchronizowanie stanu i konfiguracji przy użyciu urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md)

* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Aby wypróbować niektóre koncepcje opisane w tym artykule, zobacz następujący samouczek IoT Hub:

* [Rozpoczynanie pracy z usługą Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Aby poznać korzystanie z IoT Hub Device Provisioning Service w celu włączenia funkcji bezobsługowego, zainicjowania obsługi just-in-Time, zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](https://azure.microsoft.com/documentation/services/iot-dps)
