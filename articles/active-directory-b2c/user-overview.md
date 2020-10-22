---
title: Omówienie kont użytkowników w Azure Active Directory B2C
description: Dowiedz się więcej o typach kont użytkowników, które mogą być używane w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bb13b350dfecf70e5f3a45755f6f77069a855c6c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92363848"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Omówienie kont użytkowników w Azure Active Directory B2C

W Azure Active Directory B2C (Azure AD B2C) istnieje kilka typów kont, które można utworzyć. Azure Active Directory, Active Directory B2B i Active Directory B2C udziału w typach kont użytkowników, które mogą być używane.

Dostępne są następujące typy kont:

- **Konto** służbowe — konto służbowe może uzyskać dostęp do zasobów w dzierżawie, a rola administratora może zarządzać dzierżawcami.
- **Konto gościa** — konto gościa może mieć tylko konto Microsoft lub Azure Active Directory użytkownika, którego można użyć w celu uzyskania dostępu do aplikacji lub zarządzania dzierżawcami.
- **Konto klienta** — konto konsumenta jest używane przez użytkownika aplikacji, które zostały zarejestrowane w Azure AD B2C. Konta konsumentów mogą być tworzone przez:
  - Użytkownik przechodzący przez przepływ użytkownika podczas rejestrowania w aplikacji Azure AD B2C
  - Korzystanie z interfejsu API Microsoft Graph
  - Korzystanie z witryny Azure Portal

## <a name="work-account"></a>Konto służbowe

Konto służbowe jest tworzone w taki sam sposób dla wszystkich dzierżawców w oparciu o usługę Azure AD. Aby utworzyć konto służbowe, możesz użyć informacji z [przewodnika Szybki Start: Dodawanie nowych użytkowników do Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Konto służbowe jest tworzone przy użyciu wybranego **nowego użytkownika** w Azure Portal.

Po dodaniu nowego konta służbowego należy wziąć pod uwagę następujące ustawienia konfiguracji:

- **Nazwa** i **Nazwa użytkownika** — Właściwość **name** zawiera imię i nazwisko użytkownika. **Nazwa użytkownika** to identyfikator, który użytkownik wprowadza do logowania. Nazwa użytkownika obejmuje pełną domenę. Część nazwy domeny musi być początkową domyślną nazwą domeny *Your-Domain.onmicrosoft.com*lub zweryfikowaną [niestandardową nazwą domeny](../active-directory/fundamentals/add-custom-domain.md) nieautorytatywną, taką jak *contoso.com*.
- **Profil** — konto jest konfigurowane z profilem danych użytkownika. Możesz wprowadzić imię, nazwisko, stanowisko i nazwę działu. Można edytować profil po utworzeniu konta.
- **Grupy** — Użyj grupy do wykonywania zadań zarządzania, takich jak Przypisywanie licencji lub uprawnień do kilku użytkowników lub urządzeń jednocześnie. Nowe konto można umieścić w istniejącej [grupie](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) w dzierżawie.
- **Rola katalogu** — należy określić poziom dostępu, który ma być używany przez konto użytkownika do zasobów w dzierżawie. Dostępne są następujące poziomy uprawnień:

    - **Użytkownik** — użytkownicy mogą uzyskać dostęp do przypisanych zasobów, ale nie może zarządzać większością zasobów dzierżawców.
    - **Administrator globalny** — Administratorzy globalni mają pełną kontrolę nad wszystkimi zasobami dzierżawy.
    - **Ograniczony administrator** — wybierz rolę administracyjną lub role dla użytkownika. Aby uzyskać więcej informacji na temat ról, które można wybrać, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="create-a-work-account"></a>Tworzenie konta służbowego

Poniższe informacje służą do utworzenia nowego konta służbowego:

- [Witryna Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Aktualizowanie profilu użytkownika

Poniższe informacje służą do aktualizowania profilu użytkownika:

- [Witryna Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Zresetuj hasło dla użytkownika

Poniższe informacje służą do resetowania hasła użytkownika:

- [Witryna Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Użytkownik-Gość

Użytkowników zewnętrznych można zaprosić do Twojej dzierżawy jako użytkownik-Gość. Typowym scenariuszem zapraszania użytkownika-gościa do dzierżawy usługi Azure AD B2C jest udostępnianie obowiązków administracyjnych. Aby zapoznać się z przykładem korzystania z konta gościa, zobacz [właściwości Azure Active Directory użytkownika współpracy B2B](../active-directory/b2b/user-properties.md).

Po zaproszeniu użytkownika-gościa do dzierżawy Podaj adres e-mail adresata i komunikat opisujący zaproszenie. Link do zaproszenia przenosi użytkownika na stronę zgody, na której wybrano przycisk **wprowadzenie** i zaakceptowano przegląd uprawnień. Jeśli skrzynka odbiorcza nie jest dołączona do adresu e-mail, użytkownik może przejść do strony zgody, przechodząc do strony firmy Microsoft przy użyciu zaproszonych poświadczeń. Następnie użytkownik jest zmuszony do zrealizowania zaproszenia w taki sam sposób jak kliknięcie linku w wiadomości e-mail. Na przykład: `https://myapps.microsoft.com/B2CTENANTNAME`.

Możesz również użyć [interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) , aby zaprosić użytkownika-gościa.

## <a name="consumer-user"></a>Użytkownik odbiorcy

Użytkownik będący odbiorcą może zalogować się do aplikacji zabezpieczonych przez Azure AD B2C, ale nie może uzyskać dostępu do zasobów platformy Azure, takich jak Azure Portal. Użytkownik odbiorcy może korzystać z konta lokalnego lub kont federacyjnych, takich jak Facebook lub Twitter. Konto klienta jest tworzone przy użyciu usługi [rejestrowania lub logowania użytkownika](user-flow-overview.md)przy użyciu interfejsu API Microsoft Graph lub przy użyciu Azure Portal.

Można określić dane, które są zbierane podczas tworzenia konta użytkownika odbiorcy przy użyciu niestandardowych atrybutów użytkownika. Aby uzyskać więcej informacji, zobacz [Definiowanie atrybutów niestandardowych w Azure Active Directory B2C](user-flow-custom-attributes.md).

Więcej informacji o zarządzaniu kontami użytkowników znajduje się w temacie [Manage Azure AD B2C accounts with Microsoft graphs](manage-user-accounts-graph-api.md).

### <a name="migrate-consumer-user-accounts"></a>Migrowanie kont użytkowników indywidualnych

Może być konieczne przeprowadzenie migracji istniejących kont użytkowników konsumentów z dowolnego dostawcy tożsamości do Azure AD B2C. Aby uzyskać więcej informacji, zobacz [Migrowanie użytkowników do Azure AD B2C](user-migration.md).
