---
title: Informacje o wersji — usługa mowy
titleSuffix: Azure Cognitive Services
description: Uruchomiony dziennik wersji funkcji usługi Speech, ulepszenia, poprawki błędów i znane problemy.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 89a87cd881689f58bbc4d2b4bf2a63a992e8dae9
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461638"
---
# <a name="speech-service-release-notes"></a>Informacje o wersji usługi mowy

## <a name="text-to-speech-2020-august-release"></a>Zamiana tekstu na mowę 2020 — wydanie z sierpnia

### <a name="new-features"></a>Nowe funkcje

* **Neuronowych TTS: nowy styl wymawiania dla `en-US` Aria**. AriaNeural może wyglądać podobnie jak rzutowanie wiadomości w przypadku odczytywania wiadomości. Styl "Newscast-formaled" jest bardziej ważniejszy, podczas gdy styl "Newscast-swobodny" jest bardziej swobodny i nieformalny. Zobacz [, jak używać stylów mówiących w SSML](speech-synthesis-markup.md).

* **Niestandardowy głos: wydano nową funkcję, która umożliwia automatyczne sprawdzanie jakości danych szkoleniowych**. Po przekazaniu danych system będzie analizować różne aspekty danych audio i transkrypcji oraz automatycznie rozwiązywać lub filtrować problemy w celu poprawy jakości modelu głosu. Dotyczy to ilości dźwięku, poziomu szumu, dokładności wymowy mowy, wyrównania mowy z znormalizowanym tekstem, wyciszenia dźwięku oraz formatowania dźwięku i skryptu. 

* **Tworzenie zawartości audio: zestaw nowych funkcji umożliwiających bardziej zaawansowane funkcje dostrajania głosu i zarządzania dźwiękiem**.

    * Wymowa: funkcja dostrajania wymowy została zaktualizowana do najnowszego zestawu fonem. Możesz wybrać odpowiedni element fonem z biblioteki i uściślić wymowę wybranych wyrazów. 

    * Pobieranie: funkcja audio "Download"/"Export" została ulepszona w celu obsługi generowania dźwięku w akapicie. Zawartość można edytować w tym samym pliku/SSML, podczas gdy generowane są wiele wyjść audio. Struktura plików "Download" jest również udoskonalana. Teraz można łatwo uzyskać wszystkie dźwięki w jednym folderze. 

    * Stan zadania: udoskonalono środowisko eksportu wieloplikowego. W przypadku eksportowania wielu plików w przeszłości, jeśli jeden z plików ulegnie awarii, całe zadanie zakończy się niepowodzeniem. Ale teraz wszystkie inne pliki zostaną pomyślnie wyeksportowane. Raport zadania jest wzbogacany o bardziej szczegółowe i strukturalne informacje. Możesz sprawdzić dzienniki dla wszystkich zakończonych niepowodzeniem plików i zdań teraz za pomocą raportu. 

    * Dokumentacja usługi SSML: połączona z dokumentem SSML, ułatwiająca sprawdzenie reguł korzystania ze wszystkich funkcji dostrajania.

* **Interfejs API listy głosu został zaktualizowany tak, aby zawierał przyjazną dla użytkownika nazwę wyświetlaną i style odmowy obsługiwane dla głosów neuronowych**.

### <a name="general-tts-voice-quality-improvements"></a>Ulepszenia jakości głosu dotyczące ogólnej TTS

* Zredukowany Błąd wymowy na poziomie wyrazu `ru-RU` (%) (błędy obniżone o 56%) i `sv-SE` (błędy obniżone o 49%)

* Ulepszono Polyphony czytania wyrazów na `en-US` neuronowych głosy o 40%. Przykłady słów Polyphony to "read", "Live", "Content", "Record", "Object" itp. 

* Ulepszono naturalność tonu pytania w `fr-FR` . MOS (średnia ocena opinii): + 0,28

* Zaktualizowano vocoders dla następujących głosów z ulepszeniami dokładności i ogólną wydajnością wydajności do 40%.

    | Regionalne | Połączenia głosowe |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Poprawki błędów

* Naprawiono wiele usterek przy użyciu narzędzia do tworzenia zawartości audio 
    * Rozwiązano problem z autoodświeżaniem. 
    * Rozwiązano problemy ze stylami głosu w polu zh-CN w regionie Azja Wschodnia południowo.
    * Rozwiązano problem dotyczący stabilności, w tym błąd eksportu ze znacznikiem "Break" i błędy w interpunkcji.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Nowe ustawienia zamiany mowy na tekst: 2020 — wydanie z sierpnia
Zamiana mowy na tekst wydana 26 nowych ustawień regionalnych w sierpniu: 2 Języki europejskie `cs-CZ` i `hu-HU` , 5 angielskie ustawienia regionalne oraz 19 hiszpańskie ustawienia regionalne, które obejmują większość krajów Ameryki Południowej. Poniżej znajduje się lista nowych ustawień regionalnych. Zapoznaj się z pełną [listą](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support)języków.

| Regionalne  | Język                          |
|---------|-----------------------------------|
| `cs-CZ` | Czeski (Czechy)            | 
| `en-HK` | Angielski (Hongkong SAR)               | 
| `en-IE` | Angielski (Irlandia)                 | 
| `en-PH` | Angielski (Filipiny)             | 
| `en-SG` | Angielski (Singapur)               | 
| `en-ZA` | Angielski (Republika Południowej Afryki)            | 
| `es-AR` | Hiszpański (Argentyna)               | 
| `es-BO` | Hiszpański (Boliwia)                 | 
| `es-CL` | Hiszpański (Chile)                   | 
| `es-CO` | Hiszpański (Kolumbia)                | 
| `es-CR` | Hiszpański (Kostaryka)              | 
| `es-CU` | Hiszpański (Kuba)                    | 
| `es-DO` | Hiszpański (Dominikana)      | 
| `es-EC` | Hiszpański (Ekwador)                 | 
| `es-GT` | Hiszpański (Gwatemala)               | 
| `es-HN` | Hiszpański (Honduras)                | 
| `es-NI` | Hiszpański (Nikaragua)               | 
| `es-PA` | Hiszpański (Panama)                  | 
| `es-PE` | Hiszpański (Peru)                    | 
| `es-PR` | Hiszpański (Portoryko)             | 
| `es-PY` | Hiszpański (Paragwaj)                | 
| `es-SV` | Hiszpański (Salwador)             | 
| `es-US` | Hiszpański (USA)                     | 
| `es-UY` | Hiszpański (Urugwaj)                 | 
| `es-VE` | Hiszpański (Wenezuela)               | 
| `hu-HU` | Węgierski (Węgry)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0:2020 — wydanie z lipca

