---
title: Ustawienie wstępne dla kodowania obsługującego zawartość
description: W tym artykule omówiono kodowanie oparte na zawartości w Microsoft Azure Media Services v3.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 8b48c6b0ef84458fa54692994d8e295d25114dd8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111933"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Użyj ustawienia wstępnego kodowania obsługującego zawartość, aby znaleźć optymalną szybkość transmisji bitów dla danego rozwiązania

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Aby przygotować zawartość do dostarczenia przez [szybkość przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), należy zakodować wideo przy użyciu wielu szybkości transmisji bitów (wysokiej lub niskiej). Zapewnia to płynne obniżenie jakości, ponieważ zmniejsza się szybkość transmisji bitów, więc jest to rozdzielczość wideo. Takie wiele kodowania bitów korzysta z tak zwanej drabinki kodowania — tabeli rozdzielczości i szybkości transmisji bitów, zapoznaj się z Media Services [wbudowanych ustawień wstępnych kodowania](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Należy pamiętać o przetwarzanej zawartości i dostosowaniu i dostosowywaniu drabiny kodowania do złożoności poszczególnych filmów wideo. W każdej rozdzielczości istnieje szybkość transmisji bitów, która nie jest Perceptive — koder działa z optymalną szybkością transmisji bitów. Następnym poziomem optymalizacji jest wybranie rozwiązań opartych na zawartości — na przykład wideo prezentacji programu PowerPoint nie jest korzystne w przypadku przechodzenia poniżej 720. Następnie można wykonać zadanie kodera w celu zoptymalizowania ustawień dla każdego zrzutu w filmie wideo. 

[Adaptacyjne ustawienia wstępne przesyłania strumieniowego](encode-autogen-bitrate-ladder.md) firmy Microsoft częściowo odnoszą się do problemu zmienności jakości i rozdzielczości źródłowych filmów wideo. Nasi klienci mają zróżnicowaną zawartość, kilka o godzinie 1080p, inne w firmie 720, a kilka w SD i niższych rozdzielczościach. Ponadto nie cała zawartość źródłowa to Mezzanine o wysokiej jakości — od kliszy i TV Studios. Adaptacyjne ustawienie wstępne przesyłania strumieniowego eliminuje te problemy, upewniając się, że Drabinka szybkości transmisji nigdy nie przekracza rozdzielczości ani średniej szybkości transmisji bitów danych wejściowych Mezzanine. Jednak to ustawienie wstępne nie bada właściwości źródłowych niż rozdzielczość i szybkość transmisji bitów.

## <a name="the-content-aware-encoding"></a>Kodowanie z uwzględnieniem zawartości

Ustawienia wstępne kodowania z obsługą zawartości rozszerzają mechanizm przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów, włączając logikę niestandardową, która umożliwia koderowi wyszukiwanie optymalnej szybkości transmisji bitów dla danego rozwiązania, ale bez konieczności przeprowadzania obszernej analizy obliczeniowej. To ustawienie wstępne tworzy zestaw grupę GOP wyrównanych pliki MP4. Mając daną zawartość wejściową, usługa wykonuje wstępną lekkie analizy zawartości wejściowej i używa wyników do określenia optymalnej liczby warstw, odpowiedniej szybkości transmisji bitów i ustawień rozdzielczości do dostarczenia przez adaptacyjne przesyłanie strumieniowe. To ustawienie wstępne jest szczególnie przydatne w przypadku wideo z niską i średnią złożonością, gdzie pliki wyjściowe będą mieć mniejszą szybkość transmisji bitów niż adaptacyjne ustawienie wstępne przesyłania strumieniowego, ale z jakością, która nadal zapewnia dobre doświadczenie dla osób przeglądających. Dane wyjściowe będą zawierać pliki MP4 z przeplotem wideo i audio

Poniższe przykładowe wykresy przedstawiają porównanie przy użyciu metryk jakości, takich jak [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) i [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Źródło zostało utworzone przez połączenie krótkich klipów o wysokiej złożoności z filmów i programów telewizyjnych, które są przeznaczone do podkreślania kodera. Zgodnie z definicją, to ustawienie wstępne daje wyniki, które różnią się w zależności od zawartości — oznacza to również, że w przypadku niektórych zawartości może nie być znacząca Redukcja szybkości transmisji bitów lub poprawy jakości.

![Krzywa stopnia zniekształcenia (RD) przy użyciu PSNR](media/encode-content-aware-concept/msrv1.png)

**Rysunek 1: Krzywa stopnia zniekształcenia (RD) używająca metryki PSNR dla źródła o dużej złożoności**

![Krzywa stopnia zniekształcenia (RD) przy użyciu VMAF](media/encode-content-aware-concept/msrv2.png)

**Rysunek 2: Krzywa stopnia zniekształcenia (RD) używająca metryki VMAF dla źródła o dużej złożoności**

Poniżej znajdują się wyniki dla innej kategorii zawartości źródłowej, w której koder był w stanie stwierdzić, że dane wejściowe były słabej jakości (wiele artefaktów kompresji z powodu niskiej szybkości transmisji bitów). Należy pamiętać, że w przypadku ustawienia wstępnego z uwzględnieniem zawartości koder zdecydował się utworzyć tylko jedną warstwę wyjściową — z niską szybkością transmisji bitów, dzięki czemu większość klientów będzie mogła odtworzyć strumień bez zawieszania się.

![Krzywa usług pulpitu zdalnego przy użyciu PSNR](media/encode-content-aware-concept/msrv3.png)

**Rysunek 3: Krzywa usług pulpitu zdalnego wykorzystująca PSNR dla danych wejściowych o niskiej jakości (o godzinie 1080p)**

![Krzywa usług pulpitu zdalnego przy użyciu VMAF](media/encode-content-aware-concept/msrv4.png)

**Rysunek 4: Krzywa usług pulpitu zdalnego wykorzystująca VMAF dla danych wejściowych o niskiej jakości (o godzinie 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Jak używać ustawień predefiniowanych kodowania opartych na zawartości 

Można tworzyć przekształcenia, które używają tego ustawienia wstępnego w następujący sposób. 

Zobacz sekcję [następne kroki](#next-steps) , aby poznać samouczki korzystające z wyjścia transformacji. Element zawartości wyjściowej można dostarczyć z Media Services punktów końcowych przesyłania strumieniowego w protokołach, takich jak MPEG-myślnik i HLS (jak pokazano w samouczkach).

> [!NOTE]
> Upewnij się, że używasz ustawienia wstępnego **ContentAwareEncoding** , które nie jest ContentAwareEncodingExperimental.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Opłaty za zadania kodowania przy użyciu `ContentAwareEncoding` ustawienia wstępnego są rozliczane na podstawie minut danych wyjściowych. 
  
## <a name="next-steps"></a>Następne kroki

* [Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo z Media Services v3](stream-files-tutorial-with-api.md)
* [Samouczek: Kodowanie pliku zdalnego na podstawie adresu URL i przesyłanie strumieniowe wideo — REST](stream-files-tutorial-with-rest.md)
* [Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — interfejs wiersza polecenia](stream-files-cli-quickstart.md)
* [Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — .NET](stream-files-dotnet-quickstart.md)
* [Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — Node.js](stream-files-nodejs-quickstart.md)
