---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79126735"
---
## <a name="disable-email-verification"></a>Wyłączanie weryfikacji e-mail

Domyślnie program Azure Active Directory B2C (Azure AD B2C) weryfikuje adres e-mail klienta dla kont lokalnych (konta użytkowników logujących się przy użyciu adresu e-mail lub nazwy użytkownika). Azure AD B2C zapewnia prawidłowe adresy e-mail, wymagając od klientów zweryfikowania ich podczas procesu rejestracji. Zapobiega to również złośliwym podmiotom korzystającym z zautomatyzowanych procesów do generowania fałszywych kont w aplikacjach.

Niektórzy deweloperzy aplikacji wolą pominąć weryfikację wiadomości e-mail podczas procesu rejestracji, a zamiast tego ponownie sprawdzić swój adres e-mail przez klientów. Aby to umożliwić, Azure AD B2C można skonfigurować w celu wyłączenia weryfikacji wiadomości e-mail. Dzięki temu program tworzy płynny proces tworzenia konta i zapewnia deweloperom elastyczność w odróżnieniu od klientów, którzy sprawdzili swój adres e-mail od klientów.

> [!WARNING]
> Wyłączenie weryfikacji poczty e-mail w procesie tworzenia konta może prowadzić do spamu. W przypadku wyłączenia domyślnej weryfikacji poczty e-mail dostarczonej przez Azure AD B2C zalecamy zaimplementowanie systemu weryfikacji zamiennej.
