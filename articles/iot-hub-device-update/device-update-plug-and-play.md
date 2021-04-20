---
title: Informacje na temat sposobu, w jaki aktualizacja IoT Hub korzysta z IoT Plug and Play | Microsoft Docs
description: Aktualizacja urządzenia dla IoT Hub umożliwia odnajdywanie urządzeń z możliwością aktualizacji w powietrzu i zarządzanie nimi.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: fbc3502952e11830ef9abb06cb709fcc60288343
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739534"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Aktualizacja urządzenia dla IoT Hub i IoT Plug and Play

Aktualizacja urządzenia dla IoT Hub korzysta [IoT Plug and Play](../iot-pnp/index.yml) do odnajdywania urządzeń z możliwością aktualizacji w powietrzu i zarządzania nimi. Usługa Aktualizacji urządzeń będzie wysyłać i odbierać właściwości oraz komunikaty do i z urządzeń przy użyciu interfejsów PnP. Aktualizacja urządzenia dla IoT Hub wymaga, aby urządzenia IoT implementowały następujące interfejsy i identyfikator modelu zgodnie z poniższym opisem.

## <a name="adu-core-interface"></a>Interfejs podstawowy ADU

Interfejs "ADUCoreInterface" służy do wysyłania akcji aktualizacji i metadanych do urządzeń oraz odbierania stanu aktualizacji z urządzeń. Interfejs "ADU Core" jest podzielony na dwie właściwości obiektu.

