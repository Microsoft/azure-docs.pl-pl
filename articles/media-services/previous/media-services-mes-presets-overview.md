---
title: Ustawienia wstępne zadań dla Media Encoder Standard (MES) | Microsoft Docs
description: Temat zawiera omówienie przykładowych ustawień wstępnych zdefiniowanych przez usługę dla Media Encoder Standard (MES).
author: Juliako
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: aa0514834d1619bbbae4501fe6b1af16d7964ff7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89261479"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Przykładowe ustawienia wstępne dla Media Encoder Standard (MES)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

**Media Encoder Standard** definiuje zestaw wstępnie zdefiniowanych predefiniowanych ustawień kodowania systemu, których można użyć podczas tworzenia zadań kodowania. Zaleca się użycie ustawienia wstępnego "adaptacyjnego przesyłania strumieniowego", jeśli chcesz zakodować wideo do przesyłania strumieniowego za pomocą Media Services. Po określeniu tego ustawienia wstępnego Media Encoder Standard [automatycznie wygeneruje drabinę o szybkości transmisji bitów](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Tworzenie niestandardowych ustawień wstępnych z przykładów
Media Services w pełni obsługuje Dostosowywanie wszystkich wartości w ustawieniach wstępnych w celu spełnienia określonych wymagań w zakresie kodowania. Jeśli musisz dostosować ustawienie wstępne kodowania, należy zacząć od jednego z poniższych ustawień wstępnych systemu, które są podane w tej sekcji jako szablonu konfiguracji niestandardowej. Aby dowiedzieć się, co oznacza każdy element w tych ustawieniach wstępnych, i prawidłowe wartości dla każdego elementu, zobacz temat [schemat Media Encoder Standard](media-services-mes-schema.md) .  
  
> [!NOTE]
>  W przypadku użycia ustawienia wstępnego dla kodowania 4K należy uzyskać `S3` Typ jednostki zarezerwowanej. Aby uzyskać więcej informacji, zobacz [Jak skalować kodowanie](./media-services-scale-media-processing-overview.md).  

#### <a name="video-rotation-default-setting-in-presets"></a>Domyślne ustawienie rotacji wideo w ustawieniach wstępnych:
Podczas pracy z Media Encoder Standard obrót wideo jest domyślnie włączony. Jeśli film wideo został zarejestrowany na urządzeniu przenośnym w trybie pionowym, te ustawienia wstępne spowodują obrócenie ich do trybu poziomego przed kodowaniem.
 
## <a name="available-presets"></a>Dostępne ustawienia wstępne: 

 [Wielokrotna H264 wiele szybkości transmisji bitów 1080P Audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) produkuje zestaw 8 grupę gopych plików MP4, od 6000 KB/s do 400 KB/s i AAC 5,1 audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) tworzy zestaw 8-grupę gopych plików MP4, od 6000 KB/s do 400 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 16x9 dla systemu iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) tworzy zestaw 8 grupę GOP wyrównanych plików MP4 z zakresu od 8500 KB/s do 200 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 16X9 SD 5,1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) tworzy zestaw 5 grupę GOP wyrównanych plików MP4, od 1900 KB/s do 400 KB/s oraz AAC 5,1 audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 16X9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) tworzy zestaw 5 grupę GOP wyrównanych plików MP4, od 1900 KB/s do 400 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 4 5,1 KB](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) /s tworzy zestaw 12 grupę gopych plików MP4, od 20000 KB/s do 1000 i AAC 5,1 audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) tworzy zestaw 12 grupę gopych plików MP4 z zakresu od 20000 KB/s do 1000 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 4x3 dla systemu iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) tworzy zestaw 8 grupę GOP wyrównanych plików MP4 z zakresu od 8500 KB/s do 200 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 4X3 SD 5,1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) tworzy zestaw 5 grupę GOP wyrównanych plików MP4, od 1600 KB/s do 400 KB/s oraz AAC 5,1 audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 4X3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) tworzy zestaw 5 grupę GOP wyrównanych plików MP4, od 1600 KB/s do 400 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 wiele szybkości transmisji bitów 720 Audio 5,1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) tworzy zestaw 6 grupę gopych plików MP4, od 3400 KB/s do 400 KB/s i AAC 5,1 audio.  
  
 [Wielokrotna H264 z wieloma szybkościami transmisji bitów](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) , tworzy zestaw 6 grupę gopych plików MP4, od 3400 KB/s do 400 KB/s, oraz stereo AAC audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów 1080P Audio 5,1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) tworzy jeden plik MP4 z szybkością transmisji bitów 6750 KB/s i AAC 5,1 audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) tworzy jeden plik MP4 z szybkością transmisji bitów 6750 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów 5,1 4](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) KB/s tworzy jeden plik MP4 z szybkością transmisji bitów 18000 KB i AAC 5,1 audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów](media-services-mes-preset-H264-Single-Bitrate-4K.md) to jeden plik MP4 z szybkością transmisji bitów 18000 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów 4X3 SD 5,1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) generuje pojedynczy plik MP4 z szybkością transmisji bitów 1800 KB/s i AAC 5,1 audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów 4X3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) tworzy jeden plik MP4 z szybkością transmisji bitów 1800 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów 16X9 SD 5,1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) generuje pojedynczy plik MP4 z szybkością transmisji bitów 2200 KB/s i AAC 5,1 audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów 16X9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) tworzy jeden plik MP4 z szybkością transmisji bitów 2200 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 audio 720 o pojedynczej szybkości transmisji bitów 5,1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) tworzy jeden plik MP4 z szybkością transmisji bitów 4500 KB/s i AAC 5,1 audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów dla systemu Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) zapewnia jeden plik MP4 z szybkością transmisji bitów 2000 KB/s i AAC stereo.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów](media-services-mes-preset-H264-Single-Bitrate-720p.md) daje pojedynczy plik MP4 z szybkością transmisji bitów 4500 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 o pojedynczej szybkości transmisji bitów SD dla systemu Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) tworzy jeden plik MP4 z szybkością transmisji bitów 500 KB/s oraz stereo AAC audio.  
  
 [Wielokrotna H264 pojedynczej szybkości transmisji bitów SD dla systemu Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) tworzy pojedynczy plik MP4 z szybkością transmisji bitów 56 KB/s oraz stereo AAC audio.  
  
 Aby uzyskać więcej informacji dotyczących koderów Media Services, zobacz [kodowanie na żądanie z Azure Media Services](./media-services-encode-asset.md).