**Uwaga**: zestaw Speech SDK w systemie Windows zależy od udostępnionego Microsoft Visual C++ pakietu redystrybucyjnego dla programu Visual Studio 2015, 2017 i 2019. Pobierz i zainstaluj go w [tym miejscu](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Nowe funkcje**
- **C#**: Dodano obsługę transkrypcji konwersacji asynchronicznych. [Tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-async-conversation-transcription)znajdziesz dokumentację.  
- **JavaScript**: dodano obsługę rozpoznawanie osoby mówiącej zarówno dla [przeglądarki](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) , jak i [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **JavaScript**: Dodano obsługę automatycznego wykrywania języka/identyfikatora języka. [Tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-javascript)znajdziesz dokumentację.
- **Zamierzenie-C**: Dodano obsługę [transkrypcji](https://docs.microsoft.com/azure/cognitive-services/speech-service/conversation-transcription)konwersacji i konwersacji na [wiele urządzeń](https://docs.microsoft.com/azure/cognitive-services/speech-service/multi-device-conversation) . 
- **Python**: Dodano obsługę kompresji audio dla języka Python w systemach Windows i Linux. [Tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)znajdziesz dokumentację. 

**Poprawki błędów**
- **Wszystkie**: Rozwiązano problem, który spowodował, że KeywordRecognizer nie przeniesieł strumieni do przodu po rozpoznaniu.
- **Wszystkie**: Rozwiązano problem, który spowodował, że strumień uzyskany z KeywordRecognitionResult nie zawiera słowa kluczowego.
- **Wszystko**: Rozwiązano problem polegający na tym, że SendMessageAsync nie wyśle komunikatu przez sieć po zakończeniu oczekiwania przez użytkowników.
- **Wszystkie**: Naprawiono awarię w interfejsie API rozpoznawanie osoby mówiącej, gdy użytkownicy wywołują metodę VoiceProfileClient:: SpeakerRecEnrollProfileAsync wiele razy i nie oczekują na zakończenie wywołań.
- **Wszystkie**: Naprawiono włączenie rejestrowania plików w klasach VoiceProfileClient i SpeakerRecognizer.
- **JavaScript**: rozwiązano [problem związany](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) z ograniczaniem, gdy przeglądarka jest zminimalizowana.
- **JavaScript**: rozwiązano [problem](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) dotyczący przecieku pamięci w strumieniach.
- **JavaScript**: dodano buforowanie dla odpowiedzi protokołu OCSP z NodeJS.
- **Java**: Rozwiązano problem, który spowodował, że pola BigInteger zawsze zwracają 0.
- **iOS**: rozwiązano [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) z publikowaniem aplikacji opartych na zestawie SDK mowy w sklepie iOS App Store.

**Samples**
- **C++**: dodano przykładowy kod dla rozpoznawanie osoby mówiącej w [tym miejscu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**COVID-19 przetestowanie:** Ze względu na to, że praca zdalna jest wykonywana w ciągu ostatnich kilku tygodni, nie udało nam się wykonać tyle testów weryfikacyjnych ręcznych. Firma Microsoft nie wprowadziła żadnych zmian, które mogą być uszkodzone i wszystkie testy zautomatyzowane zostały zakończone powodzeniem. W mało prawdopodobnym zdarzeniu, poinformuj nas o tym w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Bądź w dobrej kondycji

## <a name="text-to-speech-2020-july-release"></a>Zamiana tekstu na mowę 2020 — wydanie z lipca

### <a name="new-features"></a>Nowe funkcje

* **Neuronowych TTS, 15 nowych neuronowych głosów**: nowe głosy dodawane do portfolio neuronowych TTS to Salma in `ar-EG` arabski (Egipt), Zariyah in `ar-SA` arabski (Arabia Saudyjska), Alba in `ca-ES` kataloński (Hiszpania), Christel in duński ( `da-DK` Dania), Neerja w `es-IN` języku angielskim (Indie), Noora w języku `fi-FI` fińskim (Finlandia), swara w `hi-IN` języku hindi (Indie), Colette w `nl-NL` języku holenderskim (Holandia), Zofia in `pl-PL` Polski (Polska), Fernanda in `pt-PT` portugalski (Portugalia), dariya in `ru-RU` rosyjski (Rosja), Hillevi w `sv-SE` szwedzki (Szwecja), Achara in `th-TH` tajski (Tajlandia), HiuGaai in `zh-HK` chiński (kantoński, tradycyjny) i HsiaoYu `zh-TW` (tajwański mandarynki). Sprawdź wszystkie [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices).  

* **Niestandardowy głos, usprawnione testowanie głosu przy użyciu przepływu szkoleniowego, aby uprościć środowisko użytkownika**: dzięki nowej funkcji testowania każdy głos zostanie automatycznie przetestowany przy użyciu wstępnie zdefiniowanego zestawu testów zoptymalizowanego dla każdego języka, który będzie obejmować scenariusze ogólne i asystenta głosowego. Te zestawy testów są starannie wybierane i przetestowane w taki sposób, aby obejmowały typowe przypadki użycia i fonemów w języku. Oprócz tego użytkownicy nadal mogą wybrać opcję przekazania własnych skryptów testowych podczas uczenia modelu.

* **Tworzenie zawartości audio: wydano zestaw nowych funkcji umożliwiających bardziej zaawansowane funkcje dostrajania głosu i zarządzania dźwiękiem**

    * `Pitch`, `rate` i `volume` są ulepszone do obsługi dostrajania ze wstępnie zdefiniowaną wartością, np. wolno, średnim i szybkim. Teraz jest to proste, aby użytkownicy mogli wybrać wartość "stała" do edycji dźwięku.

    ![Dostrajanie audio](media/release-notes/audio-tuning.png)

    * Użytkownicy mogą teraz przeglądać `Audio history` plik dla swojego pliku służbowego. Dzięki tej funkcji użytkownicy mogą łatwo śledzić wszystkie wygenerowane audio związane z plikiem roboczym. Mogą sprawdzić wersję historii i porównać jakość podczas dostrajania jednocześnie. 

    ![Historia audio](media/release-notes/audio-history.png)

    * Ta `Clear` Funkcja jest teraz bardziej elastyczna. Użytkownicy mogą czyścić określony parametr strojenia, zachowując inne parametry dostępne dla wybranej zawartości.  

    * Film wideo z samouczkiem został dodany na [stronie docelowej](https://speech.microsoft.com/audiocontentcreation) , aby ułatwić użytkownikom szybkie rozpoczęcie pracy z dostrajaniem głosu TTS i zarządzaniem dźwiękiem. 

### <a name="general-tts-voice-quality-improvements"></a>Ulepszenia jakości głosu dotyczące ogólnej TTS

* Ulepszona vocoder TTS w programie w celu uzyskania większej wierności i małych opóźnień.

    * Zaktualizowano Elsa w `it-IT` nowym vocoder, który osiągnął wartość "CMOS" + 0,464 (wynik porównawczy) w jakości głosu, 40% szybciej w syntezie i o 30% obniżki przy opóźnieniu pierwszego bajtu. 
    * Zaktualizowano Xiaoxiao w `zh-CN` systemie do nowego vocoderu i wzmocnienia CMOS 0148 dla domeny ogólnej, + 0,348 dla stylu Newscast oraz + 0,195 dla stylu Lyrical. 

* Zaktualizowaliśmy `de-DE` i `ja-JP` modele głosu, aby dane wyjściowe TTS były bardziej naturalne.
    
    * Zaktualizowano Katja w programie `de-DE` przy użyciu najnowszej metody modelowania Prosody, korzyści z MOS (średnia ocena opinii) to + 0,13. 
    * Zaktualizowano Nanami w programie `ja-JP` przy użyciu nowego modelu Prosody akcentu, a MOS (średnia ocena opinii) to + 0,19;  

* Ulepszona dokładność wymowy na poziomie wyrazów w pięciu językach.

    | Język | Zmniejszenie błędów wymowy |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 7 |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Poprawki błędów

* Odczytywanie waluty
    * Rozwiązano problem z odczytem waluty dla `es-ES` i `es-MX`
     
    | Język | Dane wejściowe | Odczyt po ulepszeniu |
    |---|---|---|
    | `es-MX` | $1,58 | niepeso cincuenta y Ocho centavos |
    | `es-ES` | $1,58 | dólar cincuenta y Ocho centavos |

    * Obsługa ujemnej waluty (na przykład "-€325") w następujących ustawieniach regionalnych:,,,, `en-US` `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

* Ulepszono odczytywanie adresów w `pt-PT` .
* Naprawione problemy dotyczące `en-AU` wymowy Natasha () i Libby ( `en-UK` ) w wyrazie "for" i "cztery".  
* Naprawione usterki narzędzia do tworzenia zawartości audio
    * Dodatkowe i nieoczekiwane pauzy po usunięciu akapitu drugiego.  
    * Funkcja "No Break" została dodana z powrotem z błędu regresji. 
    * Losowy problem z odświeżaniem programu Speech Studio został naprawiony.  

### <a name="samplessdk"></a>Przykłady/zestaw SDK

* JavaScript: naprawia problem z odtwarzaniem w przeglądarce Firefox i Safari w systemach macOS i iOS. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1:2020 — wydanie z czerwca
**Interfejs wiersza polecenia mowy (znany również jako SPX)**
-   Dodano funkcje wyszukiwania pomocy w interfejsie wiersza polecenia:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Zaktualizowano w celu pracy z nowo wdrożonymi interfejsami API programu Batch 3.0 i Custom Speech:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Nowe funkcje**
-   **C \# , C++**: Rozpoznawanie osoby mówiącej wersja zapoznawcza: Ta funkcja umożliwia identyfikację osoby mówiącej, która jest w trakcie rozmowy? Rozpocznij od [przeglądu](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/speaker-recognition-overview), Przeczytaj [artykuł podstawowe informacje dotyczące rozpoznawanie osoby mówiącej](https://docs.microsoft.com/azure/cognitive-services/speech-service/speaker-recognition-basics)lub [Dokumentacja interfejsu API](https://docs.microsoft.com/rest/api/speakerrecognition/).

**Poprawki błędów**
-   **C \# , C++**: stałe nagrywanie mikrofonu nie działa w 1,12 podczas rozpoznawania osoby mówiącej.
-   **JavaScript**: poprawki dla zamiany tekstu na mowę w przeglądarce Firefox i Safari w systemach MacOS i iOS.
-   Poprawka dla programu sprawdzania aplikacji systemu Windows błąd naruszenia zasad dostępu podczas transkrypcji konwersacji podczas korzystania ze strumienia ośmiu kanałów.
-   Poprawka dotycząca naruszenia zasad dostępu weryfikatora aplikacji systemu Windows w przypadku tłumaczenia konwersacji na wiele urządzeń.

**Samples**
-   **C#**: [przykładowy kod](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) do rozpoznawania osoby mówiącej.
-   **C++**: [przykładowy kod](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) do rozpoznawania osoby mówiącej.
-   **Java**: [przykładowy kod](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) do rozpoznawania przeznaczenie w systemie Android. 

**COVID-19 przetestowanie:** Ze względu na to, że praca zdalna jest wykonywana w ciągu ostatnich kilku tygodni, nie udało nam się wykonać tyle testów weryfikacyjnych ręcznych. Firma Microsoft nie wprowadziła żadnych zmian, które mogą być uszkodzone i wszystkie testy zautomatyzowane zostały zakończone powodzeniem. W mało prawdopodobnym zdarzeniu, poinformuj nas o tym w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Bądź w dobrej kondycji


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0:2020 — może wydać
**Interfejs wiersza polecenia mowy (znany również jako SPX)**
- **SPX** to nowe narzędzie wiersza polecenia, które umożliwia przeprowadzenie rozpoznawania, syntezy, tłumaczenia, transkrypcji partii i niestandardowego zarządzania mowy z poziomu wiersza polecenia. Służy do testowania usługi mowy lub do skryptowania zadań usługi mowy, które należy wykonać. Pobierz narzędzie i przeczytaj dokumentację [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-overview).

**Nowe funkcje**
- **Przejdź**: nowe języki języka go dla funkcji [rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) i [niestandardowego asystenta głosowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go). Skonfiguruj [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-go)środowisko deweloperskie. Aby zapoznać się z przykładowym kodem, zobacz sekcję przykłady poniżej. 
- **JavaScript**: Dodano obsługę funkcji zamiany tekstu na mowę przez przeglądarkę. [Tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech-audio-file?pivots=programming-language-JavaScript)znajdziesz dokumentację.
- **C++, C#, Java**: nowe `KeywordRecognizer` obiekty i interfejsy API są obsługiwane na platformach Windows, Android, Linux & iOS. Zapoznaj się z dokumentacją [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-overview). Aby zapoznać się z przykładowym kodem, zobacz sekcję przykłady poniżej. 
- **Java**: dodano konwersację dla urządzeń z obsługą tłumaczenia. Zobacz dokument Reference [tutaj](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.transcription).

**Udoskonalenia & optymalizacji**
- **JavaScript**: Optymalizacja mikrofonu zoptymalizowanej przeglądarki poprawianie dokładności rozpoznawania mowy.
- **Java**: powiązania refaktoryzacji przy użyciu bezpośredniej implementacji JNI bez SWIG. Ta zmiana zmniejsza się, 10X rozmiar powiązań dla wszystkich pakietów języka Java używanych dla systemów Windows, Android, Linux i Mac oraz ułatwia dalsze opracowywanie implementacji języka Java zestawu Speech SDK.
- **Linux**: Zaktualizowano [dokumentację](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux) pomocy technicznej z najnowszymi uwagami specyficznymi dla programu RHEL 7.
- Ulepszona logika połączenia, aby próbować łączyć się wiele razy w przypadku wystąpienia błędów usługi i sieci.
- Zaktualizowano stronę szybkiego startu mowy [Portal.Azure.com](https://portal.azure.com) , aby pomóc deweloperom w następnym kroku w podróży mowy na platformie Azure.

**Poprawki błędów**
- **C#, Java**: rozwiązano [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) z ładowaniem bibliotek SDK w systemie Linux ARM (zarówno 32 bitów, jak i 64 bitów).
- **C#**: stałe usuwanie uchwytów natywnych dla obiektów TranslationRecognizer, IntentRecognizer i Connection.
- **C#**: stałe dane wejściowe okresu istnienia audio dla obiektu ConversationTranscriber.
- Rozwiązano problem, `IntentRecognizer` w którym powód wyniku nie został poprawnie ustawiony podczas rozpoznawania intencji z prostych fraz.
- Rozwiązano problem polegający na tym, że `SpeechRecognitionEventArgs` przesunięcie wyniku nie zostało prawidłowo ustawione.
- Naprawiono sytuację wyścigu, w której zestaw SDK próbuje wysłać komunikat sieciowy przed otwarciem połączenia z użyciem protokołu WebSocket. Program został nawielony `TranslationRecognizer` podczas dodawania uczestników.
- Stałe przecieki pamięci w aparacie rozpoznawania słów kluczowych.

**Samples**
- **Przejdź**: dodano Przewodniki Szybki Start dla [funkcji rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) i [niestandardowego asystenta głosowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go). [Tutaj](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples)znajdziesz przykładowy kod. 
- **JavaScript**: dodano Przewodniki Szybki Start dla [zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech?pivots=programming-language-javascript), [tłumaczenia](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-speech-translation?tabs=script&pivots=programming-language-csharp)i [rozpoznawanie intencji](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition?pivots=programming-language-javascript).
- Przykłady rozpoznawania słów kluczowych dla języków [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) i [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**COVID-19 przetestowanie:** Ze względu na to, że praca zdalna jest wykonywana w ciągu ostatnich kilku tygodni, nie udało nam się wykonać tyle testów weryfikacyjnych ręcznych. Firma Microsoft nie wprowadziła żadnych zmian, które mogą być uszkodzone i wszystkie testy zautomatyzowane zostały zakończone powodzeniem. W przypadku nieodebrania czegoś poinformuj nas o tym w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Bądź w dobrej kondycji

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0:2020-marzec Release
**Nowe funkcje**
- Linux: Dodano obsługę Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 z [instrukcjami](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) dotyczącymi sposobu konfigurowania systemu dla zestawu Speech SDK.
- Linux: Dodano obsługę platformy .NET Core C# w systemie Linux ARM32 i ARM64. Przeczytaj więcej [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux). 
- C#, C++: dodano `UtteranceId` w `ConversationTranscriptionResult` , spójny identyfikator między wszystkimi pośrednimi i końcowymi wynikami rozpoznawania mowy. Szczegóły dla [języków C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet&preserve-view=true)i [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: Dodano obsługę `Language ID` . Zobacz speech_sample. PR w [repozytorium GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- System Windows: Dodano obsługę formatu kompresji danych wejściowych audio na platformie Windows dla wszystkich aplikacji konsolowych Win32. Szczegóły [tutaj](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). 
- JavaScript: obsługa syntezy mowy (zamiany tekstu na mowę) w NodeJS. Dowiedz się więcej [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: Dodaj nowy interfejs API, aby włączyć inspekcję wszystkich wysłanych i odebranych komunikatów. Dowiedz się więcej [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Poprawki błędów**
- C#, C++: Rozwiązano problem, dlatego `SendMessageAsync` wysyła komunikat binarny jako typ binarny. Szczegóły dla [języków C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_)i [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection).
- C#, C++: Rozwiązano problem polegający na tym, że użycie `Connection MessageReceived` zdarzenia może spowodować awarię, jeśli element `Recognizer` zostanie usunięty przed `Connection` obiektem. Szczegóły dla [języków C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet&preserve-view=true)i [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived).
- Android: rozmiar buforu audio z mikrofonu zmniejszył się z 800ms do 100 MS w celu usprawnienia opóźnienia.
- Android: rozwiązano [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) z emulatorem x86 systemu android w Android Studio.
- JavaScript: Dodano obsługę regionów w Chinach przy użyciu `fromSubscription` interfejsu API. Szczegóły [tutaj](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#fromsubscription-string--string-). 
- JavaScript: Dodaj więcej informacji o błędzie dla niepowodzeń połączeń z NodeJS.
        
**Samples**
- Unity: w przypadku niepowodzenia importu LUIS JSON został usunięty publiczny przykład rozpoznawania intencji. Szczegóły [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: dodano przykład dla `Language ID` . Szczegóły [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Covid19:** Ze względu na to, że praca zdalna jest wykonywana w ciągu ostatnich kilku tygodni, nie mogliśmy przeanalizować tak wielu ręcznych testów weryfikacyjnych urządzeń. Na przykład nie możemy przetestować danych wejściowych mikrofonu i danych wyjściowych głośników w systemach Linux, iOS i macOS. Nie wprowadziliśmy żadnych zmian, na których myślimy, że na tych platformach zakończono wszystkie czynności i wszystkie testy zautomatyzowane zostały zakończone powodzeniem. W mało prawdopodobnym zdarzeniu, poinformuj nas o tym w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br> Dziękujemy za dalszą pomoc techniczną. Jak zawsze, Opublikuj pytania lub opinię w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) lub [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Bądź w dobrej kondycji

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0:2020-luty Release

**Nowe funkcje**

 - Dodano pakiety języka Python obsługujące nowe wydanie 3,8 języka Python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 (C++, C#, Java, Python).
   > [!NOTE] 
   > Klienci muszą skonfigurować OpenSSL zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Obsługa ARM32 systemu Linux dla Debian i Ubuntu.
 - DialogServiceConnector obsługuje teraz opcjonalny parametr "bot ID" na BotFrameworkConfig. Ten parametr umożliwia korzystanie z wielu bezpośrednich botów mowy wiersza za pomocą pojedynczego zasobu usługi Azure Speech. Bez określonego parametru zostanie użyta wartość domyślna bot (zgodnie z konfiguracją kanału Direct Speech line).
 - DialogServiceConnector ma teraz Właściwość SpeechActivityTemplate. Zawartość tego ciągu JSON zostanie użyta w bezpośrednim wierszu mowy, aby wstępnie wypełnić szeroką gamę obsługiwanych pól we wszystkich działaniach, które docierają do bezpośredniego bot mowy liniowej, w tym działań automatycznie generowanych w odpowiedzi na zdarzenia, takie jak rozpoznawanie mowy.
 - Funkcja TTS używa teraz klucza subskrypcji do uwierzytelniania, co zmniejsza czas opóźnienia pierwszego bajtu z pierwszego wyniku w przypadku tworzenia syntezatora.
 - Zaktualizowane modele rozpoznawania mowy dla 19 ustawień regionalnych dla średniego współczynnika błędów wyrazu redukcja 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, Ko-KR, pt-BR, zh-CN, zh-HK, NB-NO, Fi-FL, ru-PL, pl-am, "CA-ES", zh-TW, th, pt-PT, TR-TR). Nowe modele zapewniają znaczące ulepszenia w wielu domenach, takich jak Dyktowanie, Call-Center transkrypcje i scenariusze indeksowania wideo.

**Poprawki błędów**

 - Rozwiązano problem polegający na tym, że w interfejsie API języka JAVA nie Transcriber konwersacji. 
 - Poprawka emulatora systemu Android x86 dla rozwiązania Xamarin [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Dodaj brakujące (Pobierz | Ustaw metody właściwości na AudioConfig
 - Naprawianie usterki TTS, w której nie można zatrzymać audioDataStream w przypadku niepowodzenia połączenia
 - Użycie punktu końcowego bez regionu spowoduje wystąpienie błędów USP dla translatora konwersacji
 - Generowanie identyfikatorów w aplikacjach uniwersalnych systemu Windows używa teraz odpowiedniego unikatowego algorytmu identyfikatora GUID; wcześniej i przypadkowo nie została zamierzona domyślna implementacja użyto metod zastępczych, która często wytwarza kolizje w dużych zestawach interakcji.
 
 **Samples**
 
 - Przykład aparatu Unity do korzystania z zestawu Speech SDK z [mikrofonu Unity i przesyłania strumieniowego w trybie push](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Inne zmiany**

 - [Dokumentacja dotycząca konfiguracji OpenSSL dla systemu Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0:2020 — wydanie w styczniu

**Nowe funkcje**

- Konwersacja z wieloma urządzeniami: łączenie wielu urządzeń z tą samą obsługą mowy lub konwersacją tekstową oraz opcjonalne tłumaczenie komunikatów wysyłanych między nimi. Więcej informacji znajduje się w [tym artykule](multi-device-conversation.md). 
- Dodano obsługę rozpoznawania słów kluczowych dla pakietu Android. AAR i dodano obsługę wersji x86 i x64. 
- Zamierzenia-C: `SendMessage` i `SetMessageProperty` metody dodane do `Connection` obiektu. [Tutaj](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)znajdziesz dokumentację.
- Interfejs API języka TTS C++ obsługuje teraz `std::wstring` jako dane wejściowe syntezy tekstu, eliminując konieczność konwersji wstring na ciąg przed przekazaniem go do zestawu SDK. Szczegóły można znaleźć [tutaj](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: dostępne są teraz [identyfikatory języka](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) i [konfiguracji języka źródłowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) .
- JavaScript: dodano funkcję do `Connection` obiektu, aby przekazywać komunikaty niestandardowe z usługi mowy jako wywołanie zwrotne `receivedServiceMessage` .
- JavaScript: Dodano obsługę programu `FromHost API` , aby ułatwić korzystanie z kontenerów Premium i suwerennych chmur. [Tutaj](speech-container-howto.md)znajdziesz dokumentację.
- JavaScript: Firma Microsoft honoruje `NODE_TLS_REJECT_UNAUTHORIZED` się z [orgads](https://github.com/orgads). Szczegóły można znaleźć [tutaj](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Fundamentalne zmiany**

- `OpenSSL` został zaktualizowany do wersji 1.1.1 b i jest statycznie połączony z biblioteką podstawową zestawu Speech SDK dla systemu Linux. Może to spowodować przerwanie, jeśli skrzynka odbiorcza `OpenSSL` nie została zainstalowana w `/usr/lib/ssl` katalogu w systemie. Aby obejść ten problem, zapoznaj się z [naszą dokumentacją](how-to-configure-openssl-linux.md) w obszarze dokumentacja zestawu Speech SDK.
- Zmieniono typ danych zwrócony dla języka C# `WordLevelTimingResult.Offset` z na, `int` `long` Aby zezwolić na dostęp, `WordLevelTimingResults` gdy dane mowy są dłuższe niż 2 minuty.
- `PushAudioInputStream` i `PullAudioInputStream` teraz Wysyłaj informacje z nagłówka WAV do usługi mowy opartej na systemie `AudioStreamFormat` , opcjonalnie określone podczas tworzenia. Klienci muszą teraz korzystać z [obsługiwanego formatu wejścia audio](how-to-use-audio-input-streams.md). Wszystkie inne formaty będą uzyskać optymalne wyniki rozpoznawania lub mogą powodować inne problemy. 

**Poprawki błędów**

- Zapoznaj się z `OpenSSL` aktualizacją w obszarze istotne zmiany powyżej. Rozwiązano zarówno sporadyczną awarię, jak i problem z wydajnością (Zablokuj rywalizację pod dużym obciążeniem) w systemach Linux i Java. 
- Java: wprowadzono ulepszenia dotyczące zamykania obiektów w scenariuszach o wysokim współbieżności.
- Restrukturyzacja naszego pakietu NuGet. Firma Microsoft usunęła trzy kopie `Microsoft.CognitiveServices.Speech.core.dll` i `Microsoft.CognitiveServices.Speech.extension.kws.dll` w folderach lib, dzięki czemu pakiet NuGet jest mniejszy i szybszy do pobrania, a dodaliśmy nagłówki, które są konieczne do kompilowania niektórych natywnych aplikacji C++.
- Poprawiono przykłady [szybkiego](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)startu. Zostały one zakończone bez wyświetlania wyjątku "nie znaleziono mikrofonu" w systemie Linux, macOS, Windows.
- Naprawiono awarię zestawu SDK z długią funkcją rozpoznawania mowy na niektórych ścieżkach kodu, takich jak [ten przykład](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Wystąpił błąd wdrażania zestawu SDK w środowisku aplikacji sieci Web platformy Azure w celu rozwiązania [problemu z klientem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Naprawiono błąd usługi TTS przy użyciu `<voice>` wielotagu lub `<audio>` tagu w celu rozwiązania [problemu z klientem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Naprawiono błąd TTS 401, gdy zestaw SDK zostanie odzyskany z wstrzymania.
- JavaScript: Naprawiono cykliczne Importowanie danych audio za pośrednictwem udziału z [euirim](https://github.com/euirim). 
- JavaScript: Dodano obsługę ustawiania właściwości usługi, jak dodano w 1,7.
- JavaScript: Rozwiązano problem polegający na tym, że błąd połączenia może spowodować ciągłe, nieudane próby ponownego nawiązania połączenia z użyciem protokołu WebSocket.

**Samples**

- Dodano przykład rozpoznawania słów kluczowych dla systemu Android [tutaj](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- W [tym miejscu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)dodano przykład TTS dla scenariusza serwera.
- Dodano wiele przewodników szybki start dla języków C# i C++ w [tym miejscu](quickstarts/multi-device-conversation.md).

**Inne zmiany**

- Rozmiar zoptymalizowanej biblioteki podstawowej zestawu SDK w systemie Android.
- Zestaw SDK w 1.9.0 i lub nowszym obsługuje zarówno `int` `string` typy w polu wersja podpisu głosowego, jak i na potrzeby konwersacji Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019 — wydanie w listopadzie

**Nowe funkcje**

- Dodano `FromHost()` interfejs API, aby ułatwić korzystanie z kontenerów Premium i suwerennych chmur.
- Dodano automatyczne wykrywanie języka źródła dla funkcji rozpoznawania mowy (w językach Java i C++)
- Dodano `SourceLanguageConfig` obiekt dla funkcji rozpoznawania mowy używany do określania oczekiwanych języków źródłowych (w językach Java i C++)
- Dodano `KeywordRecognizer` obsługę systemu Windows (platformy UWP), systemów Android i iOS za pomocą pakietów NuGet i programu Unity
- Dodano interfejs API języka Java konwersacji zdalnych w celu przeprowadzenia transkrypcji konwersacji w partiach asynchronicznych.

**Fundamentalne zmiany**

- Funkcje Transcriber konwersacji przenoszone w obszarze nazw `Microsoft.CognitiveServices.Speech.Transcription` .
- Części metody Transcriber konwersacji są przenoszone do nowej `Conversation` klasy.
- Porzucone wsparcie dla 32-bitowych (architektury ARMv7 i x86) iOS

**Poprawki błędów**

- Popraw w przypadku awarii `KeywordRecognizer` , jeśli jest używana lokalna bez prawidłowego klucza subskrypcji usługi mowy

**Samples**

- Przykład platformy Xamarin dla `KeywordRecognizer`
- Przykład aparatu Unity dla `KeywordRecognizer`
- Przykłady języków C++ i Java dla automatycznych wykrywanie języka źródłowych.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019 — wydanie z września

**Nowe funkcje**

- Dodano obsługę wersji beta platformy Xamarin w systemach platforma uniwersalna systemu Windows (platformy UWP), Android i iOS
- Dodano obsługę systemu iOS dla aparatu Unity
- Dodano `Compressed` obsługę danych wejściowych dla ALAW, MuLaw, FLAC w systemach Android, iOS i Linux
- Dodano `SendMessageAsync` w `Connection` klasie do wysyłania komunikatu do usługi
- Dodano `SetMessageProperty` w `Connection` klasie dla właściwości ustawienia komunikatu
- Usługi TTS dodaliśmy powiązania dla Java (JRE i Android), Python, Swift i celu-C
- Obsługa odtwarzania w systemie macOS, iOS i Android została dodana przez funkcję TTS.
- Dodano informacje "granica wyrazu" dla usługi TTS.

**Poprawki błędów**

- Rozwiązano problem z kompilacją IL2CPP w systemie Unity 2019 dla systemu Android
- Rozwiązano problem z nieprawidłowo utworzonymi nagłówkami w danych wejściowych pliku WAV, które są przetwarzane nieprawidłowo
- Rozwiązano problem z identyfikatorami UUID, które nie są unikatowe we właściwościach połączenia
- Rozwiązano kilka ostrzeżeń dotyczących specyfikatorów wartości null w powiązaniach SWIFT (może wymagać wprowadzenia małych kodu)
- Rozwiązano błąd, który spowodował, że połączenia protokołu WebSocket są niebezpiecznie zamknięte w ramach obciążenia sieci
- Rozwiązano problem w systemie Android, który czasami powoduje wystąpienie zduplikowanych identyfikatorów nakładów używanych przez program `DialogServiceConnector`
- Ulepszenia stabilności połączeń między interakcjami i raportowaniem niepowodzeń (za pośrednictwem `Canceled` zdarzeń) w przypadku wystąpienia `DialogServiceConnector`
- `DialogServiceConnector` rozpoczęcie sesji będzie teraz prawidłowo udostępniać zdarzenia, włącznie z wywoływaniem `ListenOnceAsync()` w trakcie aktywnego `StartKeywordRecognitionAsync()`
- Rozwiązywanie awarii skojarzonej z `DialogServiceConnector` odebranymi działaniami

**Samples**

- Szybki Start dla platformy Xamarin
- Zaktualizowano przewodnik szybkiego startu z systemem Linux ARM64 Information
- Zaktualizowano przewodnik szybkiego startu aparatu Unity przy użyciu informacji systemu iOS

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019 — wydanie z czerwca

**Samples**

- Przykłady szybkiego startu dla zamiany tekstu na mowę w platformy UWP i Unity
- Przykład szybkiego startu dla SWIFT w systemie iOS
- Przykłady aparatu Unity dla rozpoznawanie intencji & mowy i tłumaczenia
- Zaktualizowano przykłady szybkiego startu dla `DialogServiceConnector`

**Ulepszenia/zmiany**

- Przestrzeń nazw okna dialogowego:
  - Zmieniono nazwę polecenia `SpeechBotConnector` na `DialogServiceConnector`
  - Zmieniono nazwę polecenia `BotConfig` na `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` został ponownie zmapowany na `DialogServiceConfig::FromBotSecret()`
  - Wszystkie istniejące bezpośrednie komputery klienckie mowy są nadal obsługiwane po zmianie nazwy
- Aktualizowanie karty REST usługi TTS do obsługi serwera proxy, trwałego połączenia
- Ulepsz komunikat o błędzie po przekazaniu nieprawidłowego regionu
- SWIFT/cel-C:
  - Udoskonalone raportowanie błędów: metody, które mogą spowodować błąd, są teraz obecne w dwóch wersjach: takie, które uwidacznia `NSError` obiekt dla obsługi błędów, i jeden, który wywołuje wyjątek. Dawniej są dostępne dla Swift. Ta zmiana wymaga adaptacji do istniejącego kodu SWIFT.
  - Ulepszona obsługa zdarzeń

**Poprawki błędów**

- Poprawka dla usługi TTS: w przypadku, gdy w `SpeakTextAsync` przyszłości zostanie zwrócony bez oczekiwania na ukończenie renderowania dźwięku
- Poprawka dotycząca organizowania ciągów w języku C# w celu włączenia obsługi pełnego języka
- Naprawa problemu z aplikacją .NET Core w celu załadowania biblioteki podstawowej z platformą docelową net461 w przykładach
- Naprawa sporadycznych problemów związanych z wdrażaniem bibliotek natywnych w folderze wyjściowym w przykładach
- Naprawa niezawodnego zamykania gniazda internetowego
- Naprawianie możliwej awarii podczas otwierania połączenia z dużym obciążeniem w systemie Linux
- Naprawianie brakujących metadanych w pakiecie struktury dla macOS
- Rozwiązywanie problemów z `pip install --user` systemem Windows

## <a name="speech-sdk-151"></a>Zestaw Speech SDK 1.5.1

Jest to poprawka usterek i ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję języka JavaScript zestawu SDK.

**Poprawki błędów**

- Należy rozwiązać FromSubscription, gdy jest używany z transkrypcją konwersacji.
- Usuń usterkę słowa kluczowego wykrywania trendów dla asystentów głosowych.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019 — może wydać

**Nowe funkcje**

- Wykrywania trendów słowa kluczowego (KWS) jest teraz dostępny dla systemów Windows i Linux. Funkcja KWS może działać z dowolnym typem mikrofonu, oficjalną obsługą KWS, ale jest obecnie ograniczona do tablic mikrofonów znajdujących się na sprzęcie platformy Azure urządzenia Kinect DK lub zestawu Speech Devices SDK.
- Funkcja podpowiedzi frazy jest dostępna za pomocą zestawu SDK. Aby uzyskać więcej informacji, zobacz [tutaj](how-to-phrase-lists.md).
- Funkcja transkrypcji konwersacji jest dostępna za pomocą zestawu SDK. Zobacz [tutaj](conversation-transcription-service.md).
- Dodawanie obsługi asystentów głosowych przy użyciu kanału bezpośredniej linii mowy.

**Samples**

- Dodano przykłady dla nowych funkcji lub nowych usług obsługiwanych przez zestaw SDK.

**Ulepszenia/zmiany**

- Dodano różne właściwości aparatu rozpoznawania w celu dostosowania zachowania usługi lub wyników usług (takich jak maskowanie wulgarności i innych).
- Teraz można skonfigurować aparat rozpoznawania przy użyciu standardowych właściwości konfiguracji, nawet jeśli aparat rozpoznawania został utworzony `FromEndpoint` .
- Cel-C: `OutputFormat` Właściwość została dodana do `SPXSpeechConfiguration` .
- Zestaw SDK obsługuje teraz Debian 9 jako dystrybucję systemu Linux.

**Poprawki błędów**

- Rozwiązano problem polegający na tym, że zasób osoby mówiącej był zbyt wczesny w zamian tekstu na mowę.

## <a name="speech-sdk-142"></a>Zestaw mowy SDK 1.4.2

Jest to poprawka usterek i ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję języka JavaScript zestawu SDK.

## <a name="speech-sdk-141"></a>Zestaw Speech SDK 1.4.1

Jest to wersja tylko w języku JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

- Zapobiegaj ładowaniu protokołu HTTPS-proxy-Agent przez pakiet internetowy.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0:2019 — wydanie z kwietnia

**Nowe funkcje**

- Zestaw SDK obsługuje teraz usługę zamiany tekstu na mowę jako wersję beta. Jest ona obsługiwana na komputerach z systemami Windows i Linux w językach C++ i C#. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem zamiany tekstu na mowę](text-to-speech.md#get-started).
- Zestaw SDK obsługuje teraz pliki audio MP3 i Opus/OGG jako pliki wejściowe strumienia. Ta funkcja jest dostępna tylko w systemie Linux w językach C++ i C# i jest obecnie w wersji beta (więcej informacji znajduje się [tutaj](how-to-use-codec-compressed-audio-input-streams.md)).
- Zestaw mowy SDK dla języka Java, .NET Core, C++ i dążyć-C uzyskał pomoc techniczną macOS. Obsługa języka C dla macOS jest obecnie w wersji beta.
- iOS: zestaw Speech SDK dla systemu iOS (cel-C) jest teraz również publikowany jako CocoaPod.
- JavaScript: Obsługa mikrofonu innego niż domyślny jako urządzenia wejściowego.
- JavaScript: Obsługa serwera proxy dla Node.js.

**Samples**

- Przykłady użycia zestawu Speech SDK z językiem C++ i z celem macOS zostały dodane.
- Przykłady pokazujące użycie usługi zamiany tekstu na mowę zostały dodane.

**Ulepszenia/zmiany**

- Python: dodatkowe właściwości wyników rozpoznawania są teraz udostępniane za pośrednictwem `properties` właściwości.
- Aby uzyskać dodatkową pomoc w zakresie programowania i debugowania, możesz przekierować informacje dotyczące rejestrowania i diagnostyki zestawu SDK do pliku dziennika (więcej informacji znajdziesz [tutaj](how-to-use-logging.md)).
- JavaScript: Poprawianie wydajności przetwarzania dźwięku.

**Poprawki błędów**

- Mac/iOS: usterka, która doprowadziła do długiego oczekiwania, gdy nie można nawiązać połączenia z usługą mowy została naprawiona.
- Python: poprawianie obsługi błędów dla argumentów w wywołaniach zwrotnych języka Python.
- JavaScript: Naprawiono nieprawidłowe raportowanie stanu dla mowy zakończono w RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019 – luty Refresh

Jest to poprawka usterek i ma wpływ tylko na natywny/zarządzany zestaw SDK. Nie ma to wpływu na wersję języka JavaScript zestawu SDK.

**Poprawka błędu**

- Naprawiono przeciek pamięci podczas korzystania z mikrofonu. Nie ma to żadnego oddziaływania na dane wejściowe lub oparte na strumieniu.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0:2019-luty Release

**Nowe funkcje**

- Zestaw Speech SDK obsługuje wybór mikrofonu wejściowego za pomocą `AudioConfig` klasy. Pozwala to na przesyłanie strumieniowe danych audio do usługi mowy z mikrofonu innego niż domyślny. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [wyboru urządzenia wejściowego audio](how-to-select-audio-input-devices.md). Ta funkcja nie jest jeszcze dostępna w języku JavaScript.
- Zestaw Speech SDK obsługuje teraz środowisko Unity w wersji beta. Prześlij opinię w sekcji problemu w [repozytorium przykładowym usługi GitHub](https://aka.ms/csspeech/samples). Ta wersja obsługuje środowisko Unity w systemie Windows x86 i x64 (aplikacje klasyczne lub platforma uniwersalna systemu Windows) oraz Android (ARM32/64, x86). Więcej informacji można znaleźć w naszym [przewodniku szybki start dla aparatu Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- Plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dostarczany w poprzednich wersjach) nie jest już wymagany. Ta funkcja jest teraz zintegrowana z podstawowym zestawem SDK.

**Samples**

W naszym [przykładowym repozytorium](https://aka.ms/csspeech/samples)jest dostępna następująca Nowa zawartość:

- Dodatkowe przykłady dla `AudioConfig.FromMicrophoneInput` .
- Dodatkowe przykłady w języku Python dotyczące rozpoznawania i tłumaczenia zamierzeń.
- Dodatkowe przykłady dotyczące korzystania z `Connection` obiektu w systemie iOS.
- Dodatkowe przykłady języka Java do tłumaczenia z danymi wyjściowymi audio.
- Nowy przykład do korzystania z [interfejsu API REST transkrypcji usługi Batch](batch-transcription.md).

**Ulepszenia/zmiany**

- Python
  - Ulepszono weryfikację parametrów i komunikaty o błędach w `SpeechConfig` .
  - Dodaj obsługę `Connection` obiektu.
  - Obsługa 32-bitowego języka Python (x86) w systemie Windows.
  - Zestaw Speech SDK dla języka Python jest poza wersją beta.
- iOS
  - Zestaw SDK jest teraz oparty na zestawie SDK systemu iOS w wersji 12,1.
  - Zestaw SDK obsługuje teraz system iOS w wersji 9,2 lub nowszej.
  - Popraw dokumentację referencyjną i popraw kilka nazw właściwości.
- JavaScript
  - Dodaj obsługę `Connection` obiektu.
  - Dodaj pliki definicji typów dla powiązanego języka JavaScript
  - Wstępna pomoc techniczna i implementacja dla wskazówek dotyczących fraz.
  - Zwracanie kolekcji właściwości przy użyciu notacji JSON usługi dla rozpoznawania
- Biblioteki DLL systemu Windows zawierają teraz zasób wersji.
- Jeśli tworzysz aparat rozpoznawania `FromEndpoint` , możesz dodać parametry bezpośrednio do adresu URL punktu końcowego. `FromEndpoint`Nie można skonfigurować aparatu rozpoznawania przy użyciu standardowych właściwości konfiguracji.

**Poprawki błędów**

- Pusta nazwa użytkownika serwera proxy i hasło serwera proxy nie zostały poprawnie obsłużone. W tej wersji, jeśli ustawisz nazwę użytkownika serwera proxy i hasło serwera proxy na pusty ciąg, nie zostaną one przesłane podczas nawiązywania połączenia z serwerem proxy.
- Identyfikator sesji utworzony przez zestaw SDK nie zawsze jest losowo obsługiwany w niektórych językach &nbsp; /środowiskach. Dodano losową inicjalizację generatora, aby rozwiązać ten problem.
- Poprawa obsługi tokenu autoryzacji. Jeśli chcesz użyć tokenu autoryzacji, określ wartość w `SpeechConfig` i pozostaw klucz subskrypcji pusty. Następnie utwórz aparat rozpoznawania w zwykły sposób.
- W niektórych przypadkach `Connection` obiekt nie został prawidłowo opublikowany. Ten problem został rozwiązany.
- Przykład JavaScript został rozwiązany do obsługi danych wyjściowych audio na potrzeby syntezy tłumaczenia również w przeglądarce Safari.

## <a name="speech-sdk-121"></a>Zestaw Speech SDK 1.2.1

Jest to wersja tylko w języku JavaScript. Nie dodano żadnych funkcji. Wprowadzono następujące poprawki:

- Wygeneruj strumieniowo koniec strumienia. end, a nie na mowę. end.
- Usuń usterkę w pompie audio, która nie zaplanował następnego wysłania, jeśli bieżące wysyłanie nie powiodło się.
- Naprawianie ciągłego rozpoznawania przy użyciu tokenu uwierzytelniania.
- Poprawka usterki dla różnych aparatów rozpoznawania/punktów końcowych.
- Udoskonalenia dokumentacji.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018 — wydanie z grudnia

**Nowe funkcje**

- Python
  - Wersja beta obsługi języka Python (3,5 i nowszych) jest dostępna w tej wersji. Aby uzyskać więcej informacji, zobacz tutaj] (Szybki Start-python.md).
- JavaScript
  - Zestaw Speech SDK dla języka JavaScript został otwarty jako źródło. Kod źródłowy jest dostępny w serwisie [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Teraz obsługujemy Node.js. więcej informacji można znaleźć [tutaj](quickstart-js-node.md).
  - Ograniczenie długości sesji audio zostało usunięte, ponowne połączenie zostanie wykonane automatycznie pod pokryciem.
- `Connection` Stream
  - W programie można `Recognizer` uzyskać dostęp do `Connection` obiektu. Ten obiekt umożliwia jawne zainicjowanie połączenia z usługą i subskrypcję w celu nawiązania połączenia i rozłączenia zdarzeń.
    (Ta funkcja nie jest jeszcze dostępna w języku JavaScript i Python).
- Obsługa Ubuntu 18,04.
- Android
  - Włączono obsługę funkcji preguard podczas generowania APK.

**Poprawek**

- Udoskonalenia wewnętrznego użycia wątku, Zmniejszanie liczby wątków, blokad i muteksów.
- Ulepszone raportowanie błędów/informacje. W kilku przypadkach komunikaty o błędach nie zostały jeszcze rozpropagowane.
- Zaktualizowano zależności programistyczne w języku JavaScript w celu korzystania z aktualnych modułów.

**Poprawki błędów**

- Naprawiono przecieki pamięci ze względu na niezgodność typów w `RecognizeAsync` .
- W niektórych przypadkach wyjątki były wyciekami.
- Naprawianie przecieku pamięci w argumentach zdarzeń tłumaczenia.
- Rozwiązano problem z blokowaniem po ponownym nawiązaniu połączenia w długotrwałych sesjach.
- Rozwiązano problem, który może prowadzić do braku końcowego wyniku dla nieudanych tłumaczeń.
- C#: Jeśli `async` operacja nie została oczekiwana w wątku głównym, możliwe, że aparat rozpoznawania mógł zostać usunięty przed ukończeniem zadania asynchronicznego.
- Java: Rozwiązano problem powodujący awarię maszyny wirtualnej Java.
- Cel-C: stałe mapowanie wyliczenia; RecognizedIntent został zwrócony zamiast `RecognizingIntent` .
- JavaScript: Ustaw domyślny format wyjściowy na "Simple" w `SpeechConfig` .
- JavaScript: usuwanie niespójności między właściwościami obiektu konfiguracji w języku JavaScript i innych językach.

**Samples**

- Zaktualizowano i Naprawiono kilka próbek (na przykład głosy wyjściowe dla tłumaczenia itp.).
- Dodano przykłady Node.js w [przykładowym repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Zestaw Speech SDK 1.1.0

**Nowe funkcje**

- Obsługa systemu Android x86/x64.
- Obsługa serwera proxy: w `SpeechConfig` obiekcie można teraz wywołać funkcję, aby ustawić informacje o serwerze proxy (nazwa hosta, port, nazwa użytkownika i hasło). Ta funkcja nie jest jeszcze dostępna w systemie iOS.
- Ulepszono kod i komunikaty o błędach. Jeśli rozpoznawanie zwróciło błąd, ten element został już ustawiony `Reason` (w zdarzeniu anulowanym) lub `CancellationDetails` (w wyniku rozpoznawania) do `Error` . Zdarzenie anulowane zawiera teraz dwa dodatkowe elementy członkowskie `ErrorCode` i `ErrorDetails` . Jeśli serwer zwrócił dodatkowe informacje o błędzie z zgłoszonym błędem, będzie on teraz dostępny w nowych elementach członkowskich.

**Poprawek**

- Dodano dodatkową weryfikację w konfiguracji aparatu rozpoznawania i dodano dodatkowy komunikat o błędzie.
- Ulepszona obsługa cisza w czasie długim w środku pliku dźwiękowego.
- Pakiet NuGet: projekty .NET Framework uniemożliwiają Kompilowanie z konfiguracją AnyCPU.

**Poprawki błędów**

- Rozwiązano kilka wyjątków znalezionych w aparatach rozpoznawania. Ponadto wyjątki są przechwytywane i konwertowane na `Canceled` zdarzenia.
- Naprawianie przecieku pamięci w zarządzaniu właściwościami.
- Rozwiązano problem polegający na tym, że plik wejściowy audio może ulec awarii.
- Naprawiono usterkę, w której można odbierać zdarzenia po zdarzeniu zatrzymania sesji.
- Naprawiono niektóre sytuacje wyścigu w wątkach.
- Rozwiązano problem ze zgodnością systemu iOS, który może spowodować awarię.
- Ulepszenia stabilności dla obsługi mikrofonu w systemie Android.
- Rozwiązano problem polegający na tym, że aparat rozpoznawania w języku JavaScript zignoruje język rozpoznawania.
- Naprawiono usterkę uniemożliwiającą Ustawianie `EndpointId` (w niektórych przypadkach) w języku JavaScript.
- Zmieniono kolejność parametrów w parametrze zamierzenia w języku JavaScript i dodano brakującą `AddIntent` sygnaturę języka JavaScript.

**Samples**

- Dodano przykłady języków C++ i C# na potrzeby użycia ściągania i strumienia wypychania w [przykładowym repozytorium](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Zestaw Speech SDK 1.0.1

Ulepszenia niezawodności i poprawki błędów:

- Naprawiono potencjalny błąd krytyczny spowodowany sytuacją wyścigu podczas usuwania aparatu rozpoznawania
- Naprawiono potencjalny błąd krytyczny podczas wycofania właściwości.
- Dodano dodatkowy błąd i sprawdzanie parametrów.
- Zamierzenie-C: stały możliwy błąd krytyczny spowodowany przez zastąpienie nazwy w NSString.
- Cel-C: dostosowany widoczność interfejsu API
- JavaScript: stałe dotyczące zdarzeń i ich ładunków.
- Udoskonalenia dokumentacji.

W naszym [przykładowym repozytorium](https://aka.ms/csspeech/samples)dodano nowy przykład dla języka JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0:2018-wrzesień Release

**Nowe funkcje**

- Obsługa celu języka C w systemie iOS. Zapoznaj się z naszym [przewodnikiem Szybki Start dla systemu iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Obsługa języka JavaScript w przeglądarce. Zapoznaj się z naszym [przewodnikiem Szybki Start dla języka JavaScript](quickstart-js-browser.md).

**Fundamentalne zmiany**

- W tej wersji wprowadzono kilka istotnych zmian.
  Sprawdź [Tę stronę](https://aka.ms/csspeech/breakingchanges_1_0_0) , aby uzyskać szczegółowe informacje.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0:2018 — wydanie z sierpnia

**Nowe funkcje**

- Aplikacje platformy UWP utworzone za pomocą zestawu Speech SDK mogą teraz przejść do zestawu certyfikacji aplikacji systemu Windows (WACK).
  Zapoznaj się z [przewodnikiem Szybki Start platformy UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Obsługa .NET Standard 2,0 w systemie Linux (Ubuntu 16,04 x64).
- Eksperymentalne: obsługa języka Java 8 w systemie Windows (64-bitowy) i Linux (Ubuntu 16,04 x64).
  Zapoznaj się z [Java Runtime Environment przewodnika Szybki Start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Zmiana funkcjonalna**

- Uwidacznianie dodatkowych szczegółowych informacji o błędach dotyczących błędów połączenia.

**Fundamentalne zmiany**

- W systemie Java (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` Funkcja nie wymaga już parametru Path. Teraz ścieżka jest automatycznie wykrywana na wszystkich obsługiwanych platformach.
- Metoda Get-accessor właściwości `EndpointUrl` w językach Java i C# została usunięta.

**Poprawki błędów**

- W języku Java wynik syntezy audio dla aparatu rozpoznawania tłumaczenia jest teraz zaimplementowany.
- Rozwiązano błąd, który może spowodować nieaktywne wątki i większą liczbę otwartych i nieużywanych gniazd.
- Rozwiązano problem, w którym długotrwałe rozpoznawanie może zakończyć się w trakcie transmisji.
- Naprawiono sytuację wyścigu w zamknięciu aparatu rozpoznawania.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0:2018 – wydanie

**Nowe funkcje**

- Obsługa platformy Android (API 23: Android 6,0 Marshmallow lub nowszy). Zapoznaj się z [przewodnikiem Szybki Start dla systemu Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Obsługa .NET Standard 2,0 w systemie Windows. Zapoznaj się z [przewodnikiem Szybki Start dla platformy .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Eksperymentalne: obsługa platformy UWP w systemie Windows (wersja 1709 lub nowsza).
  - Zapoznaj się z [przewodnikiem Szybki Start platformy UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Uwaga: aplikacje platformy UWP utworzone przy użyciu zestawu Speech SDK nie przechodzą jeszcze zestawu certyfikacji aplikacji systemu Windows (WACK).
- Obsługa długotrwałego rozpoznawania z automatycznym ponownem połączeniem.

**Zmiany funkcjonalne**

- `StartContinuousRecognitionAsync()` obsługuje długotrwałe rozpoznawanie.
- Wynik rozpoznawania zawiera więcej pól. Są one przesunięte od początku i czasu trwania (w taktach) rozpoznanego tekstu oraz wartości dodatkowych reprezentujących stan rozpoznawania, na przykład `InitialSilenceTimeout` i `InitialBabbleTimeout` .
- Obsługa AuthorizationToken do tworzenia wystąpień fabryki.

**Fundamentalne zmiany**

- Zdarzenia rozpoznawania: `NoMatch` Typ zdarzenia został scalony w `Error` zdarzeniu.
- Nazwa SpeechOutputFormat w języku C# została zmieniona na `OutputFormat` , aby zachować wyrównanie do języka C++.
- Nieznacznie zmieniony typ zwracany niektórych metod `AudioInputStream` interfejsu:
  - W języku Java `read` Metoda teraz zwraca wartość `long` zamiast `int` .
  - W języku C# `Read` Metoda teraz zwraca `uint` zamiast `int` .
  - W języku C++ `Read` metody i `GetFormat` teraz zwracają `size_t` zamiast `int` .
- C++: wystąpienia strumieni wejścia audio mogą teraz być przesyłane tylko jako `shared_ptr` .

**Poprawki błędów**

- Naprawiono nieprawidłowe wartości zwracane w wyniku, gdy zostanie `RecognizeAsync()` przeprowadzony limit czasu.
- Zależność od bibliotek programu Media Foundation w systemie Windows została usunięta. Zestaw SDK używa teraz podstawowych interfejsów API audio.
- Poprawka do dokumentacji: Dodano stronę [regiony](regions.md) , aby opisać Obsługiwane regiony.

**Znany problem**

- Zestaw Speech SDK dla systemu Android nie raportuje wyników syntezy mowy na potrzeby tłumaczenia. Ten problem zostanie rozwiązany w następnej wersji.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0:2018-czerwiec Release

**Zmiany funkcjonalne**

- AudioInputStream

  Aparat rozpoznawania teraz może zużywać strumień jako źródło audio. Aby uzyskać więcej informacji, zobacz temat dotyczący [przewodnika](how-to-use-audio-input-streams.md).

- Szczegółowy format danych wyjściowych

  Podczas tworzenia, można `SpeechRecognizer` żądać `Detailed` lub `Simple` format wyjściowy. `DetailedSpeechRecognitionResult`Zawiera ocenę pewności, rozpoznany tekst, nieprzetworzoną postać leksykalną, znormalizowaną postać i znormalizowaną postać z maską bez obaw.

**Zmiana podziału**

- Zmieniono na `SpeechRecognitionResult.Text` z `SpeechRecognitionResult.RecognizedText` w języku C#.

**Poprawki błędów**

- Podczas zamykania Naprawiono możliwy problem z wywołaniem zwrotnym w warstwie USP.
- Jeśli aparat rozpoznawania zużywa plik wejściowy audio, zatrzymał się w obsłudze plików dłużej niż to konieczne.
- Usunięto kilka zakleszczenii między pompą komunikatów i aparatem rozpoznawania.
- Wygeneruj `NoMatch` wynik w przypadku przekroczenia limitu czasu odpowiedzi z usługi.
- Biblioteki programu Media Foundation w systemie Windows są ładowane z opóźnieniem. Ta biblioteka jest wymagana tylko w przypadku danych wejściowych mikrofonu.
- Szybkość przekazywania danych audio jest ograniczona do około dwa razy większa niż oryginalna szybkość audio.
- W systemie Windows zestawy C# .NET są teraz silne nazwami.
- Poprawka do dokumentacji: `Region` informacje wymagane do utworzenia aparatu rozpoznawania.

Więcej przykładów zostało dodanych i są stale aktualizowane. Aby zapoznać się z najnowszym zestawem przykładów, zobacz [repozytorium usługi Speech SDK przykłady](https://aka.ms/csspeech/samples)w witrynie GitHub.

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733:2018 — może wydać

Ta wersja stanowi pierwszą publiczną wersję zapoznawczą zestawu SDK mowy Cognitive Services.
