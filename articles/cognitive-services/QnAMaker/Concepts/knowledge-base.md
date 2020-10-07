---
title: Importowanie ze źródeł danych — QnA Maker
description: Dowiedz się, jak zaimportować pary pytań i odpowiedzi ze źródeł danych — QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5f609dc27aa1251cfad0249d26ef5140936bfe41
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776956"
---
# <a name="importing-from-data-sources"></a>Importowanie ze źródeł danych

Baza wiedzy składa się z par pytań i odpowiedzi, które są udostępniane przez publiczne adresy URL i pliki.

## <a name="data-source-locations"></a>Lokalizacje źródła danych

Zawartość jest przenoszona do bazy wiedzy ze źródła danych. Lokalizacje źródeł danych to **publiczne adresy URL lub pliki**, które nie wymagają uwierzytelniania.

Wyjątkiem są [pliki programu SharePoint](../how-to/add-sharepoint-datasources.md)zabezpieczone przy użyciu uwierzytelniania. Zasoby programu SharePoint muszą być plikami, a nie stronami sieci Web. Jeśli adres URL jest zakończony rozszerzeniem internetowym, na przykład ASPX, nie zostanie on zaimportowany do usługi QnA Maker z programu SharePoint.

## <a name="chit-chat-content"></a>Zawartość rozmowy Chit

Zestaw zawartości Chit Chat QnA jest oferowany jako kompletne źródło danych zawartości w kilku językach i stylach konwersacji. Może to być punkt początkowy dla osobowości Botu. pozwoli to zaoszczędzić czas i koszt ich zapisania od zera. Dowiedz się [, jak dodać](../how-to/chit-chat-knowledge-base.md) ten zestaw zawartości do bazy wiedzy.

## <a name="structured-data-format-through-import"></a>Strukturalny format danych za poorednictwem importu

Importowanie bazy wiedzy zastępuje zawartość istniejącej bazy wiedzy. Import wymaga pliku strukturalnego `.tsv` , który zawiera pytania i odpowiedzi. Te informacje pomagają w QnA Maker zgrupować pary odpowiedzi i atrybutów do określonego źródła danych.

| Pytanie  | Odpowiedź  | Źródło| Metadane (1 klucz: 1 wartość) |
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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Cykl życia opracowywania bazy wiedzy](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Zobacz też

Skorzystaj z QnA Maker informacji o [promocji](../reference-markdown-format.md) , aby ułatwić formatowanie odpowiedzi.

[Omówienie usługi QnA Maker](../Overview/overview.md)

Tworzenie i edytowanie bazy wiedzy przy użyciu:
* [Interfejs API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generuj odpowiedź z:
* [Interfejs API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Zestaw SDK platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
