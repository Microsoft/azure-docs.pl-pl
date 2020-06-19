---
title: Long audio API (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak długi interfejs API audio jest przeznaczony do asynchronicznej syntezy tekstu na mowę.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: ca6bff4c1e99bb8e63db212ca57693870afc30e7
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080642"
---
# <a name="long-audio-api-preview"></a>Long audio API (wersja zapoznawcza)

Długi interfejs API audio jest przeznaczony do asynchronicznej syntezy tekstu na mowę (na przykład: książki audio, artykuły i dokumenty informacyjne). Ten interfejs API nie zwraca danych z syntezy dźwiękowej w czasie rzeczywistym, zamiast tego oczekuje na to, że będziesz sondował o odpowiedzi i zużywać dane wyjściowe, gdy staną się dostępne w usłudze. W przeciwieństwie do interfejsu API zamiany mowy na mowę, który jest używany przez zestaw mowy SDK, długi interfejs API audio umożliwia tworzenie dźwięków z dźwiękiem dłużej niż 10 minut, dzięki czemu jest idealnym rozwiązaniem dla wydawców i platform zawartości audio.

Dodatkowe korzyści wynikające z długiego interfejsu API audio:

* Za pomocą funkcji syntezy głosu zwróconej przez usługę są stosowane najlepsze głosy neuronowych.
* Nie ma potrzeby wdrażania punktu końcowego głosowego, ponieważ umożliwia on syntezę głosów bez trybu wsadowego w czasie rzeczywistym.

> [!NOTE]
> Długi interfejs API audio obsługuje teraz zarówno [publiczne głosy neuronowych](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) , jak i [niestandardowe głosy neuronowych](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Przepływ pracy

Zazwyczaj podczas korzystania z długiego interfejsu API audio można przesłać plik tekstowy lub pliki do wyszukania, przeprowadzić sondowanie stanu, a następnie, jeśli stan zakończy się pomyślnie, można pobrać wyjście audio.

Ten diagram zawiera ogólne omówienie przepływu pracy.

![Diagram przepływu pracy interfejsu API Long audio](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Przygotowywanie zawartości do syntezy

Podczas przygotowywania pliku tekstowego upewnij się, że:

* Jest to zwykły tekst (. txt) lub tekst SSML (. txt)
* Jest zakodowany jako [UTF-8 z oznaczeniem kolejności bajtów (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Jest pojedynczym plikiem, a nie plikiem ZIP
* Zawiera więcej niż 400 znaków dla zwykłego tekstu lub 400 [znaków do rozliczenia](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) dla tekstu SSML i mniej niż 10 000 akapitów
  * W przypadku zwykłego tekstu każdy akapit jest oddzielony przez naciśnięcie **klawisza ENTER/Return** -View — [przykład wprowadzania tekstu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * W przypadku tekstu SSML każdy element SSML jest traktowany jako akapit. Elementy SSML są oddzielane różnymi akapitami — [przykładem wyświetlania tekstu SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> W języku chińskim (kontynent), chińskim (Hongkong SAR), chińskim (Tajwan), japońskim i koreańskim, jeden wyraz będzie liczony jako dwa znaki. 

## <a name="submit-synthesis-requests"></a>Prześlij żądania syntezy

Po przygotowaniu zawartości wejściowej postępuj zgodnie z [długim formularzem szybki start syntezy audio](https://aka.ms/long-audio-python) , aby przesłać żądanie. Jeśli masz więcej niż jeden plik wejściowy, musisz przesłać wiele żądań. 

**Kody stanu HTTP** wskazują typowe błędy.

| Interfejs API | Kod stanu HTTP | Opis | Oferta |
|-----|------------------|-------------|----------|
| Utwórz | 400 | Synteza głosowa nie jest włączona w tym regionie. | Zmień klucz subskrypcji mowy z obsługiwanym regionem. |
|        | 400 | Tylko **standardowa** subskrypcja mowy dla tego regionu jest prawidłowa. | Zmień klucz subskrypcji mowy na warstwę cenową "Standardowa". |
|        | 400 | Przekracza limit 20 000 dla konta platformy Azure. Przed przesłaniem nowych żądań Usuń niektóre żądania. | Serwer będzie przechowywać do 20 000 żądań dla każdego konta platformy Azure. Usuń niektóre żądania przed przesłaniem nowych. |
|        | 400 | Nie można użyć tego modelu w syntezie głosowej: {modelID}. | Upewnij się, że stan {modelID} jest poprawny. |
|        | 400 | Region żądania nie jest zgodny z regionem dla modelu: {modelID}. | Upewnij się, że region {modelID} jest zgodny z regionem żądania. |
|        | 400 | Synteza głosowa obsługuje tylko plik tekstowy w kodowaniu UTF-8 ze znacznikiem kolejności bajtów. | Upewnij się, że pliki wejściowe są w kodowaniu UTF-8 przy użyciu znacznika kolejności bajtów. |
|        | 400 | W żądaniu syntezy głosowej są dozwolone tylko prawidłowe dane wejściowe SSML. | Upewnij się, że wejściowe wyrażenia SSML są poprawne. |
|        | 400 | Nie znaleziono nazwy głosu {voicename} w pliku wejściowym. | Nazwa głosu SSML wejściowego nie jest wyrównana do identyfikatora modelu. |
|        | 400 | Liczba akapitów w pliku wejściowym powinna być mniejsza niż 10 000. | Upewnij się, że liczba akapitów w pliku jest mniejsza niż 10 000. |
|        | 400 | Plik wejściowy powinien zawierać więcej niż 400 znaków. | Upewnij się, że plik wejściowy przekracza 400 znaków. |
|        | 404 | Nie można znaleźć modelu zadeklarowanego w definicji syntezy głosu: {modelID}. | Upewnij się, że wartość {modelID} jest poprawna. |
|        | 429 | Przekracza limit aktywnego syntezy głosu. Zaczekaj na zakończenie niektórych żądań. | Serwer może działać i kolejkować do 120 żądań dla każdego konta platformy Azure. Zaczekaj i unikaj przesyłania nowych żądań, dopóki niektóre żądania nie zostaną ukończone. |
| Wszystko       | 429 | Zbyt wiele żądań. | Klient może przesłać do 5 żądań na serwer na sekundę dla każdego konta platformy Azure. Zmniejsz liczbę żądań na sekundę. |
| Usuń    | 400 | Zadanie syntezy głosu jest nadal w użyciu. | Można usuwać tylko żądania, które zostały **ukończone** lub **zakończyły się niepowodzeniem**. |
| GetByID   | 404 | Nie można znaleźć określonej jednostki. | Upewnij się, że identyfikator syntezy jest poprawny. |

## <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

Długi interfejs API audio jest dostępny w wielu regionach z unikatowymi punktami końcowymi.

| Region | Endpoint |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Kanada Środkowa | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| Wschodnie stany USA | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indie Środkowe | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Południowo-środkowe stany USA | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Azja Południowo-Wschodnia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Południowe Zjednoczone Królestwo | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa Zachodnia | `https://westeurope.customvoice.api.speech.microsoft.com` |
| Zachodnie stany USA 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formaty wyjściowe audio

Obsługujemy elastyczne formaty danych wyjściowych audio. Można generować dane wyjściowe audio w poszczególnych akapitach lub łączyć wyjścia audio w jedno wyjście, ustawiając parametr "concatenateResult". Następujące formaty wyjściowe audio są obsługiwane przez długi interfejs API audio:

> [!NOTE]
> Domyślny format dźwięku to RIFF-16khz-16bit-mono-PCM.

* RIFF-8khz-16bit-mono-PCM
* RIFF-16khz-16bit-mono-PCM
* RIFF-24khz-16bit-mono-PCM
* RIFF-48kHz-16bit-mono-PCM
* audio-16khz-32kbitrate-mono-MP3
* audio-16khz-64kbitrate-mono-MP3
* audio-16khz-128kbitrate-mono-MP3
* audio-24khz-48kbitrate-mono-MP3
* audio-24khz-96kbitrate-mono-MP3
* audio-24khz-160kbitrate-mono-MP3

## <a name="quickstarts"></a>Przewodniki Szybki start

Oferujemy Przewodniki Szybki Start ułatwiające pomyślne uruchomienie długiego interfejsu API audio. Ta tabela zawiera listę długich interfejsów API audio — szybki start zorganizowanych według języka.

* [Szybki Start: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Przykładowy kod
Przykładowy kod dla długiego interfejsu API audio jest dostępny w witrynie GitHub.

* [Przykładowy kod: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Przykładowy kod: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Przykładowy kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
