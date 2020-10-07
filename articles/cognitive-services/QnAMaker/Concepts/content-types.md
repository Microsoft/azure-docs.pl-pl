---
title: Typy zawartości — QnA Maker
description: Typy zawartości obejmują wiele standardowych dokumentów strukturalnych, takich jak PDF, DOCX i TXT.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 50924ce035d9a7dc7778cf45668dc993ee5486e4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776990"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Typy zawartości dokumentów, które można dodać do bazy wiedzy
Typy zawartości obejmują wiele standardowych dokumentów strukturalnych, takich jak PDF, DOC i TXT.

## <a name="file-and-url-data-types"></a>Typy danych plików i adresów URL

Poniższa tabela zawiera podsumowanie typów zawartości i formatów plików, które są obsługiwane przez QnA Maker.

|Typ źródła|Typ zawartości| Przykłady|
|--|--|--|
|Adres URL|Często zadawane pytania<br> (Płaski, z sekcjami lub z stroną główną tematów)<br>Strony pomocy technicznej <br> (Artykuły z artykułami z artykułu z jedną stroną, rozwiązywanie problemów itp.)|[Zwykłe często zadawane pytania](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Często zadawane pytania dotyczące linków](https://www.microsoft.com/en-us/software-download/faq),<br> [Często zadawane pytania dotyczące strony głównej tematów](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artykuł pomocy technicznej](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PLIK PDF/DOC|Najczęściej<br> Podręcznik produktu,<br> Broszury,<br> Drukowane<br> Zasady ulotek,<br> Przewodnik pomocy technicznej,<br> QnA strukturalne,<br> I tak dalej.|**Bez przełączania**<br>[QnA.docxstrukturalne ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Przykładowy Manual.pdfproduktu ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Przykład semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Przykład białych paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Wiele przełączania**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Korzyści firmy Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Zalety firmy Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Strukturalny plik QnA<br> (w tym RTF, obsługa HTML)|**Bez przełączania**:<br>[Przykład QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Wiele przełączania**:<br>[Strukturalna FAQ.xlsprosta ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Laptop FAQ.xlsSurface ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Strukturalny plik QnA|[Przykład Chit-Chat. tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Jeśli potrzebujesz uwierzytelniania dla źródła danych, weź pod uwagę następujące metody, aby uzyskać tę zawartość do QnA Maker:

* Pobierz plik ręcznie i zaimportuj do QnA Maker
* Dodaj plik ze uwierzytelnionej [lokalizacji programu SharePoint](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>Zawartość adresu URL

Dwa typy dokumentów można zaimportować za pośrednictwem **adresu URL** w QNA Maker:

* Adresy URL często zadawane pytania
* Adresy URL pomocy technicznej

Każdy typ wskazuje oczekiwany format.

## <a name="file-based-content"></a>Zawartość oparta na plikach

Pliki można dodawać do bazy wiedzy ze źródła publicznego lub lokalnego systemu plików w [portalu QNA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Wytyczne dotyczące formatowania zawartości

Dowiedz się więcej na temat [wytycznych dotyczących formatu](../reference-document-format-guidelines.md) dla różnych plików.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jakie informacje są przechowywane w [parze pytań i odpowiedzi (QNA)](question-answer-set.md).
