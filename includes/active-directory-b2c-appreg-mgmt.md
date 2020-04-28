---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78184361"
---
Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć bieżącego środowiska **aplikacji** lub naszego nowego interfejsu Unified **rejestracje aplikacji (wersja zapoznawcza)** . [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj**wybierz pozycję **rejestracje aplikacji (starsza wersja)**.
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
1. Wprowadź nazwę aplikacji. Na przykład *managementapp1*.
1. W obszarze **Typ aplikacji**wybierz pozycję **aplikacja sieci Web/interfejs API**.
1. Wprowadź dowolny prawidłowy adres URL w **adresie URL logowania**. Na przykład `https://localhost`. Punkt końcowy nie musi być osiągalny, ale musi być prawidłowym adresem URL.
1. Wybierz przycisk **Utwórz**.
1. Zapisz **Identyfikator aplikacji** , który pojawia się na stronie Przegląd **zarejestrowanej aplikacji** . Ta wartość jest używana w późniejszym kroku.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *managementapp1*.
1. Wybierz **konta tylko w tym katalogu organizacji**.
1. W obszarze **uprawnienia**wyczyść pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.
1. Zanotuj **Identyfikator aplikacji (klienta)** , który pojawia się na stronie przeglądu aplikacji. Ta wartość jest używana w późniejszym kroku.