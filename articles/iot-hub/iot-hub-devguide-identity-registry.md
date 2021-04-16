---
title: Opis Azure IoT Hub rejestru tożsamości | Microsoft Docs
description: Przewodnik dla deweloperów — opis IoT Hub rejestru tożsamości oraz sposobu używania go do zarządzania urządzeniami. Zawiera zbiorcze informacje na temat importowania i eksportowania tożsamości urządzeń.
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
ms.openlocfilehash: 42def04db63d81bdb3eff8098daa8c75924bffec
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502083"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Opis rejestru tożsamości w centrum IoT

Każde centrum IoT ma rejestr tożsamości, który przechowuje informacje o urządzeniach i modułach, które mogą łączyć się z centrum IoT. Zanim urządzenie lub moduł będzie można połączyć się z centrum IoT Hub, w rejestrze tożsamości centrum IoT musi znajdować się wpis dla tego urządzenia lub modułu. Urządzenie lub moduł muszą również uwierzytelniać się w centrum IoT na podstawie poświadczeń przechowywanych w rejestrze tożsamości.

W identyfikatorze urządzenia lub modułu przechowywanym w rejestrze tożsamości jest wielkość liter.

Na wysokim poziomie rejestr tożsamości jest kolekcją zasobów tożsamości urządzeń lub modułów z możliwością rest. Po dodaniu wpisu w rejestrze tożsamości program IoT Hub zestaw zasobów dla każdego urządzenia, takich jak kolejka zawierająca komunikaty chmura-urządzenie w locie.

Rejestru tożsamości należy używać w celu:

* Aprowizuj urządzenia lub moduły, które łączą się z centrum IoT.
* Kontroluj dostęp dla węzłów/modułów do urządzeń lub punktów końcowych centrum.

> [!NOTE]
> * Rejestr tożsamości nie zawiera żadnych metadanych specyficznych dla aplikacji.
> * Tożsamość modułu i bliźniacze bliźniacze reprezentacji modułu są dostępne w publicznej wersji zapoznawczej. Poniższe funkcje będą obsługiwane w przypadku tożsamości modułu, gdy jest ona ogólnie dostępna.
>

## <a name="identity-registry-operations"></a>Operacje rejestru tożsamości

Rejestr IoT Hub tożsamości uwidacznia następujące operacje:

* Tworzenie tożsamości urządzenia lub modułu
* Aktualizowanie tożsamości urządzenia lub modułu
* Pobieranie tożsamości urządzenia lub modułu według identyfikatora
* Usuwanie tożsamości urządzenia lub modułu
* Lista maksymalnie 1000 tożsamości
* Eksportowanie tożsamości urządzeń do usługi Azure Blob Storage
* Importowanie tożsamości urządzeń z usługi Azure Blob Storage

