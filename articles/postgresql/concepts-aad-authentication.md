---
title: Uwierzytelnianie Active Directory-Azure Database for PostgreSQL — pojedynczy serwer
description: Informacje na temat pojęć związanych z Azure Active Directory uwierzytelniania za pomocą jednego serwera Azure Database for PostgreSQL
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 0a19bd9d1547c16937ee575c08ea15a52589ccd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87171039"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Używanie usługi Azure Active Directory do uwierzytelniania za pomocą serwera PostgreSQL

Uwierzytelnianie Microsoft Azure Active Directory (Azure AD) to mechanizm łączenia się z Azure Database for PostgreSQL przy użyciu tożsamości zdefiniowanych w usłudze Azure AD.
Uwierzytelnianie za pomocą usługi Azure AD umożliwia zarządzanie tożsamościami użytkowników bazy danych i innymi usługami firmy Microsoft w centralnej lokalizacji, co upraszcza zarządzanie uprawnieniami.

Zalety korzystania z usługi Azure AD:

- Uwierzytelnianie użytkowników w ramach usług platformy Azure w jednolity sposób
- Zarządzanie zasadami haseł i rotacją haseł w jednym miejscu
- Wiele form uwierzytelniania obsługiwanych przez Azure Active Directory, które mogą wyeliminować konieczność przechowywania haseł
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
- Uwierzytelnianie usługi Azure AD używa ról bazy danych PostgreSQL do uwierzytelniania tożsamości na poziomie bazy danych
- Obsługa uwierzytelniania opartego na tokenach dla aplikacji łączących się z Azure Database for PostgreSQL

Aby skonfigurować uwierzytelnianie Azure Active Directory i korzystać z niego, wykonaj następujące czynności:

1. Utwórz i wypełnij Azure Active Directory tożsamościami użytkowników zgodnie z wymaganiami.
2. Opcjonalnie Skojarz lub Zmień Active Directory aktualnie skojarzone z subskrypcją platformy Azure.
3. Utwórz administratora usługi Azure AD dla serwera Azure Database for PostgreSQL.
4. Tworzenie użytkowników bazy danych w bazie danych zamapowanej na tożsamości usługi Azure AD.
5. Nawiąż połączenie z bazą danych, pobierając token dla tożsamości usługi Azure AD i zaloguj się.

> [!NOTE]
> Aby dowiedzieć się, jak utworzyć i wypełnić usługę Azure AD, a następnie skonfigurować usługę Azure AD za pomocą Azure Database for PostgreSQL, zobacz [Konfigurowanie i logowanie za pomocą usługi Azure AD dla Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="architecture"></a>Architektura

Poniższy diagram wysokiego poziomu zawiera podsumowanie sposobu działania uwierzytelniania przy użyciu uwierzytelniania usługi Azure AD z Azure Database for PostgreSQL. Strzałki oznaczają ścieżki komunikacji.

![przepływ uwierzytelniania][1]

## <a name="administrator-structure"></a>Struktura administratora

W przypadku korzystania z uwierzytelniania usługi Azure AD na serwerze PostgreSQL są dwa konta administratora. oryginalny administrator PostgreSQL i administrator usługi Azure AD. Tylko administrator oparty na koncie usługi Azure AD może utworzyć pierwszego użytkownika zawartej bazy danych usługi Azure AD w bazie danych użytkownika. Identyfikator logowania administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli administrator jest kontem grupy, może być używany przez dowolnego członka grupy, co pozwala na wielu administratorów usługi Azure AD dla serwera PostgreSQL. Użycie konta grupy jako administrator zwiększa możliwości zarządzania, umożliwiając centralne Dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników ani uprawnień na serwerze PostgreSQL. W dowolnej chwili można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę).

![Struktura administratora][2]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, którzy mogą uwierzytelniać się w usłudze Azure AD, musisz mieć `azure_ad_admin` rolę w bazie danych. Ta rola jest przypisywana przez skonfigurowanie konta administratora usługi Azure AD dla określonego serwera Azure Database for PostgreSQL.