Oczekiwana nazwa składnika w modelu to **"azureDeviceUpdateAgent"** podczas implementowania tego interfejsu. [Dowiedz się więcej o składnikach usługi Azure IoT PnP](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Metadane agenta

Metadane agenta zawierają pola, których urządzenie lub agent aktualizacji urządzenia używa do wysyłania informacji i stanu do usług Aktualizacji urządzeń.

|Nazwa|Schemat|Kierunek|Opis|Przykład|
|----|------|---------|-----------|-----------|
|resultCode|liczba całkowita|z urządzenia do chmury|Kod, który zawiera informacje o wyniku ostatniej akcji aktualizacji. Może być wypełniany w celu powodzenia lub niepowodzenia i powinien być zgodne ze [specyfikacją kodu stanu HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extendedResultCode|liczba całkowita|z urządzenia do chmury|Kod, który zawiera dodatkowe informacje o wyniku. Można je wypełnić w celu powodzenia lub niepowodzenia.|0x80004005|
|stan|liczba całkowita|z urządzenia do chmury|Jest to liczba całkowita wskazująca bieżący stan agenta aktualizacji urządzenia. Aby uzyskać szczegółowe informacje, zobacz poniżej |Okresy|
|installedUpdateId|ciąg|z urządzenia do chmury|Identyfikator aktualnie zainstalowanej aktualizacji (za pośrednictwem aktualizacji urządzenia). Ta wartość będzie miała wartość null dla urządzenia, które nigdy nie podjęło aktualizacji za pośrednictwem aktualizacji urządzenia.|Zero|
|`deviceProperties`|Mapa|z urządzenia do chmury|Zestaw właściwości, które zawierają producenta i model.|Aby uzyskać szczegółowe informacje, zobacz poniżej

#### <a name="state"></a>Stan

Jest to stan zgłoszony przez agenta aktualizacji urządzenia po otrzymaniu akcji z usługi aktualizacji urządzeń. `State` jest zgłaszany w odpowiedzi na (patrz poniżej) wysłane do agenta `Action` aktualizacji urządzenia z usługi aktualizacji `Actions` urządzeń. Zapoznaj się z [przepływem pracy przeglądu](understand-device-update.md#device-update-agent) żądań przepływu między usługą Device Update Service i agentem aktualizacji urządzeń.

|Nazwa|Wartość|Opis|
|---------|-----|-----------|
|Okresy|0|Urządzenie jest gotowe do odbierania akcji z usługi aktualizacji urządzeń. Po pomyślnej aktualizacji stan jest zwracany do `Idle` stanu .|
|DownloadSucceededed|2|Pomyślne pobranie.|
|InstallSucceededed|4|Pomyślna instalacja.|
|Niepowodzenie|255|Wystąpił błąd podczas aktualizowania.|

#### <a name="device-properties"></a>Właściwości urządzenia

Jest to zestaw właściwości, które zawierają producenta i model.

|Nazwa|Schemat|Kierunek|Opis|
|----|------|---------|-----------|
|manufacturer|ciąg|z urządzenia do chmury|Producent urządzenia zgłoszony za pośrednictwem `deviceProperties` . Ta właściwość jest odczytywana z jednego z dwóch miejsc — interfejs "AzureDeviceUpdateCore" najpierw podejmie próbę odczytania wartości "aduc_manufacturer" z pliku [konfiguracji.](device-update-configuration-file.md)  Jeśli wartość nie zostanie wypełniona w pliku konfiguracji, domyślnie będzie zgłaszana definicja czasu kompilacji dla ADUC_DEVICEPROPERTIES_MANUFACTURER. Ta właściwość będzie zgłaszana tylko podczas rozruchu.|
|model|ciąg|z urządzenia do chmury|Model urządzenia zgłoszony za pośrednictwem `deviceProperties` . Ta właściwość jest odczytywana z jednego z dwóch miejsc — interfejs AzureDeviceUpdateCore najpierw podejmie próbę odczytania wartości "aduc_model" z pliku [konfiguracji.](device-update-configuration-file.md)  Jeśli wartość nie zostanie wypełniona w pliku konfiguracji, domyślnie raportowanie definicji czasu kompilacji dla ADUC_DEVICEPROPERTIES_MODEL. Ta właściwość będzie zgłaszana tylko podczas rozruchu.|
|aduVer|ciąg|z urządzenia do chmury|Wersja agenta aktualizacji urządzenia uruchomionego na urządzeniu. Ta wartość jest odczytywana z kompilacji tylko wtedy, gdy podczas kompilacji ENABLE_ADU_TELEMETRY_REPORTING jest ustawiona na 1 (true). Klienci mogą zrezygnować z raportowania wersji, ustawiając wartość 0 (false). [Jak dostosować właściwości agenta aktualizacji urządzeń.](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)|
|Dover|ciąg|z urządzenia do chmury|Wersja agenta Optymalizacja dostarczania uruchomionego na urządzeniu. Wartość jest odczytywana z kompilacji tylko wtedy, gdy w czasie kompilacji ENABLE_ADU_TELEMETRY_REPORTING ustawiono wartość 1 (true). Klienci mogą zrezygnować z raportowania wersji, ustawiając wartość 0 (false). [Jak dostosować właściwości Optymalizacja dostarczania agenta.](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)|

### <a name="service-metadata"></a>Metadane usługi

Metadane usługi zawierają pola używane przez usługi Aktualizacji urządzeń do przekazywania akcji i danych agentowi aktualizacji urządzeń.

|Nazwa|Schemat|Kierunek|Opis|
|----|------|---------|-----------|
|action|liczba całkowita|z chmury do urządzenia|Jest to liczba całkowita odpowiadająca akcji, jaką powinien wykonać agent. Wartości wymienione poniżej.|
|updateManifest|ciąg|z chmury do urządzenia|Służy do opisywania zawartości aktualizacji. Wygenerowane na podstawie [manifestu importu](import-update.md#create-a-device-update-import-manifest)|
|updateManifestSignature|JSON, obiekt|z chmury do urządzenia|Podpis internetowy JSON (JWS) z kluczami sieci Web JSON używanymi do weryfikacji źródła.|
|fileUrls|Mapa|z chmury do urządzenia|Mapowanie `FileHash` na `DownloadUri` . Informuje agenta, które pliki mają zostać pobrane, oraz wartość skrótu do użycia w celu sprawdzenia, czy pliki zostały pobrane poprawnie.|

#### <a name="action"></a>Akcja

`Actions` Wartość poniżej reprezentuje akcje podjęte przez agenta aktualizacji urządzenia zgodnie z instrukcjami usługi Aktualizacji urządzeń. Agent aktualizacji urządzenia zda raport `State` (zobacz `State` sekcję powyżej) przetwarzający `Action` odebrane dane. Zapoznaj się z [przepływem pracy przeglądu,](understand-device-update.md#device-update-agent) aby uzyskać informacje o żądaniach przepływu między usługą Device Update Service i agentem aktualizacji urządzeń.

|Nazwa|Wartość|Opis|
|---------|-----|-----------|
|Pobierz|0|Pobieranie opublikowanej zawartości lub aktualizowanie oraz wszelkiej innej potrzebnej zawartości|
|Instalowanie|1|Zainstaluj zawartość lub aktualizację. Zazwyczaj oznacza to wywołanie instalatora dla zawartości lub aktualizacji.|
|Zastosuj|2|Zakończ aktualizację. W razie potrzeby sygnalizuje on ponowne uruchomienie systemu.|
|Anuluj|255|Zatrzymaj przetwarzanie bieżącej akcji i wróć do `Idle` . Będzie również służyć do nakłoniania agenta do powrotu do `Failed` stanu `Idle` .|

## <a name="device-information-interface"></a>Interfejs informacji o urządzeniu

Interfejs informacji o urządzeniu jest pojęciem używanym w [IoT Plug and Play architektury](../iot-pnp/overview-iot-plug-and-play.md). Zawiera właściwości z urządzenia do chmury, które zawierają informacje o sprzęcie i systemie operacyjnym urządzenia. Aktualizacja urządzenia dla IoT Hub korzysta z właściwości DeviceInformation.manufacturer i DeviceInformation.model na potrzeby telemetrii i diagnostyki. Aby dowiedzieć się więcej na temat interfejsu informacji o urządzeniu, zobacz ten [przykład](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json).

Oczekiwaną nazwą składnika w modelu jest **deviceInformation** podczas implementowania tego interfejsu. [Dowiedz się więcej o składnikach usługi Azure IoT PnP](../iot-pnp/concepts-components.md)

|Nazwa|Typ|Schemat|Kierunek|Opis|Przykład|
|----|----|------|---------|-----------|-----------|
|manufacturer|Właściwość|ciąg|z urządzenia do chmury|Nazwa firmy producenta urządzenia. Może to być taka sama jak nazwa producenta oryginalnego sprzętu (OEM).|Contoso|
|model|Właściwość|ciąg|z urządzenia do chmury|Nazwa lub identyfikator modelu urządzenia.|IoT Edge urządzenia|
|swVersion|Właściwość|ciąg|z urządzenia do chmury|Wersja oprogramowania na urządzeniu. SwVersion może być wersją oprogramowania układowego.|4.15.0-122|
|osName|Właściwość|ciąg|z urządzenia do chmury|Nazwa systemu operacyjnego na urządzeniu.|Ubuntu Server 18.04|
|processorArchitecture|Właściwość|ciąg|z urządzenia do chmury|Architektura procesora na urządzeniu.|ARM64|
|processorManufacturer|Właściwość|ciąg|z urządzenia do chmury|Nazwa producenta procesora na urządzeniu.|Microsoft|
|totalStorage|Właściwość|ciąg|z urządzenia do chmury|Łączny dostępny magazyn na urządzeniu w kilobajtach.|2048|
|totalMemory|Właściwość|ciąg|z urządzenia do chmury|Całkowita dostępna pamięć na urządzeniu w kilobajtach.|256|

## <a name="model-id"></a>Identyfikator modelu 

Identyfikator modelu to sposób, w jaki urządzenia inteligentne anonsują swoje możliwości aplikacjom usługi Azure IoT za pomocą usługi IoT Plug i usługi Play.To dowiedz się więcej na temat sposobu tworzenia inteligentnych urządzeń w celu anonsowania ich możliwości do aplikacji usługi Azure IoT, odwiedź przewodnik dewelopera urządzeń usługi [IoT Plug and Play.](../iot-pnp/concepts-developer-guide-device.md)

Aktualizacja urządzenia dla usługi IoT Hub wymaga, aby urządzenie inteligentne usługi IoT Plug and Play ogłaszało identyfikator modelu o wartości **"dtmi:AzureDeviceUpdate;1"** w ramach połączenia urządzenia. [Dowiedz się, jak ogłaszać identyfikator modelu.](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement)