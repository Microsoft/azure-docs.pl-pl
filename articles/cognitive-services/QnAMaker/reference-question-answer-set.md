---
title: Baza wiedzy o projekcie QnA Maker
description: Baza wiedzy QnA Maker składa się z zestawu par pytań i odpowiedzi (QnA) oraz opcjonalnych metadanych skojarzonych z poszczególnymi parami QnA.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: ef5476ade205109f5dfede1b3bb2c3a4ae2e81a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94506009"
---
# <a name="question-and-answer-pair"></a>Para pytań i odpowiedzi

Baza wiedzy składa się z par pytań i odpowiedzi (QnA).  Każda para ma jedną odpowiedź, a para zawiera wszystkie informacje skojarzone z tą _odpowiedzią_. Odpowiedź może być luźno podobna do wiersza bazy danych lub wystąpienia struktury danych.

## <a name="question-and-answer-pairs"></a>Pary pytań i odpowiedzi

**Wymagane** ustawienia w parze pytań i odpowiedzi (QNA) są następujące:

* **pytanie** — tekst zapytania użytkownika, używany do QNA Maker uczenia maszynowego w celu wyrównania z tekstem pytania użytkownika z różnymi wyrazami, ale z tą samą odpowiedzią
* odpowiedź **— odpowiedź** pary jest odpowiedzią zwracaną, gdy zapytanie użytkownika jest dopasowane do skojarzonego pytania

Każda para jest reprezentowana przez **Identyfikator**.

**Opcjonalne** ustawienia dla pary obejmują:

* **Alternatywne formy pytania** — pomaga QNA Maker zwrócić poprawną odpowiedź w celu uzyskania większej liczby pytań frazy
* **Metadane**: metadane są tagami skojarzonymi z parą QNA i są reprezentowane jako pary klucz-wartość. Tagi metadanych są używane do filtrowania par QnA i ograniczają zestaw, w jakim jest wykonywane Dopasowywanie zapytań.
* Wyświetlanie wielowierszowych **ekranów**, służących do kontynuowania konwersacji z obsługą wielodostępności

![QnA Maker bazy wiedzy](media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Dodaj redakcyjnie do bazy wiedzy

Jeśli nie masz wstępnie istniejącej zawartości do zapełnienia bazy wiedzy, możesz dodać pary QnA w portalu QnA Maker. Dowiedz się [tutaj](How-To/edit-knowledge-base.md), jak zaktualizować bazę wiedzy.

## <a name="editing-your-knowledge-base-locally"></a>Lokalne edytowanie bazy wiedzy

Po utworzeniu bazy wiedzy zaleca się dokonanie edycji tekstu bazy wiedzy w [portalu QNA Maker](https://qnamaker.ai), a nie eksportowanie i ponowne importowanie za pomocą plików lokalnych. Mogą jednak wystąpić sytuacje, w których należy edytować bazę wiedzy lokalnie.

Wyeksportuj bazę wiedzy ze strony **Ustawienia** , a następnie przeprowadź edycję bazy wiedzy przy użyciu programu Microsoft Excel. Jeśli zdecydujesz się użyć innej aplikacji do edytowania wyeksportowanego pliku, aplikacja może wprowadzić błędy składniowe, ponieważ nie jest w pełni zgodna z TSV. Pliki TSV programu Microsoft Excel zwykle nie wprowadzają żadnych błędów formatowania.

Po dokonaniu edycji ponownie zaimportuj plik TSV ze strony **ustawień** . Spowoduje to całkowite zastąpienie bieżącej bazy wiedzy zaimportowaną bazą wiedzy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Cykl życia bazy wiedzy w QnA Maker](Concepts/development-lifecycle-knowledge-base.md)
