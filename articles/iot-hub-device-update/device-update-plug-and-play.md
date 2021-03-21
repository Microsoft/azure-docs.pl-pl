---
title: Opis sposobu, w jaki aktualizacja urządzenia dla IoT Hub używa usługi IoT Plug and Play | Microsoft Docs
description: Aktualizacja urządzenia dla IoT Hub służy do odnajdywania i zarządzania urządzeniami, które są w trybie failover.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 227488f165aaad2f204c647eed17467a4ef561a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101663404"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Aktualizacja urządzenia dla IoT Hub i IoT Plug and Play

Aktualizacja urządzenia dla IoT Hub korzysta z usługi [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp/) w celu odnajdywania i zarządzania urządzeniami, które są w trybie failover. Usługa aktualizacji urządzenia wyśle i odbierze właściwości i komunikaty do i z urządzeń za pomocą interfejsów PnP. Aktualizacja urządzenia dla IoT Hub wymaga, aby urządzenia IoT implementują następujące interfejsy i identyfikatory modelu zgodnie z poniższym opisem.

## <a name="adu-core-interface"></a>Interfejs ADU Core

Interfejs "ADUCoreInterface" służy do wysyłania akcji aktualizacji i metadanych do urządzeń i otrzymywania stanu aktualizacji z urządzeń. Interfejs "ADU Core" jest podzielony na dwie właściwości obiektu.

