---
title: Eksportowanie danych z platformy Azure IoT Central | Microsoft Docs
description: Jak używać nowego eksportu danych do eksportowania danych IoT do platformy Azure i niestandardowych miejsc docelowych w chmurze.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 7152012c7c4a342c7491e5f8b835eaede4269c4c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522618"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Eksportowanie danych IoT do miejsc docelowych w chmurze przy użyciu eksportu danych

> [!Note]
> W tym artykule opisano funkcje eksportu danych w IoT Central. Aby uzyskać informacje o starszych funkcjach eksportu danych, zobacz [Eksportowanie danych IoT do lokalizacji docelowych w chmurze przy użyciu funkcji eksportu danych (starsza wersja)](./howto-export-data-legacy.md).

W tym artykule opisano, jak używać nowej funkcji eksportu danych w usłudze Azure IoT Central. Użyj tej funkcji, aby ciągle eksportować przefiltrowane i wzbogacone dane IoT z aplikacji IoT Central. Eksport danych umożliwia wypychanie zmian w czasie niemal rzeczywistym do innych części rozwiązania w chmurze w celu uzyskania szczegółowych informacji, analiz i magazynu.

Możesz na przykład:

- Ciągle Eksportuj dane telemetryczne i zmiany właściwości w formacie JSON w czasie niemal rzeczywistym.
- Przefiltruj strumienie danych, aby wyeksportować dane, które pasują do warunków niestandardowych.
- Wzbogacaj strumienie danych z wartościami niestandardowymi i wartościami właściwości z urządzenia.
- Wyślij dane do miejsc docelowych, takich jak Azure Event Hubs, Azure Service Bus, Azure Blob Storage i punkty końcowe elementu webhook.

> [!Tip]
> Po włączeniu eksportu danych można pobrać tylko dane z tego momentu. Obecnie nie można pobrać danych przez czas, gdy eksport danych jest wyłączony. Aby zachować więcej danych historycznych, należy wcześniej włączyć eksport danych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z funkcji eksportu danych, musisz mieć [aplikację v3](howto-get-app-info.md)i mieć uprawnienie [Eksportowanie danych](howto-manage-users-roles.md) .

Jeśli masz aplikację w wersji 2, zobacz [Migrowanie aplikacji v2 IoT Central do wersji v3](howto-migrate.md).

## <a name="set-up-export-destination"></a>Skonfiguruj miejsce docelowe eksportu

Miejsce docelowe eksportu musi istnieć przed skonfigurowaniem eksportu danych. Następujące typy miejsc docelowych są obecnie dostępne:

- Azure Event Hubs
- Kolejka usługi Azure Service Bus
- Temat usługi Azure Service Bus
- Azure Blob Storage
- Webhook

### <a name="create-an-event-hubs-destination"></a>Utwórz miejsce docelowe Event Hubs

Jeśli nie masz istniejącej przestrzeni nazw Event Hubs do eksportowania do programu, wykonaj następujące kroki:

