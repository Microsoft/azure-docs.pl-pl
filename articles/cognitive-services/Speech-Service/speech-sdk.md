---
title: Informacje o zestawie SDK mowy — Speech Service
titleSuffix: Azure Cognitive Services
description: Zestaw Speech Software Development Kit (SDK) ujawnia wiele możliwości usługi mowy, ułatwiając opracowywanie aplikacji obsługujących mowę.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 4c8ee3a65c26d1eb3acb7b9f4c18f11e870e4b82
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434522"
---
# <a name="about-the-speech-sdk"></a>Informacje o zestawie SDK usługi Mowa

Zestaw Speech Software Development Kit (SDK) ujawnia wiele możliwości usługi Speech Service, które umożliwiają tworzenie aplikacji obsługujących mowę. Zestaw Speech SDK jest dostępny w wielu językach programowania i na wszystkich platformach.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Możliwości scenariusza

Zestaw Speech SDK uwidacznia wiele funkcji usługi Speech, ale nie wszystkie z nich. Możliwości zestawu Speech SDK są często powiązane ze scenariuszami. Zestaw Speech SDK doskonale nadaje się do scenariuszy w czasie rzeczywistym i w czasie rzeczywistym, przy użyciu urządzeń lokalnych, plików, magazynu obiektów blob platformy Azure, a nawet strumieni danych wejściowych i wyjściowych. Gdy scenariusz nie jest osiągalny za pomocą zestawu Speech SDK, poszukaj alternatywy dla interfejsu API REST.

### <a name="speech-to-text"></a>Zamiana mowy na tekst

Funkcja [zamiany mowy na tekst](speech-to-text.md) (znana także jako *rozpoznawanie mowy*) przekształca strumienie audio do tekstu, który mogą być używane przez aplikacje, narzędzia lub urządzenia. Używaj zamiany mowy na tekst za pomocą [Language Understanding (Luis)](../luis/index.yml) , aby uzyskać intencje użytkownika z uzyskanego mowy i korzystać z poleceń głosowych. Użyj [tłumaczenia mowy](speech-translation.md) , aby przetłumaczyć dane wejściowe mowy na inny język przy użyciu jednego wywołania. Aby uzyskać więcej informacji, zobacz podstawowe informacje o [zamiany mowy na tekst](./get-started-speech-to-text.md).

