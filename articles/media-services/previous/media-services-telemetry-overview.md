---
title: Azure Media Services telemetrii | Microsoft Docs
description: Ten artykuł zawiera omówienie Microsoft Azure Media Services telemetrii.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 4bf9a96d7ffc3b939abe8cfb889c5bd49fee09cc
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694625"
---
# <a name="azure-media-services-telemetry"></a>Dane telemetryczne Azure Media Services  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services (AMS) umożliwia dostęp do danych telemetrycznych/metryk dla swoich usług. Bieżąca wersja usługi AMS pozwala zbierać dane telemetryczne dotyczące jednostek **kanału** na żywo, **StreamingEndpoint** i **archiwum** na żywo. 

Dane telemetryczne są zapisywane w tabeli magazynu na koncie usługi Azure Storage określonym przez użytkownika (zazwyczaj jest używane konto magazynu skojarzone z kontem AMS). 

System telemetrii nie zarządza przechowywaniem danych. Stare dane telemetryczne można usunąć, usuwając tabele magazynu.

W tym temacie omówiono sposób konfigurowania i korzystania z telemetrii AMS.

## <a name="configuring-telemetry"></a>Konfigurowanie telemetrii

Można skonfigurować telemetrię na poziomie składnika. Istnieją dwa poziomy szczegółowości "normal" i "verbose". Obecnie Oba poziomy zwracają te same informacje. Zalecane jest użycie "Normal. 

W poniższych tematach pokazano, jak włączyć telemetrię:

[Włączanie telemetrii za pomocą platformy .NET](media-services-dotnet-telemetry.md) 

