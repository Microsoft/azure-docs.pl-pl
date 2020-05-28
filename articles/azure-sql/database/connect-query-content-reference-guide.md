---
title: Łączenie i wykonywanie zapytań dotyczących zawartości
description: Informacje dotyczące Azure SQL Database przewodników szybki start przedstawiających sposób nawiązywania połączeń z usługą i Azure SQL Database zapytań.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: ea4b201cd9ad6fa295bbccafe445733aadcf31c1
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054317"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Przewodniki Szybki Start: łączenie Azure SQL Database i zapytania
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Poniższy dokument zawiera linki do przykładów platformy Azure, w których pokazano, jak nawiązać połączenie i Azure SQL Database zapytań. Zawiera także pewne rekomendacje dotyczące protokołu TLS.

## <a name="quickstarts"></a>Przewodniki Szybki start

| |  |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączenia z usługą Azure SQL Database przy użyciu programu SSMS, a następnie używania instrukcji języka Transact-SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|W tym przewodniku szybki start pokazano, jak za pomocą Azure Data Studio połączyć się z Azure SQL Database, a następnie użyć instrukcji języka Transact-SQL (T-SQL) w celu utworzenia TutorialDB używanych w samouczkach Azure Data Studio.|
|[Azure Portal](connect-query-portal.md)|W tym przewodniku Szybki start pokazano, jak używać edytora zapytań w celu nawiązywania połączenia z bazą danych SQL, a następnie, korzystając z instrukcji Transact-SQL, wysyłać zapytania o dane oraz wstawiać, aktualizować i usuwać dane z bazy danych.|
|[Visual Studio Code](connect-query-vscode.md)|W tym przewodniku szybki start pokazano, jak za pomocą Visual Studio Code połączyć się z Azure SQL Database, a następnie użyć instrukcji języka Transact-SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych.|
|[.NET przy użyciu programu Visual Studio](connect-query-dotnet-visual-studio.md)|W tym przewodniku szybki start pokazano, jak za pomocą programu .NET Framework utworzyć program w języku C# z programem Visual Studio, aby połączyć się z usługą Azure SQL Database i użyć instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych.|
|[.NET Core](connect-query-dotnet-core.md)|Ten przewodnik Szybki Start przedstawia sposób korzystania z platformy .NET Core w systemie Windows/Linux/macOS w celu utworzenia programu w języku C# do łączenia się z Azure SQL Database i używania instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych.|
|[Przejdź](connect-query-go.md)|W tym przewodniku szybki start pokazano, jak używać języka go do nawiązywania połączenia z Azure SQL Database. Ponadto przedstawiono instrukcje języka Transact-SQL umożliwiające wykonywanie zapytań i modyfikowanie danych.|
|[Java](connect-query-java.md)|Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z usługą Azure SQL Database przy użyciu języka Java, a następnie używania instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych.|
|[Node. js](connect-query-nodejs.md)|Ten przewodnik Szybki Start przedstawia sposób tworzenia przez program środowiska Node. js programu w celu nawiązania połączenia z usługą Azure SQL Database i używania instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych.|
|[PHP](connect-query-php.md)|Ten przewodnik Szybki Start przedstawia sposób tworzenia programu w celu nawiązania połączenia z usługą Azure SQL Database i używania instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych.|
|[Python](connect-query-python.md)|Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z usługą Azure SQL Database i używania instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych. |
|[Ruby](connect-query-ruby.md)|Ten przewodnik Szybki Start przedstawia sposób tworzenia programu w celu nawiązania połączenia z usługą Azure SQL Database i używania instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych.|
|[R](connect-query-r.md)|W tym przewodniku szybki start pokazano, jak używać języka R z Azure SQL Database Machine Learning Services do tworzenia programu w celu nawiązania połączenia z Azure SQL Database i używania instrukcji języka Transact-SQL do wykonywania zapytań dotyczących danych.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Zagadnienia dotyczące protokołu TLS na potrzeby łączności z usługą SQL Database

Protokół TLS (Transport Layer Security) jest używany przez wszystkie sterowniki dostarczane lub obsługiwane przez firmę Microsoft na potrzeby nawiązywania połączenia z usługą Azure SQL Database. Nie jest konieczna żadna specjalna konfiguracja. W przypadku wszystkich połączeń do SQL Server, Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL zaleca się, aby wszystkie aplikacje ustawili następujące konfiguracje lub ich odpowiedniki:

- **Encrypt = On**
- **TrustServerCertificate = Off**

Niektóre systemy używają innych, lecz równoważnych słów kluczowych dla tych słów kluczowych konfiguracji. Te konfiguracje zapewniają, że sterownik klienta weryfikuje tożsamość certyfikatu TLS otrzymanego z serwera.

Zalecamy również wyłączenie protokołu TLS 1.1 i 1.0 na kliencie, jeśli wymagana jest zgodność ze standardem Payment Card Industry Data Security Standard (PCI DSS).

Sterowniki firm innych niż Microsoft mogą nie używać domyślnie protokołu TLS. Może to mieć znaczenie podczas nawiązywania połączenia z usługą Azure SQL Database. Aplikacje ze sterownikami osadzonymi mogą nie pozwalać na kontrolowanie tych ustawień połączenia. Zaleca się zbadanie zabezpieczeń takich sterowników i aplikacji przed ich użyciem w systemach, które wchodzą w interakcję z danymi poufnymi.

## <a name="libraries"></a>Biblioteki

Do łączenia się z usługą Azure SQL Database lub wystąpieniem zarządzanym Azure SQL można używać różnych bibliotek i platform. Zapoznaj się z naszymi [samouczkami wprowadzającymi](https://aka.ms/sqldev), aby szybko rozpocząć pracę z językami programowania, takimi jak C#, Java, Node.js, PHP i Python. Następnie utwórz aplikację przy użyciu programu SQL Server w systemie Linux lub Windows albo platformy Docker w systemie macOS.

W poniższej tabeli wymieniono biblioteki łączności (*sterowniki*), których aplikacje klienckie mogą używać w różnych językach, aby łączyć się z programem SQL Server działającym lokalnie lub w chmurze i korzystać z niego. Można ich używać w systemach Linux i Windows oraz na platformie Docker, a także za ich pomocą łączyć się z usługami Azure SQL Database i Azure SQL Data Warehouse.

| Język | Platforma | Zasoby dodatkowe | Pobierz | Rozpoczęcie pracy |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Program Microsoft ADO.NET dla programu SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Pobierz](https://www.microsoft.com/net/download/) | [Rozpoczęcie pracy](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Sterownik JDBC firmy Microsoft dla programu SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Pobierz](https://go.microsoft.com/fwlink/?linkid=852460) |  [Rozpoczęcie pracy](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Sterowniki PHP SQL dla programu SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Pobierz](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Rozpoczęcie pracy](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Sterowniki Node.js dla programu SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Instalacja](https://msdn.microsoft.com/library/mt652094.aspx) |  [Rozpoczęcie pracy](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Sterownik SQL języka Python](https://msdn.microsoft.com/library/mt652092.aspx) | Opcje instalacji: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Rozpoczęcie pracy](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Sterowniki Ruby dla programu SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Instalacja](https://msdn.microsoft.com/library/mt711041.aspx) | [Rozpoczęcie pracy](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Sterownik ODBC firmy Microsoft dla programu SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Pobierz](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

W poniższej tabeli przedstawiono przykłady struktur mapowania obiektów (ORM) i platformy sieci Web, które mogą być używane przez aplikacje klienckie z SQL Server, Azure SQL Database, wystąpienia zarządzanego Azure SQL lub Azure Synapse Analytics. Można używać platform w systemie Linux, Windows lub Docker.

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

- Aby uzyskać informacje na temat architektury łączności, zobacz artykuł [Azure SQL Database Connectivity Architecture (Architektura łączności usługi Azure SQL Database)](connectivity-architecture.md).
- Znajdź [sterowniki programu SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) służące do łączenia się z aplikacji klienckich
- Łączenie z usługą SQL Database:
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu platformy .NET (C#)](connect-query-dotnet-core.md)
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka PHP](connect-query-php.md)
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka Node.js](connect-query-nodejs.md)
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu platformy Java](connect-query-java.md)
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka Python](connect-query-python.md)
  - [Nawiązywanie połączenia z bazą danych SQL Database przy użyciu języka Ruby](connect-query-ruby.md)
- Przykłady kodu logiki ponawiania próby:
  - [Nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą środowiska ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą języka PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
