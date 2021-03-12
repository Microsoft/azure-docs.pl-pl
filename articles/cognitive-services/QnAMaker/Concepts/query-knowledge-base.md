---
title: Badanie bazy wiedzy — QnA Maker
description: Baza wiedzy musi być opublikowana. Po opublikowaniu baza wiedzy jest wysyłana w punkcie końcowym przewidywania środowiska uruchomieniowego przy użyciu interfejsu API generateAnswer.
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: c723d1446c90290929bc8cad066b4744e284f3f4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008675"
---
# <a name="query-the-knowledge-base-for-answers"></a>Zapytanie dotyczące odpowiedzi z bazy wiedzy

Baza wiedzy musi być opublikowana. Po opublikowaniu baza wiedzy jest wysyłana w punkcie końcowym przewidywania środowiska uruchomieniowego przy użyciu interfejsu API generateAnswer. Zapytanie zawiera tekst pytania i inne ustawienia, aby ułatwić QnA Maker wybranie najlepszego możliwego dopasowania do odpowiedzi.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Jak QnA Maker przetwarza zapytanie użytkownika w celu wybrania najlepszej odpowiedzi

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Przeszkolony i [opublikowany](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QNA Maker baza wiedzy otrzymuje zapytanie użytkownika z bot lub innej aplikacji klienckiej w [interfejsie API GenerateAnswer](../how-to/metadata-generateanswer-usage.md). Na poniższym diagramie przedstawiono proces po odebraniu zapytania użytkownika.

![Proces modelu klasyfikowania dla zapytania użytkownika](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Proces rangi

Ten proces został wyjaśniony w poniższej tabeli.

|Krok|Przeznaczenie|
|--|--|
|1|Aplikacja kliencka wysyła zapytanie użytkownika do [interfejsu API GenerateAnswer](../how-to/metadata-generateanswer-usage.md).|
|2|QnA Maker wstępnie przetworzy zapytanie użytkownika przy użyciu wykrywania języka, modułu sprawdzania pisowni i wyłączników słów.|
|3|Przetwarzanie wstępne jest podejmowane w celu zmiany zapytania użytkownika w celu uzyskania najlepszych wyników wyszukiwania.|
|4|To zmienione zapytanie jest wysyłane do indeksu Wyszukiwanie poznawcze platformy Azure, który otrzymuje `top` liczbę wyników. Jeśli prawidłowa odpowiedź nie jest w tych wynikach, zwiększ wartość `top` nieco. Ogólnie rzecz biorąc, wartość 10 dla `top` robót budowlanych w 90% zapytań. Filtry usługi Azure Search [zatrzymują słowa](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) w tym kroku.|
|5|QnA Maker używa składni i semantyki opartej na cechowania w celu określenia podobieństwa między zapytania użytkownika i pobieranymi wynikami QnA.|
|6|Model rankingu o określonej maszynie używa różnych funkcji, od kroku 5, do określenia wyników pewności i nowej kolejności klasyfikacji.|
|7|Nowe wyniki są zwracane do aplikacji klienckiej w kolejności uporządkowanej.|
|||

Używane funkcje obejmują, ale nie są ograniczone do semantyki na poziomie wyrazów, ważności na poziomie terminu w korpus i głębokiego uczenia się modeli semantycznych w celu określenia podobieństwa i zgodności między dwoma ciągami tekstowymi.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Przeszkolony i [opublikowany](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QNA Maker baza wiedzy otrzymuje zapytanie użytkownika z bot lub innej aplikacji klienckiej w [interfejsie API GenerateAnswer](../how-to/metadata-generateanswer-usage.md). Na poniższym diagramie przedstawiono proces po odebraniu zapytania użytkownika.

![Proces modelu klasyfikowania dla podglądu zapytania użytkownika](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>Proces rangi

Ten proces został wyjaśniony w poniższej tabeli.

|Krok|Przeznaczenie|
|--|--|
|1|Aplikacja kliencka wysyła zapytanie użytkownika do [interfejsu API GenerateAnswer](../how-to/metadata-generateanswer-usage.md).|
|2|QnA Maker wstępnie przetworzy zapytanie użytkownika przy użyciu wykrywania języka, modułu sprawdzania pisowni i wyłączników słów.|
|3|Przetwarzanie wstępne jest podejmowane w celu zmiany zapytania użytkownika w celu uzyskania najlepszych wyników wyszukiwania.|
|4|To zmienione zapytanie jest wysyłane do indeksu Wyszukiwanie poznawcze platformy Azure, który otrzymuje `top` liczbę wyników. Jeśli prawidłowa odpowiedź nie jest w tych wynikach, zwiększ wartość `top` nieco. Ogólnie rzecz biorąc, wartość 10 dla `top` robót budowlanych w 90% zapytań. Filtry usługi Azure Search [zatrzymują słowa](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) w tym kroku.|
|5|QnA Maker korzysta z modelu opartego na funkcji przekształcania grafiki, aby określić podobieństwo między zapytania użytkownika a wynikami sugestii QnA pobranych z usługi Azure Wyszukiwanie poznawcze. Model oparty na przekształcaniu to model wielojęzykowy uczenia głębokiego, który działa w poziomie dla wszystkich języków, aby określić wyniki zaufania i nową kolejność klasyfikacyjną.|
|6|Nowe wyniki są zwracane do aplikacji klienckiej w kolejności uporządkowanej.|
|||

Funkcja rank działa na wszystkich alternatywnych pytaniach i odpowiedzi, aby znaleźć najlepsze dopasowane pary QnA dla zapytania użytkownika. Użytkownicy mają elastyczność, aby skonfigurować rangę tylko do rangi. 

---

## <a name="http-request-and-response-with-endpoint"></a>Żądanie HTTP i odpowiedź z punktem końcowym
Po opublikowaniu bazy wiedzy Usługa tworzy punkt końcowy HTTP oparty na protokole REST, który można zintegrować z aplikacją, zazwyczaj bot rozmowy.

### <a name="the-user-query-request-to-generate-an-answer"></a>Żądanie kwerendy użytkownika w celu wygenerowania odpowiedzi

Zapytanie użytkownika to pytanie, że użytkownik końcowy prosi o bazę wiedzy, np `How do I add a collaborator to my app?` .. Zapytanie jest często w formacie języka naturalnego lub kilka słów kluczowych, które reprezentują pytanie, na przykład `help with collaborators` . Zapytanie jest wysyłane do bazy wiedzy z żądania HTTP w aplikacji klienckiej.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```
Aby kontrolować odpowiedź, należy ustawić właściwości, takie jak [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)i [strictFilters](../how-to/query-knowledge-base-with-metadata.md).

Użyj [kontekstu konwersacji](../how-to/query-knowledge-base-with-metadata.md) z [funkcją](../how-to/multiturn-conversation.md) wieloskładnikową, aby zachować konwersację w celu uściślenia pytań i odpowiedzi, aby znaleźć poprawną i końcową odpowiedź.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Odpowiedź z wywołania do wygenerowania odpowiedzi

Odpowiedź HTTP to odpowiedź pobierana z bazy wiedzy, na podstawie najlepszego dopasowania dla danego zapytania użytkownika. Odpowiedź zawiera odpowiedź i wynik przewidywania. Jeśli zażądano więcej niż jednej górnej odpowiedzi z `top` właściwością, uzyskasz więcej niż jedną największą odpowiedź z wynikiem.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wynik pewności](./confidence-score.md)
