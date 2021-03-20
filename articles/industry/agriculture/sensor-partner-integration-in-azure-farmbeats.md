---
title: Integracja z partnerami obsługującymi czujniki
description: W tym artykule opisano integrację partnerów czujników.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 8d1b8203fa50609daf59431c2cfecba68eba52b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179752"
---
# <a name="sensor-partner-integration"></a>Integracja z partnerami obsługującymi czujniki

Ten artykuł zawiera informacje na temat składnika usługi Azure FarmBeats **translator** , który umożliwia integrację z partnerem czujnika.

Korzystając z tego składnika, partnerzy mogą zintegrować się z usługą FarmBeats przy użyciu interfejsów API FarmBeats Datahub i wysyłać dane urządzenia i telemetrię do FarmBeats Datahub. Gdy dane są dostępne w FarmBeats, są wizualizacje przy użyciu akceleratora FarmBeats i mogą być używane do łączenia danych oraz do tworzenia modeli uczenia maszynowego/sztucznej analizy.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby można było opracowywać składnik usługi translator, potrzebne są następujące poświadczenia, które umożliwią dostęp do interfejsów API FarmBeats.

- Punkt końcowy interfejsu API
- Identyfikator dzierżawy
- Identyfikator klienta
- Klucz tajny klienta
- Parametry połączenia EventHub

