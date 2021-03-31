---
title: Umiarkowane obrazy z listami niestandardowymi i konsolą interfejsu API Content Moderator
titleSuffix: Azure Content Moderator
description: Do tworzenia niestandardowych list obrazów służy interfejs API zarządzania listami w usłudze Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 0035d367017c92bd151c27e14d744ef41eace069
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800152"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Umiarkowane z listami obrazów niestandardowych w konsoli interfejsu API

Do tworzenia niestandardowych list obrazów służy [interfejs API zarządzania listami](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) w usłudze Azure Content Moderator. Użyj niestandardowych list obrazów z interfejsem API moderowania obrazu. Operacja moderowania obrazu szacuje obraz. W przypadku tworzenia list niestandardowych, operacja również porównuje ją z obrazami na listach niestandardowych. Możesz użyć list niestandardowych do blokowania lub zezwalania na obraz.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list obrazów**, a poszczególne listy **nie mogą przekraczać 10 000 obrazów**.
>

Za pomocą interfejsu API zarządzania listami wykonaj następujące zadania:

- Tworzenie listy.
- Dodaj obrazy do listy.
- Obrazy ekranu dla obrazów znajdujących się na liście.
- Usuń obrazy z listy.
- Usuwanie listy.
- Edycja informacji na liście.
- Odświeżanie indeksu, aby zmiany na liście były uwzględnione w nowym skanowaniu.

## <a name="use-the-api-console"></a>Korzystanie z konsoli interfejsu API
Aby można było przetestować interfejs API w konsoli online, musisz mieć swój klucz subskrypcji. Ta lokalizacja znajduje się na karcie **Ustawienia** w polu **OCP-APIM-Subscription-Key** . Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="refresh-search-index"></a>Odśwież indeks wyszukiwania

Po wprowadzeniu zmian na liście obrazów należy odświeżyć jej indeks, aby zmiany zostały uwzględnione w przyszłych skanowaniach. Ten krok przypomina, jak aparat wyszukiwania na pulpicie (jeśli jest włączony) lub aparat wyszukiwania w sieci Web ciągle odświeża swój indeks, aby uwzględnić nowe pliki lub strony.

1. Na stronie [Dokumentacja interfejsu API zarządzania listami obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)wybierz pozycję **listy obrazów**, a następnie wybierz pozycję **Odśwież indeks wyszukiwania**.

   **Lista obrazów — zostanie otwarta strona indeks wyszukiwania odświeżania** .

2. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację. 
 
    ![Listy obrazów — odświeżanie zaznaczenia obszaru strony indeksu wyszukiwania](images/test-drive-region.png)

    **Lista obrazów —** otwiera konsolę interfejsu API indeksu wyszukiwania.

