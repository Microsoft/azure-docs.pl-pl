---
title: Nawiązywanie połączeń i wykonywanie zapytań
description: Linki do Azure SQL Database szybkich start przedstawiających sposób nawiązywania połączeń z Azure SQL Database i Azure SQL Managed Instance.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/17/2021
ms.openlocfilehash: 96b52c80fa06be8c157ad39fd65be4e491e0cbe3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874859"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Azure SQL Database i Azure SQL Managed Instance artykuły dotyczące nawiązywania połączeń i wykonywania zapytań
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Poniższy dokument zawiera linki do przykładów platformy Azure pokazujących, jak nawiązywać połączenia i Azure SQL Database i Azure SQL Managed Instance. Aby uzyskać powiązane zalecenia dotyczące zabezpieczeń na poziomie transportu, zobacz Zagadnienia dotyczące zabezpieczeń [na poziomie transportu dotyczące łączności z bazą danych.](#tls-considerations-for-database-connectivity)

## <a name="quickstarts"></a>Przewodniki Szybki start

| Szybki start | Opis |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|W tym przewodniku Szybki start pokazano, jak używać programu SSMS do nawiązywania połączenia z bazą danych, a następnie, za pomocą instrukcji Transact-SQL, wstawiać, aktualizować i usuwać dane w bazie danych.|
|[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)|W tym przewodniku Szybki start pokazano, jak używać usługi Azure Data Studio do nawiązywania połączenia z bazą danych, a następnie, za pomocą instrukcji Transact-SQL (T-SQL), utworzyć bazę danych TutorialDB używaną w samouczkach Azure Data Studio samouczków.|
|[Witryna Azure Portal](connect-query-portal.md)|W tym przewodniku Szybki start pokazano, jak używać edytora zapytań do nawiązywania połączenia z bazą danych (tylko język Azure SQL Database), a następnie, za pomocą instrukcji Transact-SQL, wstawiać, aktualizować i usuwać dane w bazie danych.|
|[Visual Studio Code](connect-query-vscode.md)|W tym przewodniku Szybki start pokazano, jak używać języka Visual Studio Code do nawiązywania połączenia z bazą danych, a następnie, za pomocą instrukcji Transact-SQL, wstawiać, aktualizować i usuwać dane w bazie danych.|
|[.NET przy użyciu programu Visual Studio](connect-query-dotnet-visual-studio.md)|W tym przewodniku Szybki start pokazano, jak używać programu .NET Framework do tworzenia programu w języku C# z usługą Visual Studio w celu nawiązywania połączenia z bazą danych, a następnie, za pomocą instrukcji Transact-SQL, tworzyć zapytania o dane.|
|[.NET Core](connect-query-dotnet-core.md)|W tym przewodniku Szybki start pokazano, jak używać programu .NET Core w systemach Windows/Linux/macOS w celu utworzenia programu w języku C# służącego do nawiązywania połączenia z bazą danych, a następnie, za pomocą instrukcji Transact-SQL, tworzyć zapytania o dane.|
|[Przejdź](connect-query-go.md)|W tym przewodniku Szybki start pokazano, jak używać go do nawiązywania połączenia z bazą danych. Ponadto przedstawiono instrukcje języka Transact-SQL umożliwiające wykonywanie zapytań i modyfikowanie danych.|
|[Java](connect-query-java.md)|W tym przewodniku Szybki start pokazano, jak używać języka Java do nawiązywania połączenia z bazą danych, a następnie, za pomocą instrukcji Transact-SQL, zapytania o dane.|
|[Node.js](connect-query-nodejs.md)|W tym przewodniku Szybki start pokazano, jak używać języka Node.js do tworzenia programu służącego do nawiązywania połączenia z bazą danych, a następnie, za pomocą instrukcji Transact-SQL, tworzyć zapytania o dane.|
|[PHP](connect-query-php.md)|W tym przewodniku Szybki start pokazano, jak używać języka PHP do tworzenia programu służącego do nawiązywania połączenia z bazą danych, a następnie, za pomocą instrukcji Transact-SQL, tworzyć zapytania o dane.|
|[Python](connect-query-python.md)|W tym przewodniku Szybki start pokazano, jak używać języka Python do nawiązywania połączenia z bazą danych, i jak używać instrukcji Transact-SQL do wykonywania zapytań o dane. |
|[Ruby](connect-query-ruby.md)|W tym przewodniku Szybki start pokazano, jak używać języka Ruby do tworzenia programu służącego do nawiązywania połączenia z bazą danych, a następnie, za pomocą instrukcji Transact-SQL, tworzyć zapytania o dane.|
|||

## <a name="get-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem

Uzyskaj informacje o połączeniu potrzebne do nawiązania połączenia z bazą danych w Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **Bazy danych SQL** lub Wystąpienia zarządzane **SQL.**

3. Na  stronie Przegląd przejrzyj w pełni kwalifikowaną nazwę serwera obok pola Nazwa serwera dla bazy danych w usłudze Azure SQL Database lub w pełni kwalifikowaną nazwę serwera (lub adres IP) obok pola Host dla maszyny wirtualnej platformy Azure SQL Managed Instance lub SQL Server na maszynie wirtualnej platformy Azure.   Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

> [!NOTE]
> Aby uzyskać informacje o połączeniu SQL Server na maszynie wirtualnej platformy Azure, zobacz Connect to a SQL Server instance (Nawiązywanie połączenia [z SQL Server wirtualnym).](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>Uzyskiwanie ADO.NET połączenia (opcjonalnie — tylko SQL Database)

1. Przejdź do bloku bazy danych w Azure Portal i w obszarze **Ustawienia** wybierz **pozycję Parametry połączenia.**

2. Sprawdź pełne parametry połączenia sterownika **ADO.NET**.

    ![Parametry połączenia sterownika ADO.NET](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Skopiuj parametry połączenia sterownika **ADO.NET**, jeśli zamierzasz go używać.

## <a name="tls-considerations-for-database-connectivity"></a>Zagadnienia dotyczące TLS dotyczące łączności z bazą danych

Transport Layer Security (TLS) jest używany przez wszystkie sterowniki, które firma Microsoft dostarcza lub obsługuje do łączenia się z bazami danych w Azure SQL Database lub Azure SQL Managed Instance. Nie jest konieczna żadna specjalna konfiguracja. W przypadku wszystkich połączeń z wystąpieniem SQL Server, bazą danych w Azure SQL Database lub wystąpieniem usługi Azure SQL Managed Instance zaleca się, aby wszystkie aplikacje ustawiały następujące konfiguracje lub ich odpowiedniki:

- **Encrypt = On**
- **TrustServerCertificate = Off**

Niektóre systemy używają innych, lecz równoważnych słów kluczowych dla tych słów kluczowych konfiguracji. Te konfiguracje zapewniają, że sterownik klienta weryfikuje tożsamość certyfikatu TLS otrzymanego z serwera.

Zalecamy również wyłączenie protokołu TLS 1.1 i 1.0 na kliencie, jeśli wymagana jest zgodność ze standardem Payment Card Industry Data Security Standard (PCI DSS).

Sterowniki firm innych niż Microsoft mogą nie używać domyślnie protokołu TLS. Może to być czynnik podczas nawiązywania połączenia z Azure SQL Database lub Azure SQL Managed Instance. Aplikacje ze sterownikami osadzonymi mogą nie pozwalać na kontrolowanie tych ustawień połączenia. Zaleca się zbadanie zabezpieczeń takich sterowników i aplikacji przed ich użyciem w systemach, które wchodzą w interakcję z danymi poufnymi.

## <a name="libraries"></a>Biblioteki

Do nawiązywania połączenia z usługami Azure SQL Database lub Azure SQL Managed Instance można użyć różnych bibliotek i Azure SQL Managed Instance. Zapoznaj się z naszymi [samouczkami wprowadzającymi](https://aka.ms/sqldev), aby szybko rozpocząć pracę z językami programowania, takimi jak C#, Java, Node.js, PHP i Python. Następnie utwórz aplikację przy użyciu programu SQL Server w systemie Linux lub Windows albo platformy Docker w systemie macOS.

W poniższej tabeli wymieniono biblioteki łączności (*sterowniki*), których aplikacje klienckie mogą używać w różnych językach, aby łączyć się z programem SQL Server działającym lokalnie lub w chmurze i korzystać z niego. Można ich używać w systemach Linux, Windows lub Docker i używać ich do nawiązywania połączeń Azure SQL Database, Azure SQL Managed Instance i Azure Synapse Analytics.

| Język | Platforma | Dodatkowe zasoby | Pobierz | Rozpoczęcie pracy |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Program Microsoft ADO.NET dla programu SQL Server](/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Pobieranie](https://dotnet.microsoft.com/download) | [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Sterownik JDBC firmy Microsoft dla SQL Server](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [Pobieranie](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Sterowniki PHP SQL dla programu SQL Server](/sql/connect/php/microsoft-php-driver-for-sql-server) | [Pobieranie](/sql/connect/php/download-drivers-php-sql-server) | [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Sterowniki Node.js dla programu SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Instalacja](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Sterownik SQL języka Python](/sql/connect/python/python-driver-for-sql-server/) | Opcje instalacji: <br/> \* [pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \* [pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Sterowniki Ruby dla programu SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Instalacja](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Wprowadzenie](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Sterownik ODBC firmy Microsoft dla programu SQL Server](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [Pobieranie](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

W poniższej tabeli wymieniono przykłady platform mapowania obiektowo-relacyjnych (ORM) i platform internetowych, których aplikacje klienckie mogą używać z platformami SQL Server, Azure SQL Database, Azure SQL Managed Instance lub Azure Synapse Analytics. Tych platform można używać w systemie Linux, Windows lub Docker.

| Język | Platforma | Struktury ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](/ef)<br>[Entity Framework Core](/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat architektury łączności, zobacz artykuł [Azure SQL Database Connectivity Architecture (Architektura łączności usługi Azure SQL Database)](connectivity-architecture.md).
- Znajdź [SQL Server, które](/sql/connect/sql-connection-libraries/) są używane do nawiązywania połączenia z aplikacji klienckich.
- Połącz się Azure SQL Database lub Azure SQL Managed Instance:
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu .NET (C#)](connect-query-dotnet-core.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu języka PHP](connect-query-php.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu Node.js](connect-query-nodejs.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu języka Java](connect-query-java.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu języka Python](connect-query-python.md)
  - [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu ruby](connect-query-ruby.md)
  - [Zainstaluj narzędzia sqlcmd](/sql/linux/sql-server-linux-setup-tools) i bcp SQL Server wiersza polecenia w systemie Linux — w przypadku użytkowników systemu Linux spróbuj połączyć się z programem Azure SQL Database lub Azure SQL Managed Instance [sqlcmd.](/sql/ssms/scripting/sqlcmd-use-the-utility)
- Przykłady kodu logiki ponawiania próby:
  - [Nawiązywanie połączeń odpornych na błędy z ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Nawiązywanie połączeń odpornych na błędy za pomocą języka PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php
