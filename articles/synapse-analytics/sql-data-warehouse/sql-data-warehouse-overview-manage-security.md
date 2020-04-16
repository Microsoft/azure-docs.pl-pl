---
title: Zabezpieczanie bazy danych
description: Porady dotyczące zabezpieczania bazy danych i tworzenia rozwiązań w zasobie puli SQL Synapse.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 27d3a242d91a79ea00974748f4a8b5460d2dd247
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416053"
---
# <a name="secure-a-database-in-azure-synapse"></a>Zabezpieczanie bazy danych w usłudze Azure Synapse

> [!div class="op_single_selector"]
>
> * [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (portal)](sql-data-warehouse-encryption-tde.md)
> * [Szyfrowanie (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

W tym artykule przejdziesz przez podstawy zabezpieczania puli SQL Synapse. W szczególności w tym artykule rozpoczynasz pracę z zasobami w celu ograniczenia dostępu, ochrony danych i monitorowania działań w bazie danych aprowizowana przy użyciu puli SQL.

## <a name="connection-security"></a>Zabezpieczenia połączeń

Zabezpieczenia połączeń dotyczą sposobu ograniczania i zabezpieczania połączeń z bazą danych przy użyciu reguł zapory i szyfrowania połączeń.

Reguły zapory są używane zarówno przez serwer, jak i bazę danych do odrzucania prób połączenia z adresów IP, które nie zostały jawnie wpisane na białą listę. Aby zezwolić na połączenia z publicznego adresu IP aplikacji lub komputera klienckiego, należy najpierw utworzyć regułę zapory na poziomie serwera przy użyciu witryny Azure portal, interfejsu API REST lub programu PowerShell.

Najlepszym rozwiązaniem jest maksymalne ograniczenie zakresu adresów IP przepuszczanych przez zaporę serwera.  Aby uzyskać dostęp do puli SQL z komputera lokalnego, upewnij się, że zapora w sieci i komputerze lokalnym umożliwia komunikację wychodzącą na porcie TCP 1433.  

Usługa Azure Synapse Analytics używa reguł zapory IP na poziomie serwera. Nie obsługuje reguł zapory IP na poziomie bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory usługi Azure SQL Database](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Połączenia z pulą SQL są domyślnie szyfrowane.  Modyfikowanie ustawień połączenia w celu wyłączenia szyfrowania jest ignorowane.

## <a name="authentication"></a>Authentication

Uwierzytelnianie to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Pula SQL obsługuje obecnie uwierzytelnianie programu SQL Server z nazwą użytkownika i hasłem oraz z usługą Azure Active Directory.

Po utworzeniu serwera logicznego bazy danych należy określić nazwę logowania „server admin” przy użyciu nazwy użytkownika i hasła. Za pomocą tych poświadczeń można uwierzytelnić do dowolnej bazy danych na tym serwerze jako właściciel bazy danych lub "dbo" za pośrednictwem uwierzytelniania programu SQL Server.

Jednak jako najlepszą praktykę użytkownicy organizacji powinni używać innego konta do uwierzytelniania. W ten sposób można ograniczyć uprawnienia przyznane aplikacji i zmniejszyć ryzyko złośliwej aktywności w przypadku, gdy kod aplikacji jest narażony na atak iniekcji SQL.

Aby utworzyć uwierzytelnionego użytkownika programu SQL Server, połącz się z **główną bazą** danych na serwerze za pomocą logowania administratora serwera i utwórz nowy login serwera.  Dobrym pomysłem jest również utworzenie użytkownika w głównej bazie danych. Utworzenie użytkownika w wzorcu umożliwia użytkownikowi zalogowanie się przy użyciu narzędzi takich jak SSMS bez określania nazwy bazy danych.  Umożliwia im również użycie eksploratora obiektów do wyświetlania wszystkich baz danych na serwerze SQL.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Następnie połącz się z **bazą danych puli SQL** za pomocą logowania administratora serwera i utwórz użytkownika bazy danych na podstawie utworzonego logowania serwera.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Aby nadać użytkownikowi uprawnienia do wykonywania dodatkowych operacji, takich jak tworzenie loginów `Loginmanager` lub `dbmanager` tworzenie nowych baz danych, przypisz go do i ról w głównej bazie danych.

Aby uzyskać więcej informacji na temat tych dodatkowych ról i uwierzytelniania w bazie danych SQL, zobacz [Zarządzanie bazami danych i logowaniami w usłudze Azure SQL Database.](../../sql-database/sql-database-manage-logins.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  Aby uzyskać więcej informacji na temat łączenia się przy użyciu usługi Azure Active Directory, zobacz [Łączenie przy użyciu uwierzytelniania usługi Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do tego, co można zrobić w bazie danych po uwierzytelnieniu i nawiązaniu połączenia. Uprawnienia autoryzacji są określane przez członkostwo ról i uprawnienia. Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień. Aby zarządzać rolami, można użyć następujących procedur składowanych:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Konto administratora serwera, za pomocą którego nawiązujesz połączenie, jest członkiem roli db_owner, która ma uprawnienia do wykonywania wszystkich funkcji w bazie danych. Zapisz to konto, aby móc wdrażać uaktualnienia schematów i wykonywać inne operacje zarządzania. Używaj konta „ApplicationUser” z bardziej ograniczonymi uprawnienia do nawiązywania połączenia pomiędzy swoją aplikacją a bazą danych aplikacji, korzystając z minimalnych uprawnień wymaganych przez aplikację.

Istnieją sposoby dalszego ograniczenia tego, co użytkownik może zrobić w bazie danych:

* Szczegółowe uprawnienia umożliwiają kontrolowanie operacji, które można wykonywać na poszczególnych kolumnach, tabelach, widokach, [schematach,](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) procedurach i innych obiektach w bazie danych. Użyj szczegółowych uprawnień, aby mieć jak najwięcej kontroli i udzielić minimalnych niezbędnych uprawnień.
* [Role bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) inne niż db_datareader i db_datawriter mogą służyć do tworzenia bardziej zaawansowanych kont użytkowników aplikacji lub mniej zaawansowanych kont zarządzania. Wbudowane role stałej bazy danych zapewniają łatwy sposób udzielania uprawnień, ale może spowodować przyznanie więcej uprawnień niż są to konieczne.
* [Procedury składowane](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) umożliwiają ograniczenie czynności wykonywanych w bazie danych.

Poniższy przykład udziela dostępu do odczytu do schematu zdefiniowanego przez użytkownika.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Zarządzanie bazami danych i serwerami logicznymi z witryny Azure portal lub przy użyciu interfejsu API usługi Azure Resource Manager jest kontrolowane przez przypisania ról konta użytkownika portalu. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w witrynie Azure portal](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Szyfrowanie

Transparent Data Encryption (TDE) pomaga chronić przed zagrożeniem złośliwą aktywnością, szyfrując i odszyfrowując dane w spoczynku. Podczas szyfrowania bazy danych skojarzone kopie zapasowe i pliki dziennika transakcji są szyfrowane bez konieczności żadnych zmian w aplikacjach. Technologia TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego nazywanego kluczem szyfrowania bazy danych.

W bazie danych SQL klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera bazy danych SQL. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. Algorytm szyfrowania używany jest AES-256. Aby uzyskać ogólny opis TDE, zobacz [Przezroczyste szyfrowanie danych](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Bazę danych można zaszyfrować za pomocą [portalu Azure](sql-data-warehouse-encryption-tde.md) lub [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje i przykłady dotyczące łączenia się z magazynem za pomocą różnych protokołów, zobacz Łączenie się z [pulą SQL](../sql/connect-overview.md).
