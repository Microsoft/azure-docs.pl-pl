---
title: Zabezpieczanie dedykowanej puli SQL (dawniej SQL DW)
description: Porady dotyczące zabezpieczania dedykowanej puli SQL (dawniej SQL DW) i opracowywania rozwiązań w usłudze Azure Synapse Analytics.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 7e2d4b47f8f37a8e6d69a7846b8b0f92247121da
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685195"
---
# <a name="secure-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Zabezpieczanie dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics

> [!div class="op_single_selector"]
>
> * [Przegląd zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Szyfrowanie (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

W tym artykule przedstawiono podstawowe informacje dotyczące zabezpieczania dedykowanej puli SQL (dawniej SQL DW). W szczególności ten artykuł ułatwia rozpoczęcie pracy z zasobami w celu ograniczania dostępu, ochrony danych i monitorowania aktywności przy użyciu dedykowanej puli SQL (dawniej SQL DW).

## <a name="connection-security"></a>Zabezpieczenia połączeń

Zabezpieczenia połączeń dotyczą sposobu ograniczania i zabezpieczania połączeń z bazą danych przy użyciu reguł zapory i szyfrowania połączeń.

Reguły zapory są używane przez [logiczny serwer SQL](../../azure-sql/database/logical-servers.md) i jego bazy danych w celu odrzucania prób połączenia z adresów IP, które nie zostały jawnie zatwierdzone. Aby zezwolić na połączenia z publicznego adresu IP aplikacji lub komputera klienckiego, należy najpierw utworzyć regułę zapory na poziomie serwera przy użyciu Azure Portal, interfejsu API REST lub programu PowerShell.

Najlepszym rozwiązaniem jest ograniczenie zakresów adresów IP dozwolonych przez zaporę na poziomie serwera, tak jak to możliwe.  Aby uzyskać dostęp do dedykowanej puli SQL (dawniej SQL DW) z komputera lokalnego, upewnij się, że Zapora w sieci i komputer lokalny zezwalają na komunikację wychodzącą na porcie TCP 1433.  

Dedykowana Pula SQL (wcześniej SQL DW) używa reguł zapory adresów IP na poziomie serwera. Nie obsługuje reguł zapory adresów IP na poziomie bazy danych. Aby uzyskać więcej informacji, zobacz temat [Azure SQL Database regułami zapory](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Połączenia z dedykowaną pulą SQL (dawniej SQL DW) są szyfrowane domyślnie.  Modyfikowanie ustawień połączenia w celu wyłączenia szyfrowania jest ignorowane.

## <a name="authentication"></a>Authentication

Uwierzytelnianie to sposób potwierdzenia tożsamości podczas nawiązywania połączenia z bazą danych. Dedykowana Pula SQL (wcześniej SQL DW) obsługuje obecnie uwierzytelnianie SQL Server przy użyciu nazwy użytkownika i hasła oraz z Azure Active Directory.

Podczas tworzenia serwera dla bazy danych należy określić nazwę logowania "administrator serwera" przy użyciu nazwy użytkownika i hasła. Przy użyciu tych poświadczeń można uwierzytelniać się w dowolnej bazie danych na tym serwerze jako właściciel bazy danych lub "dbo" za pośrednictwem SQL Server uwierzytelniania.

Jednak najlepszym rozwiązaniem jest użycie przez użytkowników w organizacji innego konta do uwierzytelniania. W ten sposób można ograniczyć uprawnienia przyznane aplikacji i zmniejszyć ryzyko złośliwych działań w przypadku, gdy kod aplikacji jest narażony na ataki iniekcji SQL.

Aby utworzyć SQL Server uwierzytelnionego użytkownika, nawiąż połączenie z bazą danych **Master** na serwerze za pomocą identyfikatora logowania administratora serwera i Utwórz nowy identyfikator logowania do serwera.  Dobrym pomysłem jest również utworzenie użytkownika w bazie danych Master. Utworzenie użytkownika w bazie danych Master umożliwia użytkownikowi logowanie się przy użyciu narzędzi, takich jak program SSMS, bez określania nazwy bazy.  Umożliwia także używanie Eksploratora obiektów do wyświetlania wszystkich baz danych na serwerze.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Następnie połącz się z **dedykowaną pulą SQL (dawniej SQL DW)** przy użyciu identyfikatora logowania administratora serwera i Utwórz użytkownika bazy danych na podstawie utworzonego identyfikatora logowania serwera.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Aby przyznać użytkownikowi uprawnienia do wykonywania dodatkowych operacji, takich jak tworzenie nazw logowania lub tworzenie nowych baz danych, przypisz użytkownika do `Loginmanager` `dbmanager` ról i w bazie danych Master.

Aby uzyskać więcej informacji na temat dodatkowych ról i uwierzytelniania do SQL Database, zobacz temat [Zarządzanie bazami danych i logowaniami w Azure SQL Database](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Aby uzyskać więcej informacji na temat nawiązywania połączenia przy użyciu Azure Active Directory, zobacz [nawiązywanie połączenia przy użyciu uwierzytelniania Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do tego, co można zrobić w bazie danych po uwierzytelnieniu i nawiązaniu połączenia. Uprawnienia do autoryzacji są określane przez członkostwo w rolach i uprawnienia. Zalecanym najlepszym rozwiązaniem jest przyznanie użytkownikom minimalnych niezbędnych uprawnień. Aby zarządzać rolami, można użyć następujących procedur składowanych:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Konto administratora serwera, za pomocą którego nawiązujesz połączenie, jest członkiem roli db_owner, która ma uprawnienia do wykonywania wszystkich funkcji w bazie danych. Zapisz to konto, aby móc wdrażać uaktualnienia schematów i wykonywać inne operacje zarządzania. Używaj konta „ApplicationUser” z bardziej ograniczonymi uprawnienia do nawiązywania połączenia pomiędzy swoją aplikacją a bazą danych aplikacji, korzystając z minimalnych uprawnień wymaganych przez aplikację.

Istnieją sposoby dalszej ograniczenia, co użytkownik może zrobić w ramach bazy danych:

* [Uprawnienia](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) szczegółowe pozwalają kontrolować, które operacje można wykonywać na poszczególnych kolumnach, tabelach, widokach, schematach, procedurach i innych obiektach w bazie danych. Używaj szczegółowych uprawnień, aby mieć największą kontrolę i udzielić minimalnych uprawnień.
* [Role bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) inne niż db_datareader i db_datawriter mogą służyć do tworzenia bardziej zaawansowanych kont użytkowników aplikacji lub mniej zaawansowanych kont zarządzania. Wbudowane role bazy danych zapewniają łatwy sposób przyznawania uprawnień, ale mogą powodować udzielanie większej liczby uprawnień niż jest to konieczne.
* [Procedury składowane](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) umożliwiają ograniczenie czynności wykonywanych w bazie danych.

Poniższy przykład daje dostęp do odczytu do schematu zdefiniowanego przez użytkownika.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Zarządzanie bazami danych i serwerami z Azure Portal lub przy użyciu interfejsu API Azure Resource Manager jest kontrolowane przez przypisania roli konta użytkownika portalu. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Szyfrowanie

Transparent Data Encryption (TDE) pomaga chronić przed zagrożeniem złośliwymi działaniami przez szyfrowanie i odszyfrowywanie danych przy użyciu funkcji Rest. Podczas szyfrowania bazy danych skojarzone kopie zapasowe i pliki dziennika transakcji są szyfrowane bez konieczności wprowadzania jakichkolwiek zmian w aplikacjach. Technologia TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego nazywanego kluczem szyfrowania bazy danych.

W SQL Database klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. Używany algorytm szyfrowania to AES-256. Ogólny opis TDE można znaleźć w temacie [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Bazę danych można zaszyfrować przy użyciu [Azure Portal](sql-data-warehouse-encryption-tde.md) lub [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje i przykłady dotyczące łączenia się z magazynem przy użyciu różnych protokołów, zobacz [nawiązywanie połączenia z dedykowaną pulą SQL (dawniej SQL DW)](sql-data-warehouse-connect-overview.md).
