---
title: Duże przelokalizowanie
description: Dowiedz się więcej o korzystaniu ze zbyt dużej lokalizacji, aby znaleźć kotwice w sąsiedztwie.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a65b2ca4ba9f1912adeaf60df123bcd3c8833bd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496906"
---
# <a name="coarse-relocalization"></a>Zgrubna ponowna lokalizacja

Bardzo duże relokalizacja to funkcja, która zapewnia wstępną odpowiedź na pytanie: *gdzie jest teraz moje urządzenie/jakie treści należy zaobserwować?* Odpowiedź nie jest precyzyjna, ale zamiast tego jest w postaci: znajdują się w *pobliżu tych kotwic; Spróbuj zlokalizować jeden z nich*.

Bardzo duże przeszukiwanie działa przez skojarzenie różnych odczytów czujnika na urządzeniu z tworzeniem i wykonywaniem zapytań o kotwice. W przypadku scenariuszy z zewnątrz dane czujnika są zwykle pozycjami GPS (GPS) urządzenia. Gdy GPS jest niedostępny lub niezawodny (na przykład w przypadku braku drzwi), dane czujnika składają się z punktów dostępu Wi-Fi i sygnałów nawigacyjnych Bluetooth w zakresie. Wszystkie zebrane dane czujnika przyczyniają się do utrzymania indeksu przestrzennego, który jest używany przez kotwice przestrzenne platformy Azure, aby szybko określić kotwice mieszczące się w około 100 metrów na urządzeniu.

Szybkie wyszukiwanie kotwic z obsługą ogromnej relokalizacji upraszcza tworzenie aplikacji, które są obsługiwane przez światowe kolekcje z założeniami (np. milionów elementów rozproszonych geograficznie). Złożoność zarządzania zakotwiczeniam jest ukryta, co pozwala skupić się na większej ostrości w logice aplikacji. Wszystkie duże zakotwiczenie są wykonywane w tle przez kotwice przestrzenne platformy Azure.

## <a name="collected-sensor-data"></a>Zebrane dane czujnika

Dane czujnika, które można wysłać do usługi zakotwiczenia, to jeden z następujących:

* Pozycja GPS: Szerokość geograficzna, Długość geograficzna, Wysokość.
* Siła sygnału punktów dostępu Wi-Fi w zakresie.
* Siła sygnału sygnałów nawigacyjnych Bluetooth w zakresie.

Ogólnie rzecz biorąc aplikacja będzie musiała uzyskać uprawnienia specyficzne dla urządzenia, aby uzyskać dostęp do danych GPS, Wi-Fi lub z tabeli. Ponadto niektóre z powyższych danych czujników nie są dostępne przez projektowanie na niektórych platformach. Aby uwzględnić te sytuacje, kolekcja danych czujników jest opcjonalna i jest domyślnie wyłączona.

## <a name="set-up-the-sensor-data-collection"></a>Konfigurowanie zbierania danych z czujnika

Zacznijmy od utworzenia dostawcy odcisku palca czujnika i przeprowadzenia dla niego informacji o tej sesji:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Następnie należy zdecydować, które czujniki mają być używane w przypadku niedużej lokalizacji. Ta decyzja jest specyficzna dla opracowywanej aplikacji, ale zalecenia w poniższej tabeli powinny zapewnić dobry punkt początkowy:

|                 | Brak drzwi | Na zewnątrz |
|-----------------|---------|----------|
| **GPS**         | Wyłączone | Włączone |
| **Karta**        | Włączone | On (opcjonalnie) |
| **Sygnały nawigacyjne dotyczące beli** | On (opcjonalnie z zastrzeżeniami, zobacz poniżej) | Wyłączone |

### <a name="enabling-gps"></a>Włączanie GPS

Przy założeniu, że aplikacja ma już uprawnienia dostępu do pozycji GPS urządzenia, można skonfigurować kotwice przestrzenne platformy Azure, aby z niej korzystać:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

W przypadku korzystania z GPS w aplikacji należy pamiętać, że odczyty udostępniane przez sprzęt są zwykle następujące:

* częstotliwość asynchroniczna i niska (mniej niż 1 Hz).
* niezawodne/zakłócenia (średnio 7-m odchylenie standardowe).

Ogólnie rzecz biorąc, zarówno system operacyjny, jak i kotwice przestrzenne platformy Azure będą wykonywać pewne filtrowanie i ekstrapolację na nieprzetworzonym sygnale GPS, próbując wyeliminować te problemy. To dodatkowe przetwarzanie wymaga dodatkowego czasu na zbieżność, dlatego w celu uzyskania najlepszych wyników należy spróbować:

* Utwórz dostawcę odcisku palca czujnika jako wczesny, jak to możliwe w aplikacji
* utrzymywanie dostawcy odcisków palca czujnika między wieloma sesjami
* Udostępnianie dostawcy odcisków palca czujnika między wieloma sesjami

Jeśli zamierzasz używać dostawcy odcisków palca czujnika poza sesją zakotwiczenia, przed zażądaniem oszacowania czujnika upewnij się, że został on uruchomiony. Na przykład poniższy kod zajmie się aktualizacją położenia urządzenia na mapie w czasie rzeczywistym:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Włączanie sieci Wi-Fi

Przy założeniu, że aplikacja ma już uprawnienia dostępu do stanu sieci Wi-Fi urządzenia, można skonfigurować kotwice przestrzenne platformy Azure do korzystania z niej:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

W przypadku korzystania z sieci Wi-Fi w aplikacji należy pamiętać, że odczyty udostępniane przez sprzęt są zwykle następujące:

* częstotliwość asynchroniczna i niska (mniej niż 0,1 Hz).
* możliwe ograniczenie na poziomie systemu operacyjnego.
* niezawodne/zakłócenia (średnio 3-dBm odchylenie standardowe).

Kotwice przestrzenne platformy Azure podejmują próbę skompilowania odfiltrowanej mapy siły sygnału sieci Wi-Fi podczas sesji w celu rozwiązania tych problemów. Aby uzyskać najlepsze wyniki, należy spróbować:

* przed wprowadzeniem pierwszej kotwicy utwórz sesję.
* Utrzymuj sesję aktywności tak długo, jak to możliwe (to oznacza, że wszystkie kotwice i zapytania są tworzone w jednej sesji).

### <a name="enabling-bluetooth-beacons"></a>Włączanie sygnałów Bluetooth

Przy założeniu, że aplikacja ma już uprawnienia dostępu do stanu Bluetooth urządzenia, można skonfigurować kotwice przestrzenne platformy Azure do korzystania z niej:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Sygnały nawigacyjne są zazwyczaj uniwersalnymi urządzeniami, gdzie wszystko — w tym Identyfikatory UUID i adresy MAC — można skonfigurować. Ta elastyczność może być przyczyną problemów z zakotwiczeniami przestrzennymi platformy Azure, ponieważ uważają sygnały, które są jednoznacznie identyfikowane przez ich identyfikatory UUID. Niepowodzenie, aby upewnić się, że unikatowość będzie prawdopodobnie powodowało Wormholes przestrzenne. Aby uzyskać najlepsze wyniki, należy:

* Przypisywanie unikatowych identyfikatorów UUID do sygnałów nawigacyjnych.
* Wdrażaj je — zwykle w zwykłych wzorcach, takich jak siatka.
* Przekaż listę unikatowych identyfikatorów UUID sygnałów do dostawcy odcisków palca czujnika:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Kotwice przestrzenne platformy Azure śledzą sygnały nawigacyjne Bluetooth, które znajdują się w znanej liście identyfikatorów UUID sąsiedztwa. Złośliwe sygnały są programowane w taki sposób, aby na liście identyfikatorów UUID mogły mieć negatywny wpływ na jakość usługi. Z tego powodu należy używać sygnałów nawigacyjnych tylko w nadzorowanych miejscach, w których można kontrolować ich wdrażanie.

## <a name="querying-with-sensor-data"></a>Wykonywanie zapytań przy użyciu danych czujników

Po utworzeniu kotwic ze skojarzonymi danymi czujnika można rozpocząć pobieranie ich przy użyciu odczytów czujnika zgłoszonych przez urządzenie. Nie musisz już podawać usługi za pomocą listy znanych kotwic, które zamierzasz znaleźć — zamiast tego wystarczy, aby usługa wiedzieli lokalizację urządzenia jako zgłoszoną przez czujniki dołączania. Dzięki zakotwiczeniem przestrzennym platformy Azure zostanie przedstawiony zestaw kotwic blisko urządzenia i podjęto próbę wizualnego dopasowania go.

Aby zapytania używały danych z czujnika, Zacznij od utworzenia kryterium "blisko urządzenia":

# <a name="c"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

`DistanceInMeters`Parametr określa, jak daleko analizujemy Graf zakotwiczony do pobierania zawartości. Przyjęto założenie, że na przykład wypełniono miejsce z kotwicami o stałej gęstości wynoszącej 2 każdy licznik. Dodatkowo aparat na urządzeniu obsłużył jedną kotwicę, a usługa została pomyślnie umieszczona w usłudze. Najprawdopodobniej interesuje Cię pobieranie wszystkich kotwic, które zostały umieszczone w pobliżu, a nie z pojedynczej kotwicy, która jest aktualnie zaobserwowania. Przy założeniu, że kotwice zostały połączone w grafie, usługa może pobrać wszystkie kotwice w pobliżu, postępując zgodnie z krawędziami na wykresie. Liczba wykonanych operacji przechodzenia wykresu jest kontrolowana przez `DistanceInMeters` program; otrzymasz wszystkie kotwice połączone z tym, które znajdują się na tym samym miejscu niż `DistanceInMeters` .

