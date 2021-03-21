---
title: Koncepcje Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera krótkie omówienie koncepcji Microsoft Azure Media Services i linki do innych artykułów w celu uzyskania szczegółowych informacji.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: fbe387b3f86dfd10cbc4e80a02ee2528e43c4b67
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016886"
---
# <a name="azure-media-services-concepts"></a>Koncepcje Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Ten temat zawiera omówienie najważniejszych koncepcji Media Services.

## <a name="assets-and-storage"></a><a name="assets"></a>Zasoby i magazyn
### <a name="assets"></a>Elementy zawartości
Element [zawartości](/rest/api/media/operations/asset) zawiera pliki cyfrowe (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów) oraz metadane dotyczące tych plików. Po przekazaniu plików cyfrowych do elementu zawartości mogą one być używane w przepływach pracy kodowania Media Services i przesyłania strumieniowego.

Zasób jest mapowany do kontenera obiektów BLOB na koncie usługi Azure Storage, a pliki w elemencie zawartości są przechowywane jako blokowe obiekty blob w tym kontenerze. Stronicowe obiekty blob nie są obsługiwane przez Azure Media Services.

Podczas decydowania o zawartości multimedialnej, która ma zostać przekazana i przechowana, należy zastosować następujące zagadnienia:

* Zasób powinien zawierać tylko jedno, unikatowe wystąpienie zawartości multimedialnej. Na przykład pojedyncza Edycja odcinka TELEWIZORa, filmu lub anonsu.
* Element zawartości nie powinien zawierać wielu wersji ani edycji pliku audiowizualnego. Przykładem nieprawidłowego użycia zasobu będzie próba zapisania więcej niż jednego odcinka TV, anonsu lub wielu kątów kamery z jednej produkcji w obrębie elementu zawartości. Przechowywanie wielu plików lub zmian w pliku audiowizualnym w elemencie zawartości może spowodować problemy z przesyłaniem zadań kodowania, przesyłaniem strumieniowym i zabezpieczeniem dostarczania zasobów później w przepływie pracy.  

### <a name="asset-file"></a>Plik zasobów
[AssetFile](/rest/api/media/operations/assetfile) reprezentuje rzeczywisty plik wideo lub audio, który jest przechowywany w kontenerze obiektów BLOB. Plik zasobów jest zawsze skojarzony z zasobem, a zasób może zawierać jeden lub wiele plików. Zadanie kodera Media Services nie powiedzie się, jeśli obiekt pliku zasobów nie jest skojarzony z plikiem cyfrowym w kontenerze obiektów BLOB.

Wystąpienie **AssetFile** i rzeczywisty plik multimedialny są dwa odrębne obiekty. Wystąpienie AssetFile zawiera metadane dotyczące pliku nośnika, natomiast plik multimedialny zawiera rzeczywistą zawartość multimedialną.

Nie należy próbować zmieniać zawartości kontenerów obiektów blob, które zostały wygenerowane przez Media Services bez używania interfejsów API usługi Media Service.

### <a name="asset-encryption-options"></a>Opcje szyfrowania zasobów
W zależności od typu zawartości, która ma zostać przekazana, przechowanie i dostarczenie, Media Services oferuje różne opcje szyfrowania, spośród których można dokonać wyboru.

>[!NOTE]
>Żadne szyfrowanie nie jest używane. Jest to wartość domyślna. W przypadku korzystania z tej opcji zawartość nie jest chroniona podczas przesyłania ani przechowywania w magazynie.

Jeśli planujesz dostarczanie plików MP4 przy użyciu pobierania progresywnego, Użyj tej opcji, aby przekazać zawartość.

**StorageEncrypted** — ta opcja umożliwia zaszyfrowanie pustej zawartości lokalnie przy użyciu szyfrowania AES 256 bitowym, a następnie przekazanie jej do usługi Azure Storage, gdzie jest przechowywana w stanie spoczynku. Zasoby chronione za pomocą szyfrowania magazynu są automatycznie odszyfrowane i umieszczane w zaszyfrowanym systemie plików przed kodowaniem i opcjonalnie ponownie szyfrowane przed przekazaniem ich jako nowego wyjściowego elementu zawartości. Podstawowym przypadkiem użycia szyfrowania magazynu jest zagwarantowanie, że pliki multimedialne o wysokiej jakości są zabezpieczone przy użyciu silnego szyfrowania na dysku. 

