---
title: Eksportowanie danych z IoT Central | Microsoft Docs
description: Jak używać nowego eksportu danych do eksportowania danych IoT do platformy Azure i niestandardowych miejsc docelowych w chmurze.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/04/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 737fe4b334e60f1b51e8f60f39e8821588a6841c
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010314"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-preview"></a>Eksportowanie danych IoT do miejsc docelowych w chmurze przy użyciu funkcji eksportu danych (wersja zapoznawcza)

> [!Note]
> Szukasz starszego eksportu danych? Dokumenty eksportu danych można znaleźć [tutaj](./howto-export-data.md). Aby dowiedzieć się więcej o różnicach między nowym eksportem danych i eksportowaniem starszej wersji, zobacz [tabelę porównania](#comparison-of-legacy-data-export-and-new-data-export).

W tym artykule opisano sposób korzystania z nowych funkcji w wersji zapoznawczej eksportu danych w usłudze Azure IoT Central. Za pomocą tej funkcji możesz ciągle eksportować przefiltrowane i wzbogacone dane IoT do usług w chmurze. Eksport danych umożliwia wypychanie zmian w czasie niemal rzeczywistym do innych części rozwiązania w chmurze w celu uzyskania szczegółowych informacji, analiz i magazynu. 

 Możesz na przykład:
-   Ciągle Eksportuj dane telemetryczne i zmiany właściwości w formacie JSON w czasie niemal rzeczywistym
-   Odfiltruj te strumienie danych, aby wyeksportować określone funkcje, które pasują do warunków niestandardowych
-   Wzbogacanie strumieni danych z wartościami niestandardowymi i wartościami właściwości z urządzenia
-   Wyślij te dane do miejsc docelowych, takich jak Azure Event Hubs, Azure Service Bus, Azure Blob Storage i punkty końcowe elementu webhook

> [!Note]
> Po włączeniu eksportu danych można pobrać tylko dane z tego momentu. Obecnie nie można pobrać danych przez czas, gdy eksport danych jest wyłączony. Aby zachować więcej danych historycznych, należy wcześniej włączyć eksport danych.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz być administratorem w aplikacji IoT Central lub mieć uprawnienia do eksportowania danych.

## <a name="set-up-export-destination"></a>Skonfiguruj miejsce docelowe eksportu

Miejsce docelowe eksportu musi istnieć przed skonfigurowaniem eksportu danych. Są to dostępne typy miejsc docelowych:
  - Azure Event Hubs
  - Kolejka usługi Azure Service Bus
  - Temat usługi Azure Service Bus
  - Azure Blob Storage
  - Webhook

### <a name="create-an-event-hubs-destination"></a>Utwórz miejsce docelowe Event Hubs

Jeśli nie masz istniejącej przestrzeni nazw Event Hubs do eksportowania do programu, wykonaj następujące kroki:

1. Utwórz [nową przestrzeń nazw Event Hubs w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Więcej informacji można znaleźć w witrynie [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md).

2. Utwórz centrum zdarzeń w przestrzeni nazw Event Hubs. Przejdź do obszaru nazw, a następnie wybierz pozycję **+ centrum zdarzeń** u góry, aby utworzyć wystąpienie centrum zdarzeń.

3. Wygeneruj klucz, który będzie używany w IoT Central, aby skonfigurować eksport danych. 
    - Kliknij utworzone wystąpienie centrum zdarzeń. 
    - Kliknij pozycję **ustawienia/zasady dostępu współdzielonego**. 
    - Utwórz nowy klucz lub wybierz istniejący klucz, który ma uprawnienia do **wysyłania** . 
    - Skopiuj podstawowe lub pomocnicze parametry połączenia. Użyjesz tej opcji, aby skonfigurować nowe miejsce docelowe w IoT Central.

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Tworzenie kolejki Service Bus lub docelowej tematu

Jeśli nie masz istniejącej przestrzeni nazw Service Bus do eksportowania do programu, wykonaj następujące kroki:

1. Utwórz [nową przestrzeń nazw Service Bus w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Więcej informacji można znaleźć w dokumentacji [Azure Service Bus](../../service-bus-messaging/service-bus-create-namespace-portal.md).

2. Aby utworzyć kolejkę lub temat do eksportowania, przejdź do obszaru nazw Service Bus i wybierz pozycję **+ Queue** lub **+ temat**.

3. Wygeneruj klucz, który będzie używany w IoT Central, aby skonfigurować eksport danych. 
    - Kliknij utworzoną kolejkę lub temat. 
    - Kliknij pozycję **ustawienia/zasady dostępu współdzielonego**. 
    - Utwórz nowy klucz lub wybierz istniejący klucz, który ma uprawnienia do **wysyłania** . 
    - Skopiuj podstawowe lub pomocnicze parametry połączenia. Użyjesz tej opcji, aby skonfigurować nowe miejsce docelowe w IoT Central.

### <a name="create-an-azure-blob-storage-destination"></a>Utwórz miejsce docelowe Blob Storage platformy Azure

Jeśli nie masz istniejącego konta usługi Azure Storage do eksportowania, wykonaj następujące czynności:

1. Utwórz [nowe konto magazynu w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Możesz dowiedzieć się więcej na temat tworzenia nowych [kont usługi Azure Blob Storage](https://aka.ms/blobdocscreatestorageaccount) lub [kont magazynu Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md). Eksport danych umożliwia zapisanie danych tylko na kontach magazynu, które obsługują blokowe obiekty blob. Na poniższej liście przedstawiono znane zgodne typy kont magazynu:

    |Warstwa wydajności|Typ konta|
    |-|-|
    |Standardowa (Standard)|Ogólnego przeznaczenia v2|
    |Standardowa (Standard)|Ogólnego przeznaczenia v1|
    |Standardowa (Standard)|Blob Storage|
    |Premium|Blokuj Magazyn obiektów BLOB|

2. Utwórz kontener na koncie magazynu. Przejdź do swojego konta magazynu. W obszarze **BLOB Service**wybierz pozycję **Przeglądaj obiekty blob**. Wybierz pozycję **+ kontener** u góry, aby utworzyć nowy kontener.

3. Wygeneruj parametry połączenia dla konta magazynu, przechodząc do **ustawień/kluczy dostępu**. Skopiuj jeden z dwóch parametrów połączenia.

### <a name="create-a-webhook-endpoint"></a>Tworzenie punktu końcowego elementu webhook
Można udostępnić publicznie dostępny punkt końcowy HTTP dla danych do wyeksportowania. Możesz utworzyć punkt końcowy elementu webhook testowego za pomocą RequestBin. Należy pamiętać, że RequestBin ma limit żądań ustawiony, zanim żądania zostaną ograniczone.

1. Otwórz [RequestBin](https://requestbin.net/).
2. Utwórz nowy RequestBin i skopiuj adres URL bin.

## <a name="set-up-data-export"></a>Konfigurowanie eksportu danych

Teraz, gdy masz miejsce docelowe eksportu danych do programu, wykonaj następujące kroki, aby skonfigurować eksportowanie danych.

1. Zaloguj się do aplikacji IoT Central.

2. W lewym okienku wybierz pozycję **eksport danych**.

    > [!Tip]
    > Jeśli nie widzisz **eksportu danych** w okienku po lewej stronie, nie masz uprawnień do konfigurowania eksportu danych w aplikacji. Skontaktuj się z administratorem, aby skonfigurować eksportowanie danych.

3. Wybierz przycisk **+ Nowy eksport** . 

4. Wprowadź nazwę wyświetlaną dla nowego eksportu i upewnij się, że **włączony** przełącznik jest włączony dla danych do przepływu.

## <a name="1-choose-the-type-of-data-to-export"></a>1. Wybierz typ danych do wyeksportowania.
Możesz wybrać między ciągłymi eksportowaniem różnych typów danych. Oto obsługiwane typy danych:

| Typ danych | Opis | Format danych |
| :------------- | :---------- | :----------- |
|  Telemetria | Eksportowanie komunikatów telemetrycznych z urządzeń w czasie niemal rzeczywistym. Każdy wyeksportowany komunikat będzie zawierać pełną zawartość oryginalnego komunikatu urządzenia, znormalizowany.   |  [Format wiadomości telemetrycznych](#telemetry-format)   |
| Zmiany właściwości | Eksportuj zmiany do właściwości urządzenia i chmury w czasie niemal rzeczywistym. W przypadku właściwości urządzenia tylko do odczytu zostaną wyeksportowane zmiany raportowanych wartości. Dla właściwości do odczytu i zapisu zostaną wyeksportowane zarówno raportowane, jak i żądane wartości. | [Format komunikatu zmiany właściwości](#property-changes-format) |

## <a name="2-optional-add-filters"></a>2. (opcjonalnie) Dodaj filtry
Dodaj filtry, aby zmniejszyć ilość danych wyeksportowanych na podstawie warunków filtrowania. Istnieją różne typy filtrów dostępne dla każdego typu danych do wyeksportowania.

### <a name="telemetry-filters"></a>Filtry telemetrii
  - **Filtr możliwości**: w przypadku wybrania na liście rozwijanej elementu telemetrii wyeksportowany strumień będzie zawierać tylko dane telemetryczne, które spełniają warunek filtru. Jeśli wybierzesz pozycję urządzenie lub właściwość chmury na liście rozwijanej, wyeksportowany strumień będzie zawierać tylko dane telemetryczne z urządzeń, które mają właściwości pasujące do warunku filtru.
  - **Filtr właściwości komunikatów**: urządzenia korzystające z zestawów SDK urządzeń mogą wysyłać *Właściwości komunikatów* lub *właściwości aplikacji* w każdym komunikacie telemetrii, który jest zbiorem par klucz-wartość zwykle używanych do oznaczania komunikatu przy użyciu identyfikatorów niestandardowych. Filtr właściwości komunikatu można utworzyć, wpisując w polu klucz właściwości komunikatu, którego szukasz, i określając warunek. Eksportowane są tylko komunikaty telemetryczne, które mają właściwości komunikatu zgodne z określonym warunkiem filtru. Obsługiwane są tylko Operatory porównywania ciągów (równa się, nie równa się, zawiera, nie zawiera, EXISTS, nie istnieje). [Dowiedz się więcej o właściwościach aplikacji IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md)dokumentach.

### <a name="property-changes-filters"></a>Filtry zmian właściwości
**Filtr właściwości**: Wybierz element właściwości na liście rozwijanej, a wyeksportowany strumień będzie zawierać tylko zmiany wybranej właściwości spełniającej warunek filtru.

## <a name="3-optional-add-enrichments"></a>3. (opcjonalnie) Dodaj wzbogacenia
Dodaj wzbogacanie, aby wzbogacać wyeksportowane wiadomości z dodatkowymi metadanymi w parach klucz-wartość. Są to dostępne wzbogacenia typów danych telemetrii i zmiany właściwości:
  - **Ciąg niestandardowy**: dodaje niestandardowy ciąg statyczny do każdego komunikatu. Wprowadź dowolny klucz i wprowadź dowolną wartość ciągu.
  - **Właściwość**: dodaje bieżącą właściwość lub wartość właściwości Cloud urządzenia do każdego komunikatu. Wprowadź dowolny klucz, a następnie wybierz urządzenie lub właściwość chmury. Jeśli wyeksportowany komunikat pochodzi z urządzenia, które nie ma określonego urządzenia lub właściwości chmury, wyeksportowany komunikat nie będzie miał tego wzbogacania.

## <a name="4-add-destinations"></a>4. Dodaj miejsca docelowe
Utwórz nowe miejsce docelowe lub Dodaj już utworzone miejsce docelowe. 
  
1. Kliknij link **Utwórz nowe miejsce docelowe** . Wprowadź następujące informacje:
    - **Nazwa miejsca docelowego**: Nazwa wyświetlana miejsca docelowego w IoT Central
    - **Typ docelowy**: Wybierz typ lokalizacji docelowej. Jeśli jeszcze tego nie zrobiono, należy [skonfigurować miejsce docelowe eksportowania](#set-up-export-destination)
    - W przypadku platformy Azure Event Hubs, kolejki Azure Service Bus lub tematu wklej parametry połączenia dla zasobu. 
    - W przypadku usługi Azure Blob Storage wklej parametry połączenia dla zasobu i wprowadź nazwę kontenera (z uwzględnieniem wielkości liter).
    - W przypadku elementu webhook Wklej adres URL wywołania zwrotnego dla punktu końcowego elementu webhook. 
    - Kliknij pozycję **Utwórz**

2. Kliknij pozycję **+ miejsce docelowe** i wybierz lokalizację docelową z listy rozwijanej. Do jednego eksportu można dodać maksymalnie 5 miejsc docelowych.

3. Po zakończeniu konfigurowania eksportu kliknij przycisk **Zapisz**. Po kilku minutach dane będą widoczne w Twoich miejscach docelowych.

## <a name="export-contents-and-format"></a>Eksportuj zawartość i format

W przypadku Event Hubs i Service Bus miejsc docelowych dane są eksportowane niemal w czasie rzeczywistym. Dane są w treści wiadomości i są w formacie JSON zakodowanym jako UTF-8. Przykłady znajdują się poniżej.

W przypadku usługi BLOB Storage dane są eksportowane raz na minutę, przy czym każdy plik zawierający partię zmian od ostatniego wyeksportowanego pliku. Eksportowane dane są umieszczane w trzech folderach w formacie JSON. Domyślne ścieżki na koncie magazynu:

- Dane telemetryczne: _{Container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Zmiany właściwości: _{Container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Aby przeglądać wyeksportowane pliki w Azure Portal, przejdź do pliku i wybierz kartę **Edytuj obiekt BLOB** .

W przypadku miejsc docelowych elementów webhook dane są również eksportowane w czasie niemal rzeczywistym. Dane są w formacie treści wiadomości w taki sam sposób, jak w przypadku Event Hubs i Service Bus.


## <a name="telemetry-format"></a>Format telemetrii
Każdy wyeksportowany komunikat zawiera znormalizowaną postać pełnej wiadomości wysyłanej przez urządzenie w treści wiadomości w formacie JSON zakodowanego jako UTF-8. Dodatkowe informacje zawarte w poszczególnych komunikatach obejmują:

- `applicationId`aplikacji IoT Central
- `messageSource`który jest *Telemetria* do eksportowania danych telemetrycznych
- `deviceId`urządzenia, które wysłało komunikat telemetrii
- `schema`jest nazwą i wersją schematu ładunku
- `templateId`czy identyfikator szablonu urządzenia jest skojarzony z urządzeniem
- `enrichments`Czy wszystkie wzbogacania, które zostały skonfigurowane podczas eksportowania
- `messageProperties`to dodatkowe fragmenty danych wysyłane przez urządzenie wraz z wiadomością. Jest to również znane jako *właściwości aplikacji*, [Dowiedz się więcej z dokumentacji IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

W przypadku Event Hubs i Service Bus IoT Central wyeksportować nowy komunikat szybko po odebraniu komunikatu z urządzenia.

W przypadku usługi BLOB Storage komunikaty są przetwarzane wsadowo i eksportowane raz na minutę.

Poniższy przykład pokazuje wyeksportowany komunikat telemetrii:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@preview",
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

## <a name="property-changes-format"></a>Format zmian właściwości

Każdy komunikat lub rekord przedstawia jedną zmianę w właściwości urządzenia lub chmury. W przypadku właściwości urządzenia tylko zmiany w raportowanej wartości są eksportowane jako osobny komunikat. Dodatkowe informacje zawarte w wyeksportowanym komunikacie obejmują:

- `applicationId`aplikacji IoT Central
- `messageSource`który jest *właściwościami* do eksportowania danych zmian właściwości
- `messageType`jest to *cloudPropertyChange* lub *devicePropertyDesiredChange*, *devicePropertyReportedChange*
- `deviceId`urządzenia, którego właściwości zostały zmienione
- `schema`jest nazwą i wersją schematu ładunku
- `templateId`czy identyfikator szablonu urządzenia jest skojarzony z urządzeniem
- `enrichments`Czy wszystkie wzbogacania, które zostały skonfigurowane podczas eksportowania

W przypadku Event Hubs i Service Bus IoT Central eksportuje dane nowych komunikatów do centrum zdarzeń lub kolejki Service Bus lub tematu niemal w czasie rzeczywistym.

W przypadku usługi BLOB Storage komunikaty są przetwarzane wsadowo i eksportowane raz na minutę.

W poniższym przykładzie przedstawiono eksportowany komunikat zmiany właściwości otrzymany na platformie Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@preview",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "fieldName": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-new-data-export"></a>Porównanie starszych wersji eksportu i eksportu danych
Jest to tabela, która wyróżnia różnice między starym eksportem danych a nowym eksportowaniem danych. W [tym miejscu](howto-export-data.md)możesz dowiedzieć się więcej na temat eksportu danych ze starszej wersji.

| Możliwości  | Eksport starszych danych | Nowy eksport danych |
| :------------- | :---------- | :----------- |
| Dostępne typy danych | Dane telemetryczne, urządzenia, szablony urządzeń | Dane telemetryczne, zmiany właściwości |
| Filtrowanie | Brak | Zależy od typu eksportowanych danych. W przypadku telemetrii filtrowanie według telemetrii, właściwości komunikatów i wartości właściwości |
| Wzbogaceń | Brak | Wzbogacanie z niestandardowym ciągiem lub wartością właściwości na urządzeniu |
| Miejsca docelowe | Azure Event Hubs, kolejki Azure Service Bus i tematy, Blob Storage platformy Azure | Analogicznie jak w przypadku starszego eksportu danych i elementów webhook| 
| Istotne limity | 5 eksportów na aplikację, 1 miejsce docelowe na eksport | 10 eksportów — połączenia docelowe na aplikację | 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać nowego eksportu danych, przejdź do następnego kroku:

> [!div class="nextstepaction"]
> [Jak używać analiz w IoT Central](./howto-create-analytics.md)
