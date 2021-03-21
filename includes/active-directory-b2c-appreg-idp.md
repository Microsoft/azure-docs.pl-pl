---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 890b19cd8d6f4bc836f6f541a830c1a5feb8458f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026742"
---
Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *testapp1*.
1. Wybierz pozycję **konta w dowolnym katalogu organizacyjnym lub dowolnym dostawcy tożsamości**.
1. W obszarze **Identyfikator URI przekierowania** wybierz pozycję **Sieć Web**, a następnie wprowadź `https://jwt.ms` wartość w polu tekstowym adres URL.
1. W obszarze **uprawnienia** zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.

Po zakończeniu rejestracji aplikacji Włącz przepływ niejawnego przydzielenia:

1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. Wybierz opcję **Wypróbuj nowe środowisko** (jeśli zostało wyświetlone).
1. W obszarze **niejawne przyznanie** zaznacz pola wyboru **tokeny dostępu** i **tokeny identyfikatora** .
1. Wybierz pozycję **Zapisz**.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **aplikacje**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *testapp1*.
1. W przypadku **aplikacji sieci Web/internetowego interfejsu API** wybierz pozycję **tak**.
1. Dla **adresu URL odpowiedzi** wprowadź `https://jwt.ms`
1. Wybierz przycisk **Utwórz**.