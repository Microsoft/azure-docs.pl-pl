---
title: Importuj kroki aplikacji
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422752"
---
1. W [portalu Luis w wersji zapoznawczej](https://preview.luis.ai)na stronie **Moje aplikacje** wybierz pozycję **+ Nowa aplikacja do konwersacji**, a następnie **zaimportuj jako plik JSON**. Znajdź zapisany plik JSON z poprzedniego kroku. Nie musisz zmieniać nazwy aplikacji. Wybierz pozycję **gotowe**

1. W sekcji **Zarządzanie** na karcie **wersje** wybierz `0.1` wersję, a następnie wybierz pozycję **Klonuj** , aby sklonować wersję, a następnie nadaj jej nową nazwę 10 znaków `ml-entity`, a następnie wybierz pozycję **gotowe** , aby zakończyć proces klonowania. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

    > [!TIP]
    > Klonowanie do nowej wersji jest najlepszym rozwiązaniem Przed zmodyfikowaniem aplikacji. Po zakończeniu zmiany wersji wyeksportuj wersję (plik JSON lub Lu) i sprawdź plik w systemie kontroli źródła.

1. Wybierz **Build** opcję Kompiluj **, a następnie zapoznaj się z** intencjami, głównymi blokami konstrukcyjnymi aplikacji Luis.

    ![Przejdź do strony wersje na stronie intencje.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)