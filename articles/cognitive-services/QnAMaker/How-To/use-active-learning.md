---
title: Korzystanie z usługi Active Learning z bazą wiedzy QnA Maker
description: Dowiedz się, jak poprawić jakość bazy wiedzy dzięki aktywnej uczeniu. Przeglądanie, akceptowanie lub odrzucanie, Dodawanie bez usuwania lub zmieniania istniejących pytań.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: ffc1a0a401de634c1932b9653f231b377c4f154e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591926"
---
# <a name="active-learning"></a>Aktywna nauka

Funkcja _aktywne sugestie dotyczące uczenia_ pozwala ulepszyć jakość bazy wiedzy, proponując alternatywne pytania na podstawie przesłanych przez użytkowników do pary pytań i odpowiedzi. Te sugestie można przejrzeć, dodając je do istniejących pytań lub odrzucając je.

Baza wiedzy nie zmienia się automatycznie. Aby zmiany zaczęły obowiązywać, należy zaakceptować sugestie. Te sugestie dodają pytania, ale nie zmieniają ani nie usuwają istniejących pytań.

## <a name="what-is-active-learning"></a>Co to jest aktywna nauka?

QnA Maker poznaje nowe odmiany pytań z niejawną i jawną opinią.

* [Niejawna opinia](#how-qna-makers-implicit-feedback-works) — ranga jest zrozumiała, gdy pytanie użytkownika ma wiele odpowiedzi z wynikami, które są bardzo bliskie i uważa za opinię. Nie musisz nic robić, aby to zrobić.
* [Jawne Opinie](#how-you-give-explicit-feedback-with-the-train-api) — w przypadku zwrócenia z bazy wiedzy wielu odpowiedzi z małą różnicą w ocenie, aplikacja kliencka prosi użytkownika o to, jakie pytanie jest odpowiednie. Jawne Opinie użytkownika są wysyłane do QnA Maker za pomocą [interfejsu API uczenia](../How-To/improve-knowledge-base.md#train-api).

Obie metody zapewniają rangę z podobnymi zapytaniami, które są klastrowane.

## <a name="how-active-learning-works"></a>Jak działa aktywna nauka

Aktywne uczenie jest wyzwalane na podstawie ocen odpowiedzi z kilku najważniejszych wartości zwracanych przez QnA Maker. Jeśli różnice między parami QnA, które pasują do zapytania, znajdują się w niewielkim zakresie, zapytanie jest uważane za możliwą sugestię (jako zapytanie alternatywne) dla każdej możliwej pary QnA. Po zaakceptowaniu sugerowanego pytania dotyczącego określonej pary QnA zostanie on odrzucony dla innych par. Należy pamiętać o zapisaniu i pouczeniu po zaakceptowaniu sugestii.

Usługa Active Learning daje najlepsze możliwe sugestie w przypadkach, gdy punkty końcowe uzyskują odpowiednią ilość i różne zapytania dotyczące użycia. Gdy co najmniej 5 podobnych zapytań jest klastrowane, co 30 minut QnA Maker sugeruje pytania oparte na użytkownikach do projektanta bazy wiedzy w celu ich zaakceptowania lub odrzucenia. Wszystkie sugestie są klastrowane według podobieństwa i najlepsze sugestie dotyczące alternatywnych pytań są wyświetlane na podstawie częstotliwości określonych zapytań przez użytkowników końcowych.

Po wybraniu pytań w portalu QnA Maker należy przejrzeć i zaakceptować lub odrzucić te sugestie. Brak interfejsu API do zarządzania sugestiami.

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

Użyj [interfejsu API uczenia](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) , aby wysłać poprawną odpowiedź do QNA Maker, po wybraniu go przez użytkownika.

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Uaktualnij wersję środowiska uruchomieniowego, aby użyć usługi Active Learning

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Usługa Active Learning jest obsługiwana w środowisku uruchomieniowym w wersji 4.4.0 i nowszych. Jeśli baza wiedzy została utworzona w starszej wersji, [Uaktualnij środowisko uruchomieniowe](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) , aby użyć tej funkcji.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

W QnA Maker Managed (wersja zapoznawcza), ponieważ środowisko wykonawcze jest hostowane przez usługę QnA Maker, nie ma potrzeby ręcznego uaktualniania środowiska uruchomieniowego.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Włącz aktywną naukę w przypadku pytań alternatywnych

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Aktywna nauka jest domyślnie wyłączona. Włącz tę opcję, aby zobaczyć sugerowane pytania. Po włączeniu aktywnego uczenia należy wysłać informacje z aplikacji klienckiej do QnA Maker. Aby uzyskać więcej informacji, zobacz temat [Architektura przepływu na potrzeby używania GenerateAnswer i uczenia interfejsów API z bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Wybierz pozycję **Publikuj** , aby opublikować bazę wiedzy. Aktywne zapytania szkoleniowe są zbierane tylko z punktu końcowego przewidywania interfejsu API GenerateAnswer. Zapytania do okienka test w portalu QnA Maker nie wpływają na aktywną naukę.

1. Aby włączyć aktywną naukę w portalu QnA Maker, przejdź do prawego górnego rogu, wybierz swoją **nazwę** i przejdź do pozycji [**Ustawienia usługi**](https://www.qnamaker.ai/UserSettings).

    ![Na stronie ustawień usługi Włącz zaproponowane wątpliwości dotyczące aktywnego uczenia. Wybierz swoją nazwę użytkownika w prawym górnym menu, a następnie wybierz pozycję Ustawienia usługi.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Znajdź usługę QnA Maker a następnie Przełącz **aktywną naukę**.

    > [!div class="mx-imgBorder"]
    > [![Na stronie Ustawienia usługi Przełącz funkcję Active Learning. Jeśli nie możesz przełączać tej funkcji, może być konieczne uaktualnienie usługi.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Dokładna wersja na powyższym obrazie jest pokazywana tylko jako przykład. Twoja wersja może być inna.

    Po włączeniu **aktywnej uczenia** baza wiedzy sugeruje nowe pytania w regularnych odstępach czasu na podstawie pytań przesłanych przez użytkownika. **Aktywną naukę** można wyłączyć, przełączając ponownie ustawienie.
    
# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Domyślnie usługa Active Learning jest **włączona** w QNA Maker Managed (wersja zapoznawcza). Aby wyświetlić sugerowane pytania alternatywne, [Użyj opcji wyświetlania](../How-To/improve-knowledge-base.md#view-suggested-questions) na stronie Edycja.

---

## <a name="review-suggested-alternate-questions"></a>Przejrzyj sugerowane pytania alternatywne

Zapoznaj się z [alternatywnymi sugerowanymi pytaniami](improve-knowledge-base.md) na stronie **edytowania** każdej bazy wiedzy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy](./manage-knowledge-bases.md)
