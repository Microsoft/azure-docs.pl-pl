---
title: Informacje o SDK mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Zestaw SDK (Speech Software Development Kit) udostępnia wiele funkcji usługi mowy, ułatwiając tworzenie aplikacji obsługujących mowę.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f663c9af8c352d0170c633fe76b3fbc5268aad1e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399853"
---
# <a name="about-the-speech-sdk"></a>Informacje o zestawie SDK usługi Mowa

Zestaw SDK (Speech Software Development Kit) udostępnia wiele funkcji usługi mowy, aby umożliwić tworzenie aplikacji obsługujących mowę. Pakiet SDK mowy jest dostępny w wielu językach programowania i na wszystkich platformach.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Możliwości scenariusza

SDK mowy udostępnia wiele funkcji z usługi mowy, ale nie wszystkie z nich. Możliwości SDK mowy są często skojarzone ze scenariuszami. Zestaw SDK mowy jest idealny zarówno dla scenariuszy w czasie rzeczywistym, jak i innych niż w czasie rzeczywistym, przy użyciu urządzeń lokalnych, plików, magazynu obiektów blob platformy Azure, a nawet strumieni wejściowych i wyjściowych. Jeśli scenariusz nie jest osiągalny z SDK mowy, poszukaj alternatywy interfejsu API REST.

### <a name="speech-to-text"></a>Zamiana mowy na tekst

[Zamiana mowy na tekst](speech-to-text.md) (nazywana również *rozpoznawaniem mowy)* transkrybuje strumienie audio na tekst, który mogą wykorzystywać lub wyświetlać aplikacje, narzędzia lub urządzenia. Użyj zamiany mowy na tekst z [funkcją rozumienia języka (LUIS),](../luis/index.yml) aby wyprowadzić intencje użytkownika z transkrybowanego mowę i działać na poleceniach głosowych. Tłumaczenie [mowy](speech-translation.md) umożliwia tłumaczenie mowy na inny język za pomocą jednego połączenia. Aby uzyskać więcej informacji, zobacz [Podstawy zamiany mowy na tekst](speech-to-text-basics.md).

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