Wszystkie te operacje mogą używać optymistycznej współbieżności, jak określono w [dokumencie RFC7232.](https://tools.ietf.org/html/rfc7232)

> [!IMPORTANT]
> Jedynym sposobem pobrania wszystkich tożsamości w rejestrze tożsamości centrum IoT jest użycie [funkcji eksportowania.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

Rejestr IoT Hub tożsamości:

* Nie zawiera żadnych metadanych aplikacji.
* Dostęp można uzyskać jak w słowniku, używając jako klucza **wartości deviceId** lub **moduleId.**
* Nie obsługuje ekspresywnych zapytań.

Rozwiązanie IoT zwykle ma oddzielny magazyn specyficzny dla rozwiązania, który zawiera metadane specyficzne dla aplikacji. Na przykład magazyn specyficzny dla rozwiązania w inteligentnym budynku będzie rejestrował pomieszczenie, w którym wdrożono czujnik temperatury.

> [!IMPORTANT]
> Rejestru tożsamości należy używać tylko na użytek operacji zarządzania urządzeniami i aprowowania. Operacje o wysokiej przepływności w czasie wykonywania nie powinny zależeć od wykonywania operacji w rejestrze tożsamości. Na przykład sprawdzanie stanu połączenia urządzenia przed wysłaniem polecenia nie jest obsługiwanym wzorcem. Upewnij się, że sprawdzasz [wskaźniki ograniczania przepustowości](iot-hub-devguide-quotas-throttling.md) dla rejestru tożsamości i [wzorzec pulsu](iot-hub-devguide-identity-registry.md#device-heartbeat) urządzenia.

## <a name="disable-devices"></a>Wyłączanie urządzeń

Urządzenia można wyłączyć, aktualizując **właściwość stanu** tożsamości w rejestrze tożsamości. Zazwyczaj ta właściwość jest używana w dwóch scenariuszach:

* Podczas procesu aprowizowania aranżacji. Aby uzyskać więcej informacji, zobacz [Device Provisioning](iot-hub-devguide-identity-registry.md#device-provisioning).

* Jeśli z jakiegoś powodu uważasz, że urządzenie zostało naruszone lub zostało nieautoryzowane.

Ta funkcja nie jest dostępna dla modułów.

## <a name="import-and-export-device-identities"></a>Importowanie i eksportowanie tożsamości urządzeń

Użyj operacji asynchronicznych w [punkcie końcowym IoT Hub dostawcy](iot-hub-devguide-endpoints.md) zasobów, aby zbiorczo wyeksportować tożsamości urządzeń z rejestru tożsamości centrum IoT. Eksporty to długotrwałe zadania, które używają kontenera obiektów blob dostarczonego przez klienta do zapisywania danych tożsamości urządzenia odczytanych z rejestru tożsamości.

Użyj operacji asynchronicznych w [IoT Hub dostawcy](iot-hub-devguide-endpoints.md) zasobów, aby zbiorczo zaimportować tożsamości urządzeń do rejestru tożsamości centrum IoT. Importy to długotrwałe zadania, które używają danych w kontenerze obiektów blob dostarczonym przez klienta do zapisu danych tożsamości urządzenia w rejestrze tożsamości.

Aby uzyskać więcej informacji na temat interfejsów API importowania i eksportowania, [zobacz IoT Hub REST API dostawcy zasobów.](/rest/api/iothub/iothubresource) Aby dowiedzieć się więcej na temat uruchamiania zadań importu i eksportu, zobacz Zbiorcze zarządzanie [tożsamościami IoT Hub urządzeń.](iot-hub-bulk-identity-mgmt.md)

Tożsamości urządzeń można również eksportować i importować z usługi IoT Hub za pośrednictwem interfejsu API usługi za pośrednictwem interfejsu [API REST](/rest/api/iothub/service/jobs/createimportexportjob) lub jednego z zestawów SDK IoT Hub [Service.](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

## <a name="device-provisioning"></a>Aprowizowanie urządzeń

Dane urządzenia przechowywane w danym rozwiązaniu IoT zależą od konkretnych wymagań tego rozwiązania. Jednak co najmniej rozwiązanie musi przechowywać tożsamości urządzeń i klucze uwierzytelniania. Azure IoT Hub rejestr tożsamości, który może przechowywać wartości dla każdego urządzenia, takie jak identyfikatory, klucze uwierzytelniania i kody stanu. Rozwiązanie może używać innych usług platformy Azure, takich jak table storage, blob storage lub Cosmos DB do przechowywania jakichkolwiek dodatkowych danych urządzenia.

*Aprowizowanie* urządzeń to proces dodawania początkowych danych urządzenia do magazynów w rozwiązaniu. Aby umożliwić nowe urządzenie nawiązać połączenie z centrum, musisz dodać identyfikator urządzenia i klucze do IoT Hub tożsamości. W ramach procesu aprowizacji może być konieczne zainicjowanie danych specyficznych dla urządzenia w innych magazynach rozwiązań. Możesz również użyć usługi Azure IoT Hub Device Provisioning, aby włączyć bezdotykową aprowizowanie just in time w co najmniej jednym centrum IoT bez konieczności interwencji człowieka. Aby dowiedzieć się więcej, zobacz [dokumentację usługi aprowizowania](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Puls urządzenia

Rejestr IoT Hub tożsamości zawiera pole o nazwie **connectionState**. Pola **connectionState należy używać tylko** podczas opracowywania i debugowania. Rozwiązania IoT nie powinny odpytować pola w czasie wykonywania. Na przykład nie wysyłaj zapytania do pola **connectionState,** aby sprawdzić, czy urządzenie jest połączone przed wysłaniem wiadomości z chmury do urządzenia lub wiadomości SMS. Zalecamy subskrybowanie zdarzenia [  ](iot-hub-event-grid.md#event-types) rozłączenia urządzenia na Event Grid, aby uzyskać alerty i monitorować stan połączenia urządzenia. Z tego [samouczka dowiesz](iot-hub-how-to-order-connection-state-events.md) się, jak zintegrować zdarzenia Urządzenia połączone i Odłączone urządzenia IoT Hub w rozwiązaniu IoT.

Jeśli rozwiązanie IoT musi wiedzieć, czy urządzenie jest połączone, możesz zaimplementować wzorzec *pulsu*.
We wzorcu pulsu urządzenie wysyła komunikaty z urządzenia do chmury co najmniej raz na stały czas (na przykład co najmniej raz na godzinę). W związku z tym, nawet jeśli urządzenie nie ma żadnych danych do wysłania, nadal wysyła pusty komunikat z urządzenia do chmury (zazwyczaj z właściwością, która identyfikuje je jako puls). Po stronie usługi rozwiązanie utrzymuje mapę z ostatnim pulsem odebranym dla każdego urządzenia. Jeśli rozwiązanie nie odbiera komunikatu pulsu w oczekiwanym czasie z urządzenia, zakłada, że występuje problem z urządzeniem.

Bardziej złożona implementacja może [](../azure-monitor/index.yml) obejmować informacje z Azure Monitor i [Azure Resource Health](../service-health/resource-health-overview.md) w celu zidentyfikowania urządzeń, które próbują nawiązać połączenie lub się komunikować, ale nie powiedzą się. Aby dowiedzieć się więcej, zobacz [Monitorowanie IoT Hub](monitor-iot-hub.md) i Sprawdzanie [IoT Hub kondycji zasobu.](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) Podczas implementowania wzorca pulsu upewnij się, że IoT Hub limity przydziału [i ograniczenia.](iot-hub-devguide-quotas-throttling.md)

> [!NOTE]
> Jeśli rozwiązanie IoT używa stanu połączenia wyłącznie do określenia, czy wysyłać komunikaty z chmury do urządzenia, a  komunikaty nie są emitowane do dużych zestawów urządzeń, rozważ użycie prostszego wzorca krótkiego czasu wygaśnięcia. Ten wzorzec osiąga ten sam wynik, co utrzymywanie rejestru stanu połączenia urządzenia przy użyciu wzorca pulsu przy jednoczesnym zachowaniu większej wydajności. Jeśli zażądasz potwierdzenia wiadomości, IoT Hub powiadomi Cię o tym, które urządzenia mogą odbierać komunikaty, a które nie.

## <a name="device-and-module-lifecycle-notifications"></a>Powiadomienia o cyklu życia urządzeń i modułów

IoT Hub powiadamianie rozwiązania IoT o utworzeniu lub usunięciu tożsamości urządzenia przez wysyłanie powiadomień o cyklu życia. W tym celu rozwiązanie IoT musi utworzyć trasę i ustawić źródło danych na wartość *DeviceLifecycleEvents.* Domyślnie nie są wysyłane żadne powiadomienia o cyklu życia, to oznacza, że takie trasy nie istnieją wcześniej. Utworzenie trasy ze źródłem danych równym *DeviceLifecycleEvents* spowoduje, że zdarzenia cyklu życia będą wysyłane zarówno dla tożsamości urządzeń, jak i tożsamości modułów; Jednak zawartość komunikatu będzie się różnić w zależności od tego, czy zdarzenia są generowane dla tożsamości modułów, czy tożsamości urządzeń.  Należy zauważyć, że w przypadku modułów programu IoT Edge przepływ tworzenia tożsamości modułu jest inny niż w przypadku innych modułów. W związku z tym w przypadku modułów programu IoT Edge powiadomienie o utworzeniu jest wysyłane tylko wtedy, gdy odpowiednie urządzenie IoT Edge dla zaktualizowanej tożsamości modułu IoT Edge jest uruchomione. W przypadku wszystkich innych modułów powiadomienia o cyklu życia są wysyłane przy każdej aktualizacji tożsamości modułu IoT Hub stronie.  Komunikat powiadomienia zawiera właściwości i treść.

Właściwości: Właściwości systemu komunikatów mają prefiks `$` symbolu .

Komunikat powiadomienia dla urządzenia:

| Nazwa | Wartość |
| --- | --- |
|$content typu | application/json |
|$iothub-enqueuedtime |  Godzina wysłania powiadomienia |
|$iothub-message-source | deviceLifecycleEvents |
|$content kodowania kodu | utf-8 |
|Optype | **createDeviceIdentity** lub **deleteDeviceIdentity** |
|hubName (nazwa centrum) | Nazwa IoT Hub |
|deviceId | Identyfikator urządzenia |
|operationTimestamp | Sygnatura czasowa operacji ISO8601 |
|iothub-message-schema | deviceLifecycleNotification |

Treść: ta sekcja jest w formacie JSON i reprezentuje bliźniacze reprezentacji utworzonej tożsamości urządzenia. Na przykład

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
$content typu | application/json |
$iothub-enqueuedtime |  Godzina wysłania powiadomienia |
$iothub-message-source | moduleLifecycleEvents |
$content kodowania | utf-8 |
Optype | **createModuleIdentity** lub **deleteModuleIdentity** |
hubName (nazwa centrum) | Nazwa IoT Hub |
moduleId | Identyfikator modułu |
znacznik czasu operacji | Sygnatura czasowa działania ISO8601 |
iothub-message-schema | moduleLifecycleNotification |

Treść: Ta sekcja jest w formacie JSON i reprezentuje bliźniacze reprezentacji utworzonej tożsamości modułu. Na przykład

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

Tożsamości urządzeń są reprezentowane jako dokumenty JSON z następującymi właściwościami:

| Właściwość | Opcje | Opis |
| --- | --- | --- |
| deviceId |wymagane, tylko do odczytu dla aktualizacji |Ciąg z wielkość liter (maksymalnie 128 znaków) 7-bitowych znaków alfanumerycznych ASCII oraz niektórych znaków specjalnych: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |wymagane, tylko do odczytu |Wygenerowany przez centrum IoT ciąg z rozróżnianą wielkością liter o długości do 128 znaków. Ta wartość jest używana do odróżnienia urządzeń o takim samym **deviceId**, po ich usunięciu i ponownego utworzeniu. |
| Etag |wymagane, tylko do odczytu |Ciąg reprezentujący słaby tag ETag dla tożsamości urządzenia, zgodnie z [dokumentem RFC7232.](https://tools.ietf.org/html/rfc7232) |
| Auth |optional |Obiekt złożony zawierający informacje o uwierzytelnianiu i materiały zabezpieczające. |
| auth.symkey |optional |Obiekt złożony zawierający klucz podstawowy i pomocniczy przechowywany w formacie base64. |
| status |wymagane |Wskaźnik dostępu. Można ją **włączyć** lub **wyłączyć.** Jeśli **jest** włączona, urządzenie może nawiązać połączenie. Jeśli **to urządzenie** jest wyłączone, nie może uzyskać dostępu do żadnego punktu końcowego dostępnego dla urządzenia. |
| statusReason |optional |Ciąg o długości 128 znaków, który przechowuje przyczynę stanu tożsamości urządzenia. Dozwolone są wszystkie znaki UTF-8. |
| statusUpdateTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i czas ostatniej aktualizacji stanu. |
| connectionState |tylko do odczytu |Pole wskazujące stan połączenia: **Połączono lub** **Rozłączone.** To pole reprezentuje IoT Hub stanu połączenia urządzenia. **Ważne:** to pole powinno być używane tylko do celów programistyczych/debugowania. Stan połączenia jest aktualizowany tylko dla urządzeń korzystających z MQTT lub AMQP. Ponadto jest on oparty na poleceniach ping na poziomie protokołu (polecenia ping MQTT lub amqp ping) i może mieć maksymalne opóźnienie zaledwie 5 minut. Z tego powodu mogą być wyniki fałszywie dodatnie, takie jak urządzenia zgłaszane jako połączone, ale odłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i czas ostatniej aktualizacji stanu połączenia. |
| lastActivityTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i czas ostatniego podłączenia, odebrania lub wysłania komunikatu przez urządzenie. Ta właściwość jest ostatecznie spójna, ale może zostać opóźniona o 5–10 minut. Z tego powodu nie należy jej używać w scenariuszach produkcyjnych. |

> [!NOTE]
> Stan połączenia może reprezentować IoT Hub tylko widok stanu połączenia. Aktualizacje tego stanu mogą być opóźnione w zależności od warunków sieciowych i konfiguracji.

> [!NOTE]
> Obecnie zestawy SDK urządzeń nie obsługują używania znaków `+` i `#` w **deviceId**.

## <a name="module-identity-properties"></a>Właściwości tożsamości modułu

Tożsamości modułów są reprezentowane jako dokumenty JSON z następującymi właściwościami:

| Właściwość | Opcje | Opis |
| --- | --- | --- |
| deviceId |wymagane, tylko do odczytu dla aktualizacji |Ciąg z wielkość liter (maksymalnie 128 znaków) 7-bitowych znaków alfanumerycznych ASCII oraz niektórych znaków specjalnych: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| moduleId |wymagane, tylko do odczytu w przypadku aktualizacji |Ciąg z wielkość liter (maksymalnie 128 znaków) 7-bitowych znaków alfanumerycznych ASCII oraz niektórych znaków specjalnych: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |wymagane, tylko do odczytu |Wygenerowany przez centrum IoT ciąg z rozróżnianą wielkością liter o długości do 128 znaków. Ta wartość jest używana do odróżnienia urządzeń o takim samym **deviceId**, po ich usunięciu i ponownego utworzeniu. |
| Etag |wymagane, tylko do odczytu |Ciąg reprezentujący słaby tag ETag dla tożsamości urządzenia, zgodnie z dokumentem [RFC7232.](https://tools.ietf.org/html/rfc7232) |
| Auth |optional |Obiekt złożony zawierający informacje o uwierzytelnianiu i materiały zabezpieczające. |
| auth.symkey |optional |Obiekt złożony zawierający klucz podstawowy i pomocniczy przechowywany w formacie base64. |
| status |wymagane |Wskaźnik dostępu. Można ją **włączyć** lub **wyłączyć.** Jeśli **jest** włączona, urządzenie może nawiązać połączenie. Jeśli **to urządzenie** jest wyłączone, nie może uzyskać dostępu do żadnego punktu końcowego dostępnego dla urządzenia. |
| statusReason |optional |Ciąg o długości 128 znaków, który przechowuje przyczynę stanu tożsamości urządzenia. Dozwolone są wszystkie znaki UTF-8. |
| statusUpdateTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i czas ostatniej aktualizacji stanu. |
| connectionState |tylko do odczytu |Pole wskazujące stan połączenia: **Połączono lub** **Rozłączone.** To pole reprezentuje IoT Hub stanu połączenia urządzenia. **Ważne:** to pole powinno być używane tylko do celów programistyczych/debugowania. Stan połączenia jest aktualizowany tylko dla urządzeń korzystających z MQTT lub AMQP. Ponadto jest on oparty na poleceniach ping na poziomie protokołu (ping MQTT lub AMQP ping) i może mieć maksymalne opóźnienie zaledwie 5 minut. Z tego powodu mogą być wyniki fałszywie dodatnie, takie jak urządzenia zgłoszone jako połączone, ale odłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i czas ostatniej aktualizacji stanu połączenia. |
| lastActivityTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i czas ostatniego połączenia, odebrania lub wysłania komunikatu przez urządzenie. |

> [!NOTE]
> Obecnie zestawy SDK urządzeń nie obsługują używania znaków `+` i `#` w **deviceId** i **moduleId**.

## <a name="additional-reference-material"></a>Dodatkowy materiał referencyjny

Inne tematy referencyjne w przewodniku IoT Hub dla deweloperów obejmują:

* [IoT Hub punkty końcowe opisują](iot-hub-devguide-endpoints.md) różne punkty końcowe, które każde centrum IoT udostępnia dla operacji w czasie działania i zarządzania.

* [W temacie Throttling and quotas (Ograniczanie](iot-hub-devguide-quotas-throttling.md) przepustowości i przydziały) opisano przydziały i zachowania ograniczania przepustowości, które mają IoT Hub usługi.

* [Zestawy SDK urządzeń](iot-hub-devguide-sdks.md) i usług Azure IoT to lista różnych zestawów SDK języka, których można używać podczas tworzenia aplikacji zarówno dla urządzeń, jak i usług, które współdziałają z IoT Hub.

* [IoT Hub języka zapytań opisano](iot-hub-devguide-query-language.md) język zapytań, który umożliwia pobieranie informacji IoT Hub o bliźniaczych reprezentacji urządzenia i zadaniach.

* [IoT Hub MQTT zawiera](iot-hub-mqtt-support.md) więcej informacji na temat IoT Hub obsługi protokołu MQTT.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać rejestru tożsamości IoT Hub, mogą Cię zainteresować następujące tematy z IoT Hub deweloperami:

* [Kontrola dostępu do usługi IoT Hub](iot-hub-devguide-security.md)

* [Synchronizowanie stanu i konfiguracji za pomocą bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md)

* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Aby wypróbować niektóre pojęcia opisane w tym artykule, zapoznaj się z następującym samouczkiem IoT Hub samouczka:

* [Rozpoczynanie pracy z usługą Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Aby poznać korzystanie z IoT Hub Device Provisioning Service, aby włączyć bez dotykową aprowizowanie just in time, zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](https://azure.microsoft.com/documentation/services/iot-dps)
