---
title: Migrowanie baz wiedzy — QnA Maker
description: Migrowanie bazy wiedzy wymaga eksportowania z jednej bazy wiedzy, a następnie importowania do innej.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: c89ab375cb02824a08ff57e6b5278dd9299126ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350929"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrowanie bazy wiedzy przy użyciu eksportu i importu

Migracja jest procesem tworzenia nowej bazy wiedzy na podstawie istniejącej bazy wiedzy. Można to zrobić z kilku powodów:

* proces tworzenia kopii zapasowej i przywracania
* Potok ciągłej integracji/ciągłego dostarczania
* Przenieś regiony

Migrowanie bazy wiedzy wymaga eksportowania z istniejącej bazy wiedzy, a następnie importowania do innej.

> [!NOTE]
> Postępuj zgodnie z poniższymi instrukcjami, aby przeprowadzić migrację istniejącej bazy wiedzy do nowej QnA Maker zarządzanej (wersja zapoznawcza).

## <a name="prerequisites"></a>Wymagania wstępne

* Zanim rozpoczniesz, utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).
* Skonfiguruj nową [usługę QNA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrowanie bazy wiedzy z QnA Maker
1. Zaloguj się do [portalu QNA Maker](https://qnamaker.ai).
1. Wybierz bazę wiedzy źródłowej, którą chcesz zmigrować.

1. Na stronie **Ustawienia** wybierz pozycję **Eksportuj bazę wiedzy** , aby pobrać plik. tsv, który zawiera zawartość bazy wiedzy o pochodzeniu, odpowiedzi, metadane, monity monitujące oraz nazwy źródeł danych, z których zostały wyodrębnione. Identyfikatory QnA eksportowane z pytaniami i odpowiedziami mogą być używane do aktualizowania określonej pary QnA przy użyciu [interfejsu API aktualizacji](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). Identyfikator QnA dla określonej pary QnA pozostaje niezmieniony w wielu operacjach eksportu.

1. Wybierz pozycję **Utwórz bazę wiedzy** z górnego menu, a następnie Utwórz _pustą_ bazę wiedzy. Jest ona pusta, ponieważ podczas jej tworzenia nie będzie można dodawać żadnych adresów URL ani plików. Są one dodawane podczas kroku importowania po utworzeniu.

    Skonfiguruj bazę wiedzy. Ustaw tylko nową nazwę bazy wiedzy. Zduplikowane nazwy są obsługiwane, a znaki specjalne również są obsługiwane.

    Nie zaznaczaj niczego z kroku 4, ponieważ te wartości zostaną nadpisywane podczas importowania pliku.

1. W kroku 5 wybierz pozycję **Utwórz**.

1. W tej nowej bazie wiedzy Otwórz kartę **Ustawienia** , a następnie wybierz pozycję **Importuj bazę wiedzy**. Spowoduje to zaimportowanie pytań, odpowiedzi, metadanych, monitów, a następnie zachowanie nazw źródeł danych, z których zostały wyodrębnione. **Pary QNA utworzone w nowej bazie wiedzy mają taki sam identyfikator QNA jak obecny w wyeksportowanym pliku**. Dzięki temu można utworzyć dokładną replikę bazy wiedzy.

   > [!div class="mx-imgBorder"]
   > [![Importuj bazę wiedzy](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Przetestuj** nową bazę wiedzy przy użyciu panelu test. Dowiedz się, jak [przetestować bazę wiedzy](../How-To/test-knowledge-base.md).

1. **Opublikuj** bazę wiedzy i Utwórz czat bot. Dowiedz się, jak [opublikować bazę wiedzy](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Programowe Migrowanie bazy wiedzy z QnA Maker

Proces migracji jest programowo dostępny przy użyciu następujących interfejsów API REST:

**Eksportowanie**

* [Pobierz interfejs API bazy wiedzy](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**Importuj**

* [Zastąp interfejs API (Załaduj ponownie z tym samym IDENTYFIKATORem bazy wiedzy)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [Utwórz interfejs API (Załaduj z nowym IDENTYFIKATORem bazy wiedzy)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Dzienniki i zmiany rozmowy
Zmiany bez uwzględniania wielkości liter (synonimy) nie są importowane automatycznie. Użyj [interfejsów API v4](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) , aby przenieść zmiany w nowej bazie wiedzy.

Nie ma możliwości migrowania dzienników rozmowy, ponieważ nowa baza wiedzy używa Application Insights do przechowywania dzienników rozmowy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Edytowanie bazy wiedzy](../How-To/edit-knowledge-base.md)