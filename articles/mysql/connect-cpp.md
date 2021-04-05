---
title: 'Szybki Start: Łączenie przy użyciu języka C++ Azure Database for MySQL'
description: Ten przewodnik Szybki start zawiera przykład kodu w języku C++, za pomocą którego można nawiązywać połączenie z danymi usługi Azure Database for MySQL i wykonywać zapytania względem nich.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: cpp
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: e8b2842300e43a9de88cc0b9b3ae9ce6cd754612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94535847"
---
# <a name="quickstart-use-connectorc-to-connect-and-query-data-in-azure-database-for-mysql"></a>Szybki Start: używanie łącznika/C++ do nawiązywania połączenia i wykonywania zapytań dotyczących danych w Azure Database for MySQL

Ten przewodnik Szybki start przedstawia sposób nawiązywania połączeń z usługą Azure Database for MySQL przy użyciu aplikacji języka C++. Pokazano w nim, jak używać instrukcji języka SQL w celu wysyłania zapytań o dane oraz wstawiania, aktualizowania i usuwania danych w bazie danych. W tym temacie założono, że wiesz już, jak programować za pomocą języka C++, i dopiero zaczynasz pracę z Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start jako punktu wyjścia używa zasobów utworzonych w jednym z następujących przewodników:
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

Należy również:
- Zainstalować program [.NET Framework](https://www.microsoft.com/net/download)
- Zainstaluj [program Visual Studio](https://www.visualstudio.com/downloads/)
- Zainstalować [łącznik bazy danych MySQL/środowisko C++](https://dev.mysql.com/downloads/connector/cpp/) 
- Zainstalować rozwiązanie [Boost](https://www.boost.org/)

> [!IMPORTANT] 
> Upewnij się, że adres IP, z którego nawiązywane jest połączenie, został dodany przy użyciu [Azure Portal](./howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-visual-studio-and-net"></a>Instalowanie programu Visual Studio i technologii .NET
W krokach w tej sekcji założono, że wiesz już, jak programować za pomocą platformy .NET.

### <a name="windows"></a>**Windows**
- Zainstaluj program Visual Studio 2019 Community. Program Visual Studio 2019 Community to w pełni funkcjonalne, rozszerzalne, bezpłatne środowisko IDE. Za pomocą tego środowiska IDE można tworzyć nowoczesne aplikacje dla aplikacji dla systemów Android, iOS, Windows, sieci Web i baz danych oraz usług w chmurze. Możesz zainstalować pełne środowisko .NET Framework lub tylko .NET Core: fragmenty kodu zamieszczone w podręczniku działają z obiema opcjami. Jeśli na komputerze masz już zainstalowany program Visual Studio, pomiń dwa następne kroki.
   1. Pobierz [Instalatora programu Visual Studio 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
   2. Uruchom instalatora i postępuj zgodnie z wyświetlanymi monitami, aby ukończyć instalację.

### <a name="configure-visual-studio"></a>**Konfigurowanie programu Visual Studio**
1. Z poziomu programu Visual Studio, właściwości > projektu-> konsolidator-> ogólne > dodatkowe katalogi biblioteki Dodaj katalog "\lib\opt" (na przykład: C:\Program Files (x86) \MySQL\MySQL Connector C++ 1.1.9 \ lib\opt) łącznika języka C++.
2. W programie Visual Studio w pozycji Projekt -> Właściwości -> C/C++ -> Ogólne -> Dodatkowe katalogi dyrektywy include:
   - Dodaj katalog "\Include" łącznika języka c++ (na przykład: C:\Program Files (x86) \MySQL\MySQL łącznik C++ 1.1.9 \ include \) .
   - Dodaj katalog główny biblioteki wzrostu (na przykład: C:\ boost_1_64_0 \) .
3. W programie Visual Studio w lokalizacji Projekt -> Właściwości -> Konsolidator -> Wejście -> Dodatkowe zależności dodaj plik **mysqlcppconn.lib** w polu tekstowym.
4. Skopiuj plik **mysqlcppconn.dll** z folderu biblioteki łącznika języka C++ utworzonego w kroku 3 do tego samego katalogu, w którym znajduje się plik wykonywalny aplikacji, albo dodaj go do zmiennej środowiskowej, aby aplikacja mogła go odnaleźć.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu
Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z usługą Azure Database for MySQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie w witrynie Azure Portal kliknij pozycję **Wszystkie zasoby** i wyszukaj utworzony serwer, taki jak **mydemoserver**.
3. Kliknij nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 :::image type="content" source="./media/connect-cpp/1_server-overview-name-login.png" alt-text="Nazwa serwera usługi Azure Database for MySQL":::

## <a name="connect-create-table-and-insert-data"></a>Nawiązywanie połączenia, tworzenie tabeli i wstawianie danych
Użyj poniższego kodu, aby nawiązać połączenie i załadować dane przy użyciu instrukcji **CREATE TABLE** i **INSERT INTO** języka SQL. Kod używa klasy sql::Driver z metodą connect() w celu ustanowienia połączenia z programem MySQL. Następnie kod używa metod createStatement() i execute(), aby uruchamiać polecenia bazy danych. 

Zastąp parametry hosta, dbname, User i Password. Parametry można zastąpić wartościami, które zostały określone podczas tworzenia serwera i bazy danych. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    //please create database "quickstartdb" ahead of time
    con->setSchema("quickstartdb");

    stmt = con->createStatement();
    stmt->execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt->execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con->prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt->setString(1, "banana");
    pstmt->setInt(2, 150);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "orange");
    pstmt->setInt(2, 154);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "apple");
    pstmt->setInt(2, 100);
    pstmt->execute();
    cout << "One row inserted." << endl;

    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="read-data"></a>Odczyt danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. Kod używa klasy sql::Driver z metodą connect() w celu ustanowienia połączenia z programem MySQL. Następnie kod używa metod prepareStatement() i executeQuery(), aby uruchamiać polecenia select. Następnie kod używa metody next() w celu przechodzenia do rekordów w wynikach. Na końcu kod używa metod getInt() i getString() w celu przeanalizowania wartości w rekordzie.

Zastąp parametry hosta, dbname, User i Password. Parametry można zastąpić wartościami, które zostały określone podczas tworzenia serwera i bazy danych. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    con->setSchema("quickstartdb");

    //select  
    pstmt = con->prepareStatement("SELECT * FROM inventory;");
    result = pstmt->executeQuery();

    while (result->next())
        printf("Reading from table=(%d, %s, %d)\n", result->getInt(1), result->getString(2).c_str(), result->getInt(3));

    delete result;
    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Aktualizowanie danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **UPDATE** języka SQL. Kod używa klasy sql::Driver z metodą connect() w celu ustanowienia połączenia z programem MySQL. Następnie kod używa metod prepareStatement() i executeQuery(), aby uruchamiać polecenia update. 

Zastąp parametry hosta, dbname, User i Password. Parametry można zastąpić wartościami, które zostały określone podczas tworzenia serwera i bazy danych. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");

    //update
    pstmt = con->prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt->setInt(1, 200);
    pstmt->setString(2, "banana");
    pstmt->executeQuery();
    printf("Row updated\n");

    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Usuwanie danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **DELETE** języka SQL. Kod używa klasy sql::Driver z metodą connect() w celu ustanowienia połączenia z programem MySQL. Następnie kod używa metod prepareStatement() i executeQuery(), aby uruchamiać polecenia delete.

Zastąp parametry hosta, dbname, User i Password. Parametry można zastąpić wartościami, które zostały określone podczas tworzenia serwera i bazy danych. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");
        
    //delete
    pstmt = con->prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt->setString(1, "orange");
    result = pstmt->executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić wszystkie zasoby używane w ramach tego przewodnika Szybki Start, Usuń grupę zasobów przy użyciu następującego polecenia:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych MySQL do usługi Azure Database for MySQL przy użyciu zrzutu i przywracania](concepts-migrate-dump-restore.md)
