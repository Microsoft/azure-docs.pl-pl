---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.author: diberry
ms.openlocfilehash: 02610e647e2138cbf52f86c22107feec2d61273b
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604965"
---
Cognitive Services oferuje dwie usługi przetwarzania języka naturalnego, [Language Understanding](../luis/what-is-luis.md) i [QnA Maker](../qnamaker/overview/overview.md), każda o innym celu. Zrozum, kiedy korzystać z każdej usługi i jak się wzajemnie komplementują.

Przetwarzanie języka naturalnego (NLP) umożliwia aplikacji klienckiej, takiej jak czat bot, do pracy z użytkownikami, przy użyciu języka naturalnego. Użytkownik wprowadza zdanie lub frazę. Tekst użytkownika może mieć słabą gramatykę, pisownię i znaki interpunkcyjne. Usługa Cognitive Service może pracować za pomocą zdania użytkownika tak, zwracając informacje, które bot czatu musi pomóc użytkownikowi.

## <a name="cognitive-services-with-nlp"></a>Usługi kognitywne z NLP

Zrozumienie języka (LUIS) i QnA Maker zapewniają NLP. Aplikacja kliencka przesyła tekst w języku naturalnym. Usługa pobiera tekst, przetwarza go i zwraca wynik.

## <a name="when-to-use-each-service"></a>Kiedy korzystać z każdej usługi

Rozumienie języka (LUIS) i QnA Maker rozwiązują różne problemy. Usługa LUIS określa intencję tekstu użytkownika (nazywaną wypowiedź), podczas gdy program QnA Maker określa odpowiedź na tekst użytkownika (znany jako kwerenda).

Aby wybrać poprawną usługę, należy zrozumieć tekst użytkownika pochodzący z aplikacji klienckiej i jakie informacje aplikacja kliencka musi uzyskać z usługi Cognitive Service.

Jeśli twój czat bot `How do I get to the Human Resources building on the Seattle North campus?`odbiera tekst, skorzystaj z poniższego wykresu, aby zrozumieć, jak każda usługa działa z tekstem.

|Usługa|Aplikacja kliencka określa|
|--|--|
|LUIS|**Określa zamiar użytkownika** tekstu — usługa nie zwraca odpowiedzi na pytanie. Na przykład ten tekst jest `FindLocation` klasyfikowany jako pasujące intencji.<br>|
|QnA Maker|**Zwraca odpowiedź na pytanie** z niestandardowej bazy wiedzy. Na przykład ten tekst jest określany jako pytanie `Get on the #9 bus and get off at Franklin street`ze statyczną odpowiedzią tekstową .|
|||

## <a name="when-do-you-use-luis"></a>Kiedy używasz usługi LUIS?

Użyj usługi LUIS, gdy trzeba znać intencji wypowiedź jako część procesu w bot czatu. Kontynuując przykładowy tekst, , gdy wiesz, `How do I get to the Human Resources building on the Seattle North campus?`że intencją użytkownika jest znalezienie lokalizacji, można przekazać szczegóły dotyczące wypowiedź (wyciągnął z jednostek) do innej usługi, takich jak serwer transportu, aby uzyskać odpowiedź.

Nie trzeba łączyć usługi LUIS i QnA Maker w celu określenia intencji.

Możesz połączyć dwie usługi dla tej wypowiedzi, jeśli bot czatu musi przetworzyć tekst na podstawie intencji i jednostek (przy użyciu usługi LUIS), a także znaleźć określoną statyczną odpowiedź tekstową (przy użyciu programu QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Kiedy korzystasz z QnA Maker?

Użyj QnA Maker, gdy masz statyczną bazę wiedzy odpowiedzi. Ta baza wiedzy jest dostosowana do Twoich potrzeb, które zostały utworzone z dokumentów, takich jak pliki PDF i adresy URL.

Kontynuując z wypowiedź `How do I get to the Human Resources building on the Seattle North campus?`przykład, , wysłać tekst, jako zapytanie, do opublikowanej usługi QnA Maker i otrzymać najlepszą odpowiedź.

Nie trzeba łączyć usługi LUIS i QnA Maker, aby określić odpowiedź na pytanie.

Możesz połączyć dwie usługi dla tej wypowiedzi, jeśli bot czatu musi przetworzyć tekst na podstawie intencji i jednostek (przy użyciu usługi LUIS), a także znaleźć odpowiedź (przy użyciu programu QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Korzystaj z obu usług, gdy baza wiedzy jest niekompletna

Jeśli budujesz swoją bazę wiedzy QnA Maker, ale wiesz, że domena tematu się zmienia (na przykład informacje na czas), możesz połączyć usługi LUIS i QnA Maker. Dzięki temu można użyć informacji w bazie wiedzy, ale także użyć usługi LUIS do określenia intencji użytkownika. Gdy aplikacja kliencka ma zamiar, może zażądać odpowiednich informacji z innego źródła.

Aplikacja kliencka będzie musiała monitorować odpowiedzi usługi LUIS i QnA Maker pod kątem wyników. Jeśli wynik z programu QnA Maker jest poniżej pewnego dowolnego progu, użyj intencji i informacji o jednostce zwróconych z usługi LUIS, aby przekazać informacje do usługi innej firmy.

Kontynuując przykładowy tekst, załóżmy, `How do I get to the Human Resources building on the Seattle North campus?`że program QnA Maker zwraca niski wynik zaufania. Użyj intencji zwróconych `FindLocation` z usługi LUIS i `Human Resources building` wyodrębnionych jednostek, takich jak i `Seattle North campus`, aby wysłać te informacje do usługi mapowania lub wyszukiwania innej odpowiedzi.

Można przedstawić tę odpowiedź innej firmy do użytkownika do weryfikacji. Po uzyskaniu zgody użytkownika możesz wrócić do programu QnA Maker, aby dodać informacje, aby zwiększyć swoją wiedzę.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Korzystaj z obu usług, gdy twój czat bot potrzebuje więcej informacji

Jeśli twój czat bot potrzebuje więcej informacji niż którakolwiek z usług zapewnia, aby kontynuować za pośrednictwem drzewa decyzyjnego, należy użyć obu usług i przetwarzać obie odpowiedzi w aplikacji klienckiej.

Użyj narzędzia **[Interfejsu wiersza polecenia wysyłki](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** platformy bot, aby ułatwić tworzenie procesu do pracy z obiema usługami. To narzędzie tworzy najlepszą aplikację usługi LUIS intencji, która wywołuje między usługą LUIS i QnA Maker jako aplikacje podrzędne.

Użyj przykładu konstruktora botów, **NLP z wysyłką**, w [języku C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) lub [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), aby zaimplementować tego typu bota czatu.

## <a name="best-practices"></a>Najlepsze rozwiązania

Implementowanie najlepszych rozwiązań dla każdej usługi:

* Najważniejsze wskazówki dotyczące [usługi LUIS](../luis/luis-concept-best-practices.md)
* [QnA Maker](../qnamaker/concepts/best-practices.md) – najważniejsze wskazówki

## <a name="see-also"></a>Zobacz też

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [Ekspres QnA](../qnamaker/overview/overview.md)
* [Wysyłka interfejsu wiersza polecenia](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Przykłady struktury botów](https://github.com/Microsoft/BotBuilder-Samples)
* [Usługa bota platformy Azure](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Emulator bota platformy Azure](https://github.com/Microsoft/BotFramework-Emulator)
* [Czat internetowy z platformą botów](https://github.com/microsoft/BotFramework-WebChat)