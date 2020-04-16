---
title: Uwierzytelnianie SQL
description: Dowiedz się więcej o uwierzytelnianiu SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 2b80efa30ac7e04b9eb21dd6f8a39ab4ee90adf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424853"
---
# <a name="sql-authentication"></a>Uwierzytelnianie SQL

Usługa Azure Synapse Analytics ma dwa rozmiary formularza SQL, które umożliwiają kontrolowanie zużycia zasobów. W tym artykule wyjaśniono, jak dwa formaty sterujące uwierzytelnianiem użytkownika.

Aby autoryzować synapse SQL, można użyć dwóch typów autoryzacji:

- Autoryzacja AAD
- Autoryzacja SQL

Autoryzacja usługi AAD opiera się na usłudze Azure Active Directory i umożliwia tworzenie jednego miejsca do zarządzania użytkownikami. Autoryzacja SQL umożliwia starsze aplikacje do korzystania synapse SQL w dobrze znany sposób.

## <a name="administrative-accounts"></a>Rachunki administracyjne

Istnieją dwa konta z uprawnieniami administracyjnymi (**Administrator serwera** i **Administrator usługi Active Directory**), które funkcjonują jako administratorzy. Aby zidentyfikować te konta administratora dla serwera SQL, otwórz witrynę Azure portal i przejdź do karty Właściwości programu Synapse SQL.

![Administratorzy serwera SQL](./media/sql-authentication/sql-admins.png)

- **Administrator serwera**

  Podczas tworzenia usługi Azure Synapse Analytics należy wyznaczyć **login administratora serwera**. Serwer SQL tworzy to konto jako identyfikator logowania w bazie danych master. To konto używa do połączenia uwierzytelnienia programu SQL Server (nazwy użytkownika i hasła). Może istnieć tylko jedno z tych kont.

- **Administrator usługi Azure Active Directory**

  Jako konto administratora można również skonfigurować jedno konto usługi Azure Active Directory (indywidualne lub grupy zabezpieczeń). Jest opcjonalne skonfigurowanie administratora usługi Azure AD, ale administrator usługi Azure AD **musi** być skonfigurowany, jeśli chcesz używać kont usługi Azure AD do łączenia się z synapse SQL.

Konta **administratora serwera** i **administratora usługi Azure AD** mają następujące cechy:

- Są jedynymi kontami, które mogą automatycznie łączyć się z dowolną bazą danych SQL na serwerze. (Aby połączyć się z bazą danych użytkownika, inne konta muszą być właścicielem bazy danych lub mieć konto użytkownika w bazie danych użytkownika).
- Te konta korzystają z baz danych użytkowników jako użytkownik `dbo` i mają wszystkie uprawnienia w bazach danych użytkowników. (Właściciel bazy danych użytkownika również korzysta z bazy danych jako użytkownik `dbo`).
- Nie należy `master` wprowadzać `dbo` bazy danych jako użytkownika i mieć ograniczone uprawnienia w wzorcu.
- Nie **not** są członkami standardowej `sysadmin` roli serwera stacjonarnego SQL Server, która nie jest dostępna w bazie danych SQL.  
- Może tworzyć, zmieniać i upuszczać bazy danych, logowania, użytkowników w regułach zapory adresowej i adresowej ip na poziomie serwera.
- Można dodawać i `dbmanager` usuwać członków do ról i. `loginmanager`
- Można wyświetlić tabelę `sys.sql_logins` system.

## <a name="sql-on-demand-preview"></a>SQL na żądanie (wersja zapoznawcza)

Aby zarządzać użytkownikami ma dostęp do sql na żądanie, można użyć poniższych instrukcji.

Aby utworzyć identyfikator LOGOWANIA do języka SQL na żądanie, należy użyć następującej składni:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
Gdy istnieje identyfikator logowania, można utworzyć użytkowników w poszczególnych bazach danych wewnątrz punktu końcowego SQL na żądanie i udzielić wymaganych uprawnień do tych użytkowników. Aby utworzyć użycie, można użyć następującej składni:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Po utworzeniu logowania i użytkownika można użyć zwykłej składni programu SQL Server do przyznania praw.

## <a name="sql-pool"></a>Pula SQL

### <a name="administrator-access-path"></a>Ścieżka dostępu administratora

Po poprawnym skonfigurowaniu zapory na poziomie serwera konta **Administrator serwera SQL** i **Administrator usługi Azure Active Directory** mogą łączyć się przy użyciu narzędzi klienckich, takich jak SQL Server Management Studio lub SQL Server Data Tools. Tylko najnowsze narzędzia oferują wszystkie funkcje i możliwości. 

Na poniższym diagramie przedstawiono typową konfigurację dla dwóch kont administratora:
 
