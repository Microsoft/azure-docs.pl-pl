---
title: Cykl życia bazy wiedzy — QnA Maker
description: QnA Maker uczy się najlepiej w iteracyjnym cyklu zmian modelu, wypowiedź przykładów, publikowania i zbierania danych z zapytań punktów końcowych.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77914956"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Cykl życia bazy wiedzy w QnA Maker
QnA Maker uczy się najlepiej w iteracyjnym cyklu zmian modelu, wypowiedź przykładów, publikowania i zbierania danych z zapytań punktów końcowych.

![Cykl tworzenia](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Tworzenie bazy wiedzy QnA Maker
Punkt końcowy QnA Maker Knowledge Base (KB) zapewnia najlepszą zgodność z kwerendą użytkownika w oparciu o zawartość KB. Tworzenie bazy wiedzy to jednorazowa akcja służąca do konfigurowania repozytorium zawartości pytań, odpowiedzi i skojarzonych metadanych. Bazę wiedzy można utworzyć, przeszukiwanie istniejącej zawartości, takiej jak strony często zadawanych pytań, podręczniki produktu lub strukturalne pary pytań i odpowiedzi. Dowiedz się, jak [utworzyć bazę wiedzy](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testowanie i aktualizowanie bazy wiedzy

Baza wiedzy jest gotowa do testowania, gdy zostanie wypełniona zawartością, z redakcją lub przez automatyczne wyodrębnianie. Testy interaktywne można wykonać w portalu QnA Maker za pomocą panelu **test** , wprowadzając typowe zapytania użytkownika i sprawdzając, czy odpowiedzi zwrócone z prawidłową odpowiedzią i odpowiednim wynikiem pewności.

* **Aby naprawić oceny o niskim poziomie pewności**: Dodaj alternatywne pytania.
* **Gdy zapytanie niepoprawnie zwróci [domyślną odpowiedź](../How-to/change-default-answer.md)**: Dodaj nowe odpowiedzi do poprawnego pytania.

Ta ścisła pętla test-Update jest kontynuowana, dopóki wyniki nie zostaną spełnione. Dowiedz się, jak [przetestować bazę wiedzy](../How-To/test-knowledge-base.md).

W przypadku dużych artykułów bazy wiedzy Użyj testów automatycznych z [interfejsem API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) i `isTest` właściwością Body, które wysyła `test` zapytanie do bazy wiedzy zamiast opublikowanej bazy wiedzy.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy
Po zakończeniu testowania bazy wiedzy możesz ją opublikować. Opublikowanie powoduje wypchnięcie najnowszej wersji przetestowanej bazy wiedzy na dedykowany indeks Wyszukiwanie poznawcze platformy Azure reprezentujący **opublikowaną** bazę wiedzy. Zostanie również utworzony punkt końcowy, który można wywoływać w aplikacji lub czatbocie.

W ten sposób wszelkie zmiany wprowadzone w wersji testowej bazy wiedzy nie mają wpływu na opublikowaną wersję, która może znajdować się w aplikacji produkcyjnej.

Każdy z tych baz wiedzy może być przeznaczony do testowania osobno. Korzystając z interfejsów API, można wskazać wersję testową bazy wiedzy z `isTest` właściwością Body w wywołaniu generateAnswer.

Dowiedz się, jak [opublikować bazę wiedzy](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Monitorowanie użycia
Aby móc rejestrować dzienniki rozmowy usługi, należy włączyć Application Insights podczas [tworzenia usługi QNA Maker](../How-To/set-up-qnamaker-service-azure.md).

Możesz uzyskać różne analizy użycia usługi. Dowiedz się więcej na temat sposobu korzystania z usługi Application Insights w celu uzyskania [analiz w usłudze QNA Maker](../How-To/get-analytics-knowledge-base.md).

Na podstawie tego, co uczysz się na analizie, wprowadź odpowiednie [aktualizacje bazy wiedzy](../How-To/edit-knowledge-base.md).

## <a name="version-control-for-data-in-your-knowledge-base"></a>Kontrola wersji danych w bazie wiedzy

Kontrola wersji danych jest dostępna za pomocą funkcji Import/Export na stronie **Ustawienia** w portalu QNA Maker.

Można utworzyć kopię zapasową bazy wiedzy, eksportując bazę wiedzy w obu `.tsv` lub `.xls` formatach. Po wyeksportowaniu należy uwzględnić ten plik jako część regularnego sprawdzania kontroli źródła.

Jeśli musisz wrócić do określonej wersji, musisz zaimportować ten plik z systemu lokalnego. Wyeksportowana baza wiedzy **może** być używana tylko przez import na stronie **Ustawienia** . Nie można jej użyć jako źródła danych dokumentu lub pliku adresu URL. Spowoduje to zamienienie pytań i odpowiedzi znajdujących się w bazie wiedzy z zawartością zaimportowanego pliku.

## <a name="test-and-production-knowledge-base"></a>Baza wiedzy testowej i produkcyjnej
Baza wiedzy to repozytorium pytań i zestawów odpowiedzi utworzonych, obsługiwanych i używanych przez QnA Maker. Każdy zasób QnA Maker może zawierać wiele baz wiedzy.

Baza wiedzy ma dwa stany: *test* i *opublikowano*.

### <a name="test-knowledge-base"></a>Baza wiedzy testowej

*Baza wiedzy testowej* jest obecnie edytowana, zapisywana i przetestowana pod kątem dokładności i kompletności odpowiedzi. Zmiany wprowadzone w bazie wiedzy testowej nie mają wpływu na użytkownika końcowego aplikacji lub rozmowy bot. Baza wiedzy o testowaniu jest znana `test` jako żądanie HTTP. `test` Wiedza jest dostępna w okienku interaktywnego **testu** portalu QNA Maker.

### <a name="production-knowledge-base"></a>Baza wiedzy produkcyjnej

*Opublikowana baza wiedzy* jest wersją używaną w programie Chat bot lub aplikacji. Akcja publikowania bazy wiedzy umieszcza zawartość bazy wiedzy testowej w opublikowanej wersji bazy wiedzy. Ponieważ opublikowana baza wiedzy jest wersją używaną przez aplikację w punkcie końcowym, upewnij się, że zawartość jest poprawna i dobrze przetestowana. Opublikowana baza wiedzy jest znana jako `prod` żądanie HTTP.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aktywne sugestie dotyczące uczenia](./active-learning-suggestions.md)