---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: cea3245176e6c38137d68e3ad4b47477bedc78be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529188"
---
Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć bieżącego środowiska **aplikacji** lub naszego nowego interfejsu Unified **rejestracje aplikacji (wersja zapoznawcza)** . [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **aplikacje**, a następnie wybierz pozycję **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *ROPC_Auth_app*.
1. W przypadku **klienta natywnego**wybierz pozycję **tak**.
1. Pozostaw inne wartości, jeśli są, a następnie wybierz pozycję **Utwórz**.
1. Zapisz **Identyfikator aplikacji** do użycia w późniejszym kroku.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *ROPC_Auth_app*.
1. Pozostaw inne wartości, jeśli są, a następnie wybierz pozycję **zarejestruj**.
1. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.
1. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie**.
1. Wybierz opcję **Wypróbuj nowe środowisko** (jeśli zostało wyświetlone).
1. W obszarze **domyślny typ klienta**wybierz pozycję **tak** , aby traktować aplikację jako klienta publicznego. To ustawienie jest wymagane dla przepływu ROPC.
1. Wybierz pozycję **Zapisz**.
1. W menu po lewej stronie wybierz pozycję **manifest** , aby otworzyć Edytor manifestu. 
1. Ustaw atrybut **oauth2AllowImplicitFlow** na *true*:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Wybierz pozycję **Zapisz**.