Funkcja **rozpoznawania mowy (SR), lista fraz, zamierzenia, tłumaczenia i kontenery lokalne** są dostępne na następujących platformach:

  - C++/Windows & Linux & macOS
  - C# (Framework & .NET Core)/Windows & platformy UWP & Unity & Xamarin & Linux & macOS
  - Java (JRE i Android)
  - JavaScript (przeglądarce i NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Go (tylko SR)

### <a name="text-to-speech"></a>Zamiana tekstu na mowę

Zamiana [tekstu na mowę](text-to-speech.md) (znana także jako *synteza mowy*) polega na konwertowaniu tekstu na mowę, podobnie jak w przypadku wypowiedzi. Tekst wejściowy to literały ciągu lub użycie [języka SSML (Speech syntezing Language)](speech-synthesis-markup.md). Aby uzyskać więcej informacji o odniesieniu do standardowych lub neuronowych głosów, zobacz [język zamiany tekstu na mowę i obsługę głosu](language-support.md#text-to-speech).

Funkcja **zamiany tekstu na mowę (TTS)** jest dostępna na następujących platformach:

  - C++/Windows & Linux
  - C#/Windows & platformy UWP & Unity
  - Java (JRE i Android)
  - Python
  - Swift
  - Objective-C
  - Interfejs API REST usługi TTS może być używany w każdej innej sytuacji.

### <a name="voice-assistants"></a>Asystenci głosowi

[Asystenci głosu](voice-assistants.md) korzystający z zestawu Speech SDK umożliwiają tworzenie naturalnych, przypominających konwersacje interfejsów dla aplikacji i środowisk. Zestaw Speech SDK zapewnia szybką i niezawodną interakcję, która zawiera dane dotyczące zamiany mowy na tekst, zamiany tekstu na mowę i konwersacji przy użyciu jednego połączenia. Twoja implementacja może korzystać z kanału mowy linii Direct line lub zintegrowanej usługi poleceń niestandardowych do ukończenia zadania. Ponadto Asystenci głosu mogą używać niestandardowych głosów utworzonych w [niestandardowym portalu głosu](https://aka.ms/customvoice) , aby dodać unikatowe środowisko danych wyjściowych.

Obsługa **asystenta głosowego** jest dostępna na następujących platformach:

  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech Devices SDK)
  - Go

#### <a name="keyword-spotting"></a>Wykrywania trendów słowa kluczowego

Koncepcja [wykrywania trendów słowa kluczowego](./custom-keyword-basics.md) jest obsługiwana w zestawie mowy SDK. Wykrywania trendów słowa kluczowego to czynność identyfikująca słowo kluczowe w zamiany, a następnie akcję po wysłuchaniu słowa kluczowego. Na przykład "Hey Cortana" spowoduje uaktywnienie asystenta Cortany.

**Słowo kluczowe wykrywania trendów (KWS)** jest dostępne na następujących platformach:

  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)
  - Funkcja wykrywania trendów słowa kluczowego (KWS) może działać z dowolnym typem mikrofonu, oficjalną obsługą KWS, ale jest obecnie ograniczona do tablic mikrofonów znajdujących się na sprzęcie platformy Azure urządzenia Kinect DK lub zestawu Speech Devices SDK

### <a name="meeting-scenarios"></a>Scenariusze spotkań

Zestaw Speech SDK doskonale nadaje się do scenariuszy spotkań jego przepisywania, niezależnie od tego, czy jest to jedno urządzenie czy konwersacja obejmująca wiele urządzeń.

#### <a name="conversation-transcription"></a>Transkrypcja konwersacji

[Transkrypcja konwersacji](conversation-transcription.md) umożliwia rozpoznawanie mowy w czasie rzeczywistym (i asynchronicznie), identyfikację głosu i przypisanie zdania do każdego głośnika (znanego również jako *diarization*). Doskonale nadaje się do jego przepisywaniach spotkań osób z możliwością odróżniania głośników.

**Transkrypcja konwersacji** jest dostępna na następujących platformach:

  - C++/Windows & Linux
  - C# (Framework & .NET Core)/Windows & platformy UWP & Linux
  - Java/Windows & Linux & Android (Speech Devices SDK)

#### <a name="multi-device-conversation"></a>Konwersacja z obsługą kilku urządzeń

W przypadku [konwersacji z wieloma urządzeniami](multi-device-conversation.md)Połącz wiele urządzeń lub klientów w konwersacji w celu wysyłania komunikatów opartych na mowie lub tekstowych przy użyciu łatwej obsługi transkrypcji i tłumaczenia.

**Konwersacja** z różnymi urządzeniami jest dostępna na następujących platformach:

  - C++/Windows
  - C# (Framework & .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>Scenariusze niestandardowe/agenta

Zestaw mowy SDK może być używany do scenariuszy centrum połączeń jego przepisywania, w których są generowane dane telefoniczne.

#### <a name="call-center-transcription"></a>Przekształcanie nagrań w centrach telefonicznej obsługi klienta

[Transkrypcja centrum rozmów](call-center-transcription.md) jest typowym scenariuszem dla zamiany mowy na tekst dla jego przepisywania dużych ilości danych telefonii, które mogą pochodzić z różnych systemów, takich jak interaktywna Odpowiedź głosowa (IVR). Najnowsze modele rozpoznawania mowy z usługi mowy w programie Excel pod adresem jego przepisywania te dane, nawet w przypadkach, gdy dane są trudne do zrozumienia przez człowieka.

**Transkrypcja centrum połączeń** jest dostępna za pośrednictwem usługi Batch Speech Service za pośrednictwem interfejsu API REST i może być używana w każdej sytuacji.

### <a name="codec-compressed-audio-input"></a>Koder-dekoder skompresowanego sygnału audio

Kilka języków programowania zestawu mowy SDK obsługuje kodery audio w postaci skompresowanego strumienia danych. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">Korzystanie z formatów danych wejściowych kompresji dźwiękowej </a>.

**Skompresowane dane wejściowe audio kodera-dekoder** są dostępne na następujących platformach:

  - C++/Linux
  - /Linux C#
  - Java/Linux, Android i iOS

## <a name="rest-api"></a>Interfejs API REST

Zestaw mowy SDK obejmuje wiele możliwości funkcji usługi mowy, ale w niektórych scenariuszach warto użyć interfejsu API REST.

### <a name="batch-transcription"></a>Transkrypcja wsadowa

[Transkrypcja partii](batch-transcription.md) umożliwia asynchroniczne transkrypcja mowy na tekst w dużych ilościach danych. Transkrypcja partii jest możliwa tylko z interfejsu API REST. Oprócz konwersji dźwięku zamiany mowy na tekst, funkcja zamiany mowy na tekst usługi Batch umożliwia również obsługę diarization i tonacji.

## <a name="customization"></a>Dostosowywanie

Usługa mowy zapewnia doskonałe funkcje z domyślnymi modelami między zamianami mowy na tekst, zamiany tekstu na mowę i rozpoznawanie mowy. Czasami warto zwiększyć wydajność linii bazowej, aby działała jeszcze lepiej przy użyciu unikatowego przypadku użycia. Usługa mowy ma wiele narzędzi do dostosowywania bez kodu, które ułatwiają tworzenie i umożliwienie tworzenia konkurencyjnych zalet przy użyciu niestandardowych modeli opartych na własnych danych. Te modele będą dostępne tylko dla Ciebie i Twojej organizacji.

### <a name="custom-speech-to-text"></a>Custom Speech do tekstu

W przypadku korzystania z zamiany mowy na tekst w celu rozpoznawania i transkrypcji w unikatowym środowisku można tworzyć i uczenia niestandardowych modeli akustycznych, językowych i wymowych w celu obsługi szumów otoczenia lub słownictwa specyficznego dla branż. Tworzenie i Zarządzanie modelami Custom Speech bez kodu jest dostępne za pomocą [portalu Custom Speech](https://aka.ms/customspeech). Po opublikowaniu modelu Custom Speech może on być używany przez zestaw mowy SDK.

### <a name="custom-text-to-speech"></a>Niestandardowa Zamiana tekstu na mowę

Niestandardowa Zamiana tekstu na mowę, nazywana również niestandardowym głosem, jest zestawem narzędzi online, które umożliwiają utworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Tworzenie niestandardowych modeli głosu i zarządzanie nimi za pomocą [niestandardowego portalu głosowego](https://aka.ms/customvoice)jest możliwe. Po opublikowaniu niestandardowego modelu głosu może on być używany przez zestaw Speech SDK.

## <a name="get-the-speech-sdk"></a>Pobieranie zestawu Speech SDK

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

* [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/cognitive-services/)
* [Zobacz jak rozpoznać mowę w języku C #](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)