Oczekiwana nazwa składnika w modelu to **"azureDeviceUpdateAgent"** podczas implementowania tego interfejsu. [Dowiedz się więcej o składnikach PnP usługi Azure IoT](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

### <a name="agent-metadata"></a>Metadane agenta

Metadane agenta zawierają pola, które są używane przez agenta aktualizacji urządzenia lub urządzenia do wysyłania informacji i stanu do usług aktualizacji urządzeń.

|Nazwa|Schemat|Kierunek|Opis|Przykład|
|----|------|---------|-----------|-----------|
|resultCode|liczba całkowita|urządzenie do chmury|Kod, który zawiera informacje o wyniku ostatniej akcji aktualizacji. Można ją wypełnić dla sukcesu lub niepowodzenia i powinna być zgodna ze [specyfikacją kodu stanu HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extendedResultCode|liczba całkowita|urządzenie do chmury|Kod, który zawiera dodatkowe informacje o wyniku. Można ją wypełnić dla sukcesu lub niepowodzenia.|0x80004005|
|stan|liczba całkowita|urządzenie do chmury|Jest to liczba całkowita, która wskazuje bieżący stan agenta aktualizacji urządzenia. Szczegóły znajdują się poniżej. |Okresy|
|installedUpdateId|ciąg|urządzenie do chmury|Identyfikator aktualizacji, która jest aktualnie zainstalowana (za pomocą aktualizacji urządzenia). Ta wartość będzie równa null dla urządzenia, które nigdy nie zajęło aktualizacji za pomocą aktualizacji urządzenia.|Zero|
|`deviceProperties`|Mapa|urządzenie do chmury|Zestaw właściwości, które zawierają producenta i model.|Szczegóły znajdują się poniżej.

#### <a name="state"></a>Stan

Jest to stan raportowany przez agenta aktualizacji urządzenia po odebraniu akcji z usługi aktualizacji urządzenia. `State` jest raportowany w odpowiedzi na `Action` (patrz `Actions` poniżej) wysyłanej do agenta aktualizacji urządzenia z usługi aktualizacji urządzenia. Zapoznaj się z [przepływem pracy przegląd](understand-device-update.md#device-update-agent) dla żądań, które przepływają między usługą aktualizacji urządzenia a agentem aktualizacji urządzenia.

|Nazwa|Wartość|Opis|
|---------|-----|-----------|
|Okresy|0|Urządzenie jest gotowe do odebrania akcji z usługi aktualizacji urządzenia. Po pomyślnej aktualizacji stan jest zwracany do `Idle` stanu.|
|DownloadSucceeded|2|Pomyślne pobranie.|
|InstallSucceeded|4|Instalacja zakończona pomyślnie.|
|Niepowodzenie|255|Wystąpił błąd podczas aktualizowania.|

#### <a name="device-properties"></a>Właściwości urządzenia

Jest to zestaw właściwości, które zawierają producenta i model.

|Nazwa|Schemat|Kierunek|Opis|
|----|------|---------|-----------|
|manufacturer|ciąg|urządzenie do chmury|Producent urządzenia, który został zgłoszony przez `deviceProperties` . Ta właściwość jest odczytywana z jednego z dwóch miejsc — interfejs "AzureDeviceUpdateCore" najpierw podejmie próbę odczytania wartości "aduc_manufacturer" z pliku [konfiguracyjnego](device-update-configuration-file.md) .  Jeśli wartość nie jest wypełniona w pliku konfiguracji, domyślnie będzie raportowana definicja czasu kompilowania dla ADUC_DEVICEPROPERTIES_MANUFACTURER. Ta właściwość będzie raportowana tylko w czasie rozruchu.|
|model|ciąg|urządzenie do chmury|Model urządzenia urządzenia, raportowany za pomocą `deviceProperties` . Ta właściwość jest odczytywana z jednego z dwóch miejsc — interfejs AzureDeviceUpdateCore najpierw próbuje odczytać wartość "aduc_model" z pliku [konfiguracyjnego](device-update-configuration-file.md) .  Jeśli wartość nie jest wypełniona w pliku konfiguracji, domyślnie będzie raportowana definicja czasu kompilowania dla ADUC_DEVICEPROPERTIES_MODEL. Ta właściwość będzie raportowana tylko w czasie rozruchu.|
|aduVer|ciąg|urządzenie do chmury|Wersja agenta aktualizacji urządzenia uruchomionego na urządzeniu. Ta wartość jest odczytywana z kompilacji tylko wtedy, gdy w czasie kompilacji ENABLE_ADU_TELEMETRY_REPORTING jest ustawiona na 1 (true). Klienci mogą wybrać opcję rezygnacji z raportowania wersji, ustawiając wartość na 0 (false). [Jak dostosować właściwości agenta aktualizacji urządzenia](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|doVer|ciąg|urządzenie do chmury|Wersja agenta optymalizacji dostarczania działającego na urządzeniu. Wartość jest odczytywana z kompilacji tylko wtedy, gdy w czasie kompilacji ENABLE_ADU_TELEMETRY_REPORTING jest ustawiona na 1 (true). Klienci mogą wybrać opcję rezygnacji z raportowania wersji, ustawiając wartość na 0 (false). [Jak dostosować właściwości agenta optymalizacji dostarczania](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components).|

### <a name="service-metadata"></a>Metadane usługi

Metadane usługi zawierają pola, które są używane przez usługi aktualizacji urządzeń do przekazywania działań i danych do agenta aktualizacji urządzenia.

|Nazwa|Schemat|Kierunek|Opis|
|----|------|---------|-----------|
|akcja|liczba całkowita|chmura do urządzenia|Jest to liczba całkowita, która odnosi się do akcji, którą powinien wykonać Agent. Wartości wymienione poniżej.|
|UpdateManifest —|ciąg|chmura do urządzenia|Służy do opisywania zawartości aktualizacji. Wygenerowano na podstawie [manifestu importu](import-update.md#create-device-update-import-manifest)|
|updateManifestSignature|JSON, obiekt|chmura do urządzenia|Sygnatura sieci Web JSON (JWS) z kluczami sieci Web JSON użyta do weryfikacji źródła.|
|fileUrls|Mapa|chmura do urządzenia|Mapa elementu `FileHash` do `DownloadUri` . Informuje agenta, które pliki do pobrania oraz skrót do użycia w celu zweryfikowania, czy pliki zostały prawidłowo pobrane.|

#### <a name="action"></a>Akcja

`Actions` poniżej przedstawia akcje podejmowane przez agenta aktualizacji urządzeń zgodnie z instrukcją usługi aktualizacji urządzenia. Agent aktualizacji urządzenia wyświetli `State` (patrz `State` powyżej) przetwarzanie `Action` odebranych danych. Zapoznaj się z [przepływem pracy przegląd](understand-device-update.md#device-update-agent) dla żądań, które przepływają między usługą aktualizacji urządzenia a agentem aktualizacji urządzenia.

|Nazwa|Wartość|Opis|
|---------|-----|-----------|
|Pobierz|0|Pobierz opublikowaną zawartość lub aktualizację oraz inną wymaganą zawartość|
|Instalowanie|1|Zainstaluj zawartość lub aktualizację. Zazwyczaj oznacza to wywoływanie Instalatora dla zawartości lub aktualizacji.|
|Zastosuj|2|Zakończ aktualizację. Sygnalizuje systemowi ponowne uruchomienie w razie potrzeby.|
|Anuluj|255|Zatrzymaj przetwarzanie bieżącej akcji i wróć do `Idle` . Zostanie również użyta w celu poinformowania agenta w `Failed` stanie o powrocie do programu `Idle` .|

## <a name="device-information-interface"></a>Interfejs informacji o urządzeniu

Interfejs informacji o urządzeniu to koncepcja wykorzystywana w ramach [architektury Plug and Play IoT](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play). Zawiera ona urządzenia do właściwości chmury, które zawierają informacje o sprzęcie i systemie operacyjnym urządzenia. Aktualizacja urządzenia dla IoT Hub używa właściwości DeviceInformation. Manufacturer i DeviceInformation. model na potrzeby telemetrii i diagnostyki. Aby dowiedzieć się więcej o interfejsie informacji o urządzeniu, zobacz ten [przykład](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json).

Oczekiwana nazwa składnika w modelu jest **deviceInformation** podczas implementowania tego interfejsu. [Informacje o składnikach PnP usługi Azure IoT](https://docs.microsoft.com/azure/iot-pnp/concepts-components)

|Nazwa|Typ|Schemat|Kierunek|Opis|Przykład|
|----|----|------|---------|-----------|-----------|
|manufacturer|Właściwość|ciąg|urządzenie do chmury|Nazwa firmy producenta urządzenia. Może to być taka sama jak nazwa producenta oryginalnego sprzętu (OEM).|Contoso|
|model|Właściwość|ciąg|urządzenie do chmury|Nazwa lub Identyfikator modelu urządzenia.|Urządzenie IoT Edge|
|swVersion|Właściwość|ciąg|urządzenie do chmury|Wersja oprogramowania na urządzeniu. swVersion może być wersją oprogramowania układowego.|4.15.0 — 122|
|osName|Właściwość|ciąg|urządzenie do chmury|Nazwa systemu operacyjnego na urządzeniu.|Ubuntu Server 18.04|
|processorArchitecture|Właściwość|ciąg|urządzenie do chmury|Architektura procesora w urządzeniu.|ARM64|
|processorManufacturer|Właściwość|ciąg|urządzenie do chmury|Nazwa producenta procesora na urządzeniu.|Microsoft|
|totalStorage|Właściwość|ciąg|urządzenie do chmury|Łączna ilość dostępnego miejsca na urządzeniu w kilobajtach.|2048|
|totalMemory|Właściwość|ciąg|urządzenie do chmury|Łączna ilość dostępnej pamięci na urządzeniu w kilobajtach.|256|

## <a name="model-id"></a>Identyfikator modelu 

Identyfikator modelu to sposób, w jaki inteligentne urządzenia anonsują swoje możliwości w aplikacjach Azure IoT za pomocą usługi IoT plug i Play.To Dowiedz się więcej na temat tworzenia inteligentnych urządzeń w celu anonsowania ich możliwości w aplikacjach Azure IoT odwiedź [Przewodnik dla deweloperów urządzeń iot Plug and Play](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c).

Aktualizacja urządzenia dla IoT Hub wymaga, aby urządzenie usługi IoT Plug and Play inteligentne ogłaszać Identyfikator modelu o wartości **"dtmi: AzureDeviceUpdate; 1"** w ramach połączenia urządzenia. [Dowiedz się, jak ogłosić Identyfikator modelu](https://docs.microsoft.com/azure/iot-pnp/concepts-developer-guide-device-c#model-id-announcement).
