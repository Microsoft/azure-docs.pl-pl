---
title: Opis rejestru tożsamości usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — opis rejestru tożsamości usługi IoT Hub i sposób używania go do zarządzania urządzeniami. Zawiera zbiorcze informacje o imporcie i wyeksportowaniu tożsamości urządzeń.
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
ms.openlocfilehash: 2ef259bf76815fdf8672b696d2260fe6a143b798
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730171"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Opis rejestru tożsamości w centrum IoT

Każdy centrum IoT ma rejestr tożsamości, który przechowuje informacje o urządzeniach i modułach dozwolonych do łączenia się z centrum IoT hub. Aby urządzenie lub moduł mogły połączyć się z centrum IoT hub, musi być wpis dla tego urządzenia lub modułu w rejestrze tożsamości centrum IoT hub. Urządzenie lub moduł musi również uwierzytelnić się za pomocą centrum IoT na podstawie poświadczeń przechowywanych w rejestrze tożsamości.

W identyfikatorze urządzenia lub modułu przechowywanym w rejestrze tożsamości rozróżniana jest wielkość liter.

Na wysokim poziomie rejestru tożsamości jest rest-capable kolekcji zasobów tożsamości urządzenia lub modułu. Po dodaniu wpisu w rejestrze tożsamości Usługa IoT Hub tworzy zestaw zasobów na urządzenie, takich jak kolejka zawierająca komunikaty z chmury do urządzenia podczas lotu.

Rejestru tożsamości należy używać w razie potrzeby:

* Aprowizuj urządzenia lub moduły, które łączą się z centrum IoT Hub.
* Kontroluj dostęp na urządzenie/na moduł do urządzenia lub punktów końcowych skierowanych do modułu.

> [!NOTE]
> * Rejestr tożsamości nie zawiera żadnych metadanych specyficznych dla aplikacji.
> * Tożsamość modułu i bliźniaczej reprezentacji modułu jest w publicznej wersji zapoznawczej. Poniższa funkcja będzie obsługiwana w przypadku tożsamości modułu, gdy jest ogólnie dostępna.
>

## <a name="identity-registry-operations"></a>Operacje rejestru tożsamości

Rejestr tożsamości usługi IoT Hub udostępnia następujące operacje:

* Tworzenie tożsamości urządzenia lub modułu
* Aktualizowanie tożsamości urządzenia lub modułu
* Pobieranie tożsamości urządzenia lub modułu według identyfikatora
* Usuwanie tożsamości urządzenia lub modułu
* Lista do 1000 tożsamości
* Eksportowanie tożsamości urządzeń do magazynu obiektów blob platformy Azure
* Importowanie tożsamości urządzeń z magazynu obiektów blob platformy Azure

