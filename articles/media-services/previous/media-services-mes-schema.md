---
title: Schemat Media Encoder Standard | Microsoft Docs
description: W tym artykule opisano niektóre elementy i typy schematu XML, na których bazują ustawienia wstępne Media Encoder Standard.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f82e0c3f76dba05c3404b11e07c7130119ce0b9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015662"
---
# <a name="media-encoder-standard-schema"></a>Schemat usługi Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

W tym artykule opisano niektóre elementy i typy schematu XML, na których bazują [Ustawienia wstępne Media Encoder Standard](media-services-mes-presets-overview.md) . Artykuł zawiera wyjaśnienie elementów i ich prawidłowe wartości.  

## <a name="preset-root-element"></a><a name="Preset"></a> Ustawienie wstępne (element główny)
Definiuje ustawienie wstępne kodowania.  

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Kodowanie** |[Kodowanie](media-services-mes-schema.md#Encoding) |Element główny wskazuje, że źródła danych wejściowych są kodowane. |
| **Dane wyjściowe** |[Dane wyjściowe](media-services-mes-schema.md#Output) |Kolekcja żądanych plików wyjściowych. |
| **Rozciąganie**<br/>minOccurs = "0"<br/>wartość domyślna = "AutoSize|XS: ciąg|Kontrolowanie wyjściowego rozmiaru ramki wideo, dopełnienia, piksela lub wyświetlania współczynnika proporcji. Funkcja **rozciągamode** może być jedną z następujących wartości: **none**, **AutoSize** (default) lub **Autodopasowanie**.<br/><br/>**Brak**: ściśle postępuj zgodnie z rozdzielczością wyjściową (na przykład **Szerokość** i **wysokość** w ustawieniu wstępnym) bez uwzględnienia współczynnika proporcji pikseli lub współczynnika proporcji obrazu wejściowego. Zalecane w scenariuszach takich jak [przycinanie](media-services-crop-video.md), gdzie wyjściowe wideo ma inny współczynnik proporcji w porównaniu do danych wejściowych. <br/><br/>**Autodopasowanie rozmiaru**: Rozdzielczość wyjściowa będzie mieści się w oknie (szerokość * wysokość) określona przez ustawienie wstępne. Koder generuje jednak wyjściowy film wideo o współczynniku proporcji kwadratu (1:1). W związku z tym można zastąpić szerokość wyjściową lub wysokość wyjściową, aby dopasować współczynnik proporcji danych wejściowych bez wypełnienia. Na przykład, jeśli dane wejściowe to 1920 x 1080, a ustawienia wstępne kodowania pytają o 1280x1280, wartość Height w ustawieniu wstępnym jest zastępowana, a dane wyjściowe będą znajdować się w 1280x720, co zachowuje współczynnik proporcji danych 16:9. <br/><br/>**Autodopasowanie**: w razie potrzeby Uzupełnij wyjściowy film wideo (z letterbox lub pillarbox) w celu zagwarantowania pożądanej rozdzielczości wyjściowej, przy zapewnieniu, że aktywny region wideo w danych wyjściowych ma ten sam współczynnik proporcji co dane wejściowe. Załóżmy na przykład, że dane wejściowe to 1920 x 1080, a ustawienie wstępne kodowania monituje o 1280x1280. Następnie wyjściowy film wideo będzie miał wartość 1280x1280, ale będzie zawierać wewnętrzny prostokąt 1280x720 z "aktywnego wideo" z współczynnik proporcji 16:9 i letterbox regiony 280 pikseli na górze i u dołu. W innym przykładzie, jeśli dane wejściowe to 1440x1080, a dla ustawienia wstępnego kodowania zostanie wyświetlony monit o 1280x720, dane wyjściowe będą znajdować się na 1280x720, który zawiera wewnętrzny prostokąt 960x720 przy współczynniku proporcji 4:3, a słupki pola filaru 160 pikseli w lewo i w prawo. 

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Wersja**<br/><br/> Wymagane |**XS: decimal** |Wersja wstępna. Obowiązują następujące ograniczenia: xs: fractionDigits Value = "1" i xs: minInclusive Value = "1" na przykład, **Version = "1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a> Kody
Zawiera sekwencję następujących elementów:  

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Ustawienia dotyczące kodowania H. 264 wideo. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Ustawienia kodowania audio AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Ustawienia dla obrazu BMP. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Ustawienia dla obrazu PNG. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Ustawienia dla obrazu jpg. |

## <a name="h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs = "0" |**XS: wartość logiczna** |Obecnie obsługiwane jest tylko kodowanie jednokrotne. |
| **KeyFrameInterval**<br/><br/> minOccurs = "0"<br/><br/> **wartość domyślna = "00:00:02"** |**XS: godzina** |Określa stałe odstępy między ramkami IDR w jednostkach sekund. Określany również jako czas trwania grupę GOP. Zobacz **SceneChangeDetection** , aby kontrolować, czy koder może odbiegać od tej wartości. |
| **SceneChangeDetection**<br/><br/> minOccurs = "0"<br/><br/> wartość domyślna = "false" |**XS: wartość logiczna** |W przypadku ustawienia wartości true koder próbuje wykryć zmiany sceny w wideo i wstawia ramkę IDR. |
| **Stopień**<br/><br/> minOccurs = "0"<br/><br/> wartość domyślna = "Zrównoważona" |**XS: ciąg** |Steruje handlem między szybkością kodowania a jakością wideo. Może to być jedna z następujących wartości: **szybkość**, **zrównoważona** lub **jakość**<br/><br/> Domyślne: **zrównoważone** |
| **Syncmode**<br/><br/> minOccurs = "0" | |Funkcja zostanie udostępniona w przyszłej wersji. |
| **H264Layers**<br/><br/> minOccurs = "0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Kolekcja wyjściowych warstw wideo. |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**XS: ciąg** | Gdy dane wejściowe nie zawierają wideo, możesz wymusić, aby koder mógł wstawić czarną ścieżkę wideo. W tym celu należy użyć warunku = "InsertBlackIfNoVideoBottomLayerOnly" (w celu wstawienia filmu wideo tylko o najmniejszej szybkości transmisji bitów) lub warunku = "InsertBlackIfNoVideo" (w celu wstawienia wideo na wszystkie szybkości transmisji bitów). Aby uzyskać więcej informacji, zobacz [ten](media-services-advanced-encoding-with-mes.md#no_video) artykuł.|

## <a name="h264layers"></a><a name="H264Layers"></a> H264Layers

Domyślnie, Jeśli wysyłasz dane wejściowe do kodera, który zawiera tylko dźwięk, a nie wideo, wyjściowy element zawartości zawiera tylko pliki z danymi audio. Niektóre odtwarzacze mogą nie być w stanie obsłużyć takich strumieni wyjściowych. Można użyć ustawienia atrybutu H264Video's **InsertBlackIfNoVideo** , aby wymusić, że koder dodaje ścieżkę wideo do danych wyjściowych w tym scenariuszu. Aby uzyskać więcej informacji, zobacz [ten](media-services-advanced-encoding-with-mes.md#no_video) artykuł.
              
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = "0" maxOccurs = "niepowiązane" |[H264Layer](media-services-mes-schema.md#H264Layer) |Kolekcja warstw wielokrotna H264. |

## <a name="h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> Limity wideo są oparte na wartościach opisanych w tabeli [poziomów wielokrotna H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) .  
> 
> 

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> Domyślnie = "Auto" |**XS: ciąg** |Może być jedną z następujących wartości **typu xs: String** : **Auto,** **Baseline**, **Main**, **High**. |
| **Poziomie**<br/><br/> minOccurs = "0"<br/><br/> Domyślnie = "Auto" |**XS: ciąg** | |
| **Multimedia**<br/><br/> minOccurs = "0" |**XS: int** |Szybkość transmisji bitów użyta dla tej warstwy wideo określona w KB/s. |
| **MaxBitrate**<br/><br/> minOccurs = "0" |**XS: int** |Maksymalna szybkość transmisji bitów użyta dla tej warstwy wideo określona w KB/s. |
| **BufferWindow**<br/><br/> minOccurs = "0"<br/><br/> wartość domyślna = "00:00:05" |**XS: godzina** |Długość buforu wideo. |
| **Width**<br/><br/> minOccurs = "0" |**XS: int** |Szerokość wyjściowej ramki wideo (w pikselach).<br/><br/> Obecnie należy określić szerokość i wysokość. Szerokość i wysokość muszą być parzyste liczby. |
| **Height**<br/><br/> minOccurs = "0" |**XS: int** |Wysokość wyjściowej ramki wideo (w pikselach).<br/><br/> Obecnie należy określić szerokość i wysokość. Szerokość i wysokość muszą być parzyste liczby.|
| **BFrames**<br/><br/> minOccurs = "0" |**XS: int** |Liczba klatek między ramkami referencyjnymi. |
| **ReferenceFrames**<br/><br/> minOccurs = "0"<br/><br/> wartość domyślna = "3" |**XS: int** |Liczba ramek odwołań w grupę GOP. |
| **Entropia**<br/><br/> minOccurs = "0"<br/><br/> wartość domyślna = "CABAC" |**XS: ciąg** |Może to być jedna z następujących wartości: **CABAC** i **Cavlc**. |
| **Szybkości**<br/><br/> minOccurs = "0" |Liczba wymierna |Określa szybkość klatek wyjściowego wideo. Użyj wartości domyślnej "0/1", aby umożliwić koderowi używanie tej samej szybkości klatek co wejściowy film wideo. Oczekiwane wartości mogą być typowymi szybkościami klatek wideo. Jednak dozwolone są wszystkie prawidłowe racjonalne uzasadnienie. Na przykład 1/1 może być 1 fps i jest prawidłowy.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> -24000/1001 (23,976 FPS)<br/><br/> -25/1 (25 fps)<br/><br/>  -30/1 (30 fps)<br/><br/> -30000/1001 (29,97 FPS) <br/> <br/>**Uwaga** Jeśli tworzysz niestandardowe ustawienie wstępne dla kodowania o różnej szybkości transmisji bitów, wszystkie warstwy ustawienia wstępnego **muszą** używać tej samej wartości szybkości klatek.|
| **AdaptiveBFrame**<br/><br/> minOccurs = "0" |**XS: wartość logiczna** |Kopiowanie z usługi Azure Media Encoder |
| **Wycinki**<br/><br/> minOccurs = "0"<br/><br/> wartość domyślna = "0" |**XS: int** |Określa liczbę wycinków, do których jest podzielona ramka. Zaleca się użycie domyślnego. |

## <a name="aacaudio"></a><a name="AACAudio"></a> AACAudio
 Zawiera sekwencję następujących elementów i grup.  

 Aby uzyskać więcej informacji na temat AAC, zobacz [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> wartość domyślna = "AACLC" |**XS: ciąg** |Może to być jedna z następujących wartości: **AACLC**, **HEAACV1** lub **HEAACV2**. |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**XS: ciąg** |Aby wymusić generowanie przez koder elementu zawartości zawierającej cichą ścieżkę audio, gdy dane wejściowe nie zawierają dźwięku, określ wartość "InsertSilenceIfNoAudio".<br/><br/> Domyślnie, Jeśli wysyłasz dane wejściowe do kodera, który zawiera tylko wideo, a nie dźwięk, wówczas wyjściowy element zawartości zawiera pliki, które zawierają tylko dane wideo. Niektóre odtwarzacze mogą nie być w stanie obsłużyć takich strumieni wyjściowych. Możesz użyć tego ustawienia, aby wymusić, aby koder mógł dodać cichą ścieżkę audio do danych wyjściowych w tym scenariuszu. |

### <a name="groups"></a>Grupy

| Odwołanie | Opis |
| --- | --- |
| [Audio](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs = "0" |Zobacz opis obiektu [audio](media-services-mes-schema.md#AudioGroup) , aby poznać odpowiednią liczbę kanałów, częstotliwość próbkowania i szybkość transmisji bitów, które można ustawić dla każdego profilu. |

## <a name="audiogroup"></a><a name="AudioGroup"></a> Audio
Aby uzyskać szczegółowe informacje o tym, jakie wartości są prawidłowe dla każdego profilu, zobacz poniższą tabelę "Szczegóły kodera audio".  

### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Kanały**<br/><br/> minOccurs = "0" |**XS: int** |Liczba zakodowanych kanałów audio. Dostępne są następujące prawidłowe opcje: 1, 2, 5, 6, 8.<br/><br/> Wartość domyślna: 2. |
| **SamplingRate**<br/><br/> minOccurs = "0" |**XS: int** |Częstotliwość próbkowania audio, określona w Hz. |
| **Multimedia**<br/><br/> minOccurs = "0" |**XS: int** |Szybkość transmisji bitów użyta podczas kodowania dźwięku, określona w KB/s. |

### <a name="audio-codec-details"></a>Szczegóły kodera dźwiękowego audio

Koder-dekoder audio|Szczegóły  
-----------------|---  
**AACLC** |1.<br/><br/> -11025:8 &lt; = szybkość transmisji bitów &lt; 16<br/><br/> -12000:8 &lt; = szybkość transmisji bitów &lt; 16<br/><br/> -16000:8 &lt; = szybkość transmisji bitów &lt; 32<br/><br/>-22050:24 &lt; = szybkość transmisji bitów &lt; 32<br/><br/> -24000:24 &lt; = szybkość transmisji bitów &lt; 32<br/><br/> -32000:32 &lt; = szybkość transmisji bitów &lt; = 192<br/><br/> -44100:56 &lt; = szybkość transmisji bitów &lt; = 288<br/><br/> -48000:56 &lt; = szybkość transmisji bitów &lt; = 288<br/><br/> -88200:128 &lt; = szybkość transmisji bitów &lt; = 288<br/><br/> -96000:128 &lt; = szybkość transmisji bitów &lt; = 288<br/><br/> 2.<br/><br/> -11025:16 &lt; = szybkość transmisji bitów &lt; 24<br/><br/> -12000:16 &lt; = szybkość transmisji bitów &lt; 24<br/><br/> -16000:16 &lt; = szybkość transmisji bitów &lt; 40<br/><br/> -22050:32 &lt; = szybkość transmisji bitów &lt; 40<br/><br/> -24000:32 &lt; = szybkość transmisji bitów &lt; 40<br/><br/> -32000:40 &lt; = szybkość transmisji bitów &lt; = 384<br/><br/> -44100:96 &lt; = szybkość transmisji bitów &lt; = 576<br/><br/> -48000:96 &lt; = szybkość transmisji bitów &lt; = 576<br/><br/> -88200:256 &lt; = szybkość transmisji bitów &lt; = 576<br/><br/> -96000:256 &lt; = szybkość transmisji bitów &lt; = 576<br/><br/> 5/6:<br/><br/> -32000:160 &lt; = szybkość transmisji bitów &lt; = 896<br/><br/> -44100:240 &lt; = szybkość transmisji bitów &lt; = 1024<br/><br/> -48000:240 &lt; = szybkość transmisji bitów &lt; = 1024<br/><br/> -88200:640 &lt; = szybkość transmisji bitów &lt; = 1024<br/><br/> -96000:640 &lt; = szybkość transmisji bitów &lt; = 1024<br/><br/> 8.<br/><br/> -32000:224 &lt; = szybkość transmisji bitów &lt; = 1024<br/><br/> -44100:384 &lt; = szybkość transmisji bitów &lt; = 1024<br/><br/> -48000:384 &lt; = szybkość transmisji bitów &lt; = 1024<br/><br/> -88200:896 &lt; = szybkość transmisji bitów &lt; = 1024<br/><br/> -96000:896 &lt; = szybkość transmisji bitów &lt; = 1024  
**HEAACV1** |1.<br/><br/> -22050: szybkość transmisji bitów = 8<br/><br/> -24000:8 &lt; = szybkość transmisji bitów &lt; = 10<br/><br/> -32000:12 &lt; = szybkość transmisji bitów &lt; = 64<br/><br/> -44100:20 &lt; = szybkość transmisji bitów &lt; = 64<br/><br/> -48000:20 &lt; = szybkość transmisji bitów &lt; = 64<br/><br/> -88200: szybkość transmisji bitów = 64<br/><br/> 2.<br/><br/> -32000:16 &lt; = szybkość transmisji bitów &lt; = 128<br/><br/> -44100:16 &lt; = szybkość transmisji bitów &lt; = 128<br/><br/> -48000:16 &lt; = szybkość transmisji bitów &lt; = 128<br/><br/> -88200:96 &lt; = szybkość transmisji bitów &lt; = 128<br/><br/> -96000:96 &lt; = szybkość transmisji bitów &lt; = 128<br/><br/> 5/6:<br/><br/> -32000:64 &lt; = szybkość transmisji bitów &lt; = 320<br/><br/> -44100:64 &lt; = szybkość transmisji bitów &lt; = 320<br/><br/> -48000:64 &lt; = szybkość transmisji bitów &lt; = 320<br/><br/> -88200:256 &lt; = szybkość transmisji bitów &lt; = 320<br/><br/> -96000:256 &lt; = szybkość transmisji bitów &lt; = 320<br/><br/> 8.<br/><br/> -32000:96 &lt; = szybkość transmisji bitów &lt; = 448<br/><br/> -44100:96 &lt; = szybkość transmisji bitów &lt; = 448<br/><br/> -48000:96 &lt; = szybkość transmisji bitów &lt; = 448<br/><br/> -88200:384 &lt; = szybkość transmisji bitów &lt; = 448<br/><br/> -96000:384 &lt; = szybkość transmisji bitów &lt; = 448  
**HEAACV2** |2.<br/><br/> -22050:8 &lt; = szybkość transmisji bitów &lt; = 10<br/><br/> -24000:8 &lt; = szybkość transmisji bitów &lt; = 10<br/><br/> -32000:12 &lt; = szybkość transmisji bitów &lt; = 64<br/><br/> -44100:20 &lt; = szybkość transmisji bitów &lt; = 64<br/><br/> -48000:20 &lt; = szybkość transmisji bitów &lt; = 64<br/><br/> -88200:64 &lt; = szybkość transmisji bitów &lt; = 64  
  
## <a name="clip"></a><a name="Clip"></a> Obiekt
### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Rozpoczęcia** |**XS: czas trwania** |Określa godzinę rozpoczęcia prezentacji. Wartość StartTime musi odpowiadać bezwzględnym znacznikom czasu wejściowego wideo. Na przykład, jeśli pierwsza klatka wejściowego filmu wideo ma sygnaturę czasową 12:00:10.000, wartość StartTime powinna wynosić co najmniej 12:00:10.000 lub większą. |
| **Czas trwania** |**XS: czas trwania** |Określa czas trwania prezentacji (na przykład wygląd nakładki w filmie wideo). |

## <a name="output"></a><a name="Output"></a> Rozdzielczości
### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Nazwa pliku** |**XS: ciąg** |Nazwa pliku wyjściowego.<br/><br/> Można użyć makr opisanych w poniższej tabeli, aby utworzyć nazwy plików wyjściowych. Na przykład:<br/><br/> **"Dane wyjściowe": [{"FileName": "{basename}*{Solution}*{szybkość}. mp4", "format": {"Type": "MP4Format"}}]** |

### <a name="macros"></a>Makra

| Makro | Opis |
| --- | --- |
| **Basename** |Jeśli wykonujesz kodowanie VoD, {basename} to pierwsze 32 znaków właściwości AssetFile.Name pliku podstawowego w elemencie zawartości wejściowej.<br/><br/> Jeśli zasób wejściowy jest archiwum na żywo, a następnie {basename} pochodzi od atrybutów trackname w manifeście serwera. Jeśli przesyłasz zadanie podrzędne podklipu przy użyciu TopBitrate, tak jak w przypadku: "<VideoStream \> TopBitrate</VideoStream \> ", a plik wyjściowy zawiera wideo, a następnie {basename} to pierwsze 32 znaków w ścieżce wideo o najwyższej szybkości transmisji bitów.<br/><br/> Jeśli zamiast tego prześlesz zadanie podrzędne podklipu przy użyciu wszystkich szybkości transmisji bitów wejściowych, takich jak "<VideoStream \> * </VideoStream \> ", a plik wyjściowy zawiera wideo, a {basename} to pierwsze 32 znaków wartości trackname odpowiedniej warstwy wideo. |
| **Wymaga** |Mapuje na "wielokrotna H264" dla wideo i "AAC" dla dźwięku. |
| **Multimedia** |Docelowa szybkość transmisji wideo, jeśli plik wyjściowy zawiera wideo i audio, lub szybkość transmisji bitów audio, jeśli plik wyjściowy zawiera tylko dźwięk. Używana wartość to szybkość transmisji bitów w KB/s. |
| **Ukierunkowan** |Liczba kanałów audio, jeśli plik zawiera dźwięk. |
| **Szerokość** |Szerokość filmu wideo (w pikselach) w pliku wyjściowym, jeśli plik zawiera wideo. |
| **Proporcj** |Wysokość filmu wideo (w pikselach) w pliku wyjściowym, jeśli plik zawiera wideo. |
| **Rozszerzenia** |Dziedziczy po właściwości "Type" pliku wyjściowego. Nazwa pliku wyjściowego ma rozszerzenie, które jest jednym z: "MP4", "TS", "jpg", "png" lub "BMP". |
| **Indeks** |Obowiązkowe dla miniatury. Powinien być obecny tylko raz. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a> Wideo (typ złożony dziedziczy po koderze-dekoder)
### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Początek** |**XS: ciąg** | |
| **Krok** |**XS: ciąg** | |
| **Zakres** |**XS: ciąg** | |
| **PreserveResolutionAfterRotation** |**XS: wartość logiczna** |Szczegółowe wyjaśnienie można znaleźć w następującej sekcji: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Zaleca się użycie flagi **PreserveResolutionAfterRotation** w połączeniu z wartościami rozdzielczości wyrażonymi w postaci wartości procentowych (width = "100%", Height = "100%").  

Domyślnie ustawienia rozpoznawania kodowania (szerokość, wysokość) w ustawieniach wstępnych Media Encoder Standard (MES) są stosowane do filmów wideo z rotacją 0 stopni. Na przykład, jeśli wejściowy film wideo jest 1280x720 z rotacją zero stopni, domyślne ustawienia wstępne zapewniają, że dane wyjściowe mają takie samo rozwiązanie.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Jeśli wejściowe wideo zostało przechwycone z rotacją różną od zera (na przykład w przypadku telefonu smartphone lub tabletu w pionie), wówczas język MES domyślnie stosuje ustawienia kodowanie rozpoznawania (szerokość, wysokość) do wejściowego wideo, a następnie wychwytuje obrót. Na przykład zapoznaj się z poniższym obrazem. Ustawienie wstępne używa szerokości = "100%", Height = "100%", które jest interpretowane jako wymagające, aby dane wyjściowe były 1280 pikseli szerokości i 720 pikseli. Po obróceniu filmu wideo zmniejsza ono obraz, aby zmieścił się w tym oknie, co prowadzi do obszarów pola filar po lewej stronie i po prawej stronie.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Alternatywnie można użyć flagi **PreserveResolutionAfterRotation** i ustawić ją na wartość "true&quot; (domyślna to &quot;false"). Tak więc, jeśli ustawienie ma wartość Width = "100%", Height = "100%" i PreserveResolutionAfterRotation ma wartość "true", wejściowy film wideo, który jest 1280 pikseli o szerokości i rozdzielczości 720 pikseli 90, a wielkość liter na pikselach jest wysoka. Zapoznaj się z poniższym obrazem:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a> Format grupy (Grupa)
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Element

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Height**<br/><br/> minOccurs = "0" |**XS: int** | |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**XS: ciąg** | |

## <a name="pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Element

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Height**<br/><br/> minOccurs = "0" |**XS: int** | |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**XS: ciąg** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Element

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Height**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Jakość**<br/><br/> minOccurs = "0" |**XS: int** |Prawidłowe wartości: 1 (najgorszy) — 100 (Najlepsza) |

### <a name="attributes"></a>Atrybuty

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **Warunek** |**XS: ciąg** | |

## <a name="pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = "0" maxOccurs = "niepowiązane" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = "0" maxOccurs = "niepowiązane" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = "0" maxOccurs = "niepowiązane" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (typ złożony dziedziczy z wideo)
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Warstwy PNG |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (typ złożony dziedziczy z wideo)
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Warstwy PNG |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (typ złożony dziedziczy z wideo)
### <a name="elements"></a>Elementy

| Nazwa | Typ | Opis |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Warstwy PNG |

## <a name="examples"></a>Przykłady
Zobacz przykłady ustawień wstępnych XML, które są tworzone w oparciu o ten schemat, zobacz [Ustawienia wstępne zadań dla MES (Media Encoder standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