Zapoznaj się z tą sekcją w celu uzyskania powyższych poświadczeń: [Włącz integrację urządzeń](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Programowanie w usłudze translator

**Integracja oparta na interfejsie API REST**

Możliwości integracji danych z czujnika FarmBeats są udostępniane za pośrednictwem interfejsu API REST. Możliwości obejmują definicje metadanych, urządzenia i aprowizacji czujników oraz zarządzanie urządzeniami i czujnikami.

**Pozyskiwanie danych telemetrycznych**

Dane telemetryczne są mapowane na komunikat kanoniczny, który jest publikowany w usłudze Azure Event Hubs do przetwarzania. Azure Event Hubs to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji.

**Programowanie interfejsu API**

Interfejsy API zawierają dokumentację techniczną struktury Swagger. Aby uzyskać więcej informacji na temat interfejsów API i odpowiadających im żądań lub odpowiedzi, zobacz [Swagger](https://aka.ms/FarmBeatsSwagger).

**Authentication**

FarmBeats używa uwierzytelniania Microsoft Azure Active Directory.Azure App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory](../../app-service/overview-authentication-authorization.md).

FarmBeats Datahub używa uwierzytelniania okaziciela, które wymaga następujących poświadczeń:
   - Identyfikator klienta
   - Klucz tajny klienta
   - Identyfikator dzierżawy

Przy użyciu tych poświadczeń wywołujący może zażądać tokenu dostępu. Token musi zostać wysłany w kolejnych żądaniach interfejsu API, w sekcji nagłówka, w następujący sposób:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Następujący przykładowy kod w języku Python daje token dostępu, który może być używany do kolejnych wywołań interfejsu API do FarmBeats.

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```


**Nagłówki żądań HTTP**

Poniżej znajdują się najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do FarmBeats Datahub.


**Nagłówek** | **Opis i przykład**
--- | ---
Content-Type | Format żądania (Content-Type: Application/ <format> ). W przypadku interfejsów API FarmBeats Datahub format jest JSON. Content-Type: Application/JSON
Autoryzacja | Określa token dostępu wymagany do wywołania interfejsu API. Autoryzacja: <okaziciela Access-Token>
Zaakceptuj | Format odpowiedzi. W przypadku interfejsów API FarmBeats Datahub format jest JSON. Akceptuj: Application/JSON

**Żądania interfejsu API**

Aby wykonać żądanie interfejsu API REST, należy połączyć metodę HTTP (GET, POST lub PUT), adres URL usługi interfejsu API, Uniform Resource Identifier (URI) do zasobu, aby wykonać zapytanie, przesłać dane do, zaktualizować lub usunąć oraz co najmniej jeden nagłówek żądania HTTP. Adres URL usługi API Service to punkt końcowy interfejsu API, który jest udostępniany. Oto przykład: https:// \<yourdatahub-website-name> . azurewebsites.NET

Opcjonalnie można uwzględnić parametry zapytania dla wywołań GET do filtrowania, ograniczyć rozmiar i sortować dane w odpowiedziach.

Poniższe przykładowe żądanie polega na wyświetleniu listy urządzeń.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Poniższe przykładowe żądanie polega na utworzeniu urządzenia. (Ten przykład zawiera wejściowy kod JSON z treścią żądania).

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Format danych

JSON to typowy format danych niezależny od języka, który zapewnia prostą reprezentację dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Specyfikacje metadanych

FarmBeats Datahub zawiera następujące interfejsy API, które umożliwiają partnerom urządzeń tworzenie metadanych urządzenia lub czujnika oraz zarządzanie nimi.

- /**DeviceModel**: DeviceModel odpowiada metadanych urządzenia, takich jak producent i typ urządzenia, który jest bramą lub węzłem.
- /**Urządzenie**: urządzenie odpowiada urządzeniu fizycznemu znajdującemu się w farmie.
- /**SensorModel**: SensorModel odpowiada metadanych czujnika, takich jak producent, typ czujnika, który jest analogowy lub cyfrowy i pomiar czujnika, taki jak temperatura otoczenia i ciśnienie.
- /**Czujnik**: czujnik odnosi się do czujnika fizycznego, który rejestruje wartości. Czujnik jest zwykle podłączony do urządzenia z IDENTYFIKATORem urządzenia.

  DeviceModel | Opis |
  --- | ---
  Typ (węzeł, brama)  | Typ węzła urządzenia lub bramy |
  Producent  | Nazwa producenta |
  ProductCode  | Kod produktu urządzenia lub nazwa modelu lub numer. Na przykład EnviroMonitor # 6800. |
  Porty  | Nazwa i typ portu, które są cyfrowe lub analogowe.  |
  Nazwa  | Nazwa identyfikująca zasób. Na przykład nazwa modelu lub nazwa produktu. |
  Opis  | Podaj znaczący opis modelu. |
  Właściwości  | Dodatkowe właściwości producenta. |
  **Urządzenie** | **Opis** |
  DeviceModelId  |Identyfikator skojarzonego modelu urządzenia. |
  HardwareId   |Unikatowy identyfikator urządzenia, na przykład adres MAC.  |
  ReportingInterval |Interwał raportowania (w sekundach). |
  Lokalizacja    |Urządzenia Latitude (-90 do + 90), długości geograficznej (-180 do 180) i podniesienia uprawnień (w metrach). |
  ParentDeviceId | Identyfikator urządzenia nadrzędnego, z którym jest połączone to urządzenie. Na przykład jeśli węzeł jest połączony z bramą, węzeł ma parentDeviceID jako bramę. |
  Nazwa  | Nazwa identyfikująca zasób. Partnerzy urządzeń muszą wysłać nazwę zgodną z nazwą urządzenia po stronie partnera urządzeń. Jeśli nazwa urządzenia jest zdefiniowana przez użytkownika po stronie partnera urządzeń, ta sama nazwa zdefiniowana przez użytkownika powinna być propagowana do FarmBeats.  |
  Opis  | Podaj znaczący opis.  |
  Właściwości  |Dodatkowe właściwości producenta.  |
  **SensorModel** | **Opis** |
  Typ (analogowy, cyfrowy)  |Wzmianka o czujniku analogowym lub cyfrowym.|
  Producent  | Nazwa producenta. |
  ProductCode  | Kod produktu lub nazwa modelu lub numer. Na przykład RS-CO2-N01.  |
  Nazwa > SensorMeasures  | Nazwa miary czujnika. Obsługiwane są tylko małe litery. W przypadku pomiarów z różnych głębokości należy określić głębokość. Na przykład soil_moisture_15cm. Ta nazwa musi być spójna z danymi telemetrycznymi. |
  SensorMeasures > DataType  | Typ danych telemetrii. Obecnie jest obsługiwana Podwójna precyzja. |
  Typ > SensorMeasures  | Typ pomiaru danych telemetrii czujnika. Poniżej przedstawiono typy zdefiniowane przez system: AmbientTemperature, CO2, Głębokość, ElectricalConductivity, LeafWetness, długość, LiquidLevel, azotan, O2, PH, fosforan, PointInTime, potas, ciśnienie, RainGauge, RelativeHumidity, zasolenie, SoilMoisture, SoilTemperature, SolarRadiation, TimeDuration, PAR. Aby dodać więcej, zapoznaj się z interfejsem API/ExtendedType.
  Jednostka > SensorMeasures | Jednostka danych telemetrii czujnika. Poniżej przedstawiono jednostki zdefiniowane przez system: nounit, Celsjusza, Fahrenheita, Kelvin, Rankine, Pascal, rtęć, PSI, milimetry, centymetr, metr, centymetr, stopy, kilometry, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMol, MicroMolesPerLiter,, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter i Centibar. Aby dodać więcej, zapoznaj się z interfejsem API/ExtendedType.
  SensorMeasures > agregacji  | None, Average, maksimum, minimum lub StandardDeviation.
  Głębokość > SensorMeasures  | Głębokość czujnika w centymetrach. Na przykład pomiar wilgoci 10 cm pod ziemią.
  SensorMeasures > opis  | Podaj znaczący opis miary.
  Nazwa  | Nazwa identyfikująca zasób. Na przykład nazwa modelu lub nazwa produktu.
  Opis  | Podaj znaczący opis modelu.
  Właściwości  | Dodatkowe właściwości producenta.
  **Czujnik**  | **Opis** |
  HardwareId  | Unikatowy identyfikator czujnika określonego przez producenta.
  SensorModelId  | Identyfikator skojarzonego modelu czujnika.
  Lokalizacja  | Czujnik Latitude (-90 do + 90), Długość geograficzna (-180 do 180) i podniesienie (w metrach).
  Nazwa > portu  |Nazwa i typ portu, z którym jest połączony czujnik na urządzeniu. Ta nazwa musi być taka sama jak zdefiniowana w modelu urządzenia.
  DeviceId  | Identyfikator urządzenia, z którym jest połączony czujnik.
  Nazwa  | Nazwa identyfikująca zasób. Na przykład nazwa czujnika lub nazwa produktu oraz numer modelu lub kod produktu.
  Opis  | Podaj znaczący opis.
  Właściwości  | Dodatkowe właściwości producenta.

 Aby uzyskać informacje na temat poszczególnych obiektów i ich właściwości, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Interfejsy API zwracają unikatowe identyfikatory dla każdego utworzonego wystąpienia. Ten identyfikator musi być zachowywany przez translator w celu zarządzania urządzeniami i synchronizowania metadanych.


**Synchronizacja metadanych**

Translator powinien wysyłać aktualizacje metadanych. Na przykład scenariusze aktualizacji to zmiany nazwy urządzenia lub czujnika oraz zmiany lokalizacji urządzenia lub czujnika.

Translator powinien mieć możliwość dodawania nowych urządzeń lub czujników, które zostały zainstalowane przez użytkownika po utworzeniu linku FarmBeats. Podobnie, jeśli urządzenie lub czujnik został zaktualizowany przez użytkownika, ta sama powinna zostać zaktualizowana w FarmBeats dla odpowiedniego urządzenia lub czujnika. Typowe scenariusze, które wymagają zaktualizowania urządzenia lub czujnika, są zmianami w lokalizacji urządzenia lub dodaniem czujników w węźle.


> [!NOTE]
> Usuwanie nie jest obsługiwane dla metadanych urządzenia lub czujnika.
>
> Aby zaktualizować metadane, należy wywołać/Get/{ID} na urządzeniu lub czujniku, zaktualizować zmienione właściwości, a następnie wykonać/Put/{ID}, tak aby wszystkie właściwości ustawione przez użytkownika nie zostały utracone.

### <a name="add-new-types-and-units"></a>Dodawanie nowych typów i jednostek

FarmBeats obsługuje dodawanie nowych typów i jednostek miary czujnika. Aby uzyskać więcej informacji na temat interfejsu API/ExtendedType, zobacz [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Specyfikacje telemetrii

Dane telemetryczne są mapowane na komunikat kanoniczny, który jest publikowany w usłudze Azure Event Hubs do przetwarzania. Azure Event Hubs to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji.

## <a name="send-telemetry-data-to-farmbeats"></a>Wyślij dane telemetryczne do FarmBeats

Aby wysłać dane telemetryczne do FarmBeats, należy utworzyć klienta wysyłającego komunikaty do centrum zdarzeń w FarmBeats. Aby uzyskać więcej informacji na temat danych telemetrycznych, zobacz [wysyłanie telemetrii do centrum zdarzeń](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Oto przykładowy kod w języku Python, który wysyła dane telemetryczne jako klienta do określonego centrum zdarzeń.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Format komunikatu kanonicznego jest następujący:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
Wszystkie nazwy kluczy w formacie JSON danych telemetrycznych powinny być pisane małymi literami. Przykłady to DeviceID i sensordata.

Na przykład Oto komunikat telemetrii:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

> [!NOTE]
> Poniższe sekcje są związane z innymi zmianami (np. Interfejs użytkownika, zarządzanie błędami itp.) Ten partner czujnika może odwoływać się do programu podczas tworzenia składnika usługi Translator.


## <a name="link-a-farmbeats-account"></a>Łączenie konta FarmBeats

Gdy klienci zakupili i wdrożono urządzenia lub czujniki, mogą uzyskać dostęp do danych i telemetrii urządzeń w portalu "oprogramowanie jako usługa" (SaaS). Partnerzy urządzeń mogą umożliwić klientom łączenie ich kont z wystąpieniem FarmBeats na platformie Azure, zapewniając sposób wprowadzania następujących poświadczeń:

   - Nazwa wyświetlana (opcjonalne pole dla użytkowników, aby zdefiniować nazwę dla tej integracji)
   - Punkt końcowy interfejsu API
   - Identyfikator dzierżawy
   - Identyfikator klienta
   - Klucz tajny klienta
   - Parametry połączenia EventHub
   - Data rozpoczęcia

   > [!NOTE]
   > Data rozpoczęcia włącza strumieniowe źródło danych, czyli dane z daty określonej przez użytkownika.

## <a name="unlink-farmbeats"></a>Odłącz FarmBeats

Partnerzy urządzeń mogą umożliwić klientom odłączenie istniejącej integracji FarmBeats. Odłączanie FarmBeats nie powinno usunąć żadnych metadanych urządzenia ani czujnika, które zostały utworzone w FarmBeats Datahub. Odłączanie wykonuje następujące czynności:

   - Powoduje zatrzymanie przepływu telemetrii.
   - Usuwa i wymazuje poświadczenia integracji w partnerze urządzeń.

## <a name="edit-farmbeats-integration"></a>Edytuj integrację FarmBeats

Partnerzy urządzeń mogą umożliwić klientom Edytowanie ustawień integracji FarmBeats, jeśli klucz tajny klienta lub parametry połączenia są zmieniane. W takim przypadku można edytować tylko następujące pola:

   - Nazwa wyświetlana (jeśli dotyczy)
   - Wpis tajny klienta (powinien być wyświetlany w formacie "2X8 * * * * * * * * *
   - Parametry połączenia (powinny być wyświetlane w postaci "2X8 * * * * * * * * * * *," format lub Pokaż/Ukryj funkcję, a nie zwykły tekst)

## <a name="view-the-last-telemetry-sent"></a>Wyświetl ostatnie wysłane dane telemetryczne

Partnerzy urządzeń mogą umożliwić klientom wyświetlanie sygnatury czasowej ostatniej wysyłanej telemetrii, która została znaleziona w obszarze **wysłane dane telemetryczne**. Jest to czas, w którym Najnowsza Telemetria została pomyślnie wysłana do FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Rozwiązywanie problemów i zarządzanie błędami

**Opcja rozwiązywania problemów lub pomoc techniczna**

Jeśli klient nie może odebrać danych urządzenia lub telemetrii w określonym wystąpieniu usługi FarmBeats, partner urządzenia powinien zapewnić pomoc techniczną i mechanizm rozwiązywania problemów.

**Przechowywanie danych telemetrii**

Dane telemetryczne powinny być również przechowywane przez wstępnie zdefiniowany okres, dzięki czemu mogą być przydatne w debugowaniu lub ponownym wysyłaniu danych telemetrycznych w przypadku wystąpienia błędu lub utraty dane.

**Zarządzanie błędami lub powiadamianie o błędach**

Jeśli wystąpi błąd dotyczący metadanych urządzenia lub czujnika lub przepływu danych danych telemetrycznych w systemie partnerskim urządzenia, klient powinien odebrać powiadomienie. Mechanizm rozwiązywania wszelkich błędów powinien również zostać zaprojektowany i wdrożony.

**Lista kontrolna połączenia**

Producenci urządzeń lub partnerzy mogą użyć poniższej listy kontrolnej, aby upewnić się, że poświadczenia podane przez klienta są dokładne:

   - Sprawdź, czy token dostępu został odebrany przy użyciu podanych poświadczeń.
   - Sprawdź, czy wywołanie interfejsu API powiedzie się z tokenem dostępu, który został odebrany.
   - Sprawdź, czy nawiązano połączenie z klientem EventHub.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu API REST, zobacz [interfejs API REST](rest-api-in-azure-farmbeats.md).