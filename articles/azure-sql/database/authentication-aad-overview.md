---
title: Uwierzytelnianie za pomocą usługi Azure Active Directory
description: Dowiedz się, jak używać Azure Active Directory do uwierzytelniania za pomocą Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i Synapse SQL w usłudze Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: a636c0e2a41b636f30ada14d4f16a022f2890b71
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454299"
---
# <a name="use-azure-active-directory-authentication"></a>Użyj uwierzytelniania Azure Active Directory

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Uwierzytelnianie Azure Active Directory (Azure AD) to mechanizm łączenia z [Azure SQL Database](sql-database-paas-overview.md), [wystąpienia zarządzanego Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)i [Synapse SQL w usłudze Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) przy użyciu tożsamości w usłudze Azure AD.

> [!NOTE]
> Ten artykuł ma zastosowanie do Azure SQL Database, wystąpienia zarządzanego SQL i usługi Azure Synapse Analytics.

Korzystając z uwierzytelniania za pomocą usługi Azure AD, możesz zarządzać tożsamościami użytkowników bazy danych oraz innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie tożsamościami oznacza jedną lokalizację do zarządzania użytkownikami bazy danych i prostsze zarządzanie uprawnieniami. Oto przykładowe korzyści:

- Stanowi alternatywę dla SQL Server uwierzytelniania.
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników między serwerami.
- Umożliwia obrót hasła w jednym miejscu.
- Klienci mogą zarządzać uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
- Może on wyeliminować przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez Azure Active Directory.
- Uwierzytelnianie usługi Azure AD korzysta z użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
- Usługa Azure AD obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z usługą SQL Database i wystąpieniem zarządzanym SQL.
- Uwierzytelnianie usługi Azure AD obsługuje:
  - Tożsamości tylko w chmurze usługi Azure AD.
  - Tożsamości hybrydowe usługi Azure AD, które obsługują:
    - Uwierzytelnianie w chmurze z dwiema opcjami w połączeniu z bezproblemowym uwierzytelnianiem jednokrotnym ( **SSO) i uwierzytelnianiem** **skrótu hasła** .
    - Uwierzytelnianie federacyjne.
  - Aby uzyskać więcej informacji na temat metod uwierzytelniania usługi Azure AD i ich wyboru, zobacz następujący artykuł:
    - [Wybierz właściwą metodę uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](../../active-directory/hybrid/choose-ad-authn.md)

- Usługa Azure AD obsługuje połączenia z SQL Server Management Studio korzystających z Active Directory uwierzytelniania uniwersalnego, w tym Multi-Factor Authentication. Multi-Factor Authentication obejmuje silne uwierzytelnianie z szerokim zakresem opcji łatwej weryfikacji — połączenie telefoniczne, wiadomość tekstowa, karty inteligentne z numerem PIN lub powiadomieniem aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz temat [Obsługa narzędzia SSMS dla usługi Azure AD Multi-Factor Authentication z Azure SQL Database, wystąpieniem zarządzanym SQL i usługą Azure Synapse](authentication-mfa-ssms-overview.md)

- Usługa Azure AD obsługuje podobne połączenia z narzędziami SQL Server Data Tools (SSDT), które używają interaktywnego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Support in SQL Server Data Tools (SSDT).](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Nawiązywanie połączenia z wystąpieniem SQL Server uruchomionym na maszynie wirtualnej platformy Azure (VM) nie jest obsługiwane przy użyciu konta Azure Active Directory. Zamiast tego użyj konta domeny usługi Active Directory.  

Czynności konfiguracyjne obejmują następujące procedury w celu skonfigurowania i użycia uwierzytelniania Azure Active Directory.

1. Utwórz i wypełnij usługę Azure AD.
2. Opcjonalne: Skojarz lub Zmień usługę Active Directory, która jest aktualnie skojarzona z subskrypcją platformy Azure.
3. Utwórz administratora Azure Active Directory.
4. Skonfiguruj komputery klienckie.
5. Utwórz użytkowników zawartej bazy danych w bazie danych zamapowanej na tożsamości usługi Azure AD.
6. Nawiąż połączenie z bazą danych za pomocą tożsamości usługi Azure AD.

> [!NOTE]
> Aby dowiedzieć się, jak utworzyć i wypełnić usługę Azure AD, a następnie skonfigurować usługę Azure AD za pomocą Azure SQL Database, wystąpienia zarządzanego SQL i Synapse SQL w usłudze Azure Synapse Analytics, zobacz [Konfigurowanie usługi Azure AD z Azure SQL Database](authentication-aad-configure.md).

## <a name="trust-architecture"></a>Architektura zaufania

- Tylko część usługi Azure AD, SQL Database, wystąpienie zarządzane SQL i Azure Synapse jest uznawana za obsługę natywnych haseł użytkowników w usłudze Azure AD.
- Aby zapewnić obsługę poświadczeń logowania jednokrotnego systemu Windows (lub użytkownika/hasła dla poświadczeń systemu Windows), Użyj poświadczeń Azure Active Directory z domeny federacyjnej lub zarządzanej, która jest skonfigurowana do bezproblemowego logowania jednokrotnego w celu uwierzytelniania za pomocą funkcji przekazywania i skrótu hasła. Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](../../active-directory/hybrid/how-to-connect-sso.md).
- Aby zapewnić obsługę uwierzytelniania federacyjnego (lub użytkownika/hasła dla poświadczeń systemu Windows), wymagana jest komunikacja z blokiem ADFS.