Wszystkie te operacje można użyć optymistycznej współbieżności, jak określono w [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> Jedynym sposobem pobrania wszystkich tożsamości w rejestrze tożsamości centrum IoT hub jest użycie funkcji [eksportu.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

Rejestr tożsamości usługi IoT Hub:

* Nie zawiera żadnych metadanych aplikacji.
* Można uzyskać dostęp jak słownik, za pomocą **deviceId** lub **moduleId** jako klucz.
* Nie obsługuje ekspresyjnych zapytań.

Rozwiązanie IoT zazwyczaj ma oddzielny magazyn specyficzny dla rozwiązania, który zawiera metadane specyficzne dla aplikacji. Na przykład magazyn specyficzny dla rozwiązania w rozwiązaniu inteligentnego budynku rejestruje pomieszczenie, w którym jest wdrażany czujnik temperatury.

> [!IMPORTANT]
> Rejestru tożsamości należy używać tylko do zarządzania urządzeniami i inicjowania obsługi administracyjnej. Operacje wysokiej przepływności w czasie wykonywania nie powinny zależeć od wykonywania operacji w rejestrze tożsamości. Na przykład sprawdzanie stanu połączenia urządzenia przed wysłaniem polecenia nie jest obsługiwanym wzorcem. Upewnij się, aby sprawdzić [współczynniki ograniczania](iot-hub-devguide-quotas-throttling.md) przepustowości dla rejestru tożsamości i wzorzec [pulsu urządzenia.](iot-hub-devguide-identity-registry.md#device-heartbeat)

## <a name="disable-devices"></a>Wyłączanie urządzeń

Urządzenia można wyłączyć, aktualizując właściwość **stanu** tożsamości w rejestrze tożsamości. Zazwyczaj ta właściwość jest używana w dwóch scenariuszach:

* Podczas procesu aranżacji inicjowania obsługi administracyjnej. Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi administracyjnej urządzeń](iot-hub-devguide-identity-registry.md#device-provisioning).

* Jeśli z jakiegokolwiek powodu uważasz, że urządzenie zostało naruszone lub stało się nieautoryzowane.

Ta funkcja nie jest dostępna dla modułów.

## <a name="import-and-export-device-identities"></a>Importowanie i eksportowanie tożsamości urządzeń

Operacje asynchroniczne w [punkcie końcowym dostawcy zasobów usługi IoT Hub](iot-hub-devguide-endpoints.md) są używane do zbiorczego eksportowania tożsamości urządzeń z rejestru tożsamości centrum IoT. Eksportuje to długotrwałe zadania, które używają kontenera obiektów blob dostarczonych przez klienta do zapisywania danych tożsamości urządzenia odczytanych z rejestru tożsamości.

Operacje asynchroniczne w [punkcie końcowym dostawcy zasobów usługi IoT Hub](iot-hub-devguide-endpoints.md) zbiorczo importują tożsamości urządzeń do rejestru tożsamości centrum IoT. Importy są długotrwałe zadania, które używają danych w kontenerze obiektu blob dostarczone przez klienta do zapisu danych tożsamości urządzenia w rejestrze tożsamości.

Aby uzyskać więcej informacji na temat interfejsów API importowania i eksportowania, zobacz [Interfejsy API RESTI INTERFEJSU DOSTAWCY ZASOBÓW usługi IoT Hub](/rest/api/iothub/iothubresource). Aby dowiedzieć się więcej o uruchamianiu zadań importowania i eksportowania, zobacz [Zbiorcze zarządzanie tożsamościami urządzeń usługi IoT Hub](iot-hub-bulk-identity-mgmt.md).

Tożsamości urządzeń można również eksportować i importować z centrum IoT Hub za pośrednictwem interfejsu API usługi za pośrednictwem [interfejsu API REST](/rest/api/iothub/service/jobclient/createimportexportjob) lub jednego z [SDK usługi IoT](/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-service-sdks)Hub .

## <a name="device-provisioning"></a>Inicjowanie obsługi administracyjnej urządzeń

Dane urządzenia przechowywane przez dane rozwiązania IoT zależą od określonych wymagań tego rozwiązania. Jednak co najmniej rozwiązanie musi przechowywać tożsamości urządzeń i klucze uwierzytelniania. Usługa Azure IoT Hub zawiera rejestr tożsamości, który może przechowywać wartości dla każdego urządzenia, takie jak identyfikatory, klucze uwierzytelniania i kody stanu. Rozwiązanie można użyć innych usług platformy Azure, takich jak magazyn tabel, magazyn obiektów blob lub usługi Cosmos DB do przechowywania dodatkowych danych urządzenia.

*Inicjowanie obsługi administracyjnej urządzenia* to proces dodawania początkowych danych urządzenia do magazynów w rozwiązaniu. Aby włączyć nowe urządzenie do łączenia się z koncentratorem, należy dodać identyfikator urządzenia i klucze do rejestru tożsamości usługi IoT Hub. W ramach procesu inicjowania obsługi administracyjnej może być konieczne zainicjowanie danych specyficznych dla urządzenia w innych magazynach rozwiązań. Można również użyć usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub, aby włączyć obsługę obsługi administracyjnej typu "tylko w czasie" typu "zero-touch" dla jednego lub kilku centrów IoT bez konieczności interwencji międzyludzkiej. Aby dowiedzieć się więcej, zobacz [dokumentację usługi inicjowania obsługi administracyjnej](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Puls urządzenia

Rejestr tożsamości usługi IoT Hub zawiera pole o nazwie **connectionState**. Używaj **pola connectionState** tylko podczas tworzenia i debugowania. Rozwiązania IoT nie powinny wysyłać zapytań do pola w czasie wykonywania. Na przykład nie należy wysyłać kwerend do pola **connectionState,** aby sprawdzić, czy urządzenie jest połączone przed wysłaniem wiadomości z chmury do urządzenia lub wiadomości SMS. Zalecamy zasubskrybowanie zdarzenia [ **rozłączone urządzenie** ](iot-hub-event-grid.md#event-types) w usłudze Event Grid, aby otrzymywać alerty i monitorować stan połączenia urządzenia. Ten [samouczek](iot-hub-how-to-order-connection-state-events.md) służy do integrowania zdarzeń połączonych z urządzeniem i urządzeń z centrum IoT Hub w rozwiązaniu IoT.

Jeśli rozwiązanie IoT musi wiedzieć, czy urządzenie jest podłączone, można zaimplementować *wzorzec pulsu*.
We wzorzec pulsu urządzenie wysyła komunikaty z urządzenia do chmury co najmniej raz na czas określony (na przykład co najmniej raz na godzinę). W związku z tym nawet jeśli urządzenie nie ma żadnych danych do wysłania, nadal wysyła pusty komunikat urządzenia do chmury (zwykle z właściwością, która identyfikuje go jako puls). Po stronie usługi rozwiązanie przechowuje mapę z ostatnim pulsem odebranym dla każdego urządzenia. Jeśli rozwiązanie nie otrzyma komunikat pulsu w oczekiwanym czasie z urządzenia, zakłada, że występuje problem z urządzeniem.

Bardziej złożona implementacja może zawierać informacje z [usługi Azure Monitor](../azure-monitor/index.yml) i usługi Azure Resource [Health](../service-health/resource-health-overview.md) w celu zidentyfikowania urządzeń, które próbują połączyć się lub komunikować się, ale nie, sprawdź monitor z przewodnikiem [diagnostyki.](iot-hub-monitor-resource-health.md) Podczas implementowania wzorca pulsu należy sprawdzić [przydziały i ograniczenia usługi IoT Hub](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Jeśli rozwiązanie IoT używa stanu połączenia wyłącznie do określenia, czy do wysyłania komunikatów z chmury do urządzenia, a wiadomości nie są emitowane do dużych zestawów urządzeń, należy rozważyć użycie prostszego wzorca *krótkiego czasu wygaśnięcia.* Ten wzorzec osiąga taki sam wynik jak utrzymanie rejestru stanu połączenia urządzenia przy użyciu wzorca pulsu, podczas gdy jest bardziej wydajne. Jeśli zażądasz potwierdzenia wiadomości, Usługa IoT Hub może powiadomić Cię o tym, które urządzenia mogą odbierać wiadomości, a które nie.

## <a name="device-and-module-lifecycle-notifications"></a>Powiadomienia o cyklu życia urządzeń i modułów

Usługa IoT Hub może powiadamiać rozwiązanie IoT, gdy tożsamość jest tworzona lub usuwana przez wysyłanie powiadomień o cyklu życia. Aby to zrobić, rozwiązanie IoT musi utworzyć trasę i ustawić źródło danych równe *DeviceLifecycleEvents* lub *ModuleLifecycleEvents*. Domyślnie nie są wysyłane żadne powiadomienia o cyklu życia, oznacza to, że nie istnieją takie trasy. Komunikat powiadomienia zawiera właściwości i treść.

Właściwości: Właściwości systemu wiadomości są poprzedzone `$` symbolem.

Komunikat powiadomienia dla urządzenia:

| Nazwa | Wartość |
| --- | --- |
|Typ $content | application/json |
|$iothub-enqueuedtime |  Czas wysłania powiadomienia |
|źródło $iothub-message | deviceLifecycleEvents |
|Kodowanie $content | utf-8 |
|Optype | **tworzenieUżuidentity** lub **deleteDeviceIdentity** |
|nazwa centrum | Nazwa Centrum IoT |
|deviceId | Identyfikator urządzenia |
|operationStamp | Sygnatura czasowa ISO8601 |
|schemat iothub-message | deviceLifecycleNotification |

Treść: Ta sekcja jest w formacie JSON i reprezentuje bliźniaczej reprezentacji utworzonej tożsamości urządzenia. Na przykład:

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
$iothub-enqueuedtime |  Czas wysłania powiadomienia |
źródło $iothub-message | modułLifecycleEvents |
Kodowanie $content | utf-8 |
Optype | **tworzenieModuleIdentity** lub **deleteModuleIdentity** |
nazwa centrum | Nazwa Centrum IoT |
identyfikator modułu | Identyfikator modułu |
operationStamp | Sygnatura czasowa ISO8601 |
schemat iothub-message | modułLifecycleNotification |

Treść: Ta sekcja jest w formacie JSON i reprezentuje bliźniaczej reprezentacji utworzonej tożsamości modułu. Na przykład:

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
| deviceId |wymagane, tylko do odczytu w aktualizacjach |Ciąg z uwzględnieniem wielkości liter (o długości do 128 znaków) 7-bitowych znaków alfanumerycznej ASCII oraz niektóre znaki specjalne: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId (ida generacji) |wymagane, tylko do odczytu |Ciąg ioT generowane przez centrum, rozróżniana wielkość liter o długości do 128 znaków. Ta wartość służy do rozróżniania urządzeń o tym samym **identyfikatorze deviceId**, gdy zostały usunięte i ponownie utworzone. |
| Etag |wymagane, tylko do odczytu |Ciąg reprezentujący słaby ETag dla tożsamości urządzenia, zgodnie z [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |optional |Obiekt złożony zawierający informacje uwierzytelniające i materiały zabezpieczające. |
| auth.symkey |optional |Obiekt złożony zawierający klucz podstawowy i pomocniczy przechowywany w formacie base64. |
| status |wymagany |Wskaźnik dostępu. Można **włączyć** lub **wyłączyć**. Jeśli **włączono,** urządzenie może się połączyć. Jeśli **wyłączone,** to urządzenie nie może uzyskać dostępu do dowolnego punktu końcowego skierowanego do urządzenia. |
| statusReason |optional |Ciąg znaków o długości 128 znaków, który przechowuje przyczynę stanu tożsamości urządzenia. Dozwolone są wszystkie znaki UTF-8. |
| statusUpdateTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i godzinę ostatniej aktualizacji stanu. |
| connectionState (Stan połączenia) |tylko do odczytu |Pole wskazujące stan połączenia: **Połączone** lub **Rozłączone**. To pole reprezentuje widok Centrum IoT stanu połączenia urządzenia. **Ważne:** To pole powinno być używane tylko do celów rozwoju/debugowania. Stan połączenia jest aktualizowany tylko dla urządzeń korzystających z MQTT lub AMQP. Ponadto, jest on oparty na pingach na poziomie protokołu (Pingi MQTT lub pingi AMQP) i może mieć maksymalne opóźnienie tylko 5 minut. Z tych powodów mogą istnieć fałszywe alarmy, takie jak urządzenia zgłoszone jako połączone, ale które są rozłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i ostatni czas aktualizacji stanu połączenia. |
| lastAktywnośćCzas |tylko do odczytu |Wskaźnik czasowy pokazujący datę i ostatni raz urządzenie połączyło się, odebrało lub wysłało wiadomość. |

> [!NOTE]
> Stan połączenia może reprezentować tylko widok Centrum IoT stanu połączenia. Aktualizacje tego stanu mogą być opóźnione, w zależności od warunków sieciowych i konfiguracji.

> [!NOTE]
> Obecnie sdk urządzenia nie obsługuje przy `+` `#` użyciu znaków i w **deviceId**.

## <a name="module-identity-properties"></a>Właściwości tożsamości modułu

Tożsamości modułu są reprezentowane jako dokumenty JSON z następującymi właściwościami:

| Właściwość | Opcje | Opis |
| --- | --- | --- |
| deviceId |wymagane, tylko do odczytu w aktualizacjach |Ciąg z uwzględnieniem wielkości liter (o długości do 128 znaków) 7-bitowych znaków alfanumerycznej ASCII oraz niektóre znaki specjalne: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| identyfikator modułu |wymagane, tylko do odczytu w aktualizacjach |Ciąg z uwzględnieniem wielkości liter (o długości do 128 znaków) 7-bitowych znaków alfanumerycznej ASCII oraz niektóre znaki specjalne: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId (ida generacji) |wymagane, tylko do odczytu |Ciąg ioT generowane przez centrum, rozróżniana wielkość liter o długości do 128 znaków. Ta wartość służy do rozróżniania urządzeń o tym samym **identyfikatorze deviceId**, gdy zostały usunięte i ponownie utworzone. |
| Etag |wymagane, tylko do odczytu |Ciąg reprezentujący słaby ETag dla tożsamości urządzenia, zgodnie z [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |optional |Obiekt złożony zawierający informacje uwierzytelniające i materiały zabezpieczające. |
| auth.symkey |optional |Obiekt złożony zawierający klucz podstawowy i pomocniczy przechowywany w formacie base64. |
| status |wymagany |Wskaźnik dostępu. Można **włączyć** lub **wyłączyć**. Jeśli **włączono,** urządzenie może się połączyć. Jeśli **wyłączone,** to urządzenie nie może uzyskać dostępu do dowolnego punktu końcowego skierowanego do urządzenia. |
| statusReason |optional |Ciąg znaków o długości 128 znaków, który przechowuje przyczynę stanu tożsamości urządzenia. Dozwolone są wszystkie znaki UTF-8. |
| statusUpdateTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i godzinę ostatniej aktualizacji stanu. |
| connectionState (Stan połączenia) |tylko do odczytu |Pole wskazujące stan połączenia: **Połączone** lub **Rozłączone**. To pole reprezentuje widok Centrum IoT stanu połączenia urządzenia. **Ważne:** To pole powinno być używane tylko do celów rozwoju/debugowania. Stan połączenia jest aktualizowany tylko dla urządzeń korzystających z MQTT lub AMQP. Ponadto, jest on oparty na pingach na poziomie protokołu (Pingi MQTT lub pingi AMQP) i może mieć maksymalne opóźnienie tylko 5 minut. Z tych powodów mogą istnieć fałszywe alarmy, takie jak urządzenia zgłoszone jako połączone, ale które są rozłączone. |
| connectionStateUpdatedTime |tylko do odczytu |Wskaźnik czasowy pokazujący datę i ostatni czas aktualizacji stanu połączenia. |
| lastAktywnośćCzas |tylko do odczytu |Wskaźnik czasowy pokazujący datę i ostatni raz urządzenie połączyło się, odebrało lub wysłało wiadomość. |

> [!NOTE]
> Obecnie sdk urządzenia nie obsługuje przy `+` `#` użyciu znaków i w **deviceId** i **moduleId**.

## <a name="additional-reference-material"></a>Dodatkowy materiał referencyjny

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md) opisano różne punkty końcowe, które każdy centrum IoT udostępnia dla operacji wykonywania i zarządzania.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisuje przydziały i zachowania ograniczania przepustowości, które mają zastosowanie do usługi Centrum IoT.

* [Zestaw SDK urządzeń i usług Usługi Azure IoT](iot-hub-devguide-sdks.md) zawiera listę zestawów SDK w różnych językach, których można używać podczas tworzenia aplikacji na urządzenia i usługi, które współdziałają z centrum IoT Hub.

* [Język zapytań centrum IoT](iot-hub-devguide-query-language.md) zawiera opis języka zapytań, którego można użyć do pobierania informacji z Usługi IoT Hub o bliźniaczych urządzeń i zadaniach.

* [Obsługa protokołu MQTT w uężoniać w uiszczaprzestwu IoT Hub](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi protokołu MQTT w centrum IoT Hub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz instrukcje korzystania z rejestru tożsamości Usługi IoT Hub, możesz zainteresować się następującymi tematami przewodnika dla deweloperów usługi IoT Hub:

* [Kontrola dostępu do centrum IoT Hub](iot-hub-devguide-security.md)

* [Synchronizowanie stanu i konfiguracji za pomocą bliźniąt urządzeń](iot-hub-devguide-device-twins.md)

* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Aby wypróbować niektóre pojęcia opisane w tym artykule, zobacz następujący samouczek Usługi IoT Hub:

* [Wprowadzenie do usługi Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Aby eksplorować korzystanie z usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub, aby włączyć obsługę obsługi administracyjnej typu "tylko w czasie" typu "zero-touch", zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](https://azure.microsoft.com/documentation/services/iot-dps)