[Włączanie telemetrii przy użyciu interfejsu REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Zużywanie informacji telemetrycznych

Dane telemetryczne są zapisywane w tabeli usługi Azure Storage na koncie magazynu określonym podczas konfigurowania telemetrii dla konta Media Services. W tej sekcji opisano tabele magazynu dla metryk.

Dane telemetryczne można wykorzystać w jeden z następujących sposobów:

- Odczytaj dane bezpośrednio z usługi Azure Table Storage (np. przy użyciu zestawu SDK magazynu). Opis tabel magazynu telemetrii znajduje się w temacie Korzystanie z **informacji telemetrycznych** w [tym](/previous-versions/azure/mt742089(v=azure.100)) temacie.

Lub

- Skorzystaj z pomocy technicznej w zestawie SDK Media Services .NET na potrzeby odczytywania danych magazynu, zgodnie z opisem w [tym](media-services-dotnet-telemetry.md) temacie. 


Schemat telemetrii opisany poniżej został zaprojektowany w celu zapewnienia odpowiedniej wydajności w ramach limitów Table Storage platformy Azure:

- Dane są partycjonowane według identyfikatora konta i identyfikatora usługi, aby umożliwić niezależne wykonywanie zapytań telemetrycznych z poszczególnych usług.
- Partycje zawierają datę, aby zapewnić rozsądną górną granicę rozmiaru partycji.
- Klucze wierszy są w odwrotnej kolejności w celu zezwolenia na wykonywanie zapytań dotyczących najnowszych elementów telemetrii dla danej usługi.

Powinno to umożliwić wydajne wykonywanie wielu typowych zapytań:

- Równoległe, niezależne pobieranie danych dla oddzielnych usług.
- Pobieranie wszystkich danych dla danej usługi w zakresie dat.
- Pobieranie najnowszych danych dla usługi.

### <a name="telemetry-table-storage-output-schema"></a>Schemat danych wyjściowych magazynu tabeli telemetrii

Dane telemetryczne są przechowywane w postaci agregacji w jednej tabeli, "TelemetryMetrics20160321", gdzie "20160321" to data utworzonej tabeli. System telemetrii tworzy oddzielną tabelę dla każdego nowego dnia w oparciu o 00:00 czasu UTC. Tabela służy do przechowywania wartości cyklicznych, takich jak Pozyskaj szybkość transmisji bitów w danym oknie czasu, wysłanych bajtów itd. 

Właściwość|Wartość|Przykłady/uwagi
---|---|---
PartitionKey|{Account ID} _ {identyfikator jednostki}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>Identyfikator konta jest uwzględniany w kluczu partycji, aby uprościć przepływy pracy, w których wiele kont Media Services zapisuje się na tym samym koncie magazynu.
RowKey|{s do północy} _ {wartość losowa}|01688_00199<br/><br/>Klucz wiersza rozpoczyna się od liczby sekund do północy, aby zezwolić na pierwsze n kwerend w stylu w ramach partycji. Aby uzyskać więcej informacji, zobacz [ten](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artykuł. 
Znacznik czasu|Data/godzina|Autosygnatura czasowa z tabeli platformy Azure 2016 – 09-09T22:43:42.241 Z
Typ|Typ jednostki dostarczającej dane telemetryczne|Kanał/StreamingEndpoint/Archiwum<br/><br/>Typ zdarzenia to tylko wartość ciągu.
Nazwa|Nazwa zdarzenia telemetrii|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Czas wystąpienia zdarzenia telemetrii (UTC)|2016 – 09-09T22:42:36.924 Z<br/><br/>Zaobserwowany czas jest dostarczany przez jednostkę wysyłającą dane telemetryczne (na przykład kanał). Mogą występować problemy z synchronizacją między składnikami, dzięki czemu ta wartość jest przybliżona
ServiceID|{Identyfikator usługi}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Właściwości specyficzne dla jednostki|Zgodnie z definicją zdarzenia|StreamName: STREAM1, szybkość transmisji bitów 10123,...<br/><br/>Pozostałe właściwości są zdefiniowane dla danego typu zdarzenia. Zawartość tabeli platformy Azure to pary klucz-wartość.  (oznacza to, że różne wiersze w tabeli mają różne zestawy właściwości).

### <a name="entity-specific-schema"></a>Schemat specyficzny dla jednostki

Istnieją trzy typy wpisów danych telemetrycznych specyficznych dla jednostki, które zostały wypchnięte z następującą częstotliwością:

- Punkty końcowe przesyłania strumieniowego: co 30 sekund
- Kanały na żywo: co minutę
- Archiwum dynamiczne: co minutę

**Punkt końcowy przesyłania strumieniowego**

Właściwość|Wartość|Przykłady
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Autosygnatura czasowa z tabeli platformy Azure 2016 – 09-09T22:43:42.241 Z
Typ|Typ|StreamingEndpoint
Nazwa|Nazwa|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016 – 09-09T22:42:36.924 Z
ServiceID|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|Nazwa hosta punktu końcowego|builddemoserver.origin.mediaservices.windows.net
Stanu|Rejestruje stan HTTP|200
ResultCode|Szczegóły kodu wyniku|S_OK
RequestCount|Łączna liczba żądań w agregacji|3
BytesSent|Wysłane bajty zagregowane|2987358
ServerLatency|Średnie opóźnienie serwera (łącznie z magazynem)|129
E2ELatency|Średnie opóźnienie końca do końca|250

**Kanał na żywo**

Właściwość|Wartość|Przykłady/uwagi
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Autosygnatura czasowa z tabeli platformy Azure 2016 – 09-09T22:43:42.241 Z
Typ|Typ|Kanał
Nazwa|Nazwa|ChannelHeartbeat
ObservedTime|ObservedTime|2016 – 09-09T22:42:36.924 Z
ServiceID|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Ścieżka śledzenia|Typ śledzenia audio/wideo/tekstu|wideo/audio
Ścieżka śledzenia|Nazwa ścieżki|wideo/audio_1
Multimedia|Śledzenie szybkości transmisji bitów|785000
CustomAttributes —||   
IncomingBitrate|Rzeczywista szybkość transmisji bitów|784548
OverlapCount|Nakładanie się na pozyskiwanie|0
DiscontinuityCount|Nieciągłość śledzenia|0
LastTimestamp|Ostatni przezyskany znacznik czasu danych|1800488800
NonincreasingCount|Liczba fragmentów odrzuconych ze względu na nierosnącą sygnaturę czasową|2
UnalignedKeyFrames|Czy zostały odebrane fragmenty (na różnych poziomach jakości), w których ramki kluczowe nie są wyrównane |Prawda
UnalignedPresentationTime|Czy zostały odebrane fragmenty (różne poziomy jakości/ścieżki), w których czas prezentacji nie jest wyrównany|Prawda
UnexpectedBitrate|True, Jeśli obliczona/Rzeczywista szybkość transmisji bitów dla ścieżki audio/wideo > 40 000 BPS i IncomingBitrate = = 0 lub IncomingBitrate i actualBitrate różni się o 50% |Prawda
Dobra kondycja|Prawda, jeśli <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> wszystkie 0|Prawda<br/><br/>Dobra kondycja to funkcja złożona, która zwraca wartość false, jeśli są spełnione następujące warunki:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames = = true<br/>-UnalignedPresentationTime = = true<br/>-UnexpectedBitrate = = true

**Archiwum na żywo**

Właściwość|Wartość|Przykłady/uwagi
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Znacznik czasu|Znacznik czasu|Autosygnatura czasowa z tabeli platformy Azure 2016 – 09-09T22:43:42.241 Z
Typ|Typ|Archiwum
Nazwa|Nazwa|ArchiveHeartbeat
ObservedTime|ObservedTime|2016 – 09-09T22:42:36.924 Z
ServiceID|Identyfikator usługi|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Manifestname|Adres URL programu|zasób — eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba. ISM
Ścieżka śledzenia|Nazwa ścieżki|audio_1
Ścieżka śledzenia|Typ ścieżki|Audio/wideo
Element CustomAttribute ma|Ciąg szesnastkowy odróżniający różne ścieżki o tej samej nazwie i szybkości transmisji bitów (kąt wielu aparatów)|
Multimedia|Śledzenie szybkości transmisji bitów|785000
Dobra kondycja|Prawda, jeśli FragmentDiscardedCount = = 0 && ArchiveAcquisitionError = = false|True (te dwie wartości nie występują w metryce, ale są obecne w zdarzeniu źródłowym)<br/><br/>Dobra kondycja to funkcja złożona, która zwraca wartość false, jeśli są spełnione następujące warunki:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError = = true

## <a name="general-qa"></a>Ogólna Q&A

### <a name="how-to-consume-metrics-data"></a>Jak korzystać z danych metryk?

Dane metryk są przechowywane jako serie tabel platformy Azure na koncie magazynu klienta. Te dane mogą być używane przy użyciu następujących narzędzi:

- ZESTAW SDK AMS
- Eksplorator usługi Microsoft Azure Storage (obsługuje eksportowanie w formacie wartości rozdzielanych przecinkami i przetworzone w programie Excel)
- Interfejs API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Jak znaleźć średnie użycie przepustowości?

Średnie zużycie przepustowości to średnia z BytesSent w danym okresie czasu.

### <a name="how-to-define-streaming-unit-count"></a>Jak zdefiniować liczbę jednostek przesyłania strumieniowego?

Liczbę jednostek przesyłania strumieniowego można zdefiniować jako szczytową przepływność z punktów końcowych przesyłania strumieniowego usługi podzieloną przez szczytową Przepływność jednego punktu końcowego przesyłania strumieniowego. Szczytowa przepływność dla jednego punktu końcowego przesyłania strumieniowego to 160 MB/s.
Załóżmy na przykład, że szczytowa przepływność od usługi klienta to 40 MB/s (maksymalna wartość BytesSent w danym okresie). Następnie liczba jednostek przesyłania strumieniowego jest równa (40 MB/s) * (8 bitów/bajt)/(160 MB/s) = 2 jednostki przesyłania strumieniowego.

### <a name="how-to-find-average-requestssecond"></a>Jak znaleźć średnią liczbę żądań na sekundę?

Aby znaleźć średnią liczbę żądań na sekundę, Oblicz średnią liczbę żądań (RequestCount) w danym okresie czasu.

### <a name="how-to-define-channel-health"></a>Jak zdefiniować kondycję kanału?

Kondycję kanału można zdefiniować jako funkcję złożonej wartości logicznej, która jest fałszywa w przypadku spełnienia dowolnego z następujących warunków:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames = = true 
- UnalignedPresentationTime = = true 
- UnexpectedBitrate = = true


### <a name="how-to-detect-discontinuities"></a>Jak wykrywać przerwy?

Aby wykryć przerwy, Znajdź wszystkie wpisy danych kanału, w których DiscontinuityCount > 0. Odpowiednia sygnatura czasowa ObservedTime wskazuje czas, w którym wystąpiły przerwy.

### <a name="how-to-detect-timestamp-overlaps"></a>Jak wykrywać nakładanie się znaczników czasu?

Aby wykrywać nakładanie się sygnatury czasowej, Znajdź wszystkie wpisy danych kanału, w których OverlapCount > 0. Odpowiednia sygnatura czasowa ObservedTime wskazuje czas, w którym wystąpiła sygnatura czasowa.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Jak znaleźć błędy i przyczyny żądań przesyłania strumieniowego?

Aby znaleźć błędy żądań przesyłania strumieniowego i przyczyny, Znajdź wszystkie wpisy danych punktów końcowych przesyłania strumieniowego, w których ResultCode nie jest równa S_OK. Odpowiednie pole StatusCode wskazuje przyczynę niepowodzenia żądania.

### <a name="how-to-consume-data-with-external-tools"></a>Jak używać danych z narzędziami zewnętrznymi?

Dane telemetryczne mogą być przetwarzane i wizualizowane przy użyciu następujących narzędzi:

- PowerBI
- Application Insights
- Azure Monitor (dawniej Shoebox)
- Pulpit nawigacyjny Live AMS
- Witryna Azure Portal (wersja oczekująca)

### <a name="how-to-manage-data-retention"></a>Jak zarządzać przechowywaniem danych?

System telemetrii nie zapewnia zarządzania przechowywaniem danych ani usuwania starych rekordów. W tym celu należy ręcznie zarządzać starymi rekordami i usuwać je z tabeli magazynu. Aby to zrobić, możesz zapoznać się z zestawem SDK usługi Storage.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
