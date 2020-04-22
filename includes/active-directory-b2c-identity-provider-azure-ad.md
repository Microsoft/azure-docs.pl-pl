---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678035"
---
## <a name="register-an-azure-ad-app"></a>Rejestrowanie aplikacji usługi Azure AD

Aby włączyć logowanie dla użytkowników z określonej organizacji usługi Azure AD, należy zarejestrować aplikację w dzierżawie usługi Azure AD.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD (na przykład contoso.com). Wybierz **filtr subskrypcja katalogu +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
1. Wybierz **pozycję Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład `Azure AD B2C App`.
1. Zaakceptuj domyślny wybór **kont w tym katalogu organizacyjnym tylko** dla tej aplikacji.
1. W przypadku **identyfikatora URI przekierowania**zaakceptuj wartość **sieci Web**i wprowadź `your-B2C-tenant-name` następujący adres URL we wszystkich małych literach, gdzie jest zastępowany nazwą dzierżawy usługi Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Wybierz pozycję **Zarejestruj**. Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.
1. Wybierz **pozycję Certyfikaty & wpisy tajne**, a następnie wybierz pozycję Nowy klucz tajny **klienta**.
1. Wprowadź **opis klucza** tajnego, wybierz wygaśnięcie, a następnie wybierz pozycję **Dodaj**. Zapisz **wartość** klucza tajnego do użycia w późniejszym kroku.

### <a name="configuring-optional-claims"></a>Konfigurowanie oświadczeń opcjonalnych

Jeśli chcesz uzyskać `family_name` i `given_name` oświadczeń z usługi Azure AD, można skonfigurować opcjonalne oświadczenia dla aplikacji w interfejsie użytkownika witryny azure portalu lub manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [Jak podać opcjonalne oświadczenia do aplikacji usługi Azure AD](/active-directory/develop/active-directory-optional-claims.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W sekcji **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście.
1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu**.
1. Wybierz **dodaj oświadczenie opcjonalne**.
1. Dla **typu tokenu**wybierz **id**.
1. Wybierz opcjonalne oświadczenia `family_name` do `given_name`dodania i .
1. Kliknij pozycję **Add** (Dodaj).