Aby utworzyć nowego użytkownika bazy danych usługi Azure AD, musisz nawiązać połączenie jako administrator usługi Azure AD. Jest to zademonstrowane w temacie [Konfigurowanie i logowanie za pomocą usługi Azure AD dla Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

Każde uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla Azure Database for PostgreSQL. Jeśli administrator Azure Active Directory został usunięty z serwera, istniejące wcześniej utworzone Azure Active Directory użytkownicy nie mogą już łączyć się z bazą danych przy użyciu poświadczeń Azure Active Directory.

## <a name="connecting-using-azure-ad-identities"></a>Nawiązywanie połączenia przy użyciu tożsamości usługi Azure AD

Azure Active Directory Authentication obsługuje następujące metody łączenia się z bazą danych przy użyciu tożsamości usługi Azure AD:

- Azure Active Directory hasło
- Azure Active Directory zintegrowany
- Azure Active Directory Universal with MFA
- Korzystanie z Active Directory certyfikatów aplikacji lub wpisów tajnych klienta
- [Tożsamość zarządzana](howto-connect-with-managed-identity.md)

Po uzyskaniu uwierzytelnienia względem Active Directory można pobrać token. Ten token jest hasłem do logowania.

Należy pamiętać, że operacje zarządzania, takie jak dodawanie nowych użytkowników, są obsługiwane tylko w przypadku ról użytkownika usługi Azure AD.

> [!NOTE]
> Aby uzyskać więcej informacji na temat nawiązywania połączenia z tokenem Active Directory, zobacz [Konfigurowanie i logowanie za pomocą usługi Azure AD dla Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

- W celu zwiększenia możliwości zarządzania zalecamy udostępnienie dedykowanej grupy usługi Azure AD jako administrator.
- W dowolnym momencie można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę) dla serwera Azure Database for PostgreSQL.
- Tylko administrator usługi Azure AD dla usługi PostgreSQL może początkowo połączyć się z Azure Database for PostgreSQL przy użyciu konta Azure Active Directory. Administrator Active Directory może skonfigurować kolejnych użytkowników bazy danych usługi Azure AD.
- Jeśli użytkownik zostanie usunięty z usługi Azure AD, nie będzie już można uwierzytelnić się w usłudze Azure AD i w związku z tym nie będzie już możliwe uzyskanie tokenu dostępu dla tego użytkownika. W takim przypadku mimo że zgodna rola nadal znajduje się w bazie danych, nie będzie możliwe nawiązanie połączenia z serwerem z tą rolą.
> [!NOTE]
> Można nadal wykonać logowanie przy użyciu usuniętego użytkownika usługi Azure AD, dopóki token nie wygaśnie (do 60 minut od wystawiania tokenów).  Jeśli usuniesz również użytkownika z Azure Database for PostgreSQL ten dostęp zostanie natychmiast odwołany.
- Jeśli administrator usługi Azure AD zostanie usunięty z serwera, serwer nie będzie już skojarzony z dzierżawą usługi Azure AD i w związku z tym wszystkie logowania usługi Azure AD zostaną wyłączone na serwerze. Dodanie nowego administratora usługi Azure AD z tej samej dzierżawy spowoduje ponownie włączenie logowania do usługi Azure AD.
- Azure Database for PostgreSQL dopasowuje tokeny dostępu do roli Azure Database for PostgreSQL przy użyciu unikatowego identyfikatora użytkownika usługi Azure AD, w przeciwieństwie do korzystania z nazwy użytkownika. Oznacza to, że jeśli użytkownik usługi Azure AD zostanie usunięty w usłudze Azure AD i zostanie utworzony nowy użytkownik o tej samej nazwie, Azure Database for PostgreSQL uważa, że inny użytkownik. W związku z tym, jeśli użytkownik zostanie usunięty z usługi Azure AD, a następnie zostanie dodany nowy użytkownik o tej samej nazwie, nowy użytkownik nie będzie mógł nawiązać połączenia z istniejącą rolą. Aby to umożliwić, Azure Database for PostgreSQL administrator usługi Azure AD musi odwołać się, a następnie udzielić użytkownikowi roli "azure_ad_user" do odświeżania identyfikatora użytkownika usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć i wypełnić usługę Azure AD, a następnie skonfigurować usługę Azure AD za pomocą Azure Database for PostgreSQL, zobacz [Konfigurowanie i logowanie za pomocą usługi Azure AD dla Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md).
- Aby zapoznać się z omówieniem nazw logowania, użytkowników i ról bazy danych Azure Database for PostgreSQL, zobacz temat [Tworzenie użytkowników w Azure Database for PostgreSQL — pojedynczy serwer](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
