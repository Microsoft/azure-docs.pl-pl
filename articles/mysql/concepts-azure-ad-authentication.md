---
title: Uwierzytelnianie Active Directory — Azure Database for MySQL
description: Informacje na temat pojęć związanych z Azure Active Directory uwierzytelniania przy użyciu Azure Database for MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: e3afa7e7129c34aae8487affe979bd7704580a1e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612453"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Używanie usługi Azure Active Directory do uwierzytelniania za pomocą środowiska MySQL

Uwierzytelnianie Microsoft Azure Active Directory (Azure AD) to mechanizm łączenia się z Azure Database for MySQL przy użyciu tożsamości zdefiniowanych w usłudze Azure AD.
Uwierzytelnianie za pomocą usługi Azure AD umożliwia zarządzanie tożsamościami użytkowników bazy danych i innymi usługami firmy Microsoft w centralnej lokalizacji, co upraszcza zarządzanie uprawnieniami.

Zalety korzystania z usługi Azure AD:

- Uwierzytelnianie użytkowników w ramach usług platformy Azure w jednolity sposób
- Zarządzanie zasadami haseł i rotacją haseł w jednym miejscu
- Wiele form uwierzytelniania obsługiwanych przez Azure Active Directory, które mogą wyeliminować konieczność przechowywania haseł
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
- Uwierzytelnianie usługi Azure AD używa użytkowników bazy danych MySQL do uwierzytelniania tożsamości na poziomie bazy danych
- Obsługa uwierzytelniania opartego na tokenach dla aplikacji łączących się z Azure Database for MySQL

Aby skonfigurować uwierzytelnianie Azure Active Directory i korzystać z niego, wykonaj następujące czynności:

1. Utwórz i wypełnij Azure Active Directory tożsamościami użytkowników zgodnie z wymaganiami.
2. Opcjonalnie Skojarz lub Zmień Active Directory aktualnie skojarzone z subskrypcją platformy Azure.
3. Utwórz administratora usługi Azure AD dla serwera Azure Database for MySQL.
4. Tworzenie użytkowników bazy danych w bazie danych zamapowanej na tożsamości usługi Azure AD.
5. Nawiąż połączenie z bazą danych, pobierając token dla tożsamości usługi Azure AD i zaloguj się.

> [!NOTE]
> Aby dowiedzieć się, jak utworzyć i wypełnić usługę Azure AD, a następnie skonfigurować usługę Azure AD za pomocą Azure Database for MySQL, zobacz [Konfigurowanie i logowanie za pomocą usługi Azure AD dla Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Architektura

Poniższy diagram wysokiego poziomu zawiera podsumowanie sposobu działania uwierzytelniania przy użyciu uwierzytelniania usługi Azure AD z Azure Database for MySQL. Strzałki oznaczają ścieżki komunikacji.

![przepływ uwierzytelniania][1]

## <a name="administrator-structure"></a>Struktura administratora

W przypadku korzystania z uwierzytelniania usługi Azure AD dla serwera MySQL istnieją dwa konta administratora. oryginalny administrator MySQL i administrator usługi Azure AD. Tylko administrator oparty na koncie usługi Azure AD może utworzyć pierwszego użytkownika zawartej bazy danych usługi Azure AD w bazie danych użytkownika. Identyfikator logowania administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli administrator jest kontem grupy, może być używany przez dowolnego członka grupy, co pozwala na wielu administratorów usługi Azure AD dla serwera MySQL. Użycie konta grupy jako administrator zwiększa możliwości zarządzania, umożliwiając centralne Dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników ani uprawnień na serwerze MySQL. W dowolnej chwili można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę).

![Struktura administratora][2]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, którzy mogą uwierzytelniać się w usłudze Azure AD, musisz być administratorem usługi Azure AD. Ten użytkownik jest przypisany przez skonfigurowanie konta administratora usługi Azure AD dla określonego serwera Azure Database for MySQL.

Aby utworzyć nowego użytkownika bazy danych usługi Azure AD, musisz nawiązać połączenie jako administrator usługi Azure AD. Jest to zademonstrowane w temacie [Konfigurowanie i logowanie za pomocą usługi Azure AD dla Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

Każde uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla Azure Database for MySQL. Jeśli administrator Azure Active Directory został usunięty z serwera, istniejące wcześniej utworzone Azure Active Directory użytkownicy nie mogą już łączyć się z bazą danych przy użyciu poświadczeń Azure Active Directory.

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
> Aby uzyskać więcej informacji na temat nawiązywania połączenia z tokenem Active Directory, zobacz [Konfigurowanie i logowanie za pomocą usługi Azure AD dla Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

- Uwierzytelnianie Azure Active Directory jest dostępne tylko dla MySQL 5,7 i nowszych.
- W dowolnej chwili można skonfigurować tylko jednego administratora usługi Azure AD dla serwera Azure Database for MySQL.
- Tylko administrator usługi Azure AD dla programu MySQL może początkowo połączyć się z Azure Database for MySQL przy użyciu konta Azure Active Directory. Administrator Active Directory może skonfigurować kolejnych użytkowników bazy danych usługi Azure AD.
- Jeśli użytkownik zostanie usunięty z usługi Azure AD, nie będzie już można uwierzytelnić się w usłudze Azure AD i w związku z tym nie będzie już możliwe uzyskanie tokenu dostępu dla tego użytkownika. W takim przypadku Chociaż pasujący użytkownik nadal znajduje się w bazie danych, nie będzie możliwe nawiązanie połączenia z serwerem za pomocą tego użytkownika.
> [!NOTE]
> Można nadal wykonać logowanie przy użyciu usuniętego użytkownika usługi Azure AD, dopóki token nie wygaśnie (do 60 minut od wystawiania tokenów).  Jeśli usuniesz również użytkownika z Azure Database for MySQL ten dostęp zostanie natychmiast odwołany.
- Jeśli administrator usługi Azure AD zostanie usunięty z serwera, serwer nie będzie już skojarzony z dzierżawą usługi Azure AD i w związku z tym wszystkie logowania usługi Azure AD zostaną wyłączone na serwerze. Dodanie nowego administratora usługi Azure AD z tej samej dzierżawy spowoduje ponowne włączenie nazw logowania usługi Azure AD.
- Azure Database for MySQL dopasowuje tokeny dostępu do Azure Database for MySQL użytkownika przy użyciu unikatowego identyfikatora użytkownika usługi Azure AD użytkownika, a nie przy użyciu nazwy użytkownika. Oznacza to, że jeśli użytkownik usługi Azure AD zostanie usunięty w usłudze Azure AD i zostanie utworzony nowy użytkownik o tej samej nazwie, Azure Database for MySQL uważa, że inny użytkownik. W związku z tym, jeśli użytkownik zostanie usunięty z usługi Azure AD, a następnie zostanie dodany nowy użytkownik o tej samej nazwie, nowy użytkownik nie będzie mógł nawiązać połączenia z istniejącym użytkownikiem.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć i wypełnić usługę Azure AD, a następnie skonfigurować usługę Azure AD za pomocą Azure Database for MySQL, zobacz [Konfigurowanie i logowanie za pomocą usługi Azure AD dla Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).
- Aby zapoznać się z omówieniem logowania i użytkownikami bazy danych dla Azure Database for MySQL, zobacz temat [Tworzenie użytkowników w Azure Database for MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
