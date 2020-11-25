---
title: Wytyczne dotyczące formatu dokumentu importowania — QnA Maker
description: Skorzystaj z tych wytycznych dotyczących importowania dokumentów, aby uzyskać najlepsze wyniki dla zawartości.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: d35ea57a68e500deffa99033b83114ec518dd5e3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993809"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Wskazówki dotyczące formatu dla zaimportowanych dokumentów i adresów URL

Zapoznaj się z tymi wskazówkami dotyczącymi formatowania, aby uzyskać najlepsze wyniki dla zawartości.

## <a name="formatting-considerations"></a>Zagadnienia dotyczące formatowania

Po zaimportowaniu pliku lub adresu URL QnA Maker konwertuje i zapisuje zawartość w [formacie promocji](https://en.wikipedia.org/wiki/Markdown). Proces konwersji dodaje do tekstu nowe wiersze, takie jak `\n\n` . Wiedza o formacie promocji pozwala zrozumieć przekonwertowaną zawartość i zarządzać zawartością bazy wiedzy.

Jeśli dodasz lub edytujesz zawartość bezpośrednio w bazie wiedzy, użyj **formatowania promocji** w celu utworzenia zawartości tekstu sformatowanego lub zmiany zawartości formatu promocji, która jest już w odpowiedzi. QnA Maker obsługuje duży format promocji, aby zapewnić możliwość wprowadzenia tekstu sformatowanego do zawartości. Jednak aplikacja kliencka, taka jak rozmowa bot, może nie obsługiwać tego samego zestawu formatów promocji. Ważne jest, aby przetestować wyświetlanie odpowiedzi aplikacji klienta.

Zapoznaj się z pełną listą [typów zawartości i przykładów](./Concepts/content-types.md#file-and-url-data-types).

## <a name="basic-document-formatting"></a>Podstawowe formatowanie dokumentu

QnA Maker identyfikuje sekcje i podsekcje oraz relacje w pliku na podstawie wizualizacji wizualnych, takich jak:

* rozmiar czcionki
* styl czcionki
* Numerowanie
* kolory

## <a name="product-manuals"></a>Podręczniki produktu

Podręcznik jest zwykle materiałem wskazówek, który towarzyszy produktowi. Ułatwia ona użytkownikowi konfigurowanie, używanie, konserwację i rozwiązywanie problemów z produktem. Gdy QnA Maker przetwarza ręcznie, wyodrębnia nagłówki i podtytuły jako pytania i kolejną zawartość jako odpowiedzi. Zobacz przykład [tutaj](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Poniżej znajduje się przykład podręcznika ze stroną indeksu oraz zawartość hierarchiczna

 ![Przykład ręczny produktu dla bazy wiedzy](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Wyodrębnianie działa najlepiej w przypadku instrukcji zawierających Spis treści i/lub stronę indeksu oraz przejrzystą strukturę z nagłówkami hierarchicznymi.

## <a name="brochures-guidelines-papers-and-other-files"></a>Broszury, wytyczne, dokumenty i inne pliki

Wiele innych typów dokumentów można również przetworzyć w celu generowania par pytań i odpowiedzi, pod warunkiem, że mają one przejrzystą strukturę i układ. Obejmują one: broszury, wytyczne, raporty, oficjalne dokumenty, dokumenty naukowe, zasady, książki itp. Zobacz przykład [tutaj](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Poniżej znajduje się przykład dokumentu z częściową strukturą bez indeksu:

 ![Dokument z częściową strukturą usługi Azure Blob Storage](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturalny dokument QnA

Format strukturalnego Question-Answers w plikach DOC ma postać przemiennych pytań i odpowiedzi na wiersz, jedno pytanie na wiersz, a następnie odpowiedź w następującym wierszu, jak pokazano poniżej:

```text
Question1

Answer1

Question2

Answer2
```

Poniżej znajduje się przykład strukturalnego dokumentu programu Word QnA:

 ![Przykład strukturalnego dokumentu QnA dla bazy wiedzy](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturalne pliki *txt*, *TSV* i *xls*

Bazami w postaci pliku Structured *. txt*, *. tsv* lub *xls* można także przekazać do QNA Maker, aby utworzyć lub rozszerzyć bazę wiedzy.  Może to być zwykły tekst lub może zawierać zawartość w formacie RTF lub HTML.

| Pytanie  | Odpowiedź  | Metadane (1 klucz: 1 wartość) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Wszystkie dodatkowe kolumny w pliku źródłowym zostaną zignorowane.

### <a name="example-of-structured-excel-file"></a>Przykład strukturalnego pliku programu Excel

Poniżej znajduje się przykład strukturalnego pliku QnA *. xls* z zawartością HTML:

 ![Przykład strukturalnej QnA programu Excel dla bazy wiedzy](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Przykład alternatywnych pytań dotyczących pojedynczej odpowiedzi w pliku programu Excel

Poniżej znajduje się przykład strukturalnego pliku QnA *. xls* z kilkoma dodatkowymi pytaniami dotyczącymi pojedynczej odpowiedzi:

 ![Przykład alternatywnych pytań dotyczących pojedynczej odpowiedzi w pliku programu Excel](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Po zaimportowaniu pliku para pytań i odpowiedzi znajduje się w bazie wiedzy, jak pokazano poniżej:

 ![Zrzut ekranu z alternatywnymi pytaniami dotyczącymi pojedynczej odpowiedzi zaimportowanej do bazy wiedzy](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturalny format danych za poorednictwem importu

Importowanie bazy wiedzy zastępuje zawartość istniejącej bazy wiedzy. Import wymaga pliku ze strukturą. tsv, który zawiera informacje o źródle danych. Te informacje pomagają w QnA Maker zgrupować pary odpowiedzi i atrybutów do określonego źródła danych.

| Pytanie  | Odpowiedź  | Element źródłowy| Metadane (1 klucz: 1 wartość) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Dobra|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Formatowanie dokumentu wielowymiarowego

* Użyj nagłówków i nagłówków podrzędnych do określenia hierarchii. Na przykład można H1 do określenia elementu Parent QnA i H2, aby zauważyć QnA, które należy wykonać jako monit. Użyj małego rozmiaru nagłówka, aby zauważyć dalszą hierarchię. Nie używaj stylu, koloru ani innego mechanizmu, aby oznaczać strukturę w dokumencie, QnA Maker nie Wyodrębnij wyświetlonych przez siebie wierszy.
* Pierwszy znak nagłówka musi być wielkimi literami.
* Nie kończyj nagłówka ze znakiem zapytania `?` .

**Przykładowe dokumenty**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Korzyści firmy Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Zalety firmy Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z pełną listą [typów zawartości i przykładów](./Concepts/content-types.md#file-and-url-data-types)