---
title: Kodowanie filmów wideo przy użyciu standardowego kodera w Media Services
description: W tym temacie pokazano, jak używać standardowego kodera w Media Services do kodowania wejściowego wideo z automatycznie wygenerowaną drabiną szybkości transmisji bitów na podstawie rozdzielczości wejścia i szybkości transmisji bitów.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5b973d17e10f3dbb75f5208d9003b4f8118b37c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98891407"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Koduj z automatycznie wygenerowaną drabiną szybkości transmisji bitów

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Omówienie

W tym artykule wyjaśniono, jak używać standardowego kodera w Media Services do kodowania wejściowego filmu wideo do automatycznie generowanej warstwy szybkości transmisji bitów (par rozdzielczości) na podstawie rozdzielczości wejścia i szybkości transmisji bitów. To wbudowane ustawienia kodera lub ustawienia wstępnego nigdy nie przekraczają rozdzielczości wejścia i szybkości transmisji bitów. Na przykład, jeśli dane wejściowe mają wartość 720 z 3 MB/s, dane wyjściowe pozostaną w najlepszy sposób i zaczynają się od stawek mniejszych niż 3 MB/s.

### <a name="encoding-for-streaming"></a>Kodowanie przesyłania strumieniowego

W przypadku użycia ustawienia wstępnego **AdaptiveStreaming** w **przekształceniu** otrzymujesz dane wyjściowe, które są odpowiednie do dostarczania za pośrednictwem protokołów przesyłania strumieniowego, takich jak HLS i łącznik. W przypadku korzystania z tego ustawienia wstępnego usługa inteligentnie określa liczbę warstw wideo do wygenerowania oraz szybkość i szybkość transmisji bitów. Zawartość wyjściowa zawiera pliki MP4, w których AAC kodowane audio i H. 264 kodowane wideo nie jest przeplotem.

Aby zobaczyć przykład sposobu użycia tego ustawienia wstępnego, zobacz [przesyłanie strumieniowo pliku](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Dane wyjściowe

W tej sekcji przedstawiono trzy przykłady wyjściowych warstw wideo wytwarzanych przez koder Media Services w wyniku kodowania przy użyciu ustawień wstępnych **AdaptiveStreaming** . We wszystkich przypadkach dane wyjściowe zawierają plik MP4 z dźwiękiem z dźwiękiem stereo zakodowany przy 128 kb/s.

### <a name="example-1"></a>Przykład 1
Źródło o wysokości "1080" i szybkości klatek "29,970" tworzą 6 warstw wideo:

|Warstwa|Height|Width|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Przykład 2
Źródło o wysokości "720" i szybkości klatek "23,970" produkuje 5 warstw wideo:

|Warstwa|Height|Width|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Przykład 3
Źródło o wysokości "360" i szybkości klatek "29,970" tworzą 3 warstwy wideo:

|Warstwa|Height|Width|Szybkość transmisji bitów (KB/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
