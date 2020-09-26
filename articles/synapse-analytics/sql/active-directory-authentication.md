---
title: Azure Active Directory
description: Dowiedz się, jak używać Azure Active Directory do uwierzytelniania za pomocą SQL Database, wystąpienia zarządzanego i Synapse SQL
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 4f1d955999ce44e8ef3d511d67d6a64c98120b62
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289416"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Uwierzytelnianie za pomocą usługi Synapse SQL przy użyciu uwierzytelniania Azure Active Directory

Uwierzytelnianie Azure Active Directory to mechanizm, który nawiązuje połączenie z [usługą Azure Synapse Analytics](../overview-faq.md) przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD).

Za pomocą uwierzytelniania w usłudze Azure AD można centralnie zarządzać tożsamościami użytkowników mającymi dostęp do usługi Azure Synapse, aby uprościć zarządzanie uprawnieniami. Oto przykładowe korzyści:

- Stanowi alternatywę dla zwykłego uwierzytelniania nazwy użytkownika i hasła.
- Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników między serwerami.
- Umożliwia obrót hasła w jednym miejscu.
- Klienci mogą zarządzać uprawnieniami przy użyciu zewnętrznych grup (Azure AD).
- Może on wyeliminować przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i inne formy uwierzytelniania obsługiwane przez Azure Active Directory.
- Usługa Azure AD obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z usługą Azure Synapse.
- Uwierzytelnianie w usłudze Azure AD obsługuje uwierzytelnianie w usługach ADFS (Domain Federation) lub natywne uwierzytelnienie użytkownika/hasła dla lokalnego Azure Active Directory bez synchronizacji z domeną.
- Usługa Azure AD obsługuje połączenia z programu SQL Server Management Studio, które używają uniwersalnego uwierzytelniania usługi Active Directory obejmującej usługę Multi-Factor Authentication (MFA).  Funkcja MFA obejmuje silne uwierzytelnianie z szerokim zakresem łatwych weryfikacji, takich jak rozmowa telefoniczna, wiadomość tekstowa, karty inteligentne z numerem PIN lub powiadomieniem aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz temat [Obsługa usługi Azure AD MFA przy użyciu programu Synapse SQL](mfa-authentication.md).
- Usługa Azure AD obsługuje podobne połączenia z narzędziami SQL Server Data Tools (SSDT), które używają interaktywnego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Azure Active Directory support in SQL Server Data Tools (SSDT) (Obsługa usługi Azure Active Directory w narzędziach SQL Server Data Tools (SSDT))](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Czynności konfiguracyjne obejmują następujące procedury w celu skonfigurowania i użycia uwierzytelniania Azure Active Directory.

1. Utwórz i wypełnij usługę Azure AD.
2. Tworzenie tożsamości Azure Active Directory
3. Przypisywanie roli do utworzonej tożsamości Azure Active Directory w obszarze roboczym Synapse (wersja zapoznawcza)
4. Nawiązywanie połączenia z programem Synapse Studio przy użyciu tożsamości usługi Azure AD.

## <a name="aad-pass-through-in-azure-synapse-analytics"></a>Przekazywanie usługi AAD w usłudze Azure Synapse Analytics

Usługa Azure Synapse Analytics umożliwia dostęp do danych w usłudze Data Lake przy użyciu tożsamości Azure Active Directory.

Zdefiniowanie praw dostępu do plików i danych, które są przestrzegane w różnych aparatach danych, pozwala uprościć rozwiązania Data Lake, mając jedno miejsce, w którym zdefiniowano uprawnienia zamiast konieczności definiowania ich w wielu miejscach.

## <a name="trust-architecture"></a>Architektura zaufania

Poniższy diagram wysokiego poziomu podsumowuje architekturę rozwiązania przy użyciu uwierzytelniania usługi Azure AD z Synapse SQL. Aby można było obsługiwać natywne hasło użytkownika usługi Azure AD, jest uwzględniana tylko część w chmurze i usługa Azure AD/Synapse Synapse SQL. Aby zapewnić obsługę uwierzytelniania federacyjnego (lub użytkownika/hasła dla poświadczeń systemu Windows), wymagana jest komunikacja z blokiem ADFS. Strzałki oznaczają ścieżki komunikacji.

![Diagram uwierzytelniania usługi AAD](./media/aad-authentication/1-active-directory-authentication-diagram.png)

Na poniższym diagramie przedstawiono relacje Federacji, zaufania i hostingu, które umożliwiają klientowi łączenie się z bazą danych przez przesłanie tokenu. Token jest uwierzytelniany przez usługę Azure AD i jest traktowany jako zaufany przez bazę danych. 

Klient 1 może reprezentować Azure Active Directory z natywnymi użytkownikami lub usługą Azure AD z użytkownikami federacyjnymi. Klient 2 oznacza możliwe rozwiązanie, w tym importowani użytkownicy; w tym przykładzie pochodzą z Azure Active Directory federacyjnej z usługą ADFS, które są synchronizowane z Azure Active Directory. 

Ważne jest, aby zrozumieć, że dostęp do bazy danych przy użyciu uwierzytelniania usługi Azure AD wymaga, aby subskrypcja hostingu była skojarzona z usługą Azure AD. Ta sama subskrypcja musi być używana do tworzenia SQL Server hostowania Azure SQL Database lub puli SQL.

![Relacja subskrypcji](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Struktura administratora

W przypadku korzystania z uwierzytelniania usługi Azure AD istnieją dwa konta administratora dla Synapse SQL; oryginalny administrator SQL Server i administrator usługi Azure AD. Tylko administrator oparty na koncie usługi Azure AD może utworzyć pierwszego użytkownika zawartej bazy danych usługi Azure AD w bazie danych użytkownika. 

Identyfikator logowania administratora usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli administrator jest kontem grupy, może być używany przez dowolnego członka grupy, co pozwala na wielu administratorów usługi Azure AD dla wystąpienia SQL Synapse. 

Użycie konta grupy jako administrator zwiększa możliwości zarządzania, umożliwiając centralne Dodawanie i usuwanie członków grupy w usłudze Azure AD bez zmiany użytkowników ani uprawnień w obszarze roboczym Synapse Analytics. W dowolnej chwili można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę).

![Struktura administratora](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Uprawnienia

Aby utworzyć nowych użytkowników, musisz mieć odpowiednie `ALTER ANY USER` uprawnienia w bazie danych programu. `ALTER ANY USER`Uprawnienie można przyznać każdemu użytkownikowi bazy danych. `ALTER ANY USER`Uprawnienie jest również przechowywane przez konta administratora serwera oraz użytkowników bazy danych z `CONTROL ON DATABASE` `ALTER ON DATABASE` uprawnieniami lub dla tej bazy danych oraz przez członków `db_owner` roli bazy danych.

Aby utworzyć użytkownika zawartej bazy danych w programie Synapse SQL, należy połączyć się z bazą danych lub wystąpieniem przy użyciu tożsamości usługi Azure AD. Aby utworzyć pierwszego zawartego użytkownika bazy danych, musisz nawiązać połączenie z bazą danych przy użyciu administratora usługi Azure AD (który jest właścicielem bazy danych). 

Każde uwierzytelnianie usługi Azure AD jest możliwe tylko wtedy, gdy administrator usługi Azure AD został utworzony dla Synapse SQL. Jeśli administrator Azure Active Directory został usunięty z serwera, istniejące Azure Active Directory użytkownicy utworzeni wcześniej w programie Synapse SQL nie mogą już łączyć się z bazą danych przy użyciu poświadczeń Azure Active Directory.
 
## <a name="azure-ad-features-and-limitations"></a>Funkcje i ograniczenia dotyczące usługi Azure AD

- Następujące elementy członkowskie usługi Azure AD mogą być obsługiwane w programie Synapse SQL:

  - Natywni Członkowie: członek utworzony w usłudze Azure AD w domenie zarządzanej lub w domenie klienta. Aby uzyskać więcej informacji, zobacz [Dodawanie własnej nazwy domeny do usługi Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
  - Członkowie domeny federacyjnej: członek utworzony w usłudze Azure AD z domeną federacyjną. Aby uzyskać więcej informacji, zobacz [Microsoft Azure teraz obsługuje Federacji z systemem Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Zaimportowani członkowie z innych reklam, którzy są członkami domeny natywnej lub federacyjnej.
  - Grupy Active Directory utworzone jako grupy zabezpieczeń.

- Użytkownicy usługi Azure AD będący częścią grupy, która ma `db_owner` rolę serwera, nie mogą używać SKŁADNI **[poświadczeń tworzenia zakresu bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** dla Synapse SQL. Zostanie wyświetlony następujący błąd:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Przyznaj `db_owner` roli bezpośrednio użytkownikowi usługi Azure AD, aby wyeliminować problem z **poświadczeniami tworzenia zakresu bazy danych** .

- Te funkcje systemowe zwracają wartości NULL, jeśli są wykonywane w ramach podmiotów zabezpieczeń usługi Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connect-using-azure-ad-identities"></a>Nawiązywanie połączenia przy użyciu tożsamości usługi Azure AD

Azure Active Directory Authentication obsługuje następujące metody łączenia się z bazą danych przy użyciu tożsamości usługi Azure AD:

- Azure Active Directory hasło
- Azure Active Directory zintegrowany
- Azure Active Directory Universal with MFA
- Korzystanie z uwierzytelniania za pomocą tokenu aplikacji

Następujące metody uwierzytelniania są obsługiwane dla podmiotów zabezpieczeń serwera usługi Azure AD (w**publicznej wersji zapoznawczej**):

- Azure Active Directory hasło
- Azure Active Directory zintegrowany
- Azure Active Directory Universal with MFA

### <a name="additional-considerations"></a>Dodatkowe zagadnienia

- W celu zwiększenia możliwości zarządzania zalecamy udostępnienie dedykowanej grupy usługi Azure AD jako administrator.
- W dowolnej chwili można skonfigurować tylko jednego administratora usługi Azure AD (użytkownika lub grupę) dla puli SQL Synapse.
  - Dodanie podmiotów zabezpieczeń serwera usługi Azure AD dla programu SQL na żądanie (wersja zapoznawcza) umożliwia tworzenie wielu podmiotów zabezpieczeń serwera usługi Azure AD, które można dodać do `sysadmin` roli.
- Tylko administrator usługi Azure AD dla Synapse SQL może początkowo połączyć się z Synapse SQL przy użyciu konta Azure Active Directory. Administrator Active Directory może skonfigurować kolejnych użytkowników bazy danych usługi Azure AD.
- Zalecamy ustawienie limitu czasu połączenia na 30 sekund.
- SQL Server 2016 Management Studio i SQL Server Data Tools for Visual Studio 2015 (wersja 14.0.60311.1 kwietnia 2016 lub nowszy) obsługuje Azure Active Directory uwierzytelnianie. (Uwierzytelnianie usługi Azure AD jest obsługiwane przez **.NET Framework dostawca danych dla programu SqlServer**; co najmniej w wersji .NET Framework 4,6). Dlatego najnowsze wersje tych narzędzi i aplikacji warstwy danych (DAC i. BACPAC) może korzystać z uwierzytelniania usługi Azure AD.
- Począwszy od wersji 15.0.1, [narzędzia sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) i obsługi [narzędzia bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Active Directory interakcyjnego uwierzytelniania przy użyciu usługi MFA.
- Narzędzia SQL Server Data Tools for Visual Studio 2015 wymagają co najmniej wersji 2016 z kwietnia (wersja za14.0.60311.1na). Obecnie użytkownicy usługi Azure AD nie są wyświetlani w SSDT Eksplorator obiektów. Aby obejść ten element, Wyświetl użytkowników w obszarze [sys. database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- [Sterownik Microsoft JDBC 6,0 dla SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) obsługuje uwierzytelnianie w usłudze Azure AD. Ponadto zobacz [Ustawianie właściwości połączenia](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem dostępu i kontroli w programie Synapse SQL, zobacz [Synapse SQL Access Control](../sql/access-control.md).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (Role bazy danych).

 