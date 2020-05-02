---
title: Łączenie i tworzenie zapytań Szybki Start
description: Przewodniki Szybki start z informacjami na temat nawiązywania połączeń z bazą danych Azure SQL Database i wykonywania zapytań.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 05ba54c10cc693f81115f364d7a400e2275a3875
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80528230"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Przewodniki Szybki Start: łączenie Azure SQL Database i zapytania

Poniższy dokument zawiera linki do przykładów związanych z platformą Azure przedstawiających nawiązywanie połączeń z bazą danych Azure SQL Database i wykonywanie zapytań. Zawiera także pewne rekomendacje dotyczące protokołu TLS.

## <a name="quickstarts"></a>Przewodniki Szybki start

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|W tym przewodniku Szybki start pokazano, jak używać narzędzia SSMS w celu nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane, a także wstawiać, aktualizować i usuwać dane z bazy danych.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|W tym samouczku Szybki start pokazano, jak używać programu Azure Data Studio w celu nawiązania połączenia z bazą danych Azure SQL Database, a następnie utworzyć bazę danych TutorialDB używaną w samouczkach programu Azure Data Studio za pomocą instrukcji Transact-SQL (T-SQL).|
|[Azure Portal](sql-database-connect-query-portal.md)|W tym przewodniku Szybki start pokazano, jak używać edytora zapytań w celu nawiązywania połączenia z bazą danych SQL, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane oraz wstawiać, aktualizować i usuwać dane z bazy danych.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|W tym przewodniku Szybki start pokazano, jak używać edytora Visual Studio Code w celu nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane, a także wstawiać, aktualizować i usuwać dane z bazy danych.|
|[.NET przy użyciu programu Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|W tym przewodniku Szybki start pokazano, jak używać programu .NET Framework w celu utworzenia — za pomocą programu Visual Studio — programu w języku C# służącego do nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|W tym przewodniku Szybki start pokazano, jak używać platformy .NET Core w systemach operacyjnych Windows/Linux/macOS w celu utworzenia programu w języku C# służącego do nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[Przejdź](sql-database-connect-query-go.md)|W tym przewodniku Szybki start przedstawiono sposób nawiązywania połączeń z bazą danych Azure SQL Database za pomocą języka Go. Ponadto przedstawiono instrukcje języka Transact-SQL umożliwiające wykonywanie zapytań i modyfikowanie danych.|
|[Java](sql-database-connect-query-java.md)|W tym przewodniku Szybki start pokazano, jak używać języka Java w celu nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[Node.js](sql-database-connect-query-nodejs.md)|W tym przewodniku Szybki start pokazano, jak używać narzędzia Node.js w celu utworzenia programu służącego do nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[PHP](sql-database-connect-query-php.md)|W tym przewodniku Szybki start pokazano, jak używać języka PHP w celu utworzenia programu służącego do nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[Python](sql-database-connect-query-python.md)|W tym przewodniku Szybki start pokazano, jak używać języka Python w celu nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane. |
|[Ruby](sql-database-connect-query-ruby.md)|W tym przewodniku Szybki start pokazano, jak używać języka Ruby w celu utworzenia programu służącego do nawiązywania połączenia z bazą danych Azure SQL Database, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane.|
|[R](sql-database-connect-query-r.md)|Ten przewodnik Szybki Start przedstawia sposób używania języka R z usługą Azure SQL Database Machine Learning Services do tworzenia programu w celu nawiązania połączenia z bazą danych Azure SQL i używania instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Zagadnienia dotyczące protokołu TLS na potrzeby łączności z usługą SQL Database
Protokół TLS (Transport Layer Security) jest używany przez wszystkie sterowniki dostarczane lub obsługiwane przez firmę Microsoft na potrzeby nawiązywania połączenia z usługą Azure SQL Database. Nie jest konieczna żadna specjalna konfiguracja. W przypadku wszystkich połączeń z programem SQL Server lub z usługą Azure SQL Database zalecamy, aby wszystkie aplikacje ustawiały następujące konfiguracje albo ich odpowiedniki:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Niektóre systemy używają innych, lecz równoważnych słów kluczowych dla tych słów kluczowych konfiguracji. Te konfiguracje zapewniają, że sterownik klienta weryfikuje tożsamość certyfikatu TLS otrzymanego z serwera.

Zalecamy również wyłączenie protokołu TLS 1.1 i 1.0 na kliencie, jeśli wymagana jest zgodność ze standardem Payment Card Industry Data Security Standard (PCI DSS).

Sterowniki firm innych niż Microsoft mogą nie używać domyślnie protokołu TLS. Może to mieć znaczenie podczas nawiązywania połączenia z usługą Azure SQL Database. Aplikacje ze sterownikami osadzonymi mogą nie pozwalać na kontrolowanie tych ustawień połączenia. Zaleca się zbadanie zabezpieczeń takich sterowników i aplikacji przed ich użyciem w systemach, które wchodzą w interakcję z danymi poufnymi.

## <a name="libraries"></a>Biblioteki

Do łączenia z usługą Azure SQL Database można używać różnych bibliotek i platform. Zapoznaj się z naszymi [samouczkami wprowadzającymi](https://aka.ms/sqldev), aby szybko rozpocząć pracę z językami programowania, takimi jak C#, Java, Node.js, PHP i Python. Następnie utwórz aplikację przy użyciu programu SQL Server w systemie Linux lub Windows albo platformy Docker w systemie macOS.

W poniższej tabeli wymieniono biblioteki łączności (*sterowniki*), których aplikacje klienckie mogą używać w różnych językach, aby łączyć się z programem SQL Server działającym lokalnie lub w chmurze i korzystać z niego. Można ich używać w systemach Linux i Windows oraz na platformie Docker, a także za ich pomocą łączyć się z usługami Azure SQL Database i Azure SQL Data Warehouse. 

| Język | Platforma | Dodatkowe zasoby | Pliki do pobrania | Wprowadzenie |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Program Microsoft ADO.NET dla programu SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Pobierz](https://www.microsoft.com/net/download/) | [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Sterownik JDBC firmy Microsoft dla programu SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Pobierz](https://go.microsoft.com/fwlink/?linkid=852460) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Sterowniki PHP SQL dla programu SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Pobierz](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Sterowniki Node.js dla programu SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Zainstaluj](https://msdn.microsoft.com/library/mt652094.aspx) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Sterownik SQL języka Python](https://msdn.microsoft.com/library/mt652092.aspx) | Opcje instalacji: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Sterowniki Ruby dla programu SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Zainstaluj](https://msdn.microsoft.com/library/mt711041.aspx) | [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Sterownik ODBC firmy Microsoft dla programu SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Pobierz](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Poniższa tabela zawiera przykłady struktur mapowania obiektowo-relacyjnego (ORM) i struktur internetowych, których aplikacje klienckie mogą używać z programem SQL Server działającym lokalnie lub w chmurze. Tych struktur można używać w systemach Linux i Windows oraz na platformie Docker i łączyć się za ich pomocą z usługami SQL Database i SQL Data Warehouse. 

| Język | Platforma | Struktury ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat architektury łączności, zobacz artykuł [Azure SQL Database Connectivity Architecture (Architektura łączności usługi Azure SQL Database)](sql-database-connectivity-architecture.md).
- Znajdź [sterowniki programu SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) służące do łączenia się z aplikacji klienckich
- Łączenie z usługą SQL Database:
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu platformy .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka PHP](sql-database-connect-query-php.md) 
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka Node.js](sql-database-connect-query-nodejs.md) 
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu platformy Java](sql-database-connect-query-java.md) 
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka Python](sql-database-connect-query-python.md)
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka Ruby](sql-database-connect-query-ruby.md)
- Przykłady kodu logiki ponawiania próby:
  - [Nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą środowiska ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą języka PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
