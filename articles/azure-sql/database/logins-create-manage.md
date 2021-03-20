---
title: Autoryzuj dostęp serwera i bazy danych przy użyciu nazw logowania i kont użytkowników
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Dowiedz się, jak Azure SQL Database, wystąpienie zarządzane SQL i usługa Azure Synapse uwierzytelniają użytkowników w celu uzyskania dostępu przy użyciu nazw logowania i kont użytkowników. Dowiedz się również, jak przyznać role bazy danych i jawne uprawnienia do autoryzacji logowań i użytkowników w celu wykonywania akcji i danych zapytań.
keywords: zabezpieczenia bazy danych sql, zarządzanie zabezpieczeniami bazy danych, zabezpieczenia logowania, zabezpieczenia bazy danych, dostęp do bazy danych
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: d03bce1566d4f56a576c980723571f587296236f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452425"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Autoryzowanie dostępu do bazy danych w usługach SQL Database, SQL Managed Instance i Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym artykule omówiono następujące informacje:

- Opcje konfigurowania Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics w celu umożliwienia użytkownikom wykonywania zadań administracyjnych i uzyskiwania dostępu do danych przechowywanych w tych bazach danych.
- Konfiguracja dostępu i autoryzacji po pierwszym utworzeniu nowego serwera.
- Jak dodać nazwy logowania i konta użytkowników w bazie danych Master i konta użytkowników, a następnie przyznać te konta uprawnienia administracyjne.
- Jak dodać konta użytkowników w bazach danych użytkowników, skojarzone z nazwami logowania lub kontami użytkowników.
- Skonfiguruj konta użytkowników z uprawnieniami w bazach danych użytkowników, korzystając z ról bazy danych i jawnych uprawnień.

> [!IMPORTANT]
> Bazy danych w Azure SQL Database, wystąpienie zarządzane usługi Azure SQL i usługa Azure Synapse są określane zbiorczo w pozostałej części tego artykułu jako bazy danych, a serwer odwołuje się do [serwera](logical-servers.md) , który zarządza bazami danych dla Azure SQL Database i Azure Synapse.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

