---
title: Co to jest usługa QnA Maker?
description: QnA Maker to oparta na chmurze usługa NLP, która w prosty sposób tworzy naturalną komunikację między danymi. Można go użyć, aby znaleźć najbardziej odpowiednią odpowiedź dla danego danych wejściowych języka naturalnego, z niestandardowej bazy wiedzy (KB).
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 05/26/2020
ms.custom: cog-serv-seo-aug-2020
keywords: QNA Maker, niska Code Chat botów, wielostopniowe konwersacje
ms.openlocfilehash: 6a5ea51086e3ab532966c9cea9eb866334494bba
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874428"
---
# <a name="what-is-qna-maker"></a>Co jest usługa QnA Maker?

QnA Maker to oparta na chmurze usługa przetwarzania języka naturalnego (NLP), która pozwala na utworzenie naturalnej warstwy konwersacji danych. Służy do znajdowania najbardziej odpowiedniej odpowiedzi dla wszelkich danych wejściowych z niestandardowej bazy wiedzy (KB).

QnA Maker jest często używany do tworzenia konwersacji aplikacji klienckich, takich jak aplikacje multimedialne społeczności, botów rozmowy i aplikacje komputerowe z obsługą mowy.

## <a name="when-to-use-qna-maker"></a>Kiedy używać QnA Maker

* **Jeśli masz informacje statyczne** — Użyj QNA Maker, jeśli masz informacje statyczne w bazie wiedzy o odpowiedziach. Ta baza wiedzy jest niestandardowa w Twoich potrzebach, które zostały skompilowane przy użyciu dokumentów, takich jak [pliki PDF i adresy URL](../concepts/content-types.md).
* **Jeśli chcesz podać tę samą odpowiedź na żądanie, pytanie lub polecenie** — gdy różne użytkowników przesyłają te same pytania, zwracana jest ta sama odpowiedź.
* **Gdy chcesz filtrować informacje statyczne w oparciu o meta-informacje** — Dodaj Tagi [metadanych](../how-to/metadata-generateanswer-usage.md) , aby zapewnić dodatkowe opcje filtrowania odpowiednie dla użytkowników aplikacji klienckiej i informacje. Typowe informacje o metadanych obejmują [Chit — rozmowę](../how-to/chit-chat-knowledge-base.md), typ zawartości lub format, cel zawartości i świeżość zawartości.
* **Gdy chcesz zarządzać konwersacją bot zawierającą informacje statyczne** — baza wiedzy Pobiera tekst lub polecenie konwersacji użytkownika i odpowiada na nie. Jeśli odpowiedź jest częścią wstępnie określonego przepływu konwersacji, reprezentowanej w bazie wiedzy z [kontekstem wielodostępnym](../how-to/multiturn-conversation.md), bot może łatwo udostępnić ten przepływ.

## <a name="what-is-a-knowledge-base"></a>Co to jest baza wiedzy?

QnA Maker [importuje zawartość](../concepts/knowledge-base.md) do bazy wiedzy o parach pytań i odpowiedzi. Proces importowania wyodrębnia informacje o relacji między częściami zawartości strukturalnej i częściowo strukturalnej, aby implikuje relacje między parami pytania i odpowiedzi. Można edytować te pary pytań i odpowiedzi lub dodawać nowe pary.

Zawartość pary pytań i odpowiedzi obejmuje:
* Wszystkie alternatywne formy pytania
* Tagi metadanych używane do filtrowania opcji odpowiedzi podczas wyszukiwania
* Monity do kontynuowania uściślania wyszukiwania

