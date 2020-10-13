---
title: Integracja z partnerami obsługującymi dane o pogodzie
description: W tym artykule opisano, jak dostawca danych pogody można zintegrować z usługą FarmBeats
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: a2677b5343b2d65a39e7c9f6d5006db599c1ac73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86496999"
---
# <a name="weather-partner-integration"></a>Integracja z partnerami obsługującymi dane o pogodzie

Ten artykuł zawiera informacje na temat składnika Docker **łącznika** usługi Azure FarmBeats, który dostawcy danych pogody mogą opracowywać, aby zintegrować się z usługą FarmBeats, wykorzystując swoje interfejsy API i wysyłając dane pogody do FarmBeats. Gdy dane będą dostępne w FarmBeats, można je wykorzystać do łączenia danych i tworzenia modeli uczenia maszynowego/sztucznej analizy.

 > [!NOTE]
 > Na potrzeby tej dokumentacji będziemy używać implementacji referencyjnej skompilowanej przy użyciu NOAA z platformy Azure Otwórz zestawy danych i jest dostępna pod adresem [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) .
 > Odpowiedni obraz platformy Docker jest dostępny pod adresem [https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

Partner pogody musi dostarczyć obraz/program platformy Docker (ze specyfikacjami wymienionymi poniżej) i hostować obraz platformy Docker w rejestrze kontenerów, który jest dostępny dla klientów. Partner pogody będzie musiał udostępnić klientom następujące informacje:

- Adres URL obrazu platformy Docker
- Tag obrazu platformy Docker
- Klucze/poświadczenia umożliwiające dostęp do obrazu platformy Docker
- Klucze/poświadczenia interfejsu API specyficzne dla klienta umożliwiające dostęp do danych z systemu partnera pogody
- Szczegóły jednostki SKU maszyny wirtualnej (partnerzy mogą ją udostępnić w przypadku, gdy platforma Docker ma określone wymagania dotyczące maszyn wirtualnych, w przeciwnym razie klienci mogą wybrać spośród obsługiwanych jednostek SKU maszyn wirtualnych na platformie Azure)

Korzystając z podanych powyżej informacji platformy Docker, klient będzie rejestrował partnera pogody w ich wystąpieniach usługi FarmBeats. Aby dowiedzieć się więcej o tym, jak klienci mogą używać platformy Docker do pozyskiwania danych pogodowych w FarmBeats, zobacz przewodnik, aby [uzyskać dane pogodowe](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner)

## <a name="connector-docker-development"></a>Programowanie platformy Docker

**Integracja oparta na interfejsie API REST**

Interfejsy API FarmBeats zawierają dokumentację techniczną struktury Swagger. Aby uzyskać informacje na temat wszystkich interfejsów API i odpowiadających im żądań lub odpowiedzi, zobacz [FarmBeats Swagger](https://aka.ms/farmbeatsswagger). 

Jeśli zainstalowano FarmBeats, możesz uzyskać dostęp do programu FarmBeats Swagger w `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Należy zauważyć, że parametr "-API" jest dołączany do nazwy witryny sieci Web FarmBeats.
Punkt końcowy interfejsu API będzie: `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Biblioteka Datahub

FarmBeats dostarczy biblioteki lib, która może być używana przez partnera pogody. Biblioteka lib jest obecnie dostępna w ramach implementacji odwołania w [tym miejscu](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). W przyszłości taka sama będzie dostępna jako zestaw SDK dla wielu języków.

### <a name="authentication"></a>Uwierzytelnianie

**Uwierzytelnianie za pomocą interfejsów API FarmBeats**

FarmBeats używa uwierzytelniania okaziciela i dostęp do interfejsów API można uzyskać, podając token dostępu w sekcji nagłówka żądania w następujący sposób:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Token dostępu można zażądać od funkcji platformy Azure uruchomionej w wystąpieniu FarmBeats klienta. Adres URL funkcji platformy Azure zostanie dostarczony do programu Docker jako argument, a token dostępu można uzyskać, wysyłając żądanie GET w adresie URL. Odpowiedź z adresu URL będzie zawierać token dostępu. Biblioteka Datahub zawiera funkcje pomocnika umożliwiające partnerom uzyskanie tokenu dostępu. Więcej informacji znajdziesz [tutaj](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Token dostępu jest prawidłowy tylko przez kilka godzin i wymaga ponownego zażądania po jego wygaśnięciu.

**Uwierzytelnianie za pomocą interfejsów API po stronie partnera**

Aby umożliwić klientom uwierzytelnianie przy użyciu interfejsów API po stronie partnera podczas wykonywania platformy Docker, klienci muszą podać poświadczenia podczas rejestracji partnera w następujący sposób:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Usługa API serializować ten DICT i zapisuje je w [magazynie](https://docs.microsoft.com/azure/key-vault/basic-concepts)kluczy.

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) służy do organizowania zadań pogodowych i zwiększania zasobów w celu wykonania kodu platformy Docker. Zapewnia również mechanizm bezpiecznego wypychania danych do maszyny wirtualnej, w której wykonywane jest zadanie platformy Docker. Poświadczenia interfejsu API, obecnie bezpiecznie przechowywane w magazynie kluczy są odczytywane jako bezpieczne ciągi z magazynu kluczy i udostępniane jako właściwości rozszerzone w katalogu roboczym kontenera Docker jako activity.js(ścieżka do pliku to "/mnt/working_dir/activity.json") kod platformy Docker może odczytywać poświadczenia z tego pliku w czasie wykonywania, aby uzyskać dostęp do interfejsów API po stronie partnera w imieniu klienta. Poświadczenia będą dostępne w pliku w następujący sposób:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Należy zauważyć, że "partnerCredentials" będzie dostępny w sposób dokładny, który został dostarczony przez klienta podczas rejestracji partnera

Biblioteka FarmBeats zawiera funkcje pomocnika umożliwiające partnerom odczytywanie poświadczeń z właściwości działania. Więcej informacji znajdziesz [tutaj](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

Okres istnienia pliku jest tylko podczas wykonywania kodu platformy Docker i zostanie usunięty po zakończeniu przebiegu platformy Docker.

Aby uzyskać więcej informacji na temat działania potoków i działań usługi ADF, zobacz [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) .

**Nagłówki żądań HTTP**

Poniżej znajdują się najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do FarmBeats.

**Nagłówki** | **Opis i przykład**
--- | ---
Content-Type | Format żądania (Content-Type: Application/ <format> ). W przypadku interfejsów API FarmBeats Datahub format jest JSON. Content-Type: Application/JSON
Autoryzacja | Określa token dostępu wymagany do wywołania interfejsu API. Autoryzacja: <okaziciela Access-Token>
Zaakceptuj | Format odpowiedzi. W przypadku interfejsów API FarmBeats Datahub format jest JSON. Akceptuj: Application/JSON

## <a name="data-format"></a>Format danych

JSON to typowy format danych niezależny od języka, który zapewnia prostą reprezentację dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz [JSON.org](http://json.org).

## <a name="docker-specifications"></a>Specyfikacje platformy Docker

Program Docker musi mieć dwa składniki: **Bootstrap** i **Jobs**. Może istnieć więcej niż jedno zadanie.

### <a name="bootstrap"></a>Uruchomienie

Ten składnik powinien zostać wykonany po zainicjowaniu przez klienta rejestracji platformy Docker w systemie FarmBeats. Argumenty (arg1, arg2), które zostaną przekazane do tego programu, są następujące:

- Punkt końcowy interfejsu API FarmBeats: FarmBeats punktu końcowego interfejsu API dla żądań interfejsu API: jest to punkt końcowy do tworzenia wywołań interfejsu API do wdrożenia FarmBeats.
- Adres URL funkcji platformy Azure: jest to Twój własny punkt końcowy, który zapewni token dostępu dla interfejsów API FarmBeats. Po prostu wywołanie metody GET na tym adresie URL spowoduje pobranie tokenu dostępu w odpowiedzi.

Przy użyciu programu Bootstrap można utworzyć metadane wymagane, aby umożliwić użytkownikom uruchamianie zadań w celu uzyskania danych o pogodzie. Zapoznaj się z implementacją referencyjną [tutaj](https://github.com/azurefarmbeats/noaa_docker). bootstrap_manifest.jsw pliku można zaktualizować zgodnie z potrzebami użytkownika, a program ładowania referencyjnego utworzy wymagane metadane.

Następujące metadane są tworzone w ramach tego procesu. 

 > [!NOTE]
 > **Należy pamiętać** , że w przypadku aktualizacji bootstrap_manifest.jsw pliku, jak wspomniano w [implementacji referencyjnej](https://github.com/azurefarmbeats/noaa_docker), nie trzeba tworzyć poniższych metadanych, ponieważ Bootstrap zostanie utworzony w oparciu o plik manifestu.

- /**WeatherDataModel**: WeatherDataModel jest modelem służącym do reprezentowania danych pogody i odnosi się do różnych zestawów danych dostarczanych przez źródło. Na przykład DailyForecastSimpleModel może zapewnić średnią temperaturę, wilgotność i informacje o opadach raz dziennie, a DailyForecastAdvancedModel może dostarczyć znacznie więcej informacji na temat godzinowej szczegółowości. Można utworzyć dowolną liczbę WeatherDataModels.
- /**Typu zadania**: FarmBeats ma rozszerzalny system zarządzania zadaniami. Jako dostawca danych pogody będziesz mieć różne zestawy/interfejsy API (na przykład GetDailyForecasts) — możesz je włączyć w FarmBeats jako typu zadania. Po utworzeniu typu zadania klient może wyzwolić zadania tego typu, aby uzyskać dane pogodowe dla ich lokalizacji/farmy (zobacz typu zadania i interfejsy API zadań w [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)).

### <a name="jobs"></a>Stanowiska

Ten składnik będzie wywoływany za każdym razem, gdy użytkownik FarmBeats uruchamia zadanie/JobType, które zostało utworzone w ramach procesu ładowania początkowego. Polecenie Docker Run dla zadania jest zdefiniowane jako część utworzonego elementu **/JobType** .
- Odpowiedzialność za zadanie będzie pobierać dane ze źródła i wypchnąć je do FarmBeats. Parametry wymagane do pobrania danych powinny być zdefiniowane jako część/JobType w procesie ładowania początkowego.
- W ramach zadania program będzie musiał utworzyć **/WeatherDataLocation** na podstawie/WeatherDataModel, który został utworzony w ramach procesu ładowania początkowego. **/WeatherDataLocation** odnosi się do lokalizacji (lat/Long), która jest dostarczana przez użytkownika jako parametr do zadania.

### <a name="details-of-the-objects"></a>Szczegóły obiektów

  WeatherDataModel | Opis |
  --- | ---
  Nazwa  | Nazwa modelu danych pogody |
  Opis  | Podaj znaczący opis modelu. |
  Właściwości  | Dodatkowe właściwości zdefiniowane przez dostawcę danych. |
  Nazwa > weatherMeasures  | Nazwa miary pogodowej. Na przykład humidity_max |
  weatherMeasures > DataType  | Podwójna lub wyliczeniowa. Jeśli enum, measureEnumDefinition jest wymagany |
  weatherMeasures > measureEnumDefinition  | Wymagane tylko wtedy, gdy typem danych jest enum. Na przykład {"nodeszcz": 0, "śnieg": 1, "Drizzle": 2, "deszcz": 3} |
  Typ > weatherMeasures  | Typ danych telemetrii pogody. Na przykład "RelativeHumidity". Poniżej znajdują się typy zdefiniowane przez system: AmbientTemperature, nounit, CO2, Głębokość, ElectricalConductivity, LeafWetness, Length, LiquidLevel, azotan, O2, PH, fosforan, PointInTime, potas, ciśnienie, RainGauge, RelativeHumidity, sole, SoilMoisture, SoilTemperature, SolarRadiation, TimeDuration, PAR. Aby dodać więcej, zapoznaj się z interfejsem API/ExtendedType lub w [sekcji Dodawanie typów i jednostek](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) poniżej.
  Jednostka > weatherMeasures | Jednostka danych telemetrycznych o pogodzie. Poniżej przedstawiono jednostki zdefiniowane przez system: nounit, Celsjusza, Fahrenheita, Kelvin, Rankine, Pascal, rtęć, PSI, milimetry, centymetr, metr, centymetr, stopy, kilometry, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMol, MicroMolesPerLiter,, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter i Centibar. Aby dodać więcej, zapoznaj się z interfejsem API/ExtendedType lub w [sekcji Dodawanie typów i jednostek](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) poniżej.
  weatherMeasures > agregacji  | Brak, średnia, maksimum, minimum, StandardDeviation, sum, suma
  Głębokość > weatherMeasures  | Głębokość czujnika w centymetrach. Na przykład pomiar wilgoci 10 cm pod ziemią.
  weatherMeasures > opis  | Podaj znaczący opis miary. |
  **Typu zadania** | **Opis** |
  Nazwa  | Nazwa zadania — na przykład Get_Daily_Forecast; zadanie, które zostanie uruchomione przez klienta w celu uzyskania danych pogody|
  pipelineDetails > parametry > nazwa  | Nazwa parametru |
  pipelineDetails > parametrów > typu | jeden z ciągów, int, float, bool, Array |
  pipelineDetails > parametry > IsRequired | typu ma wartość true, jeśli parametr Required ma wartość false; wartość domyślna to true |
  pipelineDetails > parametry > DefaultValue | Wartość domyślna parametru |
  pipelineDetails > parametrów > opis | Opis parametru |
  Właściwości  | Dodatkowe właściwości producenta.
  Właściwości > **programRunCommand** | polecenie Docker Run — to polecenie zostanie wykonane, gdy klient uruchomi zadanie pogodowe. |
  **WeatherDataLocation** | **Opis** |
  weatherDataModelId  | Identyfikator odpowiadającego WeatherDataModel, który został utworzony podczas ładowania początkowego|
  location  | reprezentuje szerokość geograficzną, długość geograficzną i podniesienia |
  Nazwa | Nazwa obiektu |
  Opis | Opis |
  farmId | **opcjonalne** Identyfikator farmy udostępnianej przez klienta w ramach parametru zadania |
  Właściwości  | Dodatkowe właściwości producenta.

 Aby uzyskać informacje na temat poszczególnych obiektów i ich właściwości, zobacz [Swagger](https://aka.ms/FarmBeatsSwagger).

 > [!NOTE]
 > Interfejsy API zwracają unikatowe identyfikatory dla każdego utworzonego wystąpienia. Ten identyfikator musi być zachowywany przez translator w celu zarządzania urządzeniami i synchronizowania metadanych.

**Synchronizacja metadanych**

Platforma Docker powinna mieć możliwość wysyłania aktualizacji metadanych. Przykłady scenariuszy aktualizacji to: dodanie nowych parametrów pogodowych do zestawu danych dostawcy pogody, dodanie funkcji (np. Dodanie prognozy 30 dni)

> [!NOTE]
> Usuwanie nie jest obsługiwane dla metadanych. Model danych pogody.
>
> Aby zaktualizować metadane, należy wywołać/Get/{ID} w modelu danych pogody, zaktualizować zmienione właściwości, a następnie wykonać/Put/{ID}, tak aby wszystkie właściwości ustawione przez użytkownika nie zostały utracone.

## <a name="weather-data-telemetry-specifications"></a>Specyfikacje danych pogody (Telemetria)

Dane o pogodzie są mapowane na komunikat kanoniczny, który jest wypychany do centrum zdarzeń platformy Azure w celu przetworzenia. Azure EventHub to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji. Aby wysłać dane pogody do FarmBeats, należy utworzyć klienta wysyłającego komunikaty do centrum zdarzeń w FarmBeats. Aby dowiedzieć się więcej o wysyłaniu danych telemetrycznych, zobacz [wysyłanie danych telemetrycznych do centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)

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
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Na przykład Oto komunikat telemetrii:

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

**Rejestrowanie błędów**

Ponieważ zadanie partnerskie zostanie uruchomione w istniejącej platformie zadań — błędy są rejestrowane w taki sam sposób jak błędy innych wstępnie istniejących zadań w FarmBeats (na przykład GetFarmData, SensorPlacement itp.). Działanie usługi ADF uruchomione w ramach potoku APD rejestruje zarówno STDERR, jak i STDOUT. Oba pliki są dostępne na koncie magazynu "datahublogs-XXX" w grupie zasobów FarmBeats.

Biblioteka Datahub zawiera funkcje pomocnika, które umożliwiają rejestrowanie w ramach ogólnych dzienników Datahub. Więcej informacji znajdziesz [tutaj](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

**Opcja rozwiązywania problemów lub pomoc techniczna**

W przypadku, gdy klient nie może odbierać danych pogody w określonym wystąpieniu FarmBeats, partner pogody powinien zapewnić pomoc techniczną i mechanizm rozwiązywania problemów.

## <a name="add-extendedtype"></a>Dodaj Rozszerzonytype

FarmBeats obsługuje dodawanie nowych typów i jednostek miary czujnika. Należy pamiętać, że partner pogody może dodawać nowe jednostki/typy przez aktualizację bootstrap_manifest.jsw pliku w implementacji odwołania [tutaj](https://github.com/azurefarmbeats/noaa_docker)

Aby dodać nowy typ WeatherMeasure, na przykład "PrecipitationDepth", wykonaj poniższe kroki.

1. Utwórz żądanie GET na/ExtendedType za pomocą zapytania filter-Key = WeatherMeasureType
2. Zanotuj identyfikator zwróconego obiektu.
3. Dodaj nowy typ do listy w zwracanym obiekcie i wprowadź żądanie PUT na/ExtendedType{ID} z następującą nową listą. Ładunek wejściowy powinien być taki sam jak odebrana powyżej odpowiedź i nowa jednostka dołączona na końcu listy wartości.

Aby uzyskać więcej informacji na temat interfejsu API/ExtendedType, zobacz [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Następne kroki

Teraz masz łącznik platformy Docker, który integruje się z usługą FarmBeats. Następnie możesz zobaczyć, jak uzyskać dane pogodowe przy użyciu platformy Docker do FarmBeats. Zobacz [pobieranie danych pogody](get-weather-data-from-weather-partner.md).
