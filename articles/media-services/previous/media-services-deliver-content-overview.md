---
title: Dostarczanie zawartości klientom
description: Ten temat zawiera omówienie tego, co jest związane z dostarczaniem zawartości przy użyciu Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 363bb5389eda8336d04b1ff457f8246e89a042d8
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014396"
---
# <a name="deliver-content-to-customers"></a>Dostarczanie zawartości klientom

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Gdy dostarczasz klientom zawartość przesyłania strumieniowego lub wideo na żądanie, celem jest dostarczanie wysokiej jakości wideo do różnych urządzeń w różnych warunkach sieciowych.

Aby osiągnąć ten cel, możesz:

* Koduj strumień do strumienia wideo o wysokiej szybkości transmisji bitów (adaptacyjnej szybkości transmisji bitów). Zajmie to jakość i warunki sieciowe.
* Użyj Microsoft Azure Media Services [pakietów dynamicznych](media-services-dynamic-packaging-overview.md) , aby dynamicznie ponownie spakować strumień do różnych protokołów. Zajmie to przesyłanie strumieniowe na różnych urządzeniach. Media Services obsługuje dostarczanie następujących technologii przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów: <br/>
    * **Http Live Streaming** (HLS) — Dodaj ścieżkę "(format = M3U8-AAPL)" do części "/manifest" adresu URL, aby poinformować serwer pochodzenia przesyłania strumieniowego o konieczności przywrócenia zawartości HLS do użycia na urządzeniach natywnych **Apple iOS** (Aby uzyskać szczegółowe informacje, zobacz [lokalizatory](#locators) i [adresy URL](#URLs)).
    * **MPEG-kreska** — Dodaj ścieżkę "(format = MPD-Time-CSF)" do części "/manifest" adresu URL, aby poinformować serwer pochodzenia przesyłania strumieniowego o zwrotnym formacie MPEG-kreski (Aby uzyskać szczegółowe informacje, zobacz [lokalizatory](#locators) i [adresy URL](#URLs)).
    * **Smooth Streaming**.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

Ten artykuł zawiera omówienie ważnych pojęć związanych z dostarczaniem zawartości.

Aby sprawdzić znane problemy, zobacz [znane problemy](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów
Dzięki pakietowi dynamicznemu, który Media Services zapewnia, można dostarczać zawartość w formacie MP4 lub Smooth Streaming z możliwością transmisji bitów w formatach transmisji strumieniowej obsługiwanych przez Media Services (MPEG-KRESKa, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formatach. Zalecamy dostarczanie zawartości przy użyciu dynamicznego tworzenia pakietów.

Aby skorzystać z funkcji dynamicznego tworzenia pakietów, musisz zakodować plik Mezzanine (Source) do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów lub plików Smooth Streaming z adaptacyjną szybkością transmisji bitów.

Dzięki pakietowi dynamicznemu można przechowywać i regulować pliki w jednym formacie magazynu. Media Services będzie kompilować i obsłużyć odpowiednią odpowiedź w oparciu o Twoje żądania.

Dynamiczne pakowanie jest dostępne dla punktów końcowych przesyłania strumieniowego w warstwach Standardowa i Premium. 

Aby uzyskać więcej informacji, zobacz [dynamiczne pakowanie](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtry i manifesty dynamiczne
Filtry dla zasobów można definiować za pomocą Media Services. Te filtry są reguł po stronie serwera, które ułatwiają klientom wykonywanie takich czynności jak odtwarzanie konkretnej sekcji filmu wideo lub określanie podzestawu plików audio i wideo, które może obsłużyć urządzenie klienta (zamiast wszystkich oddziałów, które są skojarzone z zasobem). To filtrowanie jest realizowane za pośrednictwem *manifestów dynamicznych* , które są tworzone, gdy klient zażąda strumieniowego przesyłania wideo na podstawie jednego lub większej liczby określonych filtrów.

Aby uzyskać więcej informacji, zobacz [filtry i manifesty dynamiczne](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a><a name="locators"></a>Lokalizatory
Aby podać użytkownikowi adres URL, który może służyć do przesyłania strumieniowego lub pobierania zawartości, musisz najpierw opublikować swój zasób przez utworzenie lokalizatora. Lokalizator zapewnia punkt wejścia, aby uzyskać dostęp do plików znajdujących się w elemencie zawartości. Usługa Media Services obsługuje dwa typy lokalizatorów:

* Lokalizatory OnDemandOrigin. Są one używane do przesyłania strumieniowego multimediów (na przykład MPEG-KRESKowego, HLS lub Smooth Streaming) lub progresywnego pobierania plików.
* Lokalizatory adresów URL sygnatury dostępu współdzielonego (SAS). Są one używane do pobierania plików multimedialnych na komputer lokalny.

*Zasady dostępu* są używane do definiowania uprawnień (takich jak Odczyt, zapis i lista) oraz czasu trwania, dla którego klient ma dostęp do określonego zasobu. Należy pamiętać, że uprawnienia listy (AccessPermissions. list) nie należy używać podczas tworzenia lokalizatora OnDemandOrigin.

Lokalizatory mają daty wygaśnięcia. Azure Portal ustawia datę wygaśnięcia 100 lat w przyszłości dla lokalizatorów.

> [!NOTE]
> Jeśli użyto Azure Portal do utworzenia lokalizatorów przed 2015 marca, te lokalizatory zostały ustawione na wygasają po upływie dwóch lat.
> 
> 

Do aktualizacji daty wygaśnięcia na lokalizatorze użyj interfejsu API [REST](/rest/api/media/operations/locator#update_a_locator) lub [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator). Należy pamiętać, że po zaktualizowaniu daty wygaśnięcia lokalizatora SAS następuje zmiana adresu URL.

Lokalizatory nie są przeznaczone do zarządzania kontrolą dostępu dla poszczególnych użytkowników. Różne prawa dostępu można nadać indywidualnym użytkownikom przy użyciu rozwiązań Digital Rights Management (DRM). Aby uzyskać więcej informacji, zobacz [Zabezpieczanie nośnika](/previous-versions/azure/dn282272(v=azure.100)).

Podczas tworzenia lokalizatora może wystąpić opóźnienie z 30 sekund wynikające z wymaganych procesów magazynu i propagacji w usłudze Azure Storage.

## <a name="adaptive-streaming"></a>Adaptacyjne przesyłanie strumieniowe
Technologie z adaptacyjną szybkością transmisji bitów umożliwiają aplikacjom odtwarzacza wideo Określanie warunków sieciowych i wybór spośród kilku szybkości transmisji bitów. W przypadku obniżenia poziomu komunikacji sieciowej klient może wybrać niższą szybkość transmisji bitów, aby odtwarzanie było kontynuowane z niższą jakością wideo. W miarę poprawy warunków sieci klient może przełączyć się na wyższą szybkość transmisji bitów z lepszą jakością wideo. Azure Media Services obsługuje następujące technologie z adaptacyjną szybkością transmisji bitów: HTTP Live Streaming (HLS), Smooth Streaming i MPEG-KRESKa.

Aby zapewnić użytkownikom adresy URL przesyłania strumieniowego, należy najpierw utworzyć lokalizator OnDemandOrigin. Utworzenie lokalizatora zapewnia podstawową ścieżkę do elementu zawartości zawierającego zawartość, którą chcesz przesłać strumieniowo. Aby jednak można było przesłać strumieniowo tę zawartość, należy zmodyfikować tę ścieżkę. Aby utworzyć pełny adres URL pliku manifestu przesyłania strumieniowego, należy połączyć wartość ścieżki lokalizatora i nazwę pliku manifestu (filename. ISM). Następnie Dołącz **/manifest** i odpowiedni format (w razie potrzeby) do ścieżki lokalizatora.

> [!NOTE]
> Możesz również przesyłać strumieniowo zawartość za pośrednictwem połączenia TLS. Aby to zrobić, upewnij się, że adresy URL przesyłania strumieniowego zaczynają się od protokołu HTTPS. Należy pamiętać, że obecnie nie obsługuje protokołu TLS z domenami niestandardowymi.  
> 

Można przesyłać strumieniowo tylko przy użyciu protokołu TLS, jeśli punkt końcowy przesyłania strumieniowego, z którego dostarczasz zawartość, został utworzony po 10 września 2014. Jeśli adresy URL przesyłania strumieniowego są oparte na punktach końcowych przesyłania strumieniowego utworzonych po 10 września, 2014, adres URL zawiera "streaming.mediaservices.windows.net". Adresy URL przesyłania strumieniowego zawierające "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu TLS. Jeśli Twój adres URL ma stary format i chcesz mieć możliwość przesyłania strumieniowego przy użyciu protokołu TLS, Utwórz nowy punkt końcowy przesyłania strumieniowego. Użyj adresów URL opartych na nowym punkcie końcowym przesyłania strumieniowego do przesyłania strumieniowego zawartości za pośrednictwem protokołu TLS.

## <a name="streaming-url-formats"></a><a name="URLs"></a>Formaty adresów URL przesyłania strumieniowego

### <a name="mpeg-dash-format"></a>Format MPEG-KRESKOWANY
{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = MPD-czas-CSF)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Format Apple HTTP Live Streaming (HLS) v4
{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Format firmy Apple HTTP Live Streaming (HLS) v3
{Nazwa punktu końcowego przesyłania strumieniowego — nazwa konta usługi Media Services}. Streaming. MediaServices. Windows. NET/{Locator ID}/{Nazwa pliku (format = M3U8-AAPL-v3)

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Format Apple HTTP Live Streaming (HLS) z filtrem tylko audio
Domyślnie ścieżki tylko audio są uwzględniane w manifeście HLS. Jest to wymagane w przypadku certyfikacji sklepu Apple dla sieci komórkowych. W takim przypadku, jeśli klient nie ma wystarczającej przepustowości lub jest połączony za pośrednictwem połączenia 2G, odtwarzanie jest włączane tylko do audio. Pomaga to w zachowaniu przesyłania strumieniowego zawartości bez buforowania, ale nie ma wideo. W niektórych scenariuszach buforowanie odtwarzacza może być preferowane wyłącznie przez audio. Jeśli chcesz usunąć śledzenie tylko audio, Dodaj **tylko audio = false** do adresu URL.

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL-v3, audio-Only = false)

Aby uzyskać więcej informacji, zobacz [Obsługa dynamicznego tworzenia składowych manifestu i HLS danych wyjściowych](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Smooth Streaming format
{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

Przykład:

http: \/ /testendpoint-testaccount.Streaming.MediaServices.Windows.NET/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Manifest Smooth Streaming 2,0 (stary manifest)
Domyślnie Smooth Streaming format manifestu zawiera tag Repeat (tag języka r). Jednak niektórzy gracze nie obsługują znacznika języka r. Klienci z tymi graczami mogą używać formatu, który wyłącza tag języka r:

{Nazwa punktu końcowego przesyłania strumieniowego — nazwa konta usługi Media Services}. Streaming. MediaServices. Windows. NET/{Locator ID}/{Nazwa pliku (format = FMP4-V20)

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)`

## <a name="progressive-download"></a>Pobieranie progresywne
Dzięki pobieraniu progresywnemu można rozpocząć odtwarzanie multimediów przed pobraniem całego pliku. Nie można stopniowo pobierać plików. ISM * (ISMV, ISMA, ISMT lub ISMC).

Aby stopniowo pobierać zawartość, użyj typu OnDemandOrigin lokalizatora. Poniższy przykład pokazuje adres URL, który jest oparty na OnDemandOrigin typie lokalizatora:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

Należy odszyfrować wszystkie zasoby zaszyfrowane magazynem, które mają być przesyłane z usługi pochodzenia na potrzeby pobierania progresywnego.

## <a name="download"></a>Pobierz
Aby pobrać zawartość na urządzenie klienckie, należy utworzyć lokalizator sygnatury dostępu współdzielonego. Lokalizator SAS zapewnia dostęp do kontenera usługi Azure Storage, w którym znajduje się plik. Aby utworzyć adres URL pobierania, należy osadzić nazwę pliku między hostem a podpisem sygnatury dostępu współdzielonego.

Poniższy przykład pokazuje adres URL, który jest oparty na lokalizacji sygnatury dostępu współdzielonego:

`https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D`

Obowiązują następujące zastrzeżenia:

* Należy odszyfrować wszystkie zasoby zaszyfrowane magazynem, które mają być przesyłane z usługi pochodzenia na potrzeby pobierania progresywnego.
* Pobieranie, które nie zostało zakończone w ciągu 12 godzin, zakończy się niepowodzeniem.

## <a name="streaming-endpoints"></a>Punkty końcowe przesyłania strumieniowego

Punkt końcowy przesyłania strumieniowego reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub do usługi Content Delivery Network (CDN) w celu dalszej dystrybucji. Strumień wychodzący z usługi punktu końcowego przesyłania strumieniowego może być strumieniem na żywo lub zasobem wideo na żądanie na koncie Media Services. Istnieją dwa typy punktów końcowych przesyłania strumieniowego, **Standard** i **Premium**. Aby uzyskać więcej informacji, zobacz [Streaming endpoints overview (Omówienie punktów końcowych przesyłania strumieniowego)](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

## <a name="known-issues"></a>Znane problemy
### <a name="changes-to-smooth-streaming-manifest-version"></a>Zmiany w wersji manifestu Smooth Streaming
Przed wydaniem usługi 2016 lipca, gdy zasoby utworzone przez Media Encoder Standard, Media Encoder Premium Workflow lub wcześniejszy Azure Media Encoder były przesyłane strumieniowo przy użyciu dynamicznego tworzenia pakietów — zwracany manifest Smooth Streaming będzie zgodny z wersją 2,0. W wersji 2,0, czasy trwania fragmentacji nie są używane jako Tagi Repeat ("r"). Na przykład:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" n="0" />
        <c d="2000" />
        <c d="2000" />
        <c d="2000" />
    </StreamIndex>
</SmoothStreamingMedia>
```

W wersji 2016 usługi, wygenerowany manifest Smooth Streaming jest zgodny z wersją 2,2, z fragmentacjami powtarzania przy użyciu tagów REPEAT. Na przykład:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" r="4" />
    </StreamIndex>
</SmoothStreamingMedia>
```

Niektóre starsze klienci Smooth Streaming mogą nie obsługiwać tagów powtarzających i nie będą ładowały manifestu. Aby rozwiązać ten problem, można użyć starszego parametru formatu manifestu **(format = FMP4-V20)** lub zaktualizować klienta do najnowszej wersji, która obsługuje powtarzające się Tagi. Aby uzyskać więcej informacji, zobacz [Smooth Streaming 2,0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Aktualizowanie lokalizatorów Media Services po przeprowadzeniu wycofywania kluczy magazynu](media-services-roll-storage-access-keys.md)