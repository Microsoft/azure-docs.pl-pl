---
title: Azure Active Directory
description: Dowiedz się, jak używać Azure Active Directory do uwierzytelniania za pomocą SQL Database, wystąpienia zarządzanego i usługi Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 58f40bc7406048df2b052bea799bcbf6466fbbae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80421108"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Używanie uwierzytelniania Azure Active Directory na potrzeby uwierzytelniania przy użyciu programu SQL Server

Uwierzytelnianie Azure Active Directory to mechanizm łączenia się z usługą Azure [SQL Database](sql-database-technical-overview.md), [wystąpieniem zarządzanym](sql-database-managed-instance.md)i [analizą usługi Azure Synapse Analytics (dawniej Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) za pomocą tożsamości w usłudze Azure Active Directory (Azure AD). 

> [!NOTE]
> Ten artykuł ma zastosowanie do programu Azure SQL Server i zarówno do SQL Database, jak i do usługi Azure Synapse. Dla uproszczenia SQL Database jest używany podczas odwoływania się do SQL Database i usługi Azure Synapse.

W przypadku uwierzytelniania usługi Azure AD można centralnie zarządzać tożsamościami użytkowników bazy danych oraz innymi usługami firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie tożsamościami oznacza jedną lokalizację do zarządzania użytkownikami bazy danych i prostsze zarządzanie uprawnieniami. Oto przykładowe korzyści:

- Stanowi ono alternatywę uwierzytelniania programu SQL Server
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników na serwerach baz danych.
- Umożliwia rotację haseł w jednym miejscu
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu grup zewnętrznych (usługi Azure AD)
- Pomaga wyeliminować konieczność przechowywania haseł przez włączenie zintegrowanego uwierzytelniania systemu Windows i innych form uwierzytelniania obsługiwanych przez usługę Azure Active Directory
- Uwierzytelnianie usługi Azure AD korzysta z użytkowników zawartej bazy danych w celu uwierzytelniania tożsamości na poziomie bazy danych
- Usługa Azure AD obsługuje uwierzytelnianie oparte na tokenach w przypadku aplikacji łączących się z usługą SQL Database
- Uwierzytelnianie usługi Azure AD obsługuje:
  - Tożsamości tylko w chmurze usługi Azure AD
  - Tożsamości hybrydowe usługi Azure AD, które obsługują:
    - Uwierzytelnianie w chmurze z dwiema opcjami z bezproblemowym logowaniem jednokrotnym (SSO): uwierzytelnianie **przekazywane** i uwierzytelnianie **skrótu hasła**
    - Uwierzytelnianie federacyjne
  - Aby uzyskać więcej informacji na temat metod uwierzytelniania usługi Azure AD i ich wyboru, zobacz następujący artykuł:
    - [Wybierz właściwą metodę uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](../active-directory/hybrid/choose-ad-authn.md)
- Usługa Azure AD obsługuje połączenia z programu SQL Server Management Studio, które używają uniwersalnego uwierzytelniania usługi Active Directory obejmującej usługę Multi-Factor Authentication (MFA).  Usługa MFA obejmuje silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji, takich jak połączenia telefoniczne, wiadomości SMS, karty inteligentne z numerem PIN lub powiadomienia przez aplikacje mobilne. Aby uzyskać więcej informacji, zobacz temat [Obsługa usługi Azure AD MFA przy użyciu programu SQL Database i usługi Azure Synapse](sql-database-ssms-mfa-authentication.md)
- Usługa Azure AD obsługuje podobne połączenia z narzędziami SQL Server Data Tools (SSDT), które używają interaktywnego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Support in SQL Server Data Tools (SSDT).](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Nawiązywanie połączenia z usługą SQL Server działającej na maszynie wirtualnej platformy Azure nie jest obsługiwane przy użyciu konta Azure Active Directory. Zamiast tego użyj konta Active Directory domeny.  

Czynności konfiguracyjne obejmują następujące procedury w celu skonfigurowania i użycia uwierzytelniania Azure Active Directory.

1. Utwórz i wypełnij usługę Azure AD.
2. Opcjonalne: Skojarz lub Zmień usługę Active Directory, która jest aktualnie skojarzona z subskrypcją platformy Azure.
3. Utwórz Azure Active Directory administratora dla serwera Azure SQL Database, wystąpienia zarządzanego lub [usługi Azure Synapse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Skonfiguruj komputery klienckie.
5. Utwórz użytkowników zawartej bazy danych w bazie danych zamapowanej na tożsamości usługi Azure AD.
6. Nawiąż połączenie z bazą danych za pomocą tożsamości usługi Azure AD.

> [!NOTE]
> Aby dowiedzieć się, jak utworzyć i wypełnić usługę Azure AD, a następnie skonfigurować usługę Azure AD za pomocą Azure SQL Database, wystąpienia zarządzanego i usługi Azure Synapse, zobacz [Konfigurowanie usługi Azure AD z Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Architektura zaufania

- Aby można było obsługiwać natywne hasło użytkownika usługi Azure AD, jest uwzględniana tylko część chmury i usługa Azure AD/Azure SQL Database.
- Aby zapewnić obsługę poświadczeń logowania jednokrotnego systemu Windows (lub użytkownika/hasła dla poświadczeń systemu Windows), Użyj poświadczeń Azure Active Directory z domeny federacyjnej lub zarządzanej, która jest skonfigurowana do bezproblemowego logowania jednokrotnego w celu uwierzytelniania za pomocą funkcji przekazywania i skrótu hasła. Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](../active-directory/hybrid/how-to-connect-sso.md).
- Aby zapewnić obsługę uwierzytelniania federacyjnego (lub użytkownika/hasła dla poświadczeń systemu Windows), wymagana jest komunikacja z blokiem ADFS.

Aby uzyskać więcej informacji na temat tożsamości hybrydowych usługi Azure AD, instalacji i synchronizacji, zobacz następujące artykuły:

- Uwierzytelnianie skrótu hasła — [implementowanie synchronizacji skrótów haseł z synchronizacją Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Uwierzytelnianie przekazywane — [Azure Active Directory uwierzytelniania przekazywanego](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Uwierzytelnianie federacyjne — [wdrażanie Active Directory Federation Services na platformie Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) i [Azure AD Connect i w Federacji](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Aby zapoznać się z przykładowym uwierzytelnianiem federacyjnym z infrastrukturą usług ADFS (lub użytkownikiem/hasłem dla poświadczeń systemu Windows), zapoznaj się z poniższym diagramem. Strzałki oznaczają ścieżki komunikacji.

![Diagram uwierzytelniania usługi AAD][1]

Na poniższym diagramie przedstawiono relacje Federacji, zaufania i hostingu, które umożliwiają klientowi łączenie się z bazą danych przez przesłanie tokenu. Token jest uwierzytelniany przez usługę Azure AD i jest traktowany jako zaufany przez bazę danych. Klient 1 może reprezentować Azure Active Directory z natywnymi użytkownikami lub usługą Azure AD z użytkownikami federacyjnymi. Klient 2 oznacza możliwe rozwiązanie, w tym importowani użytkownicy; w tym przykładzie pochodzą z Azure Active Directory federacyjnej z usługą ADFS, które są synchronizowane z Azure Active Directory. Ważne jest, aby zrozumieć, że dostęp do bazy danych przy użyciu uwierzytelniania usługi Azure AD wymaga, aby subskrypcja hostingu była skojarzona z usługą Azure AD. Ta sama subskrypcja musi być używana do tworzenia SQL Server hostingu Azure SQL Database lub Azure Synapse.

![Relacja subskrypcji][2]

## <a name="administrator-structure"></a>Struktura administratora

W przypadku korzystania z uwierzytelniania usługi Azure AD istnieją dwa konta administratorów dla serwera SQL Database i wystąpienia zarządzanego; oryginalny administrator SQL Server i administrator usługi Azure AD. Te same pojęcia dotyczą usługi Azure Synapse. Tylko administrator oparty na koncie usługi Azure AD może utworzyć pierwszego użytkownika zawartej bazy danych usługi Azure AD w bazie danych użytkownika. Identyfikator logowania administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli administrator jest kontem grupy, może być używany przez dowolnego członka grupy, co umożliwia wielu administratorom usługi Azure AD dla wystąpienia SQL Server. Użycie konta grupy jako administrator rozszerza możliwości zarządzania, umożliwiając centralne Dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników ani uprawnień w SQL Database. W dowolnej chwili można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę).

![Struktura administratora][3]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, musisz mieć odpowiednie `ALTER ANY USER` uprawnienia w bazie danych programu. `ALTER ANY USER` Uprawnienie można przyznać każdemu użytkownikowi bazy danych. `ALTER ANY USER` Uprawnienie jest również przechowywane przez konta administratora serwera oraz użytkowników bazy danych z uprawnieniami `CONTROL ON DATABASE` lub `ALTER ON DATABASE` dla tej bazy danych oraz przez członków roli `db_owner` bazy danych.

Aby utworzyć użytkownika zawartej bazy danych w Azure SQL Database, wystąpieniu zarządzanym lub usłudze Azure Synapse, musisz nawiązać połączenie z bazą danych lub wystąpieniem przy użyciu tożsamości usługi Azure AD. Aby utworzyć pierwszego zawartego użytkownika bazy danych, musisz nawiązać połączenie z bazą danych przy użyciu administratora usługi Azure AD (który jest właścicielem bazy danych). Jest to zademonstrowane w temacie [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database lub Azure Synapse](sql-database-aad-authentication-configure.md). Każde uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla Azure SQL Database lub Azure Synapse. Jeśli administrator Azure Active Directory został usunięty z serwera, istniejące Azure Active Directory użytkownicy utworzeni wcześniej w programie SQL Server nie mogą już łączyć się z bazą danych przy użyciu poświadczeń Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkcje i ograniczenia dotyczące usługi Azure AD

- Następujące elementy członkowskie usługi Azure AD mogą być obsługiwane w usłudze Azure SQL Server lub Azure Synapse:

  - Natywni Członkowie: członek utworzony w usłudze Azure AD w domenie zarządzanej lub w domenie klienta. Aby uzyskać więcej informacji, zobacz [Dodawanie własnej nazwy domeny do usługi Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Członkowie domeny Active Directory federacyjnej z Azure Active Directory w domenie zarządzanej skonfigurowanej do bezproblemowego logowania jednokrotnego przy użyciu protokołu Pass-through lub uwierzytelniania skrótu hasła. Aby uzyskać więcej informacji, zobacz [Microsoft Azure teraz obsługuje Federacji z systemem Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) i [Azure Active Directory bezproblemowe logowanie jednokrotne](../active-directory/hybrid/how-to-connect-sso.md).
  - Zaimportowani członkowie z innej usługi Azure AD są członkami domeny natywnej lub federacyjnej.
  - Grupy Active Directory utworzone jako grupy zabezpieczeń.

- Użytkownicy usługi Azure AD będący częścią grupy, która `db_owner` ma rolę serwera, nie mogą używać składni **[poświadczeń "CREATE DATABASE scoped](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** " w odniesieniu do Azure SQL Database i Azure Synapse. Zostanie wyświetlony następujący błąd:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Przyznaj `db_owner` roli bezpośrednio użytkownikowi usługi Azure AD, aby wyeliminować problem z **POŚWIADCZENIAMI tworzenia zakresu bazy danych** .

- Te funkcje systemowe zwracają wartości NULL, jeśli są wykonywane w ramach podmiotów zabezpieczeń usługi Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Wystąpienia zarządzane

- Nazwy główne i użytkownicy serwera usługi Azure AD są obsługiwane jako funkcja w wersji zapoznawczej dla [wystąpień zarządzanych](sql-database-managed-instance.md).
- Ustawienie podmiotów zabezpieczeń serwera usługi Azure AD (nazw logowania) mapowanych na grupę usługi Azure AD jako właściciel bazy danych nie jest obsługiwane w [wystąpieniach zarządzanych](sql-database-managed-instance.md).
    - Rozszerzenie to oznacza, że gdy grupa zostanie dodana jako część roli `dbcreator` serwera, użytkownicy z tej grupy mogą łączyć się z wystąpieniem zarządzanym i tworzyć nowe bazy danych, ale nie będą mogli uzyskać dostępu do bazy danych. Wynika to z faktu, że nowy właściciel bazy danych jest skojarzeniem zabezpieczeń, a nie użytkownikiem usługi Azure AD. Ten problem nie jest manifestem, jeśli pojedynczy użytkownik zostanie dodany do `dbcreator` roli serwera.
- Zarządzanie agentami SQL i wykonywanie zadań są obsługiwane dla podmiotów zabezpieczeń serwera usługi Azure AD.
- Operacje tworzenia kopii zapasowej oraz przywracania bazy danych mogą być wykonywane przy użyciu jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Obsługiwane są inspekcje wszystkich instrukcji związanych z podmiotami zabezpieczeń serwera usługi Azure AD i zdarzeniami uwierzytelniania.
- Obsługiwane jest dedykowane połączenie administratora dla podmiotów zabezpieczeń serwera usługi Azure AD (logowania), które są członkami roli serwera sysadmin.
    - Obsługiwane przez Narzędzie SQLCMD i SQL Server Management Studio.
- Wyzwalacze logowania są obsługiwane dla zdarzeń logowania przychodzących z jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Service Broker i wiadomości e-mail można skonfigurować przy użyciu podmiotu zabezpieczeń serwera usługi Azure AD.


## <a name="connecting-using-azure-ad-identities"></a>Nawiązywanie połączenia przy użyciu tożsamości usługi Azure AD

Azure Active Directory Authentication obsługuje następujące metody łączenia się z bazą danych przy użyciu tożsamości usługi Azure AD:

- Azure Active Directory hasło
- Azure Active Directory zintegrowany
- Azure Active Directory Universal with MFA
- Korzystanie z uwierzytelniania za pomocą tokenu aplikacji

Następujące metody uwierzytelniania są obsługiwane dla podmiotów zabezpieczeń serwera usługi Azure AD (logowania):

- Azure Active Directory hasło
- Azure Active Directory zintegrowany
- Azure Active Directory Universal with MFA


### <a name="additional-considerations"></a>Dodatkowe zagadnienia

- W celu zwiększenia możliwości zarządzania zalecamy udostępnienie dedykowanej grupy usługi Azure AD jako administrator.   
- W dowolnym momencie można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę) dla serwera Azure SQL Database lub Azure Synapse.
  - Dodanie podmiotów zabezpieczeń serwera usługi Azure AD (Logins) dla wystąpień zarządzanych pozwala na utworzenie wielu podmiotów nazw (Logins) serwera usługi Azure AD, które można dodać do `sysadmin` roli.
- Tylko administrator usługi Azure AD dla SQL Server może początkowo nawiązać połączenie z serwerem Azure SQL Database, wystąpieniem zarządzanym lub Synapse platformy Azure przy użyciu konta Azure Active Directory. Administrator Active Directory może skonfigurować kolejnych użytkowników bazy danych usługi Azure AD.   
- Zalecamy ustawienie limitu czasu połączenia na 30 sekund.   
- SQL Server 2016 Management Studio i SQL Server Data Tools for Visual Studio 2015 (wersja 14.0.60311.1 kwietnia 2016 lub nowszy) obsługuje Azure Active Directory uwierzytelnianie. (Uwierzytelnianie usługi Azure AD jest obsługiwane przez **.NET Framework dostawca danych dla programu SqlServer**; co najmniej w wersji .NET Framework 4,6). W związku z tym najnowsze wersje tych narzędzi i aplikacji warstwy danych (DAC i. BACPAC) może korzystać z uwierzytelniania usługi Azure AD.   
- Począwszy od wersji 15.0.1, [narzędzia sqlcmd](/sql/tools/sqlcmd-utility) i obsługi [narzędzia bcp](/sql/tools/bcp-utility) Active Directory interakcyjnego uwierzytelniania przy użyciu usługi MFA.
- Narzędzia SQL Server Data Tools for Visual Studio 2015 wymagają co najmniej wersji 2016 z kwietnia (wersja za14.0.60311.1na). Obecnie użytkownicy usługi Azure AD nie są wyświetlani w SSDT Eksplorator obiektów. Aby obejść ten element, Wyświetl użytkowników w obszarze [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Sterownik Microsoft JDBC 6,0 dla SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) obsługuje uwierzytelnianie w usłudze Azure AD. Ponadto zobacz [Ustawianie właściwości połączenia](https://msdn.microsoft.com/library/ms378988.aspx).   
- Nie można uwierzytelnić przy użyciu uwierzytelniania usługi Azure AD.   
- Uwierzytelnianie usługi Azure AD jest obsługiwane dla SQL Database przez Azure Portal **importowania bazy danych** i **eksportowania baz danych** . Importowanie i eksportowanie przy użyciu uwierzytelniania usługi Azure AD jest również obsługiwane przez polecenie programu PowerShell.   
- Uwierzytelnianie usługi Azure AD jest obsługiwane w przypadku SQL Database, wystąpienia zarządzanego i platformy Azure Synapse przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database lub Azure Synapse](sql-database-aad-authentication-configure.md) i [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć i wypełnić usługę Azure AD, a następnie skonfigurować usługę Azure AD za pomocą Azure SQL Database lub Azure Synapse, zobacz [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database, wystąpienia zarządzanego lub usługi Azure Synapse](sql-database-aad-authentication-configure.md).
- Aby zapoznać się z samouczkiem dotyczącym używania podmiotów zabezpieczeń serwera usługi Azure AD (logowania) z wystąpieniami zarządzanymi, zobacz [nazwy główne serwera usługi Azure AD z wystąpieniami zarządzanymi](sql-database-managed-instance-aad-security-tutorial.md)
- Aby zapoznać się z omówieniem nazw logowania, użytkowników, ról bazy danych i uprawnień w SQL Database, zobacz [logowania, użytkowników, role bazy danych i uprawnienia](sql-database-manage-logins.md).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](https://msdn.microsoft.com/library/ms181127.aspx) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](https://msdn.microsoft.com/library/ms189121.aspx) (Role bazy danych).
- Aby zapoznać się ze składnią tworzenia podmiotów zabezpieczeń serwera usługi Azure AD (logowania) dla wystąpień zarządzanych, zobacz [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
