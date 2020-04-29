---
title: Dodawanie Chit-Chat do bazy wiedzy QnA Maker
titleSuffix: Azure Cognitive Services
description: Dodanie osobistego Chit-Chat do bot sprawia, że jest to bardziej atrakcyjny i interesujący podczas tworzenia bazy wiedzy. QnA Maker pozwala łatwo dodać wstępnie wypełniony zestaw pierwszych Chit-Chat do bazy wiedzy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220711"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Dodawanie Chit-Chat do bazy wiedzy

Dodanie Chit-Chat do bot sprawia, że jest to bardziej atrakcyjny i interesujący. Funkcja Chit-Chat w programie QnA Maker pozwala łatwo dodać wstępnie wypełniony zestaw najważniejszych Chit-Chat do bazy wiedzy (KB). Może to być punkt początkowy dla osobowości Botu. pozwoli to zaoszczędzić czas i koszt ich zapisania od zera.  

Ten zestaw danych zawiera około 100 scenariuszy Chit-Chat w głosowaniu wielu osób, takich jak Professional, friendly i Witty. Wybierz osobę, która najlepiej przypomina głos Twojego Botu. Po otrzymaniu zapytania użytkownika QnA Maker próbuje dopasować go do najbliższej znanej Chit-Chat QnA.  

Poniżej przedstawiono przykłady różnych elementów osobistych. Możesz zobaczyć wszystkie osobowe [zestawy danych](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) wraz ze szczegółami osobistymi.

Dla kwerendy użytkownika `When is your birthday?`, każda osobowość ma przydzielną odpowiedź:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Osobowości|Przykład|
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
|koreański|
|Portugalski|
|Hiszpański|


## <a name="add-chit-chat-during-kb-creation"></a>Dodaj Chit-Chat podczas tworzenia bazy wiedzy
Podczas tworzenia bazy wiedzy po dodaniu źródłowych adresów URL i plików dostępna jest opcja dodawania Chit-Chat. Wybierz osobowość, która ma być podstawą Chit — rozmowy. Jeśli nie chcesz dodawać Chit-Chat lub jeśli masz już obsługę rozmowy przez Chit w źródłach danych, wybierz opcję **Brak**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Dodawanie Chit-Chat do istniejącej bazy wiedzy
Wybierz swoją KB i przejdź do strony **ustawień** . Istnieje link do wszystkich zestawów danych Chit-Chat w odpowiednim formacie **TSV** . Pobierz żądaną osobowość, a następnie przekaż ją jako źródło pliku. Nie należy edytować formatu ani metadanych podczas pobierania i przekazywania pliku. 
  
![Dodaj Chit-Chat do istniejącej bazy wiedzy](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Edytuj swoje pytania i odpowiedzi dotyczące rozmowy Chit
Gdy edytujesz swoją KB, zobaczysz nowe źródło dla Chit-Chat w oparciu o wybraną osobowość. Możesz teraz dodawać zmienione pytania lub edytować odpowiedzi, podobnie jak w przypadku innych źródeł. 

![Edytuj Chit — czat bazami](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Aby wyświetlić metadane, wybierz pozycję **Wyświetl opcje** na pasku narzędzi, a następnie wybierz pozycję **Pokaż metadane**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Dodawanie dodatkowych pytań i odpowiedzi Chit — rozmowy
Można dodać nowy Chit-Chat QnA, który nie znajduje się we wstępnie zdefiniowanym zestawie. Upewnij się, że nie duplikujesz pary QnA, która jest już pokryte w zestawie Chit-Chat. Po dodaniu nowego Chit-Chat QnA jest on dodawany do źródła **redakcyjnego** . Aby upewnić się, że program rangi rozumie, że jest to chit-chat, Dodaj parę klucz/wartość metadanych "Redakcja: chitchat", jak pokazano na poniższej ilustracji:
   
![! [Dodaj Chit-Chat bazami] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Usuń Chit — Czat z istniejącej bazy wiedzy
Wybierz swoją KB i przejdź do strony **ustawień** . Określone źródło rozmowy Chit jest wyświetlane jako plik z wybraną nazwą osobowości. Można go usunąć jako plik źródłowy.

![Usuń Chit — Czat z bazy wiedzy](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Zobacz także 

[Omówienie usługi QnA Maker](../Overview/overview.md)
