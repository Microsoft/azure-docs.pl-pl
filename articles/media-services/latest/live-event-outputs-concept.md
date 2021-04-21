---
title: Pojęcia dotyczące wydarzeń na żywo i danych wyjściowych na żywo
description: Ten temat zawiera omówienie wydarzeń na żywo i danych wyjściowych na żywo w Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 44ab9e4ff83fec2ddfbd1cb44f503298d12789d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766303"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Wydarzenia na żywo i dane wyjściowe na żywo w Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services umożliwia dostarczanie wydarzeń na żywo do klientów w chmurze platformy Azure. Aby skonfigurować wydarzenia transmisji strumieniowej na żywo w Media Services w wersji 3, musisz zrozumieć pojęcia omówione w tym artykule.

> [!TIP]
> W przypadku klientów migrowania z interfejsów API  Media Services w  wersji 2  jednostka wydarzenia na żywo zastępuje kanał w wersji 2, a dane wyjściowe na żywo zastępują **program**.

## <a name="live-events"></a>Wydarzenia na żywo

[Wydarzenia na](/rest/api/media/liveevents) żywo są odpowiedzialne za pozysłanie i przetwarzanie kanałów informacyjnych wideo na żywo. Podczas tworzenia wydarzenia na żywo jest tworzony podstawowy i pomocniczy wejściowy punkt końcowy, za pomocą których można wysyłać sygnał na żywo z kodera zdalnego. Zdalny koder na żywo wysyła kanał informacyjny do tego wejściowego punktu końcowego przy użyciu protokołu wejściowego [RTMP](https://www.adobe.com/devnet/rtmp.html) [lub Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (pofragmentowany plik MP4). W przypadku protokołu pozysłu RTMP zawartość może być wysyłana w postaci czystej ( ) lub bezpiecznie zaszyfrowana `rtmp://` w przewodze( `rtmps://` ). W przypadku Smooth Streaming protokołu pozyskiego obsługiwane schematy adresów URL to `http://` lub `https://` .  

## <a name="live-event-types"></a>Typy wydarzeń na żywo

Wydarzenie [na](/rest/api/media/liveevents) żywo można ustawić na *pass-through* (lokalne koder na żywo wysyła strumień o wielokrotnej transmisji bitów) lub kodowanie na żywo *(koder* na żywo w środowisku lokalnym wysyła strumień o pojedynczej transmisji bitów). Typy są ustawiane podczas tworzenia przy użyciu [typu LiveEventEncodingType:](/rest/api/media/liveevents/create#liveeventencodingtype)

* **LiveEventEncodingType.None:** koder na żywo w środowisku lokalnym wysyła strumień o wielokrotnej transmisji bitów. Pozyskany strumień przechodzi przez wydarzenie na żywo bez dalszego przetwarzania. Nazywana również trybem przekazania.
* **LiveEventEncodingType.Standard:** lokalne koder na żywo wysyła strumień o pojedynczej transmisji bitów do wydarzenia na żywo i Media Services wiele strumieni o różnej transmisji bitów. Jeśli kanał informacyjny udziału ma rozdzielczość 720p lub wyższą, ustawienie wstępne **Default720p** zakoduje zestaw 6 par rozdzielczości/transmisji bitów.
* **LiveEventEncodingType.Premium1080p:** koder na żywo w środowisku lokalnym wysyła strumień o pojedynczej transmisji bitów do wydarzenia na żywo i Media Services wiele strumieni o różnej transmisji bitów. Ustawienie wstępne Default1080p określa zestaw wyjściowy par rozpoznawania/transmisji bitów.

### <a name="pass-through"></a>Przekazywanie

![wydarzenie na żywo typu pass-through z Media Services przykładowym diagramem](./media/live-streaming/pass-through.svg)

W przypadku korzystania z pass-through wydarzenia na żywo polegasz na lokalnym koderze na żywo w celu wygenerowania strumienia wideo o wielokrotnej transmisji bitów i wysłania go jako kanału informacyjnego udziału do wydarzenia na żywo (przy użyciu protokołu RTMP lub pliku MP4 podzielonego na fragmenty). Wydarzenie na żywo będzie następnie odbywać się przez przychodzące strumienie wideo bez dalszego przetwarzania. Takie pass-throughowe wydarzenie na żywo jest zoptymalizowane pod kątem długotrwałych wydarzeń na żywo lub liniowej transmisji strumieniowej na żywo 24x365. Podczas tworzenia tego typu wydarzenia na żywo określ wartość Brak (LiveEventEncodingType.None).

Możesz wysyłać kanał informacyjny o rozdzielczości maksymalnie 4K oraz z szybkością 60 klatek na sekundę, za pomocą koderów-dekoderów wideo H.264/AVC lub H.265/HEVC i kodera-dekodera audio AAC (AAC-LC, HE-AACv1 lub HE-AACv2). Aby uzyskać więcej informacji, zobacz [Porównanie typów wydarzeń na żywo](live-event-types-comparison-reference.md).

> [!NOTE]
> Użycie metody pass-through jest najbardziej opłacalnym sposobem prowadzenia transmisji strumieniowej na żywo w przypadku prowadzenia wielu wydarzeń w długim okresie i już zainwestowano w kodery lokalne. Zobacz [Szczegóły](https://azure.microsoft.com/pricing/details/media-services/) cennika.
>

Zobacz przykład kodu .NET, aby utworzyć pass-through Live Event in Live Event with DVR (Przekaż wydarzenie na żywo za pomocą [DVR).](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214)

### <a name="live-encoding"></a>Kodowanie na żywo  

![Kodowanie na żywo z Media Services przykładowym diagramem](./media/live-streaming/live-encoding.svg)

W przypadku korzystania z kodowania na żywo Media Services należy skonfigurować koder na żywo w celu wysyłania wideo o pojedynczej transmisji bitów jako kanału informacyjnego do wydarzenia na żywo (przy użyciu protokołu RTMP lub Fragmented-Mp4). Następnie należy skonfigurować wydarzenie na żywo, aby kodować ten [](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)przychodzący strumień o pojedynczej transmisji bitów do strumienia wideo o wielokrotnej transmisji bitów i udostępniać dane wyjściowe do dostarczenia w celu odtwarzania urządzeń za pośrednictwem protokołów, takich jak MPEG-DASH, HLS i Smooth Streaming.

W przypadku korzystania z kodowania na żywo można wysyłać kanał informacyjny udziału tylko w rozdzielczości do 1080p z szybkością klatek 30 klatek na sekundę, z koderem wideo H.264/AVC i koderem audio AAC (AAC-LC, HE-AACv1 lub HE-AACv2). Należy pamiętać, że pass-through live events can support resolutions to 4K at 60 frames/s (Pass-through live events can support resolutions to 4K at 60 frames/s) (Pass-through live events can support resolutions to 4K at 60 frames/s (Przechodnie wydarzenia na żywo Aby uzyskać więcej informacji, zobacz [Porównanie typów wydarzeń na żywo](live-event-types-comparison-reference.md).

Rozdzielczości i wartości transmisji bitów zawarte w danych wyjściowych kodera na żywo są określane przez ustawienie wstępne. Jeśli używasz  standardowego kodera na żywo (LiveEventEncodingType.Standard), ustawienie wstępne *Default720p* określa zestaw sześciu par szybkości rozdzielczości/bitów, od 720p przy szybkości 3,5 Mb/s do 192p przy szybkości 200 kb/s. W przeciwnym razie, jeśli używasz kodera na żywo **Premium1080p** (LiveEventEncodingType.Premium1080p), ustawienie wstępne *Default1080p* określa zestaw sześciu par szybkości rozdzielczości/bitów, od 1080p przy szybkości 3,5 Mb/s do 180p przy szybkości 200 kb/s. Aby uzyskać informacje, zobacz [Ustawienia wstępne systemu](live-event-types-comparison-reference.md#system-presets).

> [!NOTE]
> Jeśli musisz dostosować ustawienie wstępne kodowania na żywo, otwórz bilet pomocy technicznej za pośrednictwem Azure Portal. Określ żądaną tabelę rozdzielczości i transmisji bitów. Sprawdź, czy istnieje tylko jedna warstwa o standardzie 720p (w przypadku żądania ustawienia wstępnego dla kodera na żywo w warstwie Standardowa) lub 1080p (w przypadku żądania ustawienia wstępnego dla kodera na żywo Premium1080p) i co najwyżej 6 warstw.

## <a name="creating-live-events"></a>Tworzenie wydarzeń na żywo

### <a name="options"></a>Opcje

Podczas tworzenia wydarzenia na żywo można określić następujące opcje:

* Możesz nadać wydarzenie na żywo nazwę i opis.
* Kodowanie w chmurze obejmuje kodowanie w chmurze (bez kodowania w chmurze), Standard (do 720p) lub Premium (do 1080p). W przypadku kodowania Standardowa i Premium można wybrać tryb rozciągania zakodowanych wideo.
  * Brak: ściśle przestrzega rozdzielczości danych wyjściowych określonej w ustawieniach wstępnych kodowania bez uwzględnienia współczynnika proporcji pikseli lub współczynnika proporcji ekranu wejściowego wideo.
  * AutoSize: zastępuje rozdzielczość danych wyjściowych i zmienia ją tak, aby dopasować współczynnik proporcji wyświetlania danych wejściowych bez wypełnienia. Jeśli na przykład dane wejściowe to 1920 x 1080, a ustawienie wstępne kodowania wymaga wartości 1280 x 1280, wartość w ustawieniach wstępnych zostanie zastąpiona, a dane wyjściowe będą mieć format 1280 x 720, który zachowuje współczynnik proporcji danych wejściowych 16:9.
  * Automatyczne dopasowywanie: dopasowywuje dane wyjściowe (przy użyciu pola letterbox lub pola filaru), aby honorować rozdzielczość danych wyjściowych, przy jednoczesnym zapewnieniu, że aktywny region wideo w danych wyjściowych ma taki sam współczynnik proporcji jak dane wejściowe. Jeśli na przykład dane wejściowe to 1920x1080, a ustawienie wstępne kodowania wymaga wartości 1280 x 1280, dane wyjściowe będą mieć format 1280x1280, który zawiera wewnętrzny prostokąt o wymiarach 1280 x 720 o współczynniku proporcji 16:9, z obszarami pola filaru o szerokości 280 pikseli po lewej i prawej stronie.
* Protokół przesyłania strumieniowego (obecnie obsługiwane są protokoły RTMP i Smooth Streaming). Nie można zmienić opcji protokołu, gdy wydarzenie na żywo lub skojarzone z nim dane wyjściowe na żywo są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz oddzielne wydarzenie na żywo dla każdego protokołu przesyłania strumieniowego.
* Identyfikator wejściowy, który jest globalnie unikatowym identyfikatorem strumienia wejściowego wydarzenia na żywo.
* Statyczny prefiks nazwy hosta, który nie zawiera żadnych znaków (w takim przypadku zostanie użyty losowy 128-bitowy ciąg hex), użyj nazwy wydarzenia na żywo lub użyj nazwy niestandardowej.  Gdy zdecydujesz się użyć nazwy klienta, ta wartość będzie prefiksem niestandardowej nazwy hosta.
* Możesz zmniejszyć opóźnienie między emisją na żywo a odtwarzaniem, ustawiając interwał klatek wejściowych klucza, który jest czasem trwania (w sekundach) każdego segmentu nośnika w danych wyjściowych HLS. Wartość powinna być niezerową liczbą całkowitą z zakresu od 0,5 do 20 sekund.  Wartość domyślna to 2 sekundy, jeśli *żaden* z interwałów klatek wejściowych ani wyjściowych nie jest ustawiony. Interwał klatek kluczowych jest dozwolony tylko w przypadku zdarzeń przebiegu.
* Podczas tworzenia zdarzenia możesz ustawić je na autostart. Gdy autostart zostanie ustawiony na wartość true, wydarzenie na żywo zostanie uruchomione po utworzeniu. Rozliczanie rozpoczyna się natychmiast po uruchomieniu wydarzenia na żywo. Aby zatrzymać dalsze rozliczanie, musisz jawnie wywołać zatrzymać zasób wydarzenia na żywo. Możesz też uruchomić wydarzenie, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego.

> [!NOTE]
> Maksymalna szybkość klatek wynosi 30 dolarów dla kodowania Standardowa i Premium.

## <a name="standby-mode"></a>Tryb StandBy

Podczas tworzenia wydarzenia na żywo możesz ustawić je na tryb StandBy. Gdy zdarzenie jest w trybie StandBy, można edytować opis, statyczny prefiks nazwy hosta i ograniczyć ustawienia dostępu do danych wejściowych i podglądu.  Tryb StandBy jest nadal trybem rozliczanym, ale jego ceny są naliczane inaczej niż w przypadku uruchamiania transmisji strumieniowej na żywo.

Aby uzyskać więcej informacji, zobacz [Live event states and billing (Stany wydarzeń na żywo i rozliczenia).](live-event-states-billing-concept.md)

* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Możesz zdefiniować adresy IP, które mogą pozysać wideo do tego wydarzenia na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).
<br/><br/>
Jeśli nie określono żadnych adresów IP i nie ma definicji reguły, żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą mieć jeden z następujących formatów: adres IpV4 z czterema cyframi lub zakres adresów CIDR.
<br/><br/>
Jeśli chcesz włączyć określone adresy IP we własnych zaporach lub ograniczyć dane wejściowe do wydarzeń na żywo do adresów IP platformy Azure, pobierz plik JSON z zakresów adresów IP centrum danych [Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Aby uzyskać szczegółowe informacje o tym pliku, wybierz **sekcję** Szczegóły na stronie.

* Podczas tworzenia wydarzenia możesz włączyć transkrypcje na żywo. Domyślnie transkrypcja na żywo jest wyłączona. Aby uzyskać więcej informacji na temat transkrypcji na żywo, przeczytaj [temat Transkrypcja na żywo.](live-event-live-transcription-how-to.md)

### <a name="naming-rules"></a>Reguły nazewnictwa

* Maksymalna nazwa wydarzenia na żywo to 32 znaki.
* Nazwa powinna być zgodne z tym [wzorcem wyrażenia regularnego:](/dotnet/standard/base-types/regular-expression-language-quick-reference) `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Zobacz również [konwencje nazewnictwa punktów](stream-streaming-endpoint-concept.md#naming-convention)końcowych przesyłania strumieniowego .

> [!TIP]
> Aby zagwarantować unikatowość nazwy wydarzenia na żywo, możesz wygenerować identyfikator GUID, a następnie usunąć wszystkie łączniki i nawiasy klamrowe (jeśli są). Ciąg będzie unikatowy dla wszystkich wydarzeń na żywo, a jego długość będzie wynosić 32.

## <a name="live-event-ingest-urls"></a>Adresy URL pozysłania wydarzeń na żywo

Po utworzeniu wydarzenia na żywo możesz uzyskać adresy URL pozysowania, które udostępnisz koderowi lokalneowi na żywo. Koder na żywo używa tych adresów URL do wprowadzenia strumienia na żywo. Aby uzyskać więcej informacji, zobacz [Zalecane lokalne kodery na żywo.](encode-recommended-on-premises-live-encoders.md)

>[!NOTE]
> Od wersji 2020-05-01 interfejsu API adresy URL "vanity" są znane jako statyczne nazwy hostów (useStaticHostname: true)


> [!NOTE]
> Aby adres URL pozyscy był statyczny i przewidywalny do użycia w konfiguracji kodera sprzętowego, ustaw właściwość **useStaticHostname** na wartość true i ustaw właściwość **accessToken** na ten sam identyfikator GUID przy każdym tworzeniu. 

### <a name="example-liveevent-and-liveeventinput-configuration-settings-for-a-static-non-random-ingest-rtmp-url"></a>Przykładowe ustawienia konfiguracji LiveEvent i LiveEventInput dla statycznego (nie losowego) adresu URL pozysku RTMP.

```csharp
             LiveEvent liveEvent = new LiveEvent(
                    location: mediaService.Location,
                    description: "Sample LiveEvent from .NET SDK sample",
                    // Set useStaticHostname to true to make the ingest and preview URL host name the same. 
                    // This can slow things down a bit. 
                    useStaticHostname: true,

                    // 1) Set up the input settings for the Live event...
                    input: new LiveEventInput(
                        streamingProtocol: LiveEventInputProtocol.RTMP,  // options are RTMP or Smooth Streaming ingest format.
                                                                         // This sets a static access token for use on the ingest path. 
                                                                         // Combining this with useStaticHostname:true will give you the same ingest URL on every creation.
                                                                         // This is helpful when you only want to enter the URL into a single encoder one time for this Live Event name
                        accessToken: "acf7b6ef-8a37-425f-b8fc-51c2d6a5a86a",  // Use this value when you want to make sure the ingest URL is static and always the same. If omitted, the service will generate a random GUID value.
                        accessControl: liveEventInputAccess, // controls the IP restriction for the source encoder.
                        keyFrameIntervalDuration: "PT2S" // Set this to match the ingest encoder's settings
                    ),
```

* Niestatyczne nazwy hosta

    Niestatyczne nazwy hosta to domyślny tryb w programie Media Services v3 podczas tworzenia pliku **LiveEvent.** Wydarzenia na żywo można przydzielać nieco szybciej, ale adres URL do pozysowania, który będzie potrzebny dla sprzętu lub oprogramowania do kodowania na żywo, będzie losowy. Adres URL zmieni się, jeśli zatrzymasz/rozpoczniesz wydarzenie na żywo. Niestatyczne nazwy hostów są przydatne tylko w scenariuszach, w których użytkownik końcowy chce przesyłać strumieniowo przy użyciu aplikacji, która wymaga bardzo szybkiego uzyskania wydarzenia na żywo i dynamicznego adresu URL pozyskania nie jest problemem.

    Jeśli aplikacja kliency nie musi wstępnie generować adresu URL pozysku przed utworzeniem wydarzenia na żywo, Media Services automatycznie wygeneruj token dostępu dla wydarzenia na żywo.

* Statyczne nazwy hostów 

    Tryb statycznej nazwy hosta jest preferowany przez większość operatorów, którzy chcą wstępnie skonfigurować sprzęt lub oprogramowanie do kodowania na żywo przy użyciu adresu URL do pozyskania RTMP, który nigdy nie zmienia się podczas tworzenia lub zatrzymania/rozpoczęcia określonego wydarzenia na żywo. Te operatory chcą uzyskać predykcyjny adres URL do pozycyjnych danych RTMP, który nie zmienia się z czasem. Jest to również bardzo przydatne, gdy trzeba wypchnąć statyczny adres URL do pozyskania RTMP do ustawień konfiguracji sprzętowego urządzenia do kodowania, takiego jak BlackMagic Atem Mini Pro, lub podobnych narzędzi do kodowania i produkcji. 

    > [!NOTE]
    > W Azure Portal adres URL statycznej nazwy hosta ma nazwę *"Prefiks statycznej nazwy hosta".*

    Aby określić ten tryb w interfejsie API, ustaw `useStaticHostName` na wartość w czasie tworzenia `true` (wartość domyślna to `false` ). W przypadku ustawienia wartości true parametr określa pierwszą część nazwy hosta przypisaną do podglądu wydarzenia na żywo i `useStaticHostname` `hostnamePrefix` punktów końcowych pozysków. Końcowa nazwa hosta będzie kombinacją tego prefiksu, nazwy konta usługi multimediów i krótkiego kodu Azure Media Services danych.

    Aby uniknąć losowego tokenu w adresie URL, należy również przekazać własny token dostępu ( `LiveEventInput.accessToken` ) podczas tworzenia.  Token dostępu musi być prawidłowym ciągiem identyfikatora GUID (z łącznikami lub bez nich). Po skonfigurowaniu trybu nie można go zaktualizować.

    Token dostępu musi być unikatowy w Regionie świadczenia usługi Azure i Media Services konta. Jeśli aplikacja musi używać statycznego adresu URL do pozyskiwania nazwy hosta, zaleca się, aby zawsze tworzyć nowe wystąpienie identyfikatora GUID do użycia z określoną kombinacją regionu, konta usługi Media Services i wydarzenia na żywo.

    Użyj następujących interfejsów API, aby włączyć statyczny adres URL nazwy hosta i ustawić token dostępu na prawidłowy identyfikator GUID (na przykład `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Język|Włączanie statycznego adresu URL nazwy hosta|Określanie tokenu dostępu|
    |---|---|---|
    |REST|[properties.useStaticHostname](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.useStaticHostname](/rest/api/media/liveevents/create#liveeventinput)|
    |Interfejs wiersza polecenia|[--use-static-hostname](/cli/azure/ams/live-event#az_ams_live_event_create)|[--access-token](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.useStaticHostname](/dotnet/api/microsoft.azure.management.media.models.liveevent.usestatichostname?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Management_Media_Models_LiveEvent_UseStaticHostname)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Reguły nazewnictwa adresów URL pozyskowania na żywo

* *Losowy* ciąg poniżej to 128-bitowa liczba szesnastkowa (która składa się z 32 znaków 0–9 a–f).
* *token dostępu:* prawidłowy ciąg identyfikatora GUID ustawiony podczas używania ustawienia statycznej nazwy hosta. Na przykład `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *nazwa strumienia:* wskazuje nazwę strumienia dla określonego połączenia. Wartość nazwy strumienia jest zwykle dodawana przez koder na żywo, z których korzystasz. Koder na żywo można skonfigurować tak, aby opisywał połączenie przy użyciu dowolnej nazwy, na przykład "video1_audio1", "video2_audio1", "stream".

#### <a name="non-static-hostname-ingest-url"></a>Adres URL pozyscywu niestatycznej nazwy hosta

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="static-hostname-ingest-url"></a>Statyczny adres URL do pozyskania nazwy hosta

W poniższych ścieżkach oznacza nazwę nadaną zdarzeniu lub nazwę niestandardową używaną `<live-event-name>` podczas tworzenia wydarzenia na żywo.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Adres URL podglądu wydarzenia na żywo

Gdy wydarzenie na żywo zacznie otrzymywać kanał informacyjny o udziałach, możesz użyć jego punktu końcowego podglądu, aby wyświetlić podgląd i sprawdzić, czy otrzymujesz strumień na żywo przed dalszym publikowaniem. Po sprawdzeniu, czy strumień podglądu jest dobry, możesz użyć wydarzenia na żywo, aby udostępnić strumień na żywo do dostarczenia za pośrednictwem co najmniej jednego (wstępnie utworzonego) punktu końcowego przesyłania strumieniowego. W tym celu utwórz nowe dane wyjściowe [na żywo](/rest/api/media/liveoutputs) wydarzenia na żywo.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że wideo przepływa do adresu URL podglądu.

## <a name="live-event-long-running-operations"></a>Długotrwałe operacje wydarzeń na żywo

Aby uzyskać szczegółowe informacje, [zobacz długotrwałe operacje](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Dane wyjściowe na żywo

Gdy strumień przepływa do wydarzenia na żywo, możesz rozpocząć wydarzenie przesyłania strumieniowego, tworząc element [zawartości,](/rest/api/media/assets)dane wyjściowe [na żywo](/rest/api/media/liveoutputs)i lokalizator [przesyłania strumieniowego.](/rest/api/media/streaminglocators) Dane wyjściowe na żywo zarchiwizuje strumień i udostępni go podglądom za pośrednictwem punktu [końcowego przesyłania strumieniowego](/rest/api/media/streamingendpoints).  

Aby uzyskać szczegółowe informacje na temat danych wyjściowych na żywo, zobacz [Using a cloud DVR (Korzystanie z funkcji DVR w chmurze).](live-event-cloud-dvr-time-how-to.md)

## <a name="live-event-output-questions"></a>Pytania dotyczące danych wyjściowych wydarzenia na żywo

Zobacz artykuł [z pytaniami na temat danych wyjściowych wydarzeń na](questions-collection.md#live-streaming) żywo.
