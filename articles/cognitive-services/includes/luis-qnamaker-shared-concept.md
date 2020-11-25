---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: 78b93c05621754ae499e4ae8ca6b66c5bbfaad1b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026073"
---
Cognitive Services oferuje dwie usługi przetwarzania języka naturalnego, [Language Understanding](../luis/what-is-luis.md) i [QNA Maker](../qnamaker/overview/overview.md), z których każdy ma inny cel. Informacje o tym, kiedy należy używać każdej usługi i od siebie nawzajem.

Przetwarzanie języka naturalnego (NLP) umożliwia aplikacji klienckiej, takiej jak czat bot, współpracującej z użytkownikami przy użyciu języka naturalnego. Użytkownik wprowadza zdanie lub frazę. Tekst użytkownika może mieć niską gramatykę, pisownię i interpunkcję. Usługa poznawcze może nadal pracować przez zdanie użytkownika, zwracając informacje, które bot rozmowy musi pomóc użytkownikowi.

## <a name="cognitive-services-with-nlp"></a>Cognitive Services z NLP

Language Understanding (LUIS) i QnA Maker udostępniają NLP. Aplikacja kliencka przesyła tekst w języku naturalnym. Usługa przyjmuje tekst, przetwarza go i zwraca wynik.

## <a name="when-to-use-each-service"></a>Kiedy używać każdej usługi

Language Understanding (LUIS) i QnA Maker rozwiązują różne problemy. LUIS określa cel tekstu użytkownika (znany jako wypowiedź), podczas gdy QnA Maker określa odpowiedź do tekstu użytkownika (nazywanego kwerendą).

Aby można było wybrać poprawną usługę, należy zrozumieć tekst użytkownika pochodzący z aplikacji klienckiej oraz informacje o tym, co aplikacja kliencka musi pobrać z usługi poznawczej.

Jeśli rozmowa bot odbiera tekst `How do I get to the Human Resources building on the Seattle North campus?` , Skorzystaj z poniższej tabeli, aby zrozumieć, jak działa każda usługa z tekstem.

|Usługa|Aplikacja kliencka określa|
|--|--|
|LUIS|**Określa zamiar użytkownika** tekst — usługa nie zwraca odpowiedzi na pytanie. Na przykład ten tekst jest klasyfikowany jako pasujący do `FindLocation` zamiaru.<br>|
|QnA Maker|**Zwraca odpowiedź na pytanie** z niestandardowej bazy wiedzy. Na przykład ten tekst jest określany jako pytanie z odpowiedzią na tekst statyczny  `Get on the #9 bus and get off at Franklin street` .|
|||

> [!div class="mx-imgBorder"]
> ![Grafika informacyjna, aby określić, kiedy należy używać LUIS i kiedy używać QnA Maker](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>Kiedy używać LUIS?

Używaj usługi LUIS, gdy musisz znać zamiar wypowiedzi w ramach procesu w czatbocie. Kontynuując przykładowy tekst, `How do I get to the Human Resources building on the Seattle North campus?` , Jeśli wiesz, że zamiarem użytkownika jest znalezienie lokalizacji, możesz przekazać szczegóły dotyczące wypowiedź (ściągając z jednostkami) do innej usługi, takiej jak serwer transportu, aby uzyskać odpowiedź.

Nie musisz łączyć LUIS i QnA Maker, aby określić cel.

Możesz połączyć te dwie usługi dla tego wypowiedź, jeśli bot rozmowy musi przetworzyć tekst w oparciu o zamiary i jednostki (przy użyciu LUIS), a także znaleźć konkretną odpowiedź tekstową (przy użyciu QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Kiedy używać QnA Maker?

Używaj usługi QnA Maker, jeśli masz statyczną bazę wiedzy z odpowiedziami. Ta baza wiedzy jest dostosowana do Twoich potrzebach i zawiera dokumenty, takie jak pliki PDF i adresy URL.

Kontynuując przykład wypowiedź, `How do I get to the Human Resources building on the Seattle North campus?` Wyślij tekst w formie zapytania do opublikowanej usługi QNA Maker i otrzymasz najlepszą odpowiedź.

Nie musisz łączyć LUIS i QnA Maker, aby określić odpowiedź na pytanie.

Możesz połączyć te dwie usługi dla tego wypowiedź, jeśli bot rozmowy musi przetworzyć tekst w oparciu o zamiary i jednostki (przy użyciu LUIS), a także znaleźć odpowiedź (przy użyciu QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Użyj obu usług, gdy baza wiedzy jest niepełna

Jeśli tworzysz QnA Maker bazę wiedzy, ale wiesz, że domena podmiotu jest zmieniana (na przykład informacje o czasie), możesz połączyć usługi LUIS i QnA Maker. Pozwala to na użycie informacji z bazy wiedzy, ale również określenie zamiaru użytkownika przy użyciu LUIS. Gdy aplikacja kliencka jest zamiarem, może zażądać odpowiednich informacji z innego źródła.

Aplikacja kliencka musi monitorować zarówno LUIS, jak i QnA Maker odpowiedzi dla wyników. Jeśli wynik QnA Maker jest poniżej pewnego progu, użyj informacji o zamiarach i jednostkach zwróconych z LUIS, aby przekazać informacje do usługi innej firmy.

Kontynuując przykładowy tekst, `How do I get to the Human Resources building on the Seattle North campus?` Załóżmy, że QNA Maker zwraca wynik o niskiej pewności. Użyj zamierzeń zwróconych z LUIS `FindLocation` oraz wszelkich wyodrębnionych jednostek, takich jak `Human Resources building` i `Seattle North campus` ,, aby wysłać te informacje do mapowania lub usługi wyszukiwania dla innej odpowiedzi.

Tę odpowiedź innej firmy można przedstawić użytkownikowi na potrzeby weryfikacji. Po zatwierdzeniu użytkownika możesz wrócić do QnA Maker, aby dodać informacje, aby zwiększyć swoją wiedzę.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Używaj obu usług, gdy rozmowa bot potrzebuje więcej informacji

Jeśli rozmowa bot potrzebuje więcej informacji niż zapewnia usługa, aby kontynuować przez drzewo decyzyjne, należy użyć obu tych usług i przetworzyć obie odpowiedzi w aplikacji klienckiej.

Użyj narzędzia **[interfejsu wiersza polecenia do wysyłania](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** bot Framework, aby pomóc w tworzeniu procesu do pracy z obiema usługami. To narzędzie służy do tworzenia najwyższej aplikacji LUIS, które są wysyłane między LUIS i QnA Maker jako aplikacje podrzędne. [Dowiedz się więcej](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0) o integrowaniu z LUIS, QNA Maker i bot Framework.

Użyj przykładu bot Builder, **NLP z wysyłaniem** w [języku C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) lub [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), aby zaimplementować ten typ rozmowy bot.

## <a name="best-practices"></a>Najlepsze rozwiązania

Zaimplementuj najlepsze rozwiązania dla każdej usługi:

* Najlepsze rozwiązania [Luis](../luis/luis-concept-best-practices.md)
* [QNA Maker](../qnamaker/concepts/best-practices.md) najlepszych praktyk

## <a name="see-also"></a>Zobacz też

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Interfejs wiersza polecenia wysyłania](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Przykłady struktury bot Framework](https://github.com/Microsoft/BotBuilder-Samples)
* [Usługa Azure bot](/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Emulator usługi Azure bot](https://github.com/Microsoft/BotFramework-Emulator)
* [Czat w sieci Web bot Framework](https://github.com/microsoft/BotFramework-WebChat)