![Przykład pytania i odpowiedzi z metadanymi](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Po opublikowaniu bazy wiedzy aplikacja kliencka wysyła do punktu końcowego pytanie użytkownika. Usługa QnA Maker przetwarza pytanie i reaguje na najlepszą odpowiedź.

## <a name="create-a-chat-bot-programmatically"></a>Tworzenie bot rozmowy programowo

Po opublikowaniu QnA Maker bazy wiedzy aplikacja kliencka wysyła pytanie do punktu końcowego bazy wiedzy i otrzymuje wyniki jako odpowiedź JSON. Typowa aplikacja kliencka dla QnA Maker jest bot rozmowy.

![Poproś bot pytania i uzyskaj odpowiedzi z zawartości bazy wiedzy](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Krok|Akcja|
|:--|:--|
|1|Aplikacja kliencka wysyła _pytanie_ użytkownika (tekst we własnych słowach), "Jak mogę programowo zaktualizować moją bazę wiedzy?". do punktu końcowego bazy wiedzy.|
|2|QnA Maker korzysta z przeszkolonej bazy wiedzy, aby zapewnić poprawną odpowiedź i wszelkie monity monitujące, których można użyć do uściślenia wyszukiwania najlepszej odpowiedzi. QnA Maker zwraca odpowiedź sformatowaną w formacie JSON.|
|3|Aplikacja kliencka używa odpowiedzi JSON, aby podejmować decyzje dotyczące kontynuowania rozmowy. Te decyzje mogą obejmować wyświetlanie odpowiedzi z góry i przedstawienie większej liczby opcji w celu ograniczenia wyszukiwania najlepszej odpowiedzi. |
|||

## <a name="build-low-code-chat-bots"></a>Kompiluj botów rozmowy o niskim kodzie

Portal QnA Maker zapewnia pełną obsługę tworzenia bazy wiedzy. Możesz importować dokumenty w ich bieżącym formularzu do bazy wiedzy. Te dokumenty (takie jak często zadawane pytania, Podręcznik produktu, arkusz kalkulacyjny lub strona sieci Web) są konwertowane na pary pytań i odpowiedzi. Każda para jest analizowana pod kątem monitów kolejnych i podłączonych do innych par. Końcowy format _promocji_ obsługuje zaawansowaną prezentację, w tym obrazy i linki.

Po edytowaniu bazy wiedzy Opublikuj bazę wiedzy w działającej [aplikacji internetowej platformy Azure bot](https://azure.microsoft.com/services/bot-service/) bez konieczności pisania kodu. Przetestuj swoje bot w [Azure Portal](https://portal.azure.com) lub Pobierz i Kontynuuj programowanie.

## <a name="high-quality-responses-with-layered-ranking"></a>Odpowiedzi o wysokiej jakości z klasyfikacją warstwową

System QnA Maker jest podejściem z klasyfikacją warstwową. Dane są przechowywane w usłudze Azure Search, która również pełni rolę pierwszej warstwy klasyfikacyjnej. Najważniejsze wyniki z usługi Azure Search są następnie przenoszone QnA Maker przez NLPy model zmiany klasyfikacji, aby utworzyć końcowe wyniki i wynik pewności.

## <a name="multi-turn-conversations"></a>Konwersacje z obsługą wielodostępności

QnA Maker zapewnia wielodostępne i aktywne uczenie, aby pomóc w ulepszaniu podstawowych par pytań i odpowiedzi.

Polecenia z **obsługą wielodostępności** umożliwiają połączenie par pytań i odpowiedzi. To połączenie umożliwia aplikacji klienckiej dostarczenie odpowiedzi na największą odpowiedź i zawiera więcej pytań dotyczących wyszukiwania końcowej odpowiedzi.

Po otrzymaniu przez bazę wiedzy pytań od użytkowników w opublikowanym punkcie końcowym program QnA Maker stosuje **aktywną naukę** do tych rzeczywistych pytań, aby zasugerować zmiany w bazie wiedzy w celu poprawienia jakości.

## <a name="development-lifecycle"></a>Cykl projektowania oprogramowania

QnA Maker zapewnia tworzenie, uczenie i publikowanie wraz z uprawnieniami do współpracy w celu zintegrowania z pełnym cyklem życia.

> [!div class="mx-imgBorder"]
> ![Obraz przedstawiający koncepcję cyklu programowania](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="complete-a-quickstart"></a>Kończenie szybkiego startu

Oferujemy Przewodniki Szybki Start w najpopularniejszych językach programowania, z których każdy jest przeznaczony do uczenia podstawowych wzorców projektowych i uruchamiania kodu w mniej niż 10 minut. Zapoznaj się z poniższą listą przewodnika Szybki Start dla każdej funkcji.

* [Wprowadzenie do biblioteki klienta QnA Maker](../quickstarts/quickstart-sdk.md)
* [Rozpoczynanie pracy z portalem QnA Maker](../quickstarts/create-publish-knowledge-base.md)
* [Wprowadzenie do QnA Maker interfejsów API REST](../quickstarts/quickstart-rest-curl.md)


## <a name="next-steps"></a>Następne kroki
QnA Maker zapewnia wszystko, co jest potrzebne do kompilowania i wdrażania niestandardowej bazy wiedzy oraz zarządzania nią.

> [!div class="nextstepaction"]
> [Zapoznaj się z najnowszymi zmianami](../whats-new.md)