[Zamiana tekstu na mowę](text-to-speech.md) (znana również jako *synteza mowy)* przekształca tekst w syntetyzowaną mowę podobną do człowieka. Tekst wejściowy jest literałami ciągu lub przy użyciu [języka znaczników syntezy mowy (SSML).](speech-synthesis-markup.md) Aby uzyskać więcej informacji na temat standardowych lub nerwowych głosów, zobacz [Język zamiany tekstu na mowę i obsługa głosu](language-support.md#text-to-speech).

### <a name="voice-assistants"></a>Asystenci głosowi

Asystenci głosowi korzystający z narzędzia Speech SDK umożliwiają deweloperom tworzenie naturalnych interfejsów konwersacyjnych podobnych do człowieka dla ich aplikacji i środowisk. Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a asystentem. Implementacja używa kanału Direct Line Speech programu Bot Framework lub zintegrowanej usługi polecenia niestandardowe (wersja zapoznawcza) do wykonania zadania. Ponadto asystentów głosowych można tworzyć za pomocą [niestandardowego portalu głosowego,](https://aka.ms/customvoice) aby stworzyć unikalne środowisko głosowe.

#### <a name="keyword-spotting"></a>Wykrywanie słów kluczowych

Pojęcie [wykrywania słów kluczowych](speech-devices-sdk-create-kws.md) jest obsługiwane w SDK mowy. Wykrywanie słów kluczowych jest aktem identyfikacji słowa kluczowego w mowie, a następnie działaniem po usłyszeniu słowa kluczowego. Na przykład "Hey Cortana" aktywuje asystenta Cortany.

### <a name="meeting-scenarios"></a>Scenariusze spotkań

SDK mowy jest idealny do transkrypcji scenariuszy spotkania, czy z jednego urządzenia lub konwersacji wielu urządzeń.

#### <a name="conversation-transcription"></a>Transkrypcja konwersacji

[Transkrypcja konwersacji](conversation-transcription.md) umożliwia rozpoznawanie mowy w czasie rzeczywistym (i asynchroniczne), identyfikację prelegenta i przypisanie zdania każdemu mówcy (znane również jako *diarization).* Idealnie nadaje się do transkrypcji spotkań osobiście z możliwością wyróżniania mówców.

#### <a name="multi-device-conversation"></a>Konwersacja na wielu urządzeniach

Dzięki [konwersacji na wielu urządzeniach](multi-device-conversation.md)połącz wiele urządzeń lub klientów w konwersacji, aby wysyłać wiadomości oparte na mowy lub wiadomości tekstowe, z łatwą obsługą transkrypcji i tłumaczenia.

### <a name="custom--agent-scenarios"></a>Scenariusze niestandardowe/agent

Zestaw SDK mowy może służyć do transkrypcji scenariuszy centrum obsługi, w których generowane są dane telefoniczne.

#### <a name="call-center-transcription"></a>Przekształcanie nagrań w centrach telefonicznej obsługi klienta

[Transkrypcja centrum obsługi jest](call-center-transcription.md) typowym scenariuszem dla zamiany mowy na tekst do transkrypcji dużych ilości danych telefonii, które mogą pochodzić z różnych systemów, takich jak Interactive Voice Response (IVR). Najnowsze modele rozpoznawania mowy z usługi mowy excel do transkrypcji tych danych telefonii, nawet w przypadkach, gdy dane są trudne do zrozumienia dla człowieka.

### <a name="codec-compressed-audio-input"></a>Skompresowane wejście audio kodeka

Kilka języków programowania SDK mowy obsługuje kodek skompresowane strumienie wejściowe audio. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">używanie <span class="docon docon-navigate-external x-hidden-focus"> </span>formatów skompresowanych wejść audio </a>.

## <a name="rest-api"></a>Interfejs API REST

Podczas gdy SDK mowy obejmuje wiele funkcji funkcji usługi mowy, w niektórych scenariuszach można użyć interfejsu API REST.

### <a name="batch-transcription"></a>Transkrypcja wsadowa

[Transkrypcja wsadowa](batch-transcription.md) umożliwia asynchronizowe transkrypcji mowy do tekstu dużych ilości danych. Transkrypcja partii jest możliwa tylko z interfejsu API REST. Oprócz konwersji dźwięku mowy na tekst, wsadowa zamiana mowy na tekst umożliwia również diarization i analizy tonacji.

## <a name="customization"></a>Dostosowywanie

Usługa mowy zapewnia doskonałą funkcjonalność dzięki domyślnym modelom między zamianą mowy na tekst, zamianą tekstu na mowę i translacji mowy. Czasami możesz chcieć zwiększyć wydajność linii bazowej, aby pracować jeszcze lepiej z unikatowym przypadkiem użycia. Usługa mowy ma wiele narzędzi do dostosowywania bez kodu, które ułatwiają i umożliwiają tworzenie przewagi konkurencyjnej dzięki modelom niestandardowym opartym na własnych danych. Te modele będą dostępne tylko dla Ciebie i Twojej organizacji.

### <a name="custom-speech-to-text"></a>Niestandardowa zamiana mowy na tekst

Korzystając z zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, można tworzyć i szkolić niestandardowe modele akustyczne, językowe i wymowy w celu rozwiązania problemu otoczenia lub słownictwa specyficznego dla branży. Tworzenie i zarządzanie modelami mowy niestandardowej bez kodu jest dostępne za pośrednictwem [niestandardowego portalu mowy](https://aka.ms/customspeech). Po opublikowaniu modelu mowy niestandardowej może być on zużywany przez sdk mowy.

### <a name="custom-text-to-speech"></a>Niestandardowy tekst na mowę

Niestandardowy tekst na mowę, znany również jako Niestandardowy głos to zestaw narzędzi online, które umożliwiają tworzenie rozpoznawalnego, jedynego w swoim rodzaju głosu dla twojej marki. Tworzenie i zarządzanie modelami niestandardowego głosu bez kodu jest dostępne za pośrednictwem [niestandardowego portalu głosowego.](https://aka.ms/customvoice) Po opublikowaniu modelu niestandardowego głosu może on zostać wykorzystany przez sdk mowy.

## <a name="get-the-speech-sdk"></a>Pobierz sdk mowy

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Przeglądarka](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Zobacz, jak rozpoznać mowę w języku C #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
