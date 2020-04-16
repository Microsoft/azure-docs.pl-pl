---
title: Usługa Azure Active Directory
description: Dowiedz się, jak używać usługi Azure Active Directory do uwierzytelniania za pomocą bazy danych SQL, wystąpienia zarządzanego i programu Synapse SQL
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5808f892f189bd6cb2cc39bd157be1d61c966763
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424755"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Używanie uwierzytelniania usługi Azure Active Directory do uwierzytelniania za pomocą programu Synapse SQL

Uwierzytelnianie usługi Azure Active Directory to mechanizm łączenia się z [usługą Azure Synapse Analytics](../overview-faq.md) przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD).

Za pomocą uwierzytelniania usługi Azure AD można centralnie zarządzać tożsamościami użytkowników mających dostęp do usługi Azure Synapse, aby uprościć zarządzanie uprawnieniami. Oto przykładowe korzyści:

- Stanowi alternatywę dla regularnego uwierzytelniania nazwy użytkownika i hasła.
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach bazy danych.
- Umożliwia obracanie hasła w jednym miejscu.
- Klienci mogą zarządzać uprawnieniami przy użyciu zewnętrznych grup (Azure AD).
- Można wyeliminować przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez usługę Azure Active Directory.
- Usługa Azure AD obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z platformą Azure Synapse.
- Uwierzytelnianie usługi Azure AD obsługuje usługę ADFS (federacja domen) lub natywne uwierzytelnianie użytkownika/hasła dla lokalnej usługi Azure Active Directory bez synchronizacji domeny.
- Usługa Azure AD obsługuje połączenia z programu SQL Server Management Studio, które używają uniwersalnego uwierzytelniania usługi Active Directory obejmującej usługę Multi-Factor Authentication (MFA).  Usługa MFA obejmuje silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji, takich jak połączenia telefoniczne, wiadomości SMS, karty inteligentne z numerem PIN lub powiadomienia przez aplikacje mobilne. Aby uzyskać więcej informacji, zobacz [Obsługa usługi SSMS dla usługi Azure AD MFA z synapse SQL](mfa-authentication.md).
- Usługa Azure AD obsługuje podobne połączenia z narzędziami SQL Server Data Tools (SSDT), które używają interaktywnego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Azure Active Directory support in SQL Server Data Tools (SSDT) (Obsługa usługi Azure Active Directory w narzędziach SQL Server Data Tools (SSDT))](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Kroki konfiguracji obejmują następujące procedury konfigurowania i używania uwierzytelniania usługi Azure Active Directory.

1. Tworzenie i wypełnianie usługi Azure AD.
2. Tworzenie tożsamości usługi Azure Active Directory
3. Przypisywanie roli do utworzonej tożsamości usługi Azure Active Directory w obszarze roboczym Synapse (wersja zapoznawcza)
4. Połącz się z Synapse Studio przy użyciu tożsamości usługi Azure AD.

## <a name="aad-pass-through-in-azure-synapse-analytics"></a>Przekazywanie usługi AAD w usłudze Azure Synapse Analytics

Usługa Azure Synapse Analytics umożliwia dostęp do danych w aplikacji data lake przy użyciu tożsamości usługi Azure Active Directory.

Definiowanie praw dostępu do plików i danych, które są przestrzegane w różnych aparatach danych, umożliwia uproszczenie rozwiązań usługi data lake poprzez jedno miejsce, w którym zdefiniowano uprawnienia, zamiast definiowania ich w wielu miejscach.

## <a name="trust-architecture"></a>Architektura zaufania

Poniższy diagram wysokiego poziomu podsumowuje architekturę rozwiązania przy użyciu uwierzytelniania usługi Azure AD za pomocą synapse SQL. Aby obsługiwać hasło użytkownika natywnego usługi Azure AD, uwzględniana jest tylko część usługi Cloud i usługa Azure AD/Synapse Synapse SQL. Do obsługi uwierzytelniania federacyjnego (lub użytkownika/hasła dla poświadczeń systemu Windows) wymagana jest komunikacja z blokiem ADFS. Strzałki wskazują ścieżki komunikacyjne.

![schemat ujedlatek aad][1]

Poniższy diagram wskazuje federacji, zaufania i hostingu relacji, które umożliwiają klientowi połączyć się z bazą danych przez przesłanie tokenu. Token jest uwierzytelniony przez usługę Azure AD i jest zaufany przez bazę danych. Klient 1 może reprezentować usługę Azure Active Directory z użytkownikami natywnymi lub usługę Azure AD z użytkownikami federatywnymi. Klient 2 reprezentuje możliwe rozwiązanie, w tym importowanych użytkowników; w tym przykładzie pochodzących z federacyjnej usługi Azure Active Directory z usługą ADFS są synchronizowane z usługą Azure Active Directory. Ważne jest, aby zrozumieć, że dostęp do bazy danych przy użyciu uwierzytelniania usługi Azure AD wymaga, aby subskrypcja hostingu jest skojarzona z usługą Azure AD. Ta sama subskrypcja musi służyć do tworzenia programu SQL Server obsługującego bazę danych SQL azure lub puli SQL.

![relacja subskrypcji][2]

## <a name="administrator-structure"></a>Struktura administratora

Podczas korzystania z uwierzytelniania usługi Azure AD istnieją dwa konta administratora dla Synapse SQL; pierwotnego administratora programu SQL Server i administratora usługi Azure AD. Tylko administrator na podstawie konta usługi Azure AD można utworzyć pierwszy użytkownik bazy danych zawierającej usługę Azure AD w bazie danych użytkowników. Logowanie administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. 

Gdy administrator jest kontem grupy, może być używany przez dowolnego członka grupy, włączając wielu administratorów usługi Azure AD dla wystąpienia Synapse SQL. Korzystanie z konta grupy jako administratora zwiększa możliwości zarządzania, umożliwiając centralne dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników lub uprawnień w obszarze roboczym Usługi Synapse Analytics. Tylko jeden administrator usługi Azure AD (użytkownik lub grupa) można skonfigurować w dowolnym momencie.

![struktura administracyjna][3]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, `ALTER ANY USER` musisz mieć uprawnienia w bazie danych. Uprawnienie `ALTER ANY USER` można udzielić każdemu użytkownikowi bazy danych. `ALTER ANY USER` Uprawnienie jest również przechowywane przez konta administratora `CONTROL ON DATABASE` serwera `ALTER ON DATABASE` i użytkowników bazy danych z `db_owner` uprawnieniami lub uprawnieniami dla tej bazy danych oraz przez członków roli bazy danych.

Aby utworzyć użytkownika zawartej bazy danych w Synapse SQL, należy połączyć się z bazą danych lub wystąpieniem przy użyciu tożsamości usługi Azure AD. Aby utworzyć pierwszego użytkownika bazy danych, należy połączyć się z bazą danych przy użyciu administratora usługi Azure AD (który jest właścicielem bazy danych). Uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla synapse SQL. Jeśli administrator usługi Azure Active Directory został usunięty z serwera, istniejący użytkownicy usługi Azure Active Directory utworzone wcześniej wewnątrz Synapse SQL nie mogą już łączyć się z bazą danych przy użyciu poświadczeń usługi Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkcje i ograniczenia usługi Azure AD

- Następujące elementy członkowskie usługi Azure AD mogą być aprowizacji w Synapse SQL:

  - Natywnych członków: członek utworzony w usłudze Azure AD w domenie zarządzanej lub w domenie klienta. Aby uzyskać więcej informacji, zobacz [Dodawanie własnej nazwy domeny do usługi Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
  - Członkowie domeny federacyjnej: członek utworzony w usłudze Azure AD z domeną federacyjnej. Aby uzyskać więcej informacji, zobacz [Microsoft Azure obsługuje teraz federację z usługą Active Directory systemu Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Zaimportowani członkowie z innych użytkowników usługi Azure AD, którzy są natywnymi lub sfederowanymi członkami domeny.
  - Grupy usługi Active Directory utworzone jako grupy zabezpieczeń.

- Użytkownicy usługi Azure AD, którzy `db_owner` są częścią grupy, która ma rolę serwera, nie mogą używać składni **[POŚWIADCZEŃ O ZAKRESIE BAZY DANYCH](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** względem synapse SQL. Zostanie wyświetlony następujący błąd:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Udziel `db_owner` roli bezpośrednio poszczególnym użytkownikom usługi Azure AD, aby ograniczyć problem **z poświadczeniami o zakresie bazy danych.**

- Te funkcje systemowe zwracają wartości NULL, gdy są wykonywane w ramach jednostki usługi Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Łączenie przy użyciu tożsamości usługi Azure AD

Uwierzytelnianie usługi Azure Active Directory obsługuje następujące metody łączenia się z bazą danych przy użyciu tożsamości usługi Azure AD:

- Hasło usługi Azure Active Directory
- Zintegrowana usługa Azure Active Directory
- Uniwersalna usługa Azure Active Directory z usługą MFA
- Korzystanie z uwierzytelniania tokenu aplikacji

Następujące metody uwierzytelniania są obsługiwane dla podmiotów serwerowych usługi Azure AD (logowania)**(publiczna wersja zapoznawcza):**

- Hasło usługi Azure Active Directory
- Zintegrowana usługa Azure Active Directory
- Uniwersalna usługa Azure Active Directory z usługą MFA

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

- Aby zwiększyć łatwość zarządzania, zaleca się aprowizowanie dedykowanej grupy usługi Azure AD jako administratora.
- Tylko jeden administrator usługi Azure AD (użytkownik lub grupa) można skonfigurować dla puli SQL Synapse w dowolnym momencie.
  - Dodanie podmiotów serwera usługi Azure AD (logowania) dla języka SQL na żądanie (wersja zapoznawcza) umożliwia utworzenie wielu podmiotów serwera `sysadmin` usługi Azure AD (logowania), które można dodać do roli.
- Tylko administrator usługi Azure AD dla synapse SQL może początkowo połączyć się z Synapse SQL przy użyciu konta usługi Azure Active Directory. Administrator usługi Active Directory może konfigurować kolejnych użytkowników bazy danych usługi Azure AD.
- Zalecamy ustawienie limitu czasu połączenia na 30 sekund.
- Sql Server 2016 Management Studio i SQL Server Data Tools for Visual Studio 2015 (wersja 14.0.60311.1 kwietnia 2016 lub nowsze) obsługują uwierzytelnianie usługi Azure Active Directory. (Uwierzytelnianie usługi Azure AD jest obsługiwane przez **dostawcę danych programu .NET Framework dla programu SqlServer**; co najmniej w wersji .NET Framework 4.6). Dlatego najnowsze wersje tych narzędzi i aplikacji warstwy danych (DAC i . BACPAC) można użyć uwierzytelniania usługi Azure AD.
- Począwszy od wersji 15.0.1, [narzędzie sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [narzędzie bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) obsługują interaktywne uwierzytelnianie usługi Active Directory za pomocą usługi MFA.
- Sql Server Data Tools for Visual Studio 2015 wymaga co najmniej wersji narzędzia danych z kwietnia 2016 r. (wersja 14.0.60311.1). Obecnie użytkownicy usługi Azure AD nie są wyświetlane w Eksploratorze obiektów SSDT. Aby obejść ten problem, wyświetl użytkowników w [pliku sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
- [Sterownik JDBC firmy Microsoft 6.0 dla programu SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) obsługuje uwierzytelnianie usługi Azure AD. Zobacz [też: Ustawianie właściwości połączenia](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie dostępu i kontroli w synapse SQL, zobacz [Kontrola dostępu Synapse SQL](../sql/access-control.md). Aby dowiedzieć się więcej o podmiotach bazy danych, zobacz [Podmioty.](https://msdn.microsoft.com/library/ms181127.aspx) Dodatkowe informacje na temat ról bazy danych można znaleźć w artykule [Role bazy danych.](https://msdn.microsoft.com/library/ms189121.aspx)
 

<!--Image references-->

[1]: ./media/aad-authentication/1-active-directory-authentication-diagram.png
[2]: ./media/aad-authentication/2-subscription-relationship.png
[3]: ./media/aad-authentication/3-admin-structure.png
[4]: ./media/aad-authentication/4-select-subscription.png
[5]: ./media/aad-authentication/5-active-directory-settings-portal.png
[6]: ./media/aad-authentication/6-edit-directory-select.png
[7]: ./media/aad-authentication/7-edit-directory-confirm.png
[8]: ./media/aad-authentication/8-choose-active-directory.png
[9]: ./media/aad-authentication/9-active-directory-settings.png
[10]: ./media/aad-authentication/10-choose-admin.png
[11]: ./media/aad-authentication/11-connect-using-integrated-authentication.png
[12]: ./media/aad-authentication/12-connect-using-password-authentication.png
[13]: ./media/aad-authentication/13-connect-to-db.png
