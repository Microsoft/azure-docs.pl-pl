---
title: Media Services terminologii i zmiany jednostek
description: W tym artykule opisano różnice w terminologii między Azure Media Services V2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5c1fbaf9be4cb128f0e4390a8c97c6f0b9330ce2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559880"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Terminologia i zmiany jednostek między Media Services V2 i V3

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-2.svg)

W tym artykule opisano różnice w terminologii między Azure Media Services V2 a v3.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Zapoznaj się z konwencjami nazewnictwa, które są stosowane do zasobów Media Services v3. Przeglądanie również [nazw obiektów BLOB](assets-concept.md#naming)

## <a name="terminology-changes"></a>Zmiany terminologii

- *Lokalizator* jest teraz nazywany *lokalizatorem przesyłania strumieniowego*.
- *Kanał* jest teraz nazywany *wydarzeniem na żywo*.
- *Program* jest teraz nazywany *wyjściem na żywo*.
- *Zadanie* jest teraz nazywane *JobOutput*, który jest częścią zadania.

## <a name="entity-changes"></a>Zmiany jednostek
| **Jednostka v2**<!-- row --> | **Jednostka v3** | **Wskazówki** | **Dostępne dla wersji 3** | **Zaktualizowane przez v3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  Jednostka `AccessPolicies` nie istnieje w wersji 3. | Nie | Nie |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Tak | Tak |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Tak | Nie |
| `AssetFile`<!-- row --> | <!-- empty --> |Jednostka `AssetFiles` nie istnieje w wersji 3. Pliki (obiekty blob magazynu), które można przekazać, są nadal uznawane za pliki.<br/><br/> Użyj interfejsów API usługi Azure Storage, aby wyliczyć obiekty blob w kontenerze. Istnieją dwa sposoby zastosowania transformacji do plików z zadaniem:<br/><br/>Pliki zostały już przekazane do magazynu: identyfikator URI będzie zawierać identyfikator zasobu dla zadań, które mają być wykonywane dla zasobów w ramach konta magazynu.<br/><br/>Pliki do przekazania podczas transformacji i procesu zadania: zasób jest tworzony w magazynie, zwracany jest adres URL sygnatury dostępu współdzielonego, pliki są przekazywane do magazynu, a następnie transformacja zostanie zastosowana do plików. | Nie | Nie |
| `Channel`<!-- row --> | `LiveEvent` | Zdarzenia na żywo zastępują kanały z interfejsu API v2. Korzystają one z większości funkcji i mają więcej nowych funkcji, takich jak transkrypcje dynamiczne, tryb gotowości i obsługa pozyskiwania RTMP. <br/><br/>Zapoznaj [się z wydarzeniem na żywo w ramach przesyłania strumieniowego na żywo](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | Nie | Nie |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` nie jest już jednostką, jest teraz właściwością lokalizatora przesyłania strumieniowego.<br/><br/>  W wersji 3 dane klucza zawartości są skojarzone z `StreamingLocator` (na potrzeby szyfrowania danych wyjściowych) lub samego zasobu (w przypadku szyfrowania magazynu po stronie klienta). | Tak | Nie |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Tak | Nie |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` są zawarte w `ContentKeyPolicy` . | Tak | Nie |
| `IngestManifest`<!-- row --> | <!-- empty --> | Jednostka `IngestManifests` nie istnieje w wersji 3. Przekazywanie plików w wersji 3 obejmuje interfejs API usługi Azure Storage. Najpierw tworzone są zasoby, a następnie pliki są przekazywane do skojarzonego kontenera magazynu. Istnieje wiele sposobów na uzyskanie danych do kontenera usługi Azure Storage, którego można użyć zamiast tego. `JobInputHttp` umożliwia również pobranie danych wejściowych zadania z podanego adresu URL w razie potrzeby. | Nie | Nie |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Istnieje wiele sposobów na uzyskanie danych do kontenera usługi Azure Storage, którego można użyć zamiast tego. `JobInputHttp` umożliwia również pobranie danych wejściowych zadania z podanego adresu URL w razie potrzeby. | Nie | Nie |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Istnieje wiele sposobów na uzyskanie danych do kontenera usługi Azure Storage, którego można użyć zamiast tego. `JobInputHttp` umożliwia również pobranie danych wejściowych zadania z podanego adresu URL w razie potrzeby. | Nie | Nie |
| `Job`<!-- row --> | `Job` | Utwórz element a `Transform` przed utworzeniem `Job` . | Nie | Nie |
| `JobTemplate`<!-- row --> | `Transform` | `Transform`Zamiast tego użyj. Transformacja jest oddzielną jednostką od zadania i jest wielokrotnego użytku. | Nie | Nie |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Tak | Nie |
| `MediaProcessor`<!-- row --> | <!-- empty --> | Zamiast wyszukiwania `MediaProcessor` do użycia według nazwy, użyj żądanego ustawienia wstępnego podczas definiowania transformacji. Używane ustawienie wstępne określi procesor multimedialny używany przez system zadań. Zobacz tematy dotyczące kodowania w [kodowaniu opartym na scenariuszu](migrate-v-2-v-3-migration-scenario-based-encoding.md). <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | Nie | NA (ReadOnly w wersji 2) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | Powiadomienia w wersji 3 są obsługiwane przez Azure Event Grid. Program `NotificationEndpoint` jest zastępowany przez Event Grid rejestrację subskrypcji, która również hermetyzuje konfigurację typów otrzymywanych powiadomień (w wersji 2 zostało obsłużone przez `JobNotificationSubscription` zadanie, `TaskNotificationSubscription` zadanie i dane telemetryczne `ComponentMonitoringSetting` ). Dane telemetryczne v2 zostały podzielone między Azure Event Grid i Azure Monitor, aby dopasować je do ulepszeń większego ekosystemu platformy Azure. | Nie | Nie |
| `Program`<!-- row --> | `LiveOutput` | Wyjście na żywo teraz zastępuje programy w interfejsie API v3.  | Nie | Nie |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | Punkty końcowe przesyłania strumieniowego pozostają przede wszystkim takie same. Są one używane do dynamicznego tworzenia pakietów, szyfrowania i dostarczania zawartości HLS i KRESKOWANY zarówno w przypadku przesyłania strumieniowego na żywo, jak i na żądanie, bezpośrednio z pochodzenia lub za pośrednictwem sieci CDN. Nowe funkcje obejmują obsługę lepszego Azure Monitor integracji i sporządzania wykresów. |  Tak | Tak |
| `Task`<!-- row --> | `JobOutput` | Zastąpione przez `JobOutput` (który nie jest już jednostką w interfejsie API).  Zobacz tematy dotyczące kodowania w [kodowaniu opartym na scenariuszu](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nie | Nie |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Zastąpione przez `TransformOutput` (który nie jest już jednostką w interfejsie API). Zobacz tematy dotyczące kodowania w [kodowaniu opartym na scenariuszu](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Nie | Nie |
| `Inputs`<!-- row --> | `Inputs` | Dane wejściowe i wyjściowe są teraz na poziomie zadania. Zobacz tematy dotyczące kodowania w [kodowaniu opartym na scenariuszu](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Nie | Nie |
| `Outputs`<!-- row --> | `Outputs` | Dane wejściowe i wyjściowe są teraz na poziomie zadania.  W wersji 3 format metadanych został zmieniony z XML na JSON.  Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Zobacz tematy dotyczące kodowania w [kodowaniu opartym na scenariuszu](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Nie | Nie |


| **Inne zmiany** | **V**  | **Czytanie** |
|---|---|---|
| **Storage** <!--new row --> |||
| Storage <!--new row --> | | Zestawy SDK V3 są teraz rozłączone z ZESTAWem danych magazynu, co zapewnia większą kontrolę nad wersją zestawu SDK magazynu, którego chcesz używać, oraz pozwala uniknąć problemów z przechowywaniem wersji.                      |
| **Kodowanie** <!--new row --> |||
| Kodowanie szybkości transmisji bitów <!--new row --> | szybkość transmisji bitów mierzona w KB/s: 128 (KB/s)| bity na sekundę: 128000 (bity/s)|
| Kodowanie FairPlay DRM <!--new row --> | W Media Services V2 można określić wektor inicjalizacji (IV). | W Media Services V3 nie można określić FairPlay IV.|
| Koder w warstwie Premium <!--new row --> | Koder w warstwie Premium i starszy indeksator| [Koder w warstwie Premium](../previous/media-services-encode-asset.md) i starsze [procesory analizy multimediów](../previous/legacy-components.md) (Azure Media Services Indexer 2 — wersja zapoznawcza, Front redactor itp.) nie są dostępne za pośrednictwem wersji 3. Dodaliśmy obsługę mapowania kanału dźwiękowego do kodera standardowego.  Zapoznaj [się z artykułem audio w dokumentacji programu Media Servicesing Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).  | Zobacz tematy dotyczące kodowania w [kodowaniu opartym na scenariuszu](migrate-v-2-v-3-migration-scenario-based-encoding.md) |
| **Transformacje i zadania** <!--new row -->|||
| Przetwarzanie przy użyciu protokołu HTTPS na podstawie zadania <!--new row --> |<!-- empty -->| W przypadku przetwarzania zadań opartych na plikach można użyć adresu URL protokołu HTTPS jako dane wejściowe. Nie musisz mieć już zawartości przechowywanej na platformie Azure ani nie musisz tworzyć zasobów. |
| Szablony usługi ARM dla zadań <!--new row --> | Szablony ARM nie istnieją w wersji 2. | Przekształcenie może służyć do kompilowania konfiguracji do wielokrotnego użytku, tworzenia Azure Resource Manager szablonów i izolowania ustawień przetwarzania między wieloma klientami lub dzierżawcami. |
| **Zdarzenia na żywo** <!--new row --> |||
| Punkt końcowy przesyłania strumieniowego <!--new row --> | Punkt końcowy przesyłania strumieniowego reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub Content Delivery Network (CDN) w celu dalszej dystrybucji. | Punkty końcowe przesyłania strumieniowego pozostają przede wszystkim takie same. Są one używane do dynamicznego tworzenia pakietów, szyfrowania i dostarczania zawartości HLS i KRESKOWANY zarówno w przypadku przesyłania strumieniowego na żywo, jak i na żądanie, bezpośrednio z pochodzenia lub za pośrednictwem sieci CDN.  Nowe funkcje obejmują obsługę lepszego Azure Monitor integracji i sporządzania wykresów. |
| Kanały zdarzeń na żywo <!--new row --> | Kanały są odpowiedzialne za przetwarzanie zawartości przesyłania strumieniowego na żywo. Kanał udostępnia wejściowy punkt końcowy (adres URL pozyskiwania), który następnie można przekazać do transkodera na żywo. Kanał odbiera strumienie danych wejściowych na żywo z transkodera dynamicznego i udostępnia je do przesyłania strumieniowego za pośrednictwem jednego lub większej liczby punktów końcowych przesyłania strumieniowego. Kanały udostępniają również punkt końcowy (wersja zapoznawcza) służący do wyświetlania podglądu i weryfikowania strumienia przed dalszem przetwarzaniem i dostarczaniem.| Zdarzenia na żywo zastępują kanały z interfejsu API v2. Korzystają one z większości funkcji i mają więcej nowych funkcji, takich jak transkrypcje dynamiczne, tryb gotowości i obsługa pozyskiwania RTMP. |
| Programy zdarzeń na żywo <!--new row --> | Program umożliwia kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami. Relacja kanału i programu jest podobna do tradycyjnego nośnika, gdzie kanał ma stały strumień zawartości, a program jest objęty zakresem pewnego czasu zdarzenia w tym kanale. Możesz określić liczbę godzin, przez jaką ma być zachowana zarejestrowana zawartość dla programu przez ustawienie `ArchiveWindowLength` właściwości. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin.| Wyjście na żywo teraz zastępuje programy w interfejsie API v3. |
| Długość zdarzenia na żywo <!--new row --> |<!-- empty -->| Można przesyłać strumieniowo zdarzenia na żywo 24/7 przy użyciu Media Services do transkodowania pojedynczego źródła szybkości transmisji bitów do strumienia wyjściowego, który ma wiele szybkości transmisji bitów.|
| Opóźnienie zdarzeń na żywo <!--new row --> |<!-- empty -->| Obsługa przesyłania strumieniowego na żywo w przypadku nowych małych opóźnień na żywo. |
| Podgląd zdarzeń na żywo <!--new row --> |<!-- empty -->| Usługa Live Event Preview obsługuje dynamiczne pakowanie i szyfrowanie dynamiczne. Umożliwia to ochronę zawartości w wersji zapoznawczej, a także używanie ŁĄCZNIKa i HLS. |
| Aktywne zdarzenia RTMP <!--new row --> |<!-- empty-->| Ulepszona obsługa protokołu RTMP z zwiększoną stabilnością i większą obsługą kodera źródłowego. |
| Bezpieczne pozyskiwanie bezpiecznych zdarzeń <!--new row --> | | Gdy tworzysz wydarzenie na żywo, otrzymujesz 4 adresy URL pozyskiwania. 4 adresy URL pozyskiwania są prawie identyczne, mają ten sam token przesyłania strumieniowego `AppId` , a jedynie część numer portu. Dwa z adresów URL to podstawowy i zapasowy dla RTMP.| 
| Transkrypcja zdarzenia na żywo <!--new row --> |<!-- empty--> | Usługa Azure Media Service oferuje wideo, audio i tekst w różnych protokołach. Gdy publikujesz strumień na żywo przy użyciu formatu MPEG-KRESKowego lub HLS/CMAF, a następnie wideo i audio, Nasza usługa dostarcza tekst uzyskanego w IMSC 1.1 zgodne TTML.|
| Tryb gotowości zdarzenia na żywo <!--new row --> | Brak trybu wstrzymania dla wersji 2. | Tryb gotowości to nowa funkcja v3, która ułatwia zarządzanie dynamicznymi pulami wydarzeń na żywo. Przed przejściem do stanu działania klienci mogą teraz rozpocząć wydarzenie na żywo w trybie czuwania przy niższych kosztach. Zwiększa to czasy uruchamiania kanału i zmniejsza koszty operacyjnych pul z możliwością szybszego uruchamiania. |
| Rozliczanie wydarzeń na żywo <!--new row --> | <!-- empty-->| Rozliczenia wydarzeń na żywo opierają się na licznikach kanału na żywo. |
| Wyjście na żywo <!--new row --> | Programy musiały zostać uruchomione po utworzeniu. | Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. |
