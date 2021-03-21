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
ms.openlocfilehash: fc85eec008ef099d63d538e4871a1a84573f5a18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790449"
---
# <a name="what-is-a-voice-assistant"></a>Co to jest asystent głosowy?

Asystenci głosu korzystający z usługi mowy umożliwiają deweloperom tworzenie naturalnych, przypominających konwersacje interfejsów dla aplikacji i środowisk.

Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a implementacją asystenta korzystającą z jednej (1) [bezpośredniej linii mowy](direct-line-speech.md) (za pośrednictwem Azure bot Service) do dodawania funkcji głosowych do botów lub (2) poleceń niestandardowych dla scenariuszy poleceń głosowych.

## <a name="choosing-an-assistant-solution"></a>Wybieranie rozwiązania Asystenta

Pierwszym krokiem tworzenia asystenta głosowego jest podjęcie decyzji o tym, co należy zrobić. Usługa mowy oferuje wiele, uzupełniających się rozwiązań do przedstawiania interakcji z asystentem. Możesz dodać funkcje głosowe i głosowe do elastycznych i uniwersalnych bot utworzonych przy użyciu Azure Bot Service za pomocą kanału [Direct line Speech](direct-line-speech.md) Channel lub użyć prostoty tworzenia [niestandardowej aplikacji poleceń](custom-commands.md) dla prostych scenariuszy poleceń głosowych.

| Jeśli chcesz... | Następnie Rozważmy... | Na przykład: |
|-------------------|------------------|----------------|
|Otwarta-zakończona konwersacja z niezawodną integracją umiejętności i pełną kontrolą wdrożenia | Azure Bot Service bot z [bezpośrednim kanałem mowy wiersza](direct-line-speech.md) | <ul><li>"Muszę przejść do Seattle"</li><li>"Jakiego rodzaju Pizza można zamówić?"</li></ul>
|Polecenia głosowe lub proste konwersacje zorientowane na zadania z uproszczonym tworzeniem i hostingiem | [Polecenia niestandardowe](custom-commands.md) | <ul><li>"Włącz światło narzutu"</li><li>"Przekształć w 5 stopniowy"</li><li>Inne przykłady [dostępne tutaj](https://speech.microsoft.com/customcommands)</li></ul>

Zaleca się [bezpośrednie używanie mowy liniowej](direct-line-speech.md) jako najlepszego wyboru, jeśli nie masz jeszcze pewności, co chcesz obsłużyć asystenta. Oferuje ona integrację z rozbudowanym zestawem narzędzi i ułatwieniami autorskimi, takimi jak [rozwiązanie dla Asystenta wirtualnego i szablon przedsiębiorstwa](/azure/bot-service/bot-builder-enterprise-template-overview) oraz [Usługa QNA Maker](../qnamaker/overview/overview.md) , które umożliwiają kompilowanie wspólnych wzorców i korzystanie z istniejących źródeł wiedzy.

[Polecenia niestandardowe](custom-commands.md) ułatwiają tworzenie zaawansowanych poleceń głosowych zoptymalizowanych pod kątem interakcji głosowych. Zapewnia ujednolicone środowisko tworzenia, automatyczny model hostingu i stosunkowo niższą złożoność, ułatwiając skoncentrowanie się na tworzeniu najlepszego rozwiązania dla scenariuszy poleceń głosowych.

   ![Porównanie rozwiązań Asystenta](media/voice-assistants/assistant-solution-comparison.png "Porównanie rozwiązań Asystenta")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Architektura referencyjna do kompilowania asystenta głosowego przy użyciu zestawu Speech SDK

   ![Diagram koncepcyjny przepływu usługi aranżacji asystenta głosowego](media/voice-assistants/overview.png "Przepływ asystenta głosowego")

## <a name="core-features"></a>Podstawowe funkcje

Bez względu na to, czy wybierasz [bezpośrednią mowę liniową](direct-line-speech.md) , czy [polecenia niestandardowe](custom-commands.md) do tworzenia interakcji asystenta, możesz użyć bogatego zestawu funkcji dostosowywania, aby dostosować asystenta do swojej marki, produktu i osobowości.

| Kategoria | Funkcje |
|----------|----------|
|[Custom — słowo kluczowe](./custom-keyword-basics.md) | Użytkownicy mogą uruchamiać konwersacje z asystentami przy użyciu niestandardowego słowa kluczowego, takiego jak "Hej contoso". Aplikacja wykonuje tę funkcję za pomocą niestandardowego aparatu słów kluczowych w zestawie mowy SDK, który można skonfigurować za pomocą niestandardowego słowa kluczowego [, które można wygenerować w tym miejscu](./custom-keyword-basics.md). Asystenci głosu mogą korzystać z weryfikacji słowa kluczowego po stronie usługi, aby poprawić dokładność aktywacji słowa kluczowego (w przeciwieństwie do samego urządzenia).
|[Zamiana mowy na tekst](speech-to-text.md) | Asystenci głosu konwertują dźwięk w czasie rzeczywistym na rozpoznany tekst przy użyciu [zamiany mowy na tekst](speech-to-text.md) z usługi mowy. Ten tekst jest dostępny, ponieważ jest uzyskanego, zarówno w implementacji asystenta, jak i aplikacji klienckiej.
|[Zamiana tekstu na mowę](text-to-speech.md) | Odpowiedzi na tekst od asystenta są syntezą przy użyciu zamiany [tekstu na mowę](text-to-speech.md) z usługi mowy. Ta synteza jest następnie udostępniana aplikacji klienckiej jako strumień audio. Firma Microsoft oferuje możliwość tworzenia własnego, niestandardowego głosu neuronowych TTS, który daje głos do marki. Aby dowiedzieć się więcej, [skontaktuj się z nami](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Wprowadzenie do asystentów głosowych

Oferujemy Przewodniki Szybki Start zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera listę przewodników szybki start dla asystenta głosowego uporządkowanych według języka.

* [Szybki Start: Tworzenie niestandardowego asystenta głosowego przy użyciu bezpośredniej linii mowy](quickstarts/voice-assistants.md)
* [Szybki Start: Tworzenie aplikacji polecenia głosowego za pomocą poleceń niestandardowych](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>Przykładowy kod i samouczki

Przykładowy kod służący do tworzenia asystenta głosowego jest dostępny w witrynie GitHub. Te przykłady obejmują aplikację kliencką do łączenia się z asystentem w kilku popularnych językach programowania.

* [Przykłady asystenta głosowego w witrynie GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [Samouczek: Włączanie głosu przez asystenta utworzonego przy użyciu Azure Bot Service z zestawem SDK mowy C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Samouczek: Tworzenie aplikacji poleceń niestandardowych przy użyciu prostych poleceń głosowych](./how-to-develop-custom-commands-application.md)

## <a name="customization"></a>Dostosowywanie

W przypadku asystentów głosowych utworzonych przy użyciu usługi Azure Speech Services można używać pełnego zakresu opcji dostosowywania.

* [Custom Speech](./custom-speech-overview.md)
* [Niestandardowy głos](how-to-custom-voice.md)
* [Custom — słowo kluczowe](custom-keyword-overview.md)

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (zobacz [obsługiwane języki](language-support.md)).

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
* [Dowiedz się więcej na temat poleceń niestandardowych](custom-commands.md)
* [Dowiedz się więcej na temat bezpośredniej obsługi mowy w wierszu](direct-line-speech.md)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)