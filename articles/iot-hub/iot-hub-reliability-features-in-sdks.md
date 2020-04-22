---
title: Zarządzanie łącznością usługi IoT Hub & niezawodne przesyłanie wiadomości z szafami SDK z urządzeniami
description: Dowiedz się, jak poprawić łączność z urządzeniem i wiadomości podczas korzystania z zestawów SDK urządzeń usługi Azure IoT Hub
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a645ab1fa4a1be3ec8e939452a1457e84bbafe15
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759677"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Zarządzanie łącznością i niezawodną wiadomością przy użyciu zestawów SDK urządzeń usługi Azure IoT Hub

Ten artykuł zawiera wskazówki wysokiego poziomu, które ułatwiają projektowanie aplikacji urządzeń, które są bardziej odporne. Pokazuje, jak korzystać z funkcji łączności i niezawodnej obsługi wiadomości w zestawach SDK urządzeń IoT platformy Azure. Celem tego przewodnika jest pomoc w zarządzaniu następującymi scenariuszami:

* Naprawianie przerwanego połączenia sieciowego

* Przełączanie między różnymi połączeniami sieciowymi

* Ponowne łączenie z powodu błędów połączenia przejściowego usługi

Szczegóły implementacji mogą się różnić w zależności od języka. Aby uzyskać więcej informacji, zobacz dokumentację interfejsu API lub określony sdk:

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [Zestaw SDK .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Zestaw SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Zestaw SDK dla języka Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (niezawodność jeszcze nie zaimplementowana)

## <a name="designing-for-resiliency"></a>Projektowanie pod kątem odporności

Urządzenia IoT często polegają na nieciągłych lub niestabilnych połączeniach sieciowych (na przykład GSM lub satelitarnych). Błędy mogą wystąpić, gdy urządzenia współdziałają z usługami w chmurze z powodu sporadyczneuzyzgności usługi i awarii na poziomie infrastruktury lub przejściowych. Aplikacja, która działa na urządzeniu musi zarządzać mechanizmami połączenia, ponownego połączenia i logiki ponawiania próby wysyłania i odbierania wiadomości. Ponadto wymagania strategii ponawiania zależą w dużej mierze od scenariusza IoT urządzenia, kontekstu, możliwości.

Zestawy SDK urządzeń usługi Azure IoT Hub mają na celu uproszczenie łączenia się i komunikowania się z chmury do urządzenia i urządzenia do chmury. Te zestawy SDK zapewniają niezawodny sposób łączenia się z centrum Azure IoT Hub i kompleksowy zestaw opcji wysyłania i odbierania wiadomości. Deweloperzy mogą również zmodyfikować istniejącą implementację, aby dostosować lepszą strategię ponawiania prób dla danego scenariusza.

Odpowiednie funkcje SDK, które obsługują łączność i niezawodne wiadomości są omówione w poniższych sekcjach.

## <a name="connection-and-retry"></a>Połączenie i ponowienie próby

W tej sekcji przedstawiono omówienie wzorców ponownego połączenia i ponawiania prób dostępnych podczas zarządzania połączeniami. Zawiera szczegółowe informacje na temat implementacji przy użyciu różnych zasad ponawiania próby w aplikacji urządzenia i wyświetla odpowiednie interfejsy API z sdk urządzeń.

### <a name="error-patterns"></a>Wzorce błędów

Awarie połączeń mogą się zdarzyć na wielu poziomach:

* Błędy sieciowe: odłączone gniazdo i błędy rozpoznawania nazw

* Błędy na poziomie protokołu dla transportu HTTP, AMQP i MQTT: łącza odłączone lub wygasłe sesje

* Błędy na poziomie aplikacji, które wynikają z błędów lokalnych: nieprawidłowe poświadczenia lub zachowanie usługi (na przykład przekroczenie przydziału lub ograniczanie przepustowości)

SDK urządzenia wykrywają błędy na wszystkich trzech poziomach. Błędy związane z systemem operacyjnym i błędy sprzętowe nie są wykrywane i obsługiwane przez sdk urządzeń. Projekt SDK jest oparty na [wskazówki dotyczące obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults#general-guidelines) z Centrum architektury platformy Azure.

### <a name="retry-patterns"></a>Wzorce ponawiania prób

W poniższych krokach opisano proces ponawiania próby po wykryciu błędów połączenia:

1. SDK wykrywa błąd i skojarzony błąd w sieci, protokole lub aplikacji.

2. Moduł SDK używa filtru błędów, aby określić typ błędu i zdecydować, czy konieczne jest ponowienie próby.

3. Jeśli SDK identyfikuje **nieodwracalny błąd,** operacje takie jak połączenie, wysyłanie i odbieranie są zatrzymane. SDK powiadamia użytkownika. Przykłady nieodwracalnych błędów obejmują błąd uwierzytelniania i błąd punktu końcowego.

4. Jeśli zestaw SDK identyfikuje **błąd, który można odzyskać,** ponawia próbę zgodnie z określonymi zasadami ponawiania próby do czasu upływu zdefiniowanego limitu czasu.  Należy zauważyć, że SDK używa **wykładniczego wycofywania z** zasadami ponawiania tresmy domyślnie.
5. Po upływie zdefiniowanego limitu czasu sdk sdk przestaje próbować połączyć się lub wysłać. Powiadamia użytkownika.

6. SDK umożliwia użytkownikowi dołączyć wywołanie zwrotne, aby odbierać zmiany stanu połączenia.

SDKs zapewniają trzy zasady ponawiania próby:

* **Wykładniczy back-off z jitter:** Ta domyślna zasada ponawiania próby wydaje się być agresywne na początku i spowolnić w czasie, aż osiągnie maksymalne opóźnienie. Projekt jest oparty na [wskazówkach ponawiania prób z Centrum architektury platformy Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific). 

* **Niestandardowe ponównie:** W przypadku niektórych języków SDK można zaprojektować niestandardowe zasady ponawiania, które są lepiej dostosowane do twojego scenariusza, a następnie wstrzyknąć je do zasad ponownej próby. Niestandardowe ponównie nie jest dostępny na SDK C i nie jest obecnie obsługiwany w python SDK. Moduł SDK języka Python łączy się ponownie w razie potrzeby.

* **Nie ponawiaj się:** Można ustawić zasady ponawiania na "nie ponawiać próby", który wyłącza logikę ponawiania. SDK próbuje połączyć się raz i wysłać wiadomość raz, przy założeniu, że połączenie jest nawiązywać. Ta zasada jest zwykle używana w scenariuszach z przepustowością lub kosztami. Jeśli wybierzesz tę opcję, wiadomości, które nie zostaną wysłane, zostaną utracone i nie będzie można ich odzyskać.

### <a name="retry-policy-apis"></a>Ponawianie prób interfejsów API zasad

   | SDK | SetRetryPolicy metoda | Realizacje polityki | Wytyczne dotyczące wdrażania |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Domyślnie**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Niestandardowe:** użyj dostępnej [funkcji ponawianiaPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Brak ponownych prób:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementacja C/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Domyślnie**: [Klasa WykładnikaBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Niestandardowe:** implementacja [interfejsu RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Brak ponownych prób:** [Klasa NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementacja java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Domyślnie**: [Klasa WykładnikBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Niestandardowe:** [implementacja interfejsu IRetryPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Brak ponownych prób:** [Klasa NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementacja języka C#](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Węzeł| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Domyślnie**: [Klasa WykładnikaBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Niestandardowe:** implementacja [interfejsu RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Brak ponownych prób:** [Klasa NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementacja węzła](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Nie jest obecnie obsługiwana. | Nie jest obecnie obsługiwana. | Nie jest obecnie obsługiwana. |

Poniższe przykłady kodu ilustrują ten przepływ:

#### <a name="net-implementation-guidance"></a>Wskazówki dotyczące implementacji platformy .NET

Poniższy przykład kodu pokazuje, jak zdefiniować i ustawić domyślną zasadę ponawiania próby:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Aby uniknąć wysokiego użycia procesora CPU, ponownych prób są ograniczane, jeśli kod nie powiedzie się natychmiast. Na przykład, gdy nie ma sieci lub trasy do miejsca docelowego. Minimalny czas wykonania następnej próby ponawiania wynosi 1 sekundę.

Jeśli usługa odpowiada z błędem ograniczania przepustowości, zasady ponawiania jest inna i nie można zmienić za pomocą publicznego interfejsu API:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Mechanizm ponawiania `DefaultOperationTimeoutInMilliseconds`próby zatrzymuje się po , który jest obecnie ustawiony na 4 minuty.

#### <a name="other-languages-implementation-guidance"></a>Inne wytyczne dotyczące wdrażania języków

Przykłady kodu w innych językach można przejrzeć następujące dokumenty implementacji. Repozytorium zawiera przykłady, które pokazują użycie interfejsów API zasad ponawiania.

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [Zestaw SDK .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Zestaw SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Zestaw SDK dla języka Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Zestaw SDK dla języka Python](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z zestawów SDK urządzenia i usługi](./iot-hub-devguide-sdks.md)

* [Korzystanie z zestawu SDK urządzenia IoT dla języka C](./iot-hub-device-sdk-c-intro.md)

* [Opracowywanie zawartości dla urządzeń z ograniczeniami](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Opracowywanie zawartości dla urządzeń przenośnych](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Rozwiązywanie problemów z rozłączaniami urządzeń](iot-hub-troubleshoot-connectivity.md)
