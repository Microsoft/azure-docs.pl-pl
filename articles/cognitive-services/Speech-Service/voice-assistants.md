---
title: Asystenci głosu — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla asystentów głosowych przy użyciu zestawu Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79369900"
---
# <a name="what-is-a-voice-assistant"></a>Co to jest asystent głosowy?

Asystenci głosu korzystający z usługi mowy umożliwiają deweloperom tworzenie naturalnych, przypominających konwersacje interfejsów dla aplikacji i środowisk.

Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a implementacją asystenta, która używa albo (1) bezpośredniego kanału mowy w programie bot Framework lub (2) zintegrowanej usługi poleceń niestandardowych (wersja zapoznawcza) w celu ukończenia zadania.

Aplikacje nawiązują połączenie z usługą asystenta głosowego za pomocą zestawu Speech Software Development Kit (SDK).

   ![Diagram koncepcyjny przepływu usługi aranżacji asystenta głosowego](media/voice-assistants/overview.png "Przepływ asystenta głosowego")

## <a name="choosing-an-assistant-solution"></a>Wybieranie rozwiązania Asystenta

Pierwszym krokiem tworzenia asystenta głosowego jest podjęcie decyzji o tym, co należy zrobić. Usługa mowy oferuje wiele, uzupełniających się rozwiązań do przedstawiania interakcji z asystentem. Bez względu na to, czy chcesz elastyczną i uniwersalną infrastrukturę mowy w ramach [linii Direct line](direct-line-speech.md) bot Framework, czy prostota [poleceń niestandardowych (wersja zapoznawcza)](custom-commands.md) w przypadku prostych scenariuszy, wybranie odpowiednich narzędzi zostanie rozpoczęte.

| Jeśli chcesz... | Następnie Rozważmy... | Na przykład... |
|-------------------|------------------|----------------|
|Otwarta-zakończona konwersacja z niezawodną integracją umiejętności i pełną kontrolą wdrożenia | Kanał [mowy liniowej linii](direct-line-speech.md) bot Framework | <ul><li>"Muszę przejść do Seattle"</li><li>"Jakiego rodzaju Pizza można zamówić?"</li></ul>
|Obsługa poleceń i kontrolek oraz ukierunkowanych zadań z uproszczonym tworzeniem i hostingiem | [Polecenia niestandardowe (wersja zapoznawcza)](custom-commands.md) | <ul><li>"Włącz światło narzutu"</li><li>"Przekształć w 5 stopniowy"</ul>

Zaleca się [bezpośrednie używanie mowy liniowej](direct-line-speech.md) jako najlepszego wyboru, jeśli nie masz jeszcze pewności, co chcesz obsłużyć asystenta. Oferuje ona integrację z rozbudowanym zestawem narzędzi i ułatwieniami autorskimi, takimi jak [rozwiązanie dla Asystenta wirtualnego i szablon przedsiębiorstwa](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) oraz [Usługa QNA Maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) , które umożliwiają kompilowanie wspólnych wzorców i korzystanie z istniejących źródeł wiedzy.

[Polecenia niestandardowe (wersja zapoznawcza)](custom-commands.md) zapewniają usprawnione środowisko tworzenia i hostingu specjalnie dostosowane do scenariuszy poleceń i kontroli języka naturalnego.

   ![Porównanie rozwiązań Asystenta](media/voice-assistants/assistant-solution-comparison.png "Porównanie rozwiązań Asystenta")

## <a name="core-features"></a>Podstawowe funkcje

Bez względu na to, czy wybierasz [bezpośrednią mowę liniową](direct-line-speech.md) , czy [polecenia niestandardowe (wersja zapoznawcza)](custom-commands.md) w celu utworzenia interakcji asystenta, możesz użyć bogatego zestawu funkcji dostosowywania, aby dostosować asystenta do swojej marki, produktu i osobowości.

| Kategoria | Funkcje |
|----------|----------|
|[Custom — słowo kluczowe](speech-devices-sdk-create-kws.md) | Użytkownicy mogą uruchamiać konwersacje z asystentami przy użyciu niestandardowego słowa kluczowego, takiego jak "Hej contoso". Aplikacja wykonuje tę funkcję za pomocą niestandardowego aparatu słów kluczowych w zestawie mowy SDK, który można skonfigurować za pomocą niestandardowego słowa kluczowego [, które można wygenerować w tym miejscu](speech-devices-sdk-create-kws.md). Asystenci głosu mogą korzystać z weryfikacji słowa kluczowego po stronie usługi, aby poprawić dokładność aktywacji słowa kluczowego (w przeciwieństwie do samego urządzenia).
|[Zamiana mowy na tekst](speech-to-text.md) | Asystenci głosu konwertują dźwięk w czasie rzeczywistym na rozpoznany tekst przy użyciu [zamiany mowy na tekst](speech-to-text.md) z usługi mowy. Ten tekst jest dostępny, ponieważ jest uzyskanego, zarówno w implementacji asystenta, jak i aplikacji klienckiej.
|[Zamiana tekstu na mowę](text-to-speech.md) | Odpowiedzi na tekst od asystenta są syntezą przy użyciu zamiany [tekstu na mowę](text-to-speech.md) z usługi mowy. Ta synteza jest następnie udostępniana aplikacji klienckiej jako strumień audio. Firma Microsoft oferuje możliwość tworzenia własnego, niestandardowego głosu neuronowych TTS, który daje głos do marki. Aby dowiedzieć się więcej, [skontaktuj się z nami](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Wprowadzenie do asystentów głosowych

Oferujemy Przewodniki Szybki Start zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera listę przewodników szybki start dla asystenta głosowego uporządkowanych według języka.

| Szybki start | Platforma | Dokumentacja interfejsu API |
|------------|----------|---------------|
| C#, PLATFORMY UWP | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Przeglądaj](https://aka.ms/csspeech/javaref) |
| Java | Android | [Przeglądaj](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod służący do tworzenia asystenta głosowego jest dostępny w witrynie GitHub. Te przykłady obejmują aplikację kliencką do łączenia się z asystentem w kilku popularnych językach programowania.

* [Przykłady asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)
* [Samouczek: Włączanie głosu przez asystenta przy użyciu zestawu Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Samouczek

Samouczek dotyczący sposobu [obsługi głosu — Włącz asystenta przy użyciu zestawu mowy SDK i kanału mowy Direct line](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Dostosowywanie

Asystentzy głosu Skompilowani przy użyciu usługi mowy mogą korzystać z pełnego zakresu opcji dostosowywania dostępnych na potrzeby [zamiany mowy na tekst](speech-to-text.md), zamiany [tekstu na mowę](text-to-speech.md)i [niestandardowego słowa kluczowego](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (zobacz [obsługiwane języki](supported-languages.md)).

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Usługa Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
* [Dowiedz się więcej na temat poleceń niestandardowych (wersja zapoznawcza)](custom-commands.md)
* [Dowiedz się więcej na temat bezpośredniej obsługi mowy w wierszu](direct-line-speech.md)
