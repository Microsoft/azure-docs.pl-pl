---
title: Format promocji — QnA Maker
description: Poniżej znajduje się lista formatów promocji, których można użyć w tekście odpowiedzi QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f5f640f8895cd6defe31a68b790ce841c4679a6f
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777355"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Format promocji jest obsługiwany w QnA Maker tekstowym odpowiedzi

QnA Maker przechowuje tekst odpowiedzi jako przewidzianą w promocji. Istnieje wiele rodzajów promocji. Aby upewnić się, że tekst odpowiedzi jest zwracany i wyświetlany prawidłowo, użyj tego odwołania.

Skorzystaj z samouczka **[CommonMark](https://commonmark.org/help/tutorial/index.html)** , aby sprawdzić poprawność. Samouczek zawiera funkcję **Wypróbuj ją** na potrzeby szybkiej weryfikacji kopiowania/wklejania.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Kiedy należy używać edycji tekstu sformatowanego w przeciwieństwie do promocji

[Edytowanie tekstu rozbudowanego](How-To/edit-knowledge-base.md#add-an-editorial-qna-set) odpowiedzi pozwala użytkownikowi, jak autor, korzystać z paska narzędzi formatowania, aby szybko wybierać i formatować tekst.

Aby automatycznie generować zawartość w celu zaimportowania baz wiedzy w ramach potoku ciągłej integracji/ciągłego wdrażania lub [testowania wsadowego](Quickstarts/batch-testing.md), należy jeszcze lepszym narzędziem.

## <a name="supported-markdown-format"></a>Obsługiwany format promocji

Poniżej znajduje się lista formatów promocji, których można użyć w tekście odpowiedzi QnA Maker.

|Przeznaczenie|Format|Przykładowy kod języka Markdown|Renderowanie<br>jak w programie Chat bot|
|--|--|--|--|
Nowy wiersz między 2 zdaniami.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![Formatuj nowy wiersz między dwoma zdaniami](./media/qnamaker-concepts-datasources/format-newline.png)|
|Nagłówki od h1 do H6 —, liczba wskazuje, `#` który nagłówek. 1 `#` to H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![Formatuj z nagłówkami promocji](./media/qnamaker-concepts-datasources/format-headers.png)<br>![format z nagłówkami promocji od h1 do h](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kursywa |`*text*`|`How do I create a bot with *QnA Maker*?`|![Formatuj z kursywą](./media/qnamaker-concepts-datasources/format-italics.png)|
|Strong (pogrubiony)|`**text**`|`How do I create a bot with **QnA Maker**?`|![Formatuj z silnym oznaczeniem dla pogrubienia](./media/qnamaker-concepts-datasources/format-strong.png)|
|Adres URL linku|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![Format adresu URL (hiperłącze)](./media/qnamaker-concepts-datasources/format-url.png)|
|* Adres URL obrazu publicznego|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![Format adresu URL obrazu publicznego ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Przekreślenie|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![Format przekreślenia](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Pogrubienie i kursywa|`***text***`|`How can I create a ***QnA Maker*** bot?`|![Format dla pogrubienia i kursywy](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Pogrubienie adresu URL dla linku|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![Format pogrubionego adresu URL](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Adres URL w postaci kursywy dla linku|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![Format adresu URL kursywy](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Symbole promocji z przeznaczeniem|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![Format symboli o niedrogiej promocji.](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Lista uporządkowana|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>W poprzednim przykładzie funkcja automatycznego numerowania została wbudowana w funkcję promocji.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>W poprzednim przykładzie jest stosowane jawne numerowanie.|![Format listy uporządkowanej](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Listy nieuporządkowane|`\n * item1 \n * item2`<br>lub<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![Format listy nieuporządkowanej](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Listy zagnieżdżone|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Uporządkowane i nieuporządkowane listy można zagnieżdżać razem. Karta, `\t` ,, wskazuje poziom wcięcia elementu podrzędnego.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![Format zagnieżdżonej listy nieuporządkowanej](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![Format zagnieżdżonej listy uporządkowanej](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

* QnA Maker nie przetwarza obrazu w jakikolwiek sposób. Jest to rola aplikacji klienckiej do renderowania obrazu.

Jeśli chcesz dodać zawartość przy użyciu funkcji aktualizacji/zastępowania interfejsów API bazy wiedzy, a zawartość/plik zawiera Tagi HTML, możesz zachować kod HTML w pliku, upewniając się, że otwierając i zamykając Tagi są konwertowane w zakodowanym formacie.

| Zachowaj kod HTML  | Reprezentacja w żądaniu interfejsu API  | Reprezentacja w KB |
|-----------|---------|-------------------------|
| Tak | \&lt; br \& gt; | &lt;br&gt; |
| Tak | \&lt; H3 \& gt; nagłówek \& lt;/H3 \& gt; | &lt;&gt;/H3 nagłówka &lt; H3&gt; |

Ponadto znak CR LF (\r\n) jest konwertowany na \n w KB. Znaki LF (\n) są utrzymywane w postaci, w jakiej jest. Jeśli chcesz wypróbować dowolną sekwencję ucieczki, taką jak \t lub \n, możesz użyć ukośnika odwrotnego, na przykład: " \\ \\ r \\ \\ n" i " \\ \\ t"

## <a name="next-steps"></a>Następne kroki

Przejrzyj [formaty plików](reference-tsv-format-batch-testing.md)testów wsadowych.
