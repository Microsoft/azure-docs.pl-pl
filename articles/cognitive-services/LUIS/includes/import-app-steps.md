---
title: Importuj kroki aplikacji
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130760"
---
1. W [portalu Luis](https://www.luis.ai)na stronie **Moje aplikacje** wybierz pozycję **+ Nowa aplikacja do konwersacji** , a następnie **zaimportuj jako plik JSON** . Znajdź zapisany plik JSON z poprzedniego kroku. Nie musisz zmieniać nazwy aplikacji. Wybierz pozycję **gotowe**

1. W sekcji **Zarządzanie** na karcie **wersje** wybierz `0.1` wersję, a następnie wybierz pozycję **Klonuj** , aby sklonować wersję, i nadaj jej nową nazwę `ml-entity` , a następnie wybierz pozycję **gotowe** , aby zakończyć proces klonowania. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

    > [!TIP]
    > Klonowanie do nowej wersji jest najlepszym rozwiązaniem Przed zmodyfikowaniem aplikacji. Po zakończeniu zmiany wersji wyeksportuj wersję (plik JSON lub Lu) i sprawdź plik w systemie kontroli źródła.

1. Wybierz opcję **Kompiluj** w górnej części ekranu, a następnie kliknij pozycję **intencje** w menu nawigacji po lewej stronie. Zobaczysz intencje aplikacji, które są głównymi blokami konstrukcyjnymi aplikacji LUIS.
