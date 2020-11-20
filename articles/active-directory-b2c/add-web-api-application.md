---
title: Dodawanie aplikacji internetowego interfejsu API — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak dodać aplikację internetowego interfejsu API do dzierżawy Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0eeff0c8c338b09fbe375587db2b955a143138c2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949823"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Dodawanie aplikacji internetowego interfejsu API do dzierżawy Azure Active Directory B2C

 Zarejestruj zasoby interfejsu API sieci Web w dzierżawie, aby umożliwić im akceptowanie i odpowiadanie na żądania przez aplikacje klienckie, które zawierają token dostępu. W tym artykule przedstawiono sposób rejestrowania internetowego interfejsu API w programie Azure Active Directory B2C (Azure AD B2C).

Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *webapi1*.
1. W obszarze **Identyfikator URI przekierowania** wybierz pozycję **Sieć Web**, a następnie wprowadź punkt końcowy, w którym Azure AD B2C powinien zwrócić tokeny, których aplikacja żąda. W aplikacji produkcyjnej można ustawić identyfikator URI przekierowania jako punkt końcowy `https://localhost:5000` . Podczas programowania lub testowania można ustawić dla niego `https://jwt.ms` aplikację sieci Web firmy Microsoft, która wyświetla zdekodowaną zawartość tokenu (zawartość tokenu nigdy nie opuszcza przeglądarki). Można w dowolnym momencie dodawać i modyfikować identyfikatory URI przekierowań w zarejestrowanych aplikacjach.
1. Wybierz pozycję **Zarejestruj**.
1. Zapisz **Identyfikator aplikacji (klienta)** do użycia w kodzie internetowego interfejsu API.

Jeśli masz aplikację, która implementuje przepływ niejawnego przydzielenia, na przykład [aplikację jednostronicową opartą na języku JavaScript](tutorial-register-spa.md), możesz włączyć przepływ, wykonując następujące czynności:

1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. W obszarze **niejawne przyznanie** zaznacz pola wyboru **tokeny dostępu** i **tokeny identyfikatora** .
1. Wybierz pozycję **Zapisz**.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **aplikacje (starsza wersja)**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
6. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
7. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W aplikacji produkcyjnej można ustawić adres URL odpowiedzi na wartość taką jak `https://localhost:44332` . Dla celów testowych Ustaw adres URL odpowiedzi na `https://jwt.ms` .
8. Dla pozycji **Identyfikator URI identyfikatora aplikacji** wprowadź identyfikator używany na potrzeby internetowego interfejsu API. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
9. Kliknij pozycję **Utwórz**.
10. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.

* * *

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Korzystając z zakresów, w tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. Na przykład w [samouczku: rejestrowanie aplikacji w Azure Active Directory B2C](tutorial-register-applications.md), aplikacja sieci Web o nazwie *webapp1* jest zarejestrowana w Azure AD B2C. Za pomocą tej aplikacji można wywołać internetowy interfejs API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Twoja aplikacja została zarejestrowana do wywoływania chronionego internetowego interfejsu API. Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z aplikacji. Aplikacja uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.