Należy pamiętać, że duże wartości dla `MaxResultCount` mogą negatywnie wpłynąć na wydajność. Ustaw ją na wartość rozsądną dla aplikacji.

Na koniec należy poinformować sesję, aby używała wyszukiwania opartego na czujnikach:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Oczekiwane wyniki

Urządzenia GPS klasy konsumenckiej są zwykle całkiem nieprecyzyjne. Badania przez [Zandenbergen i Barbeau (2011)][6] umożliwiają raportowanie średniej dokładności telefonów komórkowych z obsługą GPS (a-GPS) do około 7 metrów — dość dużej wartości do zignorowania. Aby uwzględnić te błędy pomiarów, usługa traktuje kotwice jako rozkład prawdopodobieństwa w przestrzeni GPS. W związku z tym zakotwiczenie jest teraz regionem obszaru, który najprawdopodobniej (czyli z ponad 95% pewnością) zawiera jego prawdziwe, nieznane położenie GPS.

Te same przyczyny są stosowane podczas wykonywania zapytań przy użyciu GPS. Urządzenie jest reprezentowane jako inny region zaufania przestrzennego wokół jego prawdziwej, nieznanej pozycji GPS. Odnajdowanie kotwic w pobliżu jest tłumaczone na po prostu znalezienie kotwic z regionem zaufania *wystarczająco blisko* obszaru pewności urządzenia, jak pokazano na poniższej ilustracji:

![Wybór kandydatów zakotwiczenia przy użyciu GPS](media/coarse-reloc-gps-separation-distance.png)

Dokładność sygnału GPS, zarówno przy tworzeniu kotwic, jak i podczas zapytań, ma duży wpływ na zestaw zwracanych kotwic. W przeciwieństwie do zapytań opartych na sieci Wi-Fi/nadajnikach wszystkie kotwice, które mają co najmniej jeden punkt dostępu/sygnalizator wspólnych w zapytaniu. W tym sensie wynik zapytania opartego na sieci Wi-Fi/sygnałów jest głównie określony przez fizyczny zakres punktów dostępu/sygnałów nawigacyjnych i przeszkód środowiskowych.

W poniższej tabeli szacuje oczekiwany obszar wyszukiwania dla każdego typu czujnika:

| Czujnik      | Promień obszaru wyszukiwania (w przybliżeniu) | Szczegóły |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | Określana na podstawie niepewności GPS między innymi czynnikami. Raportowane liczby są szacowane dla średniej dokładności GPS telefonów komórkowych z-GPS, czyli 7 metrów. |
| Sieć Wi-Fi        | 50 m – 100 m | Określany przez zakres punktów dostępu bezprzewodowego. Zależy od częstotliwości, siły nadajnika, przeszkód fizycznych, zakłóceń i tak dalej. |
| Sygnały nawigacyjne dotyczące beli |  70 m | Określany przez zakres sygnałów nawigacyjnych. Zależy od częstotliwości, siły transmisji, przeszkód fizycznych, zakłóceń i tak dalej. |

## <a name="per-platform-support"></a>Obsługa poszczególnych platform

Poniższa tabela zawiera podsumowanie danych czujników zbieranych na każdej z obsługiwanych platform wraz z wszelkimi zastrzeżeniami specyficznymi dla platformy:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | Nie dotyczy | Obsługiwane za poorednictwem interfejsów API [lokalizacji][3] (GPS i Network) | Obsługiwane za poorednictwem interfejsów API [CLLocationManager][4] |
| **Karta**        | Obsługiwane z szybkością około jednego skanowania co 3 sekundy | Obsługiwane. Począwszy od poziomu interfejsu API 28, skanowania w sieci Wi-Fi są ograniczone do 4 wywołań co 2 minuty. W przypadku systemu Android 10 ograniczenie przepustowości można wyłączyć w menu Ustawienia dewelopera. Aby uzyskać więcej informacji, zobacz [dokumentację systemu Android][5]. | N/A — brak publicznego interfejsu API |
| **Sygnały nawigacyjne dotyczące beli** | Ograniczone do [Eddystone][1] i [iBeacon][2] | Ograniczone do [Eddystone][1] i [iBeacon][2] | Ograniczone do [Eddystone][1] i [iBeacon][2] |

## <a name="next-steps"></a>Następne kroki

Użyj bardzo dużej lokalizacji w aplikacji.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
