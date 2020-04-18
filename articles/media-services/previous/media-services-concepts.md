---
title: Pojęcia dotyczące usługi Azure Media Services | Dokumenty firmy Microsoft
description: Ten artykuł zawiera krótkie omówienie koncepcji usługi Microsoft Azure Media Services i łącza do innych artykułów, aby uzyskać szczegółowe informacje.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 44d6776a367a5cb1eeb86c955f083598d417aba6
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641665"
---
# <a name="azure-media-services-concepts"></a>Pojęcia dotyczące usługi Azure Media Services 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

W tym temacie przedstawiono omówienie najważniejszych pojęć usługi Media Services.

## <a name="assets-and-storage"></a><a id="assets"/>Zasoby i magazynowanie
### <a name="assets"></a>Elementy zawartości
[Zasób](https://docs.microsoft.com/rest/api/media/operations/asset) zawiera pliki cyfrowe (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki z podpisami kodowanymi) oraz metadane dotyczące tych plików. Po przekazaniu plików cyfrowych do zasobu mogą być używane w przepływach pracy kodowania i przesyłania strumieniowego usługi Media Services.

Zasób jest mapowany do kontenera obiektów blob na koncie usługi Azure Storage, a pliki w zasobie są przechowywane jako blokowe obiekty blob w tym kontenerze. Obiekty BLOB strony nie są obsługiwane przez usługę Azure Media Services.

Przy podejmowaniu decyzji o treści multimedialnej do przekazywania i przechowywania w zasobie stosuje się następujące kwestie:

* Zasób powinien zawierać tylko jedno, unikatowe wystąpienie zawartości multimedialnej. Na przykład pojedyncza edycja odcinka telewizyjnego, filmu lub reklamy.
* Zasób nie powinien zawierać wielu wersji ani edycji pliku audiowizualnego. Jednym z przykładów niewłaściwego użycia zasobu może być próba zapisania więcej niż jednego odcinka telewizyjnego, reklamy lub wielu kątów kamery z jednej produkcji wewnątrz zasobu. Przechowywanie wielu wersji lub edycji pliku audiowizualnego w zasobie może spowodować trudności z przesyłaniem zadań kodowania, przesyłaniem strumieniowym i zabezpieczaniem dostarczania zasobu w późniejszym toku przepływu pracy.  

### <a name="asset-file"></a>Plik zasobu
[Plik zasobów](https://docs.microsoft.com/rest/api/media/operations/assetfile) reprezentuje rzeczywisty plik wideo lub audio, który jest przechowywany w kontenerze obiektów blob. Plik zasobu jest zawsze skojarzony z zasobem, a zasób może zawierać jeden lub wiele plików. Zadanie kodera usługi Media Services kończy się niepowodzeniem, jeśli obiekt pliku zasobu nie jest skojarzony z plikiem cyfrowym w kontenerze obiektów blob.

**AssetFile wystąpienie** i rzeczywisty plik multimedialny są dwa odrębne obiekty. AssetFile wystąpienie zawiera metadane dotyczące pliku multimedialnego, podczas gdy plik multimedialny zawiera rzeczywistą zawartość multimedialną.

Nie należy podejmować prób zmiany zawartości kontenerów obiektów blob, które zostały wygenerowane przez usługę Media Services bez użycia interfejsów API usługi Media Service.

### <a name="asset-encryption-options"></a>Opcje szyfrowania zasobów
W zależności od typu zawartości, którą chcesz przekazać, przechowywać i dostarczać, program Media Services udostępnia różne opcje szyfrowania, które można wybrać.

>[!NOTE]
>Szyfrowanie nie jest używane. Jest to wartość domyślna. Podczas korzystania z tej opcji zawartość nie jest chroniona podczas przesyłania lub przechowywania w magazynie.

Jeśli planujesz dostarczyć mp4 przy użyciu pobierania progresywnego, użyj tej opcji, aby przesłać zawartość.

**StorageEncrypted** — ta opcja służy do szyfrowania wyczyszczonych zawartości lokalnie przy użyciu szyfrowania 256-bitowego AES, a następnie przekazywania jej do usługi Azure Storage, gdzie jest przechowywana zaszyfrowana w spoczynku. Zasoby chronione szyfrowaniem magazynu są automatycznie niezaszyfrowane i umieszczane w zaszyfrowanym systemie plików przed kodowaniem i opcjonalnie ponownie szyfrowane przed przekazaniem z powrotem jako nowego zasobu wyjściowego. Podstawowym przypadkiem użycia szyfrowania magazynu jest, gdy chcesz zabezpieczyć pliki multimedialne wysokiej jakości z silnym szyfrowaniem w spoczynku na dysku. 

Aby dostarczyć zaszyfrowany zasób magazynu, należy skonfigurować zasady dostarczania zasobu, aby program Media Services wiedział, jak chcesz dostarczać zawartość. Przed przesyłaniem strumieniowego zasobu serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu określonych zasad dostarczania (na przykład AES, PlayReady lub bez szyfrowania). 

**CommonEncryptionProtected** - Użyj tej opcji, jeśli chcesz szyfrować (lub przekazać już zaszyfrowaną) zawartość za pomocą wspólnego szyfrowania lub PlayReady DRM (na przykład Smooth Streaming chronione PlayReady DRM).

**EnvelopeEncryptionProtected** — użyj tej opcji, jeśli chcesz chronić (lub przekazać już chronione) HTTP Live Streaming (HLS) zaszyfrowane za pomocą advanced encryption standard (AES). Jeśli przesyłasz hls już zaszyfrowane za pomocą AES, musi być zaszyfrowany przez Transform Manager.

### <a name="access-policy"></a>Zasady dostępu
[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) definiuje uprawnienia (takie jak odczyt, zapis i lista) i czas trwania dostępu do zasobu. Zwykle należy przekazać AccessPolicy obiektu do lokalizatora, który następnie będzie używany do dostępu do plików zawartych w zasobie.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) temat.

### <a name="blob-container"></a>Kontener obiektów blob
Kontener obiektu blob zapewnia grupowanie zestawu obiektów blob. Kontenery obiektów blob są używane w usłudze Media Services jako punkt granic dla kontroli dostępu i lokalizatorów sygnatury dostępu współdzielonego (SAS) w zasobach. Konto usługi Azure Storage może zawierać nieograniczoną liczbę kontenerów obiektów blob. Kontener może przechowywać nieograniczoną liczbę obiektów blob.

>[!NOTE]
> Nie należy podejmować prób zmiany zawartości kontenerów obiektów blob, które zostały wygenerowane przez usługę Media Services bez użycia interfejsów API usługi Media Service.
> 
> 

### <a name="locators"></a><a id="locators"/>Locators
[Lokalizator](https://docs.microsoft.com/rest/api/media/operations/locator)s zapewniają punkt wejścia, aby uzyskać dostęp do plików zawartych w zasobie. Zasady dostępu są używane do definiowania uprawnień i czasu trwania, które klient ma dostęp do danego zasobu. Lokalizatory mogą mieć wiele do jednego relacji z zasadami dostępu, tak, że różne lokalizatory mogą zapewnić różne godziny rozpoczęcia i typy połączeń do różnych klientów, podczas gdy wszystkie przy użyciu tych samych ustawień uprawnień i czasu trwania; Jednak ze względu na ograniczenie zasad dostępu współdzielonego ustawionego przez usługi magazynu platformy Azure nie może mieć więcej niż pięć unikatowych lokalizatorów skojarzonych z danym zasobem w tym czasie. 

Usługa Media Services obsługuje dwa typy lokalizatorów: lokalizatory OnDemandOrigin, używane do przesyłania strumieniowego multimediów (na przykład MPEG DASH, HLS lub Smooth Streaming) lub stopniowe pobieranie lokalizatorów multimediów i adresów URL sygnatury dostępu Współdzielonego, używane do przekazywania lub pobierania plików multimedialnych do\z magazynu platformy Azure. 

>[!NOTE]
>Uprawnienie listy (AccessPermissions.List) nie powinny być używane podczas tworzenia lokalizatora OnDemandOrigin. 

### <a name="storage-account"></a>Konto magazynu
Cały dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu. Konto usługi media może być skojarzone z co najmniej jednym kontem magazynu. Konto może zawierać nieograniczoną liczbę kontenerów, o ile ich całkowity rozmiar jest poniżej 500 TB na konto magazynu.  Usługi Media Services zapewniają narzędzia na poziomie zestawu SDK, aby umożliwić zarządzanie wieloma kontami magazynu i równoważenie obciążenia dystrybucji zasobów podczas przekazywania na te konta na podstawie metryk lub losowej dystrybucji. Aby uzyskać więcej informacji, zobacz Praca z [usługą Azure Storage](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## <a name="jobs-and-tasks"></a>Zadania i zadania
Zadanie [job](https://docs.microsoft.com/rest/api/media/operations/job) jest zwykle używane do przetwarzania (na przykład indeksu lub kodowania) jednej prezentacji audio/wideo. Jeśli przetwarzasz wiele filmów, utwórz zadanie dla każdego filmu, który ma być zakodowany.

Zadanie zawiera metadane dotyczące przetwarzania do wykonania. Każde zadanie zawiera jedno lub więcej [zadań,](https://docs.microsoft.com/rest/api/media/operations/task)które określają zadanie przetwarzania atomowego, jego zasoby wejściowe, zasoby wyjściowe, procesor nośników i skojarzone z nim ustawienia. Zadania w zadaniu mogą być połączone ze sobą, gdzie zasób wyjściowy jednego zadania jest podawany jako zasób wejściowy do następnego zadania. W ten sposób jedno zadanie może zawierać wszystkie przetwarzanie niezbędne do prezentacji multimedialnej.

## <a name="encoding"></a><a id="encoding"></a>Kodowanie
Usługa Azure Media Services udostępnia wiele opcji kodowania multimediów w chmurze.

Podczas rozpoczynania pracy z usługą Media Services, ważne jest, aby zrozumieć różnicę między kodeków i formatów plików.
Kodeki to oprogramowanie, które implementuje algorytmy kompresji/dekompresji, podczas gdy formaty plików są kontenerami, które przechowują skompresowany film.

Usługa Media Services zapewnia dynamiczne pakowanie, które umożliwia dostarczanie adaptacyjnej szybkości transmisji bitów MP4 lub zawartości zakodowanej w trybie smooth streaming w formatach przesyłania strumieniowego obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming) bez konieczności ponownego pakowania do tych formatów przesyłania strumieniowego.

Aby skorzystać z [dynamicznego pakowania,](media-services-dynamic-packaging-overview.md)musisz zakodować plik antresoli (źródłowej) w zestaw adaptacyjnych plików MP4 o szybkości transmisji bitów lub adaptacyjnych plikach Płynnego przesyłania strumieniowego i mieć co najmniej jeden standardowy lub premium punkt końcowy przesyłania strumieniowego w stanie uruchomionym.

Usługi Media Services obsługują następujące kodery na żądanie, które są opisane w tym artykule:

* [Usługa Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Przepływ pracy usługi Media Encoder w warstwie Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Aby uzyskać informacje na temat obsługiwanych koderów, zobacz [Kodery](media-services-encode-asset.md).

## <a name="live-streaming"></a>Transmisja strumieniowa na żywo
W usłudze Azure Media Services kanał reprezentuje potok do przetwarzania zawartości przesyłania strumieniowego na żywo. Kanał odbiera transmisje wejściowe na żywo na jeden z dwóch sposobów:

* Lokalny koder na żywo wysyła do kanału wiele bitów RTMP lub Płynne przesyłanie strumieniowe (Fragmented MP4). Można użyć następujących koderów na żywo, które wyprowadzają wielosytowe płynne przesyłanie strumieniowe: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco i Elemental. Następujące kodery na żywo wyjściowe RTMP: Adobe Flash Live Encoder, [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Teradek, Haivision encoders. Pozyskane strumienie przechodzą przez kanały bez dalszego transkodowania i kodowania. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.
* Pojedynczy strumień szybkości transmisji bitów (w jednym z następujących formatów: RTMP lub Smooth Streaming (Fragmented MP4)) jest wysyłany do kanału, który jest włączony do wykonywania kodowania na żywo za pomocą usługi Media Services. Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

### <a name="channel"></a>Channel
W umywce usługi Media Services [kanały](https://docs.microsoft.com/rest/api/media/operations/channel)są odpowiedzialne za przetwarzanie zawartości przesyłanych strumieniowo na żywo. Kanał zawiera wejściowy punkt końcowy (adres URL pozyskiwania), który następnie należy podać transkoderowi na żywo. Kanał odbiera transmisje wejściowe na żywo z transkodera na żywo i udostępnia go do przesyłania strumieniowego za pośrednictwem jednego lub więcej Punktów StreamingEndpoints. Kanały zapewniają również punkt końcowy w wersji zapoznawczej (adres URL w wersji zapoznawczej), którego używasz do wyświetlania podglądu i sprawdzania poprawności strumienia przed dalszym przetwarzaniem i dostarczaniem.

Podczas tworzenia kanału możesz uzyskać adres URL pozyskiwania i adres URL podglądu. Aby uzyskać te adresy URL, kanał nie musi być w stanie uruchomionym. Gdy będziesz gotowy, aby rozpocząć wypychanie danych z transkodera na żywo do kanału, kanał musi zostać uruchomiony. Gdy transkoder na żywo zacznie pojejeć dane, możesz wyświetlić podgląd strumienia.

Każde konto usługi Media Services może zawierać wiele kanałów, wiele programów i wiele punktów StreamingEndpoints. W zależności od potrzeb przepustowości i zabezpieczeń usługi StreamingEndpoint mogą być dedykowane do jednego lub więcej kanałów. Każdy StreamingEndpoint można wyciągnąć z dowolnego kanału.

### <a name="program-event"></a>Program (wydarzenie)
[Program (zdarzenie)](https://docs.microsoft.com/rest/api/media/operations/program) umożliwia kontrolowanie publikowania i przechowywania segmentów w strumieniu na żywo. Kanały zarządzają programami (zdarzeniami). Relacja Kanał i program jest podobna do tradycyjnych mediów, w których kanał ma stały strumień zawartości, a program jest ograniczony do pewnego zdarzenia czasowego na tym kanale.
Można określić liczbę godzin przechowywania zarejestrowanej zawartości programu, ustawiając właściwość **ArchiveWindowLength.** Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin.

ArchiveWindowLength dyktuje również maksymalną ilość czasu, przez jaki klienci mogą szukać w czasie z bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program (zdarzenie) jest skojarzony z zasobem. Aby opublikować program, należy utworzyć lokalizator dla skojarzonego zasobu. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Aby uzyskać więcej informacji, zobacz:

* [Praca z kanałami, które są włączone do wykonywania kodowania na żywo za pomocą usługi Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Ochrona zawartości
### <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne
Usługa Azure Media Services umożliwia zabezpieczenie multimediów od momentu opuszczenia komputera przez magazyn, przetwarzanie i dostarczanie. Usługa Media Services umożliwia dynamiczne dostarczanie zawartości za pomocą zaawansowanego standardu szyfrowania (AES) (przy użyciu 128-bitowych kluczy szyfrowania) i wspólnego szyfrowania (CENC) przy użyciu funkcji PlayReady i/lub Widevine DRM. Usługa Media Services zapewnia również usługę dostarczania kluczy AES i licencji PlayReady autoryzowanym klientom.

Obecnie można szyfrować następujące formaty przesyłania strumieniowego: HLS, MPEG DASH i Smooth Streaming. Nie można szyfrować pobierania progresywnego.

Jeśli program Media Services ma szyfrować zasób, należy skojarzyć klucz szyfrowania (CommonEncryption lub EnvelopeEncryption) z zasobem, a także skonfigurować zasady autoryzacji dla klucza.

Jeśli chcesz przesyłać strumieniowo zaszyfrowany zasób magazynu, musisz skonfigurować zasady dostarczania zasobu, aby określić sposób dostarczania zasobu.

Gdy odtwarzacz zażąda strumienia, usługa Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu szyfrowania kopert (z AES) lub wspólnego szyfrowania (z PlayReady lub Widevine). Aby odszyfrować strumień, gracz zażąda klucza z usługi dostarczania kluczy. Aby zdecydować, czy użytkownik jest autoryzowany do uzyskania klucza, usługa ocenia zasady autoryzacji określone dla klucza.

### <a name="token-restriction"></a>Ograniczenie tokenu
Zasady autoryzacji klucza zawartości mogą mieć co najmniej jedno ograniczenie autoryzacji: otwarte, ograniczenie tokenu lub ograniczenie adresu IP. Zasadzie ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę STS (Secure Token Service). Usługa Media Services obsługuje następujące formaty tokenów: SWT (Simple Web Token) i JWT (JSON Web Token). Usługi Media Services nie zapewniają usług bezpiecznego tokenu. Można utworzyć niestandardowy STS. Sts musi być skonfigurowany do tworzenia tokenu podpisanego przy określonym kluczu i oświadczeń problem, które zostały określone w konfiguracji ograniczenia tokenu. Usługa dostarczania kluczy usługi Media Services zwróci żądany klucz (lub licencję) do klienta, jeśli token jest prawidłowy, a oświadczenia w tokenie są zgodne z tymi skonfigurowanym dla klucza (lub licencji).

Podczas konfigurowania zasad z ograniczeniami tokenu należy określić podstawowy klucz weryfikacji, parametry wystawcy i odbiorcy. Podstawowy klucz weryfikacji zawiera klucz, który token został podpisany z, wystawca jest usługa bezpiecznego tokenu, który wystawia token. Grupa odbiorców (czasami nazywana zakresem) opisuje intencję tokenu lub zasobu, do który token autoryzuje dostęp. Usługa dostarczania kluczy usługi Media Services sprawdza, czy te wartości w tokenie są zgodne z wartościami w szablonie.

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Omówienie ochrony zawartości](media-services-content-protection-overview.md)
- [Ochrona za pomocą AES-128](media-services-protect-with-aes128.md)
- [Chroń za pomocą PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Dostarczanie
### <a name="dynamic-packaging"></a><a id="dynamic_packaging"/>Dynamiczne tworzenie pakietów
Podczas pracy z usługą Media Services zaleca się zakodowanie plików z antresoli w adaptacyjny zestaw MP4 z szybkością transmisji bitów, a następnie przekonwertowanie zestawu na żądany format za pomocą [dynamicznego pakowania](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego
Punkt przesyłania strumieniowego reprezentuje usługę przesyłania strumieniowego, która może dostarczać zawartość bezpośrednio do aplikacji odtwarzacza klienckiego lub do sieci dostarczania zawartości (CDN) w celu dalszej dystrybucji (usługa Azure Media Services zapewnia teraz integrację usługi Azure CDN). Strumień wychodzący z usługi punktu końcowego przesyłania strumieniowego może być strumieniem na żywo lub zasobem wideo na żądanie na koncie usługi Media Services. Klienci platformy Media Services wybierają **standardowy** punkt końcowy przesyłania strumieniowego lub co najmniej jeden punkt końcowy przesyłania strumieniowego **Premium**, zgodnie ze swoimi potrzebami. Standardowy punkt końcowy przesyłania strumieniowego jest odpowiedni dla większości obciążeń przesyłania strumieniowego. 

Standardowy punkt końcowy przesyłania strumieniowego jest odpowiedni dla większości obciążeń przesyłania strumieniowego. Standardowe punkty końcowe przesyłania strumieniowego oferują elastyczność dostarczania zawartości do praktycznie każdego urządzenia za pomocą dynamicznego pakowania w HLS, MPEG-DASH i Smooth Streaming, a także dynamiczne szyfrowanie dla firm Microsoft PlayReady, Google Widevine, Apple Fairplay i AES128.  Skalują się również od bardzo małych do bardzo dużych odbiorców z tysiącami równoczesnych widzów za pośrednictwem integracji usługi Azure CDN. Jeśli masz zaawansowane obciążenie lub wymagania dotyczące pojemności przesyłania strumieniowego nie pasują do standardowych docelowych przepływności punktu końcowego przesyłania strumieniowego lub chcesz kontrolować pojemność usługi StreamingEndpoint do obsługi rosnących potrzeb przepustowości, zaleca się przydzielenie jednostek skalowania (znanych również jako jednostki przesyłania strumieniowego premium).

Zaleca się stosowanie dynamicznego pakowania i/lub szyfrowania dynamicznego.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Aby uzyskać więcej informacji, zobacz [ten](media-services-portal-manage-streaming-endpoints.md) temat.

Domyślnie na koncie usługi Media Services mogą być maksymalnie 2 punkty końcowe przesyłania strumieniowego. Aby zażądać wyższego limitu, zobacz [Przydziały i ograniczenia](media-services-quotas-and-limitations.md).

Naliczane są tylko wtedy, gdy punkt przesyłania strumieniowego jest w stanie uruchomionym.

### <a name="asset-delivery-policy"></a>Zasady dostarczania zasobów
Jednym z kroków w przepływie pracy dostarczania zawartości usługi Media Services jest konfigurowanie [zasad dostarczania dla zasobów,](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)które mają być przesyłane strumieniowo. Zasady dostarczania zasobów informują usługę Media Services, w jaki sposób ma być dostarczany zasób: do którego protokołu przesyłania strumieniowego powinien być dynamicznie pakowany (na przykład MPEG DASH, HLS, Smooth Streaming lub wszystkie), niezależnie od tego, czy chcesz dynamicznie szyfrować zasób i jak (koperta lub wspólne szyfrowanie).

Jeśli masz zasób zaszyfrowany magazyn, przed przesyłaniem strumieniowego zasobu serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i przesyła strumieniowo zawartość przy użyciu określonych zasad dostarczania. Na przykład, aby dostarczyć zasób zaszyfrowany za pomocą klucza szyfrowania Advanced Encryption Standard (AES), ustaw typ zasad na DynamicEnvelopeEncryption. Aby usunąć szyfrowanie magazynu i przesłać strumieniowo zasób w wyczyszcz, ustaw typ zasad na NoDynamicEncryption.

### <a name="progressive-download"></a>Pobieranie progresywne
Pobieranie progresywne umożliwia rozpoczęcie odtwarzania multimediów przed pobraniem całego pliku. Plik MP4 można pobierać tylko stopniowo.

>[!NOTE]
>Musisz odszyfrować zaszyfrowane zasoby, jeśli chcesz, aby były dostępne do stopniowego pobierania.

Aby zapewnić użytkownikom progresywne adresy URL pobierania, należy najpierw utworzyć lokalizator OnDemandOrigin. Tworzenie lokalizatora, daje podstawową ścieżkę do zasobu. Następnie należy dołączyć nazwę pliku MP4. Przykład:

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Przesyłanie strumieniowe zawartości do klientów. Aby zapewnić użytkownikom adresy URL przesyłania strumieniowego, należy najpierw utworzyć lokalizator OnDemandOrigin. Tworzenie lokalizatora, daje podstawową ścieżkę do zasobu, który zawiera zawartość, którą chcesz przesyłać strumieniowo. Jednak aby móc przesyłać strumieniowo tę zawartość, należy dalej modyfikować tę ścieżkę. Aby utworzyć pełny adres URL do pliku manifestu przesyłania strumieniowego, należy połączyć wartość ścieżki lokalizatora i nazwę pliku manifestu (nazwa pliku filename.ism). Następnie dołącz /Manifest i odpowiedni format (w razie potrzeby) do ścieżki lokalizatora.

Można również przesyłać strumieniowo zawartość za pomocą połączenia TLS. Aby to zrobić, upewnij się, że adresy URL przesyłania strumieniowego zaczynają się od protokołu HTTPS. Obecnie usługa AMS nie obsługuje protokołu TLS z domenami niestandardowymi.  

>[!NOTE]
>Można przesyłać strumieniowo za pomocą protokołu TLS tylko wtedy, gdy punkt końcowy przesyłania strumieniowego, z którego dostarczasz zawartość, został utworzony po 10 września 2014 r. Jeśli adresy URL przesyłania strumieniowego są oparte na punktach końcowych przesyłania strumieniowego utworzonych po 10 września, adres URL zawiera "streaming.mediaservices.windows.net" (nowy format). Adresy URL przesyłania strumieniowego, które zawierają "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu TLS. Jeśli adres URL jest w starym formacie i chcesz mieć możliwość przesyłania strumieniowego za pomocą protokołu TLS, utwórz nowy punkt końcowy przesyłania strumieniowego. Użyj adresów URL utworzonych na podstawie nowego punktu końcowego przesyłania strumieniowego, aby przesyłać strumieniowo zawartość za pomocą protokołu TLS.

Na poniższej liście opisano różne formaty przesyłania strumieniowego i podano przykłady:

* Smooth Streaming

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP Transmisja na żywo (HLS) v4

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP Transmisja na żywo (HLS) v3

{nazwa konta usług końcowych przesyłania strumieniowego}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

