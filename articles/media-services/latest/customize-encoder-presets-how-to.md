---
title: Kodowanie przekształcenia niestandardowego przy użyciu Media Services v3 .NET — Azure | Microsoft Docs
description: W tym temacie przedstawiono sposób kodowania niestandardowego przekształcenia przy użyciu programu .NET w programie Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2bcd5f0e6229c4130dddb48c1a20de1c711c6fcf
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519885"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Jak kodować przy użyciu przekształcenia niestandardowego — .NET

Przy kodowaniu przy użyciu Azure Media Services można szybko rozpocząć pracę z jednym z zalecanych wbudowanych ustawień predefiniowanych na podstawie najlepszych rozwiązań branżowych, jak pokazano w samouczku [pliki przesyłania strumieniowego](stream-files-tutorial-with-api.md) . Możesz również utworzyć niestandardowe ustawienie wstępne, aby określić wymagania dotyczące określonego scenariusza lub urządzenia.

## <a name="considerations"></a>Kwestie do rozważenia

Podczas tworzenia niestandardowych ustawień wstępnych są stosowane następujące zagadnienia:

* Wszystkie wartości wysokości i szerokości dla zawartości AVC muszą być wielokrotnością 4.
* W Azure Media Services V3 wszystkie szybkości transmisji bitów są w bitach na sekundę. Różni się to od ustawień predefiniowanych za pomocą naszych interfejsów API v2, które używały kilobitów/s jako jednostki. Na przykład, jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobity/sekundę), w v3 zostanie ustawiona wartość 128000 (BITS/s).

## <a name="prerequisites"></a>Wymagania wstępne 

[Tworzenie konta usługi Media Services](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Pobieranie przykładu

Sklonuj repozytorium GitHub zawierające pełną przykładową platformę .NET Core do maszyny przy użyciu następującego polecenia:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Przykład niestandardowego ustawienia wstępnego znajduje się w folderze [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) .

## <a name="create-a-transform-with-a-custom-preset"></a>Tworzenie przekształcenia z niestandardowym ustawieniem wstępnym 

Podczas tworzenia nowej [transformacji](https://docs.microsoft.com/rest/api/media/transforms)należy określić, co chcesz utworzyć jako dane wyjściowe. Wymagany parametr to obiekt [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), jak pokazano w poniższym kodzie. Każdy obiekt **TransformOutput** zawiera element **Preset**. **Ustawienie wstępne** zawiera instrukcje krok po kroku dotyczące operacji przetwarzania wideo i/lub audio, które mają być używane do generowania żądanych **TransformOutput**. Poniższy **TransformOutput** tworzy niestandardowe ustawienia kodera-dekoder i wyjście warstwy.

Podczas tworzenia obiektu [Transform](https://docs.microsoft.com/rest/api/media/transforms) należy najpierw sprawdzić, czy taki obiekt już istnieje, używając metody **Get**, tak jak przedstawiono w poniższym kodzie. W Media Services v3 metody **Get** na jednostkach zwracają **wartość null** , jeśli jednostka nie istnieje (sprawdzanie bez uwzględniania wielkości liter w nazwie).

### <a name="example"></a>Przykład

W poniższym przykładzie zdefiniowano zestaw danych wyjściowych, które mają być generowane, gdy zostanie użyte to przekształcenie. Najpierw dodamy warstwę AacAudio dla kodowania audio i dwóch warstw H264Video dla kodowania wideo. W warstwach wideo przypiszemy etykiety, aby można było ich używać w nazwach plików wyjściowych. Następnie chcemy, aby dane wyjściowe zawierały także miniatury. W poniższym przykładzie określimy obrazy w formacie PNG, Wygenerowano o 50% rozdzielczości wejściowego filmu wideo oraz trzy sygnatury czasowe — {25%, 50%, 75%} długości wejściowego wideo. Na koniec określimy format plików wyjściowych — jeden do wideo i audio, a drugi dla miniatur. Ponieważ mamy wiele H264Layers, musimy używać makr, które generują unikatowe nazwy na warstwę. Możemy użyć `{Label}` makra lub, jak `{Bitrate}` pokazano w przykładzie.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md) 
