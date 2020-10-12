---
title: Aktywne sugestie dotyczące uczenia — QnA Maker
description: Aktywne sugestie dotyczące uczenia umożliwiają poprawienie jakości bazy wiedzy przez zasugerowanie alternatywnych pytań na podstawie przesłanych użytkowników do pary pytania i odpowiedzi.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: e8cdb1d471ceef2aef790c426424b9224faac5ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777041"
---
# <a name="active-learning-suggestions"></a>Aktywne sugestie dotyczące uczenia

Funkcja _aktywne sugestie dotyczące uczenia_ pozwala ulepszyć jakość bazy wiedzy, proponując alternatywne pytania na podstawie przesłanych przez użytkowników do pary pytań i odpowiedzi. Te sugestie można przejrzeć, dodając je do istniejących pytań lub odrzucając je.

Baza wiedzy nie zmienia się automatycznie. Aby zmiany zaczęły obowiązywać, należy zaakceptować sugestie. Te sugestie dodają pytania, ale nie zmieniają ani nie usuwają istniejących pytań.

## <a name="what-is-active-learning"></a>Co to jest aktywna nauka?

QnA Maker poznaje nowe odmiany pytań z niejawną i jawną opinią.

* [Niejawna opinia](#how-qna-makers-implicit-feedback-works) — ranga jest zrozumiała, gdy pytanie użytkownika ma wiele odpowiedzi z wynikami, które są bardzo bliskie i uważa za opinię. Nie musisz nic robić, aby to zrobić.
* [Jawne Opinie](#how-you-give-explicit-feedback-with-the-train-api) — w przypadku zwrócenia z bazy wiedzy wielu odpowiedzi z małą różnicą w ocenie, aplikacja kliencka prosi użytkownika o to, jakie pytanie jest odpowiednie. Jawne Opinie użytkownika są wysyłane do QnA Maker za pomocą [interfejsu API uczenia](../How-to/improve-knowledge-base.md#train-api).

Obie metody zapewniają rangę z podobnymi zapytaniami, które są klastrowane.

## <a name="how-active-learning-works"></a>Jak działa aktywna nauka

Aktywne uczenie jest wyzwalane na podstawie ocen odpowiedzi z kilku najważniejszych wartości zwracanych przez QnA Maker. Jeśli różnice między parami QnA, które pasują do zapytania, znajdują się w niewielkim zakresie, zapytanie jest uważane za możliwą sugestię (jako zapytanie alternatywne) dla każdej możliwej pary QnA. Po zaakceptowaniu sugerowanego pytania dotyczącego określonej pary QnA zostanie on odrzucony dla innych par. Należy pamiętać o zapisaniu i pouczeniu po zaakceptowaniu sugestii.

Usługa Active Learning daje najlepsze możliwe sugestie w przypadkach, gdy punkty końcowe uzyskują odpowiednią ilość i różne zapytania dotyczące użycia. Gdy co najmniej 5 podobnych zapytań jest klastrowane, co 30 minut QnA Maker sugeruje pytania oparte na użytkownikach do projektanta bazy wiedzy w celu ich zaakceptowania lub odrzucenia. Wszystkie sugestie są klastrowane według podobieństwa i najlepsze sugestie dotyczące alternatywnych pytań są wyświetlane na podstawie częstotliwości określonych zapytań przez użytkowników końcowych.

Po wybraniu pytań w portalu QnA Maker należy przejrzeć i zaakceptować lub odrzucić te sugestie. Brak interfejsu API do zarządzania sugestiami.

## <a name="turn-on-active-learning"></a>Włącz uczenie aktywne

Domyślnie aktywna nauka jest **wyłączona**.
Aby korzystać z usługi Active Learning:
* Musisz [włączyć aktywne uczenie](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) , aby QNA Maker zbierać alternatywne pytania dotyczące bazy wiedzy.
* Aby wyświetlić sugerowane pytania alternatywne, [Użyj opcji wyświetlania](../How-To/improve-knowledge-base.md#view-suggested-questions) na stronie Edycja.

## <a name="how-qna-makers-implicit-feedback-works"></a>Jak działa niejawna opinia QnA Maker

Niejawna opinia QnA Maker używa algorytmu do określenia bliskości oceny, a następnie udostępnia aktywne sugestie dotyczące uczenia. Algorytm określający bliskość nie jest prostym wyliczeniem. Zakresów w poniższym przykładzie nie są przeznaczone do naprawienia, ale powinny być używane jako przewodnik, aby zrozumieć wpływ tylko algorytmu.

Gdy Ocena pytania ma duże wątpliwości, na przykład 80%, zakres ocen, które są brane pod uwagę w przypadku aktywnego uczenia, jest szeroki, około 10%. W miarę zmniejszania wyniku zaufania, takiego jak 40%, zakres ocen również zmniejsza się, około 4%.

W następującej odpowiedzi JSON z zapytania do QnA Maker generateAnswer, wyniki dla A, B i C są blisko i byłyby traktowane jako sugestie.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker nie wie, która odpowiedź jest najlepszą odpowiedzią. Skorzystaj z listy sugestii portalu QnA Maker, aby wybrać najlepszą odpowiedź i ponownie szkolić.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Jak przekazać jawne informacje zwrotne za pomocą interfejsu API uczenia

QnA Maker musi mieć jawną opinię na temat tego, która z odpowiedzi była najlepszą odpowiedzią. Najlepsza odpowiedź jest określana przez użytkownika i może obejmować:

* Opinie użytkowników, wybierając jedną z odpowiedzi.
* Logika biznesowa, na przykład określenie akceptowalnego zakresu oceny.
* Kombinacja opinii użytkowników i logiki biznesowej.

Użyj [interfejsu API uczenia](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) , aby wysłać poprawną odpowiedź do QNA Maker, po wybraniu go przez użytkownika.

## <a name="next-step"></a>Następny krok

> [!div class="nextstepaction"]
> [Zapytanie z bazą wiedzy](query-knowledge-base.md)