1. Utwórz [nową przestrzeń nazw Event Hubs w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Więcej informacji można znaleźć w witrynie [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md).

1. Utwórz centrum zdarzeń w przestrzeni nazw Event Hubs. Przejdź do obszaru nazw, a następnie wybierz pozycję **+ centrum zdarzeń** u góry, aby utworzyć wystąpienie centrum zdarzeń.

1. Wygeneruj klucz, który ma być używany podczas konfigurowania eksportu danych w IoT Central:

    - Wybierz utworzone wystąpienie centrum zdarzeń.
    - Wybierz pozycję **ustawienia > zasady dostępu współdzielonego**.
    - Utwórz nowy klucz lub wybierz istniejący klucz, który ma uprawnienia do **wysyłania** .
    - Skopiuj podstawowe lub pomocnicze parametry połączenia. Te parametry połączenia służą do konfigurowania nowego miejsca docelowego w IoT Central.
    - Alternatywnie można wygenerować parametry połączenia dla całej przestrzeni nazw Event Hubs:
        1. Przejdź do przestrzeni nazw Event Hubs w Azure Portal.
        2. W obszarze **Ustawienia** wybierz pozycję **zasady dostępu współdzielonego** .
        3. Utwórz nowy klucz lub wybierz istniejący klucz, który ma uprawnienia do **wysyłania** .
        4. Skopiuj podstawowe lub pomocnicze parametry połączenia
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Tworzenie kolejki Service Bus lub docelowej tematu

Jeśli nie masz istniejącej przestrzeni nazw Service Bus do eksportowania do programu, wykonaj następujące kroki:

1. Utwórz [nową przestrzeń nazw Service Bus w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Więcej informacji można znaleźć w dokumentacji [Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Aby utworzyć kolejkę lub temat do eksportowania, przejdź do obszaru nazw Service Bus i wybierz pozycję **+ Queue** lub **+ temat**.

1. Wygeneruj klucz, który ma być używany podczas konfigurowania eksportu danych w IoT Central:

    - Wybierz utworzoną kolejkę lub temat.
    - Wybierz pozycję **ustawienia/zasady dostępu współdzielonego**.
    - Utwórz nowy klucz lub wybierz istniejący klucz, który ma uprawnienia do **wysyłania** .
    - Skopiuj podstawowe lub pomocnicze parametry połączenia. Te parametry połączenia służą do konfigurowania nowego miejsca docelowego w IoT Central.
    - Alternatywnie można wygenerować parametry połączenia dla całej przestrzeni nazw Service Bus:
        1. Przejdź do przestrzeni nazw Service Bus w Azure Portal.
        2. W obszarze **Ustawienia** wybierz pozycję **zasady dostępu współdzielonego** .
        3. Utwórz nowy klucz lub wybierz istniejący klucz, który ma uprawnienia do **wysyłania** .
        4. Skopiuj podstawowe lub pomocnicze parametry połączenia

### <a name="create-an-azure-blob-storage-destination"></a>Utwórz miejsce docelowe Blob Storage platformy Azure

Jeśli nie masz istniejącego konta usługi Azure Storage do eksportowania, wykonaj następujące czynności:

1. Utwórz [nowe konto magazynu w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Możesz dowiedzieć się więcej na temat tworzenia nowych [kont usługi Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md) lub [kont magazynu Azure Data Lake Storage v2](../../storage/common/storage-account-create.md). Eksport danych umożliwia zapisanie danych tylko na kontach magazynu, które obsługują blokowe obiekty blob. Na poniższej liście przedstawiono znane zgodne typy kont magazynu:

    |Warstwa wydajności|Typ konta|
    |-|-|
    |Standardowa|Ogólnego przeznaczenia v2|
    |Standardowa|Ogólnego przeznaczenia v1|
    |Standardowa|Blob Storage|
    |Premium|Blokuj Magazyn obiektów BLOB|

1. Aby utworzyć kontener na koncie magazynu, przejdź do konta magazynu. W obszarze **BLOB Service** wybierz pozycję **Przeglądaj obiekty blob**. Wybierz pozycję **+ kontener** u góry, aby utworzyć nowy kontener.

1. Wygeneruj parametry połączenia dla konta magazynu, przechodząc do **ustawień > klucze dostępu**. Skopiuj jeden z dwóch parametrów połączenia.

### <a name="create-a-webhook-endpoint"></a>Tworzenie punktu końcowego elementu webhook

Dane można eksportować do publicznie dostępnego punktu końcowego elementu webhook protokołu HTTP. Możesz utworzyć punkt końcowy elementu webhook testowego za pomocą [RequestBin](https://requestbin.net/). RequestBin ogranicza żądanie, gdy zostanie osiągnięty limit żądań:

1. Otwórz [RequestBin](https://requestbin.net/).
2. Utwórz nowy RequestBin i skopiuj **adres URL bin**. Ten adres URL jest używany podczas testowania eksportu danych.

## <a name="set-up-data-export"></a>Konfigurowanie eksportu danych

Teraz, gdy masz miejsce docelowe eksportu danych do programu, skonfiguruj eksport danych w aplikacji IoT Central:

1. Zaloguj się do aplikacji IoT Central.

1. W lewym okienku wybierz pozycję **eksport danych**.

    > [!Tip]
    > Jeśli nie widzisz **eksportu danych** w okienku po lewej stronie, nie masz uprawnień do konfigurowania eksportu danych w aplikacji. Skontaktuj się z administratorem, aby skonfigurować eksportowanie danych.

1. Wybierz pozycję **+ Nowy eksport**.

1. Wprowadź nazwę wyświetlaną dla nowego eksportu i upewnij się, że jest **włączony** eksport danych.

1. Wybierz typ danych do wyeksportowania. W poniższej tabeli wymieniono obsługiwane typy eksportu danych:

    | Typ danych | Opis | Format danych |
    | :------------- | :---------- | :----------- |
    |  Telemetria | Eksportowanie komunikatów telemetrycznych z urządzeń w czasie niemal rzeczywistym. Każdy wyeksportowany komunikat zawiera pełną zawartość oryginalnego komunikatu urządzenia, znormalizowany.   |  [Format wiadomości telemetrycznych](#telemetry-format)   |
    | Zmiany właściwości | Eksportuj zmiany do właściwości urządzenia i chmury w czasie niemal rzeczywistym. W przypadku właściwości urządzenia tylko do odczytu są eksportowane zmiany raportowanych wartości. Dla właściwości do odczytu i zapisu są eksportowane zarówno raportowane, jak i żądane wartości. | [Format komunikatu zmiany właściwości](#property-changes-format) |

<a name="DataExportFilters"></a>
1. Opcjonalnie dodaj filtry, aby zmniejszyć ilość wyeksportowanych danych. Istnieją różne typy filtrów dostępne dla każdego typu eksportu danych:

    Aby odfiltrować dane telemetryczne, możesz:

    - **Przefiltruj** wyeksportowany strumień, tak aby zawierał tylko dane telemetryczne z urządzeń, które pasują do nazwy urządzenia, identyfikatora urządzenia i warunku filtru szablonu urządzenia.
    - **Filtruj** możliwości: w przypadku wybrania elementu telemetrii z listy rozwijanej **Nazwa** wyeksportowany strumień zawiera tylko dane telemetryczne, które spełniają warunek filtru. W przypadku wybrania elementu właściwości urządzenia lub chmury na liście rozwijanej **Nazwa** wyeksportowany strumień zawiera tylko dane telemetryczne z urządzeń mających właściwości pasujące do warunku filtru.
    - **Filtr właściwości komunikatów**: urządzenia korzystające z zestawów SDK urządzeń mogą wysyłać *Właściwości komunikatów* lub *właściwości aplikacji* w każdym komunikacie telemetrii. Właściwości są zbiorem par klucz-wartość, które oznaczają komunikat przy użyciu identyfikatorów niestandardowych. Aby utworzyć filtr właściwości wiadomości, wprowadź klucz właściwości komunikatu, którego szukasz, i określ warunek. Eksportowane są tylko komunikaty telemetryczne z właściwościami, które pasują do określonego warunku filtru. Obsługiwane są następujące operatory porównywania ciągów: Equals, nie równa się, zawiera, nie zawiera, istnieje, nie istnieje. [Dowiedz się więcej o właściwościach aplikacji IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md)dokumentach.

    Aby filtrować zmiany właściwości, użyj **filtru możliwości**. Wybierz element właściwości z listy rozwijanej. Wyeksportowany strumień zawiera tylko zmiany wybranej właściwości, które spełniają warunek filtru.

<a name="DataExportEnrichmnents"></a>
1. Opcjonalnie można wzbogacić wyeksportowany komunikat z dodatkowymi metadanymi pary klucz-wartość. Następujące wzbogacania są dostępne dla typów eksportu danych telemetrii i właściwości:

    - **Ciąg niestandardowy**: dodaje niestandardowy ciąg statyczny do każdego komunikatu. Wprowadź dowolny klucz i wprowadź dowolną wartość ciągu.
    - **Właściwość**: dodaje bieżącą właściwość lub wartość właściwości Cloud urządzenia do każdego komunikatu. Wprowadź dowolny klucz, a następnie wybierz urządzenie lub właściwość chmury. Jeśli wyeksportowany komunikat pochodzi z urządzenia, które nie ma określonej właściwości, wyeksportowany komunikat nie pobiera wzbogacania.

1. Dodaj nowe miejsce docelowe lub Dodaj miejsce docelowe, które zostało już utworzone. Wybierz łącze **Utwórz nowe** i Dodaj następujące informacje:

    - **Nazwa miejsca docelowego**: Nazwa wyświetlana miejsca docelowego w IoT Central.
    - **Typ docelowy**: Wybierz typ lokalizacji docelowej. Jeśli miejsce docelowe nie zostało jeszcze skonfigurowane, zobacz [Konfigurowanie docelowego eksportu](#set-up-export-destination).
    - W przypadku usługi Azure Event Hubs, kolejki lub tematu Azure Service Bus, wklej parametry połączenia dla zasobu i w razie potrzeby wprowadź nazwę centrum zdarzeń z uwzględnieniem wielkości liter, kolejki lub tematu.
    - W przypadku usługi Azure Blob Storage wklej parametry połączenia dla zasobu i w razie potrzeby wprowadź nazwę kontenera z uwzględnieniem wielkości liter.
    - W przypadku elementu webhook Wklej adres URL wywołania zwrotnego dla punktu końcowego elementu webhook. Opcjonalnie można skonfigurować autoryzację elementu webhook (OAuth 2,0 i token autoryzacji) i dodać niestandardowe nagłówki. 
        - W przypadku protokołu OAuth 2,0 obsługiwane są tylko przepływy poświadczeń klienta. Gdy miejsce docelowe zostanie zapisane, IoT Central będzie komunikować się z dostawcą protokołu OAuth w celu pobrania tokenu autoryzacji. Ten token zostanie dołączony do nagłówka "Authorization" dla każdej wiadomości wysyłanej do tego miejsca docelowego.
        - W przypadku tokenu autoryzacji można określić wartość tokenu, która zostanie bezpośrednio dołączona do nagłówka "Autoryzacja" dla każdej wiadomości wysyłanej do tego miejsca docelowego.
    - Wybierz przycisk **Utwórz**.

1. Wybierz pozycję **+ miejsce docelowe** i wybierz lokalizację docelową z listy rozwijanej. Do jednego eksportu można dodać maksymalnie pięć miejsc docelowych.

1. Po zakończeniu konfigurowania eksportu wybierz pozycję **Zapisz**. Po kilku minutach dane zostaną wyświetlone w Twoich miejscach docelowych.

## <a name="monitor-your-export"></a>Monitorowanie eksportu

Oprócz wyświetlania stanu eksportów w IoT Central, można użyć [Azure monitor](../../azure-monitor/overview.md) do sprawdzenia ilości eksportowanych danych i wszelkich błędów eksportu. Można uzyskać dostęp do metryk eksportu i kondycji urządzeń na wykresach w Azure Portal, za pomocą interfejsu API REST lub z zapytaniami w programie PowerShell lub interfejsie wiersza polecenia platformy Azure. Obecnie można monitorować następujące metryki eksportu danych w Azure Monitor:

- Liczba komunikatów przychodzących do eksportu przed zastosowaniem filtrów.
- Liczba komunikatów, które przechodzą przez filtry.
- Liczba komunikatów, które zostały pomyślnie wyeksportowane do miejsc docelowych.
- Liczba napotkanych błędów.

Aby dowiedzieć się więcej, zobacz [monitorowanie ogólnej kondycji aplikacji IoT Central](howto-monitor-application-health.md).

## <a name="destinations"></a>Miejsca docelowe

### <a name="azure-blob-storage-destination"></a>Miejsce docelowe Blob Storage platformy Azure

Dane są eksportowane raz na minutę, przy czym każdy plik zawierający partię zmian od poprzedniego eksportu. Eksportowane dane są zapisywane w formacie JSON. Domyślne ścieżki do wyeksportowanych danych na koncie magazynu są następujące:

- Dane telemetryczne: _{Container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Zmiany właściwości: _{Container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Aby przeglądać wyeksportowane pliki w Azure Portal, przejdź do pliku i wybierz pozycję **Edytuj obiekt BLOB**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Event Hubs platformy Azure i miejsca docelowe Azure Service Bus

Dane są eksportowane niemal w czasie rzeczywistym. Dane są w treści wiadomości i są w formacie JSON zakodowanym jako UTF-8.

Zbiór właściwości w postaci adnotacji lub systemu zawiera `iotcentral-device-id` `iotcentral-application-id` pola,, `iotcentral-message-source` i, `iotcentral-message-type` które mają takie same wartości jak odpowiadające im pola w treści komunikatu.

### <a name="webhook-destination"></a>Miejsce docelowe elementu webhook

W przypadku miejsc docelowych elementów webhook dane są również eksportowane niemal w czasie rzeczywistym. Dane w treści wiadomości mają taki sam format jak dla Event Hubs i Service Bus.

## <a name="telemetry-format"></a>Format telemetrii

Każdy wyeksportowany komunikat zawiera znormalizowaną postać pełnej wiadomości wysyłanej przez urządzenie w treści wiadomości. Komunikat jest w formacie JSON i zakodowany jako UTF-8. Informacje w każdym komunikacie obejmują:

- `applicationId`: Identyfikator aplikacji IoT Central.
- `messageSource`: Źródło wiadomości — `telemetry` .
- `deviceId`: Identyfikator urządzenia, które wysłało komunikat telemetrii.
- `schema`: Nazwa i wersja schematu ładunku.
- `templateId`: Identyfikator szablonu urządzenia skojarzonego z urządzeniem.
- `enrichments`: Wszelkie wzbogacenia zostały skonfigurowane podczas eksportowania.
- `messageProperties`: Dodatkowe właściwości wysyłane przez urządzenie z wiadomością. Te właściwości są czasami określane jako *właściwości aplikacji*. [Dowiedz się więcej z dokumentacji IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

W przypadku Event Hubs i Service Bus IoT Central wyeksportować nowy komunikat szybko po odebraniu komunikatu z urządzenia. We właściwościach użytkownika (nazywanych również właściwościami aplikacji) każdego komunikatu,, `iotcentral-device-id` `iotcentral-application-id` i `iotcentral-message-source` są dołączone automatycznie.

W przypadku usługi BLOB Storage komunikaty są przetwarzane wsadowo i eksportowane raz na minutę.

Poniższy przykład pokazuje wyeksportowany komunikat telemetrii:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>Właściwości komunikatu

Komunikaty telemetryczne mają właściwości metadanych oprócz ładunku telemetrii. W poprzednim fragmencie kodu przedstawiono przykłady komunikatów systemowych, takich jak `deviceId` i `enqueuedTime` . Aby dowiedzieć się więcej o właściwościach komunikatów systemowych, zobacz [Właściwości systemu D2C IoT Hub messages](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

Możesz dodać właściwości do komunikatów telemetrycznych, jeśli trzeba dodać niestandardowe metadane do wiadomości telemetrycznych. Na przykład należy dodać sygnaturę czasową, gdy urządzenie tworzy komunikat.

Poniższy fragment kodu przedstawia sposób dodawania `iothub-creation-time-utc` właściwości do komunikatu podczas tworzenia go na urządzeniu:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

Poniższy fragment kodu przedstawia tę właściwość w komunikacie eksportowanym do magazynu obiektów blob:

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>Format zmian właściwości

Każdy komunikat lub rekord przedstawia jedną zmianę w właściwości urządzenia lub chmury. W przypadku właściwości urządzenia tylko zmiany w raportowanej wartości są eksportowane jako osobny komunikat. Informacje zawarte w wyeksportowanym komunikacie obejmują:

- `applicationId`: Identyfikator aplikacji IoT Central.
- `messageSource`: Źródło wiadomości — `properties` .
- `messageType`: Albo `cloudPropertyChange` , `devicePropertyDesiredChange` , lub `devicePropertyReportedChange` .
- `deviceId`: Identyfikator urządzenia, które wysłało komunikat telemetrii.
- `schema`: Nazwa i wersja schematu ładunku.
- `templateId`: Identyfikator szablonu urządzenia skojarzonego z urządzeniem.
- `enrichments`: Wszelkie wzbogacenia zostały skonfigurowane podczas eksportowania.

W przypadku Event Hubs i Service Bus IoT Central eksportuje dane nowych komunikatów do centrum zdarzeń lub kolejki Service Bus lub tematu niemal w czasie rzeczywistym. We właściwościach użytkownika (nazywanych również właściwościami aplikacji) każdego komunikatu,,, `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` i `iotcentral-message-type` są dołączone automatycznie.

W przypadku usługi BLOB Storage komunikaty są przetwarzane wsadowo i eksportowane raz na minutę.

W poniższym przykładzie przedstawiono eksportowany komunikat zmiany właściwości otrzymany na platformie Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Porównanie starszego eksportu danych i eksportu danych

W poniższej tabeli przedstawiono różnice między [starym eksportem danych](howto-export-data-legacy.md) i nowymi funkcjami eksportu danych:

| Możliwości  | Eksport starszych danych | Nowy eksport danych |
| :------------- | :---------- | :----------- |
| Dostępne typy danych | Dane telemetryczne, urządzenia, szablony urządzeń | Dane telemetryczne, zmiany właściwości |
| Filtrowanie | Brak | Zależy od typu eksportowanych danych. W przypadku telemetrii filtrowanie według telemetrii, właściwości komunikatów i wartości właściwości |
| Wzbogaceń | Brak | Wzbogacanie z niestandardowym ciągiem lub wartością właściwości na urządzeniu |
| Miejsca docelowe | Azure Event Hubs, kolejki Azure Service Bus i tematy, Blob Storage platformy Azure | Analogicznie jak w przypadku starszego eksportu danych i elementów webhook|
| Obsługiwane wersje aplikacji | V2, V3 | Tylko wersja 3 |
| Istotne limity | 5 eksportów na aplikację, 1 miejsce docelowe na eksport | 10 eksportów — połączenia docelowe na aplikację |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z nowego eksportu danych, sugerowanym następnym krokiem jest zapoznanie się z [tematem korzystanie z analiz w programie IoT Central](./howto-create-analytics.md)