![konfiguracja dwóch rachunków administracyjnych](./media/sql-authentication/1sql-db-administrator-access.png)

Używając otwartego portu w zaporze na poziomie serwera, administratorzy mogą połączyć się z dowolną bazą danych SQL.

### <a name="database-creators"></a>Kreatory bazy danych

Jedną z tych ról administracyjnych jest rola **dbmanager.** Członkowie tej roli mogą tworzyć nowe bazy danych. Aby użyć tej roli, należy utworzyć użytkownika w bazie danych `master` i dodać go do roli bazy danych **dbmanager**. 

Aby utworzyć bazę danych, użytkownik musi być użytkownikiem `master` na podstawie logowania programu SQL Server w bazie danych lub zawierał użytkownika bazy danych na podstawie użytkownika usługi Azure Active Directory.

1. Korzystając z konta administratora, połącz się z bazą `master` danych.
2. Utwórz logowanie uwierzytelniania programu SQL Server przy użyciu instrukcji [CREATE LOGIN.](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Przykładowa instrukcja:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Podczas tworzenia nazwy logowania lub użytkownika zawartej bazy danych należy używać silnego hasła. Aby uzyskać więcej informacji, zobacz artykuł [Silne hasła](/sql/relational-databases/security/strong-passwords?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

   W celu poprawy wydajności nazwy logowania (nazwy główne na poziomie serwera) są tymczasowo przechowywane w pamięci podręcznej na poziomie bazy danych. Aby odświeżyć pamięć podręczną uwierzytelniania, zobacz artykuł [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

3. W `master` bazie danych utwórz użytkownika przy użyciu [instrukcji CREATE USER.](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Użytkownik może być użytkownikiem bazy danych zawierającej uwierzytelnianie usługi Azure Active Directory (jeśli skonfigurowano środowisko do uwierzytelniania usługi Azure AD) lub użytkownikiem bazy danych zawierającym uwierzytelnianie programu SQL Server lub użytkownikiem uwierzytelniania programu SQL Server na podstawie logowania uwierzytelniania programu SQL Server (utworzonego w poprzednim kroku). Przykładowe instrukcje:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Dodaj nowego użytkownika do roli bazy danych `master` **dbmanager** przy użyciu instrukcji [ALTER ROLE.](/sql/t-sql/statements/alter-role-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Przykładowe instrukcje:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary;
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > Dbmanager jest rolą bazy danych w bazie danych master, więc do roli dbmanager można dodać tylko użytkownika bazy danych. Do roli na poziomie bazy danych nie można dodać nazwy logowania na poziomie serwera.

5. W razie potrzeby skonfiguruj regułę zapory, aby umożliwić połączenie się nowemu użytkownikowi. (Nowy użytkownik może być objęty istniejącą regułą zapory).

Teraz użytkownik może połączyć się z bazą `master` danych i może tworzyć nowe bazy danych. Konto tworzące bazę danych staje się właścicielem bazy danych.

### <a name="login-managers"></a>Menedżerowie logowania

Druga rola administracyjna to rola menedżera logowania. Członkowie tej roli mogą tworzyć nowe nazwy logowania w bazie danych master. Jeśli chcesz, możesz wykonać te same kroki (utworzenie identyfikatora logowania i użytkownika, a następnie dodanie użytkownika do roli **loginmanager**), aby umożliwić użytkownikowi tworzenie nowych identyfikatorów logowania w bazie danych master. Zazwyczaj identyfikatory logowania nie są konieczne, ponieważ firma Microsoft zaleca korzystanie z użytkowników zawartej bazy danych, którzy przeprowadzają uwierzytelnianie na poziomie bazy danych zamiast użytkowników przeprowadzających uwierzytelnianie w oparciu o identyfikator logowania. Aby uzyskać więcej informacji, zobacz artykuł [Contained Database Users - Making Your Database Portable](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych).

## <a name="non-administrator-users"></a>Użytkownicy niebędący administratorami

Ogólnie rzecz biorąc, konta inne niż administracyjne nie potrzebują dostępu do bazy danych master. Tworzenie użytkowników zawartej bazy danych na poziomie bazy danych przy użyciu instrukcji [CREATE USER (język Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

Użytkownik może być użytkownikiem uwierzytelnienia zawartej bazy danych usługi Azure Active Directory (jeśli skonfigurowano środowisko dla uwierzytelniania usługi Azure AD), użytkownikiem uwierzytelnienia zawartej bazy danych programu SQL Server lub użytkownikiem uwierzytelniania programu SQL Server w oparciu o nazwę logowania uwierzytelniania programu SQL Server (utworzonym w poprzednim kroku).  

Aby utworzyć użytkowników, połącz się z bazą danych i wykonaj instrukcje podobne do następujących:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Początkowo tylko jeden z administratorów lub jeden z właścicieli bazy danych mogą tworzyć użytkowników. Aby autoryzować dodatkowych użytkowników do tworzenia nowych użytkowników, należy udzielić im uprawnienia `ALTER ANY USER` przy użyciu instrukcji, takiej jak:

```sql
GRANT ALTER ANY USER TO Mary;
```

Aby dać dodatkowym użytkownikom pełną kontrolę nad bazą danych, uczynić je członkiem **roli db_owner** stałej bazy danych.

W usłudze Azure `ALTER ROLE` SQL Database użyj instrukcji.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

W puli SQL użyj [sp_addrolemember EXEC](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> Częstym powodem, aby utworzyć użytkownika bazy danych na podstawie logowania serwera bazy danych SQL jest dla użytkowników, którzy potrzebują dostępu do wielu baz danych. Ponieważ użytkownicy zawartej bazy danych są poszczególnymi jednostkami, każda baza danych przechowuje własnego użytkownika i własne hasło. Może to spowodować obciążenie, ponieważ użytkownik musi następnie zapamiętać każde hasło dla każdej bazy danych i może stać się nie do utrzymania, gdy trzeba zmienić wiele haseł dla wielu baz danych. Jednak w przypadku korzystania z sql server logowania i wysokiej dostępności (aktywne replikacji geograficznej i grup pracy awaryjnej), SQL Server logowania muszą być ustawione ręcznie na każdym serwerze. W przeciwnym razie użytkownik bazy danych nie będzie już mapowany do logowania serwera po przejściu w tryb failover i nie będzie mógł uzyskać dostępu do bazy danych po przejściu w tryb failover. 

Aby uzyskać więcej informacji na temat konfigurowania loginów do replikacji geograficznej, zobacz [Konfigurowanie zabezpieczeń usługi Azure SQL Database i zarządzanie nimi w celu przywracania geograficznego lub pracy awaryjnej](../../sql-database/sql-database-geo-replication-security-config.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="configuring-the-database-level-firewall"></a>Konfigurowanie zapory na poziomie bazy danych

Najlepszym rozwiązaniem jest sytuacja, gdy użytkownicy niebędący administratorami mają dostęp do używanych baz danych tylko poprzez zaporę. Zamiast autoryzowania ich adresów IP poprzez zaporę na poziomie serwera i przydzielania dostępu do wszystkich baz danych użyj instrukcji [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), aby skonfigurować zaporę na poziomie bazy danych. Nie można skonfigurować zapory na poziomie bazy danych za pomocą portalu.

### <a name="non-administrator-access-path"></a>Ścieżka dostępu użytkownika niebędącego administratorem

Gdy zapora na poziomie bazy danych jest odpowiednio skonfigurowana, użytkownicy bazy danych mogą łączyć się za pomocą narzędzia klienckiego, takiego jak SQL Server Management Studio lub SQL Server Data Tools. Tylko najnowsze narzędzia oferują wszystkie funkcje i możliwości. Na poniższym diagramie przedstawiono typowe ścieżki dostępu użytkowników niebędących administratorami.

![Ścieżka dostępu użytkownika niebędącego administratorem](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupy i role

Wydajne zarządzanie dostępem obejmuje korzystanie z uprawnień przypisanych do grup i ról, a nie do poszczególnych użytkowników.

- Korzystając z uwierzytelniania usługi Azure Active Directory, umieść użytkowników usługi Azure Active Directory w grupie usługi Azure Active Directory. Utwórz użytkownika zawartej bazy danych dla tej grupy. Umieść co najmniej jednego użytkownika bazy danych w [roli bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a następnie przypisz [uprawnienia](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do roli bazy danych.

- W przypadku uwierzytelniania programu SQL Server utwórz zawartych użytkowników bazy danych w bazie danych. Umieść co najmniej jednego użytkownika bazy danych w [roli bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a następnie przypisz [uprawnienia](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do roli bazy danych.

Role bazy danych mogą być rolami wbudowanymi, takimi jak **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** i **db_denydatareader**. Rola **db_owner** jest najczęściej używana do udzielenia pełnych uprawnień jedynie niewielkiej liczbie użytkowników. Inne ustalone role bazy danych ułatwiają szybkie tworzenie prostej bazy danych, ale nie zaleca się ich używania w większości przypadków tworzenia produkcyjnych baz danych. 

Na przykład ustalona rola bazy danych **db_datareader** pozwala na odczyt każdej tabeli w bazie danych, co nie zawsze jest niezbędne. 

Znacznie lepiej jest używać instrukcji [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx), aby tworzyć własne role użytkownika bazy danych i rozważnie udzielać każdej roli możliwie najniższych uprawnień niezbędnych do zaspokojenia potrzeb biznesowych. Gdy użytkownik jest członkiem wielu ról, łączą one uprawnienia ich wszystkich.

## <a name="permissions"></a>Uprawnienia

Istnieje ponad 100 uprawnień, których można indywidualnie udzielić lub odmówić w usłudze SQL Database. Wiele z tych uprawnień jest zagnieżdżonych. Na przykład uprawnienie `UPDATE` na schemacie obejmuje `UPDATE` uprawnienie dla każdej tabeli na tym schemacie. Podobnie jak w przypadku większości systemów, odmowa przyznania uprawnienia kasuje przyznanie. 

Ze względu na zagnieżdżoną naturę uprawnień oraz ich liczbę zaprojektowanie systemu zabezpieczającego bazę danych w prawidłowy sposób może wymagać starannej analizy. 

Rozpocznij od listy uprawnień [Uprawnienia (aparat bazy danych)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) i przejrzyj [obszerny wykaz](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) uprawnień.

### <a name="considerations-and-restrictions"></a>Uwagi i ograniczenia

Podczas zarządzania logowania i użytkowników w bazie danych SQL, należy wziąć pod uwagę następujące punkty:

- Podczas wykonywania instrukcji musi być nawiązywanie połączenia z **główną** bazą `CREATE/ALTER/DROP DATABASE` danych.
- Użytkownik bazy danych odpowiadający identyfikatorowi logowania **Administrator serwera** nie może zostać zmieniony ani usunięty.
- Domyślnym językiem identyfikatora logowania **Administrator serwera** jest angielski (Stany Zjednoczone).
- Tylko administratorzy (identyfikator logowania **Administrator serwera** lub Administrator usługi Azure AD) i członkowie roli bazy danych **dbmanager** w bazie danych **master** mają uprawnienia do wykonywania instrukcji `CREATE DATABASE` i `DROP DATABASE`.
- Podczas wykonywania instrukcji `CREATE/ALTER/DROP LOGIN` musisz mieć połączenie z bazą danych master. Nie zaleca się jednak używania nazw logowania. Zamiast tego korzystaj z użytkowników zawartej bazy danych.
- Aby połączyć się z bazą danych użytkownika, podaj nazwę bazy danych w parametrach połączenia.
- Tylko główna nazwa logowania na poziomie serwera i członkowie roli bazy danych **loginmanager** w bazie danych **master** mają uprawnienia do wykonywania instrukcji `CREATE LOGIN`, `ALTER LOGIN` i `DROP LOGIN`.
- Podczas wykonywania instrukcji `CREATE/ALTER/DROP LOGIN` i `CREATE/ALTER/DROP DATABASE` w aplikacji ADO.NET niedozwolone jest używanie poleceń sparametryzowanych. Aby uzyskać więcej informacji, zobacz artykuł [Polecenia i parametry](/dotnet/framework/data/adonet/commands-and-parameters?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Podczas wykonywania instrukcji `CREATE/ALTER/DROP DATABASE` i `CREATE/ALTER/DROP LOGIN` każda z tych instrukcji musi być jedyną instrukcją w zadaniu wsadowym języka Transact-SQL. W przeciwnym razie wystąpi błąd. Na przykład następująca instrukcja języka Transact-SQL sprawdza, czy baza danych istnieje. Jeśli baza istnieje, jest wywoływana instrukcja `DROP DATABASE` w celu jej usunięcia. Ponieważ instrukcja `DROP DATABASE` nie jest jedyną instrukcją w zadaniu wsadowym, wykonywanie następującej instrukcji języka Transact-SQL spowoduje błąd.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Zamiast tego należy użyć następującej instrukcji Transact-SQL:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- Podczas wykonywania instrukcji `CREATE USER` z opcją `FOR/FROM LOGIN` musi to być jedyna instrukcja w zadaniu wsadowym języka Transact-SQL.
- Podczas wykonywania instrukcji `ALTER USER` z opcją `WITH LOGIN` musi to być jedyna instrukcja w zadaniu wsadowym języka Transact-SQL.
- Aby wykonać instrukcję `CREATE/ALTER/DROP`, użytkownik musi mieć uprawnienia `ALTER ANY USER` dla bazy danych.
- Gdy właściciel roli database próbuje dodać lub usunąć innego użytkownik bazy danych z roli database, może wystąpić następujący błąd: **User or role 'Name' does not exist in this database** (Użytkownik lub rola „Nazwa” nie istnieje w tej bazie danych). Ten błąd występuje, ponieważ użytkownik nie jest widoczny dla właściciela. Aby rozwiązać ten problem, należy udzielić właścicielowi roli uprawnień `VIEW DEFINITION` do użytkownika. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz artykuł [Contained Database Users - Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Użytkownicy zawartej bazy danych — tworzenie przenośnej bazy danych).
 
