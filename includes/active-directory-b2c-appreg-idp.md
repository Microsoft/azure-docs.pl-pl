---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: bbf1daf3a70e5ca87b4a0027b53aed5fab5691d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84317443"
---
Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *testapp1*.
1. Wybierz pozycję **konta w dowolnym katalogu organizacyjnym lub dowolnym dostawcy tożsamości**.
1. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Sieć Web**, a następnie wprowadź `https://jwt.ms` wartość w polu tekstowym adres URL.
1. W obszarze **uprawnienia**zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.

Po zakończeniu rejestracji aplikacji Włącz przepływ niejawnego przydzielenia:

1. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie**.
1. Wybierz opcję **Wypróbuj nowe środowisko** (jeśli zostało wyświetlone).
1. W obszarze **niejawne przyznanie**zaznacz pola wyboru **tokeny dostępu** i **tokeny identyfikatora** .
1. Wybierz pozycję **Zapisz**.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **aplikacje**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *testapp1*.
1. W przypadku **aplikacji sieci Web/internetowego interfejsu API**wybierz pozycję **tak**.
1. Dla **adresu URL odpowiedzi**wprowadź `https://jwt.ms`
1. Wybierz przycisk **Utwórz**.