---
title: Omówienie kont użytkowników w Azure Active Directory B2C
description: Dowiedz się więcej o typach kont użytkowników, których można używać w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 4b35cfeded13a50e5e27c240b0826f1d108ff7eb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529453"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Omówienie kont użytkowników w Azure Active Directory B2C

W Azure Active Directory B2C (Azure AD B2C) istnieje kilka typów kont, które można utworzyć. Azure Active Directory, Active Directory B2B i Active Directory B2C współużytkuje w typach kont użytkowników, które mogą być używane.

Dostępne są następujące typy kont:

- **Konto służbowe** — konto służbowe może uzyskać dostęp do zasobów w dzierżawie, a z rolą administratora może zarządzać dzierżawami.
- **Konto gościa** — konto gościa może być tylko kontem konto Microsoft lub Azure Active Directory, który może służyć do uzyskiwania dostępu do aplikacji lub zarządzania dzierżawami.
- **Konto konsumenta** — konto użytkownika jest używane przez użytkownika aplikacji zarejestrowanych w Azure AD B2C. Konta konsumentów można tworzyć za pomocą:
  - Użytkownik przechodzący przez przepływ użytkownika rejestracji w aplikacji Azure AD B2C aplikacji
  - Korzystanie z Microsoft Graph API
  - Korzystanie z witryny Azure Portal

## <a name="work-account"></a>Konto służbowe

Konto służbowe jest tworzone w taki sam sposób dla wszystkich dzierżawców w oparciu o usługę Azure AD. Aby utworzyć konto służbowe, możesz skorzystać z informacji w [przewodniku Szybki start: dodawanie nowych użytkowników](../active-directory/fundamentals/add-users-azure-active-directory.md)do Azure Active Directory . Konto służbowe jest tworzone przy użyciu opcji **Nowy** użytkownik w Azure Portal.

Podczas dodawania nowego konta służbowego należy wziąć pod uwagę następujące ustawienia konfiguracji:

- **Name** **(Nazwa) i User name (Nazwa)** — właściwość **Name** zawiera podane imię i nazwisko użytkownika. Nazwa **użytkownika to** identyfikator, który użytkownik wprowadza, aby się zalogować. Nazwa użytkownika zawiera pełną domenę. Część nazwy domeny w nazwie użytkownika musi być początkową domyślną nazwą domeny *your-domain.onmicrosoft.com* lub [](../active-directory/fundamentals/add-custom-domain.md) zweryfikowaną, nie federacyjną niestandardową nazwą domeny, taką *jak contoso.com*. 
- **Adres** e-mail — nowy użytkownik może również zalogować się przy użyciu adresu e-mail. Nie obsługujemy znaków specjalnych ani znaków wielobajtowych w wiadomości e-mail, na przykład znaków japońskich.
- **Profil** — konto jest ustawione przy użyciu profilu danych użytkownika. Możesz wprowadzić imię, nazwisko, stanowisko i nazwę działu. Profil można edytować po utworzeniu konta.
- **Grupy** — grupy są przeznaczone do wykonywania zadań zarządzania, takich jak przypisywanie licencji lub uprawnień do wielu użytkowników lub urządzeń jednocześnie. Nowe konto można umieścić w [istniejącej](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) grupie w dzierżawie.
- **Rola katalogu** — należy określić poziom dostępu konta użytkownika do zasobów w dzierżawie. Dostępne są następujące poziomy uprawnień:

    - **Użytkownik** — użytkownicy mogą uzyskać dostęp do przypisanych zasobów, ale nie mogą zarządzać większość zasobów dzierżawy.
    - **administrator globalny** — administratorzy globalni mają pełną kontrolę nad wszystkimi zasobami dzierżawy.
    - **Ograniczony administrator** — wybierz rolę lub role administracyjne użytkownika. Aby uzyskać więcej informacji na temat ról, które można wybrać, zobacz [Przypisywanie ról administratorów w](../active-directory/roles/permissions-reference.md)Azure Active Directory .

### <a name="create-a-work-account"></a>Tworzenie konta służbowego

Aby utworzyć nowe konto służbowe, możesz użyć następujących informacji:

- [Witryna Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Aktualizowanie profilu użytkownika

Aby zaktualizować profil użytkownika, możesz użyć następujących informacji:

- [Witryna Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Resetowanie hasła użytkownika

Aby zresetować hasło użytkownika, możesz użyć następujących informacji:

- [Witryna Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Użytkownik-gość

Możesz zaprosić użytkowników zewnętrznych do dzierżawy jako użytkowników-gości. Typowym scenariuszem zapraszania użytkownika-gościa do dzierżawy Azure AD B2C jest udostępnianie obowiązków administracyjnych. Aby uzyskać przykład użycia konta gościa, zobacz Właściwości użytkownika współpracy [B2B Azure Active Directory B2B.](../active-directory/external-identities/user-properties.md)

Po zaproszeniu użytkownika-gościa do dzierżawy należy podać adres e-mail adresata i wiadomość opisująca zaproszenie. Link do zaproszenia przenosi użytkownika do strony zgody. Jeśli do adresu e-mail nie dołączono skrzynki odbiorczej, użytkownik może przejść do strony zgody, przechodząc do strony firmy Microsoft przy użyciu zaproszonych poświadczeń. Następnie użytkownik musi zrealizować zaproszenie tak samo jak kliknięcie linku w wiadomości e-mail. Na przykład: `https://myapps.microsoft.com/B2CTENANTNAME`.

Możesz również użyć interfejsu [API Microsoft Graph,](/graph/api/invitation-post) aby zaprosić użytkownika-gościa.

## <a name="consumer-user"></a>Użytkownik konsument

Użytkownik może logować się do aplikacji zabezpieczonych przez usługę Azure AD B2C, ale nie może uzyskać dostępu do zasobów platformy Azure, takich jak Azure Portal. Użytkownik może używać konta lokalnego lub kont federowanych, takich jak Facebook lub Twitter. Konto użytkownika jest tworzone przy użyciu przepływu użytkownika tworzenia konta lub logowania [,](user-flow-overview.md)przy użyciu interfejsu API Microsoft Graph api lub przy użyciu interfejsu Azure Portal.

Możesz określić dane, które są zbierane podczas tworzenia konta użytkownika konsumenta. Aby uzyskać więcej informacji, zobacz [Dodawanie atrybutów użytkownika i dostosowywanie danych wejściowych użytkownika.](configure-user-input.md)

Aby uzyskać więcej informacji na temat zarządzania kontami konsumentów, zobacz [Manage Azure AD B2C user accounts with Microsoft Graph](./microsoft-graph-operations.md).

### <a name="migrate-consumer-user-accounts"></a>Migrowanie kont użytkowników konsumentów

Może być konieczne przeprowadzenie migracji istniejących kont użytkowników z dowolnego dostawcy tożsamości do Azure AD B2C. Aby uzyskać więcej informacji, zobacz [Migrowanie użytkowników do usługi Azure AD B2C](user-migration.md).
