---
title: Dodawanie Chit-Chat do bazy wiedzy QnA Maker
titleSuffix: Azure Cognitive Services
description: Dodanie osobistego Chit-Chat do bot sprawia, że jest to bardziej atrakcyjny i interesujący podczas tworzenia bazy wiedzy. QnA Maker pozwala łatwo dodać wstępnie wypełniony zestaw pierwszych Chit-Chat do bazy wiedzy.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 1208d95a282ca6e236d9d6be8013f51dead90d13
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376654"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Dodawanie Chit-Chat do bazy wiedzy

Dodanie Chit-Chat do bot sprawia, że jest to bardziej atrakcyjny i interesujący. Funkcja Chit-Chat w programie QnA Maker pozwala łatwo dodać wstępnie wypełniony zestaw najważniejszych Chit-Chat do bazy wiedzy (KB). Może to być punkt początkowy dla osobowości Botu. pozwoli to zaoszczędzić czas i koszt ich zapisania od zera.

Ten zestaw danych zawiera około 100 scenariuszy Chit-Chat w głosowaniu wielu osób, takich jak Professional, friendly i Witty. Wybierz osobę, która najlepiej przypomina głos Twojego Botu. Po otrzymaniu zapytania użytkownika QnA Maker próbuje dopasować go do najbliższej znanej Chit-Chat QnA.

Poniżej przedstawiono przykłady różnych elementów osobistych. Możesz zobaczyć wszystkie osobowe [zestawy danych](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) wraz ze szczegółami osobistymi.

Dla kwerendy użytkownika `When is your birthday?` , każda osobowość ma przydzielną odpowiedź:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Osobowość|Przykład|
|--|--|
|Professional Edition|Wiek nie dotyczy mnie.|
|Wyświetlana|Nie mam już wieku.|
|Witty|Jest bezpłatny okres ważności.|
|Caring|Nie mam wieku.|
|R|Jestem bot, więc nie mam wieku.|
||


## <a name="language-support"></a>Obsługa języków

Chit — zestawy danych czatu są obsługiwane w następujących językach:

|Język|
|--|
|Chiński|
|Angielski|
|Francuski|
|Niemcy|
|Włoski|
|japoński|
|Koreański|
|Portugalski|
|Hiszpański|


## <a name="add-chit-chat-during-kb-creation"></a>Dodaj Chit-Chat podczas tworzenia bazy wiedzy
Podczas tworzenia bazy wiedzy po dodaniu źródłowych adresów URL i plików dostępna jest opcja dodawania Chit-Chat. Wybierz osobowość, która ma być podstawą Chit — rozmowy. Jeśli nie chcesz dodawać Chit-Chat lub jeśli masz już obsługę rozmowy przez Chit w źródłach danych, wybierz opcję **Brak**.

## <a name="add-chit-chat-to-an-existing-kb"></a>Dodawanie Chit-Chat do istniejącej bazy wiedzy
Wybierz swoją KB i przejdź do strony **ustawień** . Istnieje link do wszystkich zestawów danych Chit-Chat w odpowiednim formacie **TSV** . Pobierz żądaną osobowość, a następnie przekaż ją jako źródło pliku. Nie należy edytować formatu ani metadanych podczas pobierania i przekazywania pliku.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

![Dodaj Chit-Chat do istniejącej bazy wiedzy](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

![Dodaj Chit — czat do istniejącej wersji zapoznawczej KB](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset-v2.png)

---

## <a name="edit-your-chit-chat-questions-and-answers"></a>Edytuj swoje pytania i odpowiedzi dotyczące rozmowy Chit
Gdy edytujesz swoją KB, zobaczysz nowe źródło dla Chit-Chat w oparciu o wybraną osobowość. Możesz teraz dodawać zmienione pytania lub edytować odpowiedzi, podobnie jak w przypadku innych źródeł.

![Edytuj Chit — czat bazami](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Aby wyświetlić metadane, wybierz pozycję **Wyświetl opcje** na pasku narzędzi, a następnie wybierz pozycję **Pokaż metadane**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Dodawanie dodatkowych pytań i odpowiedzi Chit — rozmowy
Można dodać nową parę Chit-Chat QnA, która nie znajduje się w wstępnie zdefiniowanym zestawie danych. Upewnij się, że nie duplikujesz pary QnA, która jest już pokryte w zestawie Chit-Chat. Po dodaniu nowego Chit-Chat QnA jest on dodawany do źródła **redakcyjnego** . Aby upewnić się, że program rangi rozumie, że jest to chit-chat, Dodaj parę klucz/wartość metadanych "Redakcja: chitchat", jak pokazano na poniższej ilustracji:

:::image type="content" source="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png" alt-text="Dodaj Chit-Chat bazami" lightbox="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png":::

## <a name="delete-chit-chat-from-an-existing-kb"></a>Usuń Chit — Czat z istniejącej bazy wiedzy
Wybierz swoją KB i przejdź do strony **ustawień** . Określone źródło rozmowy Chit jest wyświetlane jako plik z wybraną nazwą osobowości. Można go usunąć jako plik źródłowy.

![Usuń Chit — Czat z bazy wiedzy](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Zobacz też

[Omówienie usługi QnA Maker](../Overview/overview.md)