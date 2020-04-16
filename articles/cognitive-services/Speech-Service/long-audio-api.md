---
title: Long Audio API (Wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak interfejs API long audio jest przeznaczony do asynchronizowej syntezy długiego tekstu na mowę.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401071"
---
# <a name="long-audio-api-preview"></a>Interfejs API długiego dźwięku (wersja zapoznawcza)

Interfejs API Long Audio jest przeznaczony do asynchronicznego syntezy długiego tekstu na mowę (na przykład: książek audio). Ten interfejs API nie zwraca syntetyzowane audio w czasie rzeczywistym, zamiast tego oczekuje się, że będzie sondowania odpowiedzi i zużywają dane wyjściowe, ponieważ są one udostępniane z usługi. W przeciwieństwie do interfejsu API tekstu na mowę, który jest używany przez interfejs SDK mowy, interfejs API Long Audio może tworzyć syntetyzowane audio dłużej niż 10 minut, dzięki czemu jest idealny dla wydawców i platform zawartości audio.

Dodatkowe zalety interfejsu API Long Audio:

* Syntetyzowana mowa zwracana przez usługę wykorzystuje głosy neuronowe, co zapewnia wysokiej jakości wyjścia audio.
* Ponieważ odpowiedzi w czasie rzeczywistym nie są obsługiwane, nie ma potrzeby wdrażania punktu końcowego głosu.

> [!NOTE]
> Interfejs API Long Audio obsługuje teraz tylko [niestandardowy głos neuronowy.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)

## <a name="workflow"></a>Przepływ pracy

Zazwyczaj podczas korzystania z interfejsu API Long Audio, można przesłać plik tekstowy lub pliki do syntezy, sondowanie stanu, a następnie jeśli stan jest pomyślny, można pobrać wyjście audio.

Ten diagram zawiera omówienie wysokiego poziomu przepływu pracy.

![Diagram przepływu pracy interfejsu API długiego dźwięku](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Przygotowanie zawartości do syntezy

Przygotowując plik tekstowy, upewnij się, że:

* Jest to zwykły tekst (txt) lub tekst SSML (txt)
* Jest kodowany jako [UTF-8 z oznaczenia zamówienia bajtowego (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Jest to pojedynczy plik, a nie zamek błyskawiczny
* Zawiera więcej niż 400 znaków dla zwykłego tekstu lub 400 [znaków rozliczanych](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) dla tekstu SSML i mniej niż 10 000 akapitów
  * W przypadku zwykłego tekstu każdy akapit jest oddzielany przez naciśnięcie **klawisza Enter/Return** — wyświetl [przykład wprowadzania zwykłego tekstu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * W przypadku tekstu SSML każdy element SSML jest uważany za akapit. Elementy SSML są oddzielone różnymi akapitami — zobacz [przykład wprowadzania tekstu SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> W przypadku języka chińskiego (kontynentalnego), chińskiego (Hongkong), chińskiego (Tajwan), japońskiego i koreańskiego jedno słowo będzie liczone jako dwa znaki. 

## <a name="submit-synthesis-requests"></a>Przesyłanie wniosków podsumowujących

Po przygotowaniu zawartości wejściowej postępuj zgodnie z [długim pasem syntezy dźwięku,](https://aka.ms/long-audio-python) aby przesłać żądanie. Jeśli masz więcej niż jeden plik wejściowy, musisz przesłać wiele żądań. Istnieją pewne ograniczenia, o których należy pamiętać: 
* Klient może przesłać do serwera maksymalnie 5 żądań na sekundę dla każdego konta subskrypcji platformy Azure. Jeśli przekroczy ograniczenie, klient otrzyma kod błędu 429 (zbyt wiele żądań). Zmniejsz kwotę żądania na sekundę
* Serwer może uruchamiać i kolejkować do 120 żądań dla każdego konta subskrypcji platformy Azure. Jeśli przekroczy ograniczenie, serwer zwróci kod błędu 429 (zbyt wiele żądań). Poczekaj i unikaj składania nowych wniosków, dopóki niektóre wnioski nie zostaną zakończone
* Serwer będzie przechowywać do 20 000 żądań dla każdego konta subskrypcji platformy Azure. Jeśli przekroczy ona swoje ograniczenia, usuń niektóre wnioski przed przesłaniem nowych

## <a name="audio-output-formats"></a>Formaty wyjścia audio

Obsługujemy elastyczne formaty wyjścia audio. Można wygenerować wyjścia audio na akapit lub połączenie dźwięków w jedno wyjście, ustawiając parametr "concatenateResult". Interfejs API long audio obsługuje następujące formaty wyjścia audio:

> [!NOTE]
> Domyślnym formatem audio jest riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Przewodniki Szybki start

Oferujemy przewodniki Szybki start zaprojektowany, aby pomóc Ci pomyślnie uruchomić interfejs API Long Audio. Ta tabela zawiera listę szybkich startów interfejsu API long audio uporządkowanych według języka.

* [Szybki start: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Przykładowy kod
Przykładowy kod dla interfejsu API long audio jest dostępny w usłudze GitHub.

* [Przykładowy kod: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Przykładowy kod: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Przykładowy kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
