---
title: Zarządzanie IoT Hub łącznością & niezawodnymi komunikatami z zestawami SDK urządzeń
description: Dowiedz się, jak ulepszyć łączność i komunikaty urządzeń przy użyciu zestawów SDK urządzeń usługi Azure IoT Hub
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.custom:
- amqp
- mqtt
- devx-track-csharp
ms.openlocfilehash: bccad7774a8ec4ab415718f28a2adff0bc58863d
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144482"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Zarządzanie łącznością i niezawodną obsługą komunikatów przy użyciu zestawów SDK urządzeń IoT Hub platformy Azure

Ten artykuł zawiera wskazówki wysokiego poziomu ułatwiające projektowanie aplikacji urządzeń, które są bardziej odporne na błędy. Pokazano, jak korzystać z funkcji łączności i niezawodnej obsługi komunikatów w zestawach SDK urządzeń Azure IoT. Celem tego przewodnika jest ułatwienie zarządzania następującymi scenariuszami:

* Naprawianie porzuconego połączenia sieciowego

* Przełączanie między różnymi połączeniami sieciowymi

* Ponowne łączenie z powodu błędów przejściowych połączeń usługi

Szczegóły implementacji mogą się różnić w zależności od języka. Aby uzyskać więcej informacji, zobacz dokumentację interfejsu API lub konkretny zestaw SDK:

* [Zestaw SDK C/iOS](https://github.com/azure/azure-iot-sdk-c)

* [Zestaw SDK platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Zestaw SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Zestaw SDK dla języka Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Zestaw SDK języka Python](https://github.com/Azure/azure-iot-sdk-python) (niezawodność nie została jeszcze zaimplementowana)

## <a name="designing-for-resiliency"></a>Projektowanie pod kątem odporności

Urządzenia IoT często korzystają z nieciągłych lub niestabilnych połączeń sieciowych (na przykład GSM lub satelity). Błędy mogą wystąpić, gdy urządzenia współpracują z usługami w chmurze z powodu sporadycznej dostępności usługi i poziomu infrastruktury lub błędów przejściowych. Aplikacja działająca na urządzeniu musi zarządzać mechanizmami połączenia, ponownego połączenia i logiki ponowień w celu wysyłania i otrzymywania wiadomości. Ponadto wymagania dotyczące strategii ponawiania są zależne od scenariusza usługi IoT, kontekstu i możliwości.

Zestawy SDK urządzeń IoT Hub platformy Azure mają na celu uproszczenie nawiązywania połączeń i komunikacji z chmurą i urządzeniem oraz z urządzenia do chmury. Te zestawy SDK zapewniają niezawodny sposób łączenia się z usługą Azure IoT Hub i kompleksowego zestawu opcji wysyłania i otrzymywania wiadomości. Deweloperzy mogą również modyfikować istniejącą implementację, aby dostosować strategię ponawiania prób w danym scenariuszu.

Odpowiednie funkcje zestawu SDK, które obsługują łączność i niezawodne komunikaty, zostały omówione w poniższych sekcjach.

## <a name="connection-and-retry"></a>Połączenie i ponów próbę

Ta sekcja zawiera omówienie wzorców ponownych połączeń i ponownych prób dostępnych podczas zarządzania połączeniami. Szczegółowe wskazówki dotyczące implementacji dotyczące korzystania z różnych zasad ponawiania prób w aplikacji urządzenia i wyświetlają odpowiednie interfejsy API z zestawów SDK urządzeń.

### <a name="error-patterns"></a>Wzorce błędów

Błędy połączeń mogą wystąpić na wielu poziomach:

* Błędy sieci: rozłączone błędy i rozpoznawanie nazw

* Błędy poziomu protokołu dla protokołu HTTP, AMQP i transportu MQTT: odłączone linki lub wygasłe sesje

* Błędy na poziomie aplikacji wynikające z błędów lokalnych: Nieprawidłowe poświadczenia lub zachowanie usługi (na przykład przekraczanie limitu przydziału lub ograniczanie przepustowości)

Zestawy SDK urządzeń wykrywają błędy na wszystkich trzech poziomach. Błędy związane z systemem operacyjnym i błędy sprzętu nie są wykrywane i obsługiwane przez zestawy SDK urządzeń. Projekt zestawu SDK jest oparty na [wskazówkach dotyczących obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults#general-guidelines) z centrum architektury platformy Azure.

### <a name="retry-patterns"></a>Wzorce ponowień

W poniższych krokach opisano proces ponawiania próby w przypadku wykrycia błędów połączenia:

1. Zestaw SDK wykrywa błąd i związany z nim błąd w sieci, protokole lub aplikacji.

2. Zestaw SDK używa filtru błędów, aby określić typ błędu i zdecydować, czy wymagana jest ponowna próba.

3. Jeśli zestaw SDK zidentyfikuje **nieodwracalny błąd**, operacje takie jak połączenie, wysyłanie i odbieranie są zatrzymane. Zestaw SDK powiadamia użytkownika. Przykładami nieodwracalnych błędów są błędy uwierzytelniania i błędny punkt końcowy.

4. Jeśli zestaw SDK identyfikuje **odwracalny błąd**, ponawia próbę zgodnie z określonymi zasadami ponawiania, dopóki nie upłynie zdefiniowany limit czasu.  Należy pamiętać, że zestaw SDK domyślnie stosuje **wykładnicze** zasady ponawiania.
5. Po upływie zdefiniowanego limitu czasu zestaw SDK przerywa próbę nawiązania połączenia lub wysłania. Powiadamia użytkownika.

6. Zestaw SDK umożliwia użytkownikowi dołączenie wywołania zwrotnego w celu otrzymywania zmian stanu połączenia.

Zestawy SDK udostępniają trzy zasady ponawiania:

* **Wycofywanie wykładnicze z wahaniem**: te domyślne zasady ponawiania nie są na początku i spowalniają działanie, dopóki nie osiągnie on maksymalnego opóźnienia. Projekt jest oparty na [wskazówkach dotyczących ponowień w centrum architektury platformy Azure](/azure/architecture/best-practices/retry-service-specific). 

* **Ponawianie próby niestandardowej**: w przypadku niektórych języków zestawu SDK można zaprojektować niestandardowe zasady ponawiania, które są lepiej dopasowane do danego scenariusza, a następnie wstrzyknąć je do RetryPolicy. Niestandardowa ponowna próba nie jest dostępna w zestawie C SDK i nie jest obecnie obsługiwana w zestawie SDK języka Python. Zestaw SDK języka Python ponownie nawiązuje połączenie w razie konieczności.

* **Nie ponawiaj próby**: można ustawić zasady ponawiania na "bez ponawiania", co spowoduje wyłączenie logiki ponawiania. Zestaw SDK próbuje nawiązać połączenie raz i wysłać komunikat raz, przy założeniu, że połączenie zostanie nawiązane. Te zasady są zwykle używane w scenariuszach z problemami dotyczącymi przepustowości lub kosztów. W przypadku wybrania tej opcji komunikaty, które nie są wysyłane, są tracone i nie można ich odzyskać.

### <a name="retry-policy-apis"></a>Interfejsy API zasad ponawiania

   | SDK | SetRetryPolicy, Metoda | Implementacje zasad | Wskazówki dotyczące implementacji |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Wartość domyślna**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Niestandardowe:** Użyj dostępnych [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Nie próbuj ponownie:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementacja C/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Domyślnie**: [Klasa ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Niestandardowe:** Implementuj [interfejs RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Nie ponowienie:** [NoRetry Klasa](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementacja języka Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient. SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Domyślnie**: [Klasa ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Niestandardowe:** Implementuj [interfejs IRetryPolicy](/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Nie ponowienie:** [NoRetry Klasa](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementacja języka C#](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Węzeł| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Domyślnie**: [Klasa ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java) | [Implementacja węzła](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Nie jest obecnie obsługiwana. | Nie jest obecnie obsługiwana. | Nie jest obecnie obsługiwana. |

Poniższy przykład kodu ilustruje ten przepływ:

#### <a name="net-implementation-guidance"></a>Wskazówki dotyczące implementacji platformy .NET

Poniższy przykład kodu pokazuje, jak zdefiniować i ustawić domyślne zasady ponawiania prób:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Aby uniknąć wysokiego użycia procesora CPU, ponowne próby są ograniczone, jeśli kod zakończy się niepowodzeniem. Na przykład w przypadku braku sieci lub trasy do miejsca docelowego. Minimalny czas wykonywania następnej ponowienia próby to 1 sekunda.

Jeśli usługa reaguje na błąd ograniczania przepustowości, zasady ponawiania są inne i nie można ich zmienić za pomocą publicznego interfejsu API:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Mechanizm ponawiania prób zostaje zatrzymany po `DefaultOperationTimeoutInMilliseconds` , który jest obecnie ustawiony na 4 minuty.

#### <a name="other-languages-implementation-guidance"></a>Wskazówki dotyczące implementacji innych języków

Aby zapoznać się z przykładami kodu w innych językach, zapoznaj się z następującymi dokumentami dotyczącymi implementacji. Repozytorium zawiera przykłady, które demonstrują korzystanie z interfejsów API zasad ponawiania.

* [Zestaw SDK C/iOS](https://github.com/azure/azure-iot-sdk-c)

* [Zestaw SDK platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Zestaw SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Zestaw SDK dla języka Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Zestaw SDK języka Python](https://github.com/Azure/azure-iot-sdk-python) (niezawodność nie została jeszcze zaimplementowana)

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z zestawów SDK urządzenia i usługi](./iot-hub-devguide-sdks.md)

* [Korzystanie z zestawu SDK urządzenia IoT dla języka C](./iot-hub-device-sdk-c-intro.md)

* [Opracowywanie zawartości dla urządzeń z ograniczeniami](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Opracowywanie zawartości dla urządzeń przenośnych](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Rozwiązywanie problemów z rozłączemi urządzeń](iot-hub-troubleshoot-connectivity.md)