Aby można było dostarczyć zasób zaszyfrowanego magazynu, należy skonfigurować zasady dostarczania zasobów, aby Media Services wie, jak chcesz dostarczyć zawartość. Aby można było przesłać strumieniowo zasób, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu określonych zasad dostarczania (na przykład AES, PlayReady lub bez szyfrowania). 

**CommonEncryptionProtected** — Użyj tej opcji, jeśli chcesz zaszyfrować (lub przekazać już zaszyfrowaną) zawartość za pomocą Common Encryption lub oprogramowania PlayReady DRM (na przykład Smooth Streaming chronić je za pomocą technologii PlayReady DRM).

**EnvelopeEncryptionProtected** — Użyj tej opcji, jeśli chcesz chronić (lub przekazywać już chronione) http Live Streaming (HLS) zaszyfrowaną przy użyciu Advanced Encryption Standard (AES). Jeśli przekazujesz HLS już szyfrowany przy użyciu algorytmu AES, musi on być zaszyfrowany przez Menedżera transformacji.

### <a name="access-policy"></a>Zasady dostępu
[AccessPolicy](/rest/api/media/operations/accesspolicy) definiuje uprawnienia (takie jak Odczyt, zapis i lista) oraz czas trwania dostępu do elementu zawartości. Zwykle obiekt AccessPolicy można przekazać do lokalizatora, który będzie następnie używany do uzyskiwania dostępu do plików znajdujących się w elemencie zawartości.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) temat.

### <a name="blob-container"></a>Kontener obiektów blob
Kontener obiektów BLOB zawiera grupowanie zestawu obiektów BLOB. Kontenery obiektów BLOB są używane w Media Services jako punkt graniczny dla kontroli dostępu i lokalizatorów sygnatury dostępu współdzielonego (SAS) w obszarze zasoby. Konto usługi Azure Storage może zawierać nieograniczoną liczbę kontenerów obiektów BLOB. Kontener może przechowywać nieograniczoną liczbę obiektów blob.

>[!NOTE]
> Nie należy próbować zmieniać zawartości kontenerów obiektów blob, które zostały wygenerowane przez Media Services bez używania interfejsów API usługi Media Service.
> 
> 

### <a name="locators"></a><a name="locators"></a>Lokalizatory
[Lokalizator](/rest/api/media/operations/locator)s udostępnia punkt wejścia, aby uzyskać dostęp do plików znajdujących się w elemencie zawartości. Zasady dostępu są używane do definiowania uprawnień i czasu trwania, do którego klient ma dostęp do danego elementu zawartości. Lokalizatory mogą mieć relację wiele do jednego z zasadami dostępu, dzięki czemu różne lokalizatory mogą podawać różne czasy rozpoczęcia i typy połączeń różnym klientom przy użyciu tych samych ustawień uprawnień i czasu trwania. Jednak ze względu na ograniczenie zasad dostępu współdzielonego ustawione przez usługi Azure Storage nie można jednocześnie mieć więcej niż pięciu unikatowych lokalizatorów skojarzonych z danym elementem zawartości. 

Media Services obsługuje dwa typy lokalizatorów: OnDemandOrigin Locators, używany do przesyłania strumieniowego multimediów (na przykład MPEG PAUZy, HLS lub Smooth Streaming) lub progresywnego pobierania lokalizatorów adresów URL multimediów i sygnatur dostępu współdzielonego, służących do przekazywania lub pobierania plików multimedialnych to\from Azure Storage. 

>[!NOTE]
>Uprawnienia do listy (AccessPermissions. list) nie należy używać podczas tworzenia lokalizatora OnDemandOrigin. 

