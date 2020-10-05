---
title: Co to jest usługa QnA Maker?
description: QnA Maker to oparta na chmurze usługa NLP, która w prosty sposób tworzy naturalną komunikację między danymi. Można go użyć, aby znaleźć najbardziej odpowiednią odpowiedź dla danego danych wejściowych języka naturalnego, z niestandardowej bazy wiedzy (KB).
ms.topic: overview
ms.date: 05/26/2020
ms.openlocfilehash: d2ff2d789f2ea1ae6018d95ef1d880da87b4ff74
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "83994871"
---
# <a name="what-is-the-qna-maker-service"></a>Co to jest usługa QnA Maker?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker to oparta na chmurze usługa przetwarzania języka naturalnego (NLP), która w prosty sposób tworzy naturalną komunikację między danymi. Można go użyć, aby znaleźć najbardziej odpowiednią odpowiedź dla danego danych wejściowych języka naturalnego, z niestandardowej bazy wiedzy (KB).

Aplikacja kliencka dla QnA Maker to każda aplikacja do konwersacji, która komunikuje się z użytkownikiem w języku naturalnym, aby odpowiedzieć na pytanie. Przykładami aplikacji klienckich są aplikacje mediów społecznościowych, czatboty i aplikacje klasyczne z funkcją rozpoznawania mowy.

## <a name="when-to-use-qna-maker"></a>Kiedy używać QnA Maker

* **Jeśli masz informacje statyczne** — Użyj QNA Maker, jeśli masz informacje statyczne w bazie wiedzy o odpowiedziach. Ta baza wiedzy jest niestandardowa w Twoich potrzebach, które zostały skompilowane przy użyciu dokumentów, takich jak [pliki PDF i adresy URL](../concepts/content-types.md).
* **Jeśli chcesz podać tę samą odpowiedź na żądanie, pytanie lub polecenie** — gdy różne użytkowników przesyłają te same pytania, zwracana jest ta sama odpowiedź.
* **Gdy chcesz filtrować informacje statyczne w oparciu o meta-informacje** — Dodaj Tagi [metadanych](../how-to/metadata-generateanswer-usage.md) , aby zapewnić dodatkowe opcje filtrowania odpowiednie dla użytkowników aplikacji klienckiej i informacje. Typowe informacje o metadanych obejmują [Chit — rozmowę](../how-to/chit-chat-knowledge-base.md), typ zawartości lub format, cel zawartości i świeżość zawartości.
* **Gdy chcesz zarządzać konwersacją bot zawierającą informacje statyczne** — baza wiedzy Pobiera tekst lub polecenie konwersacji użytkownika i odpowiada na nie. Jeśli odpowiedź jest częścią wstępnie określonego przepływu konwersacji, reprezentowanej w bazie wiedzy z [kontekstem wielodostępnym](../how-to/multiturn-conversation.md), bot może łatwo udostępnić ten przepływ.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Korzystanie z QnA Maker bazy wiedzy w bot rozmowy

Po opublikowaniu QnA Maker bazy wiedzy aplikacja kliencka wysyła pytanie do punktu końcowego bazy wiedzy i otrzymuje wyniki jako odpowiedź JSON. Typowa aplikacja kliencka dla QnA Maker jest bot rozmowy.

