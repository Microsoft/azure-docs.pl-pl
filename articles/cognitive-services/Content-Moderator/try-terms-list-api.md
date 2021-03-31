---
title: Umiarkowany tekst z niestandardowymi listami terminów — Content Moderator
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API zarządzania listami, aby utworzyć niestandardowe listy terminów do użycia z interfejsem API moderowania tekstu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: f1dfe88741a304da92901d3997c746654336ef54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800044"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Umiarkowane z niestandardowymi listami terminów w konsoli interfejsu API

Domyślna globalna lista terminów w usługach Azure Content Moderator wystarcza w przypadku większości potrzeb moderowania zawartości. Jednak może być konieczne sprawdzanie terminów, które są specyficzne dla organizacji. Na przykład warto oznaczyć tagiem nazwy konkurencji w celu dalszego przeglądu. 

Użyj [interfejsu API zarządzania listami](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) , aby utworzyć niestandardowe listy terminów do użycia z interfejsem API moderowania tekstu. Operacja **ekranu tekstowego** skanuje tekst pod kątem niewulgarności, a także porównuje tekst z niestandardowym i udostępnionym listy blokowania adresów.

> [!NOTE]
> Istnieje maksymalny limit wynoszący **5 list terminów**, a poszczególne listy **nie mogą przekraczać 10 000 terminów**.
>

Aby wykonać następujące zadania, można użyć interfejsu API zarządzania listami:
- Tworzenie listy.
- Dodawanie terminów do listy.
- Sprawdzanie terminów względem terminów na liście.
- Usuwanie terminów z listy.
- Usuwanie listy.
- Edycja informacji na liście.
- Odświeżanie indeksu, aby zmiany na liście były uwzględnione w nowym skanowaniu.

## <a name="use-the-api-console"></a>Korzystanie z konsoli interfejsu API

Aby można było przetestować interfejs API w konsoli online, musisz mieć swój klucz subskrypcji. Ten klucz znajduje się na karcie **Ustawienia** w polu **OCP-APIM-Subscription-Key** . Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="refresh-search-index"></a>Odśwież indeks wyszukiwania

Po wprowadzeniu zmian w liście terminów należy odświeżyć jej indeks, aby zmiany zostały uwzględnione w przyszłych skanowaniach. Ten krok przypomina, jak aparat wyszukiwania na pulpicie (jeśli jest włączony) lub aparat wyszukiwania w sieci Web ciągle odświeża swój indeks, aby uwzględnić nowe pliki lub strony.

1. W temacie [Informacje o interfejsie API zarządzania listami terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)w menu po lewej stronie wybierz pozycję **listy terminów**, a następnie wybierz pozycję **Odśwież indeks wyszukiwania**. 

   **Lista terminów — zostanie otwarta strona indeks wyszukiwania odświeżania** .

2. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację. 

   ![Listy terminów — odświeżanie zaznaczenia obszaru strony indeksu wyszukiwania](images/test-drive-region.png)

   **Lista terminów —** otwiera konsolę interfejsu API indeksu wyszukiwania.

