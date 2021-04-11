---
title: Kodowanie wideo i audio przy użyciu Media Services
description: W tym artykule wyjaśniono, jak zakodować wideo i dźwięk przy użyciu Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 225f1d311739bdafbe39971a2b4ac74917e770e9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279497"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Kodowanie wideo i audio przy użyciu Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Termin kodowanie w Media Services ma zastosowanie do procesu konwertowania plików zawierających cyfrowe wideo i/lub dźwięk z jednego formatu standardowego na inny, w celu zmniejszenia rozmiaru plików i/lub (b) tworzenia formatu zgodnego z szeroką gamą urządzeń i aplikacji. Ten proces jest również nazywany kompresją wideo lub transkodowaniem. Zapoznaj się z [kompresją danych](https://en.wikipedia.org/wiki/Data_compression) i [kodowaniem i transkodowaniem?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) , aby uzyskać więcej informacji na temat pojęć.

Wideo są zwykle dostarczane do urządzeń i aplikacji przez [pobieranie progresywne](https://en.wikipedia.org/wiki/Progressive_download) lub [przesyłanie strumieniowe z adaptacyjną szybkością transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> W Media Services nie są naliczane opłaty za anulowane lub błędne zadania. Na przykład zadanie, które osiągnęło postęp 50% i anulowane, nie jest rozliczane o 50% minut zadań. Opłata jest naliczana tylko za ukończone zadania.

* Aby zapewnić pobieranie progresywne, można użyć Azure Media Services do przekonwertowania pliku multimediów cyfrowych (Mezzanine) do pliku [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) , który zawiera wideo zakodowane za pomocą kodera-dekoder [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) , i dźwięk, który został zakodowany za pomocą kodera-dekoder [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) . Ten plik MP4 jest zapisywana w elemencie zawartości na koncie magazynu. Aby pobrać plik bezpośrednio, można użyć interfejsów API lub zestawów SDK usługi Azure Storage (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md) lub [zestawu SDK platformy .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)). Jeśli w magazynie został utworzony zasób wyjściowy z określoną nazwą kontenera, Użyj tej lokalizacji. W przeciwnym razie możesz użyć Media Services, aby [wyświetlić listę adresów URL kontenerów zasobów](/rest/api/media/assets/listcontainersas). 
* Aby przygotować zawartość do dostarczenia przez przesyłanie strumieniowe z adaptacyjną szybkością transmisji bitów, plik Mezzanine musi być zakodowany na wielu szybkościach transmisji bitów (wysoka do niskich). Aby zapewnić płynne przejście do jakości, rozdzielczość wideo jest obniżana, ponieważ zmniejsza się szybkość transmisji bitów. Powoduje to, że jest to nazywane drabinem kodowania — tabelą rozdzielczości i szybkości transmisji bitów (zobacz [automatycznie wygenerowana drabina szybkość transmisji bitów](encode-autogen-bitrate-ladder.md)). Media Services do kodowania plików mezzanine przy użyciu wielu szybkości transmisji bitów. W tym celu uzyskasz zestaw plików MP4 i skojarzonych z nimi plików konfiguracji przesyłania strumieniowego, które są zapisywane do zasobu na koncie magazynu. Można następnie użyć funkcji tworzenia [pakietów dynamicznych](encode-dynamic-packaging-concept.md) w Media Services, aby dostarczyć wideo za pośrednictwem protokołów przesyłania strumieniowego, takich jak [MPEG-kreska](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) i [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Wymaga to utworzenia [lokalizatora przesyłania strumieniowego](stream-streaming-locators-concept.md) i adresów URL przesyłania strumieniowego, które są zgodne z obsługiwanymi protokołami, które można następnie przełączać do urządzeń/aplikacji na podstawie ich możliwości.

Na poniższym diagramie przedstawiono przepływ pracy dla kodowania na żądanie z pakietem dynamicznym.

![Przepływ pracy dla kodowania na żądanie z pakietem dynamicznym](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

Ten temat zawiera wskazówki dotyczące sposobu kodowania zawartości przy użyciu Media Services v3.

## <a name="transforms-and-jobs"></a>Transformacje i zadania

Aby kodować z Media Services v3, należy utworzyć [transformację](/rest/api/media/transforms) i [zadanie](/rest/api/media/jobs). Transformacja definiuje przepis dla ustawień kodowania i danych wyjściowych. to zadanie jest wystąpieniem przepisu. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-jobs-concept.md).

Przy kodowaniu przy użyciu Media Services należy użyć ustawień wstępnych, aby poinformować koder o sposobie przetwarzania plików multimedialnych. W Media Services v3 używasz kodera standardowego do kodowania plików. Na przykład można określić rozdzielczość wideo i/lub liczbę kanałów audio, które mają być w zakodowanej zawartości.

Możesz szybko rozpocząć pracę z jednym z zalecanych wbudowanych ustawień domyślnych opartych na najlepszych rozwiązaniach branżowych lub wybrać opcję utworzenia niestandardowego ustawienia wstępnego, które będzie ukierunkowane na konkretne wymagania dotyczące scenariusza lub urządzenia. Aby uzyskać więcej informacji, zobacz [kodowanie przy użyciu transformacji niestandardowej](encode-custom-presets-how-to.md).

Począwszy od stycznia 2019, gdy kodowanie przy użyciu standardowego kodera do tworzenia plików MP4, nowy plik MPI jest generowany i dodawany do wyjściowego elementu zawartości. Ten plik MPI jest przeznaczony do poprawiania wydajności dla scenariuszy [dynamicznego tworzenia pakietów](encode-dynamic-packaging-concept.md) i przesyłania strumieniowego.

> [!NOTE]
> Nie należy modyfikować ani usuwać pliku MPI ani korzystać z jakichkolwiek zależności w usłudze w przypadku istnienia takiego pliku.

### <a name="creating-job-input-from-an-https-url"></a>Tworzenie danych wejściowych zadania przy użyciu adresu URL HTTPS

Podczas przesyłania zadań w celu przetworzenia filmów wideo musisz powiedzieć, Media Services gdzie znaleźć wejściowy film wideo. Jedną z opcji jest określenie adresu URL protokołu HTTPS jako danych wejściowych zadania. Obecnie Media Services wersja 3 nie obsługuje kodowania transferu fragmentarycznego za pośrednictwem adresów URL protokołu HTTPS.

#### <a name="examples"></a>Przykłady

* [Kodowanie z adresu URL HTTPS za pomocą platformy .NET](stream-files-dotnet-quickstart.md)
* [Kodowanie z adresu URL protokołu HTTPS za pomocą interfejsu REST](stream-files-tutorial-with-rest.md)
* [Kodowanie z adresu URL HTTPS za pomocą interfejsu wiersza polecenia](stream-files-cli-quickstart.md)
* [Koduj z adresu URL HTTPS za pomocą Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Tworzenie danych wejściowych zadania z pliku lokalnego

Wejściowy film wideo może być przechowywany jako zasób usługi Media. w takim przypadku tworzony jest wejściowy zasób oparty na pliku (przechowywany lokalnie lub w usłudze Azure Blob Storage).

#### <a name="examples"></a>Przykłady

[Kodowanie pliku lokalnego przy użyciu wbudowanych ustawień wstępnych](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Tworzenie danych wejściowych zadania z podcinaniem

Podczas kodowania filmu wideo można również określić, aby przyciąć lub przyciąć plik źródłowy i utworzyć dane wyjściowe zawierające tylko pożądaną część wejściowego wideo. Ta funkcja działa z dowolnym [przekształceniem](/rest/api/media/transforms) utworzonym przy użyciu ustawień wstępnych [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) lub predefiniowanych ustawień [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) .

Możesz określić, aby utworzyć [zadanie](/rest/api/media/jobs/create) z pojedynczym klipem wideo na żądanie lub archiwum na żywo (zarejestrowane zdarzenie). Dane wejściowe zadania mogą być zasobem lub adresem URL HTTPS.

> [!TIP]
> Jeśli chcesz przesyłać strumieniowo sublip wideo bez ponownego kodowania filmu wideo, rozważ użycie [manifestów przed filtrowaniem przy użyciu Pakowarki dynamicznego](filters-dynamic-manifest-concept.md).

#### <a name="examples"></a>Przykłady

Zobacz przykłady:

* [Podcinanie wideo za pomocą platformy .NET](transform-subclip-video-dotnet-how-to.md)
* [Podcinanie wideo za pomocą interfejsu REST](transform-subclip-video-rest-how-to.md)

## <a name="built-in-presets"></a>Wbudowane ustawienia wstępne

Media Services obsługuje następujące wbudowane ustawienia wstępne kodowania:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) służy do ustawienia wbudowanego ustawienia wstępnego kodowania wejściowego filmu wideo przy użyciu kodera standardowego.

Obecnie obsługiwane są następujące wbudowane ustawienia wstępne:

- **EncoderNamedPreset. AACGoodQualityAudio**: tworzy pojedynczy plik MP4 zawierający tylko dźwięk stereo zakodowany przy 192 kb/s.
- **EncoderNamedPreset. AdaptiveStreaming** (zalecane): obsługuje to kodowanie H. 264 z adaptacyjną szybkością transmisji bitów. Aby uzyskać więcej informacji, zobacz [Autogenerowanie drabiny szybkości transmisji bitów](encode-autogen-bitrate-ladder.md).
- **EncoderNamerPreset. H265AdaptiveStreaming** : podobny do ustawienia wstępnego AdaptiveStreaming, ale używa kodera-dekoder HEVC (H. 265). Tworzy zestaw grupę GOP wyrównanych plików MP4 z dźwiękiem H. 265 wideo i stereo AAC audio. Automatycznie generuje drabinę bitów na podstawie rozdzielczości wejścia, szybkości transmisji bitów i szybkości klatek. Automatycznie generowane ustawienie wstępne nigdy nie będzie przekroczyć rozdzielczości wejściowej. Na przykład, jeśli dane wejściowe to 720, dane wyjściowe pozostaną w najlepszy sposób.
- **EncoderNamedPreset. ContentAwareEncoding**: uwidacznia ustawienia wstępne dla kodowania na potrzeby zawartości H. 264. Mając daną zawartość wejściową, usługa próbuje automatycznie określić optymalną liczbę warstw oraz odpowiednie ustawienia szybkości transmisji bitów i rozdzielczości do dostarczenia przez adaptacyjne przesyłanie strumieniowe. Algorytmy bazowe będą nadal rozwijane z upływem czasu. Dane wyjściowe będą zawierać pliki MP4 z przeplotem wideo i audio. Aby uzyskać więcej informacji, zobacz [kodowanie z uwzględnieniem zawartości](encode-content-aware-concept.md).
- **EncoderNamedPreset. H265ContentAwareEncoding**: udostępnia wstępnie zdefiniowane kodowanie dla HEVC (H. 265) kodowania obsługującego zawartość. Tworzy zestaw pliki MP4 wyrównany do grupę GOP przy użyciu kodowania obsługującego zawartość. Mając daną zawartość wejściową, usługa wykonuje wstępną lekkie analizy zawartości wejściowej i używa wyników do określenia optymalnej liczby warstw, odpowiedniej szybkości transmisji bitów i ustawień rozdzielczości do dostarczenia przez adaptacyjne przesyłanie strumieniowe. To ustawienie wstępne jest szczególnie przydatne w przypadku wideo z niską i średnią złożonością, gdzie pliki wyjściowe będą mieć mniejsze szybkości transmisji bitów, ale z jakością, która nadal zapewnia dobre środowisko dla osób przeglądających. Dane wyjściowe będą zawierać pliki MP4 z przeplotem wideo i audio.
  > [!NOTE]
  > Upewnij się, że używasz **ContentAwareEncoding** not ContentAwareEncodingExperimental, która jest obecnie przestarzała

- **EncoderNamedPreset. H264MultipleBitrate1080p**: tworzy zestaw OŚMIU plików MP4 wyrównanych do grupę GOP, od 6000 KB/s do 400 KB/s oraz stereo AAC audio. Rozdzielczość rozpocznie się o 1080p i przejdzie w dół do 360p.
- **EncoderNamedPreset. H264MultipleBitrate720p**: tworzy zestaw sześciu grupę GOP wyrównanych plików MP4, od 3400 KB/s do 400 KB/s oraz stereo AAC audio. Rozdzielczość zaczyna się od 720 i przejdzie w dół do 360p.
- **EncoderNamedPreset. H264MultipleBitrateSD**: tworzy zestaw pięciu plików MP4 wyrównanych do grupę GOP, od 1600 KB/s do 400 KB/s oraz stereo AAC audio. Rozwiązanie rozpocznie się o 480p i przejdzie w dół do 360p.
- **EncoderNamedPreset. H264SingleBitrate1080p**: tworzy plik MP4, gdzie wideo jest kodowane przy użyciu kodera-dekoder H. 264 o godzinie 6750 KB/s i wysokości obrazu 1080 pikseli, a dźwięk stereo jest zakodowany przy użyciu AAC-LC codec przy 64 KB/s.
- **EncoderNamedPreset. H264SingleBitrate720p**: tworzy plik MP4, gdzie wideo jest kodowane przy użyciu kodera-dekoder H. 264 o godzinie 4500 KB/s i wysokości obrazu 720 pikseli, a dźwięk stereo jest zakodowany przy użyciu AAC-LC codec przy 64 KB/s.
- **EncoderNamedPreset. H264SingleBitrateSD**: tworzy plik MP4, gdzie wideo jest kodowane przy użyciu kodera-dekoder H. 264 o godzinie 2200 KB/s i wysokości obrazu 480 pikseli, a dźwięk stereo jest zakodowany przy użyciu AAC-LC codec przy 64 KB/s.
- **EncoderNamedPreset. H265SingleBitrate720P**: tworzy plik MP4, gdzie wideo jest zakodowane za pomocą kodera-dekoder HEVC (H. 265) o godzinie 1800 KB/s i wysokości obrazu 720 pikseli, a dźwięk stereo jest zakodowany przy użyciu kodera-LC AAC o szybkości 128 kb/s.
- **EncoderNamedPreset. H265SingleBitrate1080p**: tworzy plik MP4, gdzie wideo jest zakodowane za pomocą kodera-dekoder HEVC (H. 265) o godzinie 3500 KB/s i wysokości obrazu 1080 pikseli, a dźwięk stereo jest zakodowany przy użyciu kodera-LC AAC o szybkości 128 kb/s.
- **EncoderNamedPreset. H265SingleBitrate4K**: tworzy plik MP4, gdzie wideo jest zakodowane za pomocą kodera-dekoder HEVC (H. 265) o godzinie 9500 KB/s i wysokości obrazu 2160 pikseli, a dźwięk stereo jest zakodowany przy użyciu kodera-LC AAC o szybkości 128 kb/s.

Aby wyświetlić listę najbardziej aktualnych ustawień predefiniowanych, zobacz [wbudowane ustawienia wstępne, które będą używane do kodowania filmów wideo](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Aby zobaczyć, jak są używane ustawienia wstępne, zobacz [przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) opisuje ustawienia, które mają być używane podczas kodowania wejściowego wideo za pomocą kodera standardowego. Użyj tego ustawienia wstępnego podczas dostosowywania ustawień predefiniowanych transformacji.

#### <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień wstępnych są stosowane następujące zagadnienia:

- Wszystkie wartości wysokości i szerokości dla zawartości AVC muszą być wielokrotnością czterech.
- W Azure Media Services V3 wszystkie szybkości transmisji bitów są w bitach na sekundę. Różni się to od ustawień predefiniowanych za pomocą naszych interfejsów API v2, które używały kilobitów/s jako jednostki. Na przykład, jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobity/sekundę), w v3 zostanie ustawiona wartość 128000 (BITS/s).

### <a name="customizing-presets"></a>Dostosowywanie ustawień wstępnych

Media Services w pełni obsługuje Dostosowywanie wszystkich wartości w ustawieniach wstępnych w celu spełnienia określonych wymagań w zakresie kodowania. Przykłady pokazujące sposób dostosowywania ustawień wstępnych kodera znajdują się na poniższej liście:

#### <a name="examples"></a>Przykłady

- [Dostosowywanie ustawień wstępnych przy użyciu platformy .NET](encode-custom-presets-how-to.md)
- [Dostosowywanie ustawień wstępnych przy użyciu interfejsu wiersza polecenia](encode-custom-preset-cli-how-to.md)
- [Dostosowywanie ustawień wstępnych przy użyciu interfejsu REST](encode-custom-preset-rest-how-to.md)


## <a name="preset-schema"></a>Schemat ustawień wstępnych

W Media Services v3, ustawienia wstępne są jednoznacznie określonymi jednostkami w interfejsie API. Definicję "schemat" tych obiektów można znaleźć w temacie [Open API Specification (lub Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Możesz również wyświetlić wstępnie zdefiniowane definicje (na przykład **StandardEncoderPreset**) w [interfejsie API REST](/rest/api/media/transforms/createorupdate#standardencoderpreset), [zestawie .NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset) (lub Media Services innej dokumentacji dotyczącej zestawu SDK w wersji v3).

## <a name="scaling-encoding-in-v3"></a>Skalowanie kodowania w wersji 3

Aby skalować przetwarzanie multimediów, zobacz [skalowanie przy użyciu interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).
W przypadku kont utworzonych w programie przy użyciu wersji **2020-05-01** interfejsu API lub Azure Portal, skalowanie i jednostki zarezerwowane multimediów nie są już wymagane. Skalowanie będzie automatyczne i obsługiwane przez usługę wewnętrznie.

## <a name="billing"></a>Rozliczenia

W Media Services nie są naliczane opłaty za anulowane lub błędne zadania. Na przykład zadanie, które osiągnęło postęp 50% i anulowane, nie jest rozliczane o 50% minut zadań. Opłata jest naliczana tylko za ukończone zadania.

Aby uzyskać więcej informacji, zobacz [cennik](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

* [Przekazywanie, kodowanie i przesyłanie strumieniowe przy użyciu Media Services](stream-files-tutorial-with-api.md).
* [Koduj z adresu URL HTTPS przy użyciu wbudowanych ustawień wstępnych](job-input-from-http-how-to.md).
* [Koduj plik lokalny przy użyciu wbudowanych ustawień wstępnych](job-input-from-local-file-how-to.md).
* [Utwórz niestandardowe ustawienie wstępne, aby określić odpowiednie wymagania dotyczące scenariusza lub urządzenia](encode-custom-presets-how-to.md).
