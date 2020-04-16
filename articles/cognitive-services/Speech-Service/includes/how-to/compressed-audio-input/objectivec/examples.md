---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421786"
---
Aby przesyłać strumieniowo w skompresowanym `SPXPullAudioInputStream` formacie `SPXPushAudioInputStream`audio do usługi Mowa, utwórz lub .

Poniższy fragment kodu pokazuje, jak `SPXAudioConfiguration` utworzyć z `SPXPushAudioInputStream`wystąpienia , określając MP3 jako format kompresji strumienia.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Następny fragment kodu pokazuje, jak skompresowane dane audio mogą być `SPXPushAudioInputStream`odczytywane z pliku i pompowane do pliku .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
