---
title: Źródła danych i typy zawartości — QnA Maker
description: Dowiedz się, jak zaimportować pary pytań i odpowiedzi ze źródeł danych i obsługiwanych typów zawartości, takich jak pliki PDF, DOCX i TXT QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 285e9e2c3187ea78898b53f27f953fc182cdb344
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128468"
---
# <a name="importing-from-data-sources"></a>Importowanie ze źródeł danych

Baza wiedzy składa się z par pytań i odpowiedzi, które są udostępniane przez publiczne adresy URL i pliki.

## <a name="data-source-locations"></a>Lokalizacje źródła danych

Zawartość jest przenoszona do bazy wiedzy ze źródła danych. Lokalizacje źródeł danych to **publiczne adresy URL lub pliki**, które nie wymagają uwierzytelniania.

Wyjątkiem są [pliki programu SharePoint](../how-to/add-sharepoint-datasources.md)zabezpieczone przy użyciu uwierzytelniania. Zasoby programu SharePoint muszą być plikami, a nie stronami sieci Web. Jeśli adres URL jest zakończony rozszerzeniem internetowym, na przykład ASPX, nie zostanie on zaimportowany do usługi QnA Maker z programu SharePoint.

## <a name="chit-chat-content"></a>Chit — zawartość rozmowy

Zestaw zawartości Chit-Chat jest oferowany jako kompletne źródło danych zawartości w kilku językach i stylach konwersacji. Może to być punkt początkowy dla osobowości Botu. pozwoli to zaoszczędzić czas i koszt ich zapisania od zera. Dowiedz się [, jak dodać zawartość Chit z rozmowy](../how-to/chit-chat-knowledge-base.md) do bazy wiedzy.

## <a name="structured-data-format-through-import"></a>Strukturalny format danych za poorednictwem importu

Importowanie bazy wiedzy zastępuje zawartość istniejącej bazy wiedzy. Import wymaga pliku strukturalnego `.tsv` , który zawiera pytania i odpowiedzi. Te informacje pomagają w QnA Maker zgrupować pary odpowiedzi i atrybutów do określonego źródła danych.

| Pytanie  | Odpowiedź  | Element źródłowy| Metadane (1 klucz: 1 wartość) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Dobra|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturalny format wielowymiarowy przy użyciu importu

W formacie pliku można tworzyć konwersacje wieloskładnikowe `.tsv` . Ten format umożliwia tworzenie wielostopniowych konwersacji przez analizowanie poprzednich dzienników rozmowy (z innymi procesami, a nie za pomocą QnA Maker), a następnie Tworzenie `.tsv` pliku za pośrednictwem automatyzacji. Zaimportuj plik w celu zamienienia istniejącej bazy wiedzy.

> [!div class="mx-imgBorder"]
> ![Model koncepcyjny 3 poziomów pytań z obsługą wieloletnich](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Kolumna dla wielowierszowego, charakterystyczna dla wielowierszowego `.tsv` , jest **wyświetlane**. Przykład `.tsv` pokazany w programie Excel, Pokaż informacje do uwzględnienia w celu zdefiniowania elementów podrzędnych:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** jest liczbą, a **DisplayText** jest tekstem, który nie powinien zawierać promocji.

> [!div class="mx-imgBorder"]
> ![Przykład pytania z obsługą wiele przełączania, jak pokazano w programie Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Eksportuj jako przykład

Jeśli nie wiesz, jak reprezentować parę QnA w `.tsv` pliku:
* Użyj tego [przykładu do pobrania z usługi GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Lub Utwórz parę w portalu QnA Maker, Zapisz, a następnie wyeksportuj bazę wiedzy, aby zapoznać się z przykładem sposobu reprezentowania pary.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Typy zawartości dokumentów, które można dodać do bazy wiedzy
Typy zawartości obejmują wiele standardowych dokumentów strukturalnych, takich jak PDF, DOC i TXT.

### <a name="file-and-url-data-types"></a>Typy danych plików i adresów URL

Poniższa tabela zawiera podsumowanie typów zawartości i formatów plików, które są obsługiwane przez QnA Maker.

|Typ źródła|Typ zawartości| Przykłady|
|--|--|--|
|Adres URL|Często zadawane pytania<br> (Płaski, z sekcjami lub z stroną główną tematów)<br>Strony pomocy technicznej <br> (Artykuły z artykułami z artykułu z jedną stroną, rozwiązywanie problemów itp.)|[Zwykłe często zadawane pytania](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Często zadawane pytania dotyczące linków](https://www.microsoft.com/en-us/software-download/faq),<br> [Często zadawane pytania dotyczące strony głównej tematów](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artykuł pomocy technicznej](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PLIK PDF/DOC|Najczęściej<br> Podręcznik produktu,<br> Broszury,<br> Drukowane<br> Zasady ulotek,<br> Przewodnik pomocy technicznej,<br> QnA strukturalne,<br> I tak dalej.|**Bez przełączania**<br>[QnA.docxstrukturalne ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Przykładowy Manual.pdfproduktu ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Przykład semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Przykład białych paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Wiele przełączania**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Korzyści firmy Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Zalety firmy Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Strukturalny plik QnA<br> (w tym RTF, obsługa HTML)|**Bez przełączania**:<br>[Przykład QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Wiele przełączania**:<br>[Strukturalna FAQ.xlsprosta ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Laptop FAQ.xlsSurface ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Strukturalny plik QnA|[Przykład Chit-Chat. tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Jeśli potrzebujesz uwierzytelniania dla źródła danych, weź pod uwagę następujące metody, aby uzyskać tę zawartość do QnA Maker:

* Pobierz plik ręcznie i zaimportuj go do QnA Maker
* Dodaj plik ze uwierzytelnionej [lokalizacji programu SharePoint](../how-to/add-sharepoint-datasources.md)

### <a name="url-content"></a>Zawartość adresu URL

Dwa typy dokumentów można zaimportować za pośrednictwem **adresu URL** w QNA Maker:

* Adresy URL często zadawane pytania
* Adresy URL pomocy technicznej

Każdy typ wskazuje oczekiwany format.

### <a name="file-based-content"></a>Zawartość oparta na plikach

Pliki można dodawać do bazy wiedzy ze źródła publicznego lub lokalnego systemu plików w [portalu QNA Maker](https://www.qnamaker.ai).

### <a name="content-format-guidelines"></a>Wytyczne dotyczące formatowania zawartości

Dowiedz się więcej na temat [wytycznych dotyczących formatu](../reference-document-format-guidelines.md) dla różnych plików.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jakie informacje są przechowywane w [parze pytań i odpowiedzi (QNA)](question-answer-set.md).