Aby uzyskać więcej informacji na temat tożsamości hybrydowych usługi Azure AD, instalacji i synchronizacji, zobacz następujące artykuły:

- Uwierzytelnianie skrótu hasła — [implementowanie synchronizacji skrótów haseł z synchronizacją Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Uwierzytelnianie przekazywane — [Azure Active Directory uwierzytelniania przekazywanego](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Uwierzytelnianie federacyjne — [wdrażanie Active Directory Federation Services na platformie Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) i [Azure AD Connect i w Federacji](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Aby zapoznać się z przykładowym uwierzytelnianiem federacyjnym z infrastrukturą usług ADFS (lub użytkownikiem/hasłem dla poświadczeń systemu Windows), zapoznaj się z poniższym diagramem. Strzałki oznaczają ścieżki komunikacji.

![Diagram uwierzytelniania usługi AAD][1]

Na poniższym diagramie przedstawiono relacje Federacji, zaufania i hostingu, które umożliwiają klientowi łączenie się z bazą danych przez przesłanie tokenu. Token jest uwierzytelniany przez usługę Azure AD i jest traktowany jako zaufany przez bazę danych. Klient 1 może reprezentować Azure Active Directory z natywnymi użytkownikami lub usługą Azure AD z użytkownikami federacyjnymi. Klient 2 to możliwe rozwiązanie, w tym zaimportowani użytkownicy, w tym przykładzie pochodzące z Azure Active Directory federacyjnej z usługą ADFS, które są synchronizowane z Azure Active Directory. Ważne jest, aby zrozumieć, że dostęp do bazy danych przy użyciu uwierzytelniania usługi Azure AD wymaga, aby subskrypcja hostingu była skojarzona z usługą Azure AD. Ta sama subskrypcja musi być używana do tworzenia Azure SQL Database, wystąpienia zarządzanego SQL lub zasobów usługi Azure Synapse.

![Relacja subskrypcji][2]

## <a name="administrator-structure"></a>Struktura administratora

W przypadku korzystania z uwierzytelniania usługi Azure AD istnieją dwa konta administratora: oryginalny administrator Azure SQL Database i administrator usługi Azure AD. Te same pojęcia dotyczą usługi Azure Synapse. Tylko administrator oparty na koncie usługi Azure AD może utworzyć pierwszego użytkownika zawartej bazy danych usługi Azure AD w bazie danych użytkownika. Identyfikator logowania administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli administrator jest kontem grupy, może być używany przez dowolnego członka grupy, co umożliwia wielu administratorom usługi Azure AD na serwerze. Użycie konta grupy jako administrator usprawnia zarządzanie, umożliwiając centralne Dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników ani uprawnień w SQL Database lub Azure Synapse. W dowolnej chwili można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę).

![Struktura administratora][3]

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, musisz mieć odpowiednie `ALTER ANY USER` uprawnienia w bazie danych programu. `ALTER ANY USER`Uprawnienie można przyznać każdemu użytkownikowi bazy danych. `ALTER ANY USER`Uprawnienie jest również przechowywane przez konta administratora serwera oraz użytkowników bazy danych z `CONTROL ON DATABASE` `ALTER ON DATABASE` uprawnieniami lub dla tej bazy danych oraz przez członków `db_owner` roli bazy danych.

Aby utworzyć użytkownika zawartej bazy danych w Azure SQL Database, wystąpieniu zarządzanym SQL lub Azure Synapse, musisz nawiązać połączenie z bazą danych lub wystąpieniem przy użyciu tożsamości usługi Azure AD. Aby utworzyć pierwszego zawartego użytkownika bazy danych, musisz nawiązać połączenie z bazą danych przy użyciu administratora usługi Azure AD (który jest właścicielem bazy danych). Jest to zademonstrowane w temacie [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database lub Azure Synapse](authentication-aad-configure.md). Uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla Azure SQL Database, wystąpienia zarządzanego SQL lub Azure Synapse. Jeśli administrator Azure Active Directory został usunięty z serwera, istniejące Azure Active Directory użytkownicy utworzeni wcześniej w programie SQL Server nie mogą już łączyć się z bazą danych przy użyciu poświadczeń Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Funkcje i ograniczenia dotyczące usługi Azure AD

- Dla Azure SQL Database można zainicjować obsługę następujących członków usługi Azure AD:

  - Natywni Członkowie: członek utworzony w usłudze Azure AD w domenie zarządzanej lub w domenie klienta. Aby uzyskać więcej informacji, zobacz [Dodawanie własnej nazwy domeny do usługi Azure AD](../../active-directory/fundamentals/add-custom-domain.md).
  - Członkowie domeny Active Directory federacyjnej z Azure Active Directory w domenie zarządzanej skonfigurowanej do bezproblemowego logowania jednokrotnego przy użyciu protokołu Pass-through lub uwierzytelniania skrótu hasła. Aby uzyskać więcej informacji, zobacz [Microsoft Azure teraz obsługuje Federacji z systemem Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) i [Azure Active Directory bezproblemowe logowanie jednokrotne](../../active-directory/hybrid/how-to-connect-sso.md).
  - Zaimportowani członkowie z innej usługi Azure AD są członkami domeny natywnej lub federacyjnej.
  - Grupy Active Directory utworzone jako grupy zabezpieczeń.

- Użytkownicy usługi Azure AD będący częścią grupy, która ma `db_owner` rolę serwera, nie mogą używać SKŁADNI **[poświadczeń "CREATE DATABASE scoped](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** " w odniesieniu do Azure SQL Database i Azure Synapse. Zostanie wyświetlony następujący błąd:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Przyznaj `db_owner` roli bezpośrednio użytkownikowi usługi Azure AD, aby wyeliminować problem z **poświadczeniami tworzenia zakresu bazy danych** .

- Te funkcje systemowe zwracają wartości NULL, jeśli są wykonywane w ramach podmiotów zabezpieczeń usługi Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

- Nazwy główne serwera usługi Azure AD (logowania) i użytkownicy są obsługiwane w przypadku [wystąpienia zarządzanego SQL](../managed-instance/sql-managed-instance-paas-overview.md).
- Ustawienie podmiotów zabezpieczeń serwera usługi Azure AD (nazw logowania) mapowanych na grupę usługi Azure AD jako właściciel bazy danych nie jest obsługiwane w [wystąpieniu zarządzanym SQL](../managed-instance/sql-managed-instance-paas-overview.md).
  - Rozszerzenie to oznacza, że gdy grupa zostanie dodana jako część `dbcreator` roli serwera, użytkownicy z tej grupy mogą łączyć się z wystąpieniem zarządzanym SQL i tworzyć nowe bazy danych, ale nie będą mogli uzyskać dostępu do bazy danych. Wynika to z faktu, że nowy właściciel bazy danych jest skojarzeniem zabezpieczeń, a nie użytkownikiem usługi Azure AD. Ten problem nie jest manifestem, jeśli pojedynczy użytkownik zostanie dodany do `dbcreator` roli serwera.
- Zarządzanie agentami SQL i wykonywanie zadań są obsługiwane dla podmiotów zabezpieczeń serwera usługi Azure AD.
- Operacje tworzenia kopii zapasowej oraz przywracania bazy danych mogą być wykonywane przy użyciu jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Obsługiwane są inspekcje wszystkich instrukcji związanych z podmiotami zabezpieczeń serwera usługi Azure AD i zdarzeniami uwierzytelniania.
- Obsługiwane jest dedykowane połączenie administratora dla podmiotów zabezpieczeń serwera usługi Azure AD (logowania), które są członkami roli serwera sysadmin.
  - Obsługiwane przez Narzędzie SQLCMD i SQL Server Management Studio.
- Wyzwalacze logowania są obsługiwane dla zdarzeń logowania przychodzących z jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Service Broker i wiadomości e-mail można skonfigurować przy użyciu podmiotu zabezpieczeń serwera usługi Azure AD.

## <a name="connect-by-using-azure-ad-identities"></a>Nawiązywanie połączenia przy użyciu tożsamości usługi Azure AD

Azure Active Directory Authentication obsługuje następujące metody łączenia się z bazą danych przy użyciu tożsamości usługi Azure AD:

- Azure Active Directory hasło
- Azure Active Directory zintegrowany
- Azure Active Directory uniwersalne z Multi-Factor Authentication
- Korzystanie z uwierzytelniania za pomocą tokenu aplikacji

Następujące metody uwierzytelniania są obsługiwane dla podmiotów zabezpieczeń serwera usługi Azure AD (logowania):

- Azure Active Directory hasło
- Azure Active Directory zintegrowany
- Azure Active Directory uniwersalne z Multi-Factor Authentication

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

- W celu zwiększenia możliwości zarządzania zalecamy udostępnienie dedykowanej grupy usługi Azure AD jako administrator.
- W dowolnej chwili można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę) dla serwera w SQL Database lub Azure Synapse.
  - Dodanie podmiotów zabezpieczeń serwera usługi Azure AD dla wystąpienia zarządzanego SQL umożliwia tworzenie wielu podmiotów nazw (Logins) serwera usługi Azure AD, które można dodać do `sysadmin` roli.
- Tylko administrator usługi Azure AD dla serwera może początkowo połączyć się z serwerem lub wystąpieniem zarządzanym przy użyciu konta Azure Active Directory. Administrator Active Directory może skonfigurować kolejnych użytkowników bazy danych usługi Azure AD.
- Zalecamy ustawienie limitu czasu połączenia na 30 sekund.
- SQL Server 2016 Management Studio i SQL Server Data Tools for Visual Studio 2015 (wersja 14.0.60311.1 kwietnia 2016 lub nowszy) obsługuje Azure Active Directory uwierzytelnianie. (Uwierzytelnianie usługi Azure AD jest obsługiwane przez **.NET Framework dostawca danych dla programu SqlServer**; co najmniej w wersji .NET Framework 4,6). W związku z tym najnowsze wersje tych narzędzi i aplikacji warstwy danych (DAC i BACPAC) mogą korzystać z uwierzytelniania usługi Azure AD.
- Począwszy od wersji 15.0.1, [narzędzia sqlcmd](/sql/tools/sqlcmd-utility) i obsługi [narzędzia bcp](/sql/tools/bcp-utility) Active Directory interakcyjnego uwierzytelniania z Multi-Factor Authentication.
- Narzędzia SQL Server Data Tools for Visual Studio 2015 wymagają co najmniej wersji 2016 z kwietnia (wersja za14.0.60311.1na). Obecnie użytkownicy usługi Azure AD nie są wyświetlani w SSDT Eksplorator obiektów. Aby obejść ten sposób, Wyświetl użytkowników w [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).
- [Sterownik Microsoft JDBC 6,0 dla SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) obsługuje uwierzytelnianie w usłudze Azure AD. Ponadto zobacz [Ustawianie właściwości połączenia](/sql/connect/jdbc/setting-the-connection-properties).
- Nie można uwierzytelnić przy użyciu uwierzytelniania usługi Azure AD.
- Uwierzytelnianie za pomocą usługi Azure AD jest obsługiwane dla Azure SQL Database i usługi Azure Synapse przy użyciu Azure Portal **importowania bazy danych** i **eksportowania baz danych** . Importowanie i eksportowanie przy użyciu uwierzytelniania usługi Azure AD jest również obsługiwane przez polecenie programu PowerShell.
- Uwierzytelnianie usługi Azure AD jest obsługiwane w przypadku SQL Database, wystąpienia zarządzanego SQL i platformy Azure Synapse przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie i Zarządzanie uwierzytelnianiem w usłudze Azure AD za pomocą SQL Database lub Azure Synapse](authentication-aad-configure.md) i [SQL Server-AZ SQL Server](/cli/azure/sql/server).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć i wypełnić wystąpienie usługi Azure AD, a następnie skonfigurować je przy użyciu Azure SQL Database, wystąpienia zarządzanego SQL lub Azure Synapse, zobacz [Konfigurowanie uwierzytelniania Azure Active Directory i zarządzanie nim za pomocą SQL Database, wystąpienia zarządzanego SQL lub usługi Azure Synapse](authentication-aad-configure.md).
- Aby zapoznać się z samouczkiem dotyczącym używania podmiotów zabezpieczeń serwera usługi Azure AD (logowania) z wystąpieniem zarządzanym SQL, zobacz [nazwy główne serwera usługi Azure AD (nazwy logowania) z wystąpieniem zarządzanym SQL](../managed-instance/aad-security-configure-tutorial.md)
- Aby zapoznać się z omówieniem nazw logowania, użytkowników, ról bazy danych i uprawnień w SQL Database, zobacz [logowania, użytkowników, role bazy danych i uprawnienia](logins-create-manage.md).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](/sql/relational-databases/security/authentication-access/database-level-roles) (Role bazy danych).
- Aby zapoznać się ze składnią tworzenia podmiotów zabezpieczeń serwera usługi Azure AD (logowania) dla wystąpienia zarządzanego SQL, zobacz  [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).
- Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
