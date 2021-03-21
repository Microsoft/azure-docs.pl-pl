---
title: Direct line — mowę — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla asystentów głosowych korzystających z bezpośredniej mowy wiersza z zestawem SDK (Speech Software Development Kit).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6eb689c2e93b5a9cac932e428245410ba35e77d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611797"
---
# <a name="what-is-direct-line-speech"></a>Co to jest mowa Direct Line?

**Bezpośrednia funkcja rozpoznawania wierszy** to niezawodne, kompleksowe rozwiązanie służące do tworzenia elastycznych, rozszerzalnych asystentów głosowych. Jest on obsługiwany przez platformę bot i kanał mowy linii Direct, który jest zoptymalizowany pod kątem komunikacji głosowej z botów.

[Asystenci głosowani](voice-assistants.md) nasłuchują użytkowników i podejmują działania w odpowiedzi, często mówiąc z powrotem. Używają [zamiany mowy na tekst](speech-to-text.md) , aby transkrypcja mowę użytkownika, a następnie podejmować działania dotyczące interpretacji języka naturalnego tekstu. Ta akcja często obejmuje głosowanie danych wyjściowych wygenerowanego przez asystenta z funkcją [zamiany tekstu na mowę](text-to-speech.md).

Funkcja bezpośrednie linie rozpoznawania mowy oferuje najwyższy poziom dostosowania i złożoności dla asystentów głosowych. Jest ona przeznaczona dla scenariuszy konwersacji, które są otwartymi, naturalnymi lub hybrydowymi z dwoma z ukończeniem zadania lub użyciem kontroli poleceń. Taka wysoka elastyczność ma większą złożoność, a scenariusze, które są ograniczone do dobrze zdefiniowanych zadań przy użyciu danych wejściowych języka naturalnego, mogą chcieć rozważyć użycie [poleceń niestandardowych](custom-commands.md) dla usprawnionego środowiska rozwiązania.

## <a name="getting-started-with-direct-line-speech"></a>Wprowadzenie do bezpośredniej obsługi mowy w wierszu

Pierwszym etapem tworzenia asystenta głosowego za pomocą polecenia Direct line Speech jest [uzyskanie klucza subskrypcji mowy](overview.md#try-the-speech-service-for-free), utworzenie nowego bot skojarzonego z tą subskrypcją i połączenie bot z kanałem mowy linii bezpośredniej.

   ![Diagram koncepcyjny przepływu usługi aranżacji mowy liniowej](media/voice-assistants/overview-directlinespeech.png "Przepływ kanału mowy")

Aby zapoznać się z kompletnym przewodnikiem krok po kroku dotyczącym tworzenia prostego asystenta głosowego przy użyciu bezpośredniej linii rozpoznawania mowy, zobacz [Samouczek dotyczący zamiany mowy na bot za pomocą zestawu Speech SDK i kanału mowy Direct line](tutorial-voice-enable-your-bot-speech-sdk.md).

Oferujemy również Przewodniki Szybki Start zaprojektowane z założeniami szybkiego uruchamiania kodu i uczenia interfejsów API. Ta tabela zawiera listę przewodników Szybki Start, zorganizowanych według języka i platformy.

| Szybki start | Platforma | Dokumentacja interfejsu API |
|------------|----------|---------------|
| C#, PLATFORMY UWP | Windows | [Przeglądaj](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Przeglądaj](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Przeglądaj](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod służący do tworzenia asystenta głosowego jest dostępny w witrynie GitHub. Te przykłady obejmują aplikację kliencką do łączenia się z asystentem w kilku popularnych językach programowania.

* [Przykłady asystenta głosowego (SDK)](https://aka.ms/csspeech/samples/#voice-assistants-quickstarts)
* [Samouczek: Włączanie głosu przez asystenta przy użyciu zestawu Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Dostosowywanie

Asystentów głosowych utworzonych za pomocą usługi mowy mogą korzystać z pełnego zakresu opcji dostosowywania dostępnych na potrzeby [zamiany mowy na tekst](speech-to-text.md), zamiany [tekstu na mowę](text-to-speech.md)i [niestandardowego słowa kluczowego](./custom-keyword-basics.md).

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (zobacz [obsługiwane języki](./language-support.md)).

Bezpośrednia obsługa mowy liniowej i skojarzonych z nią funkcji dla asystentów głosowych jest idealnym uzupełnieniem rozwiązania do obsługi [asystenta wirtualnego i szablonu przedsiębiorstwa](/azure/bot-service/bot-builder-enterprise-template-overview). Mimo że funkcja Direct line Speech może współdziałać z dowolnymi zgodnymi bot, te zasoby zapewniają linię bazową wielokrotnego użytku dla środowisk komunikacji o wysokiej jakości, a także często wspierane umiejętności i modele umożliwiające szybkie rozpoczęcie pracy.

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Zestaw SDK rozpoznawania mowy](./speech-sdk.md)
* [Azure Bot Service](/azure/bot-service/)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
* [Tworzenie i wdrażanie podstawowego bota](/azure/bot-service/bot-builder-tutorial-basic-deploy)
* [Pobierz rozwiązanie dla Asystenta wirtualnego i szablonu przedsiębiorstwa](https://github.com/Microsoft/AI)
