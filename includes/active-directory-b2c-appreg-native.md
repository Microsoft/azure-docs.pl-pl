---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 65fb406fa4f52f19b4d8e9dc4e3895207ffe6336
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793663"
---
Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *nativeapp1*.
1. W obszarze **obsługiwane typy kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym lub dowolnym dostawcy tożsamości**.
1. W obszarze **Identyfikator URI przekierowania**Użyj listy rozwijanej, aby wybrać opcję **Klient publiczny/natywny (Mobile & Desktop)**.
1. Wprowadź identyfikator URI przekierowania z unikatowym schematem. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Podczas wybierania identyfikatora URI przekierowania należy wziąć pod uwagę ważne zagadnienia:
    * **Programowanie** Do użytku programistycznego można ustawić identyfikator URI przekierowania na, `http://localhost` a Azure AD B2C będzie uwzględniać dowolny port w żądaniu. Jeśli zarejestrowany identyfikator URI zawiera port, Azure AD B2C będzie używać tylko tego portu. Na przykład jeśli zarejestrowany identyfikator URI przekierowania ma wartość `http://localhost` , identyfikator URI przekierowania w żądaniu może być `http://localhost:<randomport>` . Jeśli zarejestrowany identyfikator URI przekierowania ma wartość `http://localhost:8080` , identyfikator URI przekierowania w żądaniu musi być `http://localhost:8080` .
    * **Unikatowy**: schemat identyfikatora URI przekierowania musi być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` jest to schemat `com.onmicrosoft.contosob2c.exampleapp`. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik ma możliwość wyboru aplikacji. Jeśli użytkownik zdecyduje się nieprawidłowo, logowanie nie powiedzie się.
    * **Ukończono**: identyfikator URI przekierowania musi mieć zarówno schemat, jak i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//oauth/` działa, gdy `//oauth` nie powiedzie się. Nie dołączaj znaków specjalnych w identyfikatorze URI, na przykład podkreślenia.
1. W obszarze **uprawnienia**zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
2. Wybierz pozycję **Zarejestruj**.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **aplikacje (starsza wersja)**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *nativeapp1*.
1. W przypadku **klienta natywnego**wybierz pozycję **tak**.
1. Wprowadź **niestandardowy identyfikator URI przekierowania** z unikatowym schematem. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Istnieją dwie ważne kwestie do rozważenia podczas wybierania identyfikatora URI przekierowania:
    * **Unikatowy**: schemat identyfikatora URI przekierowania musi być unikatowy dla każdej aplikacji. W przykładzie `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` jest to schemat `com.onmicrosoft.contosob2c.exampleapp`. Należy przestrzegać tego wzorca. Jeśli dwie aplikacje mają ten sam schemat, użytkownik ma możliwość wyboru aplikacji. Jeśli użytkownik zdecyduje się nieprawidłowo, logowanie nie powiedzie się.
    * **Ukończono**: identyfikator URI przekierowania musi mieć zarówno schemat, jak i ścieżkę. Ścieżka musi zawierać co najmniej jeden ukośnik po nazwie domeny. Na przykład `//oauth/` działa, gdy `//oauth` nie powiedzie się. Nie dołączaj znaków specjalnych w identyfikatorze URI, na przykład podkreślenia.
1. Wybierz przycisk **Utwórz**.