[**Uwierzytelnianie**](security-overview.md#authentication) to proces potwierdzania tożsamości użytkownika. Użytkownik nawiązuje połączenie z bazą danych przy użyciu konta użytkownika.
Gdy użytkownik próbuje nawiązać połączenie z bazą danych, udostępnia informacje o koncie użytkownika i uwierzytelnianiu. Użytkownik jest uwierzytelniany przy użyciu jednej z następujących dwóch metod uwierzytelniania:

- [Uwierzytelnianie SQL](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Przy użyciu tej metody uwierzytelniania użytkownik przesyła nazwę konta użytkownika i skojarzone hasło w celu nawiązania połączenia. To hasło jest przechowywane w bazie danych Master dla kont użytkowników połączonych z logowaniem lub przechowywanych w bazie danych zawierającej konta użytkowników, które *nie* są połączone z logowaniem.
- [Uwierzytelnianie usługi Azure Active Directory](authentication-aad-overview.md)

  Przy użyciu tej metody uwierzytelniania użytkownik przesyła nazwę konta użytkownika i żąda, aby usługa korzystała z informacji poświadczenie przechowywanych w Azure Active Directory (Azure AD).

**Logowania i użytkownicy**: konto użytkownika w bazie danych może być skojarzone z nazwą logowania przechowywaną w bazie danych Master lub może być nazwą użytkownika przechowywaną w pojedynczej bazie danych.

- **Identyfikator logowania** to pojedyncze konto w bazie danych Master, do którego można połączyć konto użytkownika w co najmniej jednej bazie danych. W przypadku nazwy logowania informacje o poświadczeniach dla konta użytkownika przechowywane są z nazwą logowania.
- **Konto użytkownika** to pojedyncze konto w dowolnej bazie danych, które może być, ale nie musi być powiązane z logowaniem. W przypadku konta użytkownika, które nie jest powiązane z nazwą logowania, informacje o poświadczeniach są przechowywane z kontem użytkownika.

[**Autoryzacja**](security-overview.md#authorization) dostępu do danych i wykonywanie różnych akcji są zarządzane przy użyciu ról bazy danych i jawnych uprawnień. Autoryzacja odnosi się do uprawnień przypisanych do użytkownika i określa, co użytkownik może zrobić. Autoryzacja jest kontrolowana za pomocą [członkostw roli](/sql/relational-databases/security/authentication-access/database-level-roles) bazy danych konta użytkownika i [uprawnień na poziomie obiektów](/sql/relational-databases/security/permissions-database-engine). Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Istniejące dane logowania i konta użytkowników po utworzeniu nowej bazy danych

Podczas pierwszego wdrażania usługi Azure SQL należy określić nazwę logowania administratora i skojarzone hasło dla tej nazwy logowania. To konto administracyjne nosi nazwę **administrator serwera**. Następująca konfiguracja nazw logowania i użytkowników w bazach danych Master i User odbywa się podczas wdrażania:

- Logowanie SQL z uprawnieniami administracyjnymi jest tworzone przy użyciu podanej nazwy logowania. [Identyfikator logowania](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) to pojedyncze konto użytkownika służące do logowania się do SQL Database, wystąpienia zarządzanego SQL i usługi Azure Synapse.
- Ta nazwa logowania ma przyznane pełne uprawnienia administracyjne do wszystkich baz danych jako [podmiot zabezpieczeń na poziomie serwera](/sql/relational-databases/security/authentication-access/principals-database-engine). Nazwa logowania ma wszystkie dostępne uprawnienia i nie może być ograniczona. W wystąpieniu zarządzanym SQL ta nazwa logowania jest dodawana do [stałej roli serwera sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) (Ta rola nie istnieje w Azure SQL Database).
- [](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) `dbo` Dla tej nazwy logowania w każdej bazie danych użytkownika zostanie utworzone konto użytkownika o nazwie. Użytkownik [dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) ma wszystkie uprawnienia baz danych w bazie danych i jest mapowany na `db_owner` stałą rolę bazy danych. Dodatkowe role stałych baz danych zostały omówione w dalszej części tego artykułu.

Aby zidentyfikować konta administratorów dla bazy danych, Otwórz Azure Portal i przejdź do karty **Właściwości** serwera lub wystąpienia zarządzanego.

![Administratorzy serwera SQL](./media/logins-create-manage/sql-admins.png)

![Zrzut ekranu, który podświetla opcję menu właściwości.](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> Nazwy logowania administratora nie można zmienić po jej utworzeniu. Aby zresetować hasło administratora serwera, przejdź do [Azure Portal](https://portal.azure.com), kliknij pozycję **serwery SQL**, wybierz serwer z listy, a następnie kliknij przycisk **Resetuj hasło**. Aby zresetować hasło dla wystąpienia zarządzanego SQL, przejdź do Azure Portal, kliknij wystąpienie, a następnie kliknij przycisk **Resetuj hasło**. Możesz również użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Tworzenie dodatkowych logowań i użytkowników z uprawnieniami administracyjnymi

W tym momencie serwer lub wystąpienie zarządzane jest konfigurowane tylko na potrzeby dostępu przy użyciu jednego identyfikatora logowania SQL i konta użytkownika. Aby utworzyć dodatkowe nazwy logowania z pełnymi lub częściowymi uprawnieniami administracyjnymi, dostępne są następujące opcje (w zależności od trybu wdrożenia):

- **Utwórz konto administratora Azure Active Directory z pełnymi uprawnieniami administracyjnymi**

  Włącz uwierzytelnianie Azure Active Directory i Utwórz identyfikator logowania administratora usługi Azure AD. Jedno konto Azure Active Directory można skonfigurować jako administrator wdrożenia usługi Azure SQL z pełnymi uprawnieniami administracyjnymi. To konto może być kontem użytkownika lub grupy zabezpieczeń. **Należy** skonfigurować administratora usługi Azure AD, jeśli chcesz używać kont usługi Azure AD do nawiązywania połączeń z usługą SQL Database, wystąpieniem zarządzanym SQL lub usługą Azure Synapse. Aby uzyskać szczegółowe informacje na temat włączania uwierzytelniania usługi Azure AD dla wszystkich typów wdrożeń usługi Azure SQL, zobacz następujące artykuły:

  - [Używanie uwierzytelniania Azure Active Directory na potrzeby uwierzytelniania przy użyciu programu SQL Server](authentication-aad-overview.md)
  - [Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania w usłudze Azure Active Directory i zarządzanie nim przy użyciu języka SQL)](authentication-aad-configure.md)

- **W wystąpieniu zarządzanym SQL utwórz identyfikatory logowania SQL z pełnymi uprawnieniami administracyjnymi**

  - Utwórz dodatkową nazwę logowania SQL w bazie danych Master.
  - Dodaj nazwę logowania do [stałej roli serwera sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) przy użyciu instrukcji [ALTER Server role](/sql/t-sql/statements/alter-server-role-transact-sql) . Ta nazwa logowania będzie miała pełne uprawnienia administracyjne.
  - Alternatywnie możesz utworzyć [Identyfikator logowania usługi Azure AD](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) przy użyciu składni [tworzenia nazwy logowania](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) .

- **W SQL Database Utwórz nazwy logowania SQL z ograniczonymi uprawnieniami administracyjnymi**

  - Utwórz dodatkową nazwę logowania SQL w bazie danych Master.
  - Utwórz konto użytkownika w bazie danych Master skojarzonej z nowym logowaniem.
  - Dodaj konto użytkownika do elementu `dbmanager` , `loginmanager` roli lub obu w `master` bazie danych przy użyciu instrukcji [ALTER role](/sql/t-sql/statements/alter-role-transact-sql) (dla usługi Azure Synapse, użyj instrukcji [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > `dbmanager``loginmanager`role i **nie** odnoszą się do wdrożeń wystąpienia zarządzanego SQL.

  Członkowie tych [specjalnych ról głównych baz danych](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) dla Azure SQL Database mają uprawnienia do tworzenia baz danych i zarządzania nimi oraz do tworzenia nazw logowania i zarządzania nimi. W bazach danych utworzonych przez użytkownika, który jest członkiem `dbmanager` roli, członek jest mapowany na `db_owner` stałą rolę bazy danych i może logować się do tej bazy danych i zarządzać nią przy użyciu `dbo` konta użytkownika. Te role nie mają jawnych uprawnień poza główną bazą danych.

  > [!IMPORTANT]
  > Nie można utworzyć dodatkowego identyfikatora logowania SQL z pełnymi uprawnieniami administracyjnymi w programie SQL Database.

## <a name="create-accounts-for-non-administrator-users"></a>Tworzenie kont dla użytkowników niebędących administratorami

Można utworzyć konta dla użytkowników niebędących administratorami przy użyciu jednej z dwóch metod:

- **Utwórz logowanie**

  Utwórz identyfikator logowania SQL w bazie danych Master. Następnie utwórz konto użytkownika w każdej bazie danych, do której użytkownik będzie potrzebował dostępu, i skojarz konto użytkownika z tym logowaniem. Ta metoda jest preferowana, gdy użytkownik musi uzyskać dostęp do wielu baz danych i chcesz zachować synchronizację haseł. Jednak takie podejście ma złożoność w przypadku używania z replikacją geograficzną, ponieważ nazwa logowania musi być utworzona zarówno na serwerze podstawowym, jak i na serwerze pomocniczym. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zabezpieczeń Azure SQL Database i zarządzanie nimi na potrzeby przywracania geograficznego lub przełączania do trybu failover](active-geo-replication-security-configure.md).
- **Tworzenie konta użytkownika**

  Utwórz konto użytkownika w bazie danych, do której użytkownik potrzebuje dostępu (nazywany również [zawartym użytkownikiem](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Za pomocą SQL Database można zawsze utworzyć ten typ konta użytkownika.
  - Korzystając z wystąpienia zarządzanego SQL, które obsługuje [podmioty zabezpieczeń serwera usługi Azure AD](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities), można utworzyć konta użytkowników w celu uwierzytelniania w wystąpieniu zarządzanym SQL bez konieczności tworzenia użytkowników bazy danych jako zawartych użytkowników bazy danych.

  W ramach tego podejścia informacje o uwierzytelnianiu użytkownika są przechowywane w poszczególnych bazach danych i automatycznie replikowane do baz danych replikowanych geograficznie. Jeśli jednak to samo konto istnieje w wielu bazach danych i używasz uwierzytelniania SQL platformy Azure, hasła należy synchronizować ręcznie. Ponadto, jeśli użytkownik ma konto w różnych bazach danych z różnymi hasłami, zapamiętanie tych haseł może stać się problemem.

> [!IMPORTANT]
> Aby utworzyć zawartych użytkowników mapowanych na tożsamości usługi Azure AD, musisz zalogować się przy użyciu konta usługi Azure AD, które jest administratorem w bazie danych programu w Azure SQL Database. W wystąpieniu zarządzanym SQL logowanie do `sysadmin` usługi Azure AD z uprawnieniami może również tworzyć dane logowania lub użytkownika w usłudze Active Directory.

Przykłady pokazujące sposób tworzenia logowań i użytkowników znajdują się w temacie:

- [Utwórz nazwę logowania dla Azure SQL Database](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Utwórz nazwę logowania dla wystąpienia zarządzanego Azure SQL](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Utwórz nazwę logowania dla usługi Azure Synapse](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Utwórz użytkownika](/sql/t-sql/statements/create-user-transact-sql#examples)
- [Tworzenie użytkowników zawartych w usłudze Azure AD](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Aby zapoznać się z samouczkiem dotyczącym zabezpieczeń, który obejmuje tworzenie użytkowników w Azure SQL Database, zobacz [Samouczek: bezpieczny Azure SQL Database](secure-database-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Korzystanie z stałych i niestandardowych ról baz danych

Po utworzeniu konta użytkownika w bazie danych, na podstawie nazwy logowania lub jako zawartego użytkownika, można autoryzować tego użytkownika do wykonywania różnych akcji i uzyskiwania dostępu do danych w określonej bazie danych. Aby autoryzować dostęp, można użyć następujących metod:

- **Stałe role bazy danych**

  Dodaj konto użytkownika do [stałej roli bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles). Dostępne są 9 stałych ról bazy danych, z których każda ma zdefiniowany zestaw uprawnień. Najbardziej popularne role bazy danych to: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** i **db_denydatareader**. Rola **db_owner** jest najczęściej używana do udzielenia pełnych uprawnień jedynie niewielkiej liczbie użytkowników. Inne ustalone role bazy danych ułatwiają szybkie tworzenie prostej bazy danych, ale nie zaleca się ich używania w większości przypadków tworzenia produkcyjnych baz danych. Na przykład rola stałej bazy danych **db_datareader** przyznaje dostęp do odczytu każdej tabeli w bazie danych, która jest większa niż jest absolutnie konieczna.

  - Aby dodać użytkownika do stałej roli bazy danych:

    - W Azure SQL Database Użyj instrukcji [ALTER role](/sql/t-sql/statements/alter-role-transact-sql) . Aby zapoznać się z przykładami, zobacz [ALTER role przykłady](/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse, użyj instrukcji [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) . Przykłady zawierają [przykłady sp_addrolemember](/sql/t-sql/statements/alter-role-transact-sql).

- **Niestandardowa rola bazy danych**

  Utwórz niestandardową rolę bazy danych przy użyciu instrukcji [Create role](/sql/t-sql/statements/create-role-transact-sql) . Rola niestandardowa umożliwia tworzenie własnych zdefiniowanych przez użytkownika ról baz danych i staranne przyznawanie każdej roli najniższych uprawnień niezbędnych dla potrzeb firmy. Następnie można dodać użytkowników do roli niestandardowej. Gdy użytkownik jest członkiem wielu ról, łączą one uprawnienia ich wszystkich.
- **Udziel uprawnień bezpośrednio**

  Przyznaj bezpośrednio [uprawnienia](/sql/relational-databases/security/permissions-database-engine) kontu użytkownika. Istnieje ponad 100 uprawnień, których można indywidualnie udzielić lub odmówić w usłudze SQL Database. Wiele z tych uprawnień jest zagnieżdżonych. Na przykład uprawnienie `UPDATE` na schemacie obejmuje `UPDATE` uprawnienie dla każdej tabeli na tym schemacie. Podobnie jak w przypadku większości systemów, odmowa przyznania uprawnienia kasuje przyznanie. Ze względu na zagnieżdżoną naturę uprawnień oraz ich liczbę zaprojektowanie systemu zabezpieczającego bazę danych w prawidłowy sposób może wymagać starannej analizy. Rozpocznij od listy uprawnień [Uprawnienia (aparat bazy danych)](/sql/relational-databases/security/permissions-database-engine) i przejrzyj [obszerny wykaz](/sql/relational-databases/security/media/database-engine-permissions.png) uprawnień.

## <a name="using-groups"></a>Korzystanie z grup

Wydajne zarządzanie dostępem korzysta z uprawnień przypisanych do Active Directory grup zabezpieczeń i stałych lub niestandardowych ról zamiast do poszczególnych użytkowników.

- W przypadku korzystania z uwierzytelniania Azure Active Directory należy umieścić Azure Active Directory użytkowników w Azure Active Directory grupie zabezpieczeń. Utwórz użytkownika zawartej bazy danych dla tej grupy. Dodaj co najmniej jednego użytkownika bazy danych jako członka do niestandardowych lub wbudowanych ról bazy danych z określonymi uprawnieniami właściwymi dla tej grupy użytkowników.

- W przypadku korzystania z uwierzytelniania SQL należy utworzyć użytkowników zawartej bazy danych w bazie danych programu. Umieść co najmniej jednego użytkownika bazy danych w niestandardowej roli bazy danych z określonymi uprawnieniami, które są odpowiednie dla tej grupy użytkowników.

  > [!NOTE]
  > Można również użyć grup dla użytkowników nienależących do bazy danych.

Należy zapoznać się z następującymi funkcjami, których można użyć do ograniczania lub podnoszenia uprawnień:

- [Personifikacji](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) i [podpisywania modułów](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) można używać do bezpiecznego tymczasowego podnoszenia poziomu uprawnień.
- [Zabezpieczeń na poziomie wiersza](/sql/relational-databases/security/row-level-security) można używać do ograniczania zbioru wierszy, do których użytkownik może uzyskać dostęp.
- [Maskowania danych](dynamic-data-masking-overview.md) można używać do ograniczania ujawniania danych wrażliwych.
- [Procedury składowane](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) umożliwiają ograniczenie czynności wykonywanych w bazie danych.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem wszystkich Azure SQL Database i funkcji zabezpieczeń wystąpienia zarządzanego SQL, zobacz [Omówienie zabezpieczeń](security-overview.md).