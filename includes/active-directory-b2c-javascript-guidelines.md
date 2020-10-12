---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78185843"
---
## <a name="guidelines-for-using-javascript"></a>Wskazówki dotyczące korzystania z języka JavaScript

Postępuj zgodnie z poniższymi wskazówkami, gdy dostosowujesz interfejs aplikacji przy użyciu języka JavaScript:

- Nie należy wiązać zdarzenia kliknięcia dla `<a>` elementów HTML.
- Nie przyjmujej zależności od kodu Azure AD B2C ani komentarzy.
- Nie zmieniaj kolejności ani hierarchii Azure AD B2C elementów HTML. Użyj zasad Azure AD B2C, aby kontrolować kolejność elementów interfejsu użytkownika.
- Można wywołać dowolną usługę RESTful z następującymi kwestiami:
    - Może być konieczne ustawienie CORS usługi RESTful, aby zezwalać na wywołania HTTP po stronie klienta.
    - Upewnij się, że usługa RESTful jest zabezpieczona i używa tylko protokołu HTTPS.
    - Nie używaj kodu JavaScript bezpośrednio, aby wywoływać Azure AD B2C punkty końcowe.
- Możesz osadzić kod JavaScript lub połączyć się z zewnętrznymi plikami JavaScript. W przypadku korzystania z zewnętrznego pliku JavaScript upewnij się, że używasz bezwzględnego adresu URL, a nie względnego adresu URL.
- Struktury JavaScript:
    - Azure AD B2C używa określonej wersji platformy jQuery. Nie dołączaj innej wersji jQuery. Użycie więcej niż jednej wersji na tej samej stronie powoduje problemy.
    - Korzystanie z RequireJS nie jest obsługiwane.
    - Większość platform języka JavaScript nie jest obsługiwana przez Azure AD B2C.
- Ustawienia Azure AD B2C mogą być odczytywane przez wywoływanie `window.SETTINGS` , `window.CONTENT` obiektów, takich jak bieżący język interfejsu użytkownika. Nie zmieniaj wartości tych obiektów.
- Aby dostosować Azure AD B2C komunikat o błędzie, należy użyć lokalizacji w zasadzie.
- Jeśli coś można osiągnąć przy użyciu zasad, zwykle jest to zalecany sposób.