![Poproś bot pytania i uzyskaj odpowiedzi z zawartości bazy wiedzy](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Krok|Akcja|
|:--|:--|
|1|Aplikacja kliencka wysyła _pytanie_ użytkownika (tekst we własnych słowach), "Jak mogę programowo zaktualizować moją bazę wiedzy?". do punktu końcowego bazy wiedzy.|
|2|QnA Maker korzysta z przeszkolonej bazy wiedzy, aby zapewnić poprawną odpowiedź i wszelkie monity monitujące, których można użyć do uściślenia wyszukiwania najlepszej odpowiedzi. QnA Maker zwraca odpowiedź sformatowaną w formacie JSON.|
|3|Aplikacja kliencka używa odpowiedzi JSON, aby podejmować decyzje dotyczące kontynuowania rozmowy. Te decyzje mogą obejmować wyświetlanie odpowiedzi z góry i przedstawienie większej liczby opcji w celu ograniczenia wyszukiwania najlepszej odpowiedzi. |
|||

## <a name="what-is-a-knowledge-base"></a>Co to jest baza wiedzy?

QnA Maker [importuje zawartość](../concepts/knowledge-base.md) do bazy wiedzy o parach pytań i odpowiedzi. Proces importowania wyodrębnia informacje o relacji między częściami zawartości strukturalnej i częściowo strukturalnej, aby implikuje relacje między parami pytania i odpowiedzi. Można edytować te pary pytań i odpowiedzi lub dodawać nowe pary.

Zawartość pary pytań i odpowiedzi obejmuje:
* Wszystkie alternatywne formy pytania
* Tagi metadanych używane do filtrowania opcji odpowiedzi podczas wyszukiwania
* Monity do kontynuowania uściślania wyszukiwania

![Przykład pytania i odpowiedzi z metadanymi](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Po opublikowaniu bazy wiedzy aplikacja kliencka wysyła do punktu końcowego pytanie użytkownika. Usługa QnA Maker przetwarza pytanie i reaguje na najlepszą odpowiedź.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Tworzenie i publikowanie bot bez kodu oraz zarządzanie nim

Portal QnA Maker zapewnia pełną obsługę tworzenia bazy wiedzy. Możesz importować dokumenty w ich bieżącym formularzu do bazy wiedzy. Te dokumenty (takie jak często zadawane pytania, Podręcznik produktu, arkusz kalkulacyjny lub strona sieci Web) są konwertowane na pary pytań i odpowiedzi. Każda para jest analizowana pod kątem monitów kolejnych i podłączonych do innych par. Końcowy format _promocji_ obsługuje zaawansowaną prezentację, w tym obrazy i linki.

Po edytowaniu bazy wiedzy Opublikuj bazę wiedzy w działającej [aplikacji internetowej platformy Azure bot](https://azure.microsoft.com/services/bot-service/) bez konieczności pisania kodu. Przetestuj swój bot w [Azure Portal](https://portal.azure.com) lub Pobierz i Kontynuuj programowanie.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Jakość wyszukiwania i Klasyfikacja zapewniają najlepszą możliwą odpowiedź

System QnA Maker jest podejściem z klasyfikacją warstwową. Dane są przechowywane w usłudze Azure Search, która również pełni rolę pierwszej warstwy klasyfikacyjnej. Najważniejsze wyniki z usługi Azure Search są następnie przenoszone QnA Maker przez NLPy model zmiany klasyfikacji, aby utworzyć końcowe wyniki i wynik pewności.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker usprawnia proces konwersacji

QnA Maker zapewnia wielodostępne i aktywne uczenie, aby pomóc w ulepszaniu podstawowych par pytań i odpowiedzi.

Polecenia z **obsługą wielodostępności** umożliwiają połączenie par pytań i odpowiedzi. To połączenie umożliwia aplikacji klienckiej dostarczenie odpowiedzi na największą odpowiedź i zawiera więcej pytań dotyczących wyszukiwania końcowej odpowiedzi.

Po otrzymaniu przez bazę wiedzy pytań od użytkowników w opublikowanym punkcie końcowym program QnA Maker stosuje **aktywną naukę** do tych rzeczywistych pytań, aby zasugerować zmiany w bazie wiedzy w celu poprawienia jakości.

## <a name="development-lifecycle"></a>Cykl projektowania oprogramowania

QnA Maker zapewnia tworzenie, uczenie i publikowanie wraz z uprawnieniami do współpracy w celu zintegrowania z pełnym cyklem życia.

> [!div class="mx-imgBorder"]
> ![Obraz przedstawiający koncepcję cyklu programowania](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Jak rozpocząć?

**Krok 1**. utworzenie zasobu QnA Maker w [Azure Portal](https://portal.azure.com).

**Krok 2**. Tworzenie bazy wiedzy w portalu [QNA Maker](https://www.qnamaker.ai) . Dodaj [pliki i adresy URL](../concepts/content-types.md) , aby utworzyć bazę wiedzy.

**Krok 3**. opublikowanie bazy wiedzy i przetestowanie jej z niestandardowego punktu końcowego za pomocą [zaskrytka lub](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)wystawcy.

**Krok 4**. w aplikacji klienckiej programowe wywoływanie punktu końcowego bazy wiedzy. Aplikacja kliencka przetwarza odpowiedź JSON w celu wyświetlenia najlepszej odpowiedzi użytkownikowi.

## <a name="next-steps"></a>Następne kroki
QnA Maker zapewnia wszystko, co jest potrzebne do kompilowania i wdrażania niestandardowej bazy wiedzy oraz zarządzania nią.

> [!div class="nextstepaction"]
> [Zapoznaj się z najnowszymi zmianami](../whats-new.md)
