---
title: Informacje o wersji — usługa Speech Service
titleSuffix: Azure Cognitive Services
description: Dziennik uruchomionych wersji funkcji usługi Speech Service, ulepszenia, poprawki błędów i znane problemy.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: f97ecedd4088a825b9ec5a076f4da70df92a3269
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775407"
---
# <a name="speech-service-release-notes"></a>Informacje o wersji usługi Mowa

## <a name="text-to-speech-2021-april-release"></a>Wersja z 2021 r. funkcji "tekst na mowę"

**Neuronowy system TTS jest dostępny w 21 regionach**

- **Dodano 12** nowych regionów — neuronowy system TTS jest teraz dostępny w tych nowych 12 regionach: `Japan East` , , , , , , , , `Japan West` `Korea Central` `North Central US` `North Europe` `South Central US` `Southeast Asia` `UK South` `west Central US` `West Europe` `West US` `West US 2` . Sprawdź [tutaj,](regions.md#text-to-speech) aby uzyskać pełną listę 21 obsługiwanych regionów.

## <a name="text-to-speech-2021-march-release"></a>Wersja 2021—marzec 2021 — tekst na mowę

**Dodano nowe języki i głosy dla neuronowego TTS**

-  Wprowadzono sześć nowych języków — do listy neuronowych języków TTS jest dodawanych 12 nowych głosów w 6 nowych ustawieniach regionalnych: Nia w Welsh (Zjednoczone Królestwo), Aled w Welsh (Zjednoczone Królestwo), Przejrzana w języku angielskim (Ichsieci), James w języku angielskim (Przemysł), Charline in French (Dolina) w Języku Francuskim (Holandia), Dena w niderlandzkim (Holandia), Arnaud w niderlandzkim (Holandia), Polina w Przemowie (Przemów), Ostap w Ćwiartce `cy-GB` `cy-GB` `en-PH` `en-PH` `fr-BE` `fr-BE` `nl-BE` `nl-BE` `uk-UA` `uk-UA` (Przemsz), Uzma w `ur-PK` Urdu (Przemów), Asad w `ur-PK` Urdu (Holandia).

-  Pięć języków od wersji zapoznawczej do wersji zapoznawczej — 10 głosów w 5 językach regionalnych wprowadzonych w listopadzie 2020 r. to: Kert w `et-EE` Estymacji (Estymoński), Colm w Szkocie (Holandia), Nils w Szkocie (a), Leonas w Szkocie `ga-IE` `lv-LV` `lt-LT` (Przemów), Joseph in `mt-MT` Awiw (Holandia).

- **Dodano nowy głos mężczyzn dla języka francuskiego (Kanada)** — nowy głos Jest dostępny dla `fr-CA` języka francuskiego (Kanada).

- **Poprawa jakości** — zmniejszenie liczby błędów wymowy na węgierskim `hu-HU` — 48,17%, `nb-NO` norweskim — 52,76%, `nl-NL` niderlandzkim (Holandia) — 22,11%.

W tej wersji obsługujemy teraz łącznie 142 głosy neuronowe w 60 językach/lokalizacjach regionalnych. Ponadto dostępnych jest ponad 70 standardowych głosów w 49 językach/lokalizacjach regionalnych. Aby [uzyskać pełną listę,](language-support.md#text-to-speech) odwiedź stronę Obsługa języka.

**Uzyskiwanie zdarzeń pozy twarzy w celu animowania znaków**

Zdarzenie [Viseme jest dodawane](how-to-speech-synthesis-viseme.md) do neuronowego TTS, co umożliwia użytkownikom uzyskiwanie sekwencji i czasu trwania pozy twarzy z syntetyzowanej mowy. Funkcji Viseme można używać do kontrolowania ruchu modeli awatarów 2D i 3D, idealnie pasujących do ruchów ust do syntetyzowanej mowy. Teraz funkcja viseme działa tylko w przypadku głosu en-US-AriaNeural.

**Dodawanie elementu zakładki w języku znaczników syntezy mowy (SSML)**

Element [bookmark umożliwia](speech-synthesis-markup.md#bookmark-element) wstawianie niestandardowych znaczników w pliku SSML w celu uzyskania przesunięcia każdego znacznika w strumieniu audio. Może służyć do odwołania do określonej lokalizacji w sekwencji tekstu lub tagu.

## <a name="speech-sdk-1160-2021-march-release"></a>Zestaw SPEECH SDK 1.16.0: wersja z marca 2021 r.

> [!NOTE]
> Zestaw SPEECH SDK w systemie Windows zależy od udostępnionego pakietu redystrybucyjnego Microsoft Visual C++ dla Visual Studio 2015, 2017 i 2019. Pobierz go [tutaj.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

#### <a name="new-features"></a>Nowe funkcje

- **C++/C#/Java/Python:** przeniesiono do najnowszej wersji usługi GStreamer (1.18.3), aby dodać obsługę transskrybowania dowolnego formatu multimediów w systemach Windows, Linux i Android. Zobacz dokumentację [tutaj.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)
- **C++/C#/Java/Objective-C/Python:** Dodano obsługę dekodowania skompresowanego TTS/syntetyzowanego dźwięku do zestawu SDK. Jeśli ustawisz format wyjściowych audio na PCM, a usługa GStreamer jest dostępna w systemie, zestaw SDK automatycznie zażąda skompresowanego dźwięku z usługi, aby zaoszczędzić przepustowość i zdekodować dźwięk na kliencie. Możesz ustawić wartość `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` , aby wyłączyć tę `false` funkcję. Szczegóły dotyczące [języków C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid) [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.propertyid?view=azure-dotnet) [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.propertyid?view=azure-java-stable) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxpropertyid) [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?view=azure-python).
- **JavaScript:** Node.js mogą teraz używać interfejsu [ `AudioConfig.fromWavFileInput` API](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromWavFileInput_File_). Rozwiązuje to problem [z serwisem GitHub #252](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252).
- **C++/C#/Java/Objective-C/Python:** Dodano metodę dla usługi TTS w celu zwracania `GetVoicesAsync()` wszystkich dostępnych głosów syntezy. Szczegóły języków [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync) [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet#methods) [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable#methods) [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoiceasync)i [Python.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python#methods)
- **C++/C#/Java/JavaScript/Objective-C/Python:** Dodano zdarzenie dla syntezy TTS/mowy w celu zwrócenia `VisemeReceived` synchronicznej animacji wizualizacji. Zapoznaj się z [dokumentacją tutaj.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme)
- **C++/C#/Java/JavaScript/Objective-C/Python:** `BookmarkReached` Dodano zdarzenie dla TTS. Możesz ustawić zakładki w wejściowym pliku SSML i pobrać przesunięcia audio dla każdej zakładki. Zapoznaj się z [dokumentacją tutaj.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element)
- **Java:** Dodano obsługę interfejsów API rozpoznawania osób mówiących. Szczegółowe [informacje można znaleźć tutaj.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)
- **C++/C#/Java/JavaScript/Objective-C/Python:** Dodano dwa nowe formaty audio danych wyjściowych z kontenerem WebM dla usługi TTS (Webm16Khz16BitMonoOpus i Webm24Khz16BitMonoOpus). Są to lepsze formaty do przesyłania strumieniowego dźwięku za pomocą koder-system Opus. Szczegóły dotyczące [języków C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) [C#,](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable) [JavaScript,](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat)i [Python.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)
- **C++/C#/Java:** Dodano obsługę pobierania profilu głosowego dla scenariusza rozpoznawania osoby mówiącej. Szczegóły dotyczące [języków C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognizer) [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-dotnet)i [Java.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)
- **C++/C#/Java/Objective-C/Python:** Dodano obsługę oddzielnej udostępnionej biblioteki dla mikrofonu audio i sterowania prelegentem. Dzięki temu można używać zestawu SDK w środowiskach, które nie mają wymaganych zależności bibliotek audio.
- **Objective-C/Swift:** Dodano obsługę struktury modułu z nagłówkiem umbrella. Umożliwia to importowanie zestawu SDK usługi Mowa jako modułu w aplikacjach dla systemu iOS/Mac w języku Objective-C/Swift. Rozwiązuje to problem [z #452 GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452)
- **Python:** Dodano obsługę języka [Python 3.9](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) i porzucono obsługę języka Python 3.5 na koniec cyklu życia języka Python dla [wersji 3.5.](https://devguide.python.org/devcycle/#end-of-life-branches)

**Znane problemy**

- **C++/C#/Java:** nie może użyć klasy w celu uzyskania dostępu do aplikacji Polecenia niestandardowe i zamiast tego `DialogServiceConnector` `CustomCommandsConfig` napotka błąd połączenia. Można to zrobić, ręcznie dodając identyfikator aplikacji do żądania za `config.SetServiceProperty("X-CommandsAppId", "your-application-id", ServicePropertyChannel.UriQueryParameter)` pomocą . Oczekiwane zachowanie systemu `CustomCommandsConfig` zostanie przywrócone w następnej wersji.

#### <a name="improvements"></a>Ulepszenia

- W ramach naszych wysiłków w zakresie wielu wydań w celu zmniejszenia użycia pamięci i zużycia dysku przez zestaw SPEECH SDK pliki binarne systemu Android są teraz mniejsze o 3% do 5%.
- Poprawiono dokładność, czytelność i zapoznaj się z sekcjami dokumentacji referencyjnej języka C# [tutaj.](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet)

#### <a name="bug-fixes"></a>Poprawki błędów

- **JavaScript:** Duże nagłówki plików WAV są teraz analizowane poprawnie (zwiększa wycinek nagłówka do 512 bajtów). Rozwiązuje to problem [z #962 GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962)
- **JavaScript:** Rozwiązano problem z chronometrażu mikrofonu, jeśli strumień mikrofonu kończy się przed zatrzymaniem rozpoznawania, rozsyłanie problemu z rozpoznawaniem mowy nie działa w przeglądarce Firefox.
- **JavaScript:** Teraz poprawnie obsługujemy obietnicę inicjowania, gdy przeglądarka wymusza wyłączenie mikrofonu przed zakończeniem turnOn.
- **JavaScript:** Zastąpiliśmy zależność adresu URL adresem url-parse. Rozwiązuje to problem [z #264 GitHub.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264)
- **Android:** Naprawiono wywołania zwrotne, które nie działają, `minifyEnabled` gdy ustawiono wartość true.
- **C++/C#/Java/Objective-C/Python**: zostanie prawidłowo ustawione na podstawowe we/wy gniazda dla `TCP_NODELAY` TTS, aby zmniejszyć opóźnienie.
- **C++/C#/Java/Python/Objective-C/Go:** Naprawiono sporadyczne awarie, gdy aparat rozpoznawania został niszczony tuż po rozpoczęciu rozpoznawania.
- **C++/C#/Java:** Naprawiono sporadyczne awarie niszczenia rozpoznawania osób mówiących.

#### <a name="samples"></a>Samples

- **JavaScript:** [Przykłady przeglądarki](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) nie wymagają już osobnego pobierania plików biblioteki JavaScript.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Interfejs wiersza polecenia rozpoznawania mowy (znany również jako SPX): wersja z marca 2021 r.

> [!NOTE]
> Wprowadzenie do interfejsu wiersza polecenia (CLI) usługi Azure Speech Service znajdziesz [tutaj.](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics) Interfejs wiersza polecenia umożliwia korzystanie z usługi Azure Speech bez konieczności pisania kodu.

#### <a name="new-features"></a>Nowe funkcje

- Dodano `spx intent` polecenie do rozpoznawania intencji, zastępując `spx recognize intent` .
- Funkcja Recognize i intent może teraz używać funkcji platformy Azure do obliczania szybkości błędów słów przy użyciu funkcji `spx recognize --wer url <URL>` .
- Aparat Recognize może teraz wyprowadzać wyniki jako pliki VTT przy użyciu funkcji `spx recognize --output vtt file <FILENAME>` .
- Poufne informacje o kluczu są teraz zasłonięte w danych wyjściowych debugowania/pełnych danych wyjściowych.
- Dodano sprawdzanie adresu URL i komunikat o błędzie dla pola zawartości podczas tworzenia transkrypcji wsadowej.

**Skrócone testowanie coVID-19:**

Ze względu na to, że trwające pandemie nadal wymagają od naszych inżynierów pracy w domu, skrypty weryfikacji ręcznej przed pandemią znacznie się zmniejszyły. Testujemy na mniejszej liczbie urządzeń z mniejszą liczbą konfiguracji, a prawdopodobieństwo przemycania usterek specyficznych dla środowiska może zostać zwiększone. Nadal rygorystycznie waliduje się przy użyciu dużego zestawu automatyzacji. W mało prawdopodobnym przypadku pominięcia czegoś pomiń, daj nam znać w witrynie [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Pozostań w dobrej kondycji!

## <a name="text-to-speech-2021-february-release"></a>Wersja funkcji "tekst na mowę" z lutego 2021 r.

**niestandardowy neuronowy model głosu (ga ga)**

niestandardowy neuronowy model głosu jest ga w lutym w 13 językach: chiński (mandaryański, uproszczony), angielski (Australia), angielski (Indie), angielski (Zjednoczone Królestwo), angielski (Stany Zjednoczone), francuski (Kanada), francuski (Francja), niemiecki (Niemcy), włoski (Włochy), japoński (Japonia), koreański (Korea), portugalski (Brazylia), hiszpański (Meksyk) i hiszpański (Hiszpania). Dowiedz się więcej [na temat niestandardowy neuronowy model głosu](custom-neural-voice.md) i sposobu odpowiedzialnego korzystania z [nich.](concepts-guidelines-responsible-deployment-synthetic.md) niestandardowy neuronowy model głosu wymaga rejestracji, a firma Microsoft może ograniczyć dostęp na podstawie kryteriów uprawnień firmy Microsoft. Dowiedz się więcej o [ograniczonym dostępie.](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)  

## <a name="speech-sdk-1150-2021-january-release"></a>Zestaw Speech SDK 1.15.0: wersja ze stycznia 2021 r.

> [!NOTE]
> Zestaw SPEECH SDK w systemie Windows zależy od udostępnionego pakietu redystrybucyjnego Microsoft Visual C++ dla Visual Studio 2015, 2017 i 2019. Pobierz go [tutaj.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Najważniejsze podsumowanie**
- Mniejsza ilość pamięci i mniejsze zużycie dysku, dzięki czemu zestaw SDK jest bardziej wydajny.
- Formaty danych wyjściowych o większej wierności dostępne dla niestandardowej prywatnej wersji zapoznawczej neuronowego głosu.
- Rozpoznawanie intencji może teraz uzyskać zwrot więcej niż intencja najwyższego intencji, co daje możliwość oddzielnej oceny intencji klienta.
- Asystent głosowy lub bot są teraz łatwiejsze do skonfigurowania i można go natychmiast zatrzymać nasłuchiwanie, a także wyćwiczyć większą kontrolę nad tym, jak reaguje na błędy.
- Zwiększona wydajność urządzenia dzięki użyciu opcjonalnej kompresji.
- Użyj zestawu SPEECH SDK w systemie Windows ARM/ARM64.
- Ulepszone debugowanie niskiego poziomu.
- Funkcja oceny wymowy jest teraz bardziej dostępna.
- W serwisie GitHub oflagowanych jest kilka poprawek usterek, które zostały oflagowane przez Ciebie, naszych cennych klientów. Dziękuję! Bądź na bieżąco z opiniami!

**Ulepszenia**
- Zestaw SPEECH SDK jest teraz bardziej wydajny i lekki. Rozpoczęliśmy pracę nad wieloma wersjami, aby zmniejszyć użycie pamięci i zużycie dysku przez zestaw Speech SDK. Pierwszym krokiem było znaczące zmniejszenie rozmiaru plików w bibliotekach udostępnionych na większości platform. W porównaniu do wersji 1.14:
  - 64-bitowe biblioteki systemu Windows zgodne z UWP są mniejsze o około 30%.
  - 32-bitowe biblioteki systemu Windows nie mają jeszcze ulepszeń rozmiaru.
  - Biblioteki systemu Linux są mniejsze o 20–25%.
  - Biblioteki systemu Android są mniejsze o 3–5%.

**Nowe funkcje**
- **Wszystkie:** nowe formaty danych wyjściowych 48 KHz dostępne dla prywatnej wersji zapoznawczej niestandardowego głosu neuronowego za pośrednictwem interfejsu API syntezy mowy TTS: Audio48Khz192KBitRateMonoMp3, audio-48khz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48khz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, raw-48khz-16bit-mono-pcm, Riff48Khz16BitMonoPcm, riff-48khz-16bit-mono-pcm.
- **Wszystkie:** Niestandardowy głos jest również łatwiejszy w użyciu. Dodano obsługę ustawiania niestandardowego głosu za pomocą poleceń `EndpointId` ([C++,](/cpp/cognitive-services/speech/speechconfig#setendpointid) [C#,](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId) [Java,](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_) [JavaScript,](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId) [Objective-C,](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid) [Python).](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id) Przed tą zmianą niestandardowi użytkownicy głosowi musieli ustawić adres URL punktu końcowego za pośrednictwem `FromEndpoint` metody . Teraz klienci mogą używać metody `FromSubscription` tak samo jak głosów publicznych, a następnie podać identyfikator wdrożenia, ustawiając . `EndpointId` Upraszcza to konfigurowanie głosów niestandardowych. 
- **C++/C#/Java/Objective-C/Python:** Uzyskaj więcej niż główna intencja z `IntentRecognizer` . Teraz obsługuje konfigurowanie wyniku JSON zawierającego wszystkie intencje, a nie tylko intencję o najwyższej oceny za pośrednictwem metody `LanguageUnderstandingModel FromEndpoint` przy użyciu `verbose=true` parametru URI. Rozwiązuje to problem [z #880 GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880) Zaktualizowaną dokumentację [można znaleźć tutaj.](./get-started-intent-recognition.md#add-a-languageunderstandingmodel-and-intents)
- **C++/C#/Java:** Sprawić, aby asystent głosowy lub bot natychmiast przestał nasłuchiwać. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) ma teraz `StopListeningAsync()` metodę, która ma towarzyszyć `ListenOnceAsync()` . Spowoduje to natychmiastowe zatrzymanie przechwytywania dźwięku i bezpiecznie zaczekaj na wynik, dzięki czemu doskonale nadaje się do użycia w scenariuszach "zatrzymaj teraz" przy naciśnięciu przycisku.
- **C++/C#/Java/JavaScript:** Spraw, aby asystent głosowy lub bot reagował lepiej na podstawowe błędy systemu. `DialogServiceConnector`[(C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java,](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) ma teraz nową `TurnStatusReceived` obsługę zdarzeń. Te opcjonalne zdarzenia odpowiadają każdej rozdzielczości bota i będą zgłaszać błędy wykonywania podczas ich wykonywania, np. w wyniku nieobsługiwanego wyjątku, limitu czasu lub spadku sieci między Direct Line Speech a [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) botem. `TurnStatusReceived` ułatwia reagowanie na warunki awarii. Jeśli na przykład bot zajmuje zbyt długo zapytanie bazy danych zaplecza (np. szukanie produktu), umożliwia klientowi ponowne wysyłanie wiadomości z pytaniem "Niestety, nie do końca to rozumiem, możesz spróbować ponownie" lub coś `TurnStatusReceived` podobnego.
- **C++/C#:** użyj zestawu SPEECH SDK na większej liczby platformach. Pakiet [NuGet zestawu Speech SDK](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) obsługuje teraz natywne pliki binarne systemu Windows ARM/ARM64 dla komputerów stacjonarnych (platformy UWP były już obsługiwane), aby zestaw SPEECH SDK był bardziej przydatny na większej liczby typów maszyn.
- **Java**: ma teraz metodę, która została wcześniej nieumyślnie wykluczona [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) z `setSpeechActivityTemplate()` języka. Jest to równoważne ustawieniu właściwości i spowoduje zażądanie, aby wszystkie przyszłe działania usługi Bot Framework pochodzące z usługi Direct Line Speech scaliły podaną zawartość z ich ładunkami `Conversation_Speech_Activity_Template` JSON.
- **Java:** Ulepszone debugowanie niskiego poziomu. Klasa ma teraz zdarzenie podobne do innych [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) `MessageReceived` języków programowania (C++, C#). To zdarzenie zapewnia dostęp niskiego poziomu do danych przychodzących z usługi i może być przydatne do diagnostyki i debugowania.
- **JavaScript:** łatwiejsza konfiguracja asystentów głosowych i botów za pośrednictwem usługi , która teraz ma metody i factory, które upraszczają korzystanie z niestandardowych lokalizacji usług w porównaniu z [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) `fromHost()` `fromEndpoint()` ręcznym ustawianiem właściwości. Ponadto standaryzowaliśmy opcjonalną specyfikację parametru , aby używać bota nie domyślnego `botId` w fabrykach konfiguracji.
- **JavaScript:** zwiększona wydajność urządzenia dzięki dodaniu właściwości kontrolki ciągu dla kompresji websocket. Ze względu na wydajność domyślnie wyłączyliśmy kompresję websocket. Można to ponownie wywrzeć w scenariuszach o niskiej przepustowości. Więcej szczegółów [można znaleźć tutaj.](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid) Rozwiązuje to problem [z #242 GitHub.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242)
- **JavaScript:** Dodano obsługę oceny wymowy w celu umożliwienia oceny wymowy mowy. Zobacz przewodnik Szybki start [tutaj.](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)

**Poprawki**
- **Wszystkie** (z wyjątkiem języka JavaScript): Naprawiono regresję w wersji 1.14, w której za dużo pamięci zostało przydzielone przez aparat rozpoznawania.
- **C++:** Rozwiązano problem z wyrzucaniem elementów bezużytecznych w `DialogServiceConnector` [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794)#794 .
- **C#:** Rozwiązano problem z zamykaniem wątku, który powodował, że obiekty blokowały się przez około sekundę podczas usuwania.
- **C++/C#/Java:** Naprawiono wyjątek uniemożliwiający aplikacji ustawienie tokenu autoryzacji mowy lub szablonu działania więcej niż raz w pliku `DialogServiceConnector` .
- **C++/C#/Java:** Usunięto awarię rozpoznawania z powodu sytuacji wyścigu podczas awarii.
- **JavaScript:** [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) wcześniej nie honorował opcjonalnego `botId` parametru określonego w `BotFrameworkConfig` fabrykach. W związku z tym konieczne było ręczne ustawienie `botId` parametru ciągu zapytania w celu używania bota spoza domyślnego. Usterka została usunięta, a wartości dostarczone do fabryk będą honorowane i używane, w tym nowe `botId` `BotFrameworkConfig` i `fromHost()` `fromEndpoint()` dodatki. Dotyczy to również `applicationId` parametru dla `CustomCommandsConfig` .
- **JavaScript:** Rozwiązano problem [z usługą GitHub #881](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881), co umożliwia ponowne użycie obiektu rozpoznawania.
- **JavaScript:** Rozwiązano problem, który dotyczył wielokrotnego wysyłania przez dysk SKD w jednej sesji `speech.config` TTS, co marnowała przepustowość.
- **JavaScript:** Uproszczona obsługa błędów podczas autoryzacji mikrofonu, umożliwiająca bardziej opisowy komunikat do bąbelka, gdy użytkownik nie zezwolił na wprowadzanie danych z mikrofonu w przeglądarce.
- **JavaScript:** Rozwiązano problem [z serwisem GitHub #249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) gdzie błędy typu w pliku i powodowały błąd kompilacji `ConversationTranslator` dla użytkowników języka `ConversationTranscriber` TypeScript.
- **Objective-C:** rozwiązano problem z niepowodzeniem kompilacji GStreamer dla systemu iOS w wersji Xcode 11.4, który rozwiązał problem w usłudze [GitHub #911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python:** rozwiązano [problem z #870 GitHub,](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870)usuwając błąd "DeprecationWarning: moduł imp jest przestarzały na rzecz importlib".

**Samples**
- [Przykład z pliku dla przeglądarki JavaScript używa](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) teraz plików do rozpoznawania mowy. Rozwiązuje to problem [z serwisem GitHub #884](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884).

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Interfejs wiersza polecenia rozpoznawania mowy (znany także jako SPX): wersja ze stycznia 2021 r.

**Nowe funkcje**
- Interfejs wiersza polecenia usługi Mowa jest teraz dostępny jako pakiet [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) i można go zainstalować za pośrednictwem interfejsu wiersza polecenia .NET jako globalne narzędzie .NET, które można wywołać z powłoki/wiersza polecenia.
- Zaktualizowano [repo szablonów usługi Custom Speech DevOps](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) w celu używania interfejsu wiersza polecenia usługi Mowa dla przepływów pracy usługi Custom Speech.

**Skrócone testowanie coVID-19:** ze względu na to, że trwające pandemie nadal wymagają od naszych inżynierów pracy w domu, skrypty weryfikacji ręcznej przed pandemią zostały znacznie ograniczone. Testujemy na mniejszej liczbie urządzeń z mniejszą liczbą konfiguracji, a prawdopodobieństwo przemycania usterek specyficznych dla środowiska może zostać zwiększone. Nadal rygorystycznie walidujmy przy użyciu dużego zestawu automatyzacji. W mało prawdopodobnym przypadku, gdy coś nie zostało pominięte, daj nam znać w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Pozostań w dobrej kondycji!

## <a name="text-to-speech-2020-december-release"></a>Wersja z 2020 r. na mowę z 2020 r.

**Nowe głosy neuronowe w wersji zapoznawczej i wersji zapoznawczej**

Wydano 51 nowych głosów dla łącznie 129 głosów neuronowych w 54 językach/lokalizacjach regionalnych:

- **46** nowych głosów w językach regionalnych w języku angielskim: Potrzir w języku arabskim (Portugalia), Hamed in Arabski (Arabia Saudyjska), Bori przeszkoloni w Niemczech (Hiszpania), Potrząsanie w Czeskim (Czeskim), Jeppe w Duńskim (Portugalia), Jonas w języku niemieckim (Francja), Jen w Niemczech (Holandia), Nestoras w greckim (Holandia), Liam w języku angielskim (Kanada), Connor w języku angielskim `ar-EG` `ar-SA` `bg-BG` `ca-ES` `cs-CZ` `da-DK` `de-AT` `de-CH` `el-GR` `en-CA` `en-IE` (Irlandzki), Mad lukry w `en-IN` Indiach (Indie), Mohan w `en-IN` Telugu (Indie), Prabhat w języku `en-IN` angielskim (Indie) ), Valluvar w `en-IN` Tamilu (Indie), Enric in `es-ES` Katy (Hiszpania), Kert w Estonii `et-EE` (Estymoński), Harri w `fi-FI` Fińskim (Holandia), Selma w Fińskim (Portugalia), Fabrice w Języku francuskim (Holandia), Colm w Walii (Francja), Avri w języku hebrajskim (), Srecko w Łagoni `fi-FI` `fr-CH` `ga-IE` `he-IL` `hr-HR` (Wdowie), Tamas w Węgierskim `hu-HU` (Singapurze), Gadis w Indonezyjskim (Indonezyjska), Leonas w Litwinie `id-ID` `lt-LT` (Szkot), Nils w `lv-LV` Szkocie (Szkot), Osman w `ms-MY` Malaj (Portugalia), Joseph in `mt-MT` Awiw (Francja) , Przebłyski `nb-NO` w Norweskiu, Bokmål (Holandia), Pernlia w Norweskiu, Bokmål (Holandia),Plikna w Holenderskiej (Holandia), Maarten w Niderlandzkim (Holandia), Przemysow w Polski (Portugalia), Marek w Polski (Brazylia), Duarte w portugalskim (Brazylia), Raquel w portugalskim (Potugal), Emil w Holenderskiu (Holandia), Dmitry w Rosyjskim `nb-NO` `nl-NL` `nl-NL` `pl-PL` `pl-PL` `pt-BR` `pt-PT` `ro-RO` `ru-RU` (Rosyjskim), Svetlana w Rosyjskim (Brazylia), Lukas w Portugalskim `ru-RU` `sk-SK` (Potugal), Rok w Portanie (Brazylia) ), Mattias w szwedzkim (Wietnamie), Sofie w Szwedzkim (Singapurze), Niwat w tajskim (Stanie), Ahmet w tureckim `sl-SI` `sv-SE` `sv-SE` `th-TH` `tr-TR` (Singapurze), NamMinh w Wietnamie (Wietnam), HsiaoChen w Tajwańskim Mandarynie `vi-VN` (Singapurze), YunJhe `zh-TW` `zh-TW` `zh-HK` `zh-HK` w Tajwańskim Mandarynie (Wietnamie), HiuMaan w chińskim Kantonie (Hongkong), Wanlung w chińskim kantonie (Hongkong).

- **5** nowych głosów w wersjach zapoznawczych w wersjach regionalnych: Kert w Estonii (Estymejska), Colm w Niemczech (Irlandzki), Nils w jędrzejsku (Przemów), Leonas w jędrzeju (jęz.), Joseph in Aim `et-EE` `ga-IE` `lv-LV` `lt-LT` `mt-MT` (Francja).

W tej wersji obsługujemy teraz łącznie 129 głosów neuronowych w 54 językach/lokalizacjach regionalnych. Ponadto dostępnych jest ponad 70 standardowych głosów w 49 językach/lokalizacjach regionalnych. Aby [uzyskać pełną listę,](language-support.md#text-to-speech) odwiedź stronę Obsługa języka.

**Aktualizacje dla Tworzenie zawartości dźwiękowej**
- Ulepszony interfejs użytkownika wyboru głosu z kategoriami głosów i szczegółowymi opisami głosów. 
- Włączone dostrajanie intonacji dla wszystkich głosów neuronowych w różnych językach.
- Zautomatyzowana lokalizacja interfejsu użytkownika na podstawie języka przeglądarki.
- Włączono `StyleDegree` kontrolki dla wszystkich `zh-CN` głosów neuronowych.
Odwiedź stronę [Tworzenie zawartości dźwiękowej,](https://speech.microsoft.com/audiocontentcreation) aby sprawdzić nowe funkcje. 

**Aktualizacje dla głosów zh-CN**
- Zaktualizowano wszystkie `zh-CN` głosy neuronowe w celu obsługi mówienia w języku angielskim.
- Włączono wszystkie `zh-CN` głosy neuronowe w celu obsługi dostosowania do innation. Do dostosowania w celu najlepszego Tworzenie zawartości dźwiękowej można użyć narzędzia SSML lub Tworzenie zawartości dźwiękowej.
- Zaktualizowano `zh-CN` wszystkie neuronowe głosy w wielu stylach w celu obsługi `StyleDegree` kontroli. Natężenie emocji (elastyczne lub silne) można dostosowywać.
- Zaktualizowano `zh-CN-YunyeNeural` w celu obsługi wielu stylów, które mogą wykonywać różne emocje.

## <a name="text-to-speech-2020-november-release"></a>Wersja funkcji "tekst na mowę" — listopad 2020 r.

**Nowe lokalne i głosy w wersji zapoznawczej**
- **W portfolio neuronowych usług** TTS wprowadzono pięć nowych głosów i języków. Są to: Grace in Ai (Dos) (Grace in Aii), On in Ai (W 1966 r.), Anu in Estymoński (Estymoński), Orla in Ai (Ireland) i Everita in Aii in Aii (Aii) w 1997 r. (Ercisko).
- **Obsługuje pięć `zh-CN` nowych głosów** z wieloma stylami i rolami: Jego Wiedohan, Womo, Jegoorui, Tsuoxuan i Yunxi.

> Te głosy są dostępne w publicznej wersji zapoznawczej w trzech regionach świadczenia usługi Azure: EastUS, SouthEastAsia i WestEurope.

**Neuronowy kontener TTS (GA)**
- Za pomocą neuronowego kontenera TTS deweloperzy mogą uruchamiać syntezę mowy z najbardziej naturalnymi głosami cyfrowymi we własnym środowisku, aby spełnić określone wymagania dotyczące zabezpieczeń i ładu danych. Sprawdź, [jak zainstalować kontenery mowy.](speech-container-howto.md) 

**Nowe funkcje**
- **głos niestandardowy:** umożliwia użytkownikom kopiowanie modelu głosu z jednego regionu do innego; obsługiwane wstrzymanie i wznowienie punktu końcowego. Przejdź do [portalu tutaj.](https://speech.microsoft.com/customvoice)
- [Obsługa tagów ciszą SSML.](speech-synthesis-markup.md#add-silence) 
- Ogólne ulepszenia jakości głosu TTS: Ulepszona dokładność wymowy na poziomie słowa w nb-NO. Zmniejszyliśmy błąd wymowy o 53%.

> Przeczytaj więcej na [tym blogu technologicznym.](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604)

## <a name="text-to-speech-2020-october-release"></a>Wersja funkcji "tekst na mowę" — październik 2020 r.

**Nowe funkcje**
- Model Nieschema obsługuje nowy `newscast` styl. Zobacz, [jak używać stylów mówienia w języku SSML.](speech-synthesis-markup.md#adjust-speaking-styles)
- **Głosy neuronowe uaktualniono do vocodera HiFiNet z wyższą wiernością dźwięku i szybszą szybkością syntezy.** Jest to korzyścią dla klientów, których scenariusz opiera się na długich interakcjach lub dźwięku z siecią hi-fi, takich jak dublowanie wideo, książki audio lub materiały edukacyjne online. [Przeczytaj więcej na temat tej historii i dowiedz się więcej na temat próbek głosu na naszym blogu społeczności technicznej](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[głos niestandardowy](https://speech.microsoft.com/customvoice)  &  [Tworzenie zawartości dźwiękowej Studio](https://speech.microsoft.com/audiocontentcreation) zlokalizowane na 17 lokalizacji.** Użytkownicy mogą łatwo przełączyć interfejs użytkownika na język lokalny, aby uzyskać bardziej przyjazne środowisko.   
- **Tworzenie zawartości dźwiękowej:** Dodano kontrolkę stopnia stylu dla XiaoxiaoNeural; Udoskonaliliśmy dostosowaną funkcję przerwania, aby uwzględniała przyrostowe przerwy po 50 min. 

**Ogólne ulepszenia jakości głosu usługi TTS**
- Ulepszona dokładność wymowy na poziomie słów w programie `pl-PL` (zmniejszenie szybkości błędów: 51%) i `fi-FI` (zmniejszenie liczby błędów: 58%)
- Ulepszone `ja-JP` odczytywanie pojedynczych wyrazów dla scenariusza słownika. Zmniejszenie błędu wymowy o 80%.
- `zh-CN-XiaoxiaoNeural`: poprawiona tonacji/CustomerService/Newscast/W styluDane/w stylu Dosyć.
- `zh-CN`: Ulepszono wymowę Erhui oraz jasny ton i udoskonaloną prozaię przestrzeni, co znacznie zwiększa zrozumiałość. 

## <a name="speech-sdk-1140-2020-october-release"></a>Zestaw Speech SDK 1.14.0: wersja z października 2020 r.

> [!NOTE]
> Zestaw SPEECH SDK w systemie Windows zależy od udostępnionego pakietu redystrybucyjnego Microsoft Visual C++ dla Visual Studio 2015, 2017 i 2019. Pobierz go [tutaj.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Nowe funkcje**
- **Linux:** Dodano obsługę systemów Debian 10 i Ubuntu 20.04 LTS.
- **Python/Objective-C:** Dodano obsługę interfejsu `KeywordRecognizer` API. Dokumentacja będzie [tutaj.](./custom-keyword-basics.md)
- **C++/Java/C#:** Dodano obsługę ustawienia dowolnego `HttpHeader` klucza/wartości za pośrednictwem . `ServicePropertyChannel::HttpHeader`
- **JavaScript:** Dodano obsługę interfejsu `ConversationTranscriber` API. Przeczytaj dokumentację [tutaj.](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript) 
- **C++/C#:** Dodano nową `AudioDataStream FromWavFileInput` metodę (do odczytu . Pliki WAV) [tutaj (C++)](/cpp/cognitive-services/speech/audiodatastream) i [tutaj (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/C#/Java/Python/Objective-C/Swift:** Dodano metodę zatrzymania syntezy tekstu `stopSpeakingAsync()` na mowę. Zapoznaj się z dokumentacją referencyjną tutaj [(C++),](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace) [tutaj (C#)](/dotnet/api/microsoft.cognitiveservices.speech), [tutaj (Java),](/java/api/com.microsoft.cognitiveservices.speech) [tutaj (Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)i [tutaj (Objective-C/Swift).](/objectivec/cognitive-services/speech/)
- **C#, C++, Java:** Dodano funkcję do klasy , która może służyć do monitorowania zdarzeń połączenia `FromDialogServiceConnector()` `Connection` i rozłączania dla klasy `DialogServiceConnector` . Zapoznaj się z dokumentacją [referencyjną tutaj (C#),](/dotnet/api/microsoft.cognitiveservices.speech.connection) [tutaj (C++)](/cpp/cognitive-services/speech/connection)i [tutaj (Java).](/java/api/com.microsoft.cognitiveservices.speech.connection)
- **C++/C#/Java/Python/Objective-C/Swift:** Dodano obsługę oceny wymowy, która ocenia wymowę mowy i przekazuje opinie osób mówiących na temat dokładności i fluency mówionego dźwięku. Zapoznaj się z dokumentacją [tutaj.](how-to-pronunciation-assessment.md)

**Zmiana przerywana**
- **JavaScript:** Element PullAudioOutputStream.read() ma zwracaną zmianę typu z wewnętrznej obietnicy na natywną obietnicę JavaScript.

**Poprawki**
- **Wszystkie:** Naprawiono regresję 1.13 w funkcji , w której `SetServiceProperty` wartości z określonymi znakami specjalnymi były ignorowane.
- **C#:** Rozwiązano problem z przykładami konsoli systemu Windows Visual Studio 2019 r. nie można odnaleźć natywnych bibliotek DLL.
- **C#: Usunięto** awarię zarządzania pamięcią, jeśli strumień jest używany jako `KeywordRecognizer` dane wejściowe.
- **ObjectiveC/Swift:** Naprawiono awarię zarządzania pamięcią, jeśli strumień jest używany jako dane wejściowe rozpoznawania.
- **Windows:** rozwiązano problem z istnieniem z BT HFP/A2DP na platformie uniwersalnej systemu Windows.
- **JavaScript:** Naprawiono mapowanie identyfikatorów sesji, aby ulepszyć rejestrowanie i pomóc w wewnętrznych korelacjach debugowania/usługi.
- **JavaScript:** Dodano poprawkę `DialogServiceConnector` wyłączania `ListenOnce` wywołań po pierwszym wywołaniu.
- **JavaScript:** Rozwiązano problem, który oznaczał, że wynikowe dane wyjściowe zawsze byłyby "proste".
- **JavaScript:** rozwiązano problem z rozpoznawaniem ciągłym w przeglądarce Safari w systemie macOS.
- **JavaScript:** ograniczanie obciążenia procesora CPU w scenariuszu z wysoką przepływnością żądań.
- **JavaScript:** zezwalaj na dostęp do szczegółów wyniku rejestracji profilu głosowego.
- **JavaScript:** Dodano poprawkę dla ciągłego rozpoznawania w `IntentRecognizer` programie .
- **C++/C#/Java/Python/Swift/ObjectiveC:** Poprawiono nieprawidłowy adres URL dla australiaeast i brazilsouth w `IntentRecognizer` .
- **C++/C#:** `VoiceProfileType` dodano element jako argument podczas tworzenia `VoiceProfile` obiektu.
- **C++/C#/Java/Python/Swift/ObjectiveC:** Naprawiono potencjał podczas `SPX_INVALID_ARG` próby odczytu z danego `AudioDataStream` stanowiska.
- **IOS: Usunięto** awarię z rozpoznawaniem mowy w a aparatu Unity

**Samples**
- **ObjectiveC:** Dodano przykład rozpoznawania słów kluczowych [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples).
- **C#/JavaScript:** Dodano przewodnik Szybki start dla transkrypcji konwersacji [tutaj (C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) i [tutaj (JavaScript).](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription)
- **C++/C#/Java/Python/Swift/ObjectiveC:** Dodano przykład oceny wymowy [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)
- **Xamarin:** zaktualizowano przewodnik Szybki start do Visual Studio szablonu [tutaj.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin)

**Znany problem**
- Globalny certyfikat główny G2 firmy DigiCert nie jest domyślnie obsługiwany na urządzeniach HoloLens 2 i Android 4.4 (KitKat) i należy go dodać do systemu, aby zestaw SPEECH SDK był funkcjonalny. Certyfikat zostanie dodany do obrazów systemu operacyjnego HoloLens 2 w najbliższej przyszłości. Klienci korzystający z systemu Android 4.4 muszą dodać zaktualizowany certyfikat do systemu.

**Skrócone testowanie coVID-19:** Ze względu na zdalną pracę w ciągu ostatnich kilku tygodni nie mogliśmy wykonać tak wielu testów weryfikacji ręcznej, jak zwykle. Nie wprowadzaliśmy żadnych zmian, które naszym zdaniem mogły zostać przerwane, a nasze testy automatyczne zostały pomyślnie wykonane. W mało prawdopodobnym przypadku, gdy coś nie zostało pominięte, daj nam znać w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Pozostań w dobrej kondycji!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Interfejs wiersza polecenia rozpoznawania mowy (znany również jako SPX): wersja z października 2020 r.
SPX to interfejs wiersza polecenia, który umożliwia korzystanie z usługi Azure Speech bez pisania kodu. Pobierz najnowszą wersję [tutaj.](./spx-basics.md) <br>

**Nowe funkcje**
- `spx csr dataset upload --kind audio|language|acoustic` — tworzenie zestawów danych na pomocą danych lokalnych, a nie tylko z adresów URL.
- `spx csr evaluation create|status|list|update|delete` — porównanie nowych modeli z bazową prawdą/innymi modelami.
- `spx * list` — obsługuje środowisko niestronicowane (nie wymaga --top X --skip X).
- `spx * --http header A=B` — obsługa nagłówków niestandardowych (dodanych do pakietu Office w celu uwierzytelniania niestandardowego). 
- `spx help` — ulepszony kolor tekstu i znacznika powrotu zakodowany (niebieski).

## <a name="text-to-speech-2020-september-release"></a>Wersja funkcji "tekst na mowę" z 2020 r. do września

### <a name="new-features"></a>Nowe funkcje

* **Neuronowe TTS** 
    * **Rozszerzono o obsługę 18 nowych języków/danych regionalnych.** Są to: grecki, czeski, niemiecki (Niemcy), niemiecki (Holandia), grecki, angielski (Irlandia), francuski (Holandia), hebrajski, hebrajski, węgierski, indonezyjski, malajski, rumuński, węgierski, węgierski, tamilski, telugu i wietnamski. 
    * **Wydano 14 nowych głosów, aby wzbogacić różnorodność w istniejących językach.** Zobacz [pełną listę języków i głosów.](language-support.md#neural-voices)
    * **Nowe style mówienia `en-US` dla `zh-CN` głosów i .** The new voice in English (US), supports chatbot, customer service, and assistant styles (Nowy głos w języku angielskim (USA) obsługuje czatbota, obsługę klienta i style asystenta. 10 nowych stylów mówienia jest dostępnych za pomocą naszego głosu zh-CN, Nieswoiowo. Ponadto neuronowy głos Wscho obsługuje `StyleDegree` dostrajanie. Zobacz, [jak używać stylów mówienia w języku SSML.](speech-synthesis-markup.md#adjust-speaking-styles)

* **Kontenery: neuronowy kontener TTS został wydany w publicznej wersji zapoznawczej z 16 głosami dostępnymi w 14 językach.** Dowiedz się więcej na [temat wdrażania kontenerów mowy dla neuronowych usług TTS](speech-container-howto.md)  

Przeczytaj pełne [zawiadomienie o aktualizacjach TTS na konferencji Ignite 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) 

## <a name="text-to-speech-2020-august-release"></a>Wersja funkcji "tekst na mowę" —sierpień 2020 r.

### <a name="new-features"></a>Nowe funkcje

* **Neuronowe TTS: nowy styl mówienia dla `en-US` Głos Aria.** AriaNeural może brzmieć jak caster wiadomości podczas czytania wiadomości. Styl "newscast-formal" brzmi bardziej poważnie, podczas gdy styl "newscast-relax" jest bardziej swobodny i nieformalny. Zobacz, [jak używać stylów mówienia w języku SSML.](speech-synthesis-markup.md)

* **głos niestandardowy: zostanie wydana nowa funkcja do automatycznego sprawdzania jakości danych treningowych.** Podczas przekazywania danych system przeanalizuje różne aspekty danych audio i transkrypcji oraz automatycznie rozwiąże lub przefiltruje problemy w celu poprawy jakości modelu głosu. Obejmuje to ilość dźwięku, poziom szumu, dokładność wymowy mowy, wyrównanie mowy ze znormalizowanym tekstem, ciszę w dźwięku, a także format dźwięku i skryptu. 

* **Tworzenie zawartości dźwiękowej: zestaw nowych funkcji** umożliwiających bardziej zaawansowane dostrajanie głosu i zarządzanie dźwiękami.

    * Wymowa: funkcja dostrajania wymowy została zaktualizowana do najnowszego zestawu phoneme. Możesz wybrać odpowiedni element phoneme z biblioteki i uściślić wymowę wybranych wyrazów. 

    * Pobierz: funkcja dźwięku "Pobierz"/"Eksportuj" została rozszerzona o obsługę generowania dźwięku za pomocą akapitu. Zawartość można edytować w tym samym pliku/pliku SSML, generując jednocześnie wiele danych wyjściowych audio. Struktura plików "Pobierz" została również ulepszona. Teraz możesz łatwo pobrać wszystkie pliki dźwiękowe w jednym folderze. 

    * Stan zadania: ulepszono środowisko eksportu wielu plików. W przypadku eksportowania wielu plików w przeszłości, jeśli jeden z plików nie powiódł się, całe zadanie nie powiedzie się. Jednak teraz wszystkie inne pliki zostaną pomyślnie wyeksportowane. Raport zadań jest wzbogacony o bardziej szczegółowe i ustrukturyzowane informacje. Teraz możesz sprawdzić dzienniki dla wszystkich plików i zdań, które zakończyły się niepowodzeniem, przy użyciu raportu. 

    * Dokumentacja SSML: połączona z dokumentem SSML, która pomaga w sprawdzaniu reguł używania wszystkich funkcji dostrajania.

* Interfejs API listy głosów został zaktualizowany w celu dołączyć przyjazną dla użytkownika nazwę wyświetlaną i style mówienia **obsługiwane w przypadku głosów neuronowych.**

### <a name="general-tts-voice-quality-improvements"></a>Ogólne ulepszenia jakości głosu usługi TTS

* Zredukowano procent błędów wymowy na poziomie słowa dla `ru-RU` (błędy zmniejszyły się o 56%) i `sv-SE` (liczba błędów zredukowana o 49%)

* Ulepszone odczytywanie wyrazów polifonii na `en-US` głosach neuronowych o 40%. Przykłady wyrazów polifonii to "read", "live", "content", "record", "object" itp. 

* Poprawiono naturalność tonu pytania w `fr-FR` . ZYSK MOS (średni wynik opinii): +0,28

* Zaktualizowano koderów vocoder dla następujących głosów, dzięki ulepszeniom wierności i ogólnej szybkości wydajności o 40%.

    | Regionalne | Połączenia głosowe |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Poprawki błędów

* Usunięto szereg usterek w narzędziu Tworzenie zawartości dźwiękowej 
    * Rozwiązano problem z automatycznym odświeżaniem. 
    * Rozwiązano problemy ze stylami głosu w zh-CN w regionie Azja Wschodnia Południowej.
    * Rozwiązano problem ze stabilnością, w tym błąd eksportu z tagiem "break" i błędy w interpunkcji.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Nowe lokalne funkcje mowy na tekst: wersja 2020–sierpień
W sierpniu w funkcji mowy na tekst opublikowano 26 nowych lokalizacji regionalnych: 2 języki europejskiego i , 5 angielskich i 19 hiszpańskich lokalizacji regionalnych, które obejmują większość krajów Ameryki `cs-CZ` `hu-HU` Południowej. Poniżej znajduje się lista nowych lokalizacji regionalnych. Pełną listę języków można znaleźć [tutaj.](./language-support.md)

| Regionalne  | Język                          |
|---------|-----------------------------------|
| `cs-CZ` | Czeski (Czechy)            | 
| `en-HK` | Angielski (Hongkong)               | 
| `en-IE` | Angielski (Irlandia)                 | 
| `en-PH` | Angielski (Przemysł)             | 
| `en-SG` | Angielski (Singapur)               | 
| `en-ZA` | Angielski (Republika Południowej Afryki)            | 
| `es-AR` | Hiszpański (Argentyna)               | 
| `es-BO` | Hiszpański (Brazylia)                 | 
| `es-CL` | Hiszpański (Zamów)                   | 
| `es-CO` | Hiszpański (Kolumbia)                | 
| `es-CR` | Hiszpański (Dosyć)              | 
| `es-CU` | Hiszpański (Kuba)                    | 
| `es-DO` | Hiszpański (Republika Ludowej)      | 
| `es-EC` | Hiszpański (Kolumbia)                 | 
| `es-GT` | Hiszpański (Brazylia)               | 
| `es-HN` | Hiszpański (Zabłocie)                | 
| `es-NI` | Hiszpański (Nikaragua)               | 
| `es-PA` | Hiszpański (Zamów)                  | 
| `es-PE` | Hiszpański (Zdumiej)                    | 
| `es-PR` | Hiszpański (Portoryko)             | 
| `es-PY` | Hiszpański (Paragwaj)                | 
| `es-SV` | Hiszpański (El Zaimki)             | 
| `es-US` | Hiszpański (USA)                     | 
| `es-UY` | Hiszpański (Argentyna)                 | 
| `es-VE` | Hiszpański (w Szlicht)               | 
| `hu-HU` | Węgierski (Węgry)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Zestaw Speech SDK 1.13.0: wersja z lipca 2020 r.

> [!NOTE]
> Zestaw SPEECH SDK w systemie Windows zależy od udostępnionego pakietu redystrybucyjnego Microsoft Visual C++ dla Visual Studio 2015, 2017 i 2019. Pobierz i zainstaluj go [tutaj.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Nowe funkcje**
- **C#:** Dodano obsługę asynchronicznej transkrypcji konwersacji. Zapoznaj się z [dokumentacją tutaj.](./how-to-async-conversation-transcription.md)  
- **JavaScript:** dodano rozpoznawanie osoby mówiącej obsługę [](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) zarówno przeglądarki, [jak inode.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **JavaScript:** Dodano obsługę automatycznego wykrywania języka/identyfikatora języka. Zapoznaj się z [dokumentacją tutaj.](./how-to-automatic-language-detection.md?pivots=programming-language-javascript)
- **Objective-C:** Dodano obsługę [transkrypcji](./multi-device-conversation.md) konwersacji i konwersacji [na wielu urządzeniach.](./conversation-transcription.md) 
- **Python:** dodano obsługę skompresowanego dźwięku dla języka Python w systemach Windows i Linux. Zapoznaj się z [dokumentacją tutaj.](./how-to-use-codec-compressed-audio-input-streams.md) 

**Poprawki**
- **Wszystkie:** Rozwiązano problem, który powodował, że element KeywordRecognizer nie przechodził dalej strumieni po rozpoznaniu.
- **Wszystkie:** Rozwiązano problem, który powodował, że strumień uzyskany z wyniku keywordRecognitionResult nie zawierał słowa kluczowego .
- **Wszystkie:** Rozwiązano problem, który oznaczał, że metoda SendMessageAsync nie wysyłała komunikatu za pośrednictwem sieci, gdy użytkownicy zakończą oczekiwanie na ten komunikat.
- **Wszystkie:** usunięto awarię interfejsów API rozpoznawanie osoby mówiącej, gdy użytkownicy wielokrotnie wywołują metodę VoiceProfileClient::SpeakerRecEnrollProfileAsync i nie czekali na zakończenie wywołań.
- **Wszystkie:** Naprawiono włączanie rejestrowania plików w klasach VoiceProfileClient i SpeakerRecognizer.
- **JavaScript:** rozwiązano [problem z](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) ograniczaniem przepustowości, gdy przeglądarka jest zminimalizowana.
- **JavaScript:** rozwiązano [problem z](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) przeciekiem pamięci w strumieniach.
- **JavaScript:** Dodano buforowanie dla odpowiedzi OCSP ze środowiska NodeJS.
- **Java:** Rozwiązano problem, który powodował, że pola BigInteger zawsze zwracały 0.
- **iOS:** Rozwiązano problem [z publikowaniem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) aplikacji opartych na zestawie SPEECH SDK w systemie iOS App Store.

**Samples**
- **C++:** Dodano przykładowy kod dla rozpoznawanie osoby mówiącej [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**Skrócone testowanie coVID-19:** Ze względu na zdalną pracę w ciągu ostatnich kilku tygodni nie mogliśmy wykonać tak wielu testów weryfikacji ręcznej, jak zwykle. Nie wprowadzaliśmy żadnych zmian, które mogłyby zostać przerwane, a nasze testy automatyczne zostały pomyślnie wykonane. W mało prawdopodobnym przypadku pominięcia czegoś pomiń, daj nam znać w witrynie [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Pozostań w dobrej kondycji!

## <a name="text-to-speech-2020-july-release"></a>Wersja funkcji "tekst na mowę" —lipiec 2020 r.

### <a name="new-features"></a>Nowe funkcje

* **Neuronowe TTS, 15** nowych neuronowych głosów: Nowe głosy dodane do portfolio neuronowych usług TTS to Salma w języku arabskim `ar-EG` (Indie), Zariyah w arabskim (Arabia Saudyjska), Doma w Zelandii (Hiszpania), Christel w duńskim `ar-SA` `ca-ES` (Portugalia), Neerja w języku angielskim (Indie), Noora w fińskim (Holandia), Swara w Chinach (Indie), Colette w Holenderskiej (Holandia), Zofia w 1999 r. `da-DK` `es-IN` `fi-FI` `hi-IN` `nl-NL` (Rpa),Endera w Portugalskim `pl-PL` `pt-PT` (Portugalia), Daroński w `ru-RU` Rosyjskim (Portugalia), Hillevi w `sv-SE` Szwedzkij (Achara), Achara w Tajski (Portugalia), HiuGaai w Języku chińskim `th-TH` `zh-HK` (Cantonese, Tradycyjne) i HsiaoYu w `zh-TW` języku chińskim (tajwański mandaryński). Sprawdź wszystkie [obsługiwane języki.](./language-support.md#neural-voices)  

* **głos niestandardowy,** usprawnione testowanie głosowe za pomocą przepływu trenowania, aby uprościć środowisko użytkownika: dzięki nowej funkcji testowania każdy głos zostanie automatycznie przetestowany przy użyciu wstępnie zdefiniowanego zestawu testowego zoptymalizowanego pod kątem każdego języka w celu obsługi scenariuszy ogólnych i asystentów głosowych. Te zestawy testowe są starannie wybierane i testowane pod kątem dołączania typowych przypadków użycia i fonemów w języku. Oprócz tego użytkownicy mogą nadal wybierać przekazywanie własnych skryptów testowych podczas trenowania modelu.

* **Tworzenie zawartości dźwiękowej: wydano zestaw nowych funkcji umożliwiających bardziej zaawansowane dostrajanie głosu i zarządzanie dźwiękami**

    * `Pitch`Funkcje , i zostały ulepszone w celu obsługi dostrajania za pomocą wstępnie zdefiniowanej wartości, na przykład `rate` `volume` powolnej, średniej i szybkiej. Teraz użytkownicy mogą łatwo wybrać wartość "stałą" dla edytowania dźwięku.

    ![Dostrajanie dźwięku](media/release-notes/audio-tuning.png)

    * Użytkownicy mogą teraz przejrzeć `Audio history` plik służbowy. Dzięki tej funkcji użytkownicy mogą łatwo śledzić cały wygenerowany dźwięk związany z plikiem pracy. Mogą oni sprawdzić wersję historii i porównać jakość podczas dostrajania w tym samym czasie. 

    ![Historia dźwięku](media/release-notes/audio-history.png)

    * Ta `Clear` funkcja jest teraz bardziej elastyczna. Użytkownicy mogą wyczyścić określony parametr dostrajania, zachowując inne parametry dostępne dla wybranej zawartości.  

    * Na stronie docelowej dodano film wideo z samouczkiem, który pomoże użytkownikom szybko rozpocząć pracę z dostrajaniem głosu i zarządzaniem dźwiękami usługi TTS. [](https://speech.microsoft.com/audiocontentcreation) 

### <a name="general-tts-voice-quality-improvements"></a>Ogólne ulepszenia jakości głosu usługi TTS

* Ulepszony vokoder TTS w systemach w celu większej wierności i mniejszego opóźnienia.

    * Zaktualizowano system Elsa w systemie do nowego vokoderu, który osiągnął wzrost jakości głosu na poziomie `it-IT` +0,464 CMOS (porównawczy średni wynik opinii), o 40% szybciej w syntezie i o 30% zmniejszenie opóźnienia pierwszego bajtu. 
    * Zaktualizowano system Ctrloxiao w systemie do nowego vocodera za pomocą `zh-CN` +0148 korzyści CMOS dla domeny ogólnej, +0,348 dla stylu emisji wiadomości i +0,195 dla stylu liryticznego. 

* Zaktualizowano `de-DE` modele `ja-JP` i modele głosowe, aby dane wyjściowe TTS bardziej naturalne.
    
    * Zaktualizowano platformę Katja w programie przy użyciu najnowszej metody `de-DE` modelowania prosody, czyli zysk MOS (Mean Opinion Score) wynosi +0,13. 
    * Zaktualizowano nanami w programie o nowy model prosody z akcentem `ja-JP` tonu: zysk MOS (Mean Opinion Score) wynosi +0,19;  

* Ulepszona dokładność wymowy na poziomie słów w pięciu językach.

    | Język | Zmniejszenie liczby błędów wymowy |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Poprawki błędów

* Odczyt waluty
    * Rozwiązano problem z odczytem waluty dla `es-ES` i `es-MX`
     
    | Język | Dane wejściowe | Odczyt po ulepszeniu |
    |---|---|---|
    | `es-MX` | 1,58 USD | un peso cincuenta y ocho centavos |
    | `es-ES` | 1,58 USD | un dólar cincuenta y ocho centavos |

    * Obsługa waluty ujemnej (na przykład "-325 €" ) w następujących lokalizacjach regionalnych: `en-US` , , , , , `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

* Ulepszono odczytywanie adresów w funkcji `pt-PT` .
* Rozwiązano problemy z wymową natasha ( ) i Libby ( ) w `en-AU` `en-UK` wyrazach "for" i "four".  
* Usunięto usterki w Tworzenie zawartości dźwiękowej narzędziu
    * Dodatkowa i nieoczekiwana pauza po naprawieniu drugiego akapitu.  
    * Funkcja "Brak przerwania" jest dodawana z powrotem z błędu regresji. 
    * Problem z losowym odświeżaniem Speech Studio został rozwiązany.  

### <a name="samplessdk"></a>Przykłady/zestaw SDK

* JavaScript: rozwiązuje problem z odtwarzaniem w programie Firefox i przeglądarce Safari w systemach macOS i iOS. 

## <a name="speech-sdk-1121-2020-june-release"></a>Zestaw Speech SDK 1.12.1: wersja z czerwca 2020 r.
**Interfejs wiersza polecenia rozpoznawania mowy (znany również jako SPX)**
-   Dodano funkcje wyszukiwania pomocy interfejsu wiersza polecenia:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Zaktualizowano do pracy z nowo wdrożonym interfejsem API usługi Batch i usługi Custom Speech w wersji 3.0:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Nowe funkcje**
-   **C, \# C++**: rozpoznawanie osoby mówiącej Preview: ta funkcja umożliwia identyfikację osoby mówiącej (kto mówi?) i weryfikację osoby mówiącej (to osoba mówiąca, za kogo się podają?). Zacznij od [o overview (omówienie),](./speaker-recognition-overview.md)przeczytaj [rozpoznawanie osoby mówiącej basics (Podstawy interfejsu](./get-started-speaker-recognition.md)API) lub dokumentacja [interfejsu API](/rest/api/speakerrecognition/).

**Poprawki**
-   **C, \# C++:** Naprawiono nagrywanie mikrofonu nie działało w języku 1.12 w funkcji rozpoznawania osób mówiących.
-   **JavaScript:** poprawki funkcji tekstu na mowę w przeglądarce Firefox i przeglądarce Safari w systemach macOS i iOS.
-   Poprawka rozsyłania naruszeń dostępu weryfikatora aplikacji systemu Windows podczas transkrypcji konwersacji podczas korzystania ze strumienia z ośmiu kanałów.
-   Poprawka rozsyłania naruszeń dostępu weryfikatora aplikacji systemu Windows w przypadku tłumaczenia konwersacji na wielu urządzeniach.

**Samples**
-   **C#:** [przykładowy kod do](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) rozpoznawania osób mówiących.
-   **C++:** [przykładowy kod do](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) rozpoznawania osób mówiących.
-   **Java:** [przykładowy kod do](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) rozpoznawania intencji w systemie Android. 

**Skrócone testowanie coVID-19:** Ze względu na zdalną pracę w ciągu ostatnich kilku tygodni nie mogliśmy wykonać tak wielu testów weryfikacji ręcznej, jak zwykle. Nie wprowadzaliśmy żadnych zmian, które naszym zdaniem mogły zostać przerwane, a nasze testy automatyczne zostały pomyślnie wykonane. W mało prawdopodobnym przypadku pominięcia czegoś pomiń, daj nam znać w witrynie [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Pozostań w dobrej kondycji!


## <a name="speech-sdk-1120-2020-may-release"></a>Zestaw SPEECH SDK 1.12.0: wersja z maja 2020 r.
**Interfejs wiersza polecenia rozpoznawania mowy (znany również jako SPX)**
- **SPX** to nowe narzędzie wiersza polecenia, które umożliwia wykonywanie rozpoznawania, syntezy, tłumaczenia, transkrypcji wsadowej i niestandardowego zarządzania mowy z wiersza polecenia. Użyj jej do przetestowania usługi Speech Service lub do wykonania skryptów dla zadań usługi Mowa. Pobierz narzędzie i zapoznaj się z dokumentacją [tutaj.](./spx-overview.md)

**Nowe funkcje**
- **Go:** obsługa nowego języka Go na rzecz [rozpoznawania mowy i](./get-started-speech-to-text.md?pivots=programming-language-go) [niestandardowego asystenta głosowego.](./quickstarts/voice-assistants.md?pivots=programming-language-go) W tym miejscu skonfiguruj środowisko [dewelopera.](./quickstarts/setup-platform.md?pivots=programming-language-go) Przykładowy kod można znaleźć w poniższej sekcji Przykłady. 
- **JavaScript:** dodano obsługę przeglądarki dla funkcji mowy na mowę. Zobacz dokumentację [tutaj.](./get-started-text-to-speech.md?pivots=programming-language-JavaScript)
- **C++, C#, Java:** nowe obiekty i interfejsy API obsługiwane na platformach `KeywordRecognizer` Windows, Android, Linux & iOS. Zapoznaj się z dokumentacją [tutaj.](./custom-keyword-overview.md) Przykładowy kod można znaleźć w poniższej sekcji Przykłady. 
- **Java:** Dodano konwersację na wielu urządzeniach z obsługą tłumaczenia. Zobacz ten temat w doku [referencyjnym.](/java/api/com.microsoft.cognitiveservices.speech.transcription)

**Ulepszenia & optymalizacji**
- **JavaScript:** zoptymalizowana implementacja mikrofonu przeglądarki zwiększająca dokładność rozpoznawania mowy.
- **Java:** zrefactored bindings using direct JNI implementation without SWIG (Refaktoryzacja powiązań przy użyciu bezpośredniej implementacji interfejsu JNI bez usługi SWIG). Ta zmiana zmniejsza o 10 razy rozmiar powiązań dla wszystkich pakietów Java używanych dla systemów Windows, Android, Linux i Mac oraz ułatwia dalsze opracowywanie implementacji zestawu Speech SDK dla języka Java.
- **Linux:** zaktualizowano [dokumentację pomocy](./speech-sdk.md?tabs=linux) technicznej przy użyciu najnowszych uwag specyficznych dla systemu RHEL 7.
- Ulepszona logika połączenia w celu wielokrotnego nawiązywania połączenia w przypadku wystąpienia błędów usługi i sieci.
- Zaktualizowano stronę [portal.azure.com](https://portal.azure.com) Szybki start usługi Mowa, aby ułatwić deweloperom kolejny krok w podróży po usłudze Azure Speech.

**Poprawki**
- **C#, Java:** rozwiązano [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) z ładowaniem bibliotek zestawu SDK w u wersji 32-bitowej i 64-bitowej systemu Linux ARM.
- **C#:** Naprawiono jawne usuwanie natywnych dojść dla obiektów TranslationRecognizer, IntentRecognizer i Connection.
- **C#:** Naprawiono zarządzanie okresem istnienia danych wejściowych audio dla obiektu ConversationTranscriber.
- Rozwiązano problem, który oznaczał, że przyczyna wyniku nie była prawidłowo ustawiana podczas `IntentRecognizer` rozpoznawania intencji z prostych fraz.
- Rozwiązano problem, który `SpeechRecognitionEventArgs` powodować, że przesunięcie wyniku nie było poprawnie ustawione.
- Rozwiązano problem z wyścigiem, w którym zestaw SDK próbował wysłać komunikat sieciowy przed otwarciem połączenia websocket. Był odtwarzalny podczas `TranslationRecognizer` dodawania uczestników.
- Naprawiono przecieki pamięci w a aparatze rozpoznawania słów kluczowych.

**Samples**
- **Go:** Dodano przewodnik Szybki start dla funkcji [rozpoznawania mowy](./get-started-speech-to-text.md?pivots=programming-language-go) i [niestandardowego asystenta głosowego.](./quickstarts/voice-assistants.md?pivots=programming-language-go) Przykładowy kod można [znaleźć tutaj.](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples) 
- **JavaScript:** dodano przewodniki Szybki start [](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script)dotyczące funkcji tłumaczenia tekstu na [mowę,](./get-started-text-to-speech.md?pivots=programming-language-javascript)tłumaczenia [i rozpoznawanie intencji](./get-started-intent-recognition.md?pivots=programming-language-javascript).
- Przykłady rozpoznawania słów kluczowych [dla języków \# C](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) [i Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**Skrócone testowanie coVID-19:** Ze względu na zdalną pracę w ciągu ostatnich kilku tygodni nie mogliśmy wykonać tak wielu testów weryfikacji ręcznej, jak zwykle. Nie wprowadzaliśmy żadnych zmian, które naszym zdaniem mogły zostać przerwane, a nasze testy automatyczne zostały pomyślnie wykonane. Jeśli coś nam się nie udało, daj nam znać w witrynie [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Pozostań w dobrej kondycji!

## <a name="speech-sdk-1110-2020-march-release"></a>Zestaw Speech SDK 1.11.0: wersja z marca 2020 r.
**Nowe funkcje**
- Linux: Dodano obsługę systemu Red Hat Enterprise Linux (RHEL)/CentOS 7 [](./how-to-configure-rhel-centos-7.md) x64 z instrukcjami dotyczącymi sposobu konfigurowania systemu dla zestawu Speech SDK.
- Linux: Dodano obsługę języka C# na platformie .NET Core w systemach Linux ARM32 i ARM64. Przeczytaj więcej [tutaj](./speech-sdk.md?tabs=linux). 
- C#, C++: dodano w , spójny identyfikator dla wszystkich pośrednich i `UtteranceId` `ConversationTranscriptionResult` końcowych wyników rozpoznawania mowy. Szczegóły dotyczące [języków C#,](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult) [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: Dodano obsługę `Language ID` . Zobacz speech_sample.py w [repozytorium GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
- Windows: dodano obsługę skompresowanego formatu danych wejściowych audio na platformie Windows dla wszystkich aplikacji konsolowych win32. Szczegóły [można znaleźć tutaj.](./how-to-use-codec-compressed-audio-input-streams.md) 
- JavaScript: obsługa syntezy mowy (tekst na mowę) w języku NodeJS. Więcej informacji można znaleźć [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: dodaj nowe interfejsy API, aby umożliwić inspekcję wszystkich wysyłanych i odbieranych komunikatów. Więcej informacji można znaleźć [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Poprawki**
- C#, C++: Rozwiązano problem, więc `SendMessageAsync` teraz wysyła komunikat binarny jako typ binarny. Szczegóły dotyczące [języków C#,](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_) [C++](/cpp/cognitive-services/speech/connection).
- C#, C++: Rozwiązano problem, który mógł powodować awarię przy usuwaniu `Connection MessageReceived` `Recognizer` obiektu przed `Connection` obiektem. Szczegóły dotyczące [języków C#,](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived) [C++](/cpp/cognitive-services/speech/connection#messagereceived).
- Android: Rozmiar buforu audio z mikrofonu zmniejszył się z 800 do 100 ms, aby poprawić opóźnienie.
- Android: rozwiązano [problem z](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) emulatorem systemu Android x86 w Android Studio.
- JavaScript: dodano obsługę regionów w Chinach przy użyciu interfejsu `fromSubscription` API. Szczegółowe [informacje można znaleźć tutaj.](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-) 
- JavaScript: Dodaj więcej informacji o błędach dotyczących błędów połączeń z poziomu środowiska NodeJS.
        
**Samples**
- Unity: Naprawiono publiczny przykład rozpoznawania intencji, w którym importowanie danych JSON usługi LUIS nie powiadało się. Szczegółowe [informacje można znaleźć tutaj.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)
- Python: dodano przykład dla `Language ID` . Szczegółowe [informacje można znaleźć tutaj.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
    
**Testowanie skrócone covid19:** Ze względu na zdalną pracę w ciągu ostatnich kilku tygodni nie mogliśmy wykonać tak wielu testów ręcznej weryfikacji urządzenia, jak zwykle. Na przykład nie można przetestować danych wejściowych i wyjściowych mikrofonu dla osób mówiących w systemach Linux, iOS i macOS. Nie wprowadzaliśmy żadnych zmian, które mogłyby zostać przerwane na tych platformach, a nasze testy automatyczne zostały pomyślnie wykonane. W mało prawdopodobnym przypadku, gdy coś nie zostało pominięte, daj nam znać w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br> Dziękujemy za dalsze wsparcie. Jak zawsze możesz publikować pytania lub opinie w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) [lub Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Pozostań w dobrej kondycji!

## <a name="speech-sdk-1100-2020-february-release"></a>Zestaw Speech SDK 1.10.0: wersja 2020–luty

**Nowe funkcje**

 - Dodano pakiety języka Python do obsługi nowej wersji 3.8 języka Python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 support (C++, C#, Java, Python).
   > [!NOTE] 
   > Klienci muszą skonfigurować program OpenSSL zgodnie z [tymi instrukcjami.](./how-to-configure-openssl-linux.md)
 - Obsługa systemu Linux ARM32 dla systemów Debian i Ubuntu.
 - Element DialogServiceConnector obsługuje teraz opcjonalny parametr "bot ID" w pliku BotFrameworkConfig. Ten parametr umożliwia korzystanie z wielu botów Direct Line Speech z pojedynczym zasobem mowy platformy Azure. Bez określonego parametru zostanie użyty domyślny bot (określony przez Direct Line Speech konfiguracji kanału).
 - Właściwość DialogServiceConnector ma teraz właściwość SpeechActivityTemplate. Zawartość tego ciągu JSON będzie używana przez usługę Direct Line Speech do wstępnego wypełniania szerokiej gamy obsługiwanych pól we wszystkich działaniach, które docierają do bota Direct Line Speech, w tym działań generowanych automatycznie w odpowiedzi na zdarzenia, takie jak rozpoznawanie mowy.
 - TTS używa teraz klucza subskrypcji do uwierzytelniania, zmniejszając pierwsze opóźnienie bajtów pierwszego wyniku syntezy po utworzeniu syntetyzatora.
 - Zaktualizowano modele rozpoznawania mowy dla 19 lokalizacji regionalnych, aby uzyskać średnią redukcję szybkości błędów słów o 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). Nowe modele wprowadzono znaczne ulepszenia w wielu domenach, w tym dyktowanie, Call-Center transkrypcji i indeksowania wideo.

**Poprawki**

 - Usunięto usterkę, która miała miejsce, gdy transkrypcja konwersacji nie oczekiwała prawidłowo w interfejsach API języka JAVA 
 - Poprawka emulatora systemu Android x86 dla problemu z platformą GitHub na [platformie Xamarin](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Dodaj brakujący (Get| Set)Property methods to AudioConfig
 - Naprawienie usterki TTS, która nie mogła zatrzymać strumienia audioDataStream w przypadku niepowodzenia połączenia
 - Użycie punktu końcowego bez regionu spowoduje błędy USP dla translatora konwersacji
 - Generowanie identyfikatorów w aplikacjach uniwersalnych systemu Windows używa teraz odpowiednio unikatowego algorytmu identyfikatora GUID; Wcześniej i nieumyślnie domyślnie była to implementacja z wycinkiem, która często spowodowała kolizje w dużych zestawach interakcji.
 
 **Samples**
 
 - Przykład aparatu Unity do używania zestawu SPEECH SDK z [mikrofonem aparatu Unity i przesyłaniem strumieniowym w trybie wypychania](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Inne zmiany**

 - [Zaktualizowano dokumentację konfiguracji programu OpenSSL dla systemu Linux](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Zestaw Speech SDK 1.9.0: wersja ze stycznia 2020 r.

**Nowe funkcje**

- Konwersacja na wielu urządzeniach: łączenie wielu urządzeń z tymi samymi konwersacjami mowy lub tekstami i opcjonalnie tłumaczenie wysyłanych między nimi komunikatów. Dowiedz się więcej w [tym artykule.](multi-device-conversation.md) 
- Dodano obsługę rozpoznawania słów kluczowych dla pakietu AAR systemu Android i dodano obsługę wersji x86 i x64. 
- Objective-C: `SendMessage` i metody dodane do obiektu `SetMessageProperty` `Connection` . Zapoznaj się z [dokumentacją tutaj.](/objectivec/cognitive-services/speech/spxconnection)
- Interfejs API języka C++ usługi TTS obsługuje teraz funkcję wprowadzania tekstu syntezy, eliminując konieczność konwertowania ciągu na ciąg przed przekazaniem go `std::wstring` do zestawu SDK. Szczegółowe informacje można [znaleźć tutaj.](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync) 
- C#: [Identyfikator języka](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) i konfiguracja języka [źródłowego](./how-to-specify-source-language.md?pivots=programming-language-csharp) są teraz dostępne.
- JavaScript: Dodano funkcję do obiektu w celu przekazania komunikatów niestandardowych z usługi `Connection` Mowa jako wywołania zwrotnego `receivedServiceMessage` .
- JavaScript: dodano obsługę funkcji , aby ułatwić korzystanie z `FromHost API` kontenerów w środowiskach wywłaszczych i suwerennych chmur. Zapoznaj się z [dokumentacją tutaj.](speech-container-howto.md)
- JavaScript: Dziękujemy `NODE_TLS_REJECT_UNAUTHORIZED` teraz za wkład [orgads](https://github.com/orgads). Szczegółowe informacje można [znaleźć tutaj.](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)

**Fundamentalne zmiany**

- `OpenSSL` Został zaktualizowany do wersji 1.1.1b i jest statycznie połączony z podstawową biblioteką zestawu SPEECH SDK dla systemu Linux. Może to spowodować przerwę, jeśli skrzynka odbiorcza nie została zainstalowana `OpenSSL` `/usr/lib/ssl` w katalogu w systemie. Zapoznaj się z [naszą dokumentacją w](how-to-configure-openssl-linux.md) dokumentacji zestawu Speech SDK, aby omiąć problem.
- Zmieniliśmy typ danych zwracany dla języka C# z na , aby zezwolić na dostęp do danych mowy `WordLevelTimingResult.Offset` `int` `long` `WordLevelTimingResults` dłuższych niż 2 minuty.
- `PushAudioInputStream` i `PullAudioInputStream` teraz wysyłają informacje nagłówka wav do usługi Speech Service na podstawie parametru , opcjonalnie określonego `AudioStreamFormat` podczas ich tworzenia. Klienci muszą teraz używać [obsługiwanego formatu danych wejściowych audio.](how-to-use-audio-input-streams.md) Wszystkie inne formaty otrzymają nieoptymalna wyniki rozpoznawania lub mogą powodować inne problemy. 

**Poprawki**

- Zobacz aktualizację `OpenSSL` w obszarze Istotne zmiany powyżej. Naprawiliśmy zarówno sporadyczne awarie, jak i problem z wydajnością (blokowanie sporadyczne pod dużym obciążeniem) w systemach Linux i Java. 
- Java: Wprowadzono ulepszenia zamykania obiektów w scenariuszach o wysokiej współbieżności.
- Restrukturyzacja naszego pakietu NuGet. Usunęliśmy trzy kopie folderów lib i w folderach lib, dzięki czemu pakiet NuGet będzie mniejszy i szybszy do pobrania, a także dodaliśmy nagłówki potrzebne do skompilowania niektórych aplikacji natywnych `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` dla języka C++.
- Naprawiono przykłady szybkiego startu [w tym miejscu.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) Były one zamykane bez wyświetlania wyjątku "nie znaleziono mikrofonu" w systemach Linux, macOS i Windows.
- Usunięto awarię zestawu SDK z długimi wynikami rozpoznawania mowy w niektórych ścieżkach kodu, takich [jak ten przykład.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Naprawiono błąd wdrażania zestawu SDK w środowisku aplikacji internetowej platformy Azure, aby [rozwiązać ten problem klienta.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)
- Naprawiono błąd TTS podczas używania `<voice>` tagu lub `<audio>` tagu wielokrotnego do rozwiązania [tego problemu klienta.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- Naprawiono błąd TTS 401, gdy zestaw SDK został odzyskany po wstrzymaniu.
- JavaScript: Naprawiono cykliczny import danych dźwiękowych dzięki udziałowi [euirim](https://github.com/euirim). 
- JavaScript: dodano obsługę ustawiania właściwości usługi, jak dodano w wersji 1.7.
- JavaScript: rozwiązano problem, który mógł powodować błąd połączenia w wyniku ciągłych, nieudanych prób ponownego nawiązania połączenia z elementem websocket.

**Samples**

- Dodano tutaj przykład rozpoznawania słów kluczowych dla [systemu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)Android .
- W tym miejscu dodano przykład TTS dla scenariusza [serwera](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- W tym miejscu dodano przewodnik Szybki start dla konwersacji z wieloma urządzeniami dla języków C# [i C++.](quickstarts/multi-device-conversation.md)

**Inne zmiany**

- Zoptymalizowano rozmiar podstawowej biblioteki zestawu SDK w systemie Android.
- Zestaw SDK w wersji 1.9.0 i jego wersjach obsługuje typy i w polu wersji podpisu głosowego dla transkrypcji `int` `string` konwersacji.

## <a name="speech-sdk-180-2019-november-release"></a>Zestaw Speech SDK 1.8.0: wersja z listopada 2019 r.

**Nowe funkcje**

- Dodano `FromHost()` interfejs API ułatwiający korzystanie z kontenerów w sieciowych i suwerennych chmurach.
- Dodano automatyczne źródła wykrywanie języka rozpoznawania mowy (w językach Java i C++)
- Dodano `SourceLanguageConfig` obiekt rozpoznawania mowy używany do określania oczekiwanych języków źródłowych (w językach Java i C++)
- Dodano `KeywordRecognizer` obsługę w systemach Windows (UWP), Android i iOS za pośrednictwem pakietów NuGet i Unity
- Dodano interfejs API języka Java zdalnej konwersacji w celu transkrypcji konwersacji w asynchronicznych partiach.

**Fundamentalne zmiany**

- Funkcje transkrypcji konwersacji przeniesione w przestrzeni nazw `Microsoft.CognitiveServices.Speech.Transcription` .
- Części metod transkrypcji konwersacji są przenoszone do nowej `Conversation` klasy.
- Porzucona obsługa 32-bitowej (ARMv7 i x86) systemu iOS

**Poprawki**

- Poprawka awarii w przypadku użycia `KeywordRecognizer` lokalnego bez prawidłowego klucza subskrypcji usługi Mowa

**Samples**

- Przykład platformy Xamarin dla `KeywordRecognizer`
- Przykład aparatu Unity dla `KeywordRecognizer`
- Przykłady języków C++ i Java dla automatycznego źródła wykrywanie języka.

## <a name="speech-sdk-170-2019-september-release"></a>Zestaw Speech SDK 1.7.0: wersja z września 2019 r.

**Nowe funkcje**

- Dodano obsługę wersji beta dla platformy Xamarin w systemach platforma uniwersalna systemu Windows (UWP), Android i iOS
- Dodano obsługę systemu iOS dla aparatu Unity
- Dodano `Compressed` obsługę danych wejściowych dla systemów Android, iOS i Linux, a także dla mulagi i FLAC
- Dodano `SendMessageAsync` `Connection` klasę do wysyłania komunikatu do usługi
- Dodano `SetMessageProperty` do klasy w celu ustawienia właściwości `Connection` komunikatu
- TTS added bindings for Java (JRE and Android), Python, Swift, and Objective-C
- W systemie TTS dodano obsługę odtwarzania dla systemów macOS, iOS i Android.
- Dodano informacje "granica słowa" dla TTS.

**Poprawki**

- Rozwiązano problem z kompilacją IL2CPP na platformie Unity 2019 dla systemu Android
- Rozwiązano problem z źle sformułowanych nagłówków w plikach wejściowych wav przetwarzanych niepoprawnie
- Rozwiązano problem z identyfikatorami UUID, które nie były unikatowe w niektórych właściwościach połączenia
- Naprawiono kilka ostrzeżeń dotyczących specyfikatorów dopuszczania wartości null w powiązaniach swift (mogą wymagać małych zmian w kodzie)
- Usunięto usterkę, która powodowała, że połączenia websocket zostały niezakończenie zamknięte pod obciążeniem sieci
- Rozwiązano problem w systemie Android, który czasami powoduje zduplikowane identyfikatory wrażenia używane przez `DialogServiceConnector`
- Ulepszenia stabilności połączeń w interakcjach wieloetapowych i raportowanie błędów (za pośrednictwem zdarzeń) w `Canceled` przypadku ich wystąpienia `DialogServiceConnector`
- `DialogServiceConnector` Początek sesji będzie teraz prawidłowo dostarczać zdarzenia, w tym podczas `ListenOnceAsync()` wywoływania podczas aktywnego `StartKeywordRecognitionAsync()`
- Rozwiązano awarię skojarzoną `DialogServiceConnector` z odbieranych działań

**Samples**

- Przewodnik Szybki start dla platformy Xamarin
- Zaktualizowano przewodnik Szybki start dla języka CPP z informacjami o procesorze ARM64 w systemie Linux
- Zaktualizowano przewodnik Szybki start aparatu Unity z informacjami o systemie iOS

## <a name="speech-sdk-160-2019-june-release"></a>Zestaw Speech SDK 1.6.0: wersja z czerwca 2019 r.

**Samples**

- Przykłady szybkiego startu dotyczące funkcji rozpoznawania tekstu na mowę na platformie UWP i w a aparatu Unity
- Przykładowy przewodnik Szybki start dla systemu Swift w systemie iOS
- Przykłady aparatu Unity do tłumaczenia & rozpoznawanie intencji mowy
- Zaktualizowano przykłady przewodnika Szybki start dla `DialogServiceConnector`

**Ulepszenia/zmiany**

- Przestrzeń nazw okna dialogowego:
  - Zmieniono nazwę polecenia `SpeechBotConnector` na `DialogServiceConnector`
  - Zmieniono nazwę polecenia `BotConfig` na `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` został ponownie zamapowany na `DialogServiceConfig::FromBotSecret()`
  - Wszystkie istniejące Direct Line Speech klientów nadal będą obsługiwane po zmianie nazwy
- Aktualizacja adaptera REST usługi TTS w celu obsługi serwera proxy, połączenia trwałego
- Ulepszenie komunikatu o błędzie po przekazyciu nieprawidłowego regionu
- Swift/Objective-C:
  - Ulepszone raportowanie błędów: Metody, które mogą spowodować błąd, są teraz dostępne w dwóch wersjach: jednej, która uwidacznia obiekt do obsługi błędów, i jednej, która `NSError` zgłasza wyjątek. Pierwsze z nich są widoczne dla swift. Ta zmiana wymaga adaptacji do istniejącego kodu Swift.
  - Ulepszona obsługa zdarzeń

**Poprawki**

- Poprawka dla usług TTS: gdy `SpeakTextAsync` przyszłość została zwrócona bez oczekiwania na ukończenie renderowania dźwięku
- Poprawka marshalingu ciągów w języku C# w celu włączenia obsługi pełnego języka
- Poprawka problemu z aplikacją .NET Core w celu załadowania podstawowej biblioteki z platformą docelową net461 w przykładach
- Poprawka rozmysłu w przypadku okazjonalnych problemów z wdrażaniem bibliotek natywnych w folderze wyjściowym w przykładach
- Poprawka niezawodnego zamykania gniazda sieci Web
- Poprawka możliwych awarii podczas otwierania połączenia pod dużym obciążeniem w systemie Linux
- Poprawka braku metadanych w pakiecie framework dla systemu macOS
- Poprawka w przypadku problemów z programem `pip install --user` w systemie Windows

## <a name="speech-sdk-151"></a>Zestaw SPEECH SDK 1.5.1

Jest to wersja poprawki usterki, która ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję zestawu SDK w języku JavaScript.

**Poprawki**

- Poprawka fromSubscription w przypadku korzystania z transkrypcji konwersacji.
- Naprawiono usterkę w funkcji wyszukiwania słów kluczowych dla asystentów głosowych.

## <a name="speech-sdk-150-2019-may-release"></a>Zestaw Speech SDK 1.5.0: wersja 2019 — maj

**Nowe funkcje**

- Funkcja wyszukiwania słów kluczowych (KWS) jest teraz dostępna dla systemów Windows i Linux. Funkcje kwS mogą współpracować z dowolnym typem mikrofonu. Oficjalna obsługa kws jest jednak obecnie ograniczona do macierzy mikrofonów dostępnych w sprzęcie Azure Kinect DK lub zestawie SPEECH Devices SDK.
- Funkcja wskazówki dotyczącej fraz jest dostępna za pośrednictwem zestawu SDK. Aby uzyskać więcej informacji, zobacz [tutaj](./get-started-speech-to-text.md).
- Funkcja transkrypcji konwersacji jest dostępna za pośrednictwem zestawu SDK. Zobacz [tutaj](./conversation-transcription.md).
- Dodaj obsługę asystentów głosowych przy użyciu Direct Line Speech kanału.

**Samples**

- Dodano przykłady dla nowych funkcji lub nowych usług obsługiwanych przez zestaw SDK.

**Ulepszenia/zmiany**

- Dodano różne właściwości rozpoznawania w celu dostosowania zachowania usługi lub wyników usługi (na przykład maskowania wulgaryzmów i innych).
- Teraz można skonfigurować aparat rozpoznawania za pomocą standardowych właściwości konfiguracji, nawet jeśli został utworzony aparat rozpoznawania `FromEndpoint` .
- Właściwość Objective-C: `OutputFormat` została dodana do `SPXSpeechConfiguration` .
- Zestaw SDK obsługuje teraz dystrybucję Debian 9 jako dystrybucję systemu Linux.

**Poprawki**

- Rozwiązano problem, który polegał na tym, że zasób prelegenta został zbyt wcześnie zdestrukcjonowany w funkcji tekstu na mowę.

## <a name="speech-sdk-142"></a>Zestaw SPEECH SDK 1.4.2

Jest to wersja poprawki usterki, która ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję zestawu SDK w języku JavaScript.

## <a name="speech-sdk-141"></a>Zestaw SPEECH SDK 1.4.1

Jest to wersja tylko dla języka JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

- Uniemożliwiaj pakietowi internetowemu ładowanie https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Zestaw Speech SDK 1.4.0: wersja 2019–kwiecień

**Nowe funkcje**

- Zestaw SDK obsługuje teraz usługę przetwarzania tekstu na mowę jako wersję beta. Jest ona obsługiwana w systemach Windows i Linux Desktop w językach C++ i C#. Aby uzyskać więcej informacji, zapoznaj się z omówieniem funkcji ["tekst na mowę".](text-to-speech.md#get-started)
- Zestaw SDK obsługuje teraz pliki audio MP3 i Opus/OGG jako pliki wejściowe strumienia. Ta funkcja jest dostępna tylko w systemie Linux w językach C++ i C# i jest obecnie w wersji beta (więcej [szczegółów można znaleźć tutaj).](how-to-use-codec-compressed-audio-input-streams.md)
- Zestaw SPEECH SDK dla języków Java, .NET Core, C++ i Objective-C uzyskał obsługę systemu macOS. Obsługa języka Objective-C dla systemu macOS jest obecnie w wersji beta.
- iOS: zestaw SPEECH SDK dla systemu iOS (Objective-C) jest teraz również publikowany jako cocoaPod.
- JavaScript: obsługa nie domyślnego mikrofonu jako urządzenia wejściowego.
- JavaScript: obsługa serwera proxy dla Node.js.

**Samples**

- Dodano przykłady użycia zestawu SDK usługi Mowa w języku C++ i języka Objective-C w systemie macOS.
- Dodano przykłady pokazujące użycie usługi funkcji tekstu na mowę.

**Ulepszenia/zmiany**

- Python: dodatkowe właściwości wyników rozpoznawania są teraz udostępniane za pośrednictwem `properties` właściwości .
- Aby uzyskać dodatkową obsługę tworzenia i debugowania, możesz przekierować rejestrowanie i informacje diagnostyczne zestawu SDK do pliku dziennika (więcej [szczegółów można znaleźć tutaj).](how-to-use-logging.md)
- JavaScript: zwiększenie wydajności przetwarzania dźwięku.

**Poprawki**

- Mac/iOS: Usunięto usterkę, która prowadziła do długiego oczekiwania, gdy nie można było nawiązane połączenie z usługą rozpoznawania mowy.
- Python: ulepsza obsługę błędów dla argumentów w wywołaniach zwrotnych języka Python.
- JavaScript: Naprawiono błędne raportowanie stanu dla mowy zakończone w przypadku żądaniaSesja.

## <a name="speech-sdk-131-2019-february-refresh"></a>Zestaw Speech SDK 1.3.1: odświeżanie z lutego 2019 r.

Jest to wersja poprawki usterki, która ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję zestawu SDK w języku JavaScript.

**Poprawka**

- Naprawiono przeciek pamięci podczas korzystania z danych wejściowych mikrofonu. Nie ma to wpływu na dane wejściowe oparte na strumieniu ani na plikach.

## <a name="speech-sdk-130-2019-february-release"></a>Zestaw SPEECH SDK 1.3.0: wersja 2019–luty

**Nowe funkcje**

- Zestaw SPEECH SDK obsługuje wybór mikrofonu wejściowego za pośrednictwem `AudioConfig` klasy . Dzięki temu można przesyłać strumieniowo dane audio do usługi mowa z mikrofonu, który nie jest domyślny. Aby uzyskać więcej informacji, zobacz dokumentację opisową [wybór urządzenia wejściowego audio.](how-to-select-audio-input-devices.md) Ta funkcja nie jest jeszcze dostępna w języku JavaScript.
- Zestaw SPEECH SDK obsługuje teraz unity w wersji beta. Przekazać opinię za pośrednictwem sekcji dotyczącej problemu w [przykładowym repozytorium GitHub.](https://aka.ms/csspeech/samples) Ta wersja obsługuje środowisko Unity w systemach Windows x86 i x64 (aplikacje klasyczne lub platforma uniwersalna systemu Windows) oraz Android (ARM32/64, x86). Więcej informacji można znaleźć w naszym [przewodniku Szybki start dla aparatu Unity.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity)
- Plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dostarczony w poprzednich wersjach) nie jest już potrzebny. Ta funkcja jest teraz zintegrowana z podstawowym zestawem SDK.

**Samples**

Następująca nowa zawartość jest dostępna w naszym [przykładowym repozytorium:](https://aka.ms/csspeech/samples)

- Dodatkowe przykłady dla `AudioConfig.FromMicrophoneInput` usługi .
- Dodatkowe przykłady języka Python do rozpoznawania i tłumaczenia intencji.
- Dodatkowe przykłady użycia obiektu `Connection` w systemie iOS.
- Dodatkowe przykłady języka Java do tłumaczenia przy użyciu danych wyjściowych audio.
- Nowy przykład do użycia interfejsu [API REST transkrypcji wsadowej.](batch-transcription.md)

**Ulepszenia/zmiany**

- Python
  - Ulepszono weryfikację parametrów i komunikaty o błędach w programie `SpeechConfig` .
  - Dodaj obsługę `Connection` obiektu .
  - Obsługa 32-bitowego języka Python (x86) w systemie Windows.
  - Zestaw Speech SDK dla języka Python nie jest już w wersji beta.
- iOS
  - Zestaw SDK jest teraz zbudowany przy pomocy zestawu SDK systemu iOS w wersji 12.1.
  - Zestaw SDK obsługuje teraz system iOS w wersji 9.2 lub nowszej.
  - Ulepsz dokumentację referencyjną i napraw kilka nazw właściwości.
- JavaScript
  - Dodaj obsługę `Connection` obiektu .
  - Dodawanie plików definicji typu dla powiązanego języka JavaScript
  - Początkowa obsługa i implementacja wskazówek dotyczących fraz.
  - Zwracanie kolekcji właściwości za pomocą pliku JSON usługi w celu rozpoznawania
- Biblioteki DLL systemu Windows zawierają teraz zasób wersji.
- Jeśli tworzysz aparat `FromEndpoint` rozpoznawania, możesz dodać parametry bezpośrednio do adresu URL punktu końcowego. Nie `FromEndpoint` można skonfigurować rozpoznawania za pomocą standardowych właściwości konfiguracji.

**Poprawki**

- Pusta nazwa użytkownika i hasło serwera proxy nie były poprawnie obsługiwane. W tej wersji, jeśli nazwa użytkownika serwera proxy i hasło serwera proxy zostaną ustawione na pusty ciąg, nie zostaną one przesłane podczas nawiązywania połączenia z serwerem proxy.
- SessionId utworzone przez zestaw SDK nie zawsze były rzeczywiście losowe dla niektórych &nbsp; języków/środowisk. Dodano inicjowanie generatora losowego w celu rozwiązania tego problemu.
- Usprawnij obsługę tokenu autoryzacji. Jeśli chcesz użyć tokenu autoryzacji, określ wartość w wartości i `SpeechConfig` pozostaw klucz subskrypcji pusty. Następnie utwórz aparat rozpoznawania w zwykły sposób.
- W niektórych przypadkach `Connection` obiekt nie został poprawnie zwolniony. Ten problem został rozwiązany.
- Przykład w języku JavaScript został naprawiony w celu obsługi danych wyjściowych audio syntezy tłumaczenia również w przeglądarce Safari.

## <a name="speech-sdk-121"></a>Zestaw SPEECH SDK 1.2.1

Jest to wersja tylko dla języka JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

- Zakończenie strumienia po zakończeniu, a nie na stronie speech.end.
- Naprawiono usterkę w pompie audio, która nie zaplanowała następnego wysyłania, jeśli bieżące wysyłanie nie powiodło się.
- Naprawienie ciągłego rozpoznawania przy użyciu tokenu uwierzytelniania.
- Poprawka usterki dla różnych punktów końcowych/rozpoznawczych.
- Ulepszenia dokumentacji.

## <a name="speech-sdk-120-2018-december-release"></a>Zestaw Speech SDK 1.2.0: wersja z grudnia 2018 r.

**Nowe funkcje**

- Python
  - Wersja beta obsługi języka Python (wersja 3.5 i nowsza) jest dostępna w tej wersji. Aby uzyskać więcej informacji, zobacz tutaj](quickstart-python.md).
- JavaScript
  - Zestaw SPEECH SDK dla języka JavaScript został opracowany jako open source. Kod źródłowy jest dostępny w witrynie [GitHub.](https://github.com/Microsoft/cognitive-services-speech-sdk-js)
  - Teraz obsługujemy Node.js więcej informacji można znaleźć [tutaj.](./get-started-speech-to-text.md)
  - Ograniczenie długości sesji dźwiękowych zostało usunięte. Ponowne nawiązywanie połączenia nastąpi automatycznie pod osłoną.
- `Connection` Obiektu
  - Z `Recognizer` obiektu można uzyskać dostęp do `Connection` obiektu . Ten obiekt umożliwia jawne zainicjowanie połączenia z usługą i zasubskrybowanie zdarzeń nawiązywania połączenia i rozłączania.
    (Ta funkcja nie jest jeszcze dostępna w językach JavaScript i Python).
- Obsługa systemu Ubuntu 18.04.
- Android
  - Włączono obsługę funkcji ProGuard podczas generowania plików APK.

**Ulepszenia**

- Ulepszenia w wewnętrznym użyciu wątków, zmniejszające liczbę wątków, blokad i mutex.
- Ulepszone raportowanie błędów/informacje. W kilku przypadkach komunikaty o błędach nie były propagowane na zewnątrz.
- Zaktualizowano zależności programowe w języku JavaScript w celu używania aktualnych modułów.

**Poprawki**

- Naprawiono przecieki pamięci spowodowane niezgodnością typu w `RecognizeAsync` pliku .
- W niektórych przypadkach zdarzały się wyjątki.
- Naprawianie przecieku pamięci w argumentach zdarzenia tłumaczenia.
- Rozwiązano problem z blokowaniem podczas ponownego nawiązywania połączenia w długotrwałych sesjach.
- Rozwiązano problem, który mógł prowadzić do braku wyniku końcowego dla nieudanych tłumaczeń.
- C#: Jeśli operacja nie została oczekiwana w wątku głównym, można było usunąć aparat rozpoznawania przed ukończeniem zadania `async` asynchronicznego.
- Java: Rozwiązano problem, który polegał na awarii maszyny wirtualnej Java.
- Objective-C: Naprawiono mapowanie wyliowań; Zwrócono rozpoznaną iniekcję zamiast `RecognizingIntent` .
- JavaScript: ustaw domyślny format danych wyjściowych na "simple" w pliku `SpeechConfig` .
- JavaScript: Usuwanie niespójności między właściwościami obiektu config w języku JavaScript i innych językach.

**Samples**

- Zaktualizowano i naprawiono kilka przykładów (na przykład głosy wyjściowe do tłumaczenia itp.).
- Dodano Node.js przykłady w [przykładowym repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Zestaw SPEECH SDK 1.1.0

**Nowe funkcje**

- Obsługa systemu Android x86/x64.
- Obsługa serwera proxy: w obiekcie można teraz wywołać funkcję, aby ustawić informacje o serwerze `SpeechConfig` proxy (nazwa hosta, port, nazwa użytkownika i hasło). Ta funkcja nie jest jeszcze dostępna w systemie iOS.
- Ulepszony kod błędu i komunikaty. Jeśli rozpoznawanie zwróciło błąd, ta wartość została już ustawiona (w anulowanych zdarzeniach) lub `Reason` `CancellationDetails` (w wyniku rozpoznawania) na `Error` wartość . Anulowane zdarzenie zawiera teraz dwa dodatkowe elementy członkowskie: `ErrorCode` i `ErrorDetails` . Jeśli serwer zwrócił dodatkowe informacje o błędzie z zgłoszonym błędem, będzie on teraz dostępny w nowych elementach członkowskich.

**Ulepszenia**

- Dodano dodatkową weryfikację w konfiguracji rozpoznawania i dodano dodatkowy komunikat o błędzie.
- Ulepszona obsługa długotrwałej ciszy w środku pliku dźwiękowego.
- Pakiet NuGet: w .NET Framework projektach nie można budować przy użyciu konfiguracji AnyCPU.

**Poprawki**

- Naprawiono kilka wyjątków znalezionych w aparatach rozpoznawania. Ponadto wyjątki są przechwycone i konwertowane na `Canceled` zdarzenie.
- Naprawienie przecieku pamięci w zarządzaniu właściwościami.
- Usunięto usterkę, w której plik wejściowy audio mógł ulegać awarii rozpoznawania.
- Usunięto usterkę, przez która zdarzenia mogły być odbierane po zdarzeniu zatrzymania sesji.
- Naprawiono niektóre warunki wyścigu w wątkowych.
- Rozwiązano problem ze zgodnością systemu iOS, który mógł powodować awarię.
- Ulepszenia stabilności dla obsługi mikrofonu systemu Android.
- Usunięto usterkę, która oznaczała, że aparat rozpoznawania w języku JavaScript ignorował język rozpoznawania.
- Usunięto usterkę uniemożliwiającą ustawienie `EndpointId` (w niektórych przypadkach) wartości w języku JavaScript.
- Zmieniono kolejność parametrów w funkcji AddIntent w języku JavaScript i dodano brakujący `AddIntent` podpis JavaScript.

**Samples**

- Dodano przykłady dla języków C++ i C# do użycia strumienia ściągania i wypychania w [przykładowym repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Zestaw SPEECH SDK 1.0.1

Ulepszenia niezawodności i poprawki błędów:

- Naprawiono potencjalny błąd krytyczny spowodowany stanem wyścigu w rozsyłaniu rozpoznawania
- Naprawiono potencjalny błąd krytyczny w przypadku wystąpienia nieustawienia właściwości.
- Dodano dodatkowe sprawdzanie błędów i parametrów.
- Objective-C: Naprawiono możliwy błąd krytyczny spowodowany zastępowaniem nazw w NSString.
- Objective-C: Dostosowana widoczność interfejsu API
- JavaScript: Naprawiono błąd dotyczący zdarzeń i ich ładunków.
- Ulepszenia dokumentacji.

W naszym [przykładowym repozytorium](https://aka.ms/csspeech/samples)dodano nowy przykład dla języka JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: wersja 2018–wrzesień

**Nowe funkcje**

- Obsługa języka Objective-C w systemie iOS. Zapoznaj się z [naszymi przewodnikami Szybki start języka Objective-C dla systemu iOS.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone)
- Obsługa języka JavaScript w przeglądarce. Zapoznaj się z [naszymi przewodnikami Szybki start języka JavaScript.](./get-started-speech-to-text.md)

**Fundamentalne zmiany**

- W tej wersji wprowadzono szereg przełomowych zmian.
  Sprawdź [tę stronę, aby](https://aka.ms/csspeech/breakingchanges_1_0_0) uzyskać szczegółowe informacje.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: wersja 2018–sierpień

**Nowe funkcje**

- Aplikacje platformy uniwersalnej systemu Windows sbudowaną za pomocą zestawu SPEECH SDK mogą teraz przekazywać Zestaw certyfikacji aplikacji systemu Windows (WACK).
  Zapoznaj się z [przewodnikem Szybki start platformy uniwersalnej systemu Windows.](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp)
- Obsługa wersji .NET Standard 2.0 w systemie Linux (Ubuntu 16.04 x64).
- Eksperymentalne: obsługa języka Java 8 w systemach Windows (64-bitowych) i Linux (Ubuntu 16.04 x64).
  Zapoznaj się z [Java Runtime Environment szybki start.](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre)

**Zmiana funkcjonalna**

- Uwidocznij dodatkowe szczegółowe informacje o błędach połączenia.

**Fundamentalne zmiany**

- W języku Java `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` (Android) funkcja nie wymaga już parametru path. Teraz ścieżka jest automatycznie wykrywana na wszystkich obsługiwanych platformach.
- Usunięto obiekt get-accessor właściwości w `EndpointUrl` językach Java i C#.

**Poprawki**

- W języku Java zaimplementowano teraz wynik syntezy audio w rozpoznawaczach tłumaczenia.
- Usunięto usterkę, która mogła powodować nieaktywne wątki i zwiększoną liczbę otwartych i nieużywanych gniazd.
- Rozwiązano problem, który polegał na tym, że długotrwała rozpoznawanie mogło kończyć się w trakcie transmisji.
- Naprawiono warunek wyścigu podczas zamykania rozpoznawania.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: wersja 2018–lipiec

**Nowe funkcje**

- Obsługa platformy Android (interfejs API 23: Android 6.0 Marshmallow lub wyższy). Zapoznaj się z [przewodnikem Szybki start dla systemu Android.](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android)
- Obsługa .NET Standard 2.0 w systemie Windows. Zapoznaj się z przewodnikem Szybki start [dla .NET Core.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)
- Eksperymentalne: obsługa platformy UWP w systemie Windows (wersja 1709 lub nowsza).
  - Zapoznaj się z [przewodnikem Szybki start dla platformy uniwersalnej systemu Windows.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)
  - Należy pamiętać, że aplikacje platformy uniwersalnej systemu Windows sbudowaną za pomocą zestawu SPEECH SDK nie przechodzą jeszcze Zestaw certyfikacji aplikacji systemu Windows (WACK).
- Obsługa długotrwałego rozpoznawania przy użyciu automatycznego ponownego nawiązywania połączenia.

**Zmiany funkcjonalne**

- `StartContinuousRecognitionAsync()` obsługuje rozpoznawanie długotrwałe.
- Wynik rozpoznawania zawiera więcej pól. Są one przesunięte od początku i czasu trwania dźwięku (zarówno w taktach) rozpoznanego tekstu, jak i dodatkowych wartości, które reprezentują stan rozpoznawania, na `InitialSilenceTimeout` przykład i `InitialBabbleTimeout` .
- Obsługa authorizationToken do tworzenia wystąpień fabryki.

**Fundamentalne zmiany**

- Zdarzenia rozpoznawania: `NoMatch` typ zdarzenia został scalony ze `Error` zdarzeniem.
- Nazwa formatu SpeechOutputFormat w języku C# została zmieniona na `OutputFormat` , aby zachować dopasowanie do języka C++.
- Zwracany typ niektórych metod interfejsu `AudioInputStream` nieznacznie się zmienił:
  - W języku Java `read` metoda zwraca teraz wartość zamiast `long` `int` .
  - W języku C# `Read` metoda zwraca teraz wartość zamiast `uint` `int` .
  - W języku C++ `Read` metody i zwracają teraz zamiast `GetFormat` `size_t` `int` .
- C++: Wystąpienia strumieni wejściowych audio mogą być teraz przekazywane tylko jako `shared_ptr` .

**Poprawki**

- Naprawiono nieprawidłowe wartości zwracane w wyniku, gdy `RecognizeAsync()` urósł przekrój czasu.
- Zależność od bibliotek media foundation w systemie Windows została usunięta. Zestaw SDK używa teraz podstawowych interfejsów API audio.
- Poprawka dokumentacji: Dodano [stronę regionów](regions.md) opiszjącą obsługiwane regiony.

**Znany problem**

- Zestaw SPEECH SDK dla systemu Android nie zgłasza wyników syntezy mowy do tłumaczenia. Ten problem zostanie rozwiązany w następnej wersji.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: wersja z czerwca 2018 r.

**Zmiany funkcjonalne**

- AudioInputStream

  Aparat rozpoznawania może teraz używać strumienia jako źródła audio. Aby uzyskać więcej informacji, zobacz powiązany [przewodnik.](how-to-use-audio-input-streams.md)

- Szczegółowy format danych wyjściowych

  Podczas tworzenia pliku `SpeechRecognizer` można zażądać formatu `Detailed` lub formatu `Simple` danych wyjściowych. Zawiera ocenę ufności, rozpoznany tekst, nieprzetworzoną postać leksykacyjną, znormalizowaną i znormalizowaną postać z `DetailedSpeechRecognitionResult` zamaskowanymi wulgaryzmami.

**Zmiana przerywana**

- Zmieniono wartość `SpeechRecognitionResult.Text` na z `SpeechRecognitionResult.RecognizedText` w języku C#.

**Poprawki**

- Rozwiązano możliwy problem z wywołaniem zwrotny w warstwie USP podczas zamykania.
- Jeśli aparat rozpoznawania zużywał wejściowy plik audio, dłużej niż jest to konieczne, wstrzymywał dojście do pliku.
- Usunięto kilka zakleszczeń między pompą komunikatów i aparatem rozpoznawania.
- `NoMatch`Wyzjemnić wynik, gdy uchybnie czas odpowiedzi z usługi.
- Biblioteki Media Foundation w systemie Windows są ładowane z opóźnieniem. Ta biblioteka jest wymagana tylko dla danych wejściowych mikrofonu.
- Szybkość przekazywania danych audio jest ograniczona do około dwa razy maksymalną szybkości oryginalnego dźwięku.
- W systemie Windows zestawy C# .NET mają teraz silne nazwy.
- Poprawka dokumentacji: `Region` to informacje wymagane do utworzenia rozpoznawania.

Dodano więcej przykładów i są one stale aktualizowane. Aby uzyskać najnowszy zestaw przykładów, zobacz [repozytorium GitHub z](https://aka.ms/csspeech/samples)przykładami dla zestawu Speech SDK.

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: wersja z maja 2018 r.

Ta wersja to pierwsza publiczna wersja zapoznawcza zestawu SDK Cognitive Services Speech.
