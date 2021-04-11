---
title: Dynamiczne pakowanie w Azure Media Services v3
description: Ten artykuł zawiera omówienie tworzenia pakietów dynamicznych w programie Azure Media Services.
author: myoungerman
manager: femila
services: media-services
ms.service: media-services
ms.workload: media
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: a2f5e51af5e41bad4e88509d4440a8de2bc5525c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280432"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dynamiczne pakowanie w Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Microsoft Azure Media Services może służyć do kodowania wielu formatów plików źródłowych multimediów. Udostępnia je za pośrednictwem różnych protokołów przesyłania strumieniowego z ochroną zawartości lub bez niej, aby dotrzeć do wszystkich głównych urządzeń (takich jak urządzenia z systemem iOS lub Android). Ci klienci rozumieją różne protokoły. Na przykład system iOS wymaga dostarczania strumieni w formacie HTTP Live Streaming (HLS), a urządzenia z systemem Android obsługują HLS, a także KRESKę MPEG.

W Media Services [punkt końcowy przesyłania strumieniowego](stream-streaming-endpoint-concept.md) (Origin) reprezentuje dynamiczny (just-in-Time) pakiet i pierwotną usługę, która umożliwia dostarczanie zawartości na żywo i na żądanie bezpośrednio do aplikacji odtwarzacza klienckiego. Używa jednego z popularnych protokołów multimediów przesyłania strumieniowego, które wymieniono w poniższej sekcji. *Dynamiczne pakowanie* to funkcja, która jest standardem dla wszystkich punktów końcowych przesyłania strumieniowego.

## <a name="to-prepare-your-source-files-for-delivery"></a>Aby przygotować pliki źródłowe do dostarczenia

Aby skorzystać z funkcji dynamicznego tworzenia pakietów, musisz [zakodować](encode-concept.md) plik Mezzanine (Source) do zestawu wielu plików MP4 (ISO Base Media 14496-12). Musisz mieć [zasób](assets-concept.md) z zakodowanymi plikami konfiguracyjnymi MP4 i przesyłania strumieniowego, które są potrzebne do Media Services dynamicznego tworzenia pakietów. Z tego zestawu plików MP4 można użyć pakowania dynamicznego do dostarczania wideo za pośrednictwem protokołów multimediów strumieniowych opisanych poniżej.

Azure Media Services dynamiczne pakowanie obsługuje tylko pliki wideo i audio w formacie kontenera MP4. Pliki audio muszą być zakodowane w kontenerze MP4, a także przy użyciu alternatywnych koderów-dekoder, takich jak Dolby.  