3. W polu **listId** wprowadź identyfikator listy. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

   ![Listy obrazów — pole zawartości odpowiedzi konsoli indeksu wyszukiwania](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Tworzenie listy obrazów

1. Przejdź do [dokumentacji interfejsu API zarządzania listami obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   **Lista obrazów —** zostanie otwarta strona tworzenie. 

3. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację.

   ![Listy obrazów — wybór regionu strony](images/test-drive-region.png)

   **Lista obrazów —** otwiera konsolę interfejsu API.
 
4. W polu **OCP-APIM-Subscription-Key** wprowadź swój klucz subskrypcji.

5. W polu **treść żądania** wprowadź wartości w polach **Nazwa** (na przykład moja lista) i **Opis**.

   ![Listy obrazów — Utwórz nazwę i opis treści żądania konsoli](images/try-terms-list-create-1.png)

6. Użyj symboli zastępczych par klucz-wartość, aby przypisać do listy więcej metadanych opisowych.

    ```json
    {
        "Name": "MyExclusionList",
        "Description": "MyListDescription",
        "Metadata": 
        {
            "Category": "Competitors",
            "Type": "Exclude"
        }
    }
    ```

   Dodaj metadane listy jako pary klucz-wartość, a nie obrazy rzeczywiste.
 
7. Wybierz pozycję **Wyślij**. Twoja lista zostanie utworzona. Zwróć uwagę na wartość **identyfikatora** skojarzoną z nową listą. Ten identyfikator jest potrzebny dla innych funkcji zarządzania listami obrazów.

   ![Listy obrazów — pole wyboru Utwórz zawartość odpowiedzi konsoli pokazuje Identyfikator listy](images/try-terms-list-create-2.png)
 
8. Następnie Dodaj obrazy do listy. W menu po lewej stronie wybierz pozycję **obraz**, a następnie wybierz pozycję **Dodaj obraz**.

   Zostanie otwarta strona **Dodawanie obrazu** . 

9. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację.

   ![Obraz — Dodawanie obszaru strony obrazu](images/test-drive-region.png)

   Zostanie otwarta konsola interfejsu API **dodawania** obrazu.
 
10. W polu **listId** wprowadź wygenerowany identyfikator listy, a następnie wprowadź adres URL obrazu, który chcesz dodać. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

11. Aby sprawdzić, czy obraz został dodany do listy, w menu po lewej stronie wybierz pozycję **obraz**, a następnie wybierz pozycję **Pobierz wszystkie identyfikatory obrazu**.

    **Obraz — pobiera wszystkie identyfikatory obrazów** konsola interfejsu API zostanie otwarta.
  
12. W polu **listId** wprowadź identyfikator listy, a następnie wprowadź swój klucz subskrypcji. Wybierz pozycję **Wyślij**.

    ![Obraz — Pobierz wszystkie identyfikatory obrazów pole zawartości odpowiedzi konsoli zawiera listę wprowadzonych obrazów](images/try-image-list-create-11.png)
 
10. Dodaj kilka obrazów. Teraz, gdy utworzono niestandardową listę obrazów, wypróbuj jej [ocenę](try-image-api.md) przy użyciu listy obrazów niestandardowych. 

## <a name="delete-images-and-lists"></a>Usuń obrazy i listy

Usuwanie obrazu lub listy jest proste. Za pomocą interfejsu API można wykonać następujące zadania:

- Usuwanie obrazu. (**Usuwanie obrazu**)
- Usuń wszystkie obrazy z listy bez usuwania listy. (**Obraz-Usuń wszystkie obrazy**)
- Usuwanie listy i całej jej zawartości. (**Listy obrazów — usuwanie**)

Ten przykład usuwa pojedynczy obraz:

1. Na stronie [Dokumentacja interfejsu API zarządzania listami obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)wybierz pozycję **obraz**, a następnie wybierz pozycję **Usuń**. 

   Zostanie otwarta strona **Usuwanie obrazu** .

2. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację. 

   ![Obraz — usuwanie zaznaczenia obszaru strony](images/test-drive-region.png)
 
   Zostanie otwarta konsola interfejsu API **usuwania obrazu** .
 
3. W polu **listId** wprowadź identyfikator listy, z której ma zostać usunięty obraz.  Jest to liczba zwracana w **obrazie — Uzyskaj wszystkie identyfikatory obrazów** konsoli dla elementu moje listy. Następnie wprowadź **ImageId** obrazu do usunięcia. 

W naszym przykładzie IDENTYFIKATORem listy jest **58953**, wartość dla **ContentSource**. Identyfikator obrazu to **59021**, wartość dla **ContentIds**.

1. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

1. Aby sprawdzić, czy obraz został usunięty, użyj **obrazu — Pobierz wszystkie identyfikatory obrazów** konsoli.
 
## <a name="change-list-information"></a>Zmień informacje o liście

Można edytować nazwę i opis listy oraz dodawać elementy metadanych.

1. W temacie [Informacje o interfejsie API zarządzania listami obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)w menu po lewej stronie wybierz pozycję **listy obrazów**, a następnie wybierz pozycję **Aktualizuj szczegóły**. 

   **Lista obrazów —** zostanie otwarta strona Szczegóły aktualizacji.

2. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację.  

    ![Listy obrazów — Aktualizuj wybór obszaru strony szczegółów](images/test-drive-region.png)

    **Lista obrazów —** konsola interfejsu API Szczegóły aktualizacji zostanie otwarta.
 
3. W polu **listId** wprowadź identyfikator listy, a następnie wprowadź swój klucz subskrypcji.

4. Wprowadź zmiany w polu **treść żądania** , a następnie wybierz przycisk **Wyślij** na stronie.

   ![Listy obrazów — zmiany treści żądania konsoli szczegółów aktualizacji](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub Zacznij od obrazu, który [zawiera program .NET szybkiego](image-lists-quickstart-dotnet.md) startu, aby zintegrować z aplikacją.