3. W polu **listId** wprowadź identyfikator listy. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

   ![Interfejs API — Lista terminów — pole zawartości odpowiedzi konsoli indeksu wyszukiwania](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Tworzenie listy terminów
1. Przejdź do [dokumentacji interfejsu API zarządzania listami terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   **Lista terminów —** zostanie otwarta strona Tworzenie strony.

2. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację. 

   ![Listy terminów — wybór regionu strony](images/test-drive-region.png)

   **Lista terminów —** otwiera konsolę interfejsu API.
 
3. W polu **OCP-APIM-Subscription-Key** wprowadź swój klucz subskrypcji.

4. W polu **treść żądania** wprowadź wartości w polach **Nazwa** (na przykład moja lista) i **Opis**.

   ![Listy terminów — Utwórz nazwę i opis treści żądania konsoli](images/try-terms-list-create-1.png)

5. Użyj symboli zastępczych par klucz-wartość, aby przypisać do listy więcej metadanych opisowych.

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

   Dodaj metadane listy jako pary klucz-wartość, a nie rzeczywiste warunki.
 
6. Wybierz pozycję **Wyślij**. Twoja lista zostanie utworzona. Zwróć uwagę na wartość **identyfikatora** skojarzoną z nową listą. Ten identyfikator jest potrzebny dla innych funkcji zarządzania listą okresów.

   ![Listy terminów — pole wyboru Utwórz zawartość odpowiedzi konsoli pokazuje Identyfikator listy](images/try-terms-list-create-2.png)
 
7. Dodaj warunki do listy Moje. W menu po lewej stronie w obszarze **termin** wybierz pozycję **Dodaj termin**. 

   Zostanie otwarta strona **termin — Dodawanie terminu** . 

8. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację. 

   ![Termin — Dodaj region strony terminu](images/test-drive-region.png)

   Zostanie otwarta konsola interfejsu API **warunku Dodawanie terminu** .
 
9. W polu **listId** wprowadź wygenerowany identyfikator listy i wybierz wartość dla **języka**. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

   ![Termin — Dodawanie parametrów zapytania konsoli warunkowej](images/try-terms-list-create-3.png)
 
10. Aby sprawdzić, czy termin został dodany do listy, w menu po lewej stronie wybierz pozycję **termin**, a następnie wybierz pozycję **Pobierz wszystkie warunki**. 

    Termin — zostanie otwarta konsola interfejsu API **pobierania wszystkich warunków** .

11. W polu **listId** wprowadź identyfikator listy, a następnie wprowadź swój klucz subskrypcji. Wybierz pozycję **Wyślij**.

12. W polu **zawartość odpowiedzi** Sprawdź wprowadzone warunki.

    ![Termin — Pobierz wszystkie warunki pole zawartości odpowiedzi konsoli zawiera listę wprowadzonych terminów](images/try-terms-list-create-4.png)
 
13. Dodaj kilka dodatkowych warunków. Teraz, gdy utworzono niestandardową listę warunków, spróbuj [zeskanować jakiś tekst](try-text-api.md) przy użyciu listy warunków niestandardowych. 

## <a name="delete-terms-and-lists"></a>Usuwanie terminów i list

Usuwanie terminu lub listy jest bardzo proste. Użyj interfejsu API, aby wykonać następujące zadania:

- Usuwanie terminu. (**Termin — usuwanie**)
- Usuwanie wszystkich terminów z listy, bez usuwania listy. (**Term. Usuń wszystkie warunki**)
- Usuwanie listy i całej jej zawartości. (**Listy terminów — usuwanie**)

Ten przykład usuwa pojedynczy termin.

1. W temacie [Informacje o interfejsie API zarządzania listami terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)w menu po lewej stronie wybierz pozycję **termin**, a następnie wybierz pozycję **Usuń**. 

   Zostanie otwarty **termin usuwania** .

2. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację. 

   ![Termin — Usuwanie obszaru strony](images/test-drive-region.png)

   Zostanie otwarta konsola interfejsu API **usuwania** .
  
3. W polu **listId** wprowadź identyfikator listy, z której chcesz usunąć termin. Ten identyfikator jest liczbą (w naszym przykładzie **122**), która jest zwracana z **listy terminów-Get Details** Console. Wprowadź termin i wybierz język.
 
   ![Termin — Usuwanie parametrów zapytania konsoli](images/try-terms-list-delete-1.png)

4. Wprowadź klucz subskrypcji, a następnie wybierz pozycję **Wyślij**.

5. Aby sprawdzić, czy termin został usunięty, użyj **list terminów — Pobierz wszystkie** konsole.

   ![Listy terminów — pole wyboru Pobierz wszystkie informacje dotyczące odpowiedzi konsoli pokazuje, że termin jest usuwany](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Zmień informacje o liście

Można edytować nazwę i opis listy oraz dodawać elementy metadanych.

1. W temacie [Informacje o interfejsie API zarządzania listami terminów](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)w menu po lewej stronie wybierz pozycję **listy terminów**, a następnie wybierz pozycję **Aktualizuj szczegóły**. 

   **Lista terminów —** zostanie otwarta strona Szczegóły aktualizacji.

2. W obszarze **Otwórz konsolę testowania interfejsu API** wybierz region, który najlepiej opisuje lokalizację. 

   ![Listy terminów — wybór obszaru strony szczegółów aktualizacji](images/test-drive-region.png)

   **Lista terminów —** zostanie otwarta konsola interfejsu API Szczegóły aktualizacji.

3. W polu **listId** wprowadź identyfikator listy, a następnie wprowadź swój klucz subskrypcji.

4. Wprowadź zmiany w polu **treść żądania** , a następnie wybierz pozycję **Wyślij**.

   ![Listy terminów — zmiany treści żądania konsoli szczegółów aktualizacji](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub Rozpocznij od terminu na [platformie .NET — szybki](term-lists-quickstart-dotnet.md) Start, aby zintegrować z aplikacją.