### <a name="storage-account"></a>Konto magazynu
Cały dostęp do usługi Azure Storage odbywa się za pomocą konta magazynu. Konto usługi multimediów może być skojarzone z co najmniej jednym kontem magazynu. Konto może zawierać nieograniczoną liczbę kontenerów, pod warunkiem, że ich łączny rozmiar jest objęty 500 TB na konto magazynu.  Media Services udostępnia narzędzia poziomu zestawu SDK umożliwiające zarządzanie wieloma kontami magazynu i równoważenie obciążenia dystrybucji zasobów podczas przekazywania ich do tych kont na podstawie metryk lub dystrybucji losowej. Aby uzyskać więcej informacji, zobacz Praca z [usługą Azure Storage](/previous-versions/azure/dn767951(v=azure.100)). 

## <a name="jobs-and-tasks"></a>Zadania
[Zadanie](/rest/api/media/operations/job) jest zwykle używane do przetwarzania (na przykład indeksowania lub kodowania) jednej prezentacji audio/wideo. Jeśli przetwarzasz wiele filmów wideo, Utwórz zadanie dla każdego wideo, które ma zostać zakodowane.

Zadanie zawiera metadane dotyczące przetwarzania, które ma zostać wykonane. Każde zadanie zawiera co najmniej jedno [zadanie](/rest/api/media/operations/task)s określające zadanie przetwarzania niepodzielnego, jego zasoby wejściowe, zasoby wyjściowe, procesor multimediów i powiązane z nim ustawienia. Zadania w ramach zadania mogą być łańcucha ze sobą, gdzie element zawartości wyjściowej jednego zadania jest podawany jako zasób wejściowy do następnego zadania. W ten sposób jedno zadanie może zawierać wszystkie procesy wymagane do prezentacji multimedialnej.

## <a name="encoding"></a><a id="encoding"></a>Encoding
Azure Media Services udostępnia wiele opcji kodowania multimediów w chmurze.

Podczas rozpoczynania pracy z Media Services należy zrozumieć różnicę między koderami-dekodermi i formatami plików.
Kodery-dekoder to oprogramowanie implementujące algorytmy kompresji/dekompresji, natomiast formaty plików są kontenerami, które przechowują skompresowane wideo.