> [!TIP]
> Jednym ze sposobów uzyskania plików konfiguracji MP4 i przesyłania strumieniowego jest [zakodowanie pliku mezzanine przy użyciu Media Services](#encode-to-adaptive-bitrate-mp4s). 

Aby udostępnić wideo w zakodowanym elemencie zawartości klientom do odtwarzania, należy utworzyć [lokalizator przesyłania strumieniowego](stream-streaming-locators-concept.md) i tworzyć adresy URL przesyłania strumieniowego. Następnie w oparciu o określony format w manifeście klienta przesyłania strumieniowego (HLS, PAUZy MPEG lub Smooth Streaming) otrzymasz strumień w wybranym protokole.

Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Jeśli planujesz ochronę zawartości przy użyciu Media Services szyfrowania dynamicznego, zobacz [protokoły przesyłania strumieniowego i typy szyfrowania](drm-content-protection-concept.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>Protokół HLS

Twój klient przesyłania strumieniowego może określić następujące formaty HLS:

|Protokół|Przykład|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|

> [!NOTE]
> W poprzednich wytycznych firmy Apple zaleca się, aby rezerwa dla sieci o niskiej przepustowości zapewniała strumień tylko audio.  W tej chwili koder Media Services automatycznie generuje śledzenie tylko audio.  Wytyczne firmy Apple teraz stwierdzają, że *nie* należy uwzględniać ścieżki tylko audio, szczególnie w przypadku dystrybucji TV firmy Apple.  Aby uniemożliwić odtwarzaczowi domyślne śledzenie tylko audio, zalecamy użycie tagu "audio-Only = false" w adresie URL, który usuwa tylko odwzorowanie tylko audio w HLS, lub po prostu używa HLS-v3. Na przykład `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.

### <a name="mpeg-dash-protocol"></a>Protokół MPEG-KRESKOWANY

Twój klient przesyłania strumieniowego może określić następujące formaty formatu MPEG:

|Protokół|Przykład|
|---|---|
|MPEG-KRESKA CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|CMAF MPEG-KRESKA|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |

### <a name="smooth-streaming-protocol"></a>Protokół Smooth Streaming

Twój klient przesyłania strumieniowego może określić następujące formaty Smooth Streaming:

|Protokół|Uwagi/przykłady| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|
|Smooth Streaming 2,0 (starsza wersja manifestu)|Domyślnie Smooth Streaming format manifestu zawiera tag Repeat (tag języka r). Jednak niektórzy gracze nie obsługują programu `r-tag` . Klienci z tymi graczami mogą używać formatu, który wyłącza tag języka r:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> Smooth Streaming wymaga, aby dźwięk i wideo były obecne w strumieniu.

## <a name="on-demand-streaming-workflow"></a>Przepływ pracy przesyłania strumieniowego na żądanie

W poniższych krokach przedstawiono typowy przepływ pracy przesyłania strumieniowego Media Services, w którym jest używane dynamiczne pakowanie wraz ze standardowym koderem w Azure Media Services.

1. [Przekaż plik wejściowy](job-input-from-http-how-to.md) , taki jak MP4, QUICKTIME/MOV lub inny obsługiwany format pliku. Ten plik jest również określany jako plik Mezzanine lub source. Aby zapoznać się z listą obsługiwanych formatów, zobacz [formaty obsługiwane przez koder standardowy](encode-media-encoder-standard-formats-reference.md).
1. [Koduj](#encode-to-adaptive-bitrate-mp4s) plik Mezzanine do zestawu H. 264/AAC MP4 z adaptacyjną szybkością transmisji bitów.

    Jeśli masz już zakodowane pliki i chcesz skopiować i przesłać strumieniowo pliki, użyj: [CopyVideo](/rest/api/media/transforms/createorupdate#copyvideo) i [CopyAudio](/rest/api/media/transforms/createorupdate#copyaudio) API. W wyniku tego zostanie utworzony nowy plik MP4 z manifestem przesyłania strumieniowego (plik. ISM).
1. Opublikuj element wyjściowy z adaptacyjną szybkością transmisji bitów. Publikujesz, tworząc [lokalizator przesyłania strumieniowego](stream-streaming-locators-concept.md).
1. Tworzenie adresów URL przeznaczonych dla różnych formatów (HLS, MPEG-KRESKa i Smooth Streaming). *Punkt końcowy przesyłania strumieniowego* zajmie się zachowaniem prawidłowego manifestu i żądań dla wszystkich tych formatów.
    
Na poniższym diagramie przedstawiono strumień przesyłania strumieniowego na żądanie z dynamicznym przepływem pracy tworzenia pakietów.

![Diagram przepływu pracy na potrzeby przesyłania strumieniowego na żądanie z użyciem dynamicznego tworzenia pakietów](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

Ścieżka pobierania znajduje się na powyższym obrazie, aby pokazać, że można pobrać plik MP4 bezpośrednio za pomocą *punktu końcowego przesyłania strumieniowego* (Źródło) (można określić [zasady przesyłania strumieniowego](stream-streaming-policy-concept.md) do pobrania w lokalizatorze przesyłania strumieniowego).<br/>Pakowarka dynamiczna nie zmienia pliku. Opcjonalnie możesz użyć interfejsów API usługi Azure Blob Storage, aby uzyskać dostęp do plików MP4 bezpośrednio na potrzeby pobierania progresywnego, jeśli chcesz ominąć funkcję *punktu końcowego przesyłania strumieniowego* . 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Kodowanie do adaptacyjnej szybkości transmisji bitów pliki MP4

W poniższych artykułach przedstawiono przykłady [kodowania wideo przy użyciu Media Services](encode-concept.md):

* [Koduj z adresu URL HTTPS przy użyciu wbudowanych ustawień wstępnych](job-input-from-http-how-to.md).
* [Koduj plik lokalny przy użyciu wbudowanych ustawień wstępnych](job-input-from-local-file-how-to.md).
* [Utwórz niestandardowe ustawienie wstępne, aby określić odpowiednie wymagania dotyczące scenariusza lub urządzenia](encode-custom-presets-how-to.md).

Zapoznaj się z listą formatów koderów standardowych [i kodeków](encode-media-encoder-standard-formats-reference.md).

## <a name="live-streaming-workflow"></a>Przepływ pracy przesyłania strumieniowego na żywo

Wydarzenie na żywo można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień o wielokrotnej szybkości transmisji bitów) lub *kodowanie na żywo* (lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów). 

Oto typowy przepływ pracy przesyłania strumieniowego na żywo z *pakietem dynamicznym*:

1. Utwórz [wydarzenie na żywo](live-event-outputs-concept.md).
1. Pobierz adres URL pozyskiwania i skonfiguruj koder lokalny w taki sposób, aby używał adresu URL do wysyłania kanału informacyjnego udziału.
1. Uzyskaj adres URL wersji zapoznawczej i użyj go do sprawdzenia, czy dane wejściowe kodera są odbierane.
1. Utwórz nowy element zawartości.
1. Utwórz na żywo wyjście i użyj utworzonej nazwy zasobu.<br />Na żywo dane wyjściowe archiwizują strumień do elementu zawartości.
1. Utwórz lokalizator przesyłania strumieniowego z wbudowanymi typami zasad przesyłania strumieniowego.<br />Jeśli zamierzasz zaszyfrować zawartość, zapoznaj się z tematem [Omówienie ochrony zawartości](drm-content-protection-concept.md).
1. Wyświetl listę ścieżek w lokalizatorze przesyłania strumieniowego, aby uzyskać adresy URL do użycia.
1. Pobierz nazwę hosta dla punktu końcowego przesyłania strumieniowego, z którego chcesz przesyłać strumieniowo.
1. Tworzenie adresów URL przeznaczonych dla różnych formatów (HLS, MPEG-KRESKa i Smooth Streaming). *Punkt końcowy przesyłania strumieniowego* zajmuje się zachowaniem prawidłowego manifestu i żądań dla różnych formatów.

Ten diagram przedstawia przepływ pracy przesyłania strumieniowego na żywo z użyciem *dynamicznego tworzenia pakietów*:

![Diagram przepływu pracy na potrzeby kodowania przekazywanego przy użyciu dynamicznego tworzenia pakietów](./media/live-streaming/pass-through.svg)

Aby uzyskać informacje na temat przesyłania strumieniowego na żywo w Media Services v3, zobacz [Omówienie przesyłania strumieniowego na żywo](stream-live-streaming-concept.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Kodery-dekoder wideo obsługiwane przez pakowanie dynamiczne

Dynamiczne pakowanie obsługuje pliki wideo, które znajdują się w formacie pliku kontenera MP4 i zawierają wideo zakodowane przy użyciu [H. 264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (AVC MPEG-avc1) lub [H. 265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 lub hvc1).

> [!NOTE]
> Rozdzielczości do 4 KB i szybkości klatek dla maksymalnie 60 klatek na sekundę zostały przetestowane przy użyciu *dynamicznego tworzenia pakietów*.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Kodery-dekoder audio obsługiwane przez pakowanie dynamiczne

Pakowanie dynamiczne obsługuje również pliki audio, które są przechowywane w formacie kontenera plików MP4 zawierający zakodowany strumień audio, w jednym z następujących koderów-dekoder:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, IT-AAC v1 lub AAC v2). 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (ulepszone AC-3 lub E-AC3).  Zakodowane audio musi być przechowywane w formacie kontenera MP4, aby można było korzystać z dynamicznego tworzenia pakietów.
* Dolby Atmos

   Przesyłanie strumieniowe zawartości Dolby Atmos jest obsługiwane w przypadku standardów, takich jak protokół MPEG-KRESKa, przy użyciu wspólnego formatu przesyłania strumieniowego (CSF) lub Common Media Application Format (CMAF) i za pośrednictwem HTTP Live Streaming (HLS) z CMAF.
* [Pakiet](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Kodery-dekoder usług DTS obsługiwane przez ŁĄCZNIKi — CMAF, HLS-M2TS i HLS-CMAF są następujące:  

    * Cyfrowa przestrzenny usług DTS (dtsc)
    * DTS-HD High Solution i DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS — bezstratny dysk HD (bez rdzenia) (DTSL)

Dynamiczne pakowanie obsługuje wiele ścieżek audio z KRESKami lub HLS (w wersji 4 lub nowszej) dla zasobów przesyłania strumieniowego z wieloma dźwiękami i wieloma językami.

W przypadku wszystkich powyższych koderów audio, zakodowane audio musi być przechowywane w formacie kontenera MP4, aby można było korzystać z dynamicznego tworzenia pakietów. Usługa nie obsługuje nieprzetworzonych formatów plików strumieni podstawowych w usłudze BLOB Storage (na przykład następujące elementy nie są obsługiwane — DTS,. AC3.) 

W przypadku pakowania audio obsługiwane są tylko pliki z rozszerzeniem MP4 of. mp4a. 

### <a name="limitations"></a>Ograniczenia

#### <a name="ios-limitation-on-aac-51-audio"></a>ograniczenie systemu iOS na AAC 5,1 audio

Urządzenia Apple iOS nie obsługują kodera-dekoder audio 5,1 AAC. Dźwięk z obsługą kanału wielokanałowego musi być zakodowany przy użyciu koderów-dekoder Dolby Digital lub Dolby Digital Plus.

Aby uzyskać szczegółowe informacje, zobacz [specyfikację autorstwa HLS dla urządzeń firmy Apple](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices).

> [!NOTE]
> Media Services nie obsługuje kodowania Dolby Digital, Dolby Digital Plus lub Dolby Digital Plus z wielokanałowymi formatami audio Dolby Atmos.

#### <a name="dolby-digital-audio"></a>Cyfrowy dźwięk Dolby

Media Services pakiet dynamiczny nie obsługuje obecnie plików zawierających dźwięk [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3), ponieważ jest on traktowany jako starszy koder-dekoder przez Dolby).

## <a name="manifests"></a>Manifesty

W Media Services *dynamiczne pakowanie* manifesty klienta przesyłania strumieniowego dla HLS, MPEG-myślnik i Smooth Streaming są dynamicznie generowane na podstawie selektora formatu w adresie URL.  

Plik manifestu zawiera metadane przesyłania strumieniowego, takie jak typ ścieżki (audio, wideo lub tekst), nazwa ścieżki, godzina rozpoczęcia i zakończenia, szybkość transmisji bitów (jakość), Języki śledzenia, okno prezentacji (okno przewijania o stałym czasie) i koder-dekoder wideo (FourCC). Nakazuje również graczowi pobranie następnego fragmentu, dostarczając informacji o następnych, dostępnych fragmentach wideo, które są dostępne i ich lokalizacji. Fragmenty (lub segmenty) to rzeczywiste fragmenty zawartości wideo.

### <a name="examples"></a>Przykłady

#### <a name="hls"></a>HLS

Oto przykład pliku manifestu HLS, nazywanego również główną listą odtwarzania HLS: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-KRESKA

Oto przykład pliku manifestu MPEG-KRESKOWANY, nazywanego również opisem prezentacji multimediów MPEG-KRESKOWANY (MPD):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Smooth Streaming

Oto przykład pliku manifestu Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Nazewnictwo ścieżek w manifeście

Jeśli nazwa ścieżki audio została określona w pliku ISM, Media Services dodaje `Label` element wewnątrz elementu, `AdaptationSet` Aby określić textural informacje dla określonej ścieżki audio. Przykład manifestu wyjściowej PAUZy:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

Odtwarzacz może użyć `Label` elementu do wyświetlenia w jego interfejsie użytkownika.

### <a name="signaling-audio-description-tracks"></a>Sygnalizowanie ścieżek opisu audio

Możesz dodać ścieżkę narracji do filmu wideo, aby pomóc klientom niesparowanym z nagraniem wideo przez nasłuchiwanie narracji. Należy dodać adnotację do ścieżki audio jako opis audio w manifeście. W tym celu należy dodać parametry "Accessibility" i "role" do pliku ISM. Ponosisz odpowiedzialność za poprawne ustawianie tych parametrów, aby sygnalizować ścieżkę audio jako opis. Na przykład Dodaj `<param name="accessibility" value="description" />` plik i `<param name="role" value="alternate"` do pliku ISM dla określonej ścieżki audio. 

Aby uzyskać więcej informacji, zobacz artykuł [jak sygnalizować przykładową ścieżkę dźwiękową](signal-descriptive-audio-howto.md) .

#### <a name="smooth-streaming-manifest"></a>Manifest Smooth Streaming

W przypadku odtwarzania strumienia Smooth Streaming manifest będzie zawierać wartości `Accessibility` i `Role` atrybuty dla tej ścieżki audio. Na przykład `Role="alternate" Accessibility="description"` zostałaby dodana w `StreamIndex` elemencie, aby wskazać, że jest to opis audio.

#### <a name="dash-manifest"></a>Manifest KRESKOWANY

W przypadku manifestu PAUZy następujące dwa elementy zostałyby dodane do sygnalizowania opisu audio:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>Lista odtwarzania HLS

W przypadku HLS wersji 7 i wyższych `(format=m3u8-cmaf)` jego list odtwarzania będzie przenoszone `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` po zasygnalizowaniu ścieżki opisu audio.

#### <a name="example"></a>Przykład

Aby uzyskać więcej informacji, zobacz [jak sygnalizować śledzenie opisów dźwięku](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Manifest dynamiczny

Aby kontrolować liczbę ścieżek, formatów, szybkości transmisji bitów i okien czasu prezentacji, które są wysyłane do graczy, można użyć filtrowania dynamicznego z Media Servicesm dynamicznym. Aby uzyskać więcej informacji, zobacz [wstępne filtrowanie manifestów przy użyciu Pakowarki dynamicznego](filters-dynamic-manifest-concept.md).

## <a name="dynamic-encryption"></a>Szyfrowanie dynamiczne

Przy użyciu *szyfrowania dynamicznego* można dynamicznie szyfrować zawartość na żywo lub na żądanie za pomocą algorytmu AES-128 lub z jednego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługę do dostarczania kluczy AES i licencji DRM do autoryzowanych klientów. Aby uzyskać więcej informacji, zobacz [szyfrowanie dynamiczne](drm-content-protection-concept.md).

> [!NOTE]
> Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="more-information"></a>Więcej informacji

Wyewidencjonuj [Azure Media Services społeczność](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

[Przekazywanie, kodowanie i przesyłanie strumieniowe klipów wideo](stream-files-tutorial-with-api.md)
