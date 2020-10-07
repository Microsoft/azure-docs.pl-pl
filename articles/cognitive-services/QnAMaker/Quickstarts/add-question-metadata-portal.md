---
title: 'Szybki Start: dodawanie pytań i odpowiedzi w portalu QnA Maker'
description: W tym przewodniku szybki start pokazano, jak dodać pary pytań i odpowiedzi za pomocą metadanych, aby użytkownicy mogli znaleźć odpowiednią odpowiedź na pytanie.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 930acbd3bbdb8f63b6aa888b292025a76435b289
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776752"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Szybki Start: dodawanie pytań i odpowiedzi za pomocą portalu QnA Maker

Po utworzeniu bazy wiedzy Dodaj pary pytań i odpowiedzi (QnA) z metadanymi, aby odfiltrować odpowiedź. Pytania zawarte w poniższej tabeli dotyczą limitów usługi platformy Azure, ale każdy z nich musi wykonać inną usługę Azure Search.

<a name="qna-table"></a>

|Wzrok|Pytania|Odpowiedź|Metadane|
|--|--|--|--|
|1.|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2.|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Po dodaniu metadanych do pary QnA aplikacja kliencka może:

* Zażądaj odpowiedzi, które pasują tylko do niektórych metadanych.
* Odbieraj wszystkie odpowiedzi, ale nie Przetwarzaj odpowiedzi w zależności od metadanych dla każdej odpowiedzi.


## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [poprzedni Przewodnik Szybki Start](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Logowanie się do portalu QnA Maker

1. Zaloguj się do [portalu QNA Maker](https://www.qnamaker.ai).

1. Wybierz istniejącą bazę wiedzy z [poprzedniego przewodnika Szybki Start](../how-to/create-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Dodawanie dodatkowych pytań z możliwością frazy

Bieżąca baza wiedzy ma QnA Maker QnA Rozwiązywanie problemów. Te pary zostały utworzone, gdy adres URL został dodany do bazy wiedzy podczas procesu tworzenia.

Po zaimportowaniu tego adresu URL zostanie utworzona tylko jedno pytanie z jedną odpowiedzią. W tej procedurze należy dodać dodatkowe pytania.

1. Na stronie **Edytuj** Użyj pola tekstowego Wyszukaj powyżej par pytania i odpowiedzi, aby znaleźć pytanie `How large a knowledge base can I create?`

1. W kolumnie **pytania** wybierz pozycję **+ Dodaj alternatywne sformułowanie** , a następnie Dodaj każde nowe sformułowanie, które podano w poniższej tabeli.

    |Alternatywne sformułowanie|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Wybierz pozycję **Zapisz i poszkol** , aby ponownie przeprowadzić uczenie bazy wiedzy.

1. Wybierz pozycję **test**, a następnie wprowadź pytanie blisko jednego z nowych alternatywnych frazy, ale nie dokładnie ten sam wyraz:

    `What GB size can a knowledge base be?`

    Prawidłowa odpowiedź jest zwracana w formacie promocji:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    W przypadku wybrania opcji **Sprawdź** w odpowiedzi na odpowiedź można zobaczyć więcej odpowiedzi, ale nie ma tego samego wysokiego poziomu zaufania.

    Nie dodawaj każdej możliwej kombinacji alternatywnych wyrazów. Po włączeniu [aktywnej uczenia](../how-to/improve-knowledge-base.md)QNA Maker zostanie znaleziony alternatywna frazy, która będzie najlepiej odpowiadać potrzebom użytkowników.

1. Ponownie wybierz **test** , aby zamknąć okno testowe.

## <a name="add-metadata-to-filter-the-answers"></a>Dodawanie metadanych w celu filtrowania odpowiedzi

Dodanie metadanych do pary pytań i odpowiedzi umożliwia aplikacji klienckiej żądanie odfiltrowanych odpowiedzi. Ten filtr jest stosowany przed zastosowaniem [pierwszej i drugiej rangi](../concepts/query-knowledge-base.md#ranker-process) .

1. Dodaj drugą parę pytania i odpowiedzi, bez metadanych, z [pierwszej tabeli w tym przewodniku szybki start](#qna-table), a następnie wykonaj poniższe kroki.

1. Wybierz pozycję **Wyświetl opcje**, a następnie wybierz pozycję **Pokaż metadane**.

1. W przypadku dodanej pary QnA wybierz pozycję **Dodaj Tagi metadanych**, a następnie Dodaj nazwę `service` i wartość `search` . Wygląda to następująco: `service:search` .

1. Dodaj inny tag metadanych o nazwie `link_in_answer` i wartości `false` . Wygląda to następująco: `link_in_answer:false` .

1. Wyszukaj pierwszą odpowiedź w tabeli, `How large a knowledge base can I create?` .

1. Dodaj pary metadanych dla tych samych dwóch tagów metadanych:

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

    Masz teraz dwa pytania z tymi samymi tagami metadanych o różnych wartościach.

1. Wybierz pozycję **Zapisz i poszkol** , aby ponownie przeprowadzić uczenie bazy wiedzy.

1. Wybierz pozycję **Publikuj** w górnym menu, aby przejść do strony publikowanie.
1. Wybierz przycisk **Publikuj** , aby opublikować bieżącą bazę wiedzy w punkcie końcowym.
1. Po opublikowaniu bazy wiedzy przejdź do następnego przewodnika Szybki Start, aby dowiedzieć się, jak wygenerować odpowiedź z bazy wiedzy.

## <a name="what-did-you-accomplish"></a>Co zostało wykonane?

Twoja baza wiedzy została poddana edycji w celu obsługi większej liczby pytań i dostarczonych par nazwa/wartość do obsługi filtrowania podczas wyszukiwania dla najważniejszych odpowiedzi lub dostosujesz, gdy zostanie zwrócona odpowiedź lub odpowiedzi.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz kontynuować korzystania z następnego przewodnika Szybki Start, Usuń QnA Maker i zasoby struktury bot w Azure Portal.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie odpowiedzi przy użyciu narzędzia Postman lub cURL](get-answer-from-knowledge-base-using-url-tool.md)
