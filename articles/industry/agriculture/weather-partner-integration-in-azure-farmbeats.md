---
title: Integracja z partnerami obsługującymi dane o pogodzie
description: Dowiedz się, w jaki sposób dostawca danych pogody można zintegrować z usługą FarmBeats.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93147083"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Integracja z partnerem pogody z usługą FarmBeats

Ten artykuł zawiera informacje na temat składnika Docker łącznika usługi Azure FarmBeats. Jako dostawca danych pogody możesz użyć platformy Docker, aby zintegrować z usługą FarmBeats. Użyj swoich interfejsów API, aby wysyłać dane pogody do FarmBeats. W programie FarmBeats dane mogą służyć do łączenia danych i tworzenia modeli uczenia maszynowego lub sztucznych modeli analizy.

 > [!NOTE]
 > W tym artykule używamy [implementacji referencyjnej](https://github.com/azurefarmbeats/noaa_docker) , która została skompilowana przy użyciu otwartych zestawów danych platformy Azure i dane pogodowe z krajowych oceanicznych i NOAA. Używamy również odpowiedniego [obrazu platformy Docker](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa).

Musisz podać [odpowiedni obraz platformy Docker lub program](#docker-specifications) i hostować obraz platformy Docker w rejestrze kontenera, do którego klienci mogą uzyskać dostęp. Podaj następujące informacje dla klientów:

- Adres URL obrazu platformy Docker
- Tag obrazu platformy Docker
- Klucze lub poświadczenia umożliwiające dostęp do obrazu platformy Docker
- Klucze interfejsu API specyficzne dla klienta lub poświadczenia umożliwiające dostęp do danych z systemu
- Szczegóły jednostki SKU maszyny wirtualnej (podaj te szczegóły, jeśli obraz platformy Docker ma określone wymagania dotyczące maszyny wirtualnej). W przeciwnym razie klienci mogą wybrać spośród obsługiwanych jednostek SKU maszyn wirtualnych na platformie Azure.

Klienci używają tych informacji platformy Docker do rejestrowania partnera pogody w ich wystąpieniu FarmBeats. Aby uzyskać więcej informacji na temat sposobu, w jaki klienci mogą używać platformy Docker do pozyskiwania danych pogody w FarmBeats, zobacz [pobieranie danych od partnerów pogody](./get-weather-data-from-weather-partner.md).

## <a name="connector-docker-development"></a>Programowanie platformy Docker

**Integracja oparta na interfejsie API REST**

Interfejsy API FarmBeats zawierają dokumentację techniczną struktury Swagger. Aby uzyskać więcej informacji na temat interfejsów API i odpowiadających im żądań lub odpowiedzi, zobacz [FarmBeats Swagger](https://aka.ms/farmbeatsswagger). 

Jeśli już zainstalowano FarmBeats, uzyskaj dostęp do programu FarmBeats Swagger w `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Zwróć uwagę, że *interfejs API* jest dołączany do nazwy witryny sieci Web FarmBeats. Punkt końcowy interfejsu API to `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Biblioteka Datahub

FarmBeats zawiera bibliotekę, której można użyć. Biblioteka lib jest obecnie dostępna jako [część implementacji odwołania](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). Później będzie dostępny jako zestaw SDK dla wielu języków.

### <a name="authentication"></a>Authentication

**Uwierzytelnianie za pomocą interfejsów API FarmBeats**

FarmBeats używa uwierzytelniania okaziciela. Możesz uzyskać dostęp do interfejsów API, podając token dostępu w sekcji nagłówka żądania. Oto przykład:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Możesz zażądać tokenu dostępu z aplikacji Azure Functions działającej w wystąpieniu FarmBeats klienta. Adres URL Azure Functions jest dostarczany do programu Docker jako argument. Token dostępu można uzyskać, wysyłając `GET` żądanie na adres URL. Odpowiedź z adresu URL zawiera token dostępu. 

Użyj funkcji pomocnika w bibliotece Datahub, aby uzyskać token dostępu. Aby uzyskać więcej informacji, zobacz [stronę usługi GitHub dla obrazu platformy Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Token dostępu jest prawidłowy tylko dla kilku godzin. Po jego wygaśnięciu należy ponownie zażądać tego żądania.

**Uwierzytelnianie za pomocą interfejsów API po stronie partnerskiej**

Aby uwierzytelniać się przy użyciu interfejsów API po stronie partnera podczas działania zadania platformy Docker, klienci muszą podać poświadczenia podczas rejestracji partnera. Oto przykład:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Usługa API serializować ten DICT i zapisuje je w [magazynie kluczy](../../key-vault/general/basic-concepts.md).

[Azure Data Factory](../../data-factory/introduction.md) służy do organizowania zadań pogodowych. Powoduje to przekazanie zasobów w celu uruchomienia kodu platformy Docker. Data Factory udostępnia również mechanizm do bezpiecznego wypychania danych do maszyny wirtualnej, na której działa zadanie platformy Docker. Poświadczenia interfejsu API są następnie bezpiecznie przechowywane w magazynie kluczy. 

Poświadczenia są odczytywane jako bezpieczne ciągi z magazynu kluczy. Są one dostarczane jako właściwości rozszerzone w katalogu roboczym kontenera Docker. Ścieżka pliku jest */mnt/working_dir/activity.jswłączona*. 

Kod platformy Docker może odczytać poświadczenia z *activity.jsw* czasie wykonywania w celu uzyskania dostępu do interfejsów API po stronie partnera dla klienta. W pliku JSON poświadczenia wyglądają podobnie jak w tym przykładzie kodu:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
`partnerCredentials`Poświadczenie jest dostępne w sposób, w jaki klient udostępnił go podczas rejestracji partnera.

Biblioteka FarmBeats zawiera funkcje pomocnika. Te funkcje służą do odczytywania poświadczeń z właściwości działania. Aby uzyskać więcej informacji, zobacz [stronę usługi GitHub dla obrazu platformy Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

Plik jest używany tylko wtedy, gdy działa kod platformy Docker. Po zakończeniu działania kodu plik zostanie usunięty.

Aby uzyskać więcej informacji o tym, jak działają potoki i działania Data Factory, zobacz [Mapowanie schematu i typu danych](../../data-factory/copy-activity-schema-and-type-mapping.md).

**Nagłówki żądań HTTP**

W poniższej tabeli przedstawiono najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do FarmBeats.

Nagłówek | Opis i przykład
--- | ---
Content-Type | Format żądania. Przykład: `Content-Type: application/<format>` <br/>W przypadku interfejsów API FarmBeats Datahub format jest JSON. Przykład: ` Content-Type: application/json`
Autoryzacja | Token dostępu wymagany do wywołania interfejsu API. Przykład: `Authorization: Bearer <Access-Token>`
Zaakceptuj | Format odpowiedzi. W przypadku interfejsów API FarmBeats Datahub format jest JSON. Przykład: `Accept: application/json`

## <a name="data-format"></a>Format danych

JSON to typowy format danych niezależny od języka, który zapewnia prostą reprezentację dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz [JSON.org](https://json.org).

## <a name="docker-specifications"></a>Specyfikacje platformy Docker

Program platformy Docker wymaga dwóch składników: ładowania początkowego i zadania. Program może mieć więcej niż jedno zadanie.

### <a name="bootstrap"></a>Uruchomienie

Składnik ładowania początkowego powinien działać, gdy klient uruchamia rejestrację platformy Docker w systemie FarmBeats. Następujące argumenty ( `arg1` i `arg2` ) są przekazane do programu:

- **Punkt końcowy interfejsu API FarmBeats**: punkt końcowy interfejsu API FarmBeats dla żądań interfejsu API. Ten punkt końcowy wykonuje wywołania interfejsu API do wdrożenia FarmBeats.
- **Adres URL Azure Functions**: własny punkt końcowy. Ten adres URL zawiera token dostępu dla interfejsów API FarmBeats. Możesz wywołać `GET` ten adres URL, aby pobrać token dostępu.

Ten Bootstrap tworzy metadane, których użytkownicy potrzebują do uruchamiania zadań, aby uzyskać dane pogodowe. Aby uzyskać więcej informacji, zobacz [implementację referencyjną](https://github.com/azurefarmbeats/noaa_docker). 

W przypadku dostosowania *bootstrap_manifest.jsw* pliku, program ładowania referencyjnego utworzy wymagane metadane. Program ładowania początkowego tworzy następujące metadane: 

 > [!NOTE]
 > W przypadku aktualizacji *bootstrap_manifest.jsw* pliku zgodnie z opisem [implementacji referencyjnej](https://github.com/azurefarmbeats/noaa_docker) nie trzeba tworzyć następujących metadanych. Program ładowania początkowego użyje pliku manifestu do utworzenia niezbędnych metadanych.

- /**WeatherDataModel**: metadane WeatherDataModel reprezentują dane o pogodzie. Odnosi się do zestawów danych dostarczanych przez źródło. Na przykład DailyForecastSimpleModel może zapewnić średnią temperaturę, wilgotność i informacje o opadach raz dziennie. Z kolei DailyForecastAdvancedModel może zapewnić znacznie więcej informacji na poziomie szczegółowości godzinowej. Można utworzyć dowolną liczbę modeli danych pogody.
- /**Typu zadania**: FarmBeats ma rozszerzalny system zarządzania zadaniami. Jako dostawca danych pogody będziesz mieć różne zbiory i interfejsy API (na przykład GetDailyForecasts). Te zestawy danych i interfejsy API można włączyć w programie FarmBeats przy użyciu typu zadania. Po utworzeniu typu zadania klient może wyzwolić zadania tego typu, aby uzyskać dane pogodowe dla ich lokalizacji lub ich udziału w swojej organizacji. Aby uzyskać więcej informacji, zobacz typu zadania i interfejsy API zadań w [FarmBeats Swagger](https://aka.ms/farmbeatsswagger).

### <a name="jobs"></a>Stanowiska

Składnik Jobs jest wywoływany za każdym razem, gdy użytkownik FarmBeats uruchamia zadanie/JobType, które zostało utworzone w ramach procesu ładowania początkowego. Polecenie Docker Run dla zadania jest zdefiniowane jako część utworzonego elementu/JobType.

Zadanie pobiera dane ze źródła i wypycha je do FarmBeats. W procesie ładowania początkowego parametry, które są wymagane do pobrania danych, powinny być zdefiniowane jako część/JobType.

W ramach zadania program musi utworzyć/WeatherDataLocation w oparciu o/WeatherDataModel, który został utworzony podczas procesu ładowania początkowego. /WeatherDataLocation odpowiada lokalizacji (współrzędnej szerokości geograficznej i długości geograficznej), który użytkownik podano jako parametr zadania.

### <a name="object-details"></a>Szczegóły obiektu

WeatherDataModel | Opis |
--- | ---
Nazwa  | Nazwa modelu danych pogody. |
Opis  | Zrozumiały opis modelu. |
Właściwości  | Dodatkowe właściwości zdefiniowane przez dostawcę danych. |
Nazwa > weatherMeasures  | Nazwa miary pogodowej. Na przykład humidity_max. |
weatherMeasures > DataType  | Podwójna lub wyliczeniowa. Jeśli enum, measureEnumDefinition jest wymagany. |
weatherMeasures > measureEnumDefinition  | Wymagane tylko wtedy, gdy typem danych jest enum. Na przykład `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
Typ > weatherMeasures  | Typ danych telemetrii pogody. Na przykład RelativeHumidity. Typy zdefiniowane przez system to AmbientTemperature, nounit, CO2, Głębokość, ElectricalConductivity, LeafWetness, Length, LiquidLevel, azotan, O2, PH, fosforan, PointInTime, potas, ciśnienie, RainGauge, RelativeHumidity, zasolenie, SoilMoisture, SoilTemperature, SolarRadiation, TimeDuration, UVRadiation i PAR. Aby dodać więcej typów, zobacz sekcję [Add ExtendedType](#add-extendedtype) w tym artykule.
Jednostka > weatherMeasures | Jednostka danych telemetrycznych o pogodzie. Jednostki zdefiniowane przez system to nounit, Celsjusza, Fahrenheita, Kelvin, Rankine, Pascal, rtęć, PSI, milimetry, centymetr, metr, centymetr, stopy, kilometry, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMole, MicroMolesPerLiter,, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter i Centibar. Aby dodać więcej jednostek, zapoznaj się z sekcją [Add ExtendedType](#add-extendedtype) w tym artykule.
weatherMeasures > agregacji  | Typ agregacji. Możliwe wartości to None, Average, Maximum, minimum, StandardDeviation, sum i Total.
Głębokość > weatherMeasures  | Głębokość czujnika w centymetrach. Na przykład pomiar wilgoci 10 cm pod ziemią.
weatherMeasures > opis  | Zrozumiały opis miary. 

Typu zadania | Opis |
--- | ---
Nazwa  | Nazwa zadania. Na przykład Get_Daily_Forecast. Klient uruchomi to zadanie w celu uzyskania danych o pogodzie.|
pipelineDetails > parametry > nazwa  | Nazwa parametru. |
pipelineDetails > parametrów > typu | Typ parametru. Możliwe wartości to String, int, float, bool i Array. |
pipelineDetails > parametry > IsRequired | Wartość logiczna parametru. Wartość jest równa true, jeśli parametr jest wymagany. W przeciwnym razie wartość jest równa false. Wartość domyślna to true. |
pipelineDetails > parametry > DefaultValue | Wartość domyślna parametru. |
pipelineDetails > parametrów > opis | Opis parametru. |
Właściwości  | Dodatkowe właściwości producenta.
Właściwości > programRunCommand | Polecenie Docker Run. To polecenie jest uruchamiane, gdy klient uruchamia zadanie pogodowe. |

WeatherDataLocation | Opis |
--- | ---
weatherDataModelId  | Identyfikator odpowiadającego WeatherDataModel, który został utworzony podczas procesu ładowania początkowego.|
location  | Szerokości geograficznej, długości geograficznej i podniesienia uprawnień. |
Nazwa | Nazwa obiektu. |
Opis | Opis lokalizacji danych pogody. |
farmId | Obowiązkowe Identyfikator farmy. Klient dostarcza ten identyfikator jako część parametru zadania. |
Właściwości  | Dodatkowe właściwości producenta.

Aby uzyskać więcej informacji o obiektach i ich właściwościach, zobacz [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger).

> [!NOTE]
> Interfejsy API zwracają unikatowe identyfikatory dla każdego tworzonego wystąpienia. Translator dla zarządzania urządzeniami i synchronizacji metadanych musi zachować ten identyfikator.

**Synchronizacja metadanych**

Składnik Docker łącznika powinien mieć możliwość wysyłania aktualizacji metadanych. Na przykład powinna wysyłać aktualizacje, gdy dostawca pogody dodaje nowe parametry do zestawu danych lub gdy zostanie dodana nowa funkcja, taka jak nowa 30-dniowa Prognoza.

> [!NOTE]
> Usuwanie nie jest obsługiwane dla metadanych w modelu danych pogody.
>
> Aby zaktualizować metadane, należy wywołać `/Get/{ID}` model danych pogody. Zaktualizuj zmienione właściwości, a następnie wykonaj, `/Put/{ID}` Aby zachować wszystkie właściwości ustawiane przez użytkownika.

## <a name="weather-data-telemetry-specifications"></a>Specyfikacje danych pogody (Telemetria)

Dane o pogodzie są mapowane na komunikat kanoniczny, który jest wypychany do centrum zdarzeń platformy Azure w celu przetworzenia. Azure Event Hubs to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji. 

Aby wysłać dane pogody do FarmBeats, należy utworzyć klienta wysyłającego komunikaty do centrum zdarzeń w FarmBeats. Aby uzyskać więcej informacji, zobacz [wysyłanie telemetrii do centrum zdarzeń](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Poniższy przykładowy kod w języku Python wysyła dane telemetryczne jako klienta do określonego centrum zdarzeń.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Oto format komunikatu kanonicznego:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Oto przykład komunikatu telemetrii:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Rozwiązywanie problemów i zarządzanie błędami

### <a name="error-logging"></a>Rejestrowanie błędów

Zadanie partnerskie działa w istniejącej platformie zadań. Błędy są rejestrowane w taki sam sposób jak błędy dla innych istniejących wcześniej zadań FarmBeats (na przykład GetFarmData i SensorPlacement). Działanie Data Factory działające w ramach dzienników potoku Data Factory zarówno `STDERR` , jak i `STDOUT` . Oba pliki są dostępne na `datahublogs-xxx` koncie magazynu w grupie zasobów FarmBeats.

Biblioteka Datahub zawiera funkcje pomocnika, które umożliwiają rejestrowanie w ramach ogólnych dzienników Datahub. Aby uzyskać więcej informacji, zobacz [stronę usługi GitHub dla obrazu platformy Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

### <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

Jeśli klient nie może odbierać danych pogody w wystąpieniu FarmBeats, należy zapewnić pomoc techniczną i mechanizm rozwiązywania problemu.

## <a name="add-extendedtype"></a>Dodaj Rozszerzonytype

FarmBeats obsługuje dodawanie nowych typów i jednostek miary czujnika. Możesz dodać nowe jednostki lub typy, aktualizując *bootstrap_manifest.js* pliku w [implementacji odwołania](https://github.com/azurefarmbeats/noaa_docker).

Wykonaj następujące kroki, aby dodać nowy typ WeatherMeasure, na przykład PrecipitationDepth.

1. Utwórz `GET` żądanie na/ExtendedType za pomocą zapytania `filter - key = WeatherMeasureType` .
2. Zanotuj identyfikator zwróconego obiektu.
3. Dodaj nowy typ do listy w zwracanym obiekcie. Utwórz `PUT` żądanie na/ExtendedType{ID} z następującą nową listą. Ładunek wejściowy powinien być taki sam jak odebrana wcześniej odpowiedź. Na końcu listy wartości należy dołączyć nową jednostkę.

Aby uzyskać więcej informacji na temat interfejsu API/ExtendedType, zobacz [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Następne kroki

Teraz masz składnik Docker łącznika, który integruje się z usługą FarmBeats. Następnie Dowiedz się, jak [uzyskać dane pogodowe](get-weather-data-from-weather-partner.md) przy użyciu obrazu platformy Docker w FarmBeats. 