Media Services zapewnia dynamiczne pakowanie, które pozwala na dostarczanie zawartości kodowanej przy użyciu plików MP4 lub Smooth Streaming w formatach strumieniowych obsługiwanych przez Media Services (PAUZy MPEG, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formatach.

Aby korzystać z funkcji [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md), należy zakodować plik Mezzanine (Source) do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów lub z adaptacyjną szybkością transmisji bitów Smooth Streaming plików i mieć co najmniej jeden punkt końcowy przesyłania strumieniowego w warstwie Standardowa lub Premium.

Media Services obsługuje następujący koder na żądanie, który został opisany w tym artykule:

* [Usługa Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Aby uzyskać informacje na temat tego obsługiwanego kodera, zobacz [koder](media-services-encode-asset.md).

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo
W Azure Media Services kanał reprezentuje potok służący do przetwarzania zawartości przesyłania strumieniowego na żywo. Kanał odbiera strumienie wejściowe na żywo na jeden z dwóch sposobów:

* Lokalny koder na żywo wysyła do kanału protokół RTMP o dużej szybkości transmisji bitów lub Smooth Streaming (pofragmentowany plik MP4). Można użyć następujących koderów na żywo, które wychodzące z wieloszybkościowej transmisji bitów Smooth Streaming: MediaExcel, ATEME, Wyobraź Communications, Envivio, Cisco i element. Następujące kodery na żywo wyprowadzają RTMP: Adobe Flash Live Encoder, [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Teradek, kodery Haivision. Pozyskiwane strumienie przechodzą przez kanały bez dalszego transkodowania i kodowania. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.
* Strumień o pojedynczej szybkości transmisji bitów (w jednym z następujących formatów: RTMP lub Smooth Streaming (fragmentacja MP4)) jest wysyłany do kanału, w którym włączono obsługę kodowania na żywo z Media Services. Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

### <a name="channel"></a>Kanał
W Media Services [kanał](/rest/api/media/operations/channel)s jest odpowiedzialny za przetwarzanie zawartości przesyłania strumieniowego na żywo. Kanał udostępnia wejściowy punkt końcowy (adres URL pozyskiwania), który następnie można przekazać do transkodera na żywo. Kanał odbiera strumienie danych wejściowych na żywo z transkodera dynamicznego i udostępnia je do przesyłania strumieniowego za pośrednictwem co najmniej jednego StreamingEndpointsu. Kanały udostępniają również punkt końcowy (wersja zapoznawcza) służący do wyświetlania podglądu i weryfikowania strumienia przed dalszem przetwarzaniem i dostarczaniem.

Podczas tworzenia kanału można uzyskać adres URL pozyskiwania i adres URL wersji zapoznawczej. Aby uzyskać te adresy URL, kanał nie musi znajdować się w stanie uruchomienia. Gdy wszystko będzie gotowe do rozpoczęcia wypychania danych z transkodera na żywo do kanału, należy uruchomić kanał. Gdy transkoder na żywo zacznie pozyskiwanie danych, można wyświetlić podgląd strumienia.

Każde konto Media Services może zawierać wiele kanałów, wiele programów i wiele StreamingEndpoints. W zależności od potrzeb dotyczących przepustowości i zabezpieczeń usługi StreamingEndpoint Services mogą być dedykowane dla jednego lub kilku kanałów. Każdy StreamingEndpoint może pobierać z dowolnego kanału.

### <a name="program-event"></a>Program (zdarzenie)
[Program (Event)](/rest/api/media/operations/program) umożliwia kontrolowanie publikowania i przechowywania segmentów w strumieniu na żywo. Kanały zarządzają programami (zdarzenia). Relacja kanału i programu jest podobna do tradycyjnego nośnika, gdzie kanał ma stały strumień zawartości, a program jest objęty zakresem pewnego czasu zdarzenia w tym kanale.
Możesz określić liczbę godzin, przez którą ma zostać zachowana zarejestrowana zawartość dla programu, ustawiając właściwość **ArchiveWindowLength** . Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin.

ArchiveWindowLength określa również maksymalną ilość czasu, jaką klienci mogą odszukać w czasie z bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program (zdarzenie) jest skojarzony z elementem zawartości. Aby opublikować program, należy utworzyć lokalizator dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Aby uzyskać więcej informacji, zobacz:

* [Praca z kanałami obsługującymi Live Encoding z Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md)
* [Limity przydziału i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Ochrona zawartości
### <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne
Azure Media Services pozwala zabezpieczyć nośnik od momentu, w którym komputer przejdzie przez magazyn, przetwarzanie i dostarczanie. Media Services pozwala na dostarczanie zawartości szyfrowanych dynamicznie przy użyciu Advanced Encryption Standard (AES) (z użyciem 128-bitowych kluczy szyfrowania) i Common Encryption (CENC) przy użyciu technologii PlayReady i/lub Widevine DRM. Media Services udostępnia również usługę do dostarczania kluczy AES i licencji PlayReady do autoryzowanych klientów.

Obecnie można zaszyfrować następujące formaty przesyłania strumieniowego: HLS, KRESKa MPEG i Smooth Streaming. Nie można zaszyfrować pobrań progresywnych.

Jeśli chcesz, aby Media Services zaszyfrować element zawartości, musisz skojarzyć klucz szyfrowania (CommonEncryption lub EnvelopeEncryption) z zasobem, a także skonfigurować zasady autoryzacji dla klucza.

Jeśli chcesz przesłać strumieniowo zaszyfrowany zasób magazynu, musisz skonfigurować zasady dostarczania zasobów, aby określić, w jaki sposób chcesz dostarczyć zasób.

Gdy gracz prosi o przesłanie strumienia, Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu szyfrowania kopert (z algorytmem AES) lub typowego szyfrowania (z użyciem oprogramowania PlayReady lub Widevine). Aby odszyfrować strumień, gracz wyśle żądanie klucza od usługi dostarczania kluczy. Aby zdecydować, czy użytkownik ma uprawnienia do uzyskiwania klucza, usługa szacuje zasady autoryzacji określone dla klucza.

### <a name="token-restriction"></a>Ograniczenie tokenu
Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń autoryzacji: otwarte, ograniczenie tokenu lub ograniczenie adresów IP. Zasadzie ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę STS (Secure Token Service). Usługa Media Services obsługuje następujące formaty tokenów: SWT (Simple Web Token) i JWT (JSON Web Token). Media Services nie zapewnia usług Secure token Services. Możesz utworzyć niestandardową usługę STS. Usługa STS musi być skonfigurowana w taki sposób, aby utworzyć token podpisany przy użyciu określonego klucza i wystawiać oświadczenia określone w konfiguracji ograniczenia tokenu. Usługa dostarczania kluczy Media Services będzie zwracać żądany klucz (lub licencję) do klienta, jeśli token jest prawidłowy, a oświadczenia w tokenie są zgodne z tymi skonfigurowanymi dla klucza (lub licencji).

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacyjny, wystawcę i parametry odbiorców. Podstawowy klucz weryfikacyjny zawiera klucz, z którym token został podpisany, wystawca to usługa Secure Tokens, która wystawia token. Odbiorcy (czasami nazywane zakresem) opisują zamiar tokenu lub zasobu, do którego token autoryzuje dostęp. Usługa dostarczania kluczy Media Services sprawdza, czy te wartości w tokenie pasują do wartości w szablonie.

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- [Omówienie ochrony zawartości](media-services-content-protection-overview.md)
- [Ochrona za pomocą algorytmu AES-128](media-services-protect-with-aes128.md)
- [Ochrona za pomocą oprogramowania PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Wykonania
### <a name="dynamic-packaging"></a><a name="dynamic_packaging"></a>Dynamiczne tworzenie pakietów
Podczas pracy z Media Services zaleca się zakodowanie plików Mezzanine do zestawu MP4 o adaptacyjnej szybkości transmisji bitów, a następnie przekonwertowanie zestawu na żądany format przy użyciu [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego
StreamingEndpoint reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub Content Delivery Network (CDN) w celu dalszej dystrybucji (Azure Media Services zapewnia teraz integrację Azure CDN). Strumień wychodzący z usługi punktu końcowego przesyłania strumieniowego może być strumieniem na żywo lub zasobem wideo na żądanie na koncie Media Services. Klienci platformy Media Services wybierają **standardowy** punkt końcowy przesyłania strumieniowego lub co najmniej jeden punkt końcowy przesyłania strumieniowego **Premium**, zgodnie ze swoimi potrzebami. Standardowy punkt końcowy przesyłania strumieniowego jest odpowiedni dla większości obciążeń przesyłania strumieniowego. 

Standardowy punkt końcowy przesyłania strumieniowego jest odpowiedni dla większości obciążeń przesyłania strumieniowego. Standardowe punkty końcowe przesyłania strumieniowego zapewniają elastyczność dostarczania zawartości do niemal każdego urządzenia poprzez dynamiczne Pakowanie na HLS, MPEG-KRESKę i Smooth Streaming, a także dynamiczne szyfrowanie dla programów Microsoft PlayReady, Google Widevine, Apple FairPlay i AES128.  Są one także skalowane od bardzo małych i bardzo dużych odbiorców z tysiącami równoczesnych podglądów za pośrednictwem integracji Azure CDN. Jeśli masz zaawansowane obciążenie lub wymagania dotyczące pojemności przesyłania strumieniowego nie pasują do standardowych docelowych przepływów pracy przesyłania strumieniowego lub chcesz kontrolować pojemność usługi StreamingEndpoint w celu obsługi rosnących potrzeb dotyczących przepustowości, zaleca się przydzielenie jednostek skalowania (nazywanych również jednostkami przesyłania strumieniowego Premium).

Zaleca się użycie dynamicznego pakowania i/lub szyfrowania dynamicznego.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Aby uzyskać więcej informacji, zobacz [ten](media-services-portal-manage-streaming-endpoints.md) temat.

Domyślnie można mieć maksymalnie 2 punkty końcowe przesyłania strumieniowego na koncie Media Services. Aby zażądać wyższego limitu, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).

Opłaty są naliczane tylko wtedy, gdy StreamingEndpoint jest w stanie uruchomienia.

### <a name="asset-delivery-policy"></a>Zasady dostarczania zasobów
Jednym z kroków w przepływie pracy Media Services dostarczania zawartości jest skonfigurowanie [zasad dostarczania dla zasobów](/rest/api/media/operations/assetdeliverypolicy), które mają być przesyłane strumieniowo. Zasady dostarczania elementów zawartości mówią, Media Services jak chcesz, aby zasób został dostarczony: w jakim protokole przesyłania strumieniowego powinien być dynamicznie spakowany pakiet zawartości (na przykład MPEG PAUZy, HLS, Smooth Streaming lub wszystkie), czy chcesz dynamicznie szyfrować element zawartości i jak (koperta lub typowe szyfrowanie).

Jeśli do przesyłania strumieniowego zasobów jest zaszyfrowana składowa, serwer przesyłania strumieniowego usunie szyfrowanie magazynu i strumieniuje zawartość przy użyciu określonych zasad dostarczania. Na przykład, aby dostarczyć zasób szyfrowany przy użyciu klucza szyfrowania Advanced Encryption Standard (AES), ustaw typ zasad na DynamicEnvelopeEncryption. Aby usunąć szyfrowanie magazynu i przesłać strumieniowo element zawartości, ustaw dla opcji Typ zasad wartość NoDynamicEncryption.

### <a name="progressive-download"></a>Pobieranie progresywne
Pobieranie stopniowe pozwala rozpocząć odtwarzanie multimediów przed pobraniem całego pliku. Plik MP4 można pobrać progresywnie.

>[!NOTE]
>Należy odszyfrować zaszyfrowane zasoby, jeśli chcesz, aby były dostępne do pobrania progresywnego.

Aby zapewnić użytkownikom adresy URL pobierania progresywnego, należy najpierw utworzyć lokalizator OnDemandOrigin. Utworzenie lokalizatora zapewnia podstawową ścieżkę do elementu zawartości. Następnie należy dołączyć nazwę pliku MP4. Na przykład:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

### <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Przesyłanie strumieniowe zawartości do klientów. Aby zapewnić użytkownikom adresy URL przesyłania strumieniowego, należy najpierw utworzyć lokalizator OnDemandOrigin. Utworzenie lokalizatora zapewnia podstawową ścieżkę do elementu zawartości, który zawiera zawartość, którą chcesz przesłać strumieniowo. Jednak aby można było przesyłać strumieniowo tę zawartość, należy również zmodyfikować tę ścieżkę. Aby utworzyć pełny adres URL pliku manifestu przesyłania strumieniowego, należy połączyć wartość ścieżki lokalizatora i nazwę pliku manifestu (filename. ISM). Następnie Dołącz/manifest i odpowiedni format (w razie potrzeby) do ścieżki lokalizatora.

Możesz również przesyłać strumieniowo zawartość za pośrednictwem połączenia TLS. Aby to zrobić, upewnij się, że adresy URL przesyłania strumieniowego zaczynają się od protokołu HTTPS. Obecnie AMS nie obsługuje protokołu TLS z domenami niestandardowymi.  

>[!NOTE]
>Można przesyłać strumieniowo tylko przy użyciu protokołu TLS, jeśli punkt końcowy przesyłania strumieniowego, z którego dostarczasz zawartość, został utworzony po 10 września 2014. Jeśli adresy URL przesyłania strumieniowego są oparte na punktach końcowych przesyłania strumieniowego utworzonych po 10 września, adres URL zawiera "streaming.mediaservices.windows.net" (nowy format). Adresy URL przesyłania strumieniowego zawierające "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu TLS. Jeśli Twój adres URL ma stary format i chcesz mieć możliwość przesyłania strumieniowego przy użyciu protokołu TLS, Utwórz nowy punkt końcowy przesyłania strumieniowego. Użyj adresów URL utworzonych na podstawie nowego punktu końcowego przesyłania strumieniowego, aby przesyłać strumieniowo zawartość za pośrednictwem protokołu TLS.

Na poniższej liście opisano różne formaty przesyłania strumieniowego i przedstawiono przykłady:

* Smooth Streaming

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

* MPEG DASH

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = MPD-czas-CSF)

* Apple HTTP Live Streaming (HLS) v4

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL)

* Apple HTTP Live Streaming (HLS) v3

{Nazwa punktu końcowego przesyłania strumieniowego — nazwa konta usługi Media Services}. Streaming. MediaServices. Windows. NET/{Locator ID}/{Nazwa pliku (format = M3U8-AAPL-v3)